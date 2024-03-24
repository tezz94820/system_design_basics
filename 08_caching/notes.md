# CACHE  
This is used so that the client making a request does not goes to the database directly,it first checks in a cache and and if cache has that information it will serve directly to the client. 

---
Advantages of using cache:- 
1. It saves network calls. 
2. It avoid recomputation. 
3. Reduces DB load. 
---
We cannot store a lot of information inside a cache:- 
1. First of all it is very expensive because it is not hard disk it is SSD. 
2. If you are trying to store a lot of information inside a cache, then the search time inside the cache will increase, and there will be no point of using cache. 

---

So now we need to keep a track that a lot of information does not coagulate in cache. To do so we need a very strong cache policy. A cache policy is the algorithm which cache uses to remove some of the items from the cache and add the items in cache. There are various policies some are discuss below:- 
1. LRU(least recently used) :- all the elements which are used very recently are on the top and when that item get old it is thrown out of the cache. It is used extensively even now.
2. LFU(least frequently used) :- it is not used so often. 
3. Sliding window policy. 

---

What are the limitations of having cache :- 
1. Poor policy :- if the policy is very poor then cache will not help it will even increase the network request. 
2. Thrashing :- if your cache have very less storage. Then let's say x makes a call for its profile to the server. Server gets the profile stores in the cache and returns it to X. Y makes a call for its profile to the server. Server request the database get it store it in the cache and service to Y. Now while storing Y profile, it does not have space so what it did is removed X and added Y. Now again x asks for its profile, the cache will not have it even this time. 
3. Data consistency :- it will be a large problem in cache, because it may happen that cash has some old information and the database is updated. The cash will be update but it will take some time and a new or old request ask for that information and it is out from cash then it will be served old information. So data consistancy is a problem here. 

---

What should be the location of cache :- to the servers or should it be closed to the database let's see :- 
1. Close to the servers :- we are saying that the servers in their memory how cache installed. That means it is fastest and very less data consistent because every server will have it own cache. 
    1. It is fastest 
    2. If any server goes down then the cache is even dead now. 
    3. The data will not be consistent over multiple server's cache. 

2. placing cache to global cache. 

In this there is a global cache and all the functioning is done there. All the servers will request first the Global cache, if it has the information it serves, if it does not have the information then it goes to the database for it. 
1. It is not faster than close to the servers, but it is more accurate if any server goes down the cache will not be affected. 
2. It is mostly a redis cache. 
3. It is highly scalable. We can increase the distributed redis cache servers and hence our cache can be managed independently. 

---

So for maintaining the data consistency in cache two mechanism are used :- 
1. write through. 
2. write back. 

1.  write through :- here if you want to make any update to the database first you will go to the redis cache and update it first and then go to the database and update the database later. So here first the cache update first and then the database. 
2. Write back :- here first the database is updated first and then a request goes back to the cache and it is updated later. Right back can be used if the data is not meant to be very consistent. 
But if you are dealing with the financial transactions then the cash even needs to be updated first, so that means in financials or very consistent data you will use write through and in nonconsistent data you can use right back. 

So what should we use then, we can use a hybrid approach of both of them. If the update request is not so important and the data can be inconsistent for 10 to 20 seconds then we can store that request inside the cache collect that request and send it to the database at a bulk saving our network calls.