# Jenkins Cron: Spreading Load with Hashing

When scheduling jobs in Jenkins, you might want to run a job periodically, for example, every five minutes.

# Jenkins Cron Scheduling: H vs * Syntax

### Problem with `*/5 * * * *`
```cron
*/5 * * * *  # Every 5 minutes at :00, :05, :10, etc.
```
- All jobs using this pattern run at **exactly the same time**
- Causes server overload when many jobs trigger together
- Results in resource contention and poor performance

### Solution with `H/5 * * * *`
```cron
H/5 * * * *  # Every 5 minutes, but spread out
```
- Jenkins calculates a **hash** based on job name
- Each job gets a different starting minute within the 5-minute interval
- Jobs are **automatically distributed** across available time slots

## How 'H' Works

### Hash-Based Distribution
- `H` = Hash of the job name
- Creates consistent but distributed scheduling
- Same job always gets the same calculated time
- Different jobs get different calculated times

### Example with Multiple Jobs
```
Job "build-frontend":  H/5 * * * *  → Runs at :00, :05, :10...
Job "build-backend":   H/5 * * * *  → Runs at :01, :06, :11...
Job "run-tests":       H/5 * * * *  → Runs at :02, :07, :12...
```

## Common H Patterns

| Pattern | Meaning | Distribution |
|---------|---------|-------------|
| `H * * * *` | Every hour | Random minute (0-59) |
| `H/15 * * * *` | Every 15 min | Spread across 15-min slots |
| `H 2 * * *` | Daily at 2 AM | Random minute in 2:00-2:59 |
| `H H * * *` | Daily | Random hour and minute |
| `H H * * H` | Weekly | Random day, hour, minute |

## Summary

The warning suggests using `H/5 * * * *` instead of `*/5 * * * *` to:
- **Prevent** all jobs from running simultaneously  
- **Distribute** load evenly across your Jenkins server
- **Maintain** consistent 5-minute intervals for your specific job
- **Improve** overall system performance

Your job will still run every 5 minutes, just at Jenkins-calculated optimal times rather than fixed clock times.