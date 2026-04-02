# Caso 02 — Session Hijacking y Movimiento Lateral

![Severidad](https://img.shields.io/badge/Severidad-CRÍTICA-red)
![Estado](https://img.shields.io/badge/Estado-Cerrado-green)
![Marco Legal](https://img.shields.io/badge/Ley-29733%20%7C%2030096-blue)

## Metadata

| Campo | Detalle |
|-------|---------|
| **ID** | INC-2026-0307-002 |
| **Fecha** | 07 de marzo de 2026 |
| **Analista** | Álvaro Martínez Pachas |
| **Severidad** | CRÍTICA |
| **Estado** | Cerrado — Investigación forense pendiente |
| **MITRE ATT&CK** | T1539 - Session Hijacking / T1078 - Valid Accounts / T1021 - Remote Services / T1048 - Exfiltration |

---

## 1. Resumen Ejecutivo

El 07 de marzo de 2026 a las 11:23 hrs se detectó acceso no autorizado mediante credenciales comprometidas del usuario `jperez`. Un atacante externo desde IP `190.23.45.12` utilizó dichas credenciales para iniciar sesión simultáneamente con la sesión interna activa del usuario, cerrando la sesión legítima automáticamente. Durante el acceso se descargaron 847 archivos del directorio `/CONTRATOS/CLIENTES`, activando obligaciones bajo la Ley 29733.

---

## 2. Cronología del Ataque

```
11:23:41 — jperez inicia sesión legítima desde IP interna 192.168.1.45

11:23:44 — Descarga automatizada de 847 archivos
           desde /CONTRATOS/CLIENTES en 3 segundos
           (velocidad imposible para un humano — script automatizado)

11:23:51 — Atacante inicia sesión externa desde IP 190.23.45.12
           con credenciales de jperez

11:23:51 — Sistema cierra sesión interna automáticamente
           jperez pierde acceso — percibe error de sistema
```

---

## 3. Evidencia — Logs

### Logs de acceso al sistema
```
2026-03-07 11:23:41 — usuario: jperez | Auth exitosa | IP: 192.168.1.45
2026-03-07 11:23:44 — usuario: jperez | Descarga: 847 archivos | /CONTRATOS/CLIENTES
2026-03-07 11:23:51 — usuario: jperez | Auth exitosa | IP: 190.23.45.12
2026-03-07 11:23:51 — usuario: jperez | Sesión cerrada | IP: 192.168.1.45
```

### Análisis de timestamps
```
11:23:41 → 11:23:44 = 3 segundos para descargar 847 archivos
           CONCLUSIÓN: Script automatizado, no acción humana

11:23:44 → 11:23:51 = 7 segundos entre descarga y sesión externa
           CONCLUSIÓN: Atacante esperó confirmación de descarga

11:23:51            = Sesión interna cerrada al abrirse sesión externa
           CONCLUSIÓN: Sistema no permite sesiones simultáneas
```

---

## 4. Sistemas y Datos Afectados

| Sistema | Datos | Criticidad |
|---------|-------|-----------|
| Servidor de archivos | 847 archivos de /CONTRATOS/CLIENTES | Crítica |
| Credenciales jperez | Usuario y contraseña comprometidos | Alta |

---

## 5. Causa Raíz

Credenciales de `jperez` comprometidas por método aún no determinado. La ausencia de autenticación multifactor (MFA) permitió el uso de credenciales robadas sin segunda verificación.

---

## 6. Técnica Utilizada — MITRE ATT&CK

| Técnica | ID MITRE | Descripción |
|---------|----------|-------------|
| Session Hijacking | T1539 | Toma de sesión activa con credenciales robadas |
| Valid Accounts | T1078 | Uso de credenciales legítimas para acceso |
| Automated Exfiltration | T1020 | Script descargó 847 archivos en 3 segundos |
| Exfiltration | T1048 | Extracción de contratos de clientes |

---

## 7. Análisis de jperez — ¿Víctima o Cómplice?

```
EVIDENCIA A FAVOR DE VÍCTIMA:
✓ Sesión interna activa legítima al momento del ataque
✓ Sesión externa cerró su sesión sin su control
✓ IP externa confirma acceso desde fuera de la organización

EVIDENCIA PENDIENTE DE ANÁLISIS:
? Origen del script de descarga en su equipo
? Si el script se ejecutó automáticamente o manualmente
? Historial de comandos en su PC local

CONCLUSIÓN ACTUAL:
jperez debe ser considerado VÍCTIMA hasta que
el análisis forense de su equipo indique lo contrario
```

---

## 8. Impacto

- Exfiltración confirmada de 847 archivos de contratos de clientes
- Contenido y nivel de confidencialidad de archivos por determinar
- Posible activación de Ley 29733 según naturaleza de los datos

---

## 9. Línea de Investigación Pendiente

- [ ] Análisis forense de PC de jperez
- [ ] Revisar historial de comandos y archivos recientes
- [ ] Determinar origen del script de descarga
- [ ] Inventariar los 847 archivos exfiltrados
- [ ] Identificar vector de compromiso de credenciales

---

## 10. Acciones Tomadas

- [x] Bloqueo de IP `190.23.45.12`
- [x] Suspensión temporal de credenciales de jperez
- [x] Reseteo forzado de contraseña
- [x] Inicio de análisis forense en equipo local de jperez
- [x] Preservación de logs para investigación

---

## 11. Recomendaciones

1. Implementar MFA para todos los usuarios de forma inmediata
2. Inventariar los 847 archivos exfiltrados para determinar impacto real
3. Revisar política de acceso al directorio `/CONTRATOS/CLIENTES`
4. Implementar alertas de descarga masiva de archivos
5. Evaluar obligación de notificación a clientes bajo Ley 29733

---

## 12. Marco Legal Aplicable

| Ley | Descripción | Aplicación |
|-----|-------------|------------|
| **Ley 29733** | Protección de Datos Personales | Contratos de clientes potencialmente exfiltrados |
| **Ley 30096** | Delitos Informáticos | Acceso no autorizado con credenciales ajenas |

---

## Lección del Caso

> 847 archivos en 3 segundos no es un humano. Es un script.
> MFA hubiese detenido este ataque aunque las credenciales estuvieran comprometidas.
> No confundas velocidad de eventos con acción humana — los timestamps revelan la verdad.
