# Golang Concurrency Manager

[![run tests](https://github.com/zenthangplus/goccm/actions/workflows/test.yml/badge.svg?branch=master)](https://github.com/zenthangplus/goccm/actions/workflows/test.yml)
[![codecov](https://codecov.io/gh/zenthangplus/goccm/branch/master/graph/badge.svg)](https://codecov.io/gh/zenthangplus/goccm)
[![goreportcard](https://goreportcard.com/badge/github.com/zenthangplus/goccm)](https://goreportcard.com/report/github.com/zenthangplus/goccm)

Golang Concurrency Manager package limits the number of goroutines that are allowed to run concurrently.

### Installation

Run the following command to install this package:

```
$ go get -u github.com/zenthangplus/goccm
```

### Example

```go
package main

import (
    "fmt"
    "github.com/zenthangplus/goccm"
    "time"
)

func main() {
    // Limit 3 goroutines to run concurrently.
    c := goccm.New(3)

    for i := 1; i <= 10; i++ {

        // This function has to call before any goroutine
        c.Wait()

        go func(i int) {
            fmt.Printf("Job %d is running\n", i)
            time.Sleep(2 * time.Second)

            // This function has to when a goroutine has finished
            // Or you can use `defer c.Done()` at the top of goroutine.
            c.Done()
        }(i)
    }

    // This function has to call to ensure all goroutines have finished
    // after close the main program.
    c.WaitAllDone()

    // This function will release all the channels associated with goccm
    c.Release()
}
```

### List of supported functions

```go
package main

import "github.com/zenthangplus/goccm"

func main() {
    // Create the concurrency manager
    // The first argument is the maximum number of goroutines to run concurrently.
    c := goccm.New(10)

    // Wait until a slot is available for the new goroutine.
    c.Wait()

    // Mark a goroutine as finished
    c.Done()

    // Wait for all goroutines are done
    c.WaitAllDone()

    // Close the manager manually
    c.Close()

    // Returns the number of goroutines which are running
    c.RunningCount()

    // Closes all the channels associated with goccm
    c.Release()
}
```
