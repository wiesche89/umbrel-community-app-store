# Grin Node for Umbrel

Run a full **Mimblewimble/Grin node or Grin++ node** with a powerful, modern dashboard directly on your Umbrel OS.
This app bundles Rust Grin and Grin++, a Qt/C++ controller backend, and a full web UI for monitoring everything in real time.

---

## Features

- **Full Rust Grin Node (v5.4+)**
- **Full Grin++ Node**
- **Live Dashboard UI**
  - Chain height & sync progress
  - Connected peers (with geolocation map)
  - Mempool transactions
  - Block explorer (inputs/outputs/kernels)
  - Node health & status
- **Peer Management**
  - Ban/unban peers
  - Live peer performance metrics
- **Storage Persistence**
  - All chain data stored under Umbrel’s app directory
- **Node Control**
  - Start and stop the selected node from the dashboard
- **Two-container architecture**
  - `ui` → Nginx-hosted web interface
  - `controller` → Qt/C++ backend + Rust Grin and Grin++

---

## Architecture Overview

```
Umbrel Reverse Proxy
        │
        ▼
   app_proxy  ──────►  ui container (Nginx, Port 80)
                          │
                          ▼
                controller container (Grin backend)
```

Packages:

| App | App version | UI image version | Runtime image version |
| --- | --- | --- | --- |
| Mainnet | 0.2.10 | 0.2.24 | v1.0.5 |
| Testnet | 0.2.33 | 0.2.24 | v1.2.10 |

Images: `wiesche89/grin-node-docker` and `wiesche89/grin-node-runtime`.

---

## Installation (via Community App Store)

1. Open Umbrel → App Store
2. Click the **three dots** in the top right
3. Select **Community App Stores**
4. Add this store URL:

```
https://github.com/wiesche89/umbrel-community-app-store
```

5. Choose **Grin Node** (Mainnet) or **Grin Node Testnet** in your custom store.
6. Click **Install**

Umbrel will deploy:

- The UI frontend
- The controller backend
- The Rust Grin node
- The Grin++ node
- Persistent volumes for chain + config

---

## Data Persistence

All blockchain and config data is stored persistently inside Umbrel:

```
<umbrel>/app-data/grin-node/             # Mainnet
    └── data/
        ├── grin-rust-home/
        └── grinpp-home/
<umbrel>/app-data/grin-node-testnet/     # Testnet
    └── data/
        ├── grin-rust-home/
        └── grinpp-home/
```

This means:

- Updating the app **does not delete the blockchain**
- Uninstalling the app **deletes its data**. Back up needed configuration and credentials first.

Mainnet upgrades from versions before `0.2.8` and Testnet upgrades from versions
before `0.2.27` require reinstalling; old data is not migrated. Back up needed
configuration and credentials before uninstalling. Later updates retain the data directories.

---

## Ports

External ports exposed by the controller container:

| Port | Purpose |
| --- | --- |
| 3414 | Mainnet P2P |
| 13414 | Testnet P2P |

Neither app publishes controller or node RPC ports. External wallets use
`/wallet/v2/foreign` on the dashboard origin and the node API key shown in the UI.

The UI uses Umbrel’s app proxy on port 3410 (Mainnet) or 13410 (Testnet).

---

## Accessing the Dashboard

After installation, open the app from Umbrel, or use:

```
http://umbrel.local:3410     # Mainnet
http://umbrel.local:13410    # Testnet
```

or via your Umbrel device IP:

```
http://<umbrel-ip>:3410      # Mainnet
http://<umbrel-ip>:13410     # Testnet
```

---

## Troubleshooting

### UI shows “Cannot connect to controller”
Check whether the controller container is running:

```
docker ps | grep grin-node_controller
```

Restart the app from the Umbrel UI if needed.

### Node stuck on "Synchronizing"
Initial block download may take time depending on SSD speed and internet.

### Ports already in use
If another app uses one of the listed ports, installation may fail.
Stop the conflicting app, or modify ports manually.

---

## Developer Info

This app uses two Docker images:

### UI
- Docker Hub:
  https://hub.docker.com/repository/docker/wiesche89/grin-node-docker

### Controller + Grin Node
- Docker Hub:
  https://hub.docker.com/repository/docker/wiesche89/grin-node-runtime

Pullable tags:

```
# Mainnet
wiesche89/grin-node-docker:0.2.24
wiesche89/grin-node-runtime:v1.0.5

# Testnet
wiesche89/grin-node-docker:0.2.24
wiesche89/grin-node-runtime:v1.2.10
```
---

## Credits

- **Mimblewimble / Grin Developers** — https://github.com/mimblewimble/grin
- **Grin++ Developers** — https://github.com/GrinPlusPlus/GrinPlusPlus
- **Umbrel Team** — Community App Store framework
- **wiesche89** — Grin Node Controller, UI, packaging
  - https://github.com/wiesche89/grin-node-docker
  - https://github.com/wiesche89/grin-node-controller
  - https://github.com/wiesche89/GrinPlusPlus
  - https://github.com/wiesche89/grin

---

## 📜 License

MIT License
See `LICENSE` file for details.
