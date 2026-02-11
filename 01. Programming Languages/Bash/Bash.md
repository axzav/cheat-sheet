# Bash

Variables:
```bash
VARIABLE="value" #define variable
echo $VARIABLE #using variable
HELLO=$(echo "hello") #run command and save the output to variable
echo "$VARIABLE" #print variable with format preserving (tabs, line breaks)
```

Conditions:
```bash
if [ ! -z "$VARIABLE" ]; then #if variable is not empty (-z)
   echo "not empty"
else
   echo "empty"
fi
```

Command line arguments:
```bash
#./script.sh -f option1 -o option2

while getopts ":f:o:" opt; do
 case $opt in
   f) FORMAT="$OPTARG";;
   o) OUTPUT="$OPTARG";;
   \?) echo "Invalid option -$OPTARG" >&2;;
 esac
done

echo $FORMAT #option1
echo $OUTPUT #option2
```


## Ссылки

- [Все частые юзкейсы на баше](https://github.com/dylanaraps/pure-bash-bible)
