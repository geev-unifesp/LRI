# LRI
Light Regularity Index calculation (actimetry)
markdown
# Light Regularity Index (LRI) Calculator

This script calculates the **Light Regularity Index (LRI)** from light exposure data collected at 1-minute epochs. The LRI measures how consistently an individual is exposed to similar light levels at the same time each day.

## Overview

The LRI quantifies the probability that light levels at any given minute are on the same side of a threshold (e.g., above or below 10 lux) as they were exactly 24 hours earlier. Values range from 0 (completely irregular) to 100 (perfectly regular).

## Features

- Processes multiple `.txt` files from a specified folder
- Automatically skips header lines (first 29 rows)
- Handles `;` delimited files with ISO-8859-1 encoding
- Filters data to include only complete days (00:00 to 23:59)
- Resamples data to ensure 1-minute intervals
- Calculates LRI for multiple light intensity thresholds:
  - 10, 20, 50, 100, 300, and 500 lux
- Limits analysis to the last 15 days of data per file
- Exports results to an Excel file (`.xlsx`)

## Requirements

Install the required Python packages:

```bash
pip install numpy pandas openpyxl
openpyxl is required for Excel file export.

Input Format
The script expects .txt files with:

First 29 rows: Header/skip lines (automatically ignored)

Delimiter: ; (semicolon)

Encoding: ISO-8859-1

Required columns: The script assigns the following column names (20 columns total):

Index	Column Name	Description
0	DATE/TIME	Timestamp (e.g., 01/01/2023 00:00:00)
12	LIGHT	Light intensity value (lux)
Others	col1 to col20	Additional columns (ignored in calculation)
Example of expected data structure:
text
DATE/TIME;col1;col2;col3;col4;col5;col6;col7;col8;col9;col10;col11;col12;LIGHT;col14;col15;col16;col17;col18;col19;col20
01/01/2023 00:00:00;...;...;...;...;...;...;...;...;...;...;...;...;10.5;...;...;...;...;...;...;...
01/01/2023 00:01:00;...;...;...;...;...;...;...;...;...;...;...;...;12.3;...;...;...;...;...;...;...
Output
The script generates an Excel file named LRI_results.xlsx in the same folder as the input files.

Output Structure:
Column	Description
Filename	Name of the processed .txt file
LRI_10	LRI value for 10 lux threshold
LRI_20	LRI value for 20 lux threshold
LRI_50	LRI value for 50 lux threshold
LRI_100	LRI value for 100 lux threshold
LRI_300	LRI value for 300 lux threshold
LRI_500	LRI value for 500 lux threshold
Example Output:
Filename	LRI_10	LRI_20	LRI_50	LRI_100	LRI_300	LRI_500
participant1.txt	72.34	68.21	55.67	48.92	35.41	28.15
participant2.txt	81.05	79.33	74.28	65.10	52.67	41.23
How LRI Is Calculated
Binary transformation: Light values are converted to binary signals (0 or 1) based on whether they exceed a given threshold (e.g., > 10 lux = 1, ≤ 10 lux = 0)

Day-to-day comparison: For each minute of the day, the script compares the binary state with the state exactly 24 hours later

Probability calculation:

text
Probability (same state) = Number of matching comparisons / Total valid comparisons
LRI formula:

text
LRI = max(0, min(100, (probability - 0.5) × 200))
Probability = 0.5 (random chance) → LRI = 0

Probability = 1.0 (perfect consistency) → LRI = 100

Usage
1. Update the input folder path
Open the script and modify the input_folder variable:

python
input_folder = '/path/to/your/txt/files/'
2. Run the script
bash
python LRI_calculator_final.py
3. Check the output
The results will be saved as LRI_results.xlsx in the same folder as your input files.

Customization
Change the number of days to analyze
Modify this line (default is 15 days, using the last N days):

python
days_to_analyze = min(num_days, 15)  # Change 15 to desired number
Change the light thresholds
Modify the thresholds list:

python
thresholds = [10, 20, 50, 100, 300, 500]  # Add or remove values as needed
Change the output file name or location
Modify the output_file variable:

python
output_file = os.path.join(input_folder, 'custom_name.xlsx')
Adjust the number of skipped rows
If your files have a different header structure, change the skiprows parameter:

python
df = pd.read_csv(file_path, delimiter=';', skiprows=29, ...)  # Change 29 as needed
Error Handling
The script handles the following issues gracefully:

Invalid timestamps: Rows with unparsable DATE/TIME values are dropped

Missing light values: Filled with 0 lux

Incomplete days: Data outside 00:00-23:59 are filtered out

Missing 00:00 or 23:59: The script still processes available data

Less than 1440 minutes per day: Missing minutes are padded with NaN values

Fewer than 2 days of data: LRI calculation will return values but may be unreliable

Limitations
Assumes data are collected at 1-minute intervals (resampling to 1 minute is applied)

Only analyzes the last N days (default: 15) of each recording

Requires at least 2 days of data for meaningful LRI calculation

Does not account for time zones or daylight saving time transitions

Light threshold values are hardcoded (modify as needed)

First 29 rows are skipped — adjust if your file structure differs

Interpretation of LRI Values
LRI Range	Interpretation
80–100	Highly regular light exposure patterns
60–80	Moderately regular
40–60	Slightly regular / near random
20–40	Moderately irregular
0–20	Highly irregular
Note: LRI values near 0 indicate that light exposure patterns are essentially random relative to the 24-hour cycle.

Complete Workflow Example
Place your .txt files in a folder, e.g.:

text
/Users/XXX_txt/
Update the script with your folder path

Run the calculator:

bash
python LRI_calculator_final.py
Find your results in the same folder:

text
/Users/Your folder.xlsx
Author
Developed for analyzing light exposure data from actigraphy/light sensors.

License
Free to use and modify as needed.
