# Code Samples

Three scripts are a part of a Flask website built to help traders manage risk in derivitives trading. The first script, **_oi_to_db_.py** downloads and formats open interest data (once a day on scheduler) for CME futures and stores it in a database for other apps to use. The next two scripts **_algo.py_** and **_summary.py_** are related to each other. The former is a tool to create and test trading logic for each contract while the latter summarizes these data files into categories to
ensure that they are properly monitored.
The main tools used are Python, Flask, Plotly Dash, SQLAlachemy, Celery.

## Scripts

### Open Interest To Database (oi_to_db.py)

This script downloads open interest data from the CME website and uploads it to a SQL database. This is a daily processes that runs every morning (using task scheduler Celery) and other apps on the website use this data for analysis.


### Trading Strategy Summary (summary.py)

This script summarizes the different trading strategies created in **_algo.py_** to track and modify as required. On page load/refresh the script reads in an updated list of contracts for which a trading strategy has been created and also reads in current trading positions. The data is then manipulated to generate four lists which are:
- **Recently Updated**
This lists files in descending order of timestamp, upto 10 days.
- **Position On/Missing Algo**
This list keeps track of contracts which are currently being traded but do not have a defined trading strategy.
- **Algo Exists/No Position On**
This list keeps track of contracts for which trading strategy has been defined but it is not currently being traded.
- **Position On/Incomplete Algo**
This lists files for which there is logic defined but it is incomplete. Only lists contracts which are currently being traded.

### Trading Logic (algo.py)

This script defines an interactive Dash app with various HTML components. The HTML layout is defined by a Dash app and callbacks are used to take care of interactions with the components. The purpose of this tool is to define trading logic for adding on a position as well as unwinding that position, based on the parameters set by the end user.

This script mainly shows the architecture of the app. A lot of the logic is hidden in this script as it is proprietary.

The app structure is as follows:
1) **HTML layout**
- *Main Table*: Selecting a cell will show data for that month/year and type-of-relationship combination of selected product.
- *Stats Table*: Table displaying statistical data for selected contract (updated once daily through a separate process).
- *Heuristic Table*: Editable table that takes in user defined parameters like max buy price, min buy price, max position to define trading logic
- *Notes*: Editable component for user to add notes.
- *Adding Position Table*: Shows chart (qty to be bought/sold at each price level) for buy side/sell side logic.
- *Unwinding Position Table*: Shows chart (qty to be sold back/bought back at each price level) for sell back side/buy back logic.
- *Summarized Table*: Shows summarized version of adding/unwinding logic. Also includes a lookup feature to get exact trading logic at a given price level.
- *Google Calendar Invite*: Select date and provide title to send a Google Calendar invite using Google API.
- *Archive Dropdown*: Gives access to expired contracts that no longer show up on the main table. Helps with postmortem analysis of trades.
    
2) **Callbacks**
- create_maintbl_actvcell(): Reads product off URL and accordingly defines the main table using the values defined in p_dict.
- set_mmyy_dropdown(), set_relationship_dropdown(): Reads in files from expired contracts to generate options on these dropdowns.
- get_contract(): Sets the current contract either from selected active cell from main table or from archive dropdown
- show_settle_data(): Displays contract specific stats from file generated by another process.
- generate_heuristic_table(): Takes in current contract and loads file with user-defined parameters. If file doesn't exist, initialize a new file.
- generate_notes_table(): Takes in current contract and loads file with notes. If file doesn't exist, initialize a new file.
- calendar_invite(): Takes in user-inputted date, title and sends email using Google API.
- generate_tables_charts(): This callback does all the calculations for trading logic and returns data for Summarized Table, Adding Table and Unwinding Table.
3) **Helper Functions**
4) **HTML Definitions**

