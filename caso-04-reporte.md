# Caso 04 — Cyber Espionaje e Insider Threat

![Severidad](https://img.shields.io/badge/Severidad-CRÍTICA-red)
![Estado](https://img.shields.io/badge/Estado-Investigación%20Abierta-orange)
![Marco Legal](https://img.shields.io/badge/Ley-29733%20%7C%2030096%20%7C%2026702-blue)

## Metadata

| Campo | Detalle |
|-------|---------|
| **ID** | INC-2026-0307-004 |
| **Fecha** | 07 de marzo de 2026 |
| **Analista** | Álvaro Martínez Pachas |
| **Severidad** | CRÍTICA |
| **Estado** | Cerrado — Investigación forense y legal pendiente |
| **MITRE ATT&CK** | T1078 - Valid Accounts / T1090 - Proxy (TOR) / T1213 - Data from Information Repositories / T1530 - Collection |

---

## 1. Resumen Ejecutivo

Durante dos semanas consecutivas se detectó acceso sistemático al servidor de base de datos de una aseguradora utilizando credenciales del usuario `mrodriguez`, quien se encontraba en baja médica. El atacante operó exclusivamente en horario laboral para simular comportamiento normal, usando un nodo TOR para ocultar su ubicación real. El objetivo fue recopilar inteligencia sobre pólizas y siniestros pendientes — **Cyber Espionaje**, no sabotaje. El caso activa tres obligaciones legales simultáneas incluyendo reporte a la SBS.

---

## 2. Cronología del Ataque

```
Semana 1 — Lunes:
Registro RRHH: mrodriguez inicia baja médica

Semana 1 y 2 — Lunes a Miércoles:
08:01-08:05 — Login con credenciales mrodriguez
              desde IP 185.220.101.45 (Nodo TOR, Países Bajos)

Durante sesión:
              — Consultas a tabla POLIZAS_CLIENTES
              — Consultas a tabla SINIESTROS_PENDIENTES
              — Descarga de reportes PDF de siniestros
              — Sin modificaciones ni acciones administrativas

18:08-18:15 — Logout puntual dentro del horario laboral
```

---

## 3. Evidencia — Logs

### Logs de acceso al sistema
```
SEMANA 1:
Lunes    08:03 — mrodriguez | Login | IP: 185.220.101.45 (TOR)
Lunes    18:12 — mrodriguez | Logout
Martes   08:01 — mrodriguez | Login | IP: 185.220.101.45 (TOR)
Martes   18:08 — mrodriguez | Logout
Miércoles 08:05 — mrodriguez | Login | IP: 185.220.101.45 (TOR)
Miércoles 18:15 — mrodriguez | Logout

SEMANA 2: (patrón idéntico)
```

### Registro de RRHH
```
mrodriguez — Baja laboral por enfermedad
Fecha inicio: Semana 1, Lunes
Duración estimada: 2 semanas
```

### Actividad durante sesiones
```
Consultas diarias: SELECT * FROM POLIZAS_CLIENTES
Consultas diarias: SELECT * FROM SINIESTROS_PENDIENTES
Descargas:         Reportes PDF de siniestros
Modificaciones:    NINGUNA
Acciones admin:    NINGUNA
```

### IP Analysis
```
IP: 185.220.101.45
Tipo: Nodo de salida TOR
País: Países Bajos
Conclusión: Atacante con conocimiento técnico avanzado
            que deliberadamente anonimizó su ubicación
```

---

## 4. Sistemas y Datos Afectados

| Sistema | Datos | Criticidad |
|---------|-------|-----------|
| Servidor de base de datos | Tabla POLIZAS_CLIENTES | Crítica |
| Servidor de base de datos | Tabla SINIESTROS_PENDIENTES | Crítica |
| Repositorio de reportes | PDFs de siniestros | Alta |

---

## 5. Perfil del Atacante

```
CONOCIMIENTO INTERNO:
✓ Sabía que mrodriguez estaba de baja médica
✓ Conocía el horario laboral exacto de la empresa
✓ Sabía que mrodriguez tenía acceso a POLIZAS y SINIESTROS
✓ Entendió que nadie verificaría actividad durante la baja

CONOCIMIENTO TÉCNICO:
✓ Uso de red TOR para anonimato
✓ Operó sin modificar datos para no generar alertas
✓ Mantuvo patrón de comportamiento normal del usuario

CONCLUSIÓN:
Ataque planificado con información interna.
No es oportunista. Es dirigido.
```

---

## 6. Análisis — ¿mrodriguez es víctima o cómplice?

```
INDICA VÍCTIMA:
+ IP TOR desde Países Bajos descarta presencia física
+ Credenciales comprometidas antes de la baja

INDICA POSIBLE CÓMPLICE:
- Atacante conocía exactamente la fecha de inicio de baja
- Atacante conocía exactamente qué tablas tenía acceso
- Coincidencia temporal exacta con inicio de baja médica

ESTADO ACTUAL:
mrodriguez = VÍCTIMA hasta que análisis forense indique lo contrario
```

---

## 7. Objetivo del Ataque — Cyber Espionaje

El atacante no buscó dinero inmediato. Buscó **inteligencia**. Con datos de pólizas y siniestros pendientes puede:

```
→ Vender información a competidores de la aseguradora
→ Identificar clientes con siniestros grandes para contactarlos
→ Coordinar fraudes de siniestros con terceros fuera del sistema
→ Chantajear a la aseguradora con la información obtenida
→ Vender base de datos de asegurados en mercados clandestinos
```

---

## 8. Técnica Utilizada — MITRE ATT&CK

| Técnica | ID MITRE | Descripción |
|---------|----------|-------------|
| Valid Accounts | T1078 | Credenciales legítimas de mrodriguez |
| Proxy — TOR | T1090.003 | Anonimización via red TOR |
| Data from Info Repositories | T1213 | Consulta sistemática de BD |
| Automated Collection | T1119 | Recopilación automatizada durante 2 semanas |

---

## 9. Impacto

- Exfiltración de datos de pólizas y siniestros durante 14 días
- Posibles usos fraudulentos de la información obtenida
- Activación de tres obligaciones legales simultáneas

---

## 10. Línea de Investigación Pendiente

- [ ] Análisis forense de PC de mrodriguez
- [ ] Determinar vector de compromiso de credenciales
- [ ] Inventariar exactamente qué pólizas y siniestros fueron consultados
- [ ] Investigar participación voluntaria o involuntaria de mrodriguez
- [ ] Revisar si otros empleados en baja tuvieron actividad similar

---

## 11. Acciones Tomadas

- [x] Bloqueo inmediato de cuenta mrodriguez
- [x] Reseteo de credenciales
- [x] Bloqueo de nodo TOR `185.220.101.45` en firewall
- [x] Preservación de logs para investigación forense y denuncia penal
- [x] Notificación a área legal de la empresa

---

## 12. Recomendaciones

1. Implementar MFA para todos los usuarios
2. Crear regla en SIEM: **empleado en baja médica o vacaciones con sesión activa = alerta inmediata**
3. Bloquear automáticamente accesos desde nodos TOR conocidos
4. Implementar política de suspensión automática de cuentas durante bajas médicas
5. Presentar denuncia formal ante División de Delitos Informáticos PNP
6. Reportar incidente a SBS en plazos establecidos por Ley 26702

---

## 13. Marco Legal Aplicable

| Ley | Descripción | Aplicación |
|-----|-------------|------------|
| **Ley 29733** | Protección de Datos Personales | Datos de asegurados exfiltrados |
| **Ley 30096** | Delitos Informáticos | Acceso no autorizado + uso de TOR agrava el delito |
| **Ley 26702** | Sistema Financiero y Seguros | Filtración de datos de pólizas debe reportarse a la SBS |

---

## Lección del Caso

> Este ataque fue invisible para las herramientas porque usó credenciales legítimas, horario normal y volumen razonable.
> Solo fue detectable cruzando registros de RRHH con logs de acceso.
> La correlación entre sistemas no técnicos y sistemas de seguridad es una capacidad crítica del SOC.
> Un analista que solo mira alertas nunca hubiese detectado este caso.
