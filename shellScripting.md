# Complete Shell Scripting Guide - Basics to Advanced + Interview Questions

## Table of Contents
1. [Shell Scripting Fundamentals](#shell-scripting-fundamentals)
2. [Basic Concepts](#basic-concepts)
3. [Variables and Data Types](#variables-and-data-types)
4. [Input/Output Operations](#inputoutput-operations)
5. [Operators](#operators)
6. [Control Structures](#control-structures)
7. [Functions](#functions)
8. [Arrays](#arrays)
9. [String Manipulation](#string-manipulation)
10. [File Operations](#file-operations)
11. [Advanced Concepts](#advanced-concepts)
12. [Debugging and Error Handling](#debugging-and-error-handling)
13. [Best Practices](#best-practices)
14. [Real-World Examples](#real-world-examples)
15. [Top 10 Interview Questions with Answers](#top-10-interview-questions-with-answers)

---

## Shell Scripting Fundamentals

### What is Shell Scripting?
Shell scripting is writing a series of commands for the shell to execute. It's a powerful way to automate tasks, manage systems, and process data.

### Types of Shells
- **Bash (Bourne Again Shell)** - Most common, default on Linux/macOS
- **Zsh (Z Shell)** - Enhanced version of Bash
- **Fish** - User-friendly shell
- **Ksh (Korn Shell)** - Unix shell
- **Csh/Tcsh** - C-style shell

### Shebang Line
Every shell script should start with a shebang (#!) to specify the interpreter:

```bash
#!/bin/bash          # Use Bash
#!/bin/sh            # Use system default shell
#!/usr/bin/env bash  # Use Bash from PATH (portable)
```

### Creating Your First Script

```bash
#!/bin/bash
# This is a comment
echo "Hello, World!"
```

**Save as:** `hello.sh`
**Make executable:** `chmod +x hello.sh`
**Run:** `./hello.sh`

---

## Basic Concepts

### Comments
```bash
# Single line comment

: '
Multi-line comment
This is a block comment
'
```

### Script Structure
```bash
#!/bin/bash

# Script metadata
# Author: Your Name
# Date: 2025-01-26
# Purpose: Demonstrate script structure

# Variable declarations
name="John"
age=25

# Main logic
echo "Name: $name"
echo "Age: $age"

# Exit with status code
exit 0
```

### Execution Methods
```bash
# Method 1: Make executable and run
chmod +x script.sh
./script.sh

# Method 2: Run with bash
bash script.sh

# Method 3: Source the script (runs in current shell)
source script.sh
# or
. script.sh
```

---

## Variables and Data Types

### Variable Declaration and Usage
```bash
#!/bin/bash

# Variable assignment (no spaces around =)
name="John Doe"
age=30
salary=50000.50

# Using variables
echo "Name: $name"
echo "Age: ${age}"          # Preferred syntax
echo "Salary: \$${salary}" # Escape $ to print literal $

# Environment variables
echo "Home directory: $HOME"
echo "Current user: $USER"
echo "Shell: $SHELL"
```

### Variable Scope
```bash
#!/bin/bash

# Global variable
global_var="I'm global"

function demo_scope() {
    # Local variable (only accessible within function)
    local local_var="I'm local"
    
    echo "Inside function:"
    echo "Global: $global_var"
    echo "Local: $local_var"
}

demo_scope
echo "Outside function:"
echo "Global: $global_var"
echo "Local: $local_var"  # This will be empty
```

### Special Variables
```bash
#!/bin/bash

echo "Script name: $0"
echo "First argument: $1"
echo "Second argument: $2"
echo "All arguments: $@"
echo "Number of arguments: $#"
echo "Process ID: $$"
echo "Exit status of last command: $?"
```

### Variable Operations
```bash
#!/bin/bash

# String operations
string="Hello World"
echo "Length: ${#string}"
echo "Substring: ${string:6:5}"      # "World"
echo "Replace: ${string/World/Bash}" # "Hello Bash"

# Default values
echo "Value: ${undefined_var:-"default"}"  # Use default if undefined
echo "Value: ${undefined_var:="assigned"}" # Assign and use default

# Readonly variables
readonly PI=3.14159
# PI=3.14  # This would cause an error

# Unsetting variables
unset string
```

---

## Input/Output Operations

### Reading User Input
```bash
#!/bin/bash

# Basic input
echo -n "Enter your name: "
read name
echo "Hello, $name!"

# Multiple inputs
echo "Enter your name and age:"
read name age
echo "Name: $name, Age: $age"

# Silent input (for passwords)
echo -n "Enter password: "
read -s password
echo
echo "Password entered (hidden)"

# Input with prompt
read -p "Enter your city: " city
echo "You live in $city"

# Reading from file
while read line; do
    echo "Line: $line"
done < input.txt
```

### Output Operations
```bash
#!/bin/bash

# Basic output
echo "Simple text"
echo -e "Text with\nnewline"    # Enable escape sequences
echo -n "No newline"            # No trailing newline

# Printf (more control)
printf "Name: %s, Age: %d, Salary: %.2f\n" "John" 30 50000.50

# Output redirection
echo "This goes to file" > output.txt          # Overwrite
echo "This appends to file" >> output.txt      # Append
echo "Error message" >&2                       # To stderr

# Here document
cat << EOF
This is a here document.
Variables like $USER are expanded.
Multiple lines are supported.
EOF

# Here string
cat <<< "Single line here string"
```

---

## Operators

### Arithmetic Operators
```bash
#!/bin/bash

a=10
b=3

# Basic arithmetic
echo "Addition: $((a + b))"           # 13
echo "Subtraction: $((a - b))"        # 7
echo "Multiplication: $((a * b))"     # 30
echo "Division: $((a / b))"           # 3
echo "Modulo: $((a % b))"             # 1
echo "Power: $((a ** 2))"             # 100

# Using expr (older method)
echo "Addition with expr: $(expr $a + $b)"

# Floating point arithmetic (using bc)
echo "Float division: $(echo "scale=2; $a / $b" | bc)"

# Increment/Decrement
((a++))    # Post-increment
echo "After increment: $a"    # 11

((++b))    # Pre-increment
echo "After pre-increment: $b"    # 4
```

### Comparison Operators
```bash
#!/bin/bash

a=10
b=20

# Numeric comparisons
if [ $a -eq $b ]; then echo "Equal"; fi
if [ $a -ne $b ]; then echo "Not equal"; fi
if [ $a -lt $b ]; then echo "$a is less than $b"; fi
if [ $a -le $b ]; then echo "$a is less than or equal to $b"; fi
if [ $a -gt $b ]; then echo "$a is greater than $b"; fi
if [ $a -ge $b ]; then echo "$a is greater than or equal to $b"; fi

# String comparisons
str1="hello"
str2="world"

if [ "$str1" = "$str2" ]; then echo "Strings are equal"; fi
if [ "$str1" != "$str2" ]; then echo "Strings are not equal"; fi
if [ "$str1" \< "$str2" ]; then echo "$str1 comes before $str2"; fi
if [ -z "$str1" ]; then echo "String is empty"; fi
if [ -n "$str1" ]; then echo "String is not empty"; fi
```

### Logical Operators
```bash
#!/bin/bash

a=10
b=20
c=30

# AND operator
if [ $a -lt $b ] && [ $b -lt $c ]; then
    echo "a < b < c"
fi

# OR operator
if [ $a -eq 10 ] || [ $a -eq 20 ]; then
    echo "a is either 10 or 20"
fi

# NOT operator
if ! [ $a -eq $b ]; then
    echo "a is not equal to b"
fi
```

---

## Control Structures

### If-Else Statements
```bash
#!/bin/bash

read -p "Enter your age: " age

if [ $age -lt 18 ]; then
    echo "You are a minor"
elif [ $age -lt 65 ]; then
    echo "You are an adult"
else
    echo "You are a senior citizen"
fi

# One-liner if
[ $age -ge 18 ] && echo "You can vote"

# File testing
file="test.txt"
if [ -f "$file" ]; then
    echo "File exists"
elif [ -d "$file" ]; then
    echo "It's a directory"
else
    echo "File doesn't exist"
fi
```

### Case Statements
```bash
#!/bin/bash

read -p "Enter a fruit: " fruit

case $fruit in
    "apple")
        echo "Red or green fruit"
        ;;
    "banana")
        echo "Yellow fruit"
        ;;
    "orange"|"tangerine")
        echo "Orange colored fruit"
        ;;
    *)
        echo "Unknown fruit"
        ;;
esac

# Case with patterns
read -p "Enter a file name: " filename
case $filename in
    *.txt)
        echo "Text file"
        ;;
    *.jpg|*.png|*.gif)
        echo "Image file"
        ;;
    *.*)
        echo "File with extension"
        ;;
    *)
        echo "File without extension"
        ;;
esac
```

### Loops

#### For Loops
```bash
#!/bin/bash

# Traditional for loop
echo "Numbers 1 to 5:"
for i in 1 2 3 4 5; do
    echo "Number: $i"
done

# Range-based for loop
echo "Range 1 to 10:"
for i in {1..10}; do
    echo -n "$i "
done
echo

# C-style for loop
echo "C-style loop:"
for ((i=1; i<=5; i++)); do
    echo "Iteration: $i"
done

# Loop over files
echo "Files in current directory:"
for file in *.txt; do
    if [ -f "$file" ]; then
        echo "Found: $file"
    fi
done

# Loop over array
fruits=("apple" "banana" "orange")
for fruit in "${fruits[@]}"; do
    echo "Fruit: $fruit"
done
```

#### While Loops
```bash
#!/bin/bash

# Basic while loop
counter=1
while [ $counter -le 5 ]; do
    echo "Count: $counter"
    ((counter++))
done

# Reading file line by line
while read line; do
    echo "Line: $line"
done < /etc/passwd

# Infinite loop (be careful!)
# while true; do
#     echo "Press Ctrl+C to stop"
#     sleep 1
# done
```

#### Until Loops
```bash
#!/bin/bash

# Until loop (opposite of while)
counter=1
until [ $counter -gt 5 ]; do
    echo "Count: $counter"
    ((counter++))
done
```

### Loop Control
```bash
#!/bin/bash

# Break and continue
for i in {1..10}; do
    if [ $i -eq 3 ]; then
        continue  # Skip iteration
    fi
    if [ $i -eq 8 ]; then
        break     # Exit loop
    fi
    echo "Number: $i"
done
```

---

## Functions

### Basic Functions
```bash
#!/bin/bash

# Function definition
greet() {
    echo "Hello, World!"
}

# Function call
greet

# Function with parameters
greet_user() {
    local name=$1
    local age=$2
    echo "Hello, $name! You are $age years old."
}

greet_user "John" 30

# Function with return value
add_numbers() {
    local num1=$1
    local num2=$2
    local result=$((num1 + num2))
    echo $result  # Return value via echo
}

result=$(add_numbers 5 3)
echo "Sum: $result"
```

### Advanced Functions
```bash
#!/bin/bash

# Function with variable arguments
print_args() {
    echo "Number of arguments: $#"
    echo "All arguments: $@"
    
    local counter=1
    for arg in "$@"; do
        echo "Argument $counter: $arg"
        ((counter++))
    done
}

print_args "one" "two" "three"

# Recursive function
factorial() {
    local n=$1
    if [ $n -le 1 ]; then
        echo 1
    else
        local prev=$(factorial $((n-1)))
        echo $((n * prev))
    fi
}

echo "Factorial of 5: $(factorial 5)"

# Function with return status
check_file() {
    local filename=$1
    if [ -f "$filename" ]; then
        return 0  # Success
    else
        return 1  # Failure
    fi
}

if check_file "test.txt"; then
    echo "File exists"
else
    echo "File doesn't exist"
fi
```

---

## Arrays

### Indexed Arrays
```bash
#!/bin/bash

# Array declaration
fruits=("apple" "banana" "orange")

# Alternative declaration
declare -a colors
colors[0]="red"
colors[1]="green"
colors[2]="blue"

# Another way
numbers=(1 2 3 4 5)

# Accessing elements
echo "First fruit: ${fruits[0]}"
echo "Second fruit: ${fruits[1]}"

# All elements
echo "All fruits: ${fruits[@]}"
echo "All fruits: ${fruits[*]}"

# Array length
echo "Number of fruits: ${#fruits[@]}"

# Array indices
echo "Indices: ${!fruits[@]}"

# Adding elements
fruits+=("grape")
fruits[10]="mango"  # Creates sparse array

# Looping through array
echo "Fruits list:"
for fruit in "${fruits[@]}"; do
    echo "- $fruit"
done

# Looping with indices
for i in "${!fruits[@]}"; do
    echo "Index $i: ${fruits[i]}"
done
```

### Associative Arrays
```bash
#!/bin/bash

# Declare associative array
declare -A person

# Assign values
person["name"]="John Doe"
person["age"]=30
person["city"]="New York"

# Access values
echo "Name: ${person["name"]}"
echo "Age: ${person["age"]}"

# All keys and values
echo "Keys: ${!person[@]}"
echo "Values: ${person[@]}"

# Loop through associative array
for key in "${!person[@]}"; do
    echo "$key: ${person[$key]}"
done
```

### Array Operations
```bash
#!/bin/bash

# Array slicing
numbers=(1 2 3 4 5 6 7 8 9 10)
echo "Original: ${numbers[@]}"
echo "Slice [2:5]: ${numbers[@]:2:3}"  # Elements 2,3,4

# Copying arrays
copy=("${numbers[@]}")
echo "Copy: ${copy[@]}"

# Removing elements
unset numbers[3]  # Remove element at index 3
echo "After removing index 3: ${numbers[@]}"

# Replace elements
numbers=("${numbers[@]/5/fifty}")  # Replace 5 with fifty
echo "After replacement: ${numbers[@]}"
```

---

## String Manipulation

### Basic String Operations
```bash
#!/bin/bash

string="Hello, World!"

# String length
echo "Length: ${#string}"

# Substring extraction
echo "Substring: ${string:7:5}"      # "World"
echo "From position: ${string:7}"    # "World!"

# String replacement
echo "Replace first: ${string/l/L}"     # "HeLlo, World!"
echo "Replace all: ${string//l/L}"      # "HeLLo, WorLd!"
echo "Replace prefix: ${string/#Hello/Hi}"  # "Hi, World!"
echo "Replace suffix: ${string/%World!/Universe}"  # "Hello, Universe"

# Case conversion
echo "Uppercase: ${string^^}"
echo "Lowercase: ${string,,}"
echo "First char upper: ${string^}"
```

### Pattern Matching
```bash
#!/bin/bash

filename="document.pdf"

# Check if string contains pattern
if [[ $filename == *.pdf ]]; then
    echo "PDF file detected"
fi

# Remove shortest match from beginning
echo "Remove extension: ${filename%.*}"  # "document"

# Remove longest match from beginning
path="/home/user/documents/file.txt"
echo "Filename: ${path##*/}"  # "file.txt"
echo "Directory: ${path%/*}"  # "/home/user/documents"

# Remove shortest match from end
echo "Remove path: ${path#*/}"  # "home/user/documents/file.txt"
```

### String Validation
```bash
#!/bin/bash

validate_email() {
    local email=$1
    local pattern="^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$"
    
    if [[ $email =~ $pattern ]]; then
        echo "Valid email"
        return 0
    else
        echo "Invalid email"
        return 1
    fi
}

validate_email "user@example.com"
validate_email "invalid-email"

# Check if string is numeric
is_numeric() {
    local input=$1
    if [[ $input =~ ^[0-9]+$ ]]; then
        echo "Numeric"
    else
        echo "Not numeric"
    fi
}

is_numeric "123"
is_numeric "abc"
```

---

## File Operations

### File Testing
```bash
#!/bin/bash

file="test.txt"

# Common file tests
echo "File tests for: $file"
[ -e "$file" ] && echo "Exists" || echo "Doesn't exist"
[ -f "$file" ] && echo "Regular file" || echo "Not a regular file"
[ -d "$file" ] && echo "Directory" || echo "Not a directory"
[ -r "$file" ] && echo "Readable" || echo "Not readable"
[ -w "$file" ] && echo "Writable" || echo "Not writable"
[ -x "$file" ] && echo "Executable" || echo "Not executable"
[ -s "$file" ] && echo "Not empty" || echo "Empty or doesn't exist"

# File comparison
file1="file1.txt"
file2="file2.txt"
[ "$file1" -nt "$file2" ] && echo "$file1 is newer than $file2"
[ "$file1" -ot "$file2" ] && echo "$file1 is older than $file2"
```

### File Operations
```bash
#!/bin/bash

# Create files and directories
touch newfile.txt
mkdir -p new/directory/structure

# Copy files
cp source.txt destination.txt
cp -r source_dir/ destination_dir/

# Move/rename files
mv oldname.txt newname.txt

# Find files
find . -name "*.txt" -type f  # Find all .txt files
find . -mtime -1              # Files modified in last day
find . -size +1M              # Files larger than 1MB

# Process files
while IFS= read -r line; do
    echo "Processing: $line"
done < input.txt

# Count lines, words, characters
wc -l file.txt    # Lines
wc -w file.txt    # Words
wc -c file.txt    # Characters
```

### File Content Processing
```bash
#!/bin/bash

# Read file content
content=$(cat file.txt)
echo "$content"

# Process CSV files
process_csv() {
    local file=$1
    local line_num=0
    
    while IFS=',' read -r col1 col2 col3; do
        ((line_num++))
        if [ $line_num -eq 1 ]; then
            echo "Headers: $col1, $col2, $col3"
        else
            echo "Row $((line_num-1)): $col1 | $col2 | $col3"
        fi
    done < "$file"
}

# Log file analysis
analyze_log() {
    local logfile=$1
    echo "Log analysis for: $logfile"
    echo "Total lines: $(wc -l < "$logfile")"
    echo "Error count: $(grep -c "ERROR" "$logfile")"
    echo "Warning count: $(grep -c "WARNING" "$logfile")"
    echo "Recent errors:"
    grep "ERROR" "$logfile" | tail -5
}
```

---

## Advanced Concepts

### Command Substitution
```bash
#!/bin/bash

# Using $() - preferred method
current_date=$(date)
echo "Current date: $current_date"

current_user=$(whoami)
echo "Current user: $current_user"

file_count=$(ls -1 | wc -l)
echo "Files in directory: $file_count"

# Using backticks (older method)
uptime_info=`uptime`
echo "System uptime: $uptime_info"
```

### Process Substitution
```bash
#!/bin/bash

# Compare output of two commands
diff <(ls dir1) <(ls dir2)

# Process multiple files
while read line; do
    echo "Line: $line"
done < <(cat file1.txt file2.txt)
```

### Signal Handling
```bash
#!/bin/bash

# Trap signals
cleanup() {
    echo "Cleaning up..."
    rm -f temp_files*
    exit 0
}

# Trap SIGINT (Ctrl+C) and SIGTERM
trap cleanup INT TERM

echo "Script running... Press Ctrl+C to stop"
while true; do
    touch "temp_file_$$_$(date +%s)"
    sleep 1
done
```

### Parallel Processing
```bash
#!/bin/bash

# Background processes
process_file() {
    local file=$1
    echo "Processing $file..."
    sleep 2
    echo "Finished processing $file"
}

# Process files in parallel
for file in *.txt; do
    process_file "$file" &  # & runs in background
done

wait  # Wait for all background processes to complete
echo "All files processed"

# Using xargs for parallel processing
echo -e "task1\ntask2\ntask3" | xargs -I {} -P 3 bash -c 'echo "Processing {}"; sleep 2; echo "Done {}"'
```

### Regular Expressions
```bash
#!/bin/bash

# Pattern matching with =~
text="The year is 2024"
if [[ $text =~ [0-9]{4} ]]; then
    echo "Found year: ${BASH_REMATCH[0]}"
fi

# Email validation
email="user@example.com"
email_pattern="^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$"
if [[ $email =~ $email_pattern ]]; then
    echo "Valid email format"
fi

# Extract information
log_line="2024-01-26 10:30:45 ERROR: Database connection failed"
if [[ $log_line =~ ([0-9]{4}-[0-9]{2}-[0-9]{2})\ ([0-9]{2}:[0-9]{2}:[0-9]{2})\ ([A-Z]+):\ (.+) ]]; then
    echo "Date: ${BASH_REMATCH[1]}"
    echo "Time: ${BASH_REMATCH[2]}"
    echo "Level: ${BASH_REMATCH[3]}"
    echo "Message: ${BASH_REMATCH[4]}"
fi
```

---

## Debugging and Error Handling

### Debugging Options
```bash
#!/bin/bash

# Enable debugging
set -x    # Print commands as they're executed
set -e    # Exit on any error
set -u    # Exit on undefined variables
set -o pipefail  # Exit on pipe failures

# Or combine them
set -euo pipefail

# Debug specific sections
{
    set -x
    echo "Debug this section"
    ls /nonexistent 2>/dev/null || echo "Directory not found"
    set +x
}

# Conditional debugging
DEBUG=${DEBUG:-0}
if [ $DEBUG -eq 1 ]; then
    set -x
fi
```

### Error Handling
```bash
#!/bin/bash

# Error handling function
handle_error() {
    local exit_code=$?
    local line_number=$1
    echo "Error occurred in script at line $line_number: exit code $exit_code" >&2
    exit $exit_code
}

trap 'handle_error $LINENO' ERR

# Function with error handling
safe_operation() {
    local file=$1
    
    if [ ! -f "$file" ]; then
        echo "Error: File '$file' not found" >&2
        return 1
    fi
    
    if ! cp "$file" "${file}.backup"; then
        echo "Error: Failed to create backup" >&2
        return 2
    fi
    
    echo "Backup created successfully"
    return 0
}

# Usage with error checking
if safe_operation "important.txt"; then
    echo "Operation successful"
else
    echo "Operation failed with code: $?"
fi
```

### Logging
```bash
#!/bin/bash

# Logging function
log() {
    local level=$1
    shift
    local message="$@"
    local timestamp=$(date '+%Y-%m-%d %H:%M:%S')
    echo "[$timestamp] [$level] $message" | tee -a script.log
}

# Usage
log "INFO" "Script started"
log "ERROR" "Something went wrong"
log "DEBUG" "Variable value: $variable"

# Redirect all output to log file
exec > >(tee -a script.log)
exec 2>&1

echo "This goes to both console and log file"
```

---

## Best Practices

### Script Template
```bash
#!/bin/bash

#######################################
# Script Name: my_script.sh
# Description: Brief description of what the script does
# Author: Your Name
# Date: 2025-01-26
# Version: 1.0
#######################################

# Exit on any error
set -euo pipefail

# Script configuration
readonly SCRIPT_NAME=$(basename "$0")
readonly SCRIPT_DIR=$(cd "$(dirname "$0")" && pwd)
readonly LOG_FILE="${SCRIPT_DIR}/${SCRIPT_NAME%.*}.log"

# Global variables
declare -g DEBUG=${DEBUG:-0}

#######################################
# Print usage information
# Globals:
#   SCRIPT_NAME
# Arguments:
#   None
# Returns:
#   None
#######################################
usage() {
    cat << EOF
Usage: $SCRIPT_NAME [OPTIONS] [ARGUMENTS]

Description:
    Brief description of the script

Options:
    -h, --help      Show this help message
    -v, --verbose   Enable verbose output
    -d, --debug     Enable debug mode

Examples:
    $SCRIPT_NAME --verbose file.txt
    $SCRIPT_NAME -d --input data.csv

EOF
}

#######################################
# Log messages with timestamp
# Arguments:
#   $1 - Log level (INFO, ERROR, DEBUG)
#   $@ - Message to log
# Returns:
#   None
#######################################
log() {
    local level=$1
    shift
    local message="$@"
    local timestamp=$(date '+%Y-%m-%d %H:%M:%S')
    
    if [[ $level == "DEBUG" && $DEBUG -eq 0 ]]; then
        return
    fi
    
    echo "[$timestamp] [$level] $message" | tee -a "$LOG_FILE"
}

#######################################
# Main function
# Arguments:
#   All script arguments
# Returns:
#   0 on success, non-zero on error
#######################################
main() {
    local verbose=0
    
    # Parse command line arguments
    while [[ $# -gt 0 ]]; do
        case $1 in
            -h|--help)
                usage
                exit 0
                ;;
            -v|--verbose)
                verbose=1
                shift
                ;;
            -d|--debug)
                DEBUG=1
                set -x
                shift
                ;;
            -*)
                echo "Unknown option: $1" >&2
                usage
                exit 1
                ;;
            *)
                # Positional arguments
                break
                ;;
        esac
    done
    
    log "INFO" "Script started"
    
    # Main script logic here
    if [[ $verbose -eq 1 ]]; then
        log "INFO" "Verbose mode enabled"
    fi
    
    log "INFO" "Script completed successfully"
}

# Error handling
trap 'log "ERROR" "Script failed at line $LINENO"' ERR

# Run main function if script is executed directly
if [[ "${BASH_SOURCE[0]}" == "${0}" ]]; then
    main "$@"
fi
```

### Security Best Practices
```bash
#!/bin/bash

# 1. Always quote variables
file_name="my file.txt"
ls -l "$file_name"  # Correct
# ls -l $file_name  # Wrong - will break with spaces

# 2. Use [[ ]] instead of [ ] for tests
if [[ -f "$file_name" && -r "$file_name" ]]; then
    echo "File exists and is readable"
fi

# 3. Validate input
validate_input() {
    local input=$1
    
    # Check if input is empty
    if [[ -z "$input" ]]; then
        echo "Error: Input cannot be empty" >&2
        return 1
    fi
    
    # Check for dangerous characters
    if [[ "$input" =~ [;\|\&\$\`] ]]; then
        echo "Error: Input contains dangerous characters" >&2
        return 1
    fi
    
    return 0
}

# 4. Use mktemp for temporary files
temp_file=$(mktemp)
trap "rm -f '$temp_file'" EXIT

# 5. Set secure permissions
umask 077  # Only owner can read/write

# 6. Avoid eval and command substitution with user input
# Never do: eval "$user_input"
```

---

## Real-World Examples

### System Monitoring Script
```bash
#!/bin/bash

# System monitoring script
monitor_system

### Backup Script
```bash
#!/bin/bash

# Automated backup script
BACKUP_SOURCE="/home/user/documents"
BACKUP_DEST="/backup"
DATE=$(date +%Y%m%d_%H%M%S)
BACKUP_NAME="backup_$DATE.tar.gz"
LOG_FILE="/var/log/backup.log"

perform_backup() {
    log "INFO" "Starting backup process"
    
    # Check if source exists
    if [[ ! -d "$BACKUP_SOURCE" ]]; then
        log "ERROR" "Source directory does not exist: $BACKUP_SOURCE"
        return 1
    fi
    
    # Create backup directory if it doesn't exist
    mkdir -p "$BACKUP_DEST"
    
    # Create backup
    if tar -czf "$BACKUP_DEST/$BACKUP_NAME" -C "$(dirname "$BACKUP_SOURCE")" "$(basename "$BACKUP_SOURCE")"; then
        log "INFO" "Backup created successfully: $BACKUP_DEST/$BACKUP_NAME"
        
        # Calculate backup size
        backup_size=$(du -h "$BACKUP_DEST/$BACKUP_NAME" | cut -f1)
        log "INFO" "Backup size: $backup_size"
        
        # Remove old backups (keep last 7 days)
        find "$BACKUP_DEST" -name "backup_*.tar.gz" -mtime +7 -delete
        log "INFO" "Old backups cleaned up"
        
        return 0
    else
        log "ERROR" "Backup creation failed"
        return 1
    fi
}

# Send email notification
send_notification() {
    local status=$1
    local subject="Backup $status - $(hostname)"
    local message="Backup process $status at $(date)"
    
    # Using mail command (if available)
    echo "$message" | mail -s "$subject" admin@example.com 2>/dev/null || true
}

# Main execution
if perform_backup; then
    send_notification "SUCCESS"
    exit 0
else
    send_notification "FAILED"
    exit 1
fi
```

### Log Analysis Script
```bash
#!/bin/bash

# Log analysis script
analyze_apache_logs() {
    local log_file=$1
    local output_dir="log_analysis_$(date +%Y%m%d)"
    
    mkdir -p "$output_dir"
    
    echo "Analyzing Apache log: $log_file"
    echo "Output directory: $output_dir"
    
    # Top 10 IP addresses
    echo "=== Top 10 IP Addresses ===" > "$output_dir/top_ips.txt"
    awk '{print $1}' "$log_file" | sort | uniq -c | sort -nr | head -10 >> "$output_dir/top_ips.txt"
    
    # Top 10 requested pages
    echo "=== Top 10 Requested Pages ===" > "$output_dir/top_pages.txt"
    awk '{print $7}' "$log_file" | sort | uniq -c | sort -nr | head -10 >> "$output_dir/top_pages.txt"
    
    # Error analysis
    echo "=== Error Analysis ===" > "$output_dir/errors.txt"
    grep " 404 " "$log_file" | wc -l | xargs echo "404 Errors:" >> "$output_dir/errors.txt"
    grep " 500 " "$log_file" | wc -l | xargs echo "500 Errors:" >> "$output_dir/errors.txt"
    
    # Hourly traffic analysis
    echo "=== Hourly Traffic ===" > "$output_dir/hourly_traffic.txt"
    awk '{print $4}' "$log_file" | cut -d: -f2 | sort | uniq -c | sort -n >> "$output_dir/hourly_traffic.txt"
    
    # Generate summary report
    cat << EOF > "$output_dir/summary.txt"
Log Analysis Summary for: $log_file
Generated on: $(date)

Total Requests: $(wc -l < "$log_file")
Unique IPs: $(awk '{print $1}' "$log_file" | sort -u | wc -l)
Date Range: $(head -1 "$log_file" | awk '{print $4}' | tr -d '[') to $(tail -1 "$log_file" | awk '{print $4}' | tr -d '[')

Files generated:
- top_ips.txt: Top IP addresses by request count
- top_pages.txt: Most requested pages
- errors.txt: Error analysis
- hourly_traffic.txt: Traffic distribution by hour
EOF

    echo "Analysis complete. Results saved in: $output_dir"
}

# Usage
if [[ $# -eq 0 ]]; then
    echo "Usage: $0 <apache_log_file>"
    exit 1
fi

analyze_apache_logs "$1"
```

### Database Backup and Restore
```bash
#!/bin/bash

# Database operations script
DB_HOST="localhost"
DB_USER="backup_user"
DB_NAME="production_db"
BACKUP_DIR="/db_backups"
DATE=$(date +%Y%m%d_%H%M%S)

# Database backup
backup_database() {
    local backup_file="$BACKUP_DIR/${DB_NAME}_backup_$DATE.sql"
    
    echo "Starting database backup..."
    
    # Create backup directory
    mkdir -p "$BACKUP_DIR"
    
    # Perform backup
    if mysqldump -h "$DB_HOST" -u "$DB_USER" -p "$DB_NAME" > "$backup_file"; then
        echo "Backup completed: $backup_file"
        
        # Compress backup
        gzip "$backup_file"
        echo "Backup compressed: ${backup_file}.gz"
        
        # Set permissions
        chmod 600 "${backup_file}.gz"
        
        # Remove old backups (keep last 30 days)
        find "$BACKUP_DIR" -name "${DB_NAME}_backup_*.sql.gz" -mtime +30 -delete
        
        return 0
    else
        echo "Backup failed!"
        return 1
    fi
}

# Database restore
restore_database() {
    local backup_file=$1
    
    if [[ ! -f "$backup_file" ]]; then
        echo "Backup file not found: $backup_file"
        return 1
    fi
    
    echo "WARNING: This will overwrite the current database!"
    read -p "Are you sure you want to continue? (yes/no): " confirm
    
    if [[ "$confirm" != "yes" ]]; then
        echo "Restore cancelled."
        return 1
    fi
    
    echo "Starting database restore..."
    
    # Check if file is compressed
    if [[ "$backup_file" == *.gz ]]; then
        if zcat "$backup_file" | mysql -h "$DB_HOST" -u "$DB_USER" -p "$DB_NAME"; then
            echo "Restore completed successfully."
            return 0
        fi
    else
        if mysql -h "$DB_HOST" -u "$DB_USER" -p "$DB_NAME" < "$backup_file"; then
            echo "Restore completed successfully."
            return 0
        fi
    fi
    
    echo "Restore failed!"
    return 1
}

# List available backups
list_backups() {
    echo "Available backups:"
    ls -lh "$BACKUP_DIR"/${DB_NAME}_backup_*.sql.gz 2>/dev/null || echo "No backups found."
}

# Main menu
case "$1" in
    "backup")
        backup_database
        ;;
    "restore")
        if [[ -z "$2" ]]; then
            echo "Usage: $0 restore <backup_file>"
            list_backups
            exit 1
        fi
        restore_database "$2"
        ;;
    "list")
        list_backups
        ;;
    *)
        echo "Usage: $0 {backup|restore <file>|list}"
        exit 1
        ;;
esac
```

---

## Top 10 Interview Questions with Answers

### 1. What is the difference between `$*` and `$@`?

**Answer:**
```bash
#!/bin/bash

# $* - All arguments as single string
# $@ - All arguments as separate words

demo_args() {
    echo "Using \$*:"
    for arg in $*; do
        echo "Arg: $arg"
    done
    
    echo -e "\nUsing \$@:"
    for arg in $@; do
        echo "Arg: $arg"
    done
    
    echo -e "\nUsing \"\$*\":"
    for arg in "$*"; do
        echo "Arg: $arg"
    done
    
    echo -e "\nUsing \"\$@\":"
    for arg in "$@"; do
        echo "Arg: $arg"
    done
}

demo_args "hello world" "test case"
```

**Explanation:**
- `$*` treats all arguments as a single string
- `$@` treats each argument separately
- `"$*"` creates one argument with all parameters separated by first character of IFS
- `"$@"` preserves each argument as separate quoted strings (most commonly used)

### 2. How do you check if a file exists and is readable?

**Answer:**
```bash
#!/bin/bash

check_file() {
    local file=$1
    
    # Method 1: Multiple conditions
    if [[ -f "$file" && -r "$file" ]]; then
        echo "File exists and is readable"
        return 0
    else
        echo "File doesn't exist or is not readable"
        return 1
    fi
}

# Method 2: Separate checks with detailed feedback
check_file_detailed() {
    local file=$1
    
    if [[ ! -e "$file" ]]; then
        echo "File does not exist"
    elif [[ ! -f "$file" ]]; then
        echo "Path exists but is not a regular file"
    elif [[ ! -r "$file" ]]; then
        echo "File exists but is not readable"
    else
        echo "File exists and is readable"
    fi
}

# Usage
check_file "test.txt"
check_file_detailed "test.txt"
```

**Common file test operators:**
- `-e`: exists
- `-f`: regular file
- `-d`: directory
- `-r`: readable
- `-w`: writable
- `-x`: executable
- `-s`: not empty

### 3. Write a script to find and kill processes by name.

**Answer:**
```bash
#!/bin/bash

kill_process_by_name() {
    local process_name=$1
    local signal=${2:-TERM}  # Default to TERM signal
    
    if [[ -z "$process_name" ]]; then
        echo "Usage: kill_process_by_name <process_name> [signal]"
        return 1
    fi
    
    # Find processes
    local pids=$(pgrep "$process_name")
    
    if [[ -z "$pids" ]]; then
        echo "No processes found with name: $process_name"
        return 1
    fi
    
    echo "Found processes:"
    ps -p $pids -o pid,ppid,cmd
    
    # Confirm before killing
    read -p "Kill these processes? (y/N): " confirm
    if [[ "$confirm" =~ ^[Yy]$ ]]; then
        for pid in $pids; do
            if kill -$signal $pid 2>/dev/null; then
                echo "Killed process $pid"
            else
                echo "Failed to kill process $pid"
            fi
        done
    else
        echo "Operation cancelled"
    fi
}

# Alternative method using pkill
kill_process_pkill() {
    local process_name=$1
    local signal=${2:-TERM}
    
    if pkill -$signal "$process_name"; then
        echo "Processes killed successfully"
    else
        echo "No processes found or kill failed"
    fi
}

# Usage examples
kill_process_by_name "firefox"
kill_process_by_name "chrome" "KILL"
```

### 4. How do you parse command line arguments in a shell script?

**Answer:**
```bash
#!/bin/bash

# Method 1: Manual parsing
parse_args_manual() {
    local verbose=0
    local output_file=""
    local input_files=()
    
    while [[ $# -gt 0 ]]; do
        case $1 in
            -v|--verbose)
                verbose=1
                shift
                ;;
            -o|--output)
                output_file="$2"
                shift 2
                ;;
            -h|--help)
                echo "Usage: $0 [-v] [-o output] [files...]"
                exit 0
                ;;
            -*)
                echo "Unknown option: $1" >&2
                exit 1
                ;;
            *)
                input_files+=("$1")
                shift
                ;;
        esac
    done
    
    echo "Verbose: $verbose"
    echo "Output file: $output_file"
    echo "Input files: ${input_files[@]}"
}

# Method 2: Using getopts (for single-character options)
parse_args_getopts() {
    local verbose=0
    local output_file=""
    
    while getopts "vo:h" opt; do
        case $opt in
            v)
                verbose=1
                ;;
            o)
                output_file="$OPTARG"
                ;;
            h)
                echo "Usage: $0 [-v] [-o output]"
                exit 0
                ;;
            \?)
                echo "Invalid option: -$OPTARG" >&2
                exit 1
                ;;
        esac
    done
    
    # Shift processed options
    shift $((OPTIND-1))
    
    echo "Verbose: $verbose"
    echo "Output file: $output_file"
    echo "Remaining args: $@"
}

# Example usage
parse_args_manual "$@"
```

### 5. Write a function to check if a string is a valid IP address.

**Answer:**
```bash
#!/bin/bash

validate_ip() {
    local ip=$1
    local IFS='.'
    local -a octets=($ip)
    
    # Check if we have exactly 4 octets
    if [[ ${#octets[@]} -ne 4 ]]; then
        return 1
    fi
    
    # Check each octet
    for octet in "${octets[@]}"; do
        # Check if octet is numeric
        if ! [[ "$octet" =~ ^[0-9]+$ ]]; then
            return 1
        fi
        
        # Check if octet is in valid range (0-255)
        if [[ $octet -lt 0 || $octet -gt 255 ]]; then
            return 1
        fi
        
        # Check for leading zeros (except for "0")
        if [[ ${#octet} -gt 1 && ${octet:0:1} == "0" ]]; then
            return 1
        fi
    done
    
    return 0
}

# Alternative method using regex
validate_ip_regex() {
    local ip=$1
    local regex='^([0-9]{1,3}\.){3}[0-9]{1,3}() {
    echo "=== System Monitor Report ==="
    echo "Date: $(date)"
    echo
    
    # CPU Usage
    echo "=== CPU Usage ==="
    top -bn1 | grep "Cpu(s)" | awk '{print $2}' | cut -d'%' -f1
    
    # Memory Usage
    echo "=== Memory Usage ==="
    free -h | grep -E "^Mem|^Swap"
    
    # Disk Usage
    echo "=== Disk Usage ==="
    df -h | grep -vE '^Filesystem|tmpfs|cdrom'
    
    # Network connections
    echo "=== Network Connections ==="
    netstat -tuln | grep LISTEN | wc -l
    echo "Active listening ports: $(netstat -tuln | grep LISTEN | wc -l)"
    
    # Load average
    echo "=== Load Average ==="
    uptime | awk -F'load average:' '{print $2}'
    
    # Top processes
    echo "=== Top 5 CPU Processes ==="
    ps aux --sort=-%cpu | head -6
}

monitor_system
    
    
    if [[ $ip =~ $regex ]]; then
        # Additional validation for range
        local IFS='.'
        local -a octets=($ip)
        for octet in "${octets[@]}"; do
            if [[ $octet -gt 255 ]]; then
                return 1
            fi
        done
        return 0
    fi
    return 1
}

# Test function
test_ip_validation() {
    local test_ips=(
        "192.168.1.1"    # Valid
        "255.255.255.255" # Valid
        "0.0.0.0"        # Valid
        "192.168.1.256"  # Invalid (256 > 255)
        "192.168.1"      # Invalid (missing octet)
        "192.168.01.1"   # Invalid (leading zero)
        "192.168.1.1.1"  # Invalid (too many octets)
        "abc.def.ghi.jkl" # Invalid (non-numeric)
    )
    
    for ip in "${test_ips[@]}"; do
        if validate_ip "$ip"; then
            echo "$ip: VALID"
        else
            echo "$ip: INVALID"
        fi
    done
}

test_ip_validation
```

### 6. How do you handle errors and implement logging in shell scripts?

**Answer:**
```bash
#!/bin/bash

# Set strict error handling
set -euo pipefail

# Global variables
readonly SCRIPT_NAME=$(basename "$0")
readonly LOG_DIR="/var/log"
readonly LOG_FILE="$LOG_DIR/${SCRIPT_NAME%.*}.log"

# Ensure log directory exists
mkdir -p "$LOG_DIR" 2>/dev/null || LOG_FILE="/tmp/${SCRIPT_NAME%.*}.log"

# Logging function
log() {
    local level=$1
    shift
    local message="$*"
    local timestamp=$(date '+%Y-%m-%d %H:%M:%S')
    local log_entry="[$timestamp] [$level] [$] $message"
    
    # Write to log file
    echo "$log_entry" >> "$LOG_FILE"
    
    # Also output to stderr for ERROR and higher
    case $level in
        ERROR|FATAL)
            echo "$log_entry" >&2
            ;;
        *)
            echo "$log_entry"
            ;;
    esac
}

# Error handler
error_handler() {
    local exit_code=$?
    local line_number=$1
    
    log "ERROR" "Script failed at line $line_number with exit code $exit_code"
    log "ERROR" "Command: ${BASH_COMMAND}"
    
    # Cleanup function
    cleanup
    
    exit $exit_code
}

# Cleanup function
cleanup() {
    log "INFO" "Performing cleanup..."
    # Remove temporary files, close connections, etc.
    rm -f /tmp/temp_file_$* 2>/dev/null || true
}

# Set up error handling
trap 'error_handler $LINENO' ERR
trap 'cleanup' EXIT

# Function with error handling
safe_operation() {
    local file=$1
    
    log "INFO" "Starting operation on file: $file"
    
    # Validate input
    if [[ -z "$file" ]]; then
        log "ERROR" "File parameter is empty"
        return 1
    fi
    
    if [[ ! -f "$file" ]]; then
        log "ERROR" "File does not exist: $file"
        return 2
    fi
    
    # Perform operation
    if cp "$file" "${file}.backup"; then
        log "INFO" "Backup created successfully"
    else
        log "ERROR" "Failed to create backup"
        return 3
    fi
    
    log "INFO" "Operation completed successfully"
    return 0
}

# Example usage with error handling
main() {
    log "INFO" "Script started"
    
    local file="example.txt"
    
    if safe_operation "$file"; then
        log "INFO" "All operations completed successfully"
    else
        local exit_code=$?
        log "ERROR" "Operation failed with code: $exit_code"
        exit $exit_code
    fi
}

# Run main function if script is executed directly
if [[ "${BASH_SOURCE[0]}" == "${0}" ]]; then
    main "$@"
fi
```

### 7. Write a script to monitor disk usage and send alerts.

**Answer:**
```bash
#!/bin/bash

# Disk monitoring script
THRESHOLD=80
EMAIL="admin@example.com"
LOG_FILE="/var/log/disk_monitor.log"

# Function to send email alert
send_alert() {
    local filesystem=$1
    local usage=$2
    local available=$3
    
    local subject="DISK ALERT: High disk usage on $(hostname)"
    local message=$(cat << EOF
DISK USAGE ALERT

Server: $(hostname)
Filesystem: $filesystem
Usage: $usage%
Available: $available
Threshold: $THRESHOLD%
Time: $(date)

Please take immediate action to free up disk space.
EOF
)
    
    # Send email (requires mail command)
    echo "$message" | mail -s "$subject" "$EMAIL" 2>/dev/null || {
        logger "Failed to send email alert for disk usage"
    }
    
    # Log the alert
    echo "$(date '+%Y-%m-%d %H:%M:%S') ALERT: $filesystem usage at $usage%" >> "$LOG_FILE"
}

# Function to check disk usage
check_disk_usage() {
    echo "$(date '+%Y-%m-%d %H:%M:%S') Starting disk usage check" >> "$LOG_FILE"
    
    # Get disk usage information
    df -h | grep -vE '^Filesystem|tmpfs|cdrom|udev' | while read filesystem size used available usage mountpoint; do
        # Extract numeric percentage
        usage_percent=$(echo $usage | tr -d '%')
        
        echo "Checking $filesystem: $usage_percent% used" >> "$LOG_FILE"
        
        if [[ $usage_percent -ge $THRESHOLD ]]; then
            echo "WARNING: $filesystem usage is $usage_percent%" | tee -a "$LOG_FILE"
            send_alert "$filesystem" "$usage_percent" "$available"
        fi
    done
}

# Function to get top space consuming directories
get_space_consumers() {
    local path=$1
    echo "Top 10 space consumers in $path:"
    du -h "$path"/* 2>/dev/null | sort -rh | head -10
}

# Function to clean up temporary files
cleanup_temp_files() {
    echo "Cleaning up temporary files..."
    
    # Clean /tmp files older than 7 days
    find /tmp -type f -mtime +7 -delete 2>/dev/null || true
    
    # Clean log files older than 30 days
    find /var/log -name "*.log" -mtime +30 -delete 2>/dev/null || true
    
    # Clean package cache (on Debian/Ubuntu)
    if command -v apt-get &> /dev/null; then
        apt-get clean 2>/dev/null || true
    fi
    
    # Clean package cache (on RHEL/CentOS)
    if command -v yum &> /dev/null; then
        yum clean all 2>/dev/null || true
    fi
}

# Main function
main() {
    case "${1:-check}" in
        "check")
            check_disk_usage
            ;;
        "clean")
            cleanup_temp_files
            ;;
        "analyze")
            if [[ -n "$2" ]]; then
                get_space_consumers "$2"
            else
                get_space_consumers "/"
            fi
            ;;
        "report")
            echo "=== Disk Usage Report ==="
            df -h
            echo
            echo "=== Recent Alerts ==="
            tail -20 "$LOG_FILE" 2>/dev/null || echo "No log file found"
            ;;
        *)
            echo "Usage: $0 {check|clean|analyze [path]|report}"
            echo "  check   - Check disk usage and send alerts"
            echo "  clean   - Clean up temporary files"
            echo "  analyze - Analyze space usage in given path"
            echo "  report  - Generate disk usage report"
            exit 1
            ;;
    esac
}

main "$@"
```

### 8. How do you process CSV files and extract specific columns?

**Answer:**
```bash
#!/bin/bash

# CSV processing functions

# Extract specific columns by number
extract_columns_by_number() {
    local file=$1
    local columns=$2  # Comma-separated column numbers (1,3,5)
    
    if [[ ! -f "$file" ]]; then
        echo "File not found: $file" >&2
        return 1
    fi
    
    cut -d',' -f"$columns" "$file"
}

# Extract columns by header name
extract_columns_by_name() {
    local file=$1
    shift
    local column_names=("$@")
    
    if [[ ! -f "$file" ]]; then
        echo "File not found: $file" >&2
        return 1
    fi
    
    # Get header line
    local header=$(head -n1 "$file")
    local IFS=','
    local -a headers=($header)
    
    # Find column positions
    local column_positions=()
    for target_name in "${column_names[@]}"; do
        for i in "${!headers[@]}"; do
            if [[ "${headers[i]}" == "$target_name" ]]; then
                column_positions+=($((i+1)))
                break
            fi
        done
    done
    
    if [[ ${#column_positions[@]} -eq 0 ]]; then
        echo "No matching columns found" >&2
        return 1
    fi
    
    # Extract columns
    local positions=$(IFS=','; echo "${column_positions[*]}")
    cut -d',' -f"$positions" "$file"
}

# Filter CSV rows based on condition
filter_csv_rows() {
    local file=$1
    local column=$2
    local condition=$3
    local value=$4
    
    # Skip header and process data
    {
        head -n1 "$file"  # Print header
        tail -n+2 "$file" | while IFS=',' read -r -a row; do
            local cell_value="${row[$((column-1))]}"
            
            case $condition in
                "eq"|"=")
                    [[ "$cell_value" == "$value" ]] && echo "${row[*]}" | tr ' ' ','
                    ;;
                "gt"|">")
                    [[ "$cell_value" -gt "$value" ]] 2>/dev/null && echo "${row[*]}" | tr ' ' ','
                    ;;
                "lt"|"<")
                    [[ "$cell_value" -lt "$value" ]] 2>/dev/null && echo "${row[*]}" | tr ' ' ','
                    ;;
                "contains")
                    [[ "$cell_value" == *"$value"* ]] && echo "${row[*]}" | tr ' ' ','
                    ;;
            esac
        done
    }
}

# Calculate statistics for numeric column
calculate_column_stats() {
    local file=$1
    local column=$2
    
    echo "Statistics for column $column:"
    
    # Extract numeric values (skip header)
    local values=$(tail -n+2 "$file" | cut -d',' -f"$column" | grep -E '^[0-9]+(\.[0-9]+)?() {
    echo "=== System Monitor Report ==="
    echo "Date: $(date)"
    echo
    
    # CPU Usage
    echo "=== CPU Usage ==="
    top -bn1 | grep "Cpu(s)" | awk '{print $2}' | cut -d'%' -f1
    
    # Memory Usage
    echo "=== Memory Usage ==="
    free -h | grep -E "^Mem|^Swap"
    
    # Disk Usage
    echo "=== Disk Usage ==="
    df -h | grep -vE '^Filesystem|tmpfs|cdrom'
    
    # Network connections
    echo "=== Network Connections ==="
    netstat -tuln | grep LISTEN | wc -l
    echo "Active listening ports: $(netstat -tuln | grep LISTEN | wc -l)"
    
    # Load average
    echo "=== Load Average ==="
    uptime | awk -F'load average:' '{print $2}'
    
    # Top processes
    echo "=== Top 5 CPU Processes ==="
    ps aux --sort=-%cpu | head -6
}

monitor_system
    )
    
    if [[ -z "$values" ]]; then
        echo "No numeric data found in column $column"
        return 1
    fi
    
    # Calculate statistics
    local count=$(echo "$values" | wc -l)
    local sum=$(echo "$values" | awk '{sum += $1} END {print sum}')
    local avg=$(echo "scale=2; $sum / $count" | bc)
    local min=$(echo "$values" | sort -n | head -1)
    local max=$(echo "$values" | sort -n | tail -1)
    
    echo "Count: $count"
    echo "Sum: $sum"
    echo "Average: $avg"
    echo "Min: $min"
    echo "Max: $max"
}

# Convert CSV to different formats
convert_csv() {
    local file=$1
    local format=$2
    
    case $format in
        "tsv")
            # Convert to tab-separated values
            tr ',' '\t' < "$file"
            ;;
        "json")
            # Convert to JSON (simple implementation)
            {
                echo "["
                local header=$(head -n1 "$file")
                local IFS=','
                local -a headers=($header)
                
                tail -n+2 "$file" | while IFS=',' read -r -a row; do
                    echo "  {"
                    for i in "${!headers[@]}"; do
                        local comma=""
                        [[ $i -lt $((${#headers[@]}-1)) ]] && comma=","
                        echo "    \"${headers[i]}\": \"${row[i]}\"$comma"
                    done
                    echo "  },"
                done | sed '$ s/,$//'  # Remove last comma
                echo "]"
            }
            ;;
        "html")
            # Convert to HTML table
            echo "<table border='1'>"
            echo "<tr>"
            head -n1 "$file" | tr ',' '\n' | sed 's/.*/<th>&<\/th>/'
            echo "</tr>"
            tail -n+2 "$file" | while IFS=',' read -r -a row; do
                echo "<tr>"
                for cell in "${row[@]}"; do
                    echo "<td>$cell</td>"
                done
                echo "</tr>"
            done
            echo "</table>"
            ;;
    esac
}

# Example usage and testing
demo_csv_processing() {
    # Create sample CSV file
    cat << EOF > sample.csv
Name,Age,City,Salary
John,30,New York,50000
Jane,25,Los Angeles,60000
Bob,35,Chicago,55000
Alice,28,Boston,65000
EOF

    echo "=== Original CSV ==="
    cat sample.csv
    
    echo -e "\n=== Extract columns 1 and 3 ==="
    extract_columns_by_number sample.csv "1,3"
    
    echo -e "\n=== Extract Name and Salary columns ==="
    extract_columns_by_name sample.csv "Name" "Salary"
    
    echo -e "\n=== Filter rows where Age > 28 ==="
    filter_csv_rows sample.csv 2 "gt" 28
    
    echo -e "\n=== Statistics for Salary column ==="
    calculate_column_stats sample.csv 4
    
    echo -e "\n=== Convert to JSON ==="
    convert_csv sample.csv "json"
    
    # Cleanup
    rm -f sample.csv
}

# Uncomment to run demo
# demo_csv_processing
```

### 9. Write a function to backup files with rotation (keep only N backups).

**Answer:**
```bash
#!/bin/bash

# Backup function with rotation
backup_with_rotation() {
    local source_path=$1
    local backup_dir=$2
    local max_backups=${3:-7}  # Default to 7 backups
    local backup_prefix=${4:-"backup"}
    
    # Validate inputs
    if [[ -z "$source_path" || -z "$backup_dir" ]];() {
    echo "=== System Monitor Report ==="
    echo "Date: $(date)"
    echo
    
    # CPU Usage
    echo "=== CPU Usage ==="
    top -bn1 | grep "Cpu(s)" | awk '{print $2}' | cut -d'%' -f1
    
    # Memory Usage
    echo "=== Memory Usage ==="
    free -h | grep -E "^Mem|^Swap"
    
    # Disk Usage
    echo "=== Disk Usage ==="
    df -h | grep -vE '^Filesystem|tmpfs|cdrom'
    
    # Network connections
    echo "=== Network Connections ==="
    netstat -tuln | grep LISTEN | wc -l
    echo "Active listening ports: $(netstat -tuln | grep LISTEN | wc -l)"
    
    # Load average
    echo "=== Load Average ==="
    uptime | awk -F'load average:' '{print $2}'
    
    # Top processes
    echo "=== Top 5 CPU Processes ==="
    ps aux --sort=-%cpu | head -6
}

monitor_system

