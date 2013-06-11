---
title: One Practice To NAT Traversal
author: admin
layout: post
categories:
  - develop
---


There are so many nat traversal solutions on the internet,but all these codes are independent and packed which could hardly be used in our own program.so i have to write my own and test it for days,for this one i try my best to make it an interface,which could be easily used by other programs.

I write this for the github readme,so i write it in english,and blog here as a reference.

 First we create a thread to wait for message from the other client,if the ack 1 equal num,it indicates the we alreay send message to the other client sucessfully and the other client has given us a ack back.so we return the socket.if the ack 1 doesn’t equal num,it means this the first time the other client connect to us,so we give them a ack and return the socket which we can use to communicate with the other client later.

In the nat_msg,our mainly things is to keep sending msg to the other client(assumed we alreay got their nat address)

If any one is Full Cone,it just works out and the receiver’s recv\_msg thread returned which will send ack making sender’s recv\_msg returned too.

If both A and B are Restricted Cone or Port Restricted Cone, msg from A to B is first discarded by the B nat router,but the router of A has already recorded the address of B,so when B send message to A,it will success and make A’s recv_msg thread returned.

If A is Symmetric NAT and B is Restricted Cone.beacuse the A’s nat address to the server is different from it’s nat address to other client.so B cannot send message to A cause it don’t know the A’s new nat address(B knows the A’s old nat address from the server).the only communication path is A to B,when B send message to A,it will fail but record A’s ip,so when A’s nat address comes which happened having a same ip with the old nat address,it could connect to B.

My program could deal with all this stuff and return the success socket,otherwise -1 returned.

in the recv_msg thread

    ret = recvfrom(sk, &ack, sizeof(ack), 0, (struct sockaddr *)&mote, &slen);
    if(ret>0)
    {
    	printf( "get connected %s:%d...n", inet_ntoa(mote.sin_addr),mote.sin_port );
    	if(num==ack 1)
    		return sk;
    	else
    	{
    		ack_back=ack 1;
    		if(sendto(sk, &ack_back,sizeof(ack_back),0,(const struct sockaddr*)&mote, sizeof(struct sockaddr)) == -1)
    		{
    			perror("recv_msg sendto error");
    			return -1;
    		}
			printf( "send to %s:%d...n", inet_ntoa(mote.sin_addr),mote.sin_port );
			return sk;
    	}
    }
    else
    	return -1;

and in nat_msg(char ip,char* port,int pnum)

    pthread_create(&tid,NULL,(void*)&recv_msg,(void*)angs);
    for(i=0;i<5;i++)	
	{
		if(sendto(client_sk, &pnum, sizeof(pnum),0,(const struct sockaddr*)&remote, sizeof(struct sockaddr)) == -1)
    	{
    		perror("sendto error");
    		return -1;
    	}
    	printf( "send to %s:%d...n", inet_ntoa(remote.sin_addr),remote.sin_port );
    	sleep(10);
    }
    pthread_join(tid,&val);
    return (int)val;

	
*[reference]*(http://michankong.blog.51cto.com/1464983/761270 )
