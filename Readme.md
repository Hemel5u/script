#!/bin/bash

# Function to find duplicate files
find_duplicate_files() {
    directory="$1"
    # Create an associative array to store MD5 hashes as keys and filenames as values
    declare -A md5_hash_map

    # Iterate through all files in the given directory
    while IFS= read -r file; do
        # Calculate MD5 hash of the file's content
        md5sum_output=$(md5sum "$file")
        md5_hash="${md5sum_output%% *}"  # Extract MD5 hash from md5sum output

        # If the MD5 hash is already in the hash map, it's a duplicate
        if [[ -n "${md5_hash_map[$md5_hash]}" ]]; then
            echo "Duplicate found: $file and ${md5_hash_map[$md5_hash]}"
        else
            # Otherwise, store the current file's name in the hash map
            md5_hash_map["$md5_hash"]=$file
        fi
    done < <(find "$directory" -type f)

    echo "Duplicate search completed."
}

# Prompt the user for a directory name
read -p "Enter a directory name: " input_directory

# Check if the directory exists
if [[ -d "$input_directory" ]]; then
    find_duplicate_files "$input_directory"
else
    echo "Directory not found."
fi
