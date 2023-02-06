# Dated, a `date` on steroids
## ZSH exoskeleton for the command line "date" utility
### *Expand the ability of date to handle natural language time references (in English)*

The command line utility `date` from [GNU coreutils](https://www.gnu.org/software/coreutils/manual/html_node/date-invocation.html) is familiar to everyone who uses the Linux command line.
When used with the command line flag `-d` it can parse strings which contain properly formatted datetime constructs and if successful, output the resulting datetime to the standard output, formatted as per additional formatting flags if supplied.

So `date -d"next thursday 8:30 pm"` will output something like "Thu 09 Feb 2023 08:30:00 PM"

**Question: Can it be made much more "intelligent" by wrapping it in a command line preprocessor, based only on the built-ins available to the Linux shell (for example bash, better yet zsh)?**
In other words, can it not explode if the string was instead: `"Two years from now, on halloween, at 730 in the morning"`?

This repository is an attempts to answer **Yes**  to the above question. The `dated` command line utility is written in and for `zsh` and uses zsh built-in machinery (almost exclusivelly) before handing the preprocessed text to `date -d`.   So, if called at the time of this writing, 
```
dated "Two years from now, on halloween, at 730 in the morning"
```  
will respond with `Fri 31 Oct 2025 07:30:00 AM EDT` ,

or even for the floating Thanksgiving or Easter:
```
dated "on Thanksgiving day in 2040 at 8 in the evening" 
```
the output will be `Thu 22 Nov 2040 08:00:00 PM EST` 
Please, see the help `dated -h or date d --help` for more examples.
```
 SYNOPSIS: dated [--help|-h|] ...<string> [formating options of date]

   - 'dated <string>' Parse <string> to a valid time reference and send to 'date -d'
   - 'dated -h|--help' will print this text
   - any and all additional arguments are passed as-is to 'date -d' to control formating 

The text in <string> is parsed and formated into a valid datetime input for 'date -d'.
It is quite difficult for computers to parse our spoken time references and using only built-in tools
(i.e. date -d from coreutils) presents a huge challenge when parsing arbitrary datetime text.
There are dedicated, complex NLP tools that work better but they are not perfect either.

EXAMPLES:
"Set for Tuesday"       - this is valid.
"for 2023/5/24 at 8pm"   - also OK.
"March the 3rd in the evening."  - is OK
"on March 16 at 7 in the morning in a couple of years" - OK
"on New years eve, 10 years from now" - OK
"two years from now on halloween at 730 in the morning"  - works too
"3rd of March 2024 at 23 hours 13 minutes" - OK
       ( "...for next week"
Also   | "...in 3 hours"
valid: { "...tomorrow morning"  (see source code for "morning" & other adjustable definitions)
       | "...in 33 hours and 5 minutes"
       ( "...January 23 quarter past 7 in the evening
Custom:  "...at the usual time" allows privacy and customization (see code for ideas)
In some edge cases, successful parsing gives incorrect datetime. Some practice needed to avoid those
```

## But WHY?

The problem this was built to solve can be easily explained by looking at [Spoken](https://github.com/QuantiusBenignus/Spoken) a set of zsh scripts to record Joplin text notes and to-do's via speech, from the microphone. The `td` utility in the repository records audio from the microphone, transcribes it to text using whisper.cpp, a derivative of Open AI's Wisper and then parses the transcribed, free-form text for a datetime reference so that it can set an automatic notification alarm for the Joplin to-do task.  This is performed by using the code functionality of `dated`. 
