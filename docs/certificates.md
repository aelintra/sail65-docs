# Certificates

SARK uses one active TLS certificate for the HTTPS console (Apache on port **443**) and for Asterisk WebSocket Secure / WebRTC (WSS on port **8089**). Manage certificates from the **Cert** panel in the UI.

## Active certificate

SARK picks the active certificate in this order:

1. **Custom / commercial** — PEM certificate and private key you paste into the Cert panel
2. **Let's Encrypt** — issued for a public FQDN via HTTP-01
3. **Self-signed (snakeoil)** — default after install; browsers will warn until you replace it

The panel shows the active source, domain (if any), expiry, issuer, and SANs.

!!! tip
    WebRTC softphones and browsers need a **trusted** certificate on WSS (:8089). The self-signed default is enough to bind the port, but clients will warn or refuse the connection until Let's Encrypt or a commercial certificate is installed.

## Install a trusted certificate

=== "Let's Encrypt"

    Let's Encrypt issues a trusted certificate for **one** public FQDN. The same certificate is applied to Apache HTTPS and Asterisk WSS.

    ### Prerequisites

    - Set the system FQDN under **Network** (or enter it on the Cert panel).
    - Create a public DNS **A** (or AAAA) record for that FQDN pointing at this host.
    - Allow **tcp/80** from the Internet on any cloud security group / upstream firewall. SARK's Shorewall keeps public port 80 **closed** most of the time and opens it only while the certificate is being issued or renewed.
    - The ACME contact email is for expiry notices only; you do not need to click a confirmation link.

    ### Issue

    1. Open **Cert**.
    2. Under **Let's Encrypt**, confirm the FQDN and enter an ACME contact email.
    3. Click **Get Let's Encrypt certificate**.

    On success, Active shows **Let's Encrypt**, with domain and expiry filled in. Browsers should accept `https://your-fqdn/` without a certificate warning.

    ### Renew and remove

    - **Renew now** — forces a renewal attempt (also used by the nightly renew job).
    - **Remove Let's Encrypt** — stops using Let's Encrypt and falls back to self-signed (or custom if present).

    Automatic renewal runs daily (early morning). Shorewall opens public :80 for the challenge window, then closes it again.

    !!! note
        If a **custom** certificate is installed, remove it first before requesting Let's Encrypt. Custom certificates take precedence.

=== "Commercial / custom"

    If you have a certificate from a commercial CA (or an internal CA):

    1. Open **Cert** while Active is still self-signed.
    2. Under **Load a commercial certificate**, paste the certificate PEM (**full chain** preferred) and the matching private key PEM.
    3. Install / apply.

    Active should show **Custom / commercial**. Remove the certificate from the panel to revert to self-signed (or to allow Let's Encrypt afterward).

## WebRTC / WSS (port 8089)

Asterisk WSS uses the **same** active certificate as the console. After a trusted certificate is in place:

- Allow **tcp/8089** in the [SARK firewall](firewall.md) (and in any cloud security group) for the networks where WebRTC clients will connect.
- Create WebRTC extensions and **Commit** as usual.

## Firewall ports

| Port | Role |
|------|------|
| **tcp/80** | Let's Encrypt HTTP-01 only (opened briefly by SARK during issue/renew; keep open on the cloud security group if you use LE) |
| **tcp/443** | HTTPS console |
| **tcp/8089** | Asterisk WSS / WebRTC |

!!! warning
    On cloud instances (for example AWS), the security group is separate from Shorewall — both must allow the traffic you need.
