# LogQL Examples

```shell
# Detect general issues
{job="containerlogs"} |~ "(?i)(error|fail|lost|closed|panic|fatal|crash|password|authentication|denied)"
## V2 (remove false positives)
{job="containerlogs", container_name!~"^promtail.+"} |~ "(?i)(error|fail|lost|closed|panic|fatal|crash|password|authentication|denied)" != "ForgotPassword"
## v3
{job="containerlogs", container_name=~"myapp-\\w+-prod"} |~ "(?i)(error|fail|lost|closed|panic|fatal|crash|password|authentication|denied)" != "ForgotPassword"  | json | __error__ = "" | level="error"

# Rate limiting issues
{job="containerlogs"} |~ "(too many requests|rate.limit)"

# Check for authentication errors:
{job="containerlogs"} |~ "(unauthenticated|access.denied)"
## V2
{job="containerlogs"} |~ "(?i)((not |un)authenticated|(not |un)authorized|access.denied|invalid)"

# Data/parsing errors
{job="containerlogs"} |~ "(?i)(deserialize|unmarshal|bad request|missing required|invalid value)"

# Check for errors with 3rd party services and exclude startup configuration block
{job="containerlogs"} |~ "(?i)(service unavailable|maintenance|capacity|try again later|time(d |-)?out)" | json | __error__ = "" | msg!="configuration"

# Other issues:
{job="containerlogs"} |~ "(?i)(internal server error)"
```