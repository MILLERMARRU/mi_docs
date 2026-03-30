# Changelog — grControl (PRs #19 a #24)

Periodo: **marzo 2026** | Branch base: `develop`

---

## PR #19–#20 · Precio por galón en endpoints operacionales

Se agrega el campo `pricePerGallon` a las respuestas de los endpoints de mangueras y lecturas pendientes.

**Endpoints afectados:**
- `GET /api/operational/my-hoses` — nuevo campo `pricePerGallon`
- `GET /api/operational/hose-readings/pending` — nuevo campo `pricePerGallon`

**Commits:**
- `feat(operational): add price per gallon field to my-hoses endpoint`
- `feat(operational): add pricePerGallon to pending hose readings endpoint`

---

## PR #21–#22 · Dev Data Initializer

Seeder idempotente para datos de desarrollo con datos realistas de estaciones.

**Alcance:**
- Inicialización idempotente de datos base (estaciones, islas, tanques, productos, presentaciones, stock)
- Migración de IDs a UUID
- Asignación de turnos a operadores
- Restauración de `ddl-auto=update` para persistencia en dev

**Commits:**
- `chore(config): initialize dev data with idempotent seeder`
- `chore(config): update development seed data with real station locations and standardized domain`
- `chore(config): seed tanks, lubricentro products, presentations, and island stock`
- `fix(config): restore ddl-auto to update for persistent dev data`
- `chore(config): migrate dev data to UUID and add shift assignments`

---

## PR #23 · Fallback de precio en mangueras

Cuando una estación no tiene precio configurado para un producto, el sistema ahora usa el precio base del producto como fallback.

**Correcciones:**
- `fix(operational): fallback to product base price when no station price configured`
- `fix(operational): use hose initial values as fallback in MyHosesProvider query`

---

## PR #24 · Flexibilidad de turno activo

Se relaja la restricción de turno activo y se permite operar con `shiftId` opcional en los endpoints operacionales.

**Cambios:**
- La validación de turno activo ahora busca el turno más cercano del día en lugar de exigir coincidencia exacta de hora
- Se corrige el uso de `LOCALTIME` en lugar de `CURRENT_TIME` para comparaciones en PostgreSQL (evita zona horaria incorrecta)
- Los endpoints de lecturas, mediciones y cierre de turno aceptan `shiftId` opcional para operar sobre un turno específico

**Endpoints afectados:**
- `POST /api/operational/hose-readings/register`
- `POST /api/operational/hose-readings/validate`
- `POST /api/operational/hose-readings/validate-all`
- `POST /api/operational/measurements/create`
- `POST /api/operational/measurements/adjust`
- `POST /api/operational/shift-closures/close`

**Commits:**
- `fix(operational): relax active shift time constraint to nearest shift for the day`
- `fix(operational): use LOCALTIME instead of CURRENT_TIME for time comparison in PostgreSQL`
- `feat(operational): add optional shiftId to hose reading register, validate and validate-all endpoints`
- `feat(operational): add optional shiftId to measurement create and adjust endpoints`
- `feat(operational): add optional shiftId to shift closure endpoint`
