# "Expense"

NO-FRILS text only expense tracker for small business sole proprietorship

### Highlights and features
While being a NO-FRILS text only program *Expense* have some features that do not exits even in commercial high profile accounting programs such as Intuit QuickBooks^TM^. 

- Automatically identify data headers.
- Categorize transaction according to 1040 Schedule C expence caterogies.
- Remember and automatically categorize recurring transcations.
- All data is kept in visible Excel, CSV, or JSON format. No proprietery formats are used.
- Automaticaly detects duplicate transactios by reference number/ID.
- Keeps logs of ALL transactions including transactions that were accepted, skipped or deemd duplicates with their origin so everything can be tracked and verified if needed.
- Automatically copy and save all input files with their sha256 hash.
- Automatically create a local git repository and backup the entire repo with log files and input files. The git repository is updated after each import with a proper log entry so the repository can be restored at input file granularity.
- Normalize all expense amount to positive numbers.

These features, especially detection of duplilcates,nomalization on transaction amount and backup and restore capability can prevent havock cause by overlapping entries, user mistakes, and inconsistent sign convention from different sources.  

### Usage:
"expense.py init \<dir\>"               - initializes an expense repository in folder \<dir\>\
        "expense.py import \<dir\> \<exel\>"    - import transactions from \<exe\> into repository \<dir\>\
        "expense.py report \<name\>             - reserved\
        "expense.py restore \<restore point\>   - reserved

### Example:
"expense.py init 2021"                      - initializes a reposirory names 2021\
         "expense.py import 2021 Jan-stmt-17.xlsx"   - import transactions\

## Description
*Expense* is designed to help small businesses and sole proprioetrs to keep track of their expenses for book keeping and tax relates purposes.

The user first initialize a new repository using the *init* command. This is usually done once a year.

Then the user uses the *import* command imports transactions from excel that were exported sources like: Bank statements, CreditCard Statement, PayPal activity etc.

*Export* imports data from excel files having various headings and automatically detects the columns relared to:
1. **Date**  - date a transaction took place (mandatory).
2. **Reference** - reference number for the transaction (optional)
3. **Payee** - name of the payee (optional*)
4. **Description** - description of the expense (optional*)
5. **Amount** - amount of the transaction

\* Either Payee or Description must be present.

After verifying the detected fields with the user *Expense* starts importing trandactions from input file into the repository.

Every transaction is categorised accorsing to IRS 1040 Schedule C categories according to the transaction signature. 

The user is prompted to select a category for each trasnaction have a never seen before payee or description. *Expense* then remeberes the payee and description and will automatically categorize future transactions from the same payee having the same description. This greatly reduces the burden of adding recurring expenses.

The user can categorise a transaction as "Not applicable" for example non-business expense, or a transaction which is not an expense. This transaction will be skipped and *Expence* will remember and skip future transactions that have the same payee and same description.

The user can also categorize a transaction as oneof the applicable categories, but choose to skip indivdual ones that do not apply for any reason.

The new entry is presented to the user for approval. Before the entry is added to the repo it is checked against the reference ID (date and sum are not good indicators as the same expence can happen twice in one day) before it is added to the repo.

All transaction are logged into separet logs files of accepted, skipped, and dupilcate transaction to allow easy verification and tracability.

Finally, upon use approval:
1. All changes are saved.
2. The input file is copied and its sha256 is added to its name (this prevents name colusion and allow content verification)
3. All changes are added to the local git repository and comitted with a comment deisgnating the transaction inout file name responsible for the update.

### Adding manual entries (e.g. checks)
*Expense* currently does not have a manual entry from the command line. Checks and other "manual" expensed should be added to an excel file with appropriate reference code (e.g. "check:1011") and then fed into the the system, which will save the "manual" input file together with all other input file for ltare restore or verification.

### Generating reports
*Expense* currently does not have built in reports, reports can be generated from a **copy** of the Excel file.

### Restoring from backup
*Expense* currently does not have built in restore function. Restor can be done using the standard **git** interface

### Manually "tweaking" files
Because all files are visible, it is possible to manually "tweak" them. Tweaking should be done cautiously as it can break the system, however some tweaks are quite safe and will be preserved:
1. Adjust the columns width and alignmnet of the exel file for better readability
2. Make the header Bold-face for better readability.

Some changes are more dangerous and should be done with cations. Also these chages alter the content of the repository which may not be inline with the logs. 

1. Change individual values without altering the structire of the file.
2. Delete entry in the excel file
3. Manually add entry to the exel file
4. Manually remore wrong category signature from the category json file.

If you apply any change manually, it is recommended to update the git manually with a proper comment describing the change. 

## TODO
1. Set column width and alignmnet automatically.
2. Make the header Bold
3. Add reports - especially expense report by category
4. Add charts (pie chart)?
5. Add GUI wrapper (within browser)?
