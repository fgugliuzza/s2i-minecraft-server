# Minecraft Server Docker OpenShift Container via Source to Image (S2I)

Build it [with S2I](https://github.com/openshift/source-to-image) (or straight inside OpenShift):

    s2i build --copy . fabric8/s2i-java s2i-minecraft-server

Run it with:

    docker run -p 25566:25565 s2i-minecraft-server

Debug it with:

    docker run -it s2i-minecraft-server /bin/bash

PS: The sources are in the .s2i/ directory, which you may not see if you are filtering .*
