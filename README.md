# Wireshark HTTP Traffic Analysis Laboratory
 **Language / Idioma:**  
[Read in English](#-english-version) | [Leer en Español](#-versión-en-español)

---

## 🇪🇸 Versión en Español**
### 1. Objetivo del Laboratorio
Demostrar de forma práctica los riesgos de seguridad críticos asociados con el uso de protocolos en texto plano (HTTP) mediante el análisis de paquetes en un entorno controlado. Esta auditoría valida la exposición de credenciales de autenticación cuando se prescinde de cifrado TLS/SSL en la capa de transporte.

### 2. Topología y Entorno
**Host Atacante / Auditor:** Kali Linux VM (`127.0.0.1` - Enrutamiento dinámico local) 
* **Mecanismo de Evasión:** Proxychains4 + Tor Network (Proxy SOCKS5 local en puerto 9050).
* **Plataforma Objetivo:** `http://altoro.testfire.net` (Aplicación web insegura de pruebas).
* **Interfaz de Captura:** Interfaz virtual del sistema (`any`) sin modo promiscuo.



### 3. Metodología y Procedimiento (Fases de Pentesting)
1. **Fase 1: Reconocimiento y Anonimato:** Se inicializó el demonio de Tor (`sudo systemctl start tor`) y se forzó el tráfico del navegador web a través de circuitos intermedios usando el comando `proxychains4 firefox http://altoro.testfire.net/login.jsp &`.
   <img width="649" height="512" alt="Evidencia 1 Enrutamiento de tráfico mediante Proxychains4" src="https://github.com/user-attachments/assets/1ea7689a-bb18-4bb6-a49c-e1e8e85390c5" />
3. **Fase 2: Escaneo y Monitoreo:** Se abrió Wireshark y se configuró la escucha activa sobre la interfaz global `any` para capturar el tráfico encapsulado localmente.
4. **Fase 3: Obtención de Acceso:** Se interactuó con el formulario de autenticación enviando credenciales de prueba (`jsmith` / `Password123!`). Posterior al envío, se detuvo la captura de paquetes y se aplicó el siguiente filtro de visualización en la capa de aplicación:
   ```text
   http.request.method == "POST"
    ```
   <img width="983" height="287" alt="2026-06-15_13-53" src="https://github.com/user-attachments/assets/c0a68e2e-dc55-41e9-8757-c9acb627c7bf" />

5. Resultados y Evidencias
• Evidencia Principal: Al inspeccionar el paquete HTTP POST aislado (Paquete No. 279), se desplegó el árbol de datos correspondiente a HTML Form URL Encoded: application/x-www-form-urlencoded. Los campos de identidad del usuario reflejaron en texto plano los valores exactos introducidos, confirmando la fuga total de confidencialidad en el canal.

<img width="979" height="557" alt="2026-06-15_13-56" src="https://github.com/user-attachments/assets/e205ed7b-d007-4383-a415-3f199f95c3d4" />


6. Conclusiones
• Impacto en Seguridad: El uso de HTTP expone directamente la organización a ataques de sniffing y Man-in-the-Middle (MitM).

• Remediación: Forzar el uso exclusivo de HTTPS implementando certificados TLS y activar políticas HSTS (HTTP Strict Transport Security) para mitigar cualquier intento de degradación de canal.

---
---

## 🇺🇸 English Version

### 1. Laboratory Objective
To practically demonstrate the critical security risks associated with cleartext protocols (HTTP) through packet analysis in a controlled environment. This audit validates how authentication credentials are exposed when TLS/SSL encryption is absent at the transport layer.

### 2. Topology and Environment
* **Attacker / Auditor Host:** Kali Linux VM (`127.0.0.1` - Local dynamic routing)
* **Evasion Mechanism:** Proxychains4 + Tor Network (Local SOCKS5 proxy on port 9050).
* **Target Environment:** `http://altoro.testfire.net` (Insecure public testing sandbox).
* **Capture Interface:** System virtual interface (`any`) without promiscuous mode.
<img width="649" height="512" alt="Evidencia 1 Enrutamiento de tráfico mediante Proxychains4" src="https://github.com/user-attachments/assets/26b86599-2b96-4180-9746-76abc709f06a" />

### 3. Methodology & Procedure (Pentesting Phases)
1. **Phase 1: Reconnaissance & Evasion:** Enabled the Tor daemon (`sudo systemctl start tor`) and forced web browser traffic through secure relay circuits using the command `proxychains4 firefox http://altoro.testfire.net/login.jsp &`.
2. <img width="649" height="512" alt="Evidencia 1 Enrutamiento de tráfico mediante Proxychains4" src="https://github.com/user-attachments/assets/1ea7689a-bb18-4bb6-a49c-e1e8e85390c5" />
3. **Phase 2: Scanning & Monitoring:** Launched Wireshark and configured active packet inspection on the global `any` interface to capture locally encapsulated traffic.
4. **Phase 3: Gaining Access:** Interacted with the login application by submitting test credentials (`jsmith` / `Password123!`). After submission, the capture was stopped, and the following application layer display filter was applied:
```text
   http.request.method == "POST"
  ```
 <img width="983" height="287" alt="2026-06-15_13-53" src="https://github.com/user-attachments/assets/c0a68e2e-dc55-41e9-8757-c9acb627c7bf" />

4. Results & Evidence
Core Evidence: Upon inspecting the isolated HTTP POST packet (Packet No. 279), the HTML Form URL Encoded: application/x-www-form-urlencoded structural block was expanded. The user identity fields explicitly revealed cleartext strings matching the input parameters, confirming a complete loss of confidentiality over the wire.

<img width="979" height="557" alt="2026-06-15_13-56" src="https://github.com/user-attachments/assets/e205ed7b-d007-4383-a415-3f199f95c3d4" />

6. Conclusions & Hardening
Security Impact: Unencrypted HTTP communication leaves communication links vulnerable to network sniffing and Man-in-the-Middle (MitM) positioning.

Remediation: Enforce global HTTPS deployment by provisioning valid TLS certificates and configuring HSTS (HTTP Strict Transport Security) flags to deny cleartext fallbacks.

7. ## Skills Demonstrated

- Network Packet Analysis  
- Wireshark Traffic Inspection  
- HTTP Protocol Analysis  
- Credential Exposure Assessment  
- Proxychains4 Configuration  
- Tor Network Routing  
- Security Documentation  
- Technical Reporting

8.## Ethical Notice

This laboratory was conducted exclusively in a controlled testing environment using the Altoro Mutual vulnerable training application. No unauthorized systems or third-party assets were targeted.

### Referencias / References
* Cisco Networking Academy. (2023). *Introduction to Cybersecurity: Packet Analysis and Unencrypted Protocols*. Cisco.
* OWASP Foundation. (2021). *Top 10 Security Risks: A02:2021-Cryptographic Failures*. https://owasp.org/Top10/A02_2021-Cryptographic_Failures/
* The Tor Project. (2026). *Proxychains and Tor Integration for Local Traffic Routing*. Tor Documentation. https://support.torproject.org/
* Wireshark Foundation. (2026). *Wireshark User's Guide: Display Filters and Application Layer Inspection*. Wireshark. https://www.wireshark.org/docs/wsug_html_chunked/




