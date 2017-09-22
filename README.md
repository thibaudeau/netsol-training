# Lab Topology Description

My lab was created via EVE-NG. I have three node, Cisco routers all connected to an out of band network (192.168.1.0/24).

Each router is connected to each other via L3 interconnects and have BGP running between them. L1 and R2 via iBGP and L1 and R3 via eBGP.

The topology looks like this



      R2-------L1-------R3
     \        |       /
      \       |      /
       \      |     /
        \     |    /
         \    |   /
          \   |  /
           \  | /
          Out of Band
           Network
