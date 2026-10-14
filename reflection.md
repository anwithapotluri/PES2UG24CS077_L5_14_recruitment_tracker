# Reflection



## 1. Which issues were the easiest to fix, and which were the hardest? Why?



The easiest were the style issues: renaming camelCase functions to snake_case, adding docstrings, wrapping long lines, removing the unused variable total_seen, and fixing the blank lines and trailing whitespace. These are mechanical and do not change behaviour. Replacing the unsafe open()/close() with a "with" block and adding encoding="utf-8" was also quick.



The hardest was removing eval() in shortlist(). It was not a one-line change, because the function's interface had to change. The original took a string like "500000 + 150000" and evaluated it. Bandit suggested ast.literal_eval, but that would not work here because it cannot evaluate arithmetic expressions. The real fix was to pass the salary limit as a number, which also meant updating the caller in main(). The SQL injection fix was moderately hard because I had to understand why the f-string was dangerous and rewrite the INSERT with ? placeholders. Fixing the silent "except Exception: pass" also needed a decision about what to catch (sqlite3.Error) and how to report it (logging).



## 2. Did the static analysis tools report any false positives? If so, describe one example.



I did not find a clear false positive in this file. Every serious finding (B608 SQL injection, B307 eval, B110 try/except/pass, and Pylint's R1710 inconsistent return) pointed at a real problem. The closest case was Bandit's B307 suggestion to use ast.literal_eval. The warning itself was correct, but the suggested fix would not work for this code because literal_eval cannot evaluate "500000 + 150000", so the advice needed human judgement. Another mismatch was that Pylint's line-length limit is 100 while Flake8's is 79, so the same line was flagged differently by each tool. That is a configuration difference, not a false positive, but it shows that tool output should be reviewed instead of followed blindly.



## 3. How would you integrate static analysis tools into your actual software development workflow?



I would run the tools at several stages. Locally, I would use pre-commit hooks so Flake8, Pylint and Bandit run before every commit and catch problems early. In the editor, I would enable linting so issues show as I type. In CI, I would add a GitHub Actions workflow that runs all three tools on every pull request and blocks the merge if Bandit finds medium or high issues or the Pylint score drops below a threshold such as 9.5. I would also keep a shared configuration file (for example setup.cfg or .pylintrc) so all developers use the same rules and line length. Findings should still be reviewed by a person, and any deliberate exception should be suppressed with a comment explaining why.



## 4. What tangible improvements did you observe in the code quality, readability, or potential robustness after applying the fixes?



The Pylint score went from 4.47/10 to about 10/10, Flake8 went from 8 warnings to none, and Bandit went from 3 issues to none. Security improved the most: user input can no longer change the SQL statements, and arbitrary code can no longer be executed through eval. Robustness improved because shortlist() now always returns a list (empty if there are no matches) instead of sometimes returning None, update_status() now logs database errors instead of hiding them, and the CSV file is always closed properly even if an error occurs. Readability improved through consistent snake_case names, docstrings, shorter lines, and a main guard (if __name__ == "__main__") so the module can be imported without running the demo.
