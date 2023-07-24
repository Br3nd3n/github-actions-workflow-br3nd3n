# Aikido Security Github action

This repository contains an application that can be used in Github action workflows. It will trigger a scan in Aikido to make sure no new critical issues are introduced into your application. The free tier plan allows for scanning on dependencies. Other features such as blocking on SAST or license findings are part of the paid plan.

## Using the action

This is an example workflow you could use to trigger a scan for each new pull request

```yaml
name: Aikido Security
on:
  pull_request:
    branches:
      - '*'

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v3

      - name: Detect new vulnerabilities
        uses: AikidoSec/github-actions-workflow@v1.0.6
        with:
            secret-key: ${{ secrets.AIKIDO_SECRET_KEY }}
            fail-on-timeout: false
            fail-on-dependency-scan: true
            fail-on-sast-scan: false
            fail-on-iac-scan: false
            minimum-severity: 'CRITICAL'
            timeout-seconds: 120
```

The action has 3 possible outcomes: 
- `SUCCEEDED`: the scan was completed successfully and we did not encounter any new critical issues
- `FAILED`: the scan was completed successfully, but we found new critical issues
- `TIMED_OUT`: the scan did not complete before the set timeout. In this case we won't let the action fail, but we do return this special case to not block your pipeline.

Required fields:
- `secret-key`: The secret key generated at [CI integrations settings](https://app.aikido.dev/settings/integrations/continuous-integration).
- `minimum-severity`: Determines on which (minimum) severity Aikido should respond with `FAILED`. This value can be one of `LOW`, `MEDIUM`, `HIGH`, `CRITICAL`.

Optional fields:
- `fail-on-timeout`: Determines wether the workflow should respond with `FAILED` in case the scans timed out after 2 minutes.
- `fail-on-dependency-scan`: Determines wether Aikido should block on new dependency issues (CVEs).
- `fail-on-sast-scan`: Determines wether Aikido should block on new SAST issues. This is available in all [paid plans](https://www.aikido.dev/pricing).
- `fail-on-iac-scan`: Determines wether Aikido should block on new Infrastructure as Code issues. This is available in all [paid plans](https://www.aikido.dev/pricing).

## Contributing

Install the dependencies  
```bash
$ npm install
```

When the changes have been implemented, you need to build and package the code for release. Run the following commands and commit it to the repository.
```bash
$ npm run build && npm run package
```

## Change action.yml

The action.yml defines the inputs and output of our action.

See the [documentation](https://help.github.com/en/articles/metadata-syntax-for-github-actions)

## Creating a new release

To update the app, you will need to update the app's bundle. First run
```shell
npm run build
```
Followed by:
```shell
npm run package
```
The contents of the dist folder should now be altered, commit these changes and merge them into the main branch.

Next, create a release on Github by clicking on `tags` and then `releases`. Then you can draft and release a new version.
