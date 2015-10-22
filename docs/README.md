dnstwist
========

See what sort of trouble users can get in trying to type your domain name.
Find similar-looking domains that adversaries can use to attack you. Can detect
typosquatters, phishing attacks, fraud and corporate espionage. Useful as an
additional source of targeted threat intelligence.

The idea is quite straightforward: *dnstwist* takes in your domain name as a
seed, generates a list of potential phishing domains and then checks to see if
they are registered.
Additionally it can test if the mail server from MX record can be used to
intercept misdirected corporate e-mails and it can generate fuzzy hashes of the
web pages to see if they are live phishing sites.

![Demo](/docs/screens/demo.gif)

Key features
------------
There are several pretty good reasons to give it a try:

- Wide range of efficient domain fuzzing algorithms
- Multithreaded job distribution
- Resolves domain names to IPv4 and IPv6
- Queries for NS and MX records
- Evaluates web page similarity with fuzzy hashes to find live phising sites
- Tests if MX host (mail server) can be used to intercept misdirected e-mails (espionage)
- Generates additional domain variants using dictionary files
- GeoIP location information
- Grabs HTTP and SMTP service banners
- WHOIS lookups for creation and modification date
- Prints output in CSV format

Requirements
------------
If you want *dnstwist* to develop full power, please make sure the following
Python modules are present on your system. If missing, *dnstwist* **will still
work**, but without many cool features. You'll get a notification in absence of
required module.

- [A DNS toolkit for Python](http://www.dnspython.org/)
- [Python GeoIP](https://pypi.python.org/pypi/GeoIP/)
- [Python WHOIS](https://pypi.python.org/pypi/whois)
- [Requests: HTTP for Humans](http://www.python-requests.org/)
- [ssdeep Python wrapper](https://pypi.python.org/pypi/ssdeep)

If running Ubuntu or Debian, you can install dependencies like this:

```
$ sudo apt-get install python-dnspython python-geoip python-whois python-requests
```

Installation of *ssdeep* module requires just a little bit more effort:

```
$ sudo apt-get install build-essential libffi-dev python-dev python-pip automake autoconf libtool
$ sudo BUILD_LIB=1 pip install ssdeep
```

Now it is fully equipped and ready for action.

How to use
----------
To start, it's a good idea to enter only the domain name as an argument. The
tool will run it through its fuzzing algorithms and generate a list of
potential phishing domains with the following DNS records: A, AAAA, NS and MX.

```
$ dnstwist.py example.com
```

Manually checking each domain name in terms of serving a phishing site might be
time consuming. To address this, *dnstwist* makes use of so called fuzzy hashes
(context triggered piecewise hashes). Fuzzy hashing is a concept which involves
the ability to compare two inputs (in this case HTML code) and determine a
fundamental level of similarity. This unique feature of *dnstwist* can be
enabled with *--ssdeep* argument. For each generated domain, *dnstwist* will
fetch content from responding HTTP server (following possible redirects) and
compare its fuzzy hash with the one for the original (initial) domain. The
level of similarity will be expressed as a percentage. Please keep in mind it's
rather unlikely to get 100% match for a dynamicaly generated web page, but each
notification should be inspected carefully regardless of the percentage level.

```
$ dnstwist.py --ssdeep example.com
```

In some cases phishing sites are served from a specific URL. If you provide a
full or partial URL address as an argument, *dnstwist* will parse it and apply
for each generated domain name variant. This ability is obviously useful only
in conjunction with fuzzy hashing feature.

```
$ dnstwist.py --ssdeep https://example.com/owa/
$ dnstwist.py --ssdeep example.com/crm/login
```

Very often attackers set up e-mail honey pots on phishing domains and wait for
mistyped e-mails to arrive. In this scenario, attackers would configure their
server to vacuum up all e-mail addressed to that domain, regardless of the user
it was sent towards. Another *dnstwist* feature allows to perform a simple test
on each mail server (advertised through DNS MX record) in order to check which
one can be used for such hostile intent. Suspicious servers will be marked with
*SPYING-MX* string.

Please be aware of possible false positives. Some mail servers only pretend to
accept incorrectly addressed e-mails but then discard those messages. This
technique is used to prevent a directory harvest attack.

```
$ dnstwist.py --mxcheck example.com
```

Not always domain names generated by the fuzzing algorithms are sufficient. To
generate even more domain name variants please feed *dnstwist* with a
dictionary file. Some dictionary samples with a list of the most common words
used in targeted phishing campaigns are included. Feel free to adapt it to your
needs.

```
$ dnstwist.py --dictionary dictionaries/english.dict example.com
``` 

Usually generated list of domains has more than a hundred of rows - especially
for longer domain names. In such cases, it may be practical to display only
registered (resolvable) ones using *--registered* argument.

```
$ dnstwist.py --registered example.com
```

The tool is shipped with built-in GeoIP database. Use *--geoip* argument to
display geographical location (country name) for each IPv4 address.

```
$ dnstwist.py --geoip example.com
```

Of course all of the features offered by *dnstwist* together with brief
descriptions are always available at your fingertips:

```
$ dnstwist.py --help
```

Good luck!

Contact
-------
To send questions, comments or a chocolate, just drop an e-mail at
[marcin@ulikowski.pl](mailto:marcin@ulikowski.pl)

You can also reach me via:

- Twitter: [@elceef](https://twitter.com/elceef)
- LinkedIn: [Marcin Ulikowski](https://pl.linkedin.com/in/elceef)

Any feedback is appreciated. I like to receive notifications from satisfied
customers so if you were able to run the tool and you are happy with the
results after just let me know.

If you find some confirmed phishing domains with *dnstwist* and are comfortable
with sharing them, please send me a message. Thank you.