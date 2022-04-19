# Golang-Security-Vulnerabilities-Explanation

## Author: Dr. Mohamamdreza Ashouri
## Web: https://ashoury.net
## Contact: ashourics@protonmail.com


## Nil pointer deference in Golang
Nil pointer deference is a trickly error that can lead to memory access violations once a nil pointer or nil interface was passed to a function that does not handle nil. In all cases, it is a tricky programming error that sometimes cannot be found by static analysis; either the function should handle nil, or the caller should not have passed nil to the function.
This error has been controlled in the {\targetproject}'s go files, and the project is secure against this issue.


The following snippet of code shows a sample of the problem in practice. This vulnerable code does not expect the type to be nil. For instance, when developers create a function that accepts an interface, they usually want to call the method(s) that the interface defines on the variable:

```
        package main
        
        import "fmt"
        
        type Named interface {
            Name() string
        }
        func greeting(thing Named) string {
            return "Hello " + thing.Name()
        }
        
        func main() {
        	
        	fmt.Println("nil pointer dereference") 
        	greeting(nil)
        }
```

Note that the same is true when using a pointer to a type that is used to modify a struct in place. Developers expect to get an instance of the struct when writing a function like this:

```
            type myNumber struct {
                n int
            }
            func plusOne(number *myNumber) {
                number.n++
            }
            \end{lstlisting}
            
            type myNumber struct {
                n int
            }
            func plusOne(number *myNumber) {
                number.n++
            }
```

However, when calling it with nil it will compile fine but error at runtime:

```
    func main() {
        var number *myNumber
        plusOne(number)
    }
```

It is important to attention that simple examples are controlled during code review. However, nil pointer dereferences cause panics we have in production. They often happen in some rarely used "code path" or because of unexpected inputs.
We used static analysis to find this issue in the source code, but we could not find any reports.
### Solution for nil pointer deference
Finally, to prevent this issue from further development, we suggest adding the nil checks in the coding practices.

```
func sample(x Named) (string, error) {
    if x == nil {
        return "", errors.New("x cannot be nil")
    }
    return "... " + x.Name(), nil
}
```
