# Programming the services.

It's 9:07 pm here at Space Apps Challenge, when I started writing this explanation about how we program different services in the aircraft.

We try to implement these services by the `MajorDomo` pattern. There is a main broker named as `broker` which controls and coordinates the data and control flow in the aircraft.

## Our Broker instance.

```c
typedef struct {
    zctx_t *ctx;                //  Our context
    void *socket;               //  Socket for clients & workers
    int verbose;                //  Print activity to stdout
    char *endpoint;             //  Broker binds to this endpoint
    zhash_t *services;          //  Hash of known services
    zhash_t *workers;           //  Hash of known workers
    zlist_t *waiting;           //  List of waiting workers
    uint64_t heartbeat_at;      //  When to send HEARTBEAT
} broker_t;
```

Different functions that the broker does are :

* To setup a new broker instance.
```c
static broker_t *
    s_broker_new (int verbose);
```

* Destroy the broker
```c
static void
    s_broker_destroy (broker_t **self_p);
```

* Binding here
```c
static void
    s_broker_bind (broker_t *self, char *endpoint);
```

* Interact with workers and coordinate the messages.
```c
static void
    s_broker_worker_msg (broker_t *self, zframe_t *sender, zmsg_t *msg);
```

* Interact with clients and coordinate the messages.
```c
static void
    s_broker_client_msg (broker_t *self, zframe_t *sender, zmsg_t *msg);
```

* Clear entire traces of Broker
```c
static void
    s_broker_purge (broker_t *self);
```
