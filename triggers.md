### What are DB Triggers

* Code invoked on database event
* Transactional



### Uses for DB Triggers

* Data Integrity
* Audit Logging



### Requirements

* Distrubuted map
* Embedded topology 
* Transactional



### Events

* Events are just messages



### Map Events

* EntryListener
* MapInterceptor



###Entry Listener
* Global entry listener
* Local entry listener



### Global Entry Listener

* Events on every node - no good
* Invoked after update - not transactional 
* Client -> Cluser topology



### Local Entry Listener

* Events on node owning the entry - better
* Invoked after update - not transactional
* Fine for embedded topology



### Map Interceptor

* Same thread as update
* Exception cancel update
* This sounds perfect!

