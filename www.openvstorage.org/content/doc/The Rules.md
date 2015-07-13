### The Rules

### Introduction

It's easy enough to figure out most (if not all) of the conventions
after spending some time with the code. But it's even easier to read a
style guide before committing any code. That is why we have this "The
Rule" section. Here you can find a [GUI Style Guide]( {{< relref "The Rules.md" >}}#guistyleguide ). and
an overview of [terms to use and their explenation]( {{< relref "The Rules.md" >}}#termstouse ).


<a name="guistyleguide" class="internal-ref"></a>
### GUI Guide Lines

### Numbers in the GUI

#### IOPS

-   (round number): (xxx,xxx,xx)x
-   Some examples:
    -   5 -\> 5
    -   125 -\> 125
    -   1255 -\> 1,255
    -   123456789 -\> 123,456,789

#### Bandwidth

-   (xx)x.xx kiB/s, MiB/s, GiB/s, TiB/s
-   Some examples:
    -   5 -\> 5.00 kiB/s
    -   123.54 -\> 123.45 MiB/s

#### Stored Data

-   (xx)x.xx KiB, MiB, GiB, TiB
-   Some examples:
    -   5 -\> 5.00 kiB
    -   123.54 -\> 123.45 MiB

#### Cache Hit Ratio, Free Space

-   (xx)x.xx %
-   Some examples:
    -   5 -\> 5.00 %
    -   93.23 -\> 93.23 %
    -   100 -\> 100.00 %

### Networking

-   IP: (xx)x.(xx)x.(xx)x.(xx)x
-   MAC address: xx:xx:xx:xx:xx:xx
-   Some examples:
    -   1.1.1.1
    -   10.100.187.100
    -   01:23:45:67:89:ab

<a name="termstouse" class="internal-ref"></a>
### Terms to use and their explanation

Below are a list of term which can be used in the code to reference
certain standardized concepts.

WARNING: UNSUPPORTED DOC, TABLES NOT SUPPORT YET.

Term: should be used in comments, documentation and HTML/GUI Pyhton
Code: should be in python code Javascript Code: should be used when
writing javascript, css Explanation: can be used to explain the Term in
code, documentation or the GUI