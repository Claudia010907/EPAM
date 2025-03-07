#!/bin/bash

# Define the input and output file
INPUT_FILE="passwd"
OUTPUT_FILE="passwd_new"

# Step (a): Create a copy of passwd file
cp "$INPUT_FILE" "$OUTPUT_FILE"

# Step (b): Change shell for user 'saned' using AWK
awk -F: 'BEGIN{OFS=":"} $1=="saned" {$NF="/bin/bash"}1' "$OUTPUT_FILE" > temp && mv temp "$OUTPUT_FILE"

# Step (c): Change shell for user 'avahi' using SED
sed -i '/^avahi:/s#/usr/sbin/nologin#/bin/bash#' "$OUTPUT_FILE"

# Step (d): Keep only 1st, 3rd, 5th, and 7th columns
awk -F: 'BEGIN{OFS=":"} {print $1, $3, $5, $7}' "$OUTPUT_FILE" > temp && mv temp "$OUTPUT_FILE"

# Step (e): Remove all lines containing "daemon"
sed -i '/daemon/d' "$OUTPUT_FILE"

# Step (f): Change shell for users with even UID to /bin/zsh
awk -F: 'BEGIN{OFS=":"} {if ($2 % 2 == 0) $4="/bin/zsh"; print}' "$OUTPUT_FILE" > temp && mv temp "$OUTPUT_FILE"

# Step (g): Ensure no new line at the end of the file
truncate -s -1 "$OUTPUT_FILE"
