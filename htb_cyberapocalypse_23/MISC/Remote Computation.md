# Remote Computation (easy)
## Description 
The alien species use remote machines for all their computation needs. Pandora managed to hack into one, but broke its functionality in the process. Incoming computation requests need to be calculated and answered rapidly, in order to not alarm the aliens and ultimately pivot to other parts of their network. Not all requests are valid though, and appropriate error messages need to be sent depending on the type of error. Can you buy us some time by correctly responding to the next 500 requests?

## Initial thoughts
Easy enough right? Have to accept input from the server, solve the problem and return it. I could use pwntools to help me with python. Thats partially true.

## Requirements 
There were a few errors they were very picky of.
```
Results
---
All results are rounded
to 2 digits after the point.
ex. 9.5752 -> 9.58

Error Codes
---
* Divide by 0:
This may be alien technology,
but dividing by zero is still an error!
Expected response: DIV0_ERR

* Syntax Error
Invalid expressions due syntax errors.
ex. 3 +* 4 = ?
Expected response: SYNTAX_ERR

* Memory Error
The remote machine is blazingly fast,
but its architecture cannot represent any result
outside the range -1337.00 <= RESULT <= 1337.00
Expected response: MEM_ERR
```
There were even a few things they didn't tell me like how all floats had to be the hundreths or numbers required proper comma formatting (ex. 1,000 vs 1000). All return I had back was 'Incorrect Answer' so that took me a while to get down but nonetheless once I had the requirements the rest was straight forwards.

```
#!/bin/python3
from pwn import *

#connect to server
r = remote('ip', port)

#read in menu output from server and select start option (1)
r.recv(4)
r.clean()
r.sendline(b'1')
r.clean()

#read first question from server
srvResponse = r.recv()

#loop until complete
while '=' in srvResponse.decode('utf-8'):

    #print out the question (debug)
    print(srvResponse.decode('utf-8'))

    #some errors might come along so I used a try except for those cases
    try:
        #cleaned up the question response and threw it into the big ugly eval
        answer = eval(srvResponse.decode('utf-8').split(":")[1].split('=')[0])

        #returned the correct error if calculation was too big or small for aliens poor computer 
        if float(answer) > 1337.00 or float(answer) < -1337.00:
            answer = "MEM_ERR"
        #this is where I ensured all floats had 2 decimal places
        elif type(answer) == float:
            answer = '{:.2f}'.format(answer)
        #added the comma formatting as needed
        else:
            answer = format(answer, ",")
    #Example 2/0 return correct error
    except ZeroDivisionError as e:
        answer = 'DIV0_ERR'
    #Example 3 +* 4 return correct eror
    except SyntaxError as e:
        answer = "SYNTAX_ERR"
        
    #prints out what I'm responding with (debug)
    print(answer)

    #response to server with correct answers
    r.sendline(str(answer).strip().encode())
    r.clean()
    srvResponse = r.recv()

else:
    #used for debug and returned flag for me
    print(srvResponse.decode('utf-8'))
```

After running that, it went 500 times and returned my flag