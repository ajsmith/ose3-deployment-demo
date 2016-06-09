=====================
Architecture Overview
=====================

The HA deployment of OSE 3.2 will consist of the following hosts:

  - 3 x Master Nodes
  - 4 x Compute Nodes (2 Infra, 2 App)
  - 2 x Load Balancers
  - 1 x DNS

Below are the hardware specs for each node type:

+-----------+------+----------+-------------+-------------+
| Node Type | CPUs | RAM (GB) | Disk 1 (GB) | Disk 2 (GB) |
+===========+======+==========+=============+=============+
| Master    | 2    | 4        | 40          | None        |
+-----------+------+----------+-------------+-------------+
| Compute   | 2    | 4        | 25          | 15          |
+-----------+------+----------+-------------+-------------+
| LB        | 2    | 4        | 20          | None        |
+-----------+------+----------+-------------+-------------+
| DNS       | 2    | 4        | 20          | None        |
+-----------+------+----------+-------------+-------------+
