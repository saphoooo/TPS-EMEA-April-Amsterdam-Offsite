# 3 Ways to Configure the Datadog Agent with Community Integration

1. create image with the extras installed
1. Use a lifecycle poststart command and have it a run the command to install it before the agent starts
1. If the check is simple, you can copy over the code inside of <check>.py, inside of these lines: https://github.com/DataDog/helm-charts/blob/main/charts/datadog/values.yaml#L335-L336 then a config here: https://github.com/DataDog/helm-charts/blob/main/charts/datadog/values.yaml#L320-L324
