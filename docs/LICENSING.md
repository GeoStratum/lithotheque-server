# Licensing & Activation

Lithotheque Server is licensed on a per-base-camp model with tier capacities designed to match standard university classes and corporate survey teams.

---

## 1. License File Structure

Your license is delivered as a lightweight text file named `license.key`. It contains:
- Your organization or university name.
- Your licensed concurrent device capacity (e.g., 15 devices, 40 devices).
- The validity period (annual subscription or perpetual).
- An offline cryptographic signature.

The server operates **100% offline**: it validates your license directly on your hardware without contacting external validation servers over the internet.

---

## 2. Capacity & Peer Limits

The dashboard displays your current connection usage in the top header (e.g., `12 / 40 connected peers`).
- While active peers remain below your licensed capacity, new field mobile devices can scan the QR code and enroll seamlessly.
- If your fleet reaches the maximum allowed peer quota, the pairing module displays a notification until an active peer disconnects or the campaign session is refreshed.

---

## 3. License Renewal

To renew or upgrade your capacity quota, replace the existing `license.key` file in your server directory (`/etc/litho-hub/` on Linux or `C:\ProgramData\Lithotheque Hub\` on Windows) and restart the service.
