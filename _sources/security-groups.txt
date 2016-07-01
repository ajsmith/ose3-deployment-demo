===============
Security Groups
===============

To expose the environment to the outside world, you'll need to configure the
appropriate security groups in OpenStack. The following security groups are
needed.

+-------------------------+----------+-----------+
| Host                    | Port     | Network   |
+=========================+==========+===========+
| demo-ose-lb-1           | 443/tcp  | 0.0.0.0/0 |
| demo-ose-lb-1           | 8443/tcp | 0.0.0.0/0 |
| demo-ose-master-{1,2,3} | 443/tcp  | 0.0.0.0/0 |
| demo-ose-master-{1,2,3} | 8443/tcp | 0.0.0.0/0 |
| demo-ose-node-{1,2}     | 443/tcp  | 0.0.0.0/0 |
| demo-ose-node-{1,2}     | 80/tcp   | 0.0.0.0/0 |
+-------------------------+----------+-----------+

Exposing the DNS server is a potential security risk, since it's configured for
recursive queries, so we won't both to configure a security group to expose
it. Instead, we'll access the environment, including DNS, through a SOCKS
proxy, discussed later.
