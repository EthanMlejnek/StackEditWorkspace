
### Original

```csharp
// 5) Fetch baselines from DB: for each key at the initial test step, pick the LAST by CreatedOn.
//    Status aligned with FirstMeas: allow 1 or 2.
var baselineByKey = await context.Measurements
    .Include(m => m.MeasurementOptical)
    .AsNoTracking()
    .Where(m => m.MeasurementOptical != null
                && (m.Status == 1 || m.Status == 2)
                && m.TestId == testId
                && m.TestStepId.HasValue
                && initialStepIdSet.Contains(m.TestStepId.Value)
                && baseTypeIds.Contains(m.MeasurementTypeId))
    .GroupBy(m => new
    {
        m.SampleId,
        m.FiberId,
        m.MeasurementOptical!.WavelengthStart,
        m.MeasurementOptical!.WavelengthStop,
        m.MeasurementOptical!.RlLen,
        m.MeasurementOptical!.LCh,
        m.MeasurementOptical!.RCh,
        TypeId = m.MeasurementTypeId,
        InitialTestStepId = m.TestStepId!.Value
    })
    .Select(g => g.OrderByDescending(m => m.CreatedOn).FirstOrDefault())
    .ToDictionaryAsync(
        m => (
            m!.SampleId,
            m.FiberId,
            (float?)m.MeasurementOptical!.WavelengthStart,
            (float?)m.MeasurementOptical!.WavelengthStop,
            (float?)m.MeasurementOptical!.RlLen,
            (int?)m.MeasurementOptical!.LCh,
            (int?)m.MeasurementOptical!.RCh,
            m.MeasurementTypeId,
            m.TestStepId!.Value // InitialTestStepId
        ),
        m => m!
    );

if (baselineByKey.Count == 0) return;
```
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTI4MTQ2NjY1LDIxNjk3MjEyMV19
-->