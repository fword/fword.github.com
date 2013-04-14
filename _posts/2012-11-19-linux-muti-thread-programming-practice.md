---
title: linux环境多线程编程同步实践
author: admin
layout: post
categories:develop
tags: [linux]
---
 

首先我们复习下线程同步问题里的经典问题，消费者生产者问题，读者和写者问题。  
所谓的***消费者生产者问题***，就是生产者线程生产物品，然后将物品放置在一个空缓冲区中供消费者线程消费。消费者线程从缓冲区中获得物品，然后释放缓冲区。当生产者线程生产物品时，如果没有空缓冲区可用，那么生产者线程必须等待消费者线程释放出一个空缓冲区。当消费者线程消费物品时，如果没有满的缓冲区，那么消费者线程将被阻塞，直到新的物品被生产出来。

我们知道linux线程同步常用的方法有三种，分别是互斥锁，条件变量和信号量。而对于这个问题使用条件变量可以很方便的解决。代码如下，



    producer:
        	pthread_mutex_lock(&p_lock);
    		while(queue->size==0)
    			pthread_cond_wait(&notempty, &p_lock);
    		append_queue(node);
    		pthread_cond_signal(&notfull);
    		pthread_mutex_unlock(&p_lock);
    
    consumer:
    		pthread_mutex_lock(&c_lock);
    		while(queue->full)
    			pthread_cond_wait(&notfull, &c_lock);
    		pop_queue();
    		pthread_cond_signal(&notempty);
    		pthread_mutex_unlock(&c_lock);

这里使用两个互斥锁，一个p\_lock，一个c\_lock，使用两个锁是假设生产者和消费者共同操作缓冲区时不会产生冲突，比如一个操作队列头，另一个操作队列尾部（我们根据问题假设生产者和消费者是异步的）。如果你的缓冲区只能同时进行一个操作的话，可以改成一个锁。同时由于这里加了锁，意味着同一时刻只能有一个消费者（或是生产者）操作，不能是多个消费者共同操作队列，因为这里的操作意味着修改缓冲区，所以要防止两个线程同时修改缓冲区而产生冲突。

还有其他的同步经典问题，如***读者和写者问题***，***哲学家进餐问题***，***理发师睡觉问题***，由于篇幅有限，我就不再赘述了。有兴趣的话可以自己想想看，下面进入正题，叙述一下我在一次多线程编程时遇到的一个同步问题。  
 
我遇到的其实属于生产者和消费者的变体，同步时完全套用第一个代码就可以，不同的是，我这个问题有多个消费者，而且需要当一个消费者线程处理完以后，其他的消费者线程也要结束，复杂就在这个地方。

线程间同步最简单的方法就是全局变量，所以起先，我准备设一个全局变量quit=0，比如当生产者生产完最后一个产品后就修改这个全局变量quit=1，可是在逻辑上这讲不通，因为退出与否的逻辑在消费者，只有消费者一个线程处理完之后才可以设置这个quit=1。所以这就决定了修改全局变量quit的代码在消费者这一端。同时，我们在消费者线程的开始判断这个quit，如果为1则退出。

这样看起来很简单的逻辑，可是由于生产消费者之间条件变量的存在，变的不那么简单。比如，有两个消费者线程判断quit不为1，同时读不到数据进行睡眠，而生产者生产最后一个包后结束，唤醒一个消费者线程，这个线程处理完后设置quit为1后退出，那么另一个睡眠的线程将永远睡下去。

为什么解决这个问题，我们一点要使线程睡眠的条件和quit相关起来，只要不该退出才睡眠，如果该退出，则不用进行睡眠。所以有

    pthread_mutex_lock(&lock);
    while(pq->available==0&&!quit)
        pthread_cond_wait(&notempty, &lock);
    

那么下一步，判断线程是否该退出是在这段的前面还是后面呢，如果在前面还是会出现上述的那个问题，所以应该在线程醒来后判断是否该退出。还有一点就是，一个线程结束并且设置了quit标志后，为了唤醒睡眠的线程还需要pthread\_cond\_broadcast一下。所以一个消费者整体的代码如下所示，

    pthread_mutex_lock(&lock);
    while(pq->available==0&&!quit)
        pthread_cond_wait(&notempty, &lock);
    if(quit==1)
    {
        pthread_mutex_unlock(&lock);
        return 0;
    }
    //handling stuff
    if(last_one==1)
    {
        quit=1;
        pthread_cond_broadcast(&notempty);
    }
    pthread_mutex_unlock(&lock);
    

这里还要补充一点知识，当线程调用pthread\_cond\_wait(&notempty, &lock)睡眠后，那么值钱获得的锁pthread\_mutex\_lock(&lock)会被释放，想想如果不这样的话，其他的线程就无法获得该锁，那么多线程就无法并发了。同时当线程从pthread\_cond\_wait(&notempty, &lock)被唤醒后，又会重新获得该锁，这样就可以继续下面的操作了。正因为如此，所以此时线程遇到异常退出时要首先释放该锁，见上述代码6,7行。（或许你现在就明白了为什么pthread\_cond\_wait需要两个参数，而pthread\_cond\_signal只需要一个参数了吧。）



**附录**  
——————————————————————————————————————–  
***读者和写者问题***指的是，可以有多个读者同时工作，不能既有读者和写者同时工作，不能有多个写者同时工作。使用两个互斥锁实现代码如下，

    reader:
           pthread_mutex_lock(&r_lock);
           if(count>=0)
               pthread_mutex_lock(&w_lock);
           count  ;
           pthread_mutex_unlock(&r_lock);
           //reading...
           pthread_mutex_lock(&r_lock);
           count--;
           if(count==0)
               pthread_mutex_unlock(&w_lock);
           pthread_mutex_unlock(&r_lock);
    
    consumer:
           pthread_mutex_lock(&w_lock);
           //writing
           pthread_mutex_unlock(&w_lock);
    

这个读者和写者问题采用了两个互斥锁，一个是r\_lock，一个是w\_lock，当第一次进入读者程序时，需要更新读者数count，这时用r\_lock来保护。在第一次更新cout之前，判断是否已经有读者，如果cout大于0（有其他读者）或是count等于0（我是第一个读者），都需要对w\_lock进行加锁，表示我现在开始读了，你不能写了。在读操作进行完之后，读者程序退出需要修改count，所以再次对r\_lock加锁，这时也需要判断count是否为0，如果为0表示最后一个读者退出，所以此时可以解开w\_lock，那么下次写线程请求该锁时，就可以获得而进行写操作了。

***哲学家进餐问题***指的是假设有五位哲学家围坐在一张圆形餐桌旁，做以下两件事情之一：吃饭，或者思考。吃东西的时候，他们就停止思考，思考的时候也停止吃东西。餐桌中间有一大碗意大利面，每两个哲学家之间有一只餐叉。因为用一只餐叉很难吃到意大利面，所以假设哲学家必须用两只餐叉吃东西。他们只能使用自己左右手边的那两只餐叉。哲学家从来不交谈，这就很危险，可能产生死锁，每个哲学家都拿着左手的餐叉，永远都在等右边的餐叉（或者相反）。  
参考文献里有篇文章讲的很详细，这里我做一个简单的实现

    #DEFINE LEFT=(i-1)%N
    #DEFINE RIGHT=(i 1)%N
    philosopher(i)
    {
        while(1)
        {
           test_forks(i);
           eating;
           drop_forks(i);
        }
    }
    test_forks(i)
    {
        pthread_lock(&lock);
        if(state[LEFT]==not_eating&&state[right]==not_eating)
        {
            state[i]=eating;
            sem_post(&a[i]);
        }
        pthread_unlock(&lock);
        sem_wait(&a[i]);
    }
    drop_forks(i)
    {
        pthread_lock(&lock);
        state[i]=not_eating;
        pthread_unlock(&lock);
    }
    

***理发师睡觉问题***指的是理发店里有一位理发师、一把理发椅和n把供等候理发的顾客坐的椅子。如果没有顾客，则理发师便在理发椅上睡觉。当一个顾客到来时，他必须先叫醒理发师，如果理发师正在理发时又有顾客来到，则如果有空椅子可坐，他们就坐下来等。如果没有空椅子，他就离开。这里的问题是为理发师和顾客各编写一段程序来描述他们的行为，要求不能带有竞争条件。

    barber()
    {
        while(TRUE)
        {
            sem_wait(&customers);
            pthread_mutex_lock(&lock);
            waiting=waiting-1;
            sem_post(&barbers);
            pthread_mutex_lock(&lock);
            cut_hair();
        }
    }
    customer()
    {
        pthread_mutex_lock(&lock);
        if(waiting