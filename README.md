# mmexutil
Utilities for [Money Manager Ex (MMEX)](https://moneymanagerex.org) database

## Features

- Export to XLSX file
- Import from XLSX file
- Print reports

## Installation

Install required perl modules:
  ```
  cpan install DBI
  cpan install Excel::Writer::XLSX
  cpan install Spreadsheet::ParseXLSX
  ```

Download `mmexutil`, make it executable, and add it into your path.

## Usage

Show program usage:
  ```
  mmexutil -h
  ```

Show usage for output to an XLSX file:
  ```
  mmexutil -o -h
  ```

Show usage for input from an XLSX file:
  ```
  mmexutil -i -h
  ```

Export `test1.mmb` to `test1.xlsx` (all worksheets, all ids):
  ```
  mmexutil test1.mmb -o test1.xlsx -sa -ci
  ```

The output (`-o`) command extracts the whole database to an XLSX file.
Each XLSX worksheet contains one main MMEX table, and possibly
other related tables, or parts of auxiliary tables.
All MMEX database ids are translated from a numeric value to a key symbol or name.
XLSX columns containing numeric ids are disabled (not generated) by default;
they can be enabled with the `-ci` flag.

The output XLSX file without id columns, is in principle an exact representation of
the MMEX database, except for the id values, i.e., the original MMEX database can
be reproduced from the XLSX file, possibly with different id values. Notice that
database ids are used as links, in order to define relationships between records,
however, the numeric value of an id itself is not important.

Import `test1.xlsx` to `test2.mmb`
(all worksheets, update add, add all required records):
  ```
  mmexutil test2.mmb -i test1.xlsx -sa -ua -ra
  ```

The input (`-i`) command imports data from an XLSX file into the MMEX database, by
1. adding new records
2. updating existing records
3. adding required records

(2) and (3) can be performed only on tables with identifiable records,
i.e., on tables with a key, other than the rowid.
The database ids are abstracted; even if they are present
in the XLSX file, they are ignored by the input command.
For tables without a key, all entries in XLSX file are added as new records,
therefore an export to an XLSX file followed by an import into the same MMEX file,
generates duplicate records in these tables.

Many MMEX tables have a unique key. For a few other tables, a key is assumed, although
it is not declared as unique in the database. The keys of each table can be listed
using the `-i -htk` flags.
If records with the same non-unique key are found, an error is printed and the import
is aborted. The assumed keys can be disabled with the `-ku` flag,
in which case all XLSX entries are added as new (possibly duplicate) records
in tables without a key.

Two important tables do not have a key (either unique or non-unique):
- `CHECKINGACCOUNT_V1` (transactions)
- `BILLSDEPOSITS_V1` (scheduled transactions)

An export to an XLSX file followed by an import into the same MMEX file,
would duplicate all transactions and all scheduled transactions,
unless their import is disabled, or they are removed in either the MMEX
or the XLSX file before import.

Show usage of print command:
  ```
  mmexutil -p -h
  ```

Print all keys in `test.mmb` (currencies, accounts, categories, payees, etc.):
  ```
  mmexutil test.mmb -p -sa
  ```

Print payees in `test.mmb`:
  ```
  mmexutil test.mmb -p -sl p
  ```

Print cash flow per calendar year in `test.mmb` (all years):
  ```
  mmexutil test.mmb -p -cy ..
  ```

Print cash flow per month in `test.mmb` (all months in current year):
  ```
  mmexutil test.mmb -p -cm .-1..12
  ```

The cash flow contains the sum of transactions (in the past)
and scheduled transactions (in the future), grouped by currency.
Money transfers in the same currency and within the set of selected
account types (all types by default), are not shown in the sums.

## License

Distributed under the BSD 3-Clause License.
See `license` fold at the beginning of `mmexutil`.

