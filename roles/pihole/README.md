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

(This example is for macOS; other OSes and devices are similar.)

  1. Open System Preferences.
  2. Click on Network.
  3. For whatever interface is at the top of the list and active for your connection (e.g. Wi-Fi or Ethernet), click the 'Advanced...' button.
  4. Click the 'DNS' tab
  5. Remove any existing entries under 'DNS Servers' (but note them in case if you want to switch back later).
  6. Add the IP address of any of the worker nodes in the cluster.

At this point, if you start browsing the web, then visit the admin UI, you should see requests are being blocked.

If DNS requests are not resolving (e.g. most Internet things are not working), then revert the DNS settings to what they were previously.
