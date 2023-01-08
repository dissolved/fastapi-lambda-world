# fastapi-lambda-world

This is a proof of concept following resources at https://www.eliasbrange.dev/posts/deploy-fastapi-on-aws-part-1-lambda-api-gateway/

## Notes

The tutorial didn't work as written at the time of my reading. Namely, the `sam build` in step 5 failed with this error:

```bash
❯ sam build --debug
2023-01-08 12:11:42,305 | Config file location: /Users/ryan/Code/python/fastapi-lambda-world/samconfig.toml
2023-01-08 12:11:42,306 | Config file '/Users/ryan/Code/python/fastapi-lambda-world/samconfig.toml' does not exist
2023-01-08 12:11:42,306 | Using SAM Template at /Users/ryan/Code/python/fastapi-lambda-world/template.yaml
2023-01-08 12:11:42,392 | Using config file: samconfig.toml, config environment: default
2023-01-08 12:11:42,392 | Expand command line arguments to:
2023-01-08 12:11:42,392 | --template_file=/Users/ryan/Code/python/fastapi-lambda-world/template.yaml --build_dir=.aws-sam/build --cache_dir=.aws-sam/cache
2023-01-08 12:11:42,934 | 'build' command is called
2023-01-08 12:11:42,934 | No Parameters detected in the template
2023-01-08 12:11:42,934 | 0 stacks found in the template
2023-01-08 12:11:42,934 | No Parameters detected in the template
2023-01-08 12:11:42,934 | 0 resources found in the stack
2023-01-08 12:11:42,935 | Telemetry endpoint configured to be https://aws-serverless-tools-telemetry.us-west-2.amazonaws.com/metrics
2023-01-08 12:11:42,935 | Template is not provided in context, skip adding project type metric
2023-01-08 12:11:43,014 | Sending Telemetry: {'metrics': [{'commandRun': {'requestId': '56cdc5d7-5375-46a1-83eb-209db38486a5', 'installationId': '8b19ac49-1ecb-4b21-a332-fcd1ebb01593', 'sessionId': '38780b6e-c954-44c4-85d4-b0c02c9f7c09', 'executionEnvironment': 'CLI', 'ci': False, 'pyversion': '3.8.16', 'samcliVersion': '1.68.0', 'awsProfileProvided': False, 'debugFlagProvided': True, 'region': '', 'commandName': 'sam build', 'metricSpecificAttributes': {'gitOrigin': None, 'projectName': 'ca897e6304d60800dbc197920e9a2f54e48296a0964795f67cbe5f88379efb55', 'initialCommit': None}, 'duration': 543, 'exitReason': 'AttributeError', 'exitCode': 255}}]}
2023-01-08 12:11:43,375 | HTTPSConnectionPool(host='aws-serverless-tools-telemetry.us-west-2.amazonaws.com', port=443): Read timed out. (read timeout=0.1)
Traceback (most recent call last):
  File "/usr/local/bin/sam", line 8, in <module>
    sys.exit(cli())
  File "/usr/local/Cellar/aws-sam-cli/1.68.0/libexec/lib/python3.8/site-packages/click/core.py", line 1130, in __call__
    return self.main(*args, **kwargs)
  File "/usr/local/Cellar/aws-sam-cli/1.68.0/libexec/lib/python3.8/site-packages/click/core.py", line 1055, in main
    rv = self.invoke(ctx)
  File "/usr/local/Cellar/aws-sam-cli/1.68.0/libexec/lib/python3.8/site-packages/click/core.py", line 1657, in invoke
    return _process_result(sub_ctx.command.invoke(sub_ctx))
  File "/usr/local/Cellar/aws-sam-cli/1.68.0/libexec/lib/python3.8/site-packages/click/core.py", line 1404, in invoke
    return ctx.invoke(self.callback, **ctx.params)
  File "/usr/local/Cellar/aws-sam-cli/1.68.0/libexec/lib/python3.8/site-packages/click/core.py", line 760, in invoke
    return __callback(*args, **kwargs)
  File "/usr/local/Cellar/aws-sam-cli/1.68.0/libexec/lib/python3.8/site-packages/click/decorators.py", line 84, in new_func
    return ctx.invoke(f, obj, *args, **kwargs)
  File "/usr/local/Cellar/aws-sam-cli/1.68.0/libexec/lib/python3.8/site-packages/click/core.py", line 760, in invoke
    return __callback(*args, **kwargs)
  File "/usr/local/Cellar/aws-sam-cli/1.68.0/libexec/lib/python3.8/site-packages/samcli/lib/telemetry/metric.py", line 175, in wrapped
    raise exception  # pylint: disable=raising-bad-type
  File "/usr/local/Cellar/aws-sam-cli/1.68.0/libexec/lib/python3.8/site-packages/samcli/lib/telemetry/metric.py", line 150, in wrapped
    return_value = func(*args, **kwargs)
  File "/usr/local/Cellar/aws-sam-cli/1.68.0/libexec/lib/python3.8/site-packages/samcli/lib/utils/version_checker.py", line 41, in wrapped
    actual_result = func(*args, **kwargs)
  File "/usr/local/Cellar/aws-sam-cli/1.68.0/libexec/lib/python3.8/site-packages/samcli/cli/main.py", line 86, in wrapper
    return func(*args, **kwargs)
  File "/usr/local/Cellar/aws-sam-cli/1.68.0/libexec/lib/python3.8/site-packages/samcli/commands/build/command.py", line 186, in cli
    do_cli(
  File "/usr/local/Cellar/aws-sam-cli/1.68.0/libexec/lib/python3.8/site-packages/samcli/commands/build/command.py", line 276, in do_cli
    ctx.run()
  File "/usr/local/Cellar/aws-sam-cli/1.68.0/libexec/lib/python3.8/site-packages/samcli/commands/build/build_context.py", line 226, in run
    template_transform = template_dict.get("Transform", "")
AttributeError: 'NoneType' object has no attribute 'get'
```

I found [this](https://github.com/aws/aws-sam-cli/issues/2931) GitHub issue, and adding Properties to the Api resource as described (and reflected in the committed template.yaml) resolved my issue.

The next issue I was unsure of and stumble on, was [step 6](https://www.eliasbrange.dev/posts/deploy-fastapi-on-aws-part-1-lambda-api-gateway/#6-deploy-your-api-to-aws). I wasn't sure if `YOUR_S3_BUCKET` needed to exist prior to executing the deploy command (it did need to exist) or if it would be created, and I also wasn't sure if `CAPABILITY_IAM` was to be sent literally, or if that was a stand in for something else (just as `YOUR_S3_BUCKET` was). I found [another resource](https://www.linkedin.com/pulse/deploying-simple-lambda-function-using-sam-aws-gabe-olokun/) with a slightly different deploy command that worked beautifully for me. This was literally the command I used (no substitutions):

```bash
> sam deploy --stack-name fastapi-lambda-world --resolve-s3 --capabilities CAPABILITY_AUTO_EXPAND CAPABILITY_IAM
```

The `--resolve-s3` part just creates a unique bucket for the deploy. And it turns out those capabilities are literal not substitutions. I need to learn more about that IAM stuff.


## Other common steps when following this workflow for deploying using SAM.

- create `.gitignore` if it doesn't already exist, and add `.aws-sam` because we don't want to commit build artifacts

## Other Resources

- [Part 2: Deploy FastAPI on AWS Part 2: Fargate & ALB](https://www.eliasbrange.dev/posts/deploy-fastapi-on-aws-part-2-fargate-alb/)
