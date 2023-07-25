## Projects

### Peroject-1 Write a bash shell script to create a folder and two files inside that folder?
```
#!/bin/bash

# Create folder
mkdir chandan

# create two files in 'chandan' folder
cd chandan
touch file1.txt file2.txt
```
Save the script in a file, e.g., createfile.sh. Then, make the script executable using the following command:
```
chmod +x createfile.sh
```
Now, you can run the script to gather CPU and memory utilization information:
```
./createfile.sh
```

### Project-2 Write a bash shell script to get all the CPU and memory utilization into an output.txt file?
```
#!/bin/bash

# Get CPU utilization using 'top' command
top -b -n 1 | grep "Cpu(s)" > output.txt

# Get memory utilization using 'free' command
free -m >> output.txt

# Append additional system information
echo -e "\n\nAdditional System Information:" >> output.txt
uname -a >> output.txt
```
Save the script in a file, e.g., get_utilization.sh. Then, make the script executable using the following command:
```
chmod +x get_utilization.sh
```
Now, you can run the script to gather CPU and memory utilization information:
```
./get_utilization.sh
```

### Project-3 Linux script to find and replace a word in a file?
```
#!/bin/bash

# Check if the required arguments are provided
if [ $# -ne 3 ]; then
  echo "Usage: $0 <input_file> <old_word> <new_word>"
  exit 1
fi

# Store the command-line arguments in variables
input_file="$1"
old_word="$2"
new_word="$3"

# Use 'sed' to find and replace the old word with the new word in the input file
sed -i "s/${old_word}/${new_word}/g" "$input_file"

echo "Replacement complete. Word '${old_word}' replaced with '${new_word}' in ${input_file}."

```
Save the above script in a file, e.g., find_replace.sh. Make the script executable using the following command:
```
chmod +x find_replace.sh
```
Now, you can use the script to find and replace a word in a file. The script takes three arguments: the name of the input file, the word you want to replace, and the new word you want to use for replacement.

For example, if you want to replace all occurrences of the word "apple" with "orange" in a file called fruits.txt, you would run the script as follows:
```
./find_replace.sh fruits.txt apple orange
```
The script will perform the replacement and provide you with a message indicating that the operation is complete.

I**mportant note:** The -i option used with sed is for in-place editing, which means the changes will be made directly in the input file. Make sure to have a backup of your file before running the script in case you need to revert the changes. If you want to perform a dry-run without modifying the file, remove the -i option from the sed command in the script.
