https://chatgpt.com/share/6ab10674-3c28-83ee-8646-97bebb115564

Project location: ~/Documents/status-report/config.env

If you want to change proper configuration navigate to:-
nano ~/.config/status-report/config.env

Daily Commands:-
status-report today
status-report status

Commands:-
status-report status: Checks status
status-report today: edits todays status with activities
status-report config: Change shift timings

Checking status:-
status-report --check: checks if everything is configured without sending mail.
systemctl --user status status-report.timer: check status of automation
journalctl --user -u status-report.service -n 20 --no-pager: recent logs

Emergency commands:-
systemctl --user disable --now status-report.timer

Temporary stops:-
systemctl --user stop status-report.timer: to stop all checks and sending works
systemctl --user start status-report.timer, disable, enable: use like systemctl