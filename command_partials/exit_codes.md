
### expect_exit_codes (optional)

| Key               | Allowed Values                 | Default | Since Version |
|-------------------|--------------------------------|---------|---------------|
| expect_exit_codes | 'All', Fixnum, Array of Fixnum | 0       | 0.1           |

The exit codes DOPi should expect if the command terminates. If the program
exits with an exit code not listed here, DOPi will mark the run as failed.
The values can be a number, an array of numbers or 'all' for all possible exit
codes. This will replace the current default.

