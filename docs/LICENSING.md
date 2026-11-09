# Commercial Licensing & Activation

Lithotheque Base Camp Hub is commercial software developed and distributed by **GeoStratum**. Access to multi-user field synchronization and production deployment requires an active commercial license.

---

## 1. Commercial Licensing Matrix

GeoStratum offers four commercial tiers, available as either an **Annual Subscription** (including all major upgrades and technical support) or a **Perpetual License** (with optional annual maintenance contracts):

| Tier | Concurrent Devices (`max_peers`) | Typical Target Organization | Pricing Model |
| :--- | :--- | :--- | :--- |
| **Team** | Up to **5 devices** | Small geotechnical consultancies, independent exploration geologists | Annual / Perpetual |
| **Base Camp** | Up to **20 devices** | Academic research teams, standard university field schools | Annual / Perpetual |
| **Promotion** | Up to **50 devices** | University cohorts, large-scale mining exploration projects | Annual / Perpetual |
| **Site / Enterprise**| **Unlimited devices** | Geological surveys, multinational energy/mining corporations, campuses | Annual / Perpetual |

---

## 2. Air-Gapped & Offline Verification Architecture

Expedition teams often operate in areas without satellite or cellular reception for weeks. Therefore, Lithotheque Hub **never contacts external licensing servers** or telemetry endpoints.

Licensing is verified 100% locally using **digital asymmetric signatures**:
- **Dual-Algorithm Support**: Standard Ed25519 or Post-Quantum Cryptography (NIST FIPS 204 ML-DSA / Crystals-Dilithium).
- **Embedded Parameters**: Organization name, Order ID, Device Quota, and Maintenance Expiration Date are sealed within the binary signature.

---

## 3. License Installation

1. Receive your `license.key` file from GeoStratum upon order completion.
2. Deposit the key on the server:
   - **Linux**: `/etc/litho-hub/license.key`
   - **Windows**: `C:\ProgramData\Lithotheque Hub\license.key`
3. Restart the service (`sudo systemctl restart litho-hub` or `Restart-Service LithoHub`).
4. The dashboard header will reflect your organization name and active peer quota.

---

## 4. Inquiries & Sales

To request an enterprise quote, academic discount, or evaluation trial:
- **Email**: [sales@geostratum.eu](mailto:sales@geostratum.eu) / [contact@geostratum.eu](mailto:contact@geostratum.eu)
- **Website**: [https://geostratum.eu](https://geostratum.eu)
