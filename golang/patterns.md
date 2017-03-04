# Go Concurrency Patterns

from [https://www.youtube.com/watch?v=f6kdp27TYZs](https://www.youtube.com/watch?v=f6kdp27TYZs)

## Generator: function that returns a channel

The following example blocks on the receive channel for five iterations

	c := boring("boring!")  // function returns a receive-only channel
	for i := 0; i<5; i++ {
		fmt.Printf("You say: %q\n", <-c)
	}

	func boring(msg String) <-chan string {
		c := make(chan string)
		go func() { // Launch go-routine
			c <- fmt.Sprintf("%s %d", msg, i) // write the message to the channel
			time.Sleep( /* sleep some amount */ )
		}()
		return c
	}

### Multiplexing: Fan-in

Take multiple values, and forward them to the output of fan-in function

	func fanIn(input1, input2 <- chan string) <-chan string {
		c := make(chan string)
		go func() { for { c <- <-input1 } }()
		go func() { for { c <- <-input2 } }()
		return c
	}

	// Improved fanIn, uses select
	func fanIn(input1, input2 <- chan string) <-chan string {
		c := make(chan string)
		go func() {
			for {
				select {  // will block until input1 / input2 is ready
				case s := <-input1: c <- s
				case s := <-input2: c <- s
				}
			}
		}()
		return c
	}

	c := fanIn(boring("Joe"), boring("Ann"))
	for i := 0; i<10; i++ {
		fmt.Println(<-c)  // because there is a go routine for each separate input, they are concurrently
				  // checking for input, and writing to a single channel. This de-couples the
                                  // execution order of the go-routine
	}

### Timing out a select

	select {
	case <something>: ...
	case <- time.After(1 * time.Second)
		return
	}

	// or timeout entire select
	time := time.After(5 * time.Second)
	select {
	case <- time
		return;
	}

### Restoring sequencing

Introducing a 'wait' channel which allows synchronisation

	type Message struct {
		str string
		wait chan bool
	}


	func boring(msg string) <-chan Message { // returns a channel with string message, and a wait channel
		...

		waitForIt := make(chan bool)
		go func() {
			c <- Message{ fmt.Sprintf("%s %d", msg, i), waitForIt } // Write the message (including the wait channel)
			... random sleep ...
			<-waitForIt  // read from wait channel allowing client to progress
		}()

		...
	}

	func main() {
		...
		// in loop {
			// read message from channel, print to stdout

			msg1.wait <- true
			msg2.wait <- true
		}
		...
	}


