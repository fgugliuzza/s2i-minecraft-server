# Minecraft Server Docker OpenShift Container via Source to Image (S2I)

    s2i build --copy . fabric8/s2i-java s2i-minecraft-server
    docker run -p 25566:25565 s2i-minecraft-server
