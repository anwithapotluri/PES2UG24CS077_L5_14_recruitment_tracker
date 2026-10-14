| Issue | Type | Line(s) | Description | Fix Approach |
| ----- | ---- | ------- | ----------- | ------------ |
| SQL injection | Security, Medium (Bandit B608, CWE-89) | 8 | addApplicant builds the INSERT with an f-string, so input like x'); DROP TABLE applicants;-- can alter the query | Use a parameterized query with ? placeholders |
| Use of eval | Security, Medium (Bandit B307, Pylint W0123) | 14 | eval(salary_rule) executes arbitrary code passed by the caller | Remove eval; pass the salary limit as a number |
| Broad exception swallowed | Bug, Low (Bandit B110, Pylint W0718) | 27-29 | except Exception: pass hides every failure, so a failed update looks successful | Catch sqlite3.Error and log it |
| Inconsistent return | Bug (Pylint R1710) | 12-21 | shortlist returns a list or None, so callers must special-case None | Always return a list, empty if no match |
| File not closed safely | Resource leak (Pylint R1732, W1514) | 32-35 | open() without with leaks the file if an error occurs, and no encoding is set | Use with open(..., encoding="utf-8") |
| Unused variable | Quality (Flake8 F841, Pylint W0612) | 15 | total_seen is assigned but never used | Remove it |
| Naming style | Style (Pylint C0103) | 7, 24, 31, 38 | Functions use camelCase instead of snake_case | Rename to add_applicant, update_status, export_report, count_by_status |
| Docstrings, long lines, spacing | Style (C0114, C0116, E501, E302, W291) | multiple | Missing docstrings, lines over 79 chars, missing blank line before exportReport, trailing whitespace | Add docstrings, wrap lines, fix spacing |
