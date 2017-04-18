# Sed

## Resources

* [http://www.grymoire.com/Unix/Sed.html](http://www.grymoire.com/Unix/Sed.html)


## Cheat-sheet

	# Only substitute lines n through [m]
	sed 'n[,m] s/PATTERN/REPLACEMENT/'

	# Only substitute n though EOF
	sed 'n,$ s/PATTERN/REPLACEMENT/'

	# Only substitute lines matching REGEXP
	sed '/REGEXP/ s/PATTERN/REPLACEMENT/'

	# Only substitue lines in range of START and STOP (inclusive)
	sed '/START/,/STOP/ s/PATTERN/REPLACEMENT/'

	# Use line numbers and REGEXP to select range
	sed '1,/START/ s/PATTERN/REPLACEMENT/'

	# Delete command 'd'
	sed '/PATTERN/ d'

	# Print command 'p' (only print lines matching PATTERN')
	sed -n '/PATTERN/p'

	# Quit command - quit after first 10 lines
	sed '10 q'

	# Append line(s) after pattern match
	sed '/PATTERN/ a\
	line to append'

	# Insert line(s) before pattern match
	sed '/PATTERN/ i\
	line to insert'

	# Change line for new one
	sed '/PATTERN/ c\
	new line'

	# Transform pattern, the 'y' command
	sed 'y/<CHAR1><CHAR2>/<REPLACEMENT1><REPLACEMENT2>/
	echo 'aaaabbb' | sed 'y/ba/BA/'

	# Invert selection with '!'
	sed '/PATTERN/ !d'
	sed 'n[,m] !d'

	# Multiple expressions, evaluated left to right
	sed -e 'expr 1' -e 'expr 2'


