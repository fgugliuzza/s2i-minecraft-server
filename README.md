# Minecraft Server Docker OpenShift Container via Source to Image (S2I)

Build it with:

    s2i build --copy . fabric8/s2i-java s2i-minecraft-server

Run it with:

    docker run -p 25566:25565 s2i-minecraft-server

Debug it with:

    docker run -it s2i-minecraft-server /bin/bash
