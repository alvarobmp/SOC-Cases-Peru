# 🛡️ SOC Cases Peru

> Análisis de incidentes de seguridad documentados con marco legal peruano.

[![Casos](https://img.shields.io/badge/Casos%20documentados-5-blue)]()
[![Marco Legal](https://img.shields.io/badge/Leyes-29733%20%7C%2030096%20%7C%2026702%20%7C%2029571-green)]()
[![MITRE](https://img.shields.io/badge/Framework-MITRE%20ATT%26CK-red)]()
[![Idioma](https://img.shields.io/badge/Idioma-Español-orange)]()

---

## ¿Por qué este repositorio?

La mayoría de recursos de análisis SOC están en inglés y sin contexto legal local.

Este repositorio documenta casos de seguridad reales con:
- **Metodología de triage** paso a paso
- **Framework MITRE ATT&CK** aplicado a cada técnica
- **Marco legal peruano** integrado en cada análisis
- **Lecciones aprendidas** orientadas al mercado LATAM

---

## Casos Documentados

| # | Caso | Técnica | Severidad | Leyes |
|---|------|---------|-----------|-------|
| 01 | [Webshell y Escalada de Privilegios](./caso-01-webshell-escalada-privilegios-reporte.md) | Webshell / Command Execution / Lateral Movement | 🔴 CRÍTICA | 29733, 30096 |
| 02 | [Session Hijacking y Movimiento Lateral](./caso-02-session-hijacking-movimiento-lateral-reporte.md) | Session Hijacking / Valid Accounts / Exfiltration | 🔴 CRÍTICA | 29733, 30096 |
| 03 | [Low and Slow Attack](./caso-03-low-and-slow-attack-reporte.md) | Scheduled Task / Low and Slow Exfiltration | 🔴 CRÍTICA | 29733, 30096 |
| 04 | [Cyber Espionaje e Insider Threat](./caso-04-cyber-espionaje-insider-threat-reporte.md) | TOR Proxy / Valid Accounts / Data Collection | 🔴 CRÍTICA | 29733, 30096, 26702 |
| 05 | [Botnet y Campaña de Phishing](./caso-05-botnet-phishing-correlacion-reporte.md) | LotL Malware / Botnet / Phishing / Typosquatting | 🔴 CRÍTICA | 29733, 30096, 26702, 29571 |

---

## Estructura de cada reporte

```
1. Metadata (ID, fecha, analista, MITRE ATT&CK)
2. Resumen ejecutivo (para gerencia — sin tecnicismos)
3. Cronología del ataque
4. Evidencia — Logs
5. Sistemas y datos afectados
6. Técnica utilizada — MITRE ATT&CK
7. Impacto
8. Línea de investigación pendiente
9. Acciones tomadas
10. Recomendaciones
11. Marco legal peruano aplicable
12. Lección del caso
```

---

## Marco Legal Peruano

| Ley | Descripción |
|-----|-------------|
| **Ley 29733** | Ley de Protección de Datos Personales |
| **Ley 30096** | Ley de Delitos Informáticos |
| **Ley 26702** | Ley General del Sistema Financiero y de Seguros |
| **Ley 29571** | Código de Protección y Defensa del Consumidor |

---

## Autor

**Álvaro Martínez Pachas**
Especialista en Infraestructura TI | Analista SOC en formación |
10 años de experiencia TI
📧 alvarobasiliosmart@gmail.com
🔗 [LinkedIn](https://linkedin.com/in/tu-perfil)
🐙 [GitHub](https://github.com/alvarobmp)

---

## ⚠️ Disclaimer

Los casos documentados en este repositorio son escenarios de práctica basados en técnicas de ataque reales. No representan incidentes de organizaciones específicas. Su propósito es educativo y de desarrollo profesional en ciberseguridad.
