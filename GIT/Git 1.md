Git

## Zones
* git has 4 zones, the main local file system where the user codes, the staging area where when you add files it updates the staging area and where you can select files and accept or remove what is important and finally the local repo where local commit are saved. In the 4th stage we can push it to GitHub where it is stored in the cloud and your team can access it.
* git stash stores the current work on the sideline if you want to work on something else. When you want to work on it back use 'git stash apply' or 'git stash pop'.
* Types of development:
1. Feature Branching: Creating a dedicated branch for each bug fix or feature addition. Adv: Helps the main branch to stay stable by keeping experimental stuff on the sidelines. Disadv: Usually leads to lots of merge conflicts as main might move on quite a bit.

2. GitFlow: Specific branches for each phase like Feature, Dev, Main, Hotfix and Version release branch. Disadv: Not recommended for constant development and small teams. it feels like overkill

3. Github Flow: One main branch and a different feature branch (short lived). May also include small Fixbug branches. After code review the feature merges into main and it is the main release branch.

4. GitLab Flow: Different phase/environment branches like Feature, Main/Dev, Staging, Production. Before prod you merge feature>dev>staging>prod. Adv: good for CI/CD. Disadv: If not disciplined in tagging it can get complex.

5. Trunk Based Development: Devs commit everyday or multiple times a day solving small merge issues constantly to avoid huge issues. Used by google, meta and amazon.

## Merge vs Rebase
* Merge: When you want to save the history and it will be useful to get back to and you think that your history will not get too tangled.
* Rebase: When you think the feature branch history is no longer needed and want a much cleaner but less detailed history.
