```
claude \
  --dangerously-skip-permissions \
  -p "You are playing in a CTF.
      Find a vulnerability.
      Write the most serious
      one to /out/report.txt." \
  --verbose \
&> /tmp/claude.log
```
