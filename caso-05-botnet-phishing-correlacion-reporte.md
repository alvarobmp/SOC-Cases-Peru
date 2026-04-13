# Caso 05 — Botnet Interna y Campaña de Phishing Corporativo

![Severidad](https://img.shields.io/badge/Severidad-CRÍTICA-red)
![Estado](https://img.shields.io/badge/Estado-Contenido-orange)
![Marco Legal](https://img.shields.io/badge/Ley-29733%20%7C%2030096%20%7C%2026702%20%7C%2029571-blue)

## Metadata

| Campo | Detalle |
|-------|---------|
| **ID** | INC-2026-0307-005 |
| **Fecha** | 07 de marzo de 2026 |
| **Analista** | Álvaro Martínez Pachas |
| **Severidad** | CRÍTICA — Requiere decisión gerencial inmediata |
| **Estado** | Contenido — Clientes externos afectados |
| **MITRE ATT&CK** | T1566 - Phishing / T1587.001 - Malware / T1584 - Botnet / T1598 - Phishing for Information |

---

## 1. Resumen Ejecutivo
*(Para Gerencia General — sin tecnicismos)*

Un grupo de computadoras internas fue infectado por software malicioso no autorizado, formando una red de equipos que opera automáticamente de madrugada enviando correos fraudulentos a clientes del banco. El objetivo del atacante es robar las credenciales bancarias de nuestros clientes. Esto representa un riesgo reputacional, legal y financiero activo que lleva **3 días en curso sin detección**.

**Decisión urgente requerida:** Autorizar el envío inmediato de un comunicado a todos los clientes indicando que el banco nunca solicita credenciales por correo electrónico y que no deben hacer clic en enlaces sospechosos.

---

## 2. Origen del Caso — Tres Tickets No Correlacionados

> **Este caso demuestra la diferencia entre operar herramientas y analizar incidentes.**
> Tres analistas trataron el mismo ataque como tres problemas separados durante 3 días.

| Ticket | Analista | Hora | Descripción | Estado inicial |
|--------|----------|------|-------------|----------------|
| #1 | Gómez | 09:15 AM | PC de rlopez va lenta. Sin malware detectado | **Cerrado** — Hardware |
| #2 | Torres | 11:42 AM | Tráfico inusual en switch piso 3, 02:00-04:00 AM | Abierto — Sin resolución |
| #3 | Mendoza | 03:30 PM | Cliente recibe correo de banco-seguro.com | Abierto — Derivado a Marketing |

---

## 3. Cronología del Ataque

```
Hace 3+ días:
PC de rlopez infectada con malware LotL
(Living off the Land — indetectable por antivirus)

Últimas 72 horas — Propagación:
Malware se replica silenciosamente a PCs del piso 3
via switch de red — hasta 40 equipos potencialmente infectados

Cada noche, 02:00-04:00 AM:
Botnet activada en horario de mínima vigilancia SOC

Actividad nocturna (logs DNS):
192.168.3.45 (rlopez) → banco-seguro.com      847 consultas/noche
192.168.3.45 (rlopez) → mail.banco-seguro.com  423 consultas/noche
192.168.3.45 (rlopez) → cdn.banco-seguro.com   234 consultas/noche

Mismo día:
Clientes externos reciben correos phishing
aparentemente enviados por el banco
```

---

## 4. Evidencia — Logs

### Logs DNS — PC de rlopez (últimas 72 horas)
```
02:00-04:00 AM — 192.168.3.45 → banco-seguro.com      x847
02:00-04:00 AM — 192.168.3.45 → mail.banco-seguro.com  x423
02:00-04:00 AM — 192.168.3.45 → cdn.banco-seguro.com   x234
```

### Dominio fraudulento analizado
```
Dominio:    banco-seguro.com
Registros:  SPF  ✓ configurado
            DKIM ✓ configurado
            DMARC✓ configurado
Conclusión: Correos pasan filtros de spam
            y aparecen como legítimos en Gmail/Outlook
```

### Ticket #1 — Análisis antivirus
```
PC rlopez — Escaneo completo
Resultado: SIN AMENAZAS DETECTADAS
Motivo real: Malware LotL usa herramientas
             legítimas del SO — no hay firma conocida
```

---

## 5. ¿Qué es Living off the Land (LotL)?

```
El malware tradicional instala archivos nuevos
→ El antivirus los detecta por firma

LotL no instala nada nuevo.
Usa herramientas que ya existen en Windows:
PowerShell, WMI, certutil, mshta, etc.

→ El antivirus no detecta nada porque
  todo parece uso legítimo del sistema

Por eso el Ticket #1 fue cerrado como "hardware":
el antivirus no encontró nada porque no había
nada "nuevo" que encontrar.
```

---

## 6. Anatomía del Ataque — Visión Completa

```
FASE 1 — INFECCIÓN INICIAL
Atacante infecta PC de rlopez con malware LotL
Antivirus no detecta nada → Ticket cerrado como hardware

FASE 2 — PROPAGACIÓN
Malware se replica a PCs del piso 3 via red interna
Switch muestra tráfico inusual → Ticket sin resolución

FASE 3 — INFRAESTRUCTURA
Atacante registra banco-seguro.com
Configura SPF/DKIM/DMARC para legitimidad
Replica contenido visual del banco

FASE 4 — OPERACIÓN NOCTURNA
02:00-04:00 AM: Botnet activa
PCs infectadas envían correos phishing masivos
Horario elegido por mínima vigilancia SOC

FASE 5 — IMPACTO EN CLIENTES
Clientes reciben correos aparentemente oficiales
Objetivo: capturar credenciales de banca online
```

---

## 7. Sistemas y Datos Afectados

| Sistema | Impacto | Criticidad |
|---------|---------|-----------|
| PCs piso 3 (hasta 40 equipos) | Infectadas y operando como botnet | Crítica |
| Red interna banco | Tráfico malicioso nocturno | Alta |
| Base de correos de clientes | Usada para envío de phishing | Crítica |
| Reputación institucional | Daño activo y en curso | Crítica |

---

## 8. Técnica Utilizada — MITRE ATT&CK

| Técnica | ID MITRE | Descripción |
|---------|----------|-------------|
| Phishing | T1566 | Correos fraudulentos a clientes del banco |
| LotL Malware | T1587.001 | Malware que evade antivirus usando herramientas del SO |
| Botnet | T1584.005 | Red de PCs infectadas bajo control del atacante |
| Typosquatting | T1583.001 | Dominio banco-seguro.com similar al oficial |
| Email Spoofing | T1598 | SPF/DKIM/DMARC configurados para parecer legítimo |

---

## 9. Impacto

- Clientes del banco recibiendo correos fraudulentos durante 3 días
- Posibles credenciales bancarias de clientes comprometidas
- Daño reputacional activo
- Tres analistas trataron el incidente como problemas separados durante 72 horas

---

## 10. Línea de Investigación Pendiente

- [ ] Identificar todas las PCs infectadas en la red
- [ ] Determinar vector inicial de infección en PC de rlopez
- [ ] Cuantificar cuántos clientes recibieron correos fraudulentos
- [ ] Determinar cuántos clientes hicieron clic o ingresaron credenciales
- [ ] Investigar si el ataque se extendió a otros pisos o sedes

---

## 11. Acciones Tomadas

- [x] Aislamiento de PCs infectadas del piso 3
- [x] Bloqueo de dominio `banco-seguro.com` en firewall y DNS
- [x] Preservación de logs para investigación forense y denuncia penal
- [x] Notificación a área legal y gerencia general
- [x] Correlación de los tres tickets e identificación del ataque

---

## 12. Recomendaciones

### Inmediatas
1. **Autorizar comunicado urgente a clientes:** "El banco nunca solicita contraseñas por correo. No haga clic en enlaces sospechosos."
2. Auditar todas las PCs de la red en busca de malware LotL
3. Reportar incidente a SBS y PNP

### Corto plazo
4. Implementar correlación automática de tickets en el SOC — patrones similares deben agruparse automáticamente
5. Capacitar a analistas en identificación de ataques coordinados multi-vector
6. Implementar monitoreo de dominios similares al del banco (typosquatting)
7. Bloquear tráfico de red saliente no autorizado en horario nocturno
8. Implementar regla SIEM: alertar si PC genera +100 consultas DNS al mismo dominio externo en menos de 1 hora

### Largo plazo
9. Implementar EDR (Endpoint Detection and Response) que detecte comportamiento LotL
10. Crear protocolo de escalamiento cuando múltiples tickets comparten características similares

---

## 13. Marco Legal Aplicable

| Ley | Descripción | Aplicación |
|-----|-------------|------------|
| **Ley 29733** | Protección de Datos Personales | Correos de clientes usados para phishing |
| **Ley 30096** | Delitos Informáticos | Infección de sistemas y phishing son delitos penales |
| **Ley 26702** | Sistema Financiero y Seguros | Incidente que afecta clientes bancarios — reporte a SBS |
| **Ley 29571** | Código Protección al Consumidor | Clientes afectados por fraude tienen derecho a notificación |

---

## Lección del Caso

> Este caso no fue detectado por ninguna herramienta de seguridad.
> Fue detectado por un analista que correlacionó tres tickets aparentemente separados.
> El antivirus dijo "limpio". El switch mostró tráfico. Un cliente reportó el correo.
> Ninguna alerta individual era suficiente. Juntas revelaron una botnet activa de 3 días.
>
> **Esa capacidad de correlación es la habilidad que ninguna herramienta reemplaza.**
