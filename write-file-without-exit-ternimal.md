

#Use Cat

'#-----v---v------
```vi
cat <<'EOF' >> brightup.sh
#!/bin/bash
curr=`cat /sys/class/backlight/intel_backlight/actual_brightness`
if [ $curr -lt 4477 ]; then
   curr=$((curr+406));
   echo $curr  > /sys/class/backlight/intel_backlight/brightness;
fi
EOF
```
or 

```vi
cat <<< '
#!/bin/bash
curr=`cat /sys/class/backlight/intel_backlight/actual_brightness`
if [ $curr -lt 4477 ]; then
  curr=$((curr+406));
  echo $curr  > /sys/class/backlight/intel_backlight/brightness;
fi' > file # use overwrite mode so that you don't keep on appending the same script to that file over and over again, unless that's what you want.
```

Model
```vi
cat <<< ' > file
 ... code ...'

```
Source:
[Stack-over-flow](https://stackoverflow.com/questions/22697688/how-to-cat-eof-a-file-containing-code)
