# 👑 Blue Team Protocol Quick Reference v1.2 👑

Referencia de combate optimizada para analistas SOC/IR/Threat Hunters. Máxima densidad de información accionable, contexto ATT&CK y prioridades de remediación.

**Última Actualización:** 2025-04-08
**Licencia:** [MIT License](LICENSE)
**Contribuciones:** [Ver CONTRIBUTING.md](CONTRIBUTING.md) (¡Se aceptan contribuciones!)

**Leyenda:**
* 🔌 **Puerto(s):** `Puerto/Proto`
* 🎯 **Uso:** Propósito legítimo
* 💀 **Sev:** (💀 Bajo | 💀💀 Medio | 💀💀💀 Crítico) - Severidad típica en ataques
* ⚠️ `[[ID Táctica/Técnica]](Link)` **Riesgo:** Amenaza + Ref MITRE ATT&CK
* 🔍 **Monitorizar (Net/Log/Endpt):** Indicadores clave + Ejemplos/Anomalías
* 🛡️ **Fix (Priorizado):** Acciones de remediación clave

---

## 🌐 Web, Naming & Core Infrastructure

### [HTTP](https://developer.mozilla.org/en-US/docs/Web/HTTP/Overview)
🔌 `80/TCP` | 🎯 Web | 💀💀 | ⚠️ [[TA0006]](https://attack.mitre.org/tactics/TA0006/) **Creds/Cookies Plaintext** | 🔍 Tráfico HTTP a páginas auth, `POST` con keywords (`password`, `login`), User-Agents sospechosos/raros. | 🛡️ **Fix:** 1. Forzar HTTPS (HSTS) | 2. Monitorizar `POST` sensibles.

### [HTTPS (TLS/SSL)](https://developer.mozilla.org/en-US/docs/Web/Security/Transport_Layer_Security)
🔌 `443/TCP` (+ `UDP` QUIC) | 🎯 Web Segura | 💀💀 | ⚠️ [[TA0011]](https://attack.mitre.org/tactics/TA0011/) **C2/Phishing Oculto** `[T1571]` `[T1573]` | 🔍 Certs (Inválidos/Self-Signed/Let's Encrypt anómalo/DVC), JA3/S Hash anómalo/raro, SNI (Dominios typosquatted/DGA-like), Vol/Timing anómalo, QUIC (`UDP 443`) inesperado. | 🛡️ **Fix:** 1. TLS Inspection (si viable) | 2. Filtrado DNS/Web | 3. Bloquear Ciphers/Versiones débiles.

### [DNS](https://datatracker.ietf.org/doc/html/rfc1034)
🔌 `53/UDP,TCP` | 🎯 Nombre<->IP | 💀💀💀 | ⚠️ [[TA0011]](https://attack.mitre.org/tactics/TA0011/) **C2 (Tunnel `[T1572]`, DGA `[T1568]`)**, [[TA0007]](https://attack.mitre.org/tactics/TA0007/) **Recon** | 🔍 Consultas a Dominios/TLDs maliciosos/nuevos/raros, Alto `NXDOMAIN` rate, Anomalías Query (Tamaño, Entropía, Tipo `ANY/TXT/NULL`), Tráfico `TCP 53` (Zone Xfer/Túnel), Beaconing DNS. | 🛡️ **Fix:** 1. Filtrado DNS (RPZ/Blocking) | 2. Monitorizar anomalías volumen/tipo query | 3. Limitar resolvers recursivos internos.

### [ICMP](https://datatracker.ietf.org/doc/html/rfc792)
🔌 `IP Proto 1` | 🎯 Control/Error IP | 💀💀 | ⚠️ [[TA0007]](https://attack.mitre.org/tactics/TA0007/) **Recon (Ping Sweep `[T1018]`, Traceroute)**, [[TA0011]](https://attack.mitre.org/tactics/TA0011/) **Tunneling C2/Exfil `[T1095]`** | 🔍 Sweeps (`icmp.type == 8` > thresh), Tipos/Códigos Bloqueados (`icmp.type == 3`), Patrones Túnel (Tamaño Payload, Freq), Redirects (`icmp.type == 5`), Timestamp Req/Reply (`icmp.type == 13/14`). | 🛡️ **Fix:** 1. Filtrar ICMP en perímetro (Allow specific types needed) | 2. Monitorizar patrones de túnel | 3. Rate-limit.

---

## 📁 File Transfer & Sharing

### [FTP](https://datatracker.ietf.org/doc/html/rfc959)
🔌 `21,20/TCP` (+ Pasivos >1024) | 🎯 Transfer Ficheros | 💀💀 | ⚠️ [[TA0006]](https://attack.mitre.org/tactics/TA0006/) **Creds/Datos Plaintext**, [[TA0010]](https://attack.mitre.org/tactics/TA0010/) **Exfil** | 🔍 **USO NO RECOMENDADO.** Cualquier tráfico externo/inesperado. Auth Anónima. `USER anonymous`. | 🛡️ **Fix:** 1. Migrar a SFTP/FTPS | 2. Deshabilitar/Restringir severamente | 3. Monitorizar logs FTP server.

### [TFTP](https://datatracker.ietf.org/doc/html/rfc1350)
🔌 `69/UDP` | 🎯 Transfer Simple | 💀💀 | ⚠️ [[T1037.005]](https://attack.mitre.org/techniques/T1037/005/) **Malware Drop/Exfil Interno**, [[TA0007]](https://attack.mitre.org/tactics/TA0007/) **Recon** | 🔍 **CUALQUIER TRÁFICO INESPERADO ES SOSPECHOSO.** `udp.port == 69`. Especialmente escrituras (`WRQ`). | 🛡️ **Fix:** 1. Deshabilitar si no es esencial | 2. Restringir por IP/VLAN | 3. Monitorizar.

### [SMB](https://learn.microsoft.com/en-us/windows/win32/fileio/microsoft-smb-protocol-and-cifs-protocol-overview)
🔌 `445/TCP` (, `139/TCP` leg) | 🎯 Win File/Print/IPC | 💀💀💀 | ⚠️ [[TA0008]](https://attack.mitre.org/tactics/TA0008/) **Lat. Mov.** (PtH `[T1550.002]`, Exploits `[T1210]`, `PsExec` `[T1021.002]`), [[TA0006]](https://attack.mitre.org/tactics/TA0006/) **Cred Theft** (Relay `[T1557.001]`, Hash Grab `[T1552.004]`), [[TA0010]](https://attack.mitre.org/tactics/TA0010/) **Exfil** `[T1030]` | 🔍 **Net:** Wks-Wks conns (`unusual pattern/timing`), SMBv1 (`smb`), Internet (`BLOCK!`), Named Pipe anomalies (`IPC$`). **Logs:** `4624(3)`(Anomalous Src/Dst/User/Time/WorkstationName), `4625`(Fail>thresh), `5140/5145`(Admin Share access/Write), `7045`(`PsExec` service), `4688`(Process `psexec.exe`/`rundll32`). | 🛡️ **Fix:** 1. Disable `SMBv1` | 2. Segment network (Perimeter/Internal) | 3. Enforce `SMB Signing` | 4. Limit Admin Share access/Use LAPS.

### [NFS](https://datatracker.ietf.org/doc/html/rfc7530) (v4)
🔌 `2049/TCP,UDP` (+ RPC `111`) | 🎯 Unix/Linux File Share | 💀💀 | ⚠️ [[T1059.004]](https://attack.mitre.org/techniques/T1059/004/) **Config Insegura** (Exports `*`, `no_root_squash`), [[TA0010]](https://attack.mitre.org/tactics/TA0010/) **Exfil**, [[TA0008]](https://attack.mitre.org/tactics/TA0008/) **Lat. Mov.** | 🔍 Accesos desde IPs no autorizadas, Exports `*`, `no_root_squash` en uso, Tráfico NFS a/desde Internet. Mount logs. | 🛡️ **Fix:** 1. Usar NFSv4 con Kerberos (`sec=krb5p`) | 2. Principio de Mínimo Privilegio en Exports | 3. Firewalling.

### [SFTP (sobre SSH)](https://datatracker.ietf.org/doc/html/rfc4253)
🔌 `22/TCP` | 🎯 Transfer Segura | 💀💀 | ⚠️ [[TA0010]](https://attack.mitre.org/tactics/TA0010/) **Exfil Cifrado**, [[TA0148]](https://attack.mitre.org/tactics/TA0148/) **Malware Upload** | 🔍 Ver SSH. Transferencias volumen/frecuencia anómalas. | 🛡️ **Fix:** Ver SSH. Monitorizar volúmenes transferencia.

---

## 📧 Email

### [SMTP](https://datatracker.ietf.org/doc/html/rfc5321)
🔌 `25/TCP`(Srv), `587/TCP`(TLS Sub), `465/TCP`(Leg SMTPS) | 🎯 Envío Email | 💀💀💀 | ⚠️ [[T1566]](https://attack.mitre.org/techniques/T1566/) **Phishing/Malware**, [[T1071.003]](https://attack.mitre.org/techniques/T1071/003/) **C2 via Email**, [[T1534]](https://attack.mitre.org/techniques/T1534/) **Internal Spearphishing**, Open Relay | 🔍 Alto vol. salida (`> baseline`), Conexión MX externos inusuales, Fallos SPF/DKIM/DMARC (`Authentication-Results`), Relaying interno anómalo, Headers sospechosos (`X-`, `Received:` path). | 🛡️ **Fix:** 1. Implementar SPF/DKIM/DMARC | 2. Filtrado Anti-Spam/Malware Gateway | 3. Cerrar Open Relays | 4. Usar STARTTLS/SMTPS.

### [IMAP](https://datatracker.ietf.org/doc/html/rfc3501)
🔌 `143/TCP`, `993/TCP`(IMAPS) | 🎯 Acceso Email Server | 💀💀 | ⚠️ [[T1114]](https://attack.mitre.org/techniques/T1114/) **Acceso Buzón**, [[T1078]](https://attack.mitre.org/techniques/T1078/) **Fuerza Bruta/Creds**, [[TA0006]](https://attack.mitre.org/tactics/TA0006/) **Creds Claro**(143) | 🔍 Fallos login (`> thresh`), Acceso IP/GEO/User-Agent inusual, Uso `143/TCP`. Mail Server Logs (`Login OK/Fail`). | 🛡️ **Fix:** 1. Usar IMAPS (`993`) | 2. MFA | 3. Monitorizar logs acceso.

### [POP3](https://datatracker.ietf.org/doc/html/rfc1939)
🔌 `110/TCP`, `995/TCP`(POP3S) | 🎯 Descarga Email | 💀 | ⚠️ [[TA0006]](https://attack.mitre.org/tactics/TA0006/) **Creds Claro (110)** | 🔍 Uso `110/TCP`. | 🛡️ **Fix:** 1. Usar POP3S (`995`) o IMAPS | 2. Deshabilitar si no es necesario.

---

## 💻 Remote Access & Management

### [SSH](https://datatracker.ietf.org/doc/html/rfc4251)
🔌 `22/TCP` | 🎯 CLI Seguro, Túnel, SFTP | 💀💀💀 | ⚠️ [[T1078]](https://attack.mitre.org/techniques/T1078/) **Fuerza Bruta/Creds/Claves Robadas**, [[T1090]](https://attack.mitre.org/techniques/T1090/) **Proxy/Tunneling C2/Exfil `[T1572]`**, [[T1021.004]](https://attack.mitre.org/techniques/T1021/004/) **Lat. Mov.** | 🔍 Fallos login (`> thresh`), Login IP/GEO inusual, Conexiones persistentes/alto volumen/fuera horario, Uso túneles (`ssh -L/-R` process args), Modificaciones `authorized_keys`, Uso `scp`/`sftp`. Auth Logs (`/var/log/auth.log`, Win Evt `SSH Operational`). | 🛡️ **Fix:** 1. Deshabilitar auth por password (usar claves) | 2. MFA | 3. Fail2Ban/SSHGuard | 4. Jump Hosts | 5. Monitorizar logs SSH.

### [Telnet](https://datatracker.ietf.org/doc/html/rfc854)
🔌 `23/TCP` | 🎯 CLI (Obsoleto) | 💀 | ⚠️ [[TA0006]](https://attack.mitre.org/tactics/TA0006/) **INSEGURO: TODO CLARO** | 🔍 **CUALQUIER USO ES SOSPECHOSO.** `tcp.port == 23`. | 🛡️ **Fix:** 1. DESHABILITAR | 2. Reemplazar por SSH.

### [RDP](https://learn.microsoft.com/en-us/windows/win32/termserv/remote-desktop-protocol)
🔌 `3389/TCP,UDP` | 🎯 Win GUI Remoto | 💀💀💀 | ⚠️ [[T1078]](https://attack.mitre.org/techniques/T1078/) **Fuerza Bruta/Creds**, [[T1021.001]](https://attack.mitre.org/techniques/T1021/001/) **Lat. Mov.**, [[T1567]](https://attack.mitre.org/techniques/T1567/) **Exfil vía Clipboard**, [[T1219]](https://attack.mitre.org/techniques/T1219/) **Exploits** (`BlueKeep`) | 🔍 **Net:** RDP desde/hacia Internet (`BLOCK!`), Conexiones internas Wks-Wks/inusuales. **Logs:** `4624(10)`(Login OK - Anomalous Src/User/Time), `4625`(Fail>thresh), `4634/4647`(Logoff - session duration anomalies), RDP Operational logs (Conns, Disconns). | 🛡️ **Fix:** 1. **NUNCA** exponer RDP a Internet (Usar VPN/Gateway+MFA) | 2. Network Level Authentication (NLA) | 3. Strong Passwords/LAPS | 4. RestrictedAdmin Mode / Remote Credential Guard.

### [SNMP](https://datatracker.ietf.org/doc/html/rfc3411) (v3)
🔌 `161/UDP`(Query), `162/UDP`(Trap) | 🎯 Monitorización Red | 💀 | ⚠️ [[TA0007]](https://attack.mitre.org/tactics/TA0007/) **Recon** (v1/v2c `public`/`private`), [[T1498]](https://attack.mitre.org/techniques/T1498/.003) **DDoS Amp** | 🔍 Queries v1/v2c desde externo/inesperado, Comunidades débiles (`public`, `private`), Traps inusuales/volumen alto. `snmp.version != 3`. | 🛡️ **Fix:** 1. Usar **SNMPv3** (AuthPriv) | 2. ACLs en dispositivos | 3. Cambiar comunidades default.

---

## 🔐 Authentication & Directory

### [LDAP](https://datatracker.ietf.org/doc/html/rfc4511)
🔌 `389/TCP,UDP`, `636/TCP`(LDAPS) | 🎯 Acceso Directorios (AD) | 💀💀💀 | ⚠️ [[TA0007]](https://attack.mitre.org/tactics/TA0007/) **Recon** (Bind Anón. `[T1087.002]`), [[T1078]](https://attack.mitre.org/techniques/T1078/) **Fuerza Bruta/Creds Plaintext**(389), [[T1003.006]](https://attack.mitre.org/techniques/T1003/006/) **DCSync** (via RPC over LDAP?) | 🔍 Binds Anónimos (si deshab.), Fallos bind (`> thresh`), Queries grandes/inusuales (targeting groups/users `samaccountname=*`), Tráfico `389/TCP` vs `636/TCP`. AD Logs (Audit Directory Service Access - `EventID 4662` con GUIDs específicos, o `EventID 1644` para queries ineficientes/costosas en DCs >= 2012 R2). | 🛡️ **Fix:** 1. Requerir LDAPS (`636`) | 2. Deshabilitar Bind Anónimo | 3. Requerir firma/sellado LDAP (LDAP Signing/Channel Binding) | 4. Monitorizar logs DS Access.

### [Kerberos](https://datatracker.ietf.org/doc/html/rfc4120)
🔌 `88/TCP,UDP`(Auth), `464/TCP`(Pass Chg) | 🎯 Auth AD | 💀💀💀 | ⚠️ [[T1558.003]](https://attack.mitre.org/techniques/T1558/003/) **Kerberoasting**, [[T1558.002]](https://attack.mitre.org/techniques/T1558/002/) **Pass-the-Ticket**, [[T1558.001]](https://attack.mitre.org/techniques/T1558/001/) **Golden/Silver Ticket** | 🔍 EventID `4768`(TGT Req - Anomalías Src/User), `4769`(TGS Req - Anomalías SPN/RC4 Cipher/Alto Vol), `4771`(Auth Fail - Kerberos Pre-Auth), Picos actividad Kerberos, Uso cifrado `RC4_HMAC_MD5`. | 🛡️ **Fix:** 1. Passwords robustos (esp. Service Accounts) | 2. Monitorizar logs Kerberos (4768/4769/4771) | 3. Deshabilitar RC4 | 4. Protected Users Group.

### [RADIUS](https://datatracker.ietf.org/doc/html/rfc2865)
🔌 `1812,1813/UDP` (+Leg `1645,1646`) | 🎯 AAA (VPN, WiFi) | 💀💀 | ⚠️ [[T1078]](https://attack.mitre.org/techniques/T1078/) **Secreto Débil/Fuerza Bruta**, **Sniffing** (sin EAP seguro como `PEAP`/`EAP-TLS`) | 🔍 Fallos auth (`> thresh`), Peticiones desde NAS/IPs no autorizados, Uso EAP inseguro. Logs AAA Server (`Access-Request`, `Access-Challenge`, `Access-Accept`, `Access-Reject`). | 🛡️ **Fix:** 1. Secretos compartidos fuertes/únicos (o certs) | 2. Usar EAP seguro (`EAP-TLS`) | 3. Monitorizar logs RADIUS.

---

## 🏗️ Infra & Others

### [DHCP](https://datatracker.ietf.org/doc/html/rfc2131)
🔌 `67/UDP`(Srv), `68/UDP`(Cli) | 🎯 Asignación IP Auto | 💀💀 | ⚠️ [[T1557.003]](https://attack.mitre.org/techniques/T1557/003/) **Rogue DHCP (MitM)**, [[T1498]](https://attack.mitre.org/techniques/T1498/.002) **Starvation (DoS)** | 🔍 Múltiples `Offer` (DHCP Snooping logs, Port Security), Pool agotado, Opciones DHCP anómalas (WPAD, DNS Server). | 🛡️ **Fix:** 1. DHCP Snooping en switches | 2. Port Security | 3. Monitorizar logs servidor DHCP.

### [NTP](https://datatracker.ietf.org/doc/html/rfc5905)
🔌 `123/UDP` | 🎯 Sincro Reloj | 💀 | ⚠️ [[T1498]](https://attack.mitre.org/techniques/T1498/.003) **DDoS Amp**, Manipulación Tiempo | 🔍 Tráfico NTP a/desde externos no autorizados, Respuestas grandes (`readvar`), Desincronización > umbral. Logs NTP. | 🛡️ **Fix:** 1. Usar fuentes NTP internas/autorizadas | 2. Autenticación NTP (si posible) | 3. Monitorizar sincronización.

### [Syslog](https://datatracker.ietf.org/doc/html/rfc5424)
🔌 `514/UDP`(Legacy), `514/TCP`, `6514/TCP`(TLS) | 🎯 Envío Logs | 💀 | ⚠️ [[TA0009]](https://attack.mitre.org/tactics/TA0009/) **Pérdida (UDP), Sniffing/Mod (sin TLS)** | 🔍 Gaps en logs, Picos volumen, Fuentes inesperadas, Tráfico UDP/TCP `514` (preferir `6514`), Cambios formato inesperados. Health SIEM/Collector. | 🛡️ **Fix:** 1. Usar Syslog sobre TLS (`6514`) | 2. Monitorizar health colector/SIEM | 3. Verificar recepción logs críticos.

---

*Nota Final: Esta guía es una referencia rápida de élite. El contexto y la investigación profunda son siempre necesarios. Mantén tus herramientas y conocimientos actualizados.*
