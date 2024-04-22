# DevopsInternAssignment


# Tested a linux on vagrant
# Firstly created a file name script.py
# Created a app.log file where used to mention configure logging and logging functions

#!/usr/bin/env python

import logging
import time
import random
import signal
import sys
import re


# Configure logging
logging.basicConfig(level=logging.DEBUG)  # Set the root logger level to DEBUG


# Create a logger
logger = logging.getLogger(__name__)


# Define log message formats
formats = {
    logging.INFO: "INFO message",
    logging.DEBUG: "DEBUG message",
    logging.ERROR: "ERROR message"
}

# Define log levels to cycle through
log_levels = [logging.INFO, logging.DEBUG, logging.ERROR]


# Define keywords/patterns to search for
error_patterns = ["ERROR", "Exception"]
http_status_codes = [404, 500]  # Example HTTP status codes to monitor


# Function to handle keyboard interrupt (Ctrl+C)
def signal_handler(sig, frame):
    print("\nLogging interrupted. Exiting.")
    sys.exit(0)


# Set signal handler for Ctrl+C
signal.signal(signal.SIGINT, signal_handler)


# Function to count occurrences of keywords/patterns
def count_occurrences(log_entry, patterns):
    count = 0
    for pattern in patterns:
        count += len(re.findall(pattern, log_entry))
    return count


# Main loop to log messages
while True:
    try:
        # Randomly select a log level
        log_level = random.choice(log_levels)

        # Get the log message format for the selected log level
        log_message = formats[log_level]

        # Log the message
        logger.log(log_level, log_message)

        # Read last line of log file
        with open("monitoring.log", "r") as file:
            last_line = file.readlines()[-1].strip()
            print("New log entry:", last_line)

            # Perform basic analysis on log entry
            # Count occurrences of specific keywords/patterns
            error_count = count_occurrences(last_line, error_patterns)
            http_status_count = count_occurrences(last_line, [str(code) for code in http_status_codes])

            # Generate summary reports
            print("Error count:", error_count)
            print("HTTP status code count:", http_status_count)

        # Sleep for a short interval
        time.sleep(1)

    except KeyboardInterrupt:
        # Handle keyboard interrupt (Ctrl+C)
        print("\nLogging interrupted. Exiting.")
        break
