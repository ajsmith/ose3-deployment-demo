=========================
Accessing the Web Console
=========================

Once installation is complete, try accessing the web console. Most likely, you
don't want to expose your DNS server to the public internet, so the easiest way
to access the web console is by means of a SOCKS proxy using SSH.

.. code-block:: shell

   $ ssh -D 8111 cloud-user@<public-ip-of-demo-ose-installer>

That's all it takes to establish the proxy, but you'll also need to configure
your browser to use it. Go into your browser's network settings and configure
it to use a SOCKS proxy on localhost, port 8111, and check the checkbox for
"Remote DNS" to enabled DNS lookups through the proxy.

Once you've done the above steps, you'll be ready to access the console. Just
open your browser to `https://cluster.osedemo.example.com:8443` and you'll see
the login screen.

The following test users are available:

- arya
- bran
- jon
- sansa

All users have the password "$tark".

Congratulations! You're all set up with your own OSE environment! Now go
containerize all the things!
