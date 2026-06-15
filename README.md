### 1. Objetivo del Laboratorio
Demostrar de forma práctica los riesgos de seguridad críticos asociados con el uso de protocolos en texto plano (HTTP) mediante el análisis de paquetes en un entorno controlado. Esta auditoría valida la exposición de credenciales de autenticación cuando se prescinde de cifrado TLS/SSL en la capa de transporte.

### 2. Topología y Entorno
**Host Atacante / Auditor:** Kali Linux VM (`127.0.0.1` - Enrutamiento dinámico local) 
* **Mecanismo de Evasión:** Proxychains4 + Tor Network (Proxy SOCKS5 local en puerto 9050).
* **Plataforma Objetivo:** `http://altoro.testfire.net` (Aplicación web insegura de pruebas).
* **Interfaz de Captura:** Interfaz virtual del sistema (`any`) sin modo promiscuo.

### 3. Metodología y Procedimiento (Fases de Pentesting)
1. **Fase 1: Reconocimiento y Anonimato:** Se inicializó el demonio de Tor (`sudo systemctl start tor`) y se forzó el tráfico del navegador web a través de circuitos intermedios usando el comando `proxychains4 firefox http://altoro.testfire.net/login.jsp &`.
2. **Fase 2: Escaneo y Monitoreo:** Se abrió Wireshark y se configuró la escucha activa sobre la interfaz global `any` para capturar el tráfico encapsulado localmente.
3. **Fase 3: Obtención de Acceso:** Se interactuó con el formulario de autenticación enviando credenciales de prueba (`jsmith` / `Password123!`). Posterior al envío, se detuvo la captura de paquetes y se aplicó el siguiente filtro de visualización en la capa de aplicación:
   ```text
   http.request.method == "POST"
    ```
   <img width="983" height="287" alt="2026-06-15_13-53" src="https://github.com/user-attachments/assets/c0a68e2e-dc55-41e9-8757-c9acb627c7bf" />

4. Resultados y Evidencias
• Evidencia Principal: Al inspeccionar el paquete HTTP POST aislado (Paquete No. 279), se desplegó el árbol de datos correspondiente a HTML Form URL Encoded: application/x-www-form-urlencoded. Los campos de identidad del usuario reflejaron en texto plano los valores exactos introducidos, confirmando la fuga total de confidencialidad en el canal.
<img width="979" height="557" alt="2026-06-15_13-56" src="https://github.com/user-attachments/assets/e205ed7b-d007-4383-a415-3f199f95c3d4" />


5. Conclusiones
• Impacto en Seguridad: El uso de HTTP expone directamente la organización a ataques de sniffing y Man-in-the-Middle (MitM).

• Remediación: Forzar el uso exclusivo de HTTPS implementando certificados TLS y activar políticas HSTS (HTTP Strict Transport Security) para mitigar cualquier intento de degradación de canal.

---

### Referencias / References
* Cisco Networking Academy. (2023). *Introduction to Cybersecurity: Packet Analysis and Unencrypted Protocols*. Cisco.
* OWASP Foundation. (2021). *Top 10 Security Risks: A02:2021-Cryptographic Failures*. https://owasp.org/Top10/A02_2021-Cryptographic_Failures/
* The Tor Project. (2026). *Proxychains and Tor Integration for Local Traffic Routing*. Tor Documentation. https://support.torproject.org/
* Wireshark Foundation. (2026). *Wireshark User's Guide: Display Filters and Application Layer Inspection*. Wireshark. https://www.wireshark.org/docs/wsug_html_chunked/




