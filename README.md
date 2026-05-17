# Amnezia VPN Client for umbrelOS

A native umbrelOS application that deploys an obfuscated AmneziaWG (WireGuard Protocol V2) client directly onto the host network. This setup is specifically optimized to circumvent Deep Packet Inspection (DPI) firewalls, ensuring your node maintains a stable and secure connection under highly restrictive network conditions.

## Prerequisites & Configuration

Before installing the application via the Umbrel App Store, you must manually prepare and place your AmneziaWG configuration file on the host system.

### 1. Obtain the Configuration File
Generate or obtain an AmneziaWG client configuration file from your VPN server. The file **must** be named exactly:
```text
awg0.conf
```

### 2. Adjust Network & DNS Settings
Open your `awg0.conf` file in a text editor prior to deployment and verify/adjust the following parameters:
* **DNS:** Ensure the `DNS` entry in the `[Interface]` section points to a reachable, high-performance DNS resolver (e.g., your self-hosted AdGuard Home instance, or public privacy-focused options like `1.1.1.1` or `9.9.9.9`). This prevents DNS resolution bottlenecks over the tunnel.
* **AllowedIPs:** Configure the `AllowedIPs` in the `[Peer]` section according to your needs (e.g., `0.0.0.0/0, ::/0` to route all traffic, or specific subnets for split-tunneling your node's data).
* **Sync Obfuscation Values:** Double-check that your custom AmneziaWG header and junk packet metrics (`Jc`, `Jmin`, `Jmax`, `S1`, `S2`, `H1`, `H2`, `H3`, `H4`) accurately match your server's stealth requirements.

---

## Deployment & Installation

### Step 1: Place the Configuration File on the Umbrel Host
Connect to your Umbrel node via SSH. Create the required persistent directory on your SSD if it does not already exist:

```bash
mkdir -p /data/app-data/custom-wireguard
```

Securely transfer or copy your prepared `awg0.conf` file into this exact location:
```text
/data/app-data/custom-wireguard/awg0.conf
```
> ⚠️ **Important:** The application container strictly expects the file at this absolute path on the host system to mount it properly into its sandbox environment.

### Step 2: Install the Application
1. Open your **Umbrel Web Dashboard** in your browser.
2. Navigate to the **App Store**.
3. Locate **Amnezia VPN Client** under your custom community store.
4. Click **Install**.

The app framework will pull the pre-compiled image, spin up the container using the host network namespace, automatically read the `awg0.conf` file, and bring up the stealth network interface.

---

## Verification & Troubleshooting

To monitor the active handshake, interface statistics, and routing tables of your active tunnel, execute the following command on your Umbrel CLI:

```bash
docker exec -it amnezia-vpn awg show
```

To view the live connection initialization and runtime logs:

```bash
docker logs -f amnezia-vpn
```
