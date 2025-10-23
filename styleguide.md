# Guia de Estilo Universal de Desarrollo Seguro – Company Secure Standards

# Introduccion
Esta guia define las convenciones universales de codificacion y arquitectura seguras aplicables a cualquier lenguaje de programacion.  
Esta alineada con **OWASP Top 10 (2021)**, **OWASP ASVS**, **NIST SP 800-53** y buenas practicas de desarrollo seguro.  
Su objetivo es fortalecer la **confidencialidad**, **integridad** y **disponibilidad** (modelo CIA) del software, garantizando calidad, mantenibilidad y cumplimiento normativo.

# Principios Fundamentales
* **Seguridad por diseno:** toda decision de arquitectura debe considerar amenazas y controles de mitigacion.
* **Mantenibilidad:** el codigo debe ser legible, modular y documentado.
* **Validacion temprana:** toda entrada, parametro o evento externo debe validarse antes de procesarse.
* **Principio de minimo privilegio:** los componentes y usuarios solo deben tener los permisos necesarios.
* **Transparencia y trazabilidad:** el sistema debe registrar eventos criticos, errores y accesos relevantes.
* **Consistencia:** seguir un estandar uniforme reduce errores y facilita auditorias.

---

# 1. Estructura del Codigo y Nomenclatura
* Mantn una estructura clara: **configuracion**, **logica de negocio**, **validacion** y **datos** deben estar separados.
* Nombrar variables, clases y funciones de manera **descriptiva y consistente**:
  * Variables y funciones  'snake_case'
  * Clases y estructuras  'PascalCase'
  * Constantes  'MAYuSCULAS_CON_GUIONES_BAJOS'
* No uses abreviaturas ambiguas ni nombres genricos como 'data', 'tmp', 'foo'.

---

# 2. Validacion y Saneamiento de Entradas (OWASP A03:2021 – Inyeccion)
* Valida **todas las entradas** provenientes de usuarios, APIs, archivos, formularios o servicios externos.
* Usa **listas blancas (whitelisting)** en lugar de listas negras.
* Escapa correctamente las entradas antes de construir comandos o consultas.
* Evita la concatenacion directa en SQL, Shell, LDAP o XML.
* Usa **ORMs seguros**, **consultas parametrizadas** o **sentencias preparadas**.
* En APIs, valida tipos, rangos y formato (por ejemplo, 'email', 'uuid', 'int').
* Sanea los datos de salida para prevenir **XSS**, **HTML Injection** o **template injection**.

---

# 3. Autenticacion y Gestion de Sesiones (OWASP A07 y A01)
* Implementa autenticacion robusta (por ejemplo, JWT con firma y expiracion).
* Nunca almacenes contrasenas en texto plano.  
  Usa **bcrypt**, **Argon2id** o **PBKDF2** con sal aleatoria.
* Expira tokens y sesiones inactivas.
* Implementa **bloqueo tras multiples intentos fallidos**.
* Usa **cookies seguras** con las banderas 'HttpOnly' y 'Secure'.
* Evita exponer tokens o credenciales en URLs, logs o frontend.

---

# 4. Control de Acceso (OWASP A01)
* Aplica el principio de **Zero Trust**: nunca confies en la identidad del cliente.
* Valida el **rol y contexto** en cada solicitud del backend.
* No confies en validaciones del lado del cliente.
* Implementa control de acceso a nivel de **objeto** y **recurso** (evita IDOR).
* Audita y registra accesos a informacion sensible.

---

# 5. Criptografia y Manejo de Secretos (OWASP A02)
* Nunca codifiques ni subas **claves, contrasenas o tokens** al repositorio.
* Usa **vaults o gestores de secretos** (ej. HashiCorp Vault, AWS Secrets Manager, Azure Key Vault).
* Utiliza algoritmos criptograficos actuales:
  * Cifrado simtrico: AES-256-GCM
  * Cifrado asimtrico: RSA-2048+ o ECC (P-256+)
  * Hash seguro: SHA-256, SHA3-512
* No reutilices IV/Nonce.
* Aplica rotacion periodica de claves.
* Cifra los datos en transito (TLS 1.2+) y en reposo.

---

# 6. Errores, Excepciones y Logging Seguro (OWASP A09)
* No expongas detalles tcnicos en mensajes de error.
* Usa un marco de **logging centralizado** con niveles: DEBUG, INFO, WARNING, ERROR, CRITICAL.
* No registres contrasenas, tokens ni informacion personal.
* Usa IDs de correlacion para trazabilidad.
* Asegura que los logs estn protegidos contra escritura no autorizada.

---

# 7. Gestion de Dependencias y Componentes (OWASP A06)
* Mantn todas las librerias y frameworks actualizados.
* Usa herramientas como **OWASP Dependency-Check**, **npm audit**, **pip-audit**, **Trivy**, etc.
* Verifica firmas o checksums de paquetes descargados.
* Evita dependencias sin mantenimiento o con vulnerabilidades conocidas (CVE).
* Usa versiones fijas ('pinning') en los archivos de dependencia.

---

# 8. Seguridad en APIs y Servicios Web (OWASP A05)
* Usa HTTPS obligatorio.
* Implementa **rate limiting** y **throttling**.
* Aplica autenticacion basada en tokens (OAuth2, OpenID Connect, API Keys).
* Define esquemas de validacion JSON/XML para cada endpoint.
* Responde con codigos de estado HTTP adecuados.
* Implementa CORS restringido a dominios de confianza.
* Evita exponer informacion sensible en los mensajes de error o cabeceras.

---

# 9. Seguridad del Frontend y UX Seguro
* Escapa dinamicamente el contenido HTML (prevencion XSS).
* Usa cabeceras HTTP de seguridad:
  * 'Content-Security-Policy'
  * 'X-Frame-Options: DENY'
  * 'X-Content-Type-Options: nosniff'
* No confies en validaciones del lado del cliente.
* Oculta rutas y endpoints administrativos.
* Implementa MFA y CAPTCHA cuando aplique.

---

# 10. Arquitectura y DevSecOps
* Implementa revisiones de codigo automaticas (Gemini, OpenAI, SonarQube).
* Integra analisis SAST, DAST e IaC (Terraform, CloudFormation).
* Usa CI/CD con politicas de firma y escaneo de artefactos.
* Define entornos segregados: dev, qa, prod.
* Aplica **seguridad en pipelines**: control de secretos, dependencias y permisos.
* Define backups cifrados y pruebas periodicas de restauracion.

---

# 11. Hardening de Entorno y Configuracion
* Desactiva servicios y puertos no utilizados.
* Aplica el principio de **infraestructura inmutable** (IaC + control de version).
* Usa cuentas de servicio con privilegios minimos.
* Asegura bases de datos con usuarios especificos, contrasenas fuertes y TLS.
* Protege archivos de configuracion con permisos estrictos.
* Revisa y minimiza configuraciones predeterminadas.

---

# 12. Comentarios y Documentacion
* Comenta el **por qu** y no solo el **qu**.
* Usa lenguaje claro, sin informacion sensible.
* Documenta decisiones de seguridad y dependencias criticas.
* Mantn README actualizado con dependencias, variables de entorno y politicas de seguridad.

---

# 13. Ejemplo de Buenas Practicas Transversales
'''js
// Ejemplo de manejo seguro de entrada y consulta parametrizada
app.post("/login", async (req, res) => {
  const { username, password } = req.body;

  // Validacion y saneamiento
  if (!/^[a-zA-Z0-9_]{3,20}$/.test(username)) {
    return res.status(400).json({ error: "Nombre de usuario invalido" });
  }

  const user = await db.query("SELECT * FROM users WHERE username = $1", [username]);
  if (!user) return res.status(401).json({ error: "Usuario no encontrado" });

  const valid = await bcrypt.compare(password, user.password_hash);
  if (!valid) return res.status(403).json({ error: "Contrasena incorrecta" });

  const token = jwt.sign({ id: user.id }, process.env.JWT_SECRET, { expiresIn: "8h" });
  res.cookie("session", token, { httpOnly: true, secure: true });
  res.json({ message: "Acceso concedido" });
});
