# Randomito 

![](./randomito.png)


Donc on nous laisse avec ce script 

```py

#!/usr/local/bin/python2

import sys
import signal
from random import randint

# Time allowed to answer (seconds)
DELAY = 10

def handler(signum, frame):
   raise Exception("Time is up!\n")

def p(s):
	sys.stdout.write(s)
	sys.stdout.flush()

def challenge():

	for _ in range(10):
		p("[+] Generating a 128-bit random secret (a, b)\n")
		secret_a = randint(0, 2**64 - 1)
		secret_b = randint(0, 2**64 - 1)
		secret   = "{:016x}{:016x}".format(secret_a, secret_b)
		p("[+] Done! Now, try go guess it!\n")
		p(">>> a = ")
		a = int(input())
		p(">>> b = ")
		b = int(input())
		check = "{:016x}{:016x}".format(a, b)
		p("[-] Trying {}\n".format(check))
		if check == secret:
			flag = open("flag.txt").read()
			p("[+] Well done! Here is the flag: {}\n".format(flag))
			break
		else:
			p("[!] Nope, it started by {}. Please try again.\n".format(secret[:5]))

if __name__ == "__main__":
	signal.alarm(DELAY)
	signal.signal(signal.SIGALRM, handler)
	try:
		challenge()
	except Exception, e: 
		exit(0)
	else:
		exit(0)
```

Ici c'est le check qui n'est pas secure on peut le bypass simplement en mettant dans les input "secret_a" & "secret_b". Testons ça

    $ nc challenges2.france-cybersecurity-challenge.fr 6001
    [+] Generating a 128-bit random secret (a, b)
    [+] Done! Now, try go guess it!
    >>> a = secret_a
    >>> b = secret_b
    [-] Trying 98e0f1ec6fe2c9d5aaf389b29e0380a4
    [+] Well done! Here is the flag: FCSC{4496d11d19db92ae53e0b9e9415d99d877ebeaeab99e9e875ac346c73e8aca77}

Plutôt simple non ?