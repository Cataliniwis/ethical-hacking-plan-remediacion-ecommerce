# Comandos de verificación por hallazgo

> `<SITIO>` = dominio evaluado (omitido) · usar solo sobre activos con autorización.

| Hallazgo | Comando | Resultado antes de remediar | Resultado esperado después |
|---|---|---|---|
| Puertos expuestos | `nmap -sV -Pn <SITIO>` | Solo 80/443 | Sin cambios |
| Resolución DNS | `dig +short <SITIO>` | IP del host | — |
| VULN-01 TLS obsoleto | `nmap -p 443 --script ssl-enum-ciphers <SITIO>` | Lista TLSv1.0 y TLSv1.1 | Solo TLSv1.2 / TLSv1.3 |
| VULN-01 TLS 1.0 | `openssl s_client -connect <SITIO>:443 -tls1` | Handshake exitoso | `handshake failure` / `no protocols available` |
| VULN-01 TLS 1.1 | `openssl s_client -connect <SITIO>:443 -tls1_1` | Handshake exitoso | Falla |
| Certificado | `openssl s_client -connect <SITIO>:443 -servername <SITIO> </dev/null` | Cadena y fechas válidas | — |
| VULN-02/03/04 Cookies | `curl -sI https://<SITIO>/ \| grep -i set-cookie` | Sin `HttpOnly` / `Secure` / `SameSite` | `; HttpOnly; Secure; SameSite=Lax` |
| VULN-05 HSTS | `curl -sI https://<SITIO>/ \| grep -i strict-transport` | Sin resultado | `Strict-Transport-Security: max-age=...` |
| VULN-06 nosniff | `curl -sI https://<SITIO>/ \| grep -i x-content-type` | Sin resultado | `X-Content-Type-Options: nosniff` |
| VULN-07 CSP | `curl -sI https://<SITIO>/ \| grep -i content-security` | Contiene `unsafe-inline` | Con *nonces*/*hashes* |
| Rutas | `dirb https://<SITIO>/` · `curl https://<SITIO>/robots.txt` | 4 candidatos revisados | — |
| Tráfico | Wireshark, filtro `tls.handshake.type == 1` | ClientHello negociando TLS 1.0 | — |
