# Caso 01 — Webshell y Escalada de Privilegios

![Severidad](https://img.shields.io/badge/Severidad-CRÍTICA-red)
![Estado](https://img.shields.io/badge/Estado-Cerrado-green)
![Marco Legal](https://img.shields.io/badge/Ley-29733%20%7C%2030096-blue)

## Metadata

| Campo | Detalle |
|-------|---------|
| **ID** | INC-2026-0306-001 |
| **Fecha** | 06 de marzo de 2026 |
| **Analista** | Álvaro Martínez Pachas |
| **Severidad** | CRÍTICA |
| **Estado** | Cerrado |
| **MITRE ATT&CK** | T1505.003 - Web Shell / T1059 - Command Execution / T1136 - Create Account / T1021 - Remote Services |

---

## 1. Resumen Ejecutivo

El 06 de marzo de 2026 a las 02:47 hrs se detectó un acceso no autorizado originado desde la IP `190.45.23.87` que comprometió los servidores `CONTABLE-01` y `RRHH-02`. El atacante explotó una vulnerabilidad en un archivo de carga web para ejecutar comandos remotos y crear un usuario persistente. El incidente fue contenido y los accesos no autorizados fueron bloqueados.

---

## 2. Cronología del Ataque

```
02:45 hrs — IP 190.45.23.87 sube archivo malicioso via upload.php
            en SERVIDOR-CONTABLE-01

02:46 hrs — Atacante prueba ejecución de comandos remotos
            ejecutando comando "id"

02:47 hrs — Atacante crea usuario "support" modificando /etc/passwd
            Alerta Wazuh: Regla 550 - Modificación de archivo crítico

02:51 hrs — Atacante accede exitosamente a SERVIDOR-RRHH-02
            via SSH con usuario "support" (movimiento lateral)

02:53 hrs — Intentos fallidos de acceso a SERVIDOR-GERENCIA-03
02:54 hrs — Intentos fallidos de acceso a SERVIDOR-BACKUP-04
```

---

## 3. Evidencia — Logs

### Alerta Wazuh inicial
```
Timestamp: 2026-03-06 02:47:33
Agente: SERVIDOR-CONTABLE-01
Regla: 550 - Modificación de archivo crítico
Nivel: 7
Archivo modificado: /etc/passwd
Usuario que ejecutó: www-data
Proceso: apache2
IP origen: 190.45.23.87 (Lima, Perú)
```

### Logs Apache
```
190.45.23.87 - [06/Mar/2026:02:45:11] "POST /upload.php HTTP/1.1" 200 512
190.45.23.87 - [06/Mar/2026:02:46:44] "GET /upload.php?cmd=id" 200 18
190.45.23.87 - [06/Mar/2026:02:47:33] "GET /upload.php?cmd=useradd+support" 200 12
```

### Usuario creado en /etc/passwd
```
support:x:1001:1001::/home/support:/bin/bash
```

### Logs de red — Movimiento lateral
```
02:51:14 — 190.45.23.87 → SERVIDOR-RRHH-02    Puerto 22 — EXITOSO
02:53:44 — 190.45.23.87 → SERVIDOR-GERENCIA-03 Puerto 22 — FALLIDO
02:54:01 — 190.45.23.87 → SERVIDOR-BACKUP-04   Puerto 22 — FALLIDO
```

---

## 4. Sistemas Afectados

| Sistema | Criticidad | Motivo |
|---------|-----------|--------|
| SERVIDOR-CONTABLE-01 | Alta | Vector inicial. Información financiera del negocio |
| SERVIDOR-RRHH-02 | Crítica | Datos personales de empleados — Ley 29733 |

---

## 5. Causa Raíz

Existencia del archivo `upload.php` con capacidad de ejecución de comandos del sistema operativo en `SERVIDOR-CONTABLE-01`. Este archivo no debería existir en producción o debería tener controles estrictos de validación de contenido.

---

## 6. Técnica Utilizada — MITRE ATT&CK

| Técnica | ID MITRE | Descripción |
|---------|----------|-------------|
| Web Shell | T1505.003 | upload.php usado como puerta de ejecución remota |
| Command Execution | T1059 | Ejecución de comandos via parámetro ?cmd= |
| Create Account | T1136 | Creación de usuario "support" para persistencia |
| Remote Services | T1021 | Movimiento lateral via SSH con credenciales creadas |

---

## 7. Impacto

- Acceso confirmado a servidor con datos personales de empleados
- Activación de obligación de notificación bajo **Ley 29733**
- Creación de usuario persistente en dos servidores

---

## 8. Acciones Tomadas

- [x] Bloqueo de IP `190.45.23.87` en firewall perimetral
- [x] Deshabilitación de `upload.php` en todos los servidores
- [x] Eliminación del usuario `support`
- [x] Revisión de logs en toda la arquitectura

---

## 9. Recomendaciones

1. Realizar escaneo trimestral de vulnerabilidades en toda la infraestructura
2. Implementar política de revisión de código antes de subir archivos a producción
3. Configurar alertas de creación de usuarios en horario nocturno
4. Revisar política de acceso SSH entre servidores internos
5. Capacitar al equipo de desarrollo en prácticas seguras de programación

---

## 10. Marco Legal Aplicable

| Ley | Descripción | Aplicación |
|-----|-------------|------------|
| **Ley 29733** | Protección de Datos Personales | Acceso a servidor RRHH con datos de empleados |
| **Ley 30096** | Delitos Informáticos | Acceso no autorizado a sistemas — pena 1 a 4 años |

---

## Lección del Caso

> Un archivo de subida sin validación en producción es una puerta abierta.
> El antivirus no detecta webshells porque no son malware tradicional.
> La hora del ataque (02:47 AM) no es casualidad — el atacante conoce los horarios de vigilancia.
