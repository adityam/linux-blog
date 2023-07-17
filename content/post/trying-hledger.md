---
title     : "Trying out hledger"
date  : 2023-07-15
tags  :
  - plain text accounting
  - hledger
categories :
  - CLI
---

As an academic, I need to track research funds: what is the remaining balance of each grant, how much I have committed for student stipends, what are anticipated expenditures for conference travel, etc. Like everyone else, I used to do this via a spreadsheet, but my spreadsheet was becoming a bit too complicated. So, I decided to try plain text accounting, and use [hledger][hledger] to keep tracks of research funds.

[hledger]: https://hledger.org/

<!-- more -->

`hledger` may be considered as a Haskell port of [ledger](https://ledger-cli.org/), though there are a few subtle [differences](https://hledger.org/ledger.html) between the two. 

For my purposes, I created a `grants.journal` file which looks something like this:

* First set the formatting options. I want to use commas as thousand separators and am happy to round amounts to the nearest dollar.

    ```
    decimal-mark .
    commodity 1,000. CAD
    ```

* Then define accounts for each grant and each category of expense. I also create a new `eXpense` category called `stipend` so that I can type `stipend:student1` instead of `expenses:stipend:student1`. Strictly speaking, I don't need to define these accounts as they can be inferred from the journal file, but it helps with autocomplete etc.

    ```
    account assets:grant1
    account assets:grant2
    account assets:grant3

    account expenses:conferences
    account expenses:memberships
    account expenses:supplies

    account stipend:student1; type: X
    account stipend:student2; type: X
    account stipend:student3; type: X
    account stipend:student4; type: X
    ```

* Then create opening balances for each grant at the beginning of the financial year.

    ```
    2023-05-01 Opening balances
      assets:grant1     40,000 CAD
      assets:grant2     60,000 CAD
      assets:grant3     52,000 CAD
      equity:opening/closing balances
    ```

* Next add the yearly allocation for the different grants, which may occur and different dates. Moreover, the grants might have different duration, so some finish earlier than others.

    ```
    2024-05-01 Yearly grant allocations
      assets:grant1     40,000 CAD
      assets:grant2     40,000 CAD
      equity:grants

    2024-06-01 Yearly allocation (at a different date)
      assets:grant3     40,000 CAD
      equity:grants


    2025-05-01 Yearly grant allocations
      assets:grant2     40,000 CAD
      equity:grants

    2025-06-01 Yearly allocation (at a different date)
      assets:grant3     40,000 CAD
      equity:grants
    ```

    In principle, it is possible to cature this with _periodic entries_, but in my experiments, periodic entries don't play well with `hledger-ui`, so I decided not to use them and manually add an entry for each year.

* Then indicate the yearly stipends of students. Note that some students (like `Student 1` in the example below) receive partial funding from the university and I only pay part of the stipend. Others are co-supervised, so I pay half the stipend (like `Student 2` below). 
    ```
    2023-09-01 Student 1 Fellowship Stipend
      stipend:student1   20,000 CAD
      assets:grant2

    2024-09-01 Student 1 Fellowship Stipend
      stipend:student1   20,000 CAD
      assets:grant2

    2025-09-01 Student 1 Fellowship Stipend
      stipend:student1   20,000 CAD
      assets:grant2

    2026-09-01 Student 1 PhD Stipend
      stipend:student1   30,000 CAD
      assets:grant2

    2027-09-01 Student 1 PhD Stipend
      stipend:student1   30,000 CAD
      assets:grant2

    2023-09-01 Student 2 Fellowship Stipend (co-supervised)
      stipend:student2   10,000 CAD
      assets:grant1

    2024-09-01 Student 2 Fellowship Stipend
      stipend:student2   10,000 CAD
      assets:grant1

    2025-09-01 Student 2 Fellowship Stipend
      stipend:student2   10,000 CAD
      assets:grant1

    2026-09-01 Student 2 PhD Stipend
      stipend:student2   15,000 CAD
      assets:grant1

    2027-09-01 Student 2 PhD Stipend
      stipend:student2   15,000 CAD
      assets:grant1

    2023-09-01 Student 3 PhD Stipend (had started in 2021, no fellowship)
      stipend:student3   30,000 CAD
      assets:grant3

    2024-09-01 Student 3 PhD Stipend
      stipend:student3   30,000 CAD
      assets:grant3

    2025-09-01 Student 3 PhD Stipend
      stipend:student3   30,000 CAD
      assets:grant3
    ```

    This is verbose. In principle, some of it can be shortened by using periodic transactions, but I had some issues in using periodic transactions in `hledger-ui`. Since this needs to be updated only once or twice a year, I don't mind the verbose format. 

* Similarly, add expenses for conferences:

    ```
    2023-05-24 Conference 1
      expenses:conferences:conf1:travel     500 CAD
      expenses:conferences:conf1:hotel      800 CAD
      expenses:conferences:conf1:food       200 CAD
      assets:grant3 

    2023-12-15 Conference 2
      ! expenses:conferences:conf2:travel     1,500 CAD
      ! expenses:conferences:conf2:hotel      1,200 CAD
      ! expenses:conferences:conf2:food         300 CAD
      ! assets:grant1 
    ```

    Note that the second conference is something in the future, so I use `!` to indicate that it is a _pending_ transaction. This helps in filtering later.

That's it. I also have similar entries for `expenses:supplies` and `expenses:memberships`. For the sake of brevity, I'll not go into the detail for those. 

To view the journal file, I use `hledger-ui`. I have the following in my `.zshrc` file:

```
alias hl="hledger-ui --tree --depth=2 --cost --empty --period='2023' --change --forecast -f blog.journal --all not:equity"
```

This launches `hledger-ui` to view the file `blog.journal` (which we created above) for the period of 2023. The other flags change the defaults of `hledger-ui` to my preference.

Here is a screencast of `hledger-ui` in action:

<script async id="asciicast-597308" src="https://asciinema.org/a/597308.js" data-startAt="15.0"></script>
