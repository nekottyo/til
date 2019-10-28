# local での assume_role


`~/.aws/config` で profile を指定して別 role になる


```ini
[profile <your new profile>]
role_arn = arn:aws:iam::<your role>
source_profile = <exists profile>
```
