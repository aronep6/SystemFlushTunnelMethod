# Wispio System Flush Tunnel Method

The Wispio System Flush Tunnel Method is a simple yet effective way to keep the output stream of your Python program up to date and to ensure that all of the data generated is written out to the stream, instead of being buffered in memory.

## Python side

For the exemple there is a piece of code to explain this method, The program works by creating a loop of n iterations and waiting for 1 second between each iteration. At each iteration, a message is printed to the output stream. Additionally, an exception is thrown at the 10th iteration to test the error handling.

To use this method, simply import the `time` and `sys` modules and call the `main()` function. The `sys.stdout.flush()` command is used to flush the output stream, so that any buffered data is written out to the stream.

This method is useful when writing programs that generate large amounts of output data, as it ensures that all of the data is written out to the stream, instead of being buffered in memory.

```bash
import time
import sys

repeat = 60

def main():
    for i in range(repeat):
        print("Hey we are at the iteration " + str(i) + " in python script !")
        sys.stdout.flush()

        # if is the 10 iteration, we will throw an error to test the error handling on NodeJS worker
        if i == 10:
            raise Exception("This is an error to test the error handling")
        
        time.sleep(1)

if __name__ == "__main__":
    main()
```

By putting  `sys.stdout.flush()` in the loop, it ensures that all of the data generated is written out to the stream, instead of being buffered in memory. This way, you can be sure that your output stream is always up to date.

## Node.js side

On the Node.js side, we get all flush data stream on the worker by  

- listening to the `data` event on the `pythonProcess.stdout` 
- emit the error message when a `data` event is received on `pythonProcess.stderr` 
- and finally, when the `close` event is triggered on `pythonProcess`, we check the exit code and exit the process accordingly.

By using this method, we can ensure that our output stream is always up to date and we can handle errors accordingly.

```bash
const pythonProcess = spawn("python", [ __dirname + "/_0_.py" ]);

pythonProcess.stdout.on("data", data => {
    parentPort && parentPort.postMessage(data.toString());
});

pythonProcess.stderr.on("data", data => {
    parentPort && parentPort.emit("error", data.toString());
});

pythonProcess.on("close", code => {
    if (code !== 0) {
        return process.exit(1);
    } else {
        return process.exit(0);
    }
});
```
