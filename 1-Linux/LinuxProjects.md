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
