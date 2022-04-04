# quickmaths
Quickmaths CTF challenge solve

The challenge was that the server was constantly sending mathematical calculations that had to be calculated. According to the content of the task, there were 1000 calculations. In order to automate and facilitate the task, the author, in this case Kacper "53jk1" created a fairly simple script in Python.

To complete the exercise, Kacper imported the pwn library, which is available in Python. The script was executed by the author in Python 3.8.10 (default, Nov 26 2021, 20:14:08).

The host constant represented the address to which the script was connecting.

The constant port represented the port that was open and on which the mathematical calculations were served.

The connection constant created a connection to the remote host. The advantage of this class is that it supports both IPv4 and IPv6. The returned object supported all methods from the `pwnlib.tubes.sock` and `pwlib.tubes.tube` libraries.

The necessary arguments to the remote class that I provided were the constant `host` stored in a string, which represented the host to which I would connect, and the constant `port`, which was an integer and represented the port to which it would connect.

Next, the author wrote out five `recvline` methods that retrieved individual lines from the tube. "A `recvline` is a sequence of bytes, ending in `n`. If the request is not fulfilled before `timeout`, then all data is cached as an empty string `''` and is returned.

The script that Kacper "53jk1" created is fairly simple. It uses a simple trick with an infinite petal `while True`, where `True` is a `bool` value. Each time it loops, the `data` variable is overwritten. The author defined the `data` variable as a variable that is a list containing a byte. Each time `data` was received, then the `split` method was executed to return a list of sections in bytes using `b'"` as a delimiter.

To make it easier to keep track of what actually happens during the execution of the script, the author implemented a `print` function that displayed the `data` value updated at each loop. The `print` function works on the principle that it prints out a pipe on output.

The variables `first`, `second` and `third` were also overwritten at each loop. The `first` and `third` variables represented the numbers to be used in the calculation, while the `second` variable represented the character in the calculation. To hold such data, the author had to use arrays and list the last elements. To decode the special character, however, he used the `decode` method, which decodes the byte using the codec registered for encoding. The encoding used is `UTF-8`. At each looping, a `print` function was also used by the author to display the pipeline value in the output, so that you can check if the computation definitely looks as it should.

In analyzing the issue, the author noted that there are four types of operations:
- `-`
- `+`
- `//`
- `*`

To distinguish the resulting symbol, the conditional function `if` was used. That is, if the condition was met then a particular set of instructions was executed.

The result, was created using the `str()` class. The purpose of this was to create a new string object from the received object. That is, even if the result was an `int` value, you could still easily convert it to a `string` and send it on.

Once the result was written to the `result` variable, the author used the `connection` variable, which was responsible for the remote connection and contained the `sendline` method, through which the author could send the `result` variable, which was a string, and through the `encode` method, encode the string using the codec registered through encoding. The author used the UTF-8 standard here.

In a further step, the author used the `print` function to output the pipe and get the `result` variable, which was a string, and then thanks to the `encode` method, encode it into a string so as to check what data is being sent to the host. 

In the next step, the `data` value was overwritten by the `connection` variable, which represented the remote connection, thanks to the `recvline` method, and received a single line from the tube. A line is a sequence of bytes that ends with a newline, which by default is stored as `n`. If the request was not fulfilled before the declared value of `timeout`, then all cached data would be returned as the empty string `' '`.

Then, thanks to the `print` function, the author would write the values to the pipeline, for output. The data it showed in the output was the variable `data` given in `byte`. This instruction was created mainly to make it easier to debug.

The last thing that was implemented in each conditional `if` function was `continue`, which allowed us to create an infinite loop.

Below is the source code that allowed the author to obtain the flag.

```
from pwn import *

host = '34.148.103.218'
port = 1228

connection = remote('34.148.103.218',1228)
data = connection.recvline(1024)
data = connection.recvline(1024)
data = connection.recvline(1024)
data = connection.recvline(1024)
data = connection.recvline(1024)

while True:
    data = connection.recvline(1024)
    
    data = data.split(b" ")
    print(data)

    first = int(data[-3])
    second = data[-2].decode("utf-8")
    third = int(data[-1])
    print(first, second, third)

    if second == '-':
        result = str(first - third)
        connection.sendline(result.encode("utf-8"))
        print(result.encode("utf-8"))
        data = connection.recvline(1024)
        print(data)
        continue

    if second == '*':
        result = str(first * third)
        connection.sendline(result.encode("utf-8"))
        print(result.encode("utf-8"))
        data = connection.recvline(1024)
        print(data)
        continue

    if second == '//':
        result = str(first // third)
        connection.sendline(result.encode("utf-8"))
        print(result.encode("utf-8"))
        data = connection.recvline(1024)
        print(data)
        continue

    if second == '+':
        result = str(first + third)
        connection.sendline(result.encode("utf-8"))
        print(result.encode("utf-8"))
        data = connection.recvline(1024)
        print(data)
        continue
```
