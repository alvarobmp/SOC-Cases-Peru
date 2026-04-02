# Caso 03 — Low and Slow Attack con Credenciales Comprometidas

![Severidad](https://img.shields.io/badge/Severidad-CRÍTICA-red)
![Estado](https://img.shields.io/badge/Estado-Cerrado-green)
![Marco Legal](https://img.shields.io/badge/Ley-29733%20%7C%2030096-blue)

## Metadata

| Campo | Detalle |
|-------|---------|
| **ID** | INC-2026-0307-003 |
| **Fecha** | 07 de marzo de 2026 |
| **Analista** | Álvaro Martínez Pachas |
| **Severidad** | CRÍTICA |
| **Estado** | Cerrado — Investigación forense pendiente |
| **MITRE ATT&CK** | T1078 - Valid Accounts / T1053 - Scheduled Task / T1029 - Scheduled Transfer / T1048 - Exfiltration |

---

## 1. Resumen Ejecutivo

El 07 de marzo de 2026 entre las 17:41 y 17:53 hrs se detectó actividad anómala en el servidor de base de datos. La cuenta `dbadmin` ejecutó consultas automatizadas repetitivas sobre la tabla de clientes, pese a que el titular registró salida física del edificio a las 17:38 hrs. Se determinó que las credenciales fueron comprometidas y utilizadas para ejecutar una tarea programada no autorizada con el objetivo de exfiltrar datos mediante técnica **Low and Slow**.

---

## 2. ¿Qué es un Low and Slow Attack?

> Técnica de exfiltración que extrae pequeñas cantidades de datos de forma repetida y espaciada en el tiempo para evadir alertas de volumen. Diseñada para pasar desapercibida en sistemas que solo alertan por descarga masiva.

```
Detección difícil porque:
- Cada consulta individual parece legítima
- El volumen por evento es mínimo
- El patrón solo es visible analizando el conjunto
```

---

## 3. Cronología del Ataque

```
17:38:00 — dbadmin registra salida física del edificio
           (registro de control de acceso)

17:41:23 — Primera consulta automatizada ejecutada
           con credenciales de dbadmin (3 minutos después de salir)

17:41 
  a      — 7 consultas SELECT * FROM clientes LIMIT 10
17:53      ejecutadas cada ~2 minutos

17:53:21 — Última consulta registrada
```

---

## 4. Evidencia — Logs

### Logs del servidor de base de datos
```
17:41:23 — dbadmin | SELECT * FROM clientes LIMIT 10 | 10 registros
17:43:11 — dbadmin | SELECT * FROM clientes LIMIT 10 | 10 registros
17:45:07 — dbadmin | SELECT * FROM clientes LIMIT 10 | 10 registros
17:47:02 — dbadmin | SELECT * FROM clientes LIMIT 10 | 10 registros
17:49:14 — dbadmin | SELECT * FROM clientes LIMIT 10 | 10 registros
17:51:33 — dbadmin | SELECT * FROM clientes LIMIT 10 | 10 registros
17:53:21 — dbadmin | SELECT * FROM clientes LIMIT 10 | 10 registros
```

### Registro de acceso físico
```
17:38:00 — dbadmin marcó salida del edificio
```

### Análisis del patrón
```
Total consultas:     7
Registros por query: 10
Total extraído:      70 registros (potencialmente no únicos)
Intervalo:           ~2 minutos entre cada consulta
Horario:             Fin de jornada laboral — baja vigilancia
Anomalía crítica:    Usuario fuera del edificio con sesión activa
```

---

## 5. Sistemas y Datos Afectados

| Sistema | Datos | Criticidad |
|---------|-------|-----------|
| Servidor de base de datos | Tabla de clientes — 70 registros potencialmente exfiltrados | Crítica |
| Credenciales dbadmin | Cuenta de administrador comprometida | Crítica |

---

## 6. Causa Raíz

Credenciales de `dbadmin` comprometidas. Uso indebido de cuenta personal para tarea programada no autorizada. Las tareas programadas legítimas deben usar **cuentas de servicio dedicadas**, nunca cuentas personales de administrador.

---

## 7. Técnica Utilizada — MITRE ATT&CK

| Técnica | ID MITRE | Descripción |
|---------|----------|-------------|
| Valid Accounts | T1078 | Uso de credenciales legítimas de dbadmin |
| Scheduled Task | T1053 | Tarea programada no autorizada |
| Scheduled Transfer | T1029 | Exfiltración en intervalos regulares |
| Low and Slow | T1048 | Volumen mínimo por evento para evadir detección |

---

## 8. Impacto

- Exfiltración probable de datos personales de clientes
- Activación de **Ley 29733** de Protección de Datos Personales
- No se descarta que otras cuentas hayan sido comprometidas

---

## 9. Línea de Investigación Pendiente

- [ ] Auditoría completa del servidor de base de datos
- [ ] Identificar quién creó la tarea programada y cuándo
- [ ] Verificar si otras cuentas presentan actividad anómala
- [ ] Determinar vector de compromiso de credenciales de dbadmin
- [ ] Extender auditoría al resto de la infraestructura

---

## 10. Acciones Tomadas

- [x] Suspensión inmediata de cuenta dbadmin
- [x] Reseteo forzado de contraseña
- [x] Desactivación de tarea programada no autorizada
- [x] Inicio de auditoría en servidor de base de datos
- [x] Preservación de logs para investigación forense

---

## 11. Recomendaciones

1. Implementar MFA para todos los usuarios con acceso a base de datos
2. Establecer política de uso de **cuentas de servicio dedicadas** para tareas programadas — nunca cuentas personales
3. Crear regla en SIEM para detectar patrón Low and Slow:
   ```
   ALERTA SI: mismo usuario + misma query + intervalos regulares + volumen bajo
   ```
4. Revisar y auditar todas las tareas programadas existentes
5. Evaluar notificación bajo Ley 29733

---

## 12. Marco Legal Aplicable

| Ley | Descripción | Aplicación |
|-----|-------------|------------|
| **Ley 29733** | Protección de Datos Personales | Datos de clientes potencialmente exfiltrados |
| **Ley 30096** | Delitos Informáticos | Acceso no autorizado y uso indebido de credenciales |

---

## Lección del Caso

> Este ataque no generó ninguna alerta de volumen porque cada evento individual era mínimo.
> Solo fue detectable cruzando el registro físico de salida con los logs del sistema.
> La correlación entre fuentes no técnicas y logs es una habilidad que ninguna herramienta reemplaza.
