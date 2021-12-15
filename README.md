# *TinyExpense* - NO-FRILLS expense tracker

NO-FRILLS text only expense tracker for a small business or sole proprietorship

This program only does one thing - process transactions from excel files
such as bank transactions, credit card transactions, and PayPal transactions,
Categorize them by IRS 1040 Schedule-C categories, and keep tracking of everything 
using continuous backups at a single import granularity.


### Highlights and features
- Automatically identify data headers.
- Categorize transactions according to 1040 Schedule C expense categories.
- Remember and automatically categorize recurring transactions.
- All data is kept in visible Excel, CSV, or JSON format. No proprietary formats are used.
- Automatically detect duplicate transactions by reference number/ID and sum (only when a reference number exists)
- Keep logs of ALL transactions, including accepted, skipped, and duplicates with their origin, so everything can be tracked to its source and verified if needed (journal).
- Automatically copy and save all input files with their sha256 hash.
- Automatically create a local git repository and backup the entire repo with log files and input files. The git repository is updated after each import with a proper log entry so the repository can be restored at input file granularity.
- Normalize expense amount to positive numbers.

These features, especially detection of duplicates, normalization of the transaction amount, and backup and restore capability, can prevent havoc caused by overlapping entries, user mistakes, and inconsistent sign conventions from different sources.


### Usage:
"expense.py init \<dir\>"               - initializes an expense repository in folder \<dir\>\
        "expense.py import \<dir\> \<exel\>"    - import transactions from \<exe\> into repository \<dir\>\
        "expense.py report \<name\>             - reserved\
        "expense.py restore \<restore point\>   - reserved

### Example:
"expense.py init 2021"                      - initializes a repository named 2021\
         "expense.py import 2021 Jan-stmt-17.xlsx"   - import transactions

## Description
The user first initializes a new repository using the *init* command. Initialization is usually done once a year.

Then the user uses the *import* command to import transactions from excel files such as exported Bank statements, credit card statements, PayPal activity, etc.

*TinyExpense* imports data from excel files having various headings and automatically detect the columns related to:
1. **Date**  - date a transaction took place (mandatory).
2. **Reference** - reference number for the transaction (optional)
3. **Payee** - name of the payee (optional*)
4. **Description** - description of the expense (optional*)
5. **Amount** - amount of the transaction

\* Either Payee or Description must be present.

**Note:** Header should currently be in the first line. 

After verifying the detected fields with the user, *TinyExpense* starts importing transactions from the input file into the repository.

According to the transaction signature, recurring transactions are categorized according to IRS 1040 Schedule C categories.

The user is prompted to select a category for each transaction that has a never seen before payee or description. *TinyExpense* then remembers the payee and description and will automatically categorize future transactions from the same payee having the same description. This significantly reduces the burden of adding recurring expenses.

The user can categorize a transaction as "Not applicable," for example, non-business expense or a transaction that is not an expense. This transaction will be skipped, and *TinyExpense* will remember and skip future transactions with the same payee and exact description.

The user can also categorize a transaction as one of the applicable categories but choose to skip individual ones that do not apply for any reason.

The new entry is presented to the user for approval. Before the entry is added to the repo, it is checked against the reference ID and sum for duplicate before it is added to the repo. Duplicate checks are done only on transactions that have a reference ID because the rest
may be a legitimate identical transaction.

All transactions are logged into separate logs files of: accepted, skipped, and duplicate transactions to allow easy verification and traceability.

Finally, upon user approval:
1. All changes are saved.
2. The input file is copied, and its sha256 is added to its name (this prevents name collision and allow content verification)
3. All changes are added to the local git repository and committed with a comment designating the transaction input file name responsible for the update.

### Adding manual entries (e.g. checks)
*TinyExpense* currently does not have a manual entry from the command line. Checks and other "manual" expensed should be added to an excel file with appropriate reference code (e.g., "check:1011") and then fed into the system, which will save the "manual" input file together with all other input files for later restoration or verification.

### Generating reports
*TinyExpense* currently does not have built-in reports; reports can be generated from a **copy** of the Excel file.

### Restoring from backup
*TinyExpense* currently does not have a built-in restore function. Restore can be done using the standard **git** interface
for example, "git reset --hard HEAD^" will **permanently* delete the latest import. When a backup is restored this way - everything
is restored, including logs and categories.

### Manually "tweaking" files
Because all files are visible, it is possible to manually "tweak" them. Tweaking should be cautiously done as it can break the system. However, some tweaks are pretty safe and will be preserved:
1. Adjust the width and alignment of the columns of the excel file for better readability
2. Make the header Bold-face for better readability.

Some changes are more dangerous and should be done with cations. Also, these changes alter the repository's content, which may not be in line with the logs. 

1. Change individual values without altering the structure of the file.
2. Delete an entry in the excel file
3. Manually add an entry to the excel file
4. Manually remove a wrong category signature from the category JSON file.
5. Reverting only part of a commit (e.g., revert the workbook but keep the categories).

If you apply any change manually, updating the git manually with an appropriate comment describing the change is recommended.

If you mistakenly break the system, the system can be restored from **git**.

### For Developers
- *TinyExpense* uses "openpyxl," which provides tools that can add charts (e.g., pie charts) to the workbook and can be used to create reports
   This is simply not currently implemented.
- *TinyExpense* does not do *any* data manipulation except sign change when needed. If data manipulation is added, it needs to be done using decimal arithmetic as explained here: [Decimal fixed point and floating-point arithmetic](https://docs.python.org/3/library/decimal.html)
- *TinyExpense* UI is spartan... good enough for me to work with, but can be significantly improved even if using TUI.
- The detection of header title uses fixed (hardcoded) values. Some flexibility may be helpful, although I am not sure it should be as flexible as categories.
- Git is done via shell. Python have libraries for git that can allow more functionaly. 
- Import of CSV files would be helpful (not supported by openpyxl). I am currently using gnumeric ssconvert to convert csv to xlsx files manually.
- Categories are now set to 1040 schedule C, it is just a matter of replaceing or adding a selection to adjust the code for peronal/home use.  A better UI and reports/charts are definetly needed in this case.

