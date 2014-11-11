# Naritive 
## Story 1 - Events and me
Hi guys, thanks for coming tonight. My name is Dirk van Rensburg and I work at 

ecetera where we work hard to 

rid the world of badly behaving web and mobile applications with a strong slant towards application performance. Data grids are one of the ways a system can be made to perform better and as it happens, Hazelcast is a data grid. Data grids, like traditional SQL databases are responisble for storing data for later use and providing a way of updating and retrieving that data. One of the interesting features of SQL databases is the concept of a trigger.

a trigger is a piece of procedural code which is automatically executed in response to some database event such as updating a row. Databse triggers are transactional and execute in the same thread as the updating call. This means if the trigger fail then the update is rolled back. Back in the day triggers were misused, a lot, and probably still are somewhere. They got a bit of a bad reputation for causing hard to find and reason about side effects. 

However, they are still useful, particularly for things like data integrity or audit logging. Since Hazelcast is, like a database, concerned with data I wondered if I could use Hazelcast's eventing system to simulate the behaviour of database triggers in a distributed data grid. 

I wanted to see if trigger like behaviour can be implemented for the distributed map in the embedded topology, meaning the trigger code and the Hazelcast instance run is in the same Java virtual machine. Secondary to this, I wanted to see if I can get transaction like behaviour, similar to that of database triggers.

I thought I could try use the Hazelcast event system to implement the triggers. Events are just messages sent to communicate the information that something happened and in doing so it gives the application an oppurtunity to react to the fact. Hazelcast provides two main ways to get notified of changes to the distributed map, called 

EnteryListener and MapInterceptor. EntryListeners listen for events on entries and can then react to the event. They all run in a single thread, seperate from the original action that caused the event. MapInterceptors intercept the call to the map and gives you the opportunity to perform some work before or after the change to map, using the same thread as the map update. So this sound promising, especially the MapInterceptor but

## Story 2 - Experiment with implementing triggers

I decided to start experimenting with EntryListeners and surprise surprise they behaved exaclty like the documentation says they would. Entry listeners can be added to the map in a one of two ways. Globally which means the listener will be notified of changes to any entry in the map and locally which means the listener will only be called for the entries owned by the node that registered the listener. 

Global entry listeners, is no good for triggers in the embedded topology because every node will get notified of every change event. It is useful when using a client to connect to the Hazelcast cluster. The client can add the listener and will be notified of all map updates.

Local entry listeners, can work. The only get notified of changes to the entries owned by the node which added the listener. There are no duplicate events, however there is no way to cancel the map update should the event listener fail.

If you remember I mentioned another way of getting notifications on map changes, earlier. MapInterceptor. The MapInterceptor is invoked in the same thread as the updating process. Intercepting the map update happens before the update, so you can incluence the value going into the map and throwing an exception will cancel the update. This sounds great - lets see how it behaves!

To add the interceptor, as always, you need to get hold of the Hazelcast instance and then get the map you want to add the interceptor too. Adding the interceptor is a simple call on the map and in this case we are adding an instance of MyInterceptor.

MyInterceptor is just a class that implements MapInterceptor. For this test I implement only the interceptPut method and all it does is append the string -foo to the end of the value before placing it in the map.

To see all this in action we need to test this. To this loop forever and put an entry in the map, sleep for 2 seconds so we can see what is going on, and get the entry from the map and print it out. So when we run up one instance this is what we see. Great! But distributed computing is hard, with some unexpected gotchas so I better test it with more nodes than one. Lets start another node. What? That doesn't look right.... Lets shut the second node down but there is no change in output. Strange.... So lets start that second node again.... What? still two foos? Lets start that second node again - say noting look at the crowd and show WTF cat.

WTF cat

This is what we want to see. One foo no matter how many instances are running. Luckily we are working with 

Hazelcast

more specifically Open source Hazelcast. We can fix this. First I added an issue at the Hazelcast issue tracker on github, but after about thirty minutes I got bored waiting and decided to try and fix it myself. Turns out all you have to do is

remove all the pink stuff and add in some green stuff.

## Story 3 - So does contributing to Hazelcast work

So now it is fixed but I'd hate to be the only person with this awesome fix. How do I tell everyone or distribute this fix. Thank goodness for github! But what is github? It is internet service which makes it easy to collaborate on projects which involve the changing of computer files. It can track who changed what and when. In a nutshell github works something like this

First you need to FORK the hazelcast repository which will make a copy of all the hazelcast code into your github accout. But to make changes or compile and test the code you need it local, on your laptop. 

You do this by using the GIT CLONE command. This will create a local copy of the hazelcast code so that you can work on it. No you can make changes and when you are done you

commit your changes to your local repository. No one can see any of these changes yet. To make it public you need to send it back to github using 

the GIT PUSH command. This will send your changes to your fork in github where other people watching your fork can see your change, but Hazelcast still has no idea of these changes. To notify them you need to

create a PULL REQUEST. This will notify the Hazelcast developers that there is a new change that can be merged in and it will also automatically kick off the integeration build process. 

So will your changes be merge in? Is there anything else you need to do to make sure your changes are accepted and merged in swiftly? Yes. First you need to download and sign the Code Contribution Agreement.

Show deal with devil

Create an issue on github to accompany your pull request. You can then quote the issue number in the pull request and a well described issue will go a long way to speed up the acceptance procees.

Make sure your pull request contains tests. This is very important since the chances of your change being merged in without a test is slim.

Now that you know about contributing to Hazelcast you should. We all should. You don't have to be a coder to contribute. Documentation changes is contributing. Creating issues to describe documentation mistakes or errors are ways to contribute. Every little bit help make Hazelcast better.

Questions?







