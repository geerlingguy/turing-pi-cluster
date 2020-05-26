# Pi-hole Setup

## Admin UI Setup

To access the Pi-hole admin UI, you'll need to point the hostname `pi.hole` at the IP address of the Pi where ingress is routed. You can find that IP address by running the following `kubectl` command:

```
# kubectl get ing -n pihole
NAME     HOSTS                         ADDRESS       PORTS   AGE
pihole   chart-example.local,pi.hole   10.0.100.99   80      55s
```

Take the value of the `ADDRESS` and add a line like the following to your `/etc/hosts` file:

10.0.100.99  pi.hole

Then you can access `http://pi.hole/` in your browser and view the admin UI.

## DNS setup

TODO.
