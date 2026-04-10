 (cd "$(git rev-parse --show-toplevel)" && git apply --3way <<'EOF' 
diff --git a/README.md b/README.md
index 8691df74abea48b53919b912c7fdeb07546e72fc..ac7c976337311e121e83140fdeaeea6afc50f47c 100644
--- a/README.md
+++ b/README.md
@@ -1,4 +1,37 @@
 # IHM-Stripe-and-ESP8266
 This project allow you to use Stripe payment system to trigger ESP8266 function. The google Script helps to forward the API from HTTPS protocol to HTTP.
 
 ![alt text](https://github.com/MamaxeFinders/IHM-Stripe-and-ESP8266/blob/main/Sequences%20from%20payment%20to%20credit.png?raw=true)
+
+## KC868-A8 Ethernet: optional static-IP mode (Livebox troubleshooting)
+
+If requests stop being received after some time (and rebooting the ESP32 temporarily fixes it), the issue can be caused by DHCP lease/renew behavior on the router side.
+
+The sketch now supports an optional static-IP mode:
+
+1. Open `KC868-A8 with Ethernet`.
+2. Find:
+   - `const bool USE_STATIC_ETH_IP = false;`
+   - `ETH_LOCAL_IP`, `ETH_GATEWAY`, `ETH_SUBNET`, `ETH_DNS1`, `ETH_DNS2`
+3. Set `USE_STATIC_ETH_IP` to `true`.
+4. Set `ETH_LOCAL_IP` to a free IP in your LAN (example: `192.168.1.90`).
+5. Keep gateway/subnet matching your Livebox LAN settings.
+6. Upload and reboot the board.
+
+### What to change on the Livebox
+
+- Recommended: create a **DHCP reservation** for the board MAC address to the same IP (or exclude that IP from DHCP range) so no other device can take it.
+- Ensure local devices can reach each other on LAN (no client isolation / guest-network isolation).
+- If possible, disable aggressive eco/idle LAN features for the switch port used by the board.
+
+### How to validate quickly
+
+- Ping the board IP continuously from a PC on the same LAN.
+- Call `/health` periodically and check `eth_connected` and `ip`.
+- If static-IP mode is stable while DHCP mode is not, the Livebox DHCP path is likely the root cause.
+
+### Recovery behavior now implemented in code
+
+- If link is up but no IP is present for ~15s, Ethernet is reinitialized.
+- If physical link is down for ~45s, Ethernet is reinitialized.
+- If Ethernet remains down for 2 minutes, the ESP32 is fully restarted.
 
EOF
)
