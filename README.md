# Minecraft Server Docker OpenShift Container via Source to Image (S2I)

## OpenShift

Create a project named e.g. `test` on the web console (NOT using `oc new-project test`), and:

    oc project test

    oc new-app fabric8/s2i-java~https://github.com/vorburger/s2i-minecraft-server.git

    oc logs -f bc/s2i-minecraft-server

    oc get pods | grep -v build

    oc port-forward s2i-minecraft-server-.... 25565:25565

If you have `git clone https://github.com/vorburger/s2i-minecraft-server.git` locally, then you can:

    oc start-build s2i-minecraft-server --from-dir=. --follow


## Locally

Build it [using S2I](https://github.com/openshift/source-to-image):

    s2i build --copy . fabric8/s2i-java s2i-minecraft-server

Run it with with an ephemeral world for a first quick test:

    docker run --rm -p 25565:25565 s2i-minecraft-server

or with a persistent world on a volume (NB -v not --mount, even for volumes):

    docker volume create test-universe
    docker run --rm -p 25565:25565 -v test-universe:/deployments/universe s2i-minecraft-server

    docker volume inspect test-universe
    docker volume ls
    docker volume rm ...
    docker volume prune

NB: Using -v with a direct absolute path to a local host directory
is NOK and leads to: java.nio.file.AccessDeniedException: /deployments/universe/world.

Debug it with:

    docker run --rm -it s2i-minecraft-server /bin/bash


## What else?

The sources are in the .s2i/ directory, which you may not see if you are filtering .*
