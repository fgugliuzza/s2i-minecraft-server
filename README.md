# Minecraft Server Docker OpenShift Container via Source to Image (S2I)

## OpenShift (incl. minishift)

Create a project named e.g. `test` (on the web console or using `oc new-project test`), and:

    oc project test

    oc new-app fabric8/s2i-java~https://github.com/vorburger/s2i-minecraft-server.git

    oc logs -f bc/s2i-minecraft-server

    oc get pods | grep -v build

    oc port-forward s2i-minecraft-server-.... 25565:25565

If you have `git clone https://github.com/vorburger/s2i-minecraft-server.git` locally, then you can:

    oc start-build s2i-minecraft-server --from-dir=. --follow

Use the UI to Add Storage (PV) for /deployments/persistent/ to the Deployment (Application).
[There is no CLI yet to add a PVC](https://github.com/kubernetes/kubernetes/pull/52006),
but we could use [Templates](https://docs.openshift.org/latest/dev_guide/templates.html)
to set up the PV based on a parameter (and also set memory and CPU resource limits, and healthcheck).

To expose the Minecraft server port, Edit YAML of the Service, replace `type: ClusterIP` by `LoadBalancer`, and this to ports:

    - name: minecraft
      port: 25565

You'll most probably also want to remove the 8778 and 9779 ports it exposed by default (from the s2i-java Dockerfile).

This will only work if your OpenShift supports external non-HTTP traffic.

For minishift, in addition to above, you need to note the `nodePort` given to `targetPort`/`port: 25565`
shown to `oc export svc s2i-minecraft-server`, and the IP shown by `minishift ip` and connect to that from the Minecraft client.


### Troubleshooting

If the Builds > ... > Events (not Builds > ... > #N > Events!) shows
"error instantiating Build from BuildConfig ... (0): Error resolving ImageStreamTag s2i-java:latest in namespace minecraft: unable to find latest tagged image"
then you can try if first doing this helps:

    oc new-build https://github.com/fabric8io-images/s2i.git --context-dir=java/images/jboss

If this also fails with "error: buildconfigs.build.openshift.io "s2i" is forbidden: build strategy Docker is not allowed"
then you cannot use that OpenShift instance.


## Locally with Docker

Build it [using S2I](https://github.com/openshift/source-to-image):

    s2i build --copy . fabric8/s2i-java s2i-minecraft-server

Run it with with an ephemeral world for a first quick test:

    docker run --rm -p 25565:25565 s2i-minecraft-server

or with a persistent world on a volume (NB -v not --mount, even for volumes):

    docker volume create test-universe
    docker run --rm -p 25565:25565 -v test-universe:/deployments/persistent/ s2i-minecraft-server

    docker volume inspect test-universe
    docker volume ls
    docker volume rm ...
    docker volume prune

NB: Using -v with a direct absolute path to a local host directory
is NOK and leads to: java.nio.file.AccessDeniedException: /deployments/persistent/universe/world.

Debug it with:

    docker run --rm -it s2i-minecraft-server /bin/bash


## What else?

The sources are in the .s2i/ directory, which you may not see if you are filtering .*
