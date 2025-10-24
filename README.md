name: Dispatch remote Selenium workflow

on:
  push:
    branches: [ main ]

jobs:
  trigger-remote-workflow:
    name: Trigger workflow in other repo
    runs-on: ubuntu-latest
    steps:
      - name: Trigger remote workflow via workflow-dispatch
        uses: peter-evans/workflow-dispatch@v1
        with:
          token: ${{ secrets.REPO_TRIGGER_PAT }}
          repository: sharmarishiksh01/WebAppAutomationSG
          workflow: .github/workflows/run-selenium-tests.yml
          ref: main

      - name: Confirm dispatch
        run: echo "Dispatch request sent to sharmarishksh01/WebAppAutomationSG/.github/workflows/run-selenium-tests.yml on ref 'main'"

      - name: use MY_PAT to query target repo
        env:
          MY_PAT: ${{ secrets.MY_PAT }}
        run: |
          # Safe demo: do not print the token. If MY_PAT is present, query the target repo
          if [ -z "$MY_PAT" ]; then
            echo "MY_PAT not set; skipping demo query"
          else
            echo "Running demo API query against target repo (response will be masked)"
            curl -s -H "Authorization: token $MY_PAT" https://api.github.com/repos/sharmarishksh01/WebAppAutomationSG | python3 -c "import sys,json; print(json.load(sys.stdin).get('full_name'))"
          fi
