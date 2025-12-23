# Stack: Immich

This stack deploys [Immich](https://immich.app/), a self-hosted photo and video backup and sharing solution.

## Engineering Decisions

I've made some significant deviations from my usual practices for this stack.
There are two primary decisions I've made:

1. To host Immich on Sagittarius in the first place
2. To give Immich its own postgres & redis databases on Sagittarius, rather than using Hydra's High Availability clusters

### The Method to my Madness

There are two main reasons for Decision #1:

- Having Immich save photos to Sagittarius over NFS felt very clunky, especially considering photo hosting is pretty in-scope for a NAS
- Immich can take advantage of Sagittarius' dedicated GPU for hardware accelerated transcoding and Machine Learning, tasks that Hydra would seriously struggle with

Decision #2 was a much harder to choice to make, but ultimately came down to these main factors:

- As per [the docs](https://docs.immich.app/administration/postgres-standalone#without-superuser-permission), Immich needs superuser access to the Postgres database - something I was hesitant to give to a third-party application on a shared database cluster
- Immich needs a Postgres database with some special extensions enabled, which I felt would be better isolated from other applications
- Hydra's Postgres cluster currently isn't reachable outside of Kubernetes, so I'd need to open up a pretty big attack vector to allow Immich to connect to it
- Immich would be relying on Hydra's availability to operate, which is something I like to avoid where possible
- I had concerns about Immich's ability to run on a Redis cluster rather than single instance, I've had apps struggle with this in the past that needed their own dedicated Redis instance

### Pros & Cons

This approach has the following merits:

- Immich is entirely on Sagittarius, it doesn't rely on another machine being online (this is a very rare win for me)
- Immich's databases are fully isolated from everything else, improving security
- Immich can take advantage of Sagittarius' GPU for transcoding and ML tasks
- No dodgy NFS mounts are needed, Immich can directly use the ZFS pool
- Hydra's HA clusters don't need exposing to the intranet (only a small security win, but a win nonetheless)

However, it does have a few downsides:

- Hydra is meant to handle compute while Sagittarius is meant to handle storage - Immich breaks this model (but only slightly)
- We're not taking advantage of Hydra's High Availability at all
- More resources are being used in total, as we're running separate database instances
- Immich can't be properly distributed on Sagittarius, whereas it could on Hydra (depending on which resources need sharing)
- The LDAP server is hosted on Hydra, so using central authentication would mean there is a slight reliance on Hydra after all (this only affects authentication, Immich's own data is still safe on Sagittarius)

### Conclusion

Overall, I feel the engineering decisions I've made here are justified, and the benefits outweigh the downsides in the end.
Immich's storagey nature and need for GPU compute ultimately make it very in-scope for a NAS system, so it doesn't really violate the whole Hydra-Sagittarius separation of concerns thing.