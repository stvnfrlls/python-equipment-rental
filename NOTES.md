# NOTES.md
 
## One fix explained: the PHP 0.00 total bug

The `rental_days` calculation used `(to_date - from_date).days`, which counts nights between two dates rather than the actual billed days. Because rentals are billed inclusively, a same-day rental (where from_date equals to_date) showed 0 days and charged PHP 0.00. Short rentals were underbilled by one day for the same reason. The fix adds 1 to the calculation: `(to_date - from_date).days + 1`. Now, a same-day rental bills for 1 day, and a 3-day rental (e.g., Jan 10 to Jan 12) correctly bills for 3 days instead of 2.

## Double-booking failure example

Original booking: Canon DSLR Camera, Jan 10 to Jan 15.
New booking the old code wrongly allowed: Jan 5 to Jan 12 (starts before the existing booking but overlaps Jan 10 to 12). The old `dates_overlap` only checked if the new booking's start date fell inside the existing range, so this slipped through. The fixed version correctly blocks it.

## AI use
 
I used Claude to help debug and explain each bug (double-booking overlap logic, the rental_days billing calculation, and the maintenance-status booking rule), and to review/patch the frontend JS for live total updates. For each fix, I checked correctness by tracing through concrete example dates by hand (e.g. same-day rental, overlapping ranges, ranges that fully contain an existing booking) and confirming the math and logic matched the stated business rules before accepting the change.