jira status 付け直し
---

Resolved が Done じゃなかったので、設定してから status のつけ直しを行ったときのスクリプト

```python
from jira import JIRA
import time

issue_status = {'To Do': 11, 'Canceled': 51,'Resolved': 41, 'DOING': 21, 'Done': 51}

options = {'server': 'https://YOUR_WORKSPACE.atlassian.net'}
apikey = 'YOUR API KEY'

jira = JIRA(options, basic_auth=('name@example.com', apikey))

for i in jira.search_issues('project=YOUR PROJECT and status != "To Do" and status != "DOING" and status != "Done" and resolution is EMPTY',maxResults=100):
    current_status = issue_status[i.fields.status.name]
    status_id, status_name = i.fields.status.id, i.fields.status.name
    print("%s, status: %s, name: %s" % (i.key, current_status, i.fields.status.name))
    jira.transition_issue(i, 11)
    jira.transition_issue(i, current_status)
    time.sleep(0.5)

```
---
参考
- https://jira.readthedocs.io/en/master/examples.html#transitions
- https://jira.readthedocs.io/en/master/examples.html#searching
- https://stackoverflow.com/questions/19945179/is-it-possible-to-change-jira-issue-status-with-python-jira/19945470
- https://ja.confluence.atlassian.com/jirakb/using-not-equals-on-a-resolution-does-not-return-unresolved-issues-635897091.html

