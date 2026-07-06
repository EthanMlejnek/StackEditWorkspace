
### Original

```csharp
// Issues a SQL query with:
// - .Include(m => m.MeasurementOptical) → JOIN to MeasurementOpticals
// - .GroupBy(...) → server-side GROUP BY
// - .Select(g => g.OrderByDescending(m => m.CreatedOn).FirstOrDefault())
// → ROW_NUMBER() / TOP 1 per group in SQL
// - .ToDictionaryAsync(m => (...9-tuple...), m => m!)
// → stores full Measurement entity as value

var  baselineByKey  =  await  context.Measurements
 .Include(m  =>  m.MeasurementOptical)
.AsNoTracking()
.Where(...)
.GroupBy(m  =>  new { m.SampleId, m.FiberId, ...WavelengthStart, ...LCh, ...RCh, TypeId, InitialTestStepId })
.Select(g  =>  g.OrderByDescending(m  =>  m.CreatedOn).FirstOrDefault())
.ToDictionaryAsync(
m  => (m.SampleId, m.FiberId, ...),
m  =>  m!  // ← full Measurement entity
);
```
<!--stackedit_data:
eyJoaXN0b3J5IjpbMTUyNTYwMjA3NV19
-->