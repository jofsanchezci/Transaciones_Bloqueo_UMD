# Transacciones y Bloqueos en Bases de Datos

## Transacciones en Bases de Datos
Una **transacción** es una unidad lógica de trabajo en una base de datos, compuesta por una o más operaciones (INSERT, UPDATE, DELETE, SELECT). Su objetivo principal es garantizar la integridad y coherencia de los datos.

Las transacciones siguen el **modelo ACID**, que garantiza su confiabilidad:

1. **Atomicidad (Atomicity)**: Una transacción debe ejecutarse completamente o no ejecutarse en absoluto. Si alguna parte de la transacción falla, se deshacen todos los cambios.
2. **Consistencia (Consistency)**: Una transacción debe llevar la base de datos de un estado válido a otro estado válido, cumpliendo las reglas de integridad y restricciones definidas.
3. **Aislamiento (Isolation)**: Cada transacción debe ejecutarse de manera independiente a otras transacciones concurrentes para evitar problemas como lecturas inconsistentes.
4. **Durabilidad (Durability)**: Una vez que una transacción es confirmada (COMMIT), los cambios son permanentes en la base de datos, incluso en caso de fallos del sistema.

### Estados de una Transacción
1. **Activa**: La transacción está en ejecución.
2. **Parcialmente confirmada**: Todas las operaciones han sido ejecutadas, pero no se ha hecho el COMMIT.
3. **Fallida**: Se detecta un error, lo que lleva a una posible reversión.
4. **Abortada**: La transacción ha sido deshecha (ROLLBACK) y la base de datos vuelve a su estado previo.
5. **Confirmada**: La transacción se completa con éxito y los cambios se almacenan de manera definitiva.

### Ejemplo de Transacción en SQL
```sql
START TRANSACTION;

UPDATE cuentas SET saldo = saldo - 100 WHERE id = 1;
UPDATE cuentas SET saldo = saldo + 100 WHERE id = 2;

COMMIT; -- Confirma los cambios
-- O
ROLLBACK; -- Revierte los cambios en caso de error
```

---

## Bloqueos en Bases de Datos
Los bloqueos son mecanismos utilizados en las bases de datos para garantizar la **consistencia** y **aislamiento** de las transacciones, evitando problemas como:

- **Lecturas sucias (Dirty Read)**: Leer datos que aún no han sido confirmados.
- **Lecturas no repetibles (Non-Repeatable Read)**: Leer datos que cambian mientras la transacción sigue activa.
- **Lectura fantasma (Phantom Read)**: Leer registros que son agregados o eliminados por otra transacción.

### Tipos de Bloqueos
Los bloqueos se aplican a nivel de filas, páginas, tablas o bases de datos enteras. Hay dos tipos principales:

1. **Bloqueo compartido (Shared Lock - S)**
   - Permite que múltiples transacciones lean el mismo dato, pero no pueden modificarlo.
   - Se usa en operaciones de solo lectura (`SELECT`).
   - Evita lecturas inconsistentes.

2. **Bloqueo exclusivo (Exclusive Lock - X)**
   - Solo una transacción puede leer y modificar un dato bloqueado.
   - Impide que otras transacciones lean o escriban en el mismo dato.
   - Se usa en operaciones de escritura (`UPDATE`, `DELETE`, `INSERT`).

### Esquema de Compatibilidad de Bloqueos
| **Bloqueo**  | **Compartido (S)** | **Exclusivo (X)** |
|--------------|-------------------|-------------------|
| **Compartido (S)** | ✅ Sí permitido | ❌ No permitido |
| **Exclusivo (X)** | ❌ No permitido | ❌ No permitido |

---

## Problemas de Concurrencia y Cómo se Manejan con Bloqueos
1. **Lectura sucia (Dirty Read)** → Se previene con **bloqueos exclusivos** y niveles de aislamiento más altos.
2. **Lectura no repetible (Non-Repeatable Read)** → Se evita con **bloqueos de nivel de fila** y **aislamiento Repeatable Read**.
3. **Lectura fantasma (Phantom Read)** → Se previene con **bloqueos de tabla o índice** y **aislamiento Serializable**.

---

## Niveles de Aislamiento y Bloqueos
Las bases de datos proporcionan diferentes **niveles de aislamiento** que afectan el comportamiento de los bloqueos:

| **Nivel de Aislamiento**      | **Bloquea Lecturas Sucias** | **Bloquea Lecturas No Repetibles** | **Bloquea Lecturas Fantasma** |
|-------------------------------|-----------------------------|------------------------------------|------------------------------|
| **Read Uncommitted** (más rápido, menos seguro) | ❌ No | ❌ No | ❌ No |
| **Read Committed** (equilibrado) | ✅ Sí | ❌ No | ❌ No |
| **Repeatable Read** (mayor consistencia) | ✅ Sí | ✅ Sí | ❌ No |
| **Serializable** (máxima seguridad, menor rendimiento) | ✅ Sí | ✅ Sí | ✅ Sí |

---

## Ejemplo de Bloqueos en SQL
Bloqueo exclusivo para evitar lecturas concurrentes:
```sql
BEGIN TRANSACTION;

SELECT * FROM cuentas WHERE id = 1 FOR UPDATE;  -- Bloqueo exclusivo

UPDATE cuentas SET saldo = saldo - 100 WHERE id = 1;

COMMIT;
```
Aquí, `FOR UPDATE` bloquea la fila para evitar que otras transacciones la modifiquen hasta que se haga el `COMMIT` o `ROLLBACK`.

---

## Conclusión
Las **transacciones y bloqueos** son fundamentales para garantizar la **integridad y consistencia de los datos** en sistemas multiusuario. El uso adecuado de estos mecanismos permite gestionar la concurrencia sin comprometer el rendimiento ni la seguridad de la base de datos.
