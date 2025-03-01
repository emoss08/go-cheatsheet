# My Personal Golang Cheatsheet

## Basic Types

### Integer Size

|Type|Size|Range|
|---|---|---|
|int|Depends on Platform: 32 bits in 32 bit systems and 64 in 64 bit systems|-2147483648 to 2147483647 in 32 bit systems and -9223372036854775808 to 9223372036854775807 in 64 bit systems|
|int8|8 bits/1 byte|-128 to 127|
|int16|16 bits/2 byte|-32768 to 32767|
|int32|32 bits/4 byte|-2147483648 to 2147483647|
|int64|64 bits/8 byte|-9223372036854775808 to 9223372036854775807|

### Unsigned Integers

|Type|Size|Range|
|---|---|---|
|uint|Platform dependent|0 to 4294967295 (32-bit) or 0 to 18446744073709551615 (64-bit)|
|uint8|8 bits/1 byte|0 to 255|
|uint16|16 bits/2 byte|0 to 65535|
|uint32|32 bits/4 byte|0 to 4294967295|
|uint64|64 bits/8 byte|0 to 18446744073709551615|
|uintptr|Platform dependent|Unsigned integer large enough to store the uninterpreted bits of a pointer value|

### Floating Point

|Type|Size|Precision|
|---|---|---|
|float32|32 bits/4 byte|~6-9 digits of precision|
|float64|64 bits/8 byte|~15-17 digits of precision|
|complex64|64 bits/8 byte|Complex numbers with float32 real and imaginary parts|
|complex128|128 bits/16 byte|Complex numbers with float64 real and imaginary parts|

### Other Basic Types

|Type|Description|Zero Value|
|---|---|---|
|bool|Boolean (true/false)|false|
|string|UTF-8 encoded text|"" (empty string)|
|byte|Alias for uint8|0|
|rune|Alias for int32 (Unicode code point)|0|
|error|Interface type for error handling|nil|

## String Operations

```go
// String concatenation
str1 := "Hello"
str2 := "World"
result := str1 + " " + str2

// String length
length := len(str1)  // 5

// Accessing characters (returns byte/uint8)
firstChar := str1[0]  // 'H' (72 in ASCII)

// Slicing strings
substring := str1[1:3]  // "el"

// Converting to runes (for Unicode handling)
runes := []rune(str1)
for i, r := range runes {
    fmt.Printf("Position %d: %c (Unicode: %U)\n", i, r, r)
}

// String comparison
equal := str1 == "Hello"  // true
notEqual := str1 != str2  // true
lessThan := str1 < str2   // true ('H' < 'W' in ASCII)

// String formatting
formatted := fmt.Sprintf("Hello, %s! You are %d years old.", "John", 30)

// String conversion
num := 123
strNum := strconv.Itoa(num)  // "123"
backToNum, _ := strconv.Atoi(strNum)  // 123

// Multiline strings
multiline := `This is a
multiline
string`

// String Builder (efficient for multiple concatenations)
var builder strings.Builder
builder.WriteString("Hello")
builder.WriteString(", ")
builder.WriteString("World")
result := builder.String()  // "Hello, World"

// Check if string contains substring
contains := strings.Contains(str1, "ell")  // true

// Replace in string
replaced := strings.Replace(str1, "e", "E", 1)  // "HEllo"

// Split string
parts := strings.Split("a,b,c", ",")  // ["a", "b", "c"]

// Join strings
joined := strings.Join([]string{"a", "b", "c"}, "-")  // "a-b-c"

// Trim spaces
trimmed := strings.TrimSpace(" hello ")  // "hello"

// Convert case
upper := strings.ToUpper(str1)  // "HELLO"
lower := strings.ToLower(str1)  // "hello"
```

## Pointers

```go
// Declare a pointer
var p *int

// Initialize a pointer
i := 42
p = &i  // p holds the memory address of i

// Dereferencing a pointer
value := *p  // value = 42

// Update value through pointer
*p = 21  // i is now 21

// Create a pointer with new
p := new(int)  // Creates a pointer to a zero-valued int
*p = 42

// Pointer to struct
type Person struct {
    Name string
    Age  int
}

person := Person{"John", 30}
pointer := &person
pointer.Name = "Jane"  // Shorthand for (*pointer).Name = "Jane"

// Function with pointer argument
func updateAge(p *Person, newAge int) {
    p.Age = newAge
}
updateAge(&person, 31)  // person.Age is now 31

// Return a pointer from a function
func createPerson(name string, age int) *Person {
    return &Person{name, age}
}
p := createPerson("John", 30)

// Pointer to array
arr := [3]int{1, 2, 3}
arrPtr := &arr
(*arrPtr)[0] = 10  // arr[0] is now 10
// Shorthand
arrPtr[0] = 20     // arr[0] is now 20

// Pointer to slice
// Note: Slices already contain a pointer to the underlying array
slice := []int{1, 2, 3}
slicePtr := &slice
// To change the slice itself (not its contents)
*slicePtr = append(*slicePtr, 4)

// Pointer to map
// Note: Maps are already reference types
m := map[string]int{"a": 1}
mapPtr := &m
(*mapPtr)["b"] = 2  // m["b"] is now 2
```

## Function Types

```go
// Function type declaration
type MathFunc func(int, int) int

// Function that takes a function as parameter
func applyMath(a, b int, fn MathFunc) int {
    return fn(a, b)
}

// Anonymous function as variable
add := func(a, b int) int {
    return a + b
}
result := add(5, 3)  // 8

// Pass anonymous function as argument
result := applyMath(5, 3, func(a, b int) int {
    return a + b
})

// Function with multiple return values
func divMod(a, b int) (int, int) {
    return a / b, a % b
}
quotient, remainder := divMod(10, 3)  // 3, 1

// Named return values
func rectangle(width, height float64) (area, perimeter float64) {
    area = width * height
    perimeter = 2 * (width + height)
    return  // "naked" return uses named return values
}

// Variadic functions
func sum(nums ...int) int {
    total := 0
    for _, num := range nums {
        total += num
    }
    return total
}
total := sum(1, 2, 3, 4)  // 10

// Passing slice to variadic function
numbers := []int{1, 2, 3, 4}
total := sum(numbers...)  // Spread operator

// Closures
func adder() func(int) int {
    sum := 0
    return func(x int) int {
        sum += x
        return sum
    }
}
counter := adder()
fmt.Println(counter(1))  // 1
fmt.Println(counter(2))  // 3
fmt.Println(counter(3))  // 6

// Defer function calls
func example() {
    defer fmt.Println("This runs last")
    fmt.Println("This runs first")
}

// Multiple defers (LIFO order)
func example() {
    defer fmt.Println("This runs third")
    defer fmt.Println("This runs second")
    fmt.Println("This runs first")
}
```

## Custom Types

```go
// Type definitions
type Celsius float64
type Fahrenheit float64

// Methods on custom types
func (c Celsius) ToFahrenheit() Fahrenheit {
    return Fahrenheit(c*9/5 + 32)
}

func (f Fahrenheit) ToCelsius() Celsius {
    return Celsius((f - 32) * 5 / 9)
}

// Usage
var temp Celsius = 20
fmt.Println(temp.ToFahrenheit())  // 68

// Type aliases
type MyInt = int  // Alias (not a new type)

// Type conversions
var num int = 5
var float float64 = float64(num)

// Type definitions vs aliases
type Counter int      // New type, needs explicit conversion from int
type AltCounter = int // Alias, interchangeable with int

var c Counter = 5
// var c Counter = int(5)  // Doesn't work, need explicit conversion
var a AltCounter = 5  // Works directly, AltCounter is just an alias
```

## Time and Date

```go
// Current time
now := time.Now()

// Create a specific time
t := time.Date(2022, time.January, 1, 12, 0, 0, 0, time.UTC)

// Format time
formatted := t.Format("2006-01-02 15:04:05")
formatted := t.Format(time.RFC3339)

// Common format constants
time.RFC3339       // "2006-01-02T15:04:05Z07:00"
time.RFC1123       // "Mon, 02 Jan 2006 15:04:05 MST"
time.DateOnly      // "2006-01-02" (Go 1.20+)
time.TimeOnly      // "15:04:05" (Go 1.20+)

// Parse time
t, err := time.Parse("2006-01-02", "2022-01-01")
t, err := time.Parse(time.RFC3339, "2022-01-01T12:00:00Z")

// Time components
year, month, day := t.Date()
hour, min, sec := t.Clock()

// Duration
duration := 5 * time.Second
duration := 2*time.Hour + 30*time.Minute
duration := 5 * 24 * time.Hour // 5 days

// Add/subtract time
newTime := t.Add(2 * time.Hour)
pastTime := t.Add(-3 * time.Hour)
tomorrow := t.AddDate(0, 0, 1) // Add 1 day

// Difference between times
duration := t2.Sub(t1)
seconds := duration.Seconds()
minutes := duration.Minutes()
hours := duration.Hours()

// Compare times
before := t1.Before(t2)
after := t1.After(t2)
equal := t1.Equal(t2)

// Time zones
loc, err := time.LoadLocation("America/New_York")
t := time.Date(2022, time.January, 1, 12, 0, 0, 0, loc)

// Convert to UTC
utcTime := t.UTC()

// Sleep
time.Sleep(2 * time.Second)

// Timers
timer := time.NewTimer(2 * time.Second)
<-timer.C // Wait for timer to expire
timer.Stop() // Stop timer

// Tickers
ticker := time.NewTicker(1 * time.Second)
defer ticker.Stop()

for {
    select {
    case <-ticker.C:
        fmt.Println("Tick at", time.Now())
    case <-done:
        return
    }
}
```

## JSON

```go
// Define struct for JSON
type Person struct {
    Name    string `json:"name"`
    Age     int    `json:"age"`
    Address string `json:"address,omitempty"` // omit if empty
}

// Marshal (struct to JSON)
p := Person{Name: "John", Age: 30}
data, err := json.Marshal(p)
if err != nil {
    log.Fatal(err)
}
fmt.Println(string(data)) // {"name":"John","age":30}

// Pretty printing
data, err := json.MarshalIndent(p, "", "  ")
if err != nil {
    log.Fatal(err)
}
fmt.Println(string(data))

// Unmarshal (JSON to struct)
jsonData := []byte(`{"name":"Alice","age":25,"address":"123 Main St"}`)
var p Person
err := json.Unmarshal(jsonData, &p)
if err != nil {
    log.Fatal(err)
}
fmt.Println(p.Name, p.Age, p.Address)

// Working with dynamic JSON
var data map[string]interface{}
err := json.Unmarshal(jsonData, &data)
if err != nil {
    log.Fatal(err)
}
fmt.Println(data["name"])

// JSON number handling
var result struct {
    Value json.Number `json:"value"`
}
err := json.Unmarshal([]byte(`{"value": 12345}`), &result)
intVal, _ := result.Value.Int64()
floatVal, _ := result.Value.Float64()

// Custom JSON marshaling
type Date struct {
    time.Time
}

func (d Date) MarshalJSON() ([]byte, error) {
    return json.Marshal(d.Format("2006-01-02"))
}

func (d *Date) UnmarshalJSON(b []byte) error {
    var s string
    if err := json.Unmarshal(b, &s); err != nil {
        return err
    }
    t, err := time.Parse("2006-01-02", s)
    if err != nil {
        return err
    }
    d.Time = t
    return nil
}

// JSON streams
enc := json.NewEncoder(os.Stdout)
enc.SetIndent("", "  ")
enc.Encode(data)

dec := json.NewDecoder(resp.Body)
var result Person
dec.Decode(&result)
```

## Variables and Constants

### Variable Declaration

```go
// Single variable declaration
var name string
name = "John"

// Declaration with initialization
var name string = "John"

// Type inference
var name = "John"

// Short declaration (only inside functions)
name := "John"

// Multiple variable declaration
var a, b, c int
a, b, c = 1, 2, 3

// Multiple variable with type inference
var a, b, c = 1, 2, "three"

// Multiple variable short declaration
a, b, c := 1, 2, "three"

// Declaration with initialization block
var (
    name   string = "John"
    age    int    = 30
    active bool   = true
)
```

### Constants

```go
// Single constant
const Pi = 3.14159

// Multiple constants
const (
    StatusOK      = 200
    StatusCreated = 201
    StatusAccepted = 202
)

// Using iota (auto-incrementing counter)
const (
    Monday = iota // 0
    Tuesday       // 1
    Wednesday     // 2
    Thursday      // 3
    Friday        // 4
    Saturday      // 5
    Sunday        // 6
)

// Skipping values with iota
const (
    _           = iota // 0 (ignored)
    KB          = 1 << (10 * iota) // 1 << 10 = 1024
    MB                             // 1 << 20 = 1048576
    GB                             // 1 << 30 = 1073741824
)
```

## Control Structures

### If-Else

```go
// Basic if
if x > 10 {
    fmt.Println("x is greater than 10")
}

// If with else
if x > 10 {
    fmt.Println("x is greater than 10")
} else {
    fmt.Println("x is not greater than 10")
}

// If with else if
if x > 10 {
    fmt.Println("x is greater than 10")
} else if x < 5 {
    fmt.Println("x is less than 5")
} else {
    fmt.Println("x is between 5 and 10")
}

// If with initialization
if val := getValue(); val > 10 {
    fmt.Println("val is greater than 10:", val)
}
```

### Switch

```go
// Basic switch
switch day {
case "Monday":
    fmt.Println("It's Monday")
case "Tuesday":
    fmt.Println("It's Tuesday")
default:
    fmt.Println("It's another day")
}

// Switch with initialization
switch day := time.Now().Weekday(); day {
case time.Saturday, time.Sunday: // Multiple cases
    fmt.Println("It's the weekend")
default:
    fmt.Println("It's a weekday")
}

// Switch with no expression (alternative to if-else)
switch {
case hour < 12:
    fmt.Println("Good morning!")
case hour < 17:
    fmt.Println("Good afternoon!")
default:
    fmt.Println("Good evening!")
}

// Fallthrough (executes next case)
switch num {
case 1:
    fmt.Println("One")
    fallthrough
case 2:
    fmt.Println("Less than or equal to two")
}
```

### Loops

```go
// For loop (standard C-style)
for i := 0; i < 10; i++ {
    fmt.Println(i)
}

// For as a while loop
i := 0
for i < 10 {
    fmt.Println(i)
    i++
}

// Infinite loop
for {
    // Do something forever
    if shouldBreak {
        break
    }
}

// For with range (arrays, slices, strings, maps, channels)
// Arrays, slices, strings
for index, value := range collection {
    fmt.Println(index, value)
}

// Maps
for key, value := range myMap {
    fmt.Println(key, value)
}

// If you only need the key/index
for key := range myMap {
    fmt.Println(key)
}

// If you only need the value
for _, value := range collection {
    fmt.Println(value)
}

// Break and continue
for i := 0; i < 10; i++ {
    if i == 3 {
        continue // Skip this iteration
    }
    if i == 8 {
        break // Exit the loop
    }
    fmt.Println(i)
}
```

## Data Structures

### Arrays

```go
// Declaration with fixed size
var arr [5]int

// Declaration with initialization
arr := [5]int{1, 2, 3, 4, 5}

// Size inferred from initialization
arr := [...]int{1, 2, 3, 4, 5} // Creates [5]int

// Access array elements
first := arr[0]
arr[1] = 10

// Get array length
length := len(arr)

// Multi-dimensional arrays
var matrix [3][4]int
matrix[0][0] = 1
```

### Slices

```go
// Declaration
var s []int

// Creating a slice with make
s := make([]int, 5)      // Length and capacity of 5
s := make([]int, 3, 5)   // Length of 3, capacity of 5

// Slice literals
s := []int{1, 2, 3, 4, 5}

// Creating a slice from an array
arr := [5]int{1, 2, 3, 4, 5}
s := arr[1:4]  // Elements 1,2,3 (indices 1,2,3)

// Slice operations
s := []int{1, 2, 3, 4, 5}
s = append(s, 6)        // Add one element
s = append(s, 7, 8, 9)  // Add multiple elements
s = append(s, anotherSlice...) // Append another slice

// Copy slices
src := []int{1, 2, 3, 4, 5}
dst := make([]int, len(src))
copied := copy(dst, src)

// Slice length and capacity
length := len(s)
capacity := cap(s)

// Nil slice (zero value)
var s []int // s == nil, len(s) == 0, cap(s) == 0

// Check if a slice is nil
if s == nil {
    fmt.Println("s is nil")
}

// Empty slice (not nil)
s := []int{} // s != nil, len(s) == 0, cap(s) == 0
```

### Maps

```go
// Declaration
var m map[string]int

// Creating a map with make
m := make(map[string]int)

// Map literal
m := map[string]int{
    "apple": 5,
    "banana": 8,
    "orange": 7,
}

// Access map elements
count := m["apple"]

// Adding or updating elements
m["apple"] = 10
m["grape"] = 4

// Check if a key exists
value, exists := m["apple"]
if exists {
    fmt.Println("Value:", value)
} else {
    fmt.Println("Key doesn't exist")
}

// Delete a key
delete(m, "apple")

// Map length
size := len(m)

// Iterate over a map
for key, value := range m {
    fmt.Println(key, value)
}
```

## Functions

### Basic Functions

```go
// Function declaration
func add(a int, b int) int {
    return a + b
}

// Multiple parameters of the same type
func add(a, b int) int {
    return a + b
}

// Multiple return values
func divmod(a, b int) (int, int) {
    return a / b, a % b
}

// Named return values
func divmod(a, b int) (quotient, remainder int) {
    quotient = a / b
    remainder = a % b
    return // "naked" return uses named return values
}

// Variadic functions (variable number of arguments)
func sum(nums ...int) int {
    total := 0
    for _, num := range nums {
        total += num
    }
    return total
}
// Usage: sum(1, 2, 3) or sum(nums...)
```

### Function Values and Closures

```go
// Function as a value
add := func(a, b int) int {
    return a + b
}
result := add(5, 3)

// Function as a parameter
func apply(fn func(int, int) int, a, b int) int {
    return fn(a, b)
}

// Closures (functions that reference variables from outside their body)
func adder() func(int) int {
    sum := 0
    return func(x int) int {
        sum += x
        return sum
    }
}
// Usage:
counter := adder()
fmt.Println(counter(1)) // 1
fmt.Println(counter(2)) // 3
fmt.Println(counter(3)) // 6
```

### Defer, Panic, and Recover

```go
// Defer (executes when the surrounding function returns)
func example() {
    defer fmt.Println("This runs last")
    fmt.Println("This runs first")
}

// Multiple defers (executed in LIFO order)
func example() {
    defer fmt.Println("This runs third")
    defer fmt.Println("This runs second")
    fmt.Println("This runs first")
}

// Defer with function calls
file, err := os.Open("file.txt")
if err != nil {
    return err
}
defer file.Close() // Ensures file is closed even if the function panics

// Panic (stops normal execution of the current goroutine)
func divide(a, b int) int {
    if b == 0 {
        panic("division by zero")
    }
    return a / b
}

// Recover (captures a panic and resumes normal execution)
func safeOperation() (err error) {
    defer func() {
        if r := recover(); r != nil {
            err = fmt.Errorf("recovered from panic: %v", r)
        }
    }()
    
    // Do something that might panic
    panic("something went wrong")
}
```

## Structs and Methods

### Structs

```go
// Struct declaration
type Person struct {
    Name string
    Age  int
}

// Creating struct instances
var p1 Person
p1.Name = "John"
p1.Age = 30

// Struct literal
p2 := Person{Name: "Alice", Age: 25}

// Struct literal (order matters if fields not named)
p3 := Person{"Bob", 35}

// Pointer to struct
p4 := &Person{Name: "Charlie", Age: 40}
fmt.Println(p4.Name) // Shorthand for (*p4).Name

// Anonymous struct
point := struct {
    x, y int
}{10, 20}

// Embedded structs
type Address struct {
    Street string
    City   string
}

type Contact struct {
    Name    string
    Address // Embedded struct (field name is the type name)
}

c := Contact{
    Name: "John",
    Address: Address{
        Street: "123 Main St",
        City:   "Anytown",
    },
}
// Access embedded struct fields directly
fmt.Println(c.City) // same as c.Address.City
```

### Methods

```go
// Method declaration (with value receiver)
type Rectangle struct {
    Width, Height float64
}

func (r Rectangle) Area() float64 {
    return r.Width * r.Height
}

// Method with pointer receiver (can modify the receiver)
func (r *Rectangle) Scale(factor float64) {
    r.Width *= factor
    r.Height *= factor
}

// Usage
rect := Rectangle{Width: 10, Height: 5}
area := rect.Area()
rect.Scale(2)
```

## Interfaces

```go
// Interface declaration
type Shape interface {
    Area() float64
    Perimeter() float64
}

// Implementing an interface (implicit)
type Rectangle struct {
    Width, Height float64
}

func (r Rectangle) Area() float64 {
    return r.Width * r.Height
}

func (r Rectangle) Perimeter() float64 {
    return 2 * (r.Width + r.Height)
}

// Using an interface
func printShapeInfo(s Shape) {
    fmt.Printf("Area: %v, Perimeter: %v\n", s.Area(), s.Perimeter())
}

rect := Rectangle{Width: 10, Height: 5}
printShapeInfo(rect)

// Empty interface (accepts any type)
func printAny(v interface{}) {
    fmt.Println(v)
}

// Type assertions
value, ok := v.(string)
if ok {
    fmt.Println("It's a string:", value)
} else {
    fmt.Println("Not a string")
}

// Type switches
switch v := v.(type) {
case string:
    fmt.Println("String:", v)
case int:
    fmt.Println("Integer:", v)
default:
    fmt.Println("Unknown type")
}
```

## Concurrency

### Goroutines

```go
// Starting a goroutine
go func() {
    fmt.Println("Running in a goroutine")
}()

// Goroutine with function
func doWork() {
    // Do something
}
go doWork()
```

### Channels

```go
// Unbuffered channel
ch := make(chan int)

// Buffered channel
ch := make(chan int, 10)

// Send value to channel
ch <- 42

// Receive from channel
value := <-ch

// Close a channel
close(ch)

// Check if channel is closed
value, ok := <-ch
if !ok {
    fmt.Println("Channel closed")
}

// Range over channel (until closed)
for v := range ch {
    fmt.Println(v)
}

// Select statement
select {
case v1 := <-ch1:
    fmt.Println("Received from ch1:", v1)
case v2 := <-ch2:
    fmt.Println("Received from ch2:", v2)
case ch3 <- 42:
    fmt.Println("Sent to ch3")
default:
    fmt.Println("No channel operations ready")
}

// Timeout using select
select {
case result := <-ch:
    fmt.Println("Result:", result)
case <-time.After(2 * time.Second):
    fmt.Println("Timeout after 2 seconds")
}

// Quit channel pattern
func worker(quit chan bool) {
    for {
        select {
        case <-quit:
            fmt.Println("Worker stopping")
            return
        default:
            fmt.Println("Working...")
            time.Sleep(1 * time.Second)
        }
    }
}

quit := make(chan bool)
go worker(quit)
// Later:
quit <- true
```

### WaitGroups

```go
// Synchronize multiple goroutines
var wg sync.WaitGroup

// Add goroutines to wait for
wg.Add(3)

// Launch goroutines
for i := 0; i < 3; i++ {
    go func(id int) {
        defer wg.Done() // Decrement counter when done
        fmt.Printf("Worker %d done\n", id)
    }(i)
}

// Wait for all goroutines to complete
wg.Wait()
```

### Mutex

```go
// For protecting shared data
var (
    counter int
    mu      sync.Mutex
)

// Protect access to counter
mu.Lock()
counter++
mu.Unlock()

// Shorter form with defer
func increment() {
    mu.Lock()
    defer mu.Unlock()
    counter++
}

// RWMutex (multiple readers, single writer)
var rwmu sync.RWMutex

// Read lock (multiple allowed)
rwmu.RLock()
value := counter
rwmu.RUnlock()

// Write lock (exclusive)
rwmu.Lock()
counter++
rwmu.Unlock()
```

## Error Handling

```go
// Functions returning errors
func divide(a, b float64) (float64, error) {
    if b == 0 {
        return 0, errors.New("division by zero")
    }
    return a / b, nil
}

// Checking for errors
result, err := divide(10, 0)
if err != nil {
    fmt.Println("Error:", err)
    return
}
fmt.Println("Result:", result)

// Creating errors
err := errors.New("simple error")
err := fmt.Errorf("error value: %v", value)

// Custom error types
type MyError struct {
    Code    int
    Message string
}

func (e *MyError) Error() string {
    return fmt.Sprintf("error %d: %s", e.Code, e.Message)
}

// Returning custom error
return nil, &MyError{Code: 404, Message: "not found"}

// Checking error types
if err, ok := err.(*MyError); ok {
    fmt.Println("Code:", err.Code)
}

// Error wrapping (Go 1.13+)
err := fmt.Errorf("failed to process data: %w", origErr)

// Unwrapping errors
unwrapped := errors.Unwrap(err)

// Checking wrapped errors
if errors.Is(err, os.ErrNotExist) {
    // Handle specific error
}

// Checking error types with As
var pathErr *os.PathError
if errors.As(err, &pathErr) {
    fmt.Println("Path:", pathErr.Path)
}
```

## File I/O

```go
// Reading a file
data, err := ioutil.ReadFile("file.txt")
if err != nil {
    log.Fatal(err)
}
fmt.Println(string(data))

// Reading line by line
file, err := os.Open("file.txt")
if err != nil {
    log.Fatal(err)
}
defer file.Close()

scanner := bufio.NewScanner(file)
for scanner.Scan() {
    line := scanner.Text()
    fmt.Println(line)
}

if err := scanner.Err(); err != nil {
    log.Fatal(err)
}

// Writing to a file
err := ioutil.WriteFile("file.txt", []byte("Hello, world!"), 0644)
if err != nil {
    log.Fatal(err)
}

// Writing with a buffer
file, err := os.Create("file.txt")
if err != nil {
    log.Fatal(err)
}
defer file.Close()

writer := bufio.NewWriter(file)
_, err = writer.WriteString("Hello, world!")
if err != nil {
    log.Fatal(err)
}
writer.Flush()
```

## JSON Handling

```go
// Define a struct
type Person struct {
    Name    string `json:"name"`
    Age     int    `json:"age"`
    Address string `json:"address,omitempty"` // omit if empty
}

// Marshal (struct to JSON)
p := Person{Name: "John", Age: 30}
data, err := json.Marshal(p)
if err != nil {
    log.Fatal(err)
}
fmt.Println(string(data)) // {"name":"John","age":30}

// Pretty printing
data, err := json.MarshalIndent(p, "", "  ")
if err != nil {
    log.Fatal(err)
}
fmt.Println(string(data))

// Unmarshal (JSON to struct)
jsonData := []byte(`{"name":"Alice","age":25,"address":"123 Main St"}`)
var p Person
err := json.Unmarshal(jsonData, &p)
if err != nil {
    log.Fatal(err)
}
fmt.Println(p.Name, p.Age, p.Address)

// Working with dynamic JSON
var data map[string]interface{}
err := json.Unmarshal(jsonData, &data)
if err != nil {
    log.Fatal(err)
}
fmt.Println(data["name"])

// JSON encoder/decoder for streams
enc := json.NewEncoder(os.Stdout)
enc.Encode(p)

dec := json.NewDecoder(resp.Body)
var result Person
dec.Decode(&result)
```

## HTTP Server

```go
// Simple HTTP server
http.HandleFunc("/", func(w http.ResponseWriter, r *http.Request) {
    fmt.Fprintf(w, "Hello, World!")
})
http.ListenAndServe(":8080", nil)

// HTTP handler with mux
mux := http.NewServeMux()
mux.HandleFunc("/hello", func(w http.ResponseWriter, r *http.Request) {
    fmt.Fprintf(w, "Hello!")
})
http.ListenAndServe(":8080", mux)

// Reading request parameters
func handler(w http.ResponseWriter, r *http.Request) {
    // URL query parameters
    query := r.URL.Query()
    name := query.Get("name")
    
    // Form data
    r.ParseForm()
    email := r.Form.Get("email")
    
    // Request headers
    userAgent := r.Header.Get("User-Agent")
    
    // JSON request body
    var data struct {
        Name string `json:"name"`
    }
    decoder := json.NewDecoder(r.Body)
    err := decoder.Decode(&data)
}

// Setting response headers
w.Header().Set("Content-Type", "application/json")
w.WriteHeader(http.StatusCreated) // 201

// Writing JSON response
json.NewEncoder(w).Encode(data)
```

## HTTP Client

```go
// Simple GET request
resp, err := http.Get("https://api.example.com")
if err != nil {
    log.Fatal(err)
}
defer resp.Body.Close()

// Read response body
body, err := ioutil.ReadAll(resp.Body)
if err != nil {
    log.Fatal(err)
}
fmt.Println(string(body))

// POST request with data
data := url.Values{}
data.Set("name", "John")
data.Set("age", "30")

resp, err := http.PostForm("https://api.example.com", data)
if err != nil {
    log.Fatal(err)
}
defer resp.Body.Close()

// Custom request with client
client := &http.Client{
    Timeout: 10 * time.Second,
}

req, err := http.NewRequest("POST", "https://api.example.com", bytes.NewBuffer(jsonData))
if err != nil {
    log.Fatal(err)
}
req.Header.Set("Content-Type", "application/json")
req.Header.Set("Authorization", "Bearer token123")

resp, err := client.Do(req)
if err != nil {
    log.Fatal(err)
}
defer resp.Body.Close()
```

## Testing

```go
// In file: math.go
package math

func Add(a, b int) int {
    return a + b
}

// In file: math_test.go
package math

import "testing"

func TestAdd(t *testing.T) {
    sum := Add(2, 3)
    if sum != 5 {
        t.Errorf("Add(2, 3) = %d; want 5", sum)
    }
}

// Table-driven tests
func TestAdd(t *testing.T) {
    tests := []struct {
        name     string
        a, b     int
        expected int
    }{
        {"positive", 2, 3, 5},
        {"negative", -2, -3, -5},
        {"mixed", -2, 3, 1},
    }
    
    for _, tt := range tests {
        t.Run(tt.name, func(t *testing.T) {
            if got := Add(tt.a, tt.b); got != tt.expected {
                t.Errorf("Add(%d, %d) = %d; want %d", tt.a, tt.b, got, tt.expected)
            }
        })
    }
}

// Running tests
// go test
// go test -v
// go test -cover

// Benchmarks
func BenchmarkAdd(b *testing.B) {
    for i := 0; i < b.N; i++ {
        Add(2, 3)
    }
}
// go test -bench=.

// Examples (also serve as documentation)
func ExampleAdd() {
    sum := Add(2, 3)
    fmt.Println(sum)
    // Output: 5
}
```

## Context

```go
// Creating a context
ctx := context.Background()
ctx := context.TODO()

// Context with timeout
ctx, cancel := context.WithTimeout(ctx, 2*time.Second)
defer cancel() // Always call cancel to release resources

// Context with deadline
deadline := time.Now().Add(2 * time.Second)
ctx, cancel := context.WithDeadline(ctx, deadline)
defer cancel()

// Context with value
ctx = context.WithValue(ctx, "key", "value")

// Getting a value from context
value := ctx.Value("key").(string)

// Checking if context is done
select {
case <-ctx.Done():
    err := ctx.Err() // context.DeadlineExceeded or context.Canceled
    fmt.Println("Context done:", err)
default:
    // Context still valid
}

// Using context with HTTP requests
req, err := http.NewRequestWithContext(ctx, "GET", "https://api.example.com", nil)

// Using context with database operations
rows, err := db.QueryContext(ctx, "SELECT * FROM users")
```

## Reflection

```go
// Get type of a variable
t := reflect.TypeOf(x)
fmt.Println(t.Name(), t.Kind())

// Get value of a variable
v := reflect.ValueOf(x)

// Create a new value
newInt := reflect.New(reflect.TypeOf(0))
newInt.Elem().SetInt(42)
i := newInt.Elem().Interface().(int)

// Accessing struct fields
t := reflect.TypeOf(s)
for i := 0; i < t.NumField(); i++ {
    field := t.Field(i)
    fmt.Println(field.Name, field.Type)
    
    // Get field tags
    tag := field.Tag.Get("json")
}

// Call methods
v := reflect.ValueOf(s)
m := v.MethodByName("Method")
result := m.Call([]reflect.Value{reflect.ValueOf(arg)})

// Set values (must be addressable)
v := reflect.ValueOf(&s).Elem()
if v.Kind() == reflect.Struct {
    f := v.FieldByName("Name")
    if f.IsValid() && f.CanSet() {
        f.SetString("New name")
    }
}
```

## Common Packages

### fmt

```go
// Printing
fmt.Print("Hello")
fmt.Println("Hello, world!")
fmt.Printf("Name: %s, Age: %d\n", name, age)

// String formatting
s := fmt.Sprintf("Name: %s, Age: %d", name, age)

// Format specifiers
fmt.Printf("%v", value)        // Default format
fmt.Printf("%+v", value)       // Struct field names
fmt.Printf("%#v", value)       // Go syntax
fmt.Printf("%T", value)        // Type
fmt.Printf("%d", number)       // Decimal integer
fmt.Printf("%f", float)        // Float
fmt.Printf("%.2f", float)      // Float with precision
fmt.Printf("%s", string)       // String
fmt.Printf("%q", string)       // Quoted string
fmt.Printf("%t", boolean)      // Boolean
fmt.Printf("%p", pointer)      // Pointer
fmt.Printf("%b", number)       // Binary
fmt.Printf("%x", number)       // Hex
fmt.Printf("%c", rune)         // Character
fmt.Printf("%U", rune)         // Unicode

// Reading input
var name string
fmt.Scan(&name)

fmt.Scanf("%s %d", &name, &age)

fmt.Scanln(&name, &age)
```

### time

```go
// Current time
now := time.Now()

// Creating time
t := time.Date(2022, time.January, 1, 12, 30, 0, 0, time.UTC)

// Time components
year, month, day := t.Date()
hour, min, sec := t.Clock()

// Formatting time
fmt.Println(t.Format("2006-01-02 15:04:05"))
fmt.Println(t.Format(time.RFC3339))

// Time constants
time.RFC3339       // "2006-01-02T15:04:05Z07:00"
time.RFC1123       // "Mon, 02 Jan 2006 15:04:05 MST"
time.ANSIC         // "Mon Jan _2 15:04:05 2006"
time.Kitchen       // "3:04PM"
time.DateOnly      // "2006-01-02"
time.TimeOnly      // "15:04:05"

// Parse time
t, err := time.Parse("2006-01-02", "2022-01-01")
t, err := time.Parse(time.RFC3339, "2022-01-01T12:30:00Z")

// Time zones
loc, err := time.LoadLocation("America/New_York")
t := time.Date(2022, time.January, 1, 12, 0, 0, 0, loc)
t = t.UTC() // Convert to UTC

// Comparing times
before := t1.Before(t2)
after := t1.After(t2)
equal := t1.Equal(t2)

// Duration
duration := 5 * time.Second
duration := 2*time.Hour + 30*time.Minute
duration := time.Duration(numberOfSeconds) * time.Second

// Adding time
newTime := t.Add(2 * time.Hour)
tomorrow := t.AddDate(0, 0, 1) // Add 1 day

// Subtracting time
duration := t2.Sub(t1)
pastTime := t.Add(-2 * time.Hour)

// Sleep
time.Sleep(2 * time.Second)

// Timers and tickers
timer := time.NewTimer(2 * time.Second)
<-timer.C // Wait for timer to expire

ticker := time.NewTicker(1 * time.Second)
for t := range ticker.C {
    fmt.Println("Tick at", t)
    if shouldStop {
        ticker.Stop()
        break
    }
}
```

### strings

```go
// String operations
s := "Hello, World!"
fmt.Println(len(s))                              // Length
fmt.Println(strings.ToUpper(s))                  // Convert to uppercase
fmt.Println(strings.ToLower(s))                  // Convert to lowercase
fmt.Println(strings.Contains(s, "World"))        // Check if contains substring
fmt.Println(strings.HasPrefix(s, "Hello"))       // Check prefix
fmt.Println(strings.HasSuffix(s, "!"))           // Check suffix
fmt.Println(strings.Index(s, "World"))           // Find position of substring
fmt.Println(strings.Count(s, "l"))               // Count occurrences
fmt.Println(strings.Replace(s, "World", "Go", 1)) // Replace (use -1 for all)
fmt.Println(strings.TrimSpace(" text "))         // Trim whitespace
fmt.Println(strings.Trim(s, "!"))                // Trim specific characters

// Splitting and joining
parts := strings.Split("a,b,c", ",")            // Split by delimiter
joined := strings.Join([]string{"a", "b", "c"}, ",") // Join with delimiter

// Builder (efficient string concatenation)
var builder strings.Builder
builder.WriteString("Hello")
builder.WriteString(", ")
builder.WriteString("World!")
result := builder.String()

// Comparing strings
result := strings.Compare(s1, s2) // -1, 0, or 1
equal := s1 == s2                 // Direct comparison

// Runes and bytes
runes := []rune(s)                // Convert to rune slice
bytes := []byte(s)                // Convert to byte slice
s = string(runes)                 // Back to string
s = string(bytes)                 // Back to string

// Iterate over characters (runes)
for i, r := range s {
    fmt.Printf("%d: %c\n", i, r)
}
```

### strconv

```go
// String to numeric conversions
i, err := strconv.Atoi("123")                 // String to int
i64, err := strconv.ParseInt("123", 10, 64)   // String to int64 (base 10)
u64, err := strconv.ParseUint("123", 10, 64)  // String to uint64
f, err := strconv.ParseFloat("123.45", 64)    // String to float64
b, err := strconv.ParseBool("true")           // String to bool

// Numeric to string conversions
s := strconv.Itoa(123)                        // int to string
s := strconv.FormatInt(123, 10)               // int64 to string (base 10)
s := strconv.FormatUint(123, 10)              // uint64 to string
s := strconv.FormatFloat(123.45, 'f', 2, 64)  // float64 to string
s := strconv.FormatBool(true)                 // bool to string

// Format specifiers for FormatFloat
// 'f': decimal no exponent (-123.456)
// 'e': scientific notation (1.234456e+2)
// 'g': 'e' for large exponents, 'f' otherwise
// prec: precision (decimal places for 'f', significant digits for 'e')

// Quote strings
quoted := strconv.Quote("Hello\nWorld")       // Adds quotes and escapes
unquoted, err := strconv.Unquote(quoted)      // Removes quotes and unescapes
```

### regexp

```go
// Compile a regular expression
re, err := regexp.Compile(`\d+`)
re := regexp.MustCompile(`\d+`) // Panics on error

// Match a string
isMatch := re.MatchString("abc123")

// Find matches
match := re.FindString("abc123def456")         // First match
allMatches := re.FindAllString("abc123def456", -1) // All matches (-1 for all)

// Find match positions
loc := re.FindStringIndex("abc123")            // [3, 6]
allLocs := re.FindAllStringIndex("abc123def456", -1)

// Extract submatches (capturing groups)
re := regexp.MustCompile(`(\w+):(\d+)`)
match := re.FindStringSubmatch("name:123")     // ["name:123", "name", "123"]
groups := re.FindStringSubmatchIndex("name:123")

// Replace
result := re.ReplaceAllString("abc123", "xyz")
result := re.ReplaceAllStringFunc("abc123", func(s string) string {
    // Process each match
    return "x" + s + "x"
})

// Split a string based on a regex
parts := re.Split("abc123def456", -1)
```

### math

```go
// Constants
math.Pi      // 3.141592653589793
math.E       // 2.718281828459045
math.MaxInt  // Maximum integer value
math.MaxFloat64 // Maximum float64 value

// Basic operations
math.Abs(-10.5)           // Absolute value
math.Ceil(10.1)           // Ceiling
math.Floor(10.9)          // Floor
math.Round(10.5)          // Round to nearest integer
math.Trunc(10.9)          // Truncate decimal part

// Powers and logarithms
math.Pow(2, 3)            // 2^3 = 8
math.Sqrt(16)             // Square root
math.Cbrt(27)             // Cube root
math.Log(math.E)          // Natural logarithm
math.Log10(100)           // Base 10 logarithm
math.Log2(8)              // Base 2 logarithm

// Trigonometry (angles in radians)
math.Sin(math.Pi/2)       // Sine
math.Cos(0)               // Cosine
math.Tan(math.Pi/4)       // Tangent
math.Asin(1)              // Arc sine
math.Acos(0)              // Arc cosine
math.Atan(1)              // Arc tangent
math.Atan2(y, x)          // Arc tangent of y/x

// Angular conversions
degrees := 180.0
radians := degrees * math.Pi / 180.0
degrees = radians * 180.0 / math.Pi

// Min/Max
math.Min(10, 20)          // Minimum
math.Max(10, 20)          // Maximum

// Random numbers (use with rand package)
import "math/rand"
rand.Seed(time.Now().UnixNano()) // Seed the random generator
r := rand.Float64()              // Random float64 between 0.0 and 1.0
r := rand.Intn(100)              // Random int between 0 and 99
```

## Go Modules and Package Management

```go
// Initialize a module in the current directory
go mod init example.com/mymodule

// Add missing and remove unused modules
go mod tidy

// Download all dependencies
go mod download

// List current module dependencies
go list -m all

// Add a specific dependency
go get github.com/some/package

// Add a specific version
go get github.com/some/package@v1.2.3
go get github.com/some/package@latest
go get github.com/some/package@master

// Remove a dependency
go get github.com/some/package@none

// Update dependencies
go get -u              // Update all direct and indirect dependencies
go get -u=patch        // Update patch releases only
go get -u github.com/some/package  // Update specific package

// Print module dependency graph
go mod graph

// Verify dependencies have expected content
go mod verify

// Make vendored copy of dependencies
go mod vendor

// Create go.work file for multi-module workspace
go work init ./module1 ./module2

// Build with vendored dependencies
go build -mod=vendor
```

## Code Organization and Best Practices

### Project Layout

```
project/
├── cmd/              # Main applications
│   └── myapp/        # Main package for the application
│       └── main.go   # Application entry point
├── internal/         # Private packages (not importable by other modules)
│   ├── auth/         # Authentication package
│   └── db/           # Database package
├── pkg/              # Public packages (can be imported by other modules)
│   ├── models/       # Data models
│   └── utils/        # Utility functions
├── api/              # API definitions (OpenAPI/Swagger specs, protocol definitions)
├── web/              # Web assets (HTML, CSS, JS)
├── configs/          # Configuration files
├── deployments/      # Deployment configurations and templates
├── test/             # Additional test applications and test data
├── docs/             # Documentation
├── examples/         # Examples for using the project
├── scripts/          # Scripts for build, setup, etc.
├── go.mod            # Module definition
├── go.sum            # Module checksums
└── README.md         # Project documentation
```

### Error Handling Patterns

```go
// Return error directly
func doSomething() error {
    if err := doStep1(); err != nil {
        return err
    }
    
    if err := doStep2(); err != nil {
        return err
    }
    
    return nil
}

// Wrap errors with context
func doSomething() error {
    if err := doStep1(); err != nil {
        return fmt.Errorf("step1 failed: %w", err)
    }
    
    return nil
}

// Error typing for more specific handling
type NotFoundError struct {
    Item string
}

func (e *NotFoundError) Error() string {
    return fmt.Sprintf("%s not found", e.Item)
}

func GetItem(id string) (Item, error) {
    if itemNotExists(id) {
        return Item{}, &NotFoundError{Item: id}
    }
    // ...
}

// Using it
item, err := GetItem("123")
if err != nil {
    if notFound, ok := err.(*NotFoundError); ok {
        // Handle not found case
        fmt.Printf("Item %s was not found\n", notFound.Item)
    } else {
        // Handle other errors
    }
}

// Using sentinel errors
var (
    ErrNotFound = errors.New("item not found")
    ErrPermissionDenied = errors.New("permission denied")
)

func GetItem(id string) (Item, error) {
    if itemNotExists(id) {
        return Item{}, ErrNotFound
    }
    // ...
}

// Using it
if errors.Is(err, ErrNotFound) {
    // Handle not found case
}
```

### Logging Patterns

```go
// Basic logging
log.Println("Starting application")
log.Printf("Processing item: %s", itemID)

// Log levels with standard library
log.Print("Info message")
log.Fatal("Fatal error") // Logs and calls os.Exit(1)
log.Panic("Panic error") // Logs and calls panic()

// Custom logger
logger := log.New(os.Stdout, "PREFIX: ", log.Ldate|log.Ltime|log.Lshortfile)
logger.Println("Custom logger message")

// Using structured logging (with a 3rd party library like zerolog)
import "github.com/rs/zerolog"

log := zerolog.New(os.Stdout).With().Timestamp().Logger()
log.Info().Str("user", "john").Int("id", 123).Msg("User logged in")
log.Error().Err(err).Str("path", "/api/users").Msg("Failed to handle request")
```

### Configuration Patterns

```go
// Environment variables
port := os.Getenv("APP_PORT")
if port == "" {
    port = "8080" // Default value
}

// Command line flags
import "flag"

port := flag.String("port", "8080", "Server port")
debug := flag.Bool("debug", false, "Enable debug mode")
flag.Parse()

fmt.Printf("Starting server on port %s, debug mode: %v\n", *port, *debug)

// Configuration struct with environment variables
type Config struct {
    Port     string
    DBHost   string
    DBUser   string
    DBPass   string
    LogLevel string
}

func LoadConfig() Config {
    return Config{
        Port:     getEnv("APP_PORT", "8080"),
        DBHost:   getEnv("DB_HOST", "localhost"),
        DBUser:   getEnv("DB_USER", "postgres"),
        DBPass:   getEnv("DB_PASS", ""),
        LogLevel: getEnv("LOG_LEVEL", "info"),
    }
}

func getEnv(key, defaultValue string) string {
    value := os.Getenv(key)
    if value == "" {
        return defaultValue
    }
    return value
}

// Configuration from JSON file
import "encoding/json"

type Config struct {
    Server struct {
        Port    string `json:"port"`
        Timeout int    `json:"timeout"`
    } `json:"server"`
    Database struct {
        Host     string `json:"host"`
        Port     int    `json:"port"`
        Username string `json:"username"`
        Password string `json:"password"`
    } `json:"database"`
}

func LoadConfig(path string) (Config, error) {
    var config Config
    
    file, err := os.Open(path)
    if err != nil {
        return config, err
    }
    defer file.Close()
    
    decoder := json.NewDecoder(file)
    err = decoder.Decode(&config)
    
    return config, err
}
```

## Advanced Techniques

### Embedding Resources

```go
// Using go:embed (Go 1.16+)
//go:embed static/logo.png
var logo []byte

//go:embed templates/*.html
var templates embed.FS

func main() {
    // Use embedded logo
    writeImage(logo)
    
    // Read files from embedded FS
    html, _ := templates.ReadFile("templates/index.html")
    fmt.Println(string(html))
    
    // List files in directory
    entries, _ := templates.ReadDir("templates")
    for _, entry := range entries {
        fmt.Println(entry.Name())
    }
}
```

### Generics (Go 1.18+)

```go
// Generic function
func Min[T constraints.Ordered](a, b T) T {
    if a < b {
        return a
    }
    return b
}

// Usage
minInt := Min[int](10, 20)
minFloat := Min[float64](10.5, 20.5)
minString := Min[string]("a", "b")

// Type constraints
type Number interface {
    int | int8 | int16 | int32 | int64 | float32 | float64
}

func Sum[T Number](values []T) T {
    var sum T
    for _, v := range values {
        sum += v
    }
    return sum
}

// Generic data structures
type Stack[T any] struct {
    items []T
}

func (s *Stack[T]) Push(item T) {
    s.items = append(s.items, item)
}

func (s *Stack[T]) Pop() (T, bool) {
    var zero T
    if len(s.items) == 0 {
        return zero, false
    }
    
    item := s.items[len(s.items)-1]
    s.items = s.items[:len(s.items)-1]
    return item, true
}

// Usage
stack := Stack[int]{}
stack.Push(10)
stack.Push(20)
item, ok := stack.Pop() // 20, true
```

### Performance Optimization

```go
// Benchmarking (create _test.go file)
func BenchmarkMyFunction(b *testing.B) {
    for i := 0; i < b.N; i++ {
        MyFunction()
    }
}
// Run with: go test -bench=.

// Profiling
import "runtime/pprof"

// CPU profiling
f, _ := os.Create("cpu.prof")
pprof.StartCPUProfile(f)
defer pprof.StopCPUProfile()

// Memory profiling
f, _ := os.Create("mem.prof")
defer func() {
    pprof.WriteHeapProfile(f)
    f.Close()
}()

// Run with profiling enabled
// go test -cpuprofile=cpu.prof -memprofile=mem.prof -bench=.

// Analyze with pprof
// go tool pprof cpu.prof
// go tool pprof mem.prof

// Avoid allocations in hot paths
// Pre-allocate slices with expected capacity
data := make([]int, 0, expectedSize)

// Use sync.Pool for frequently allocated objects
var bufferPool = sync.Pool{
    New: func() interface{} {
        return new(bytes.Buffer)
    },
}

buffer := bufferPool.Get().(*bytes.Buffer)
buffer.Reset()
// Use buffer...
bufferPool.Put(buffer)

// String concatenation: use strings.Builder instead of +
var builder strings.Builder
builder.WriteString("Hello")
builder.WriteString(", ")
builder.WriteString("World!")
result := builder.String()
```

### Working with Encoding and Compression

```go
// Base64 encoding/decoding
encoded := base64.StdEncoding.EncodeToString([]byte("Hello, world!"))
decoded, err := base64.StdEncoding.DecodeString(encoded)

// URL-safe Base64
encoded := base64.URLEncoding.EncodeToString([]byte("Hello, world!"))
decoded, err := base64.URLEncoding.DecodeString(encoded)

// Hex encoding/decoding
encoded := hex.EncodeToString([]byte("Hello, world!"))
decoded, err := hex.DecodeString(encoded)

// GZIP compression
var buf bytes.Buffer
zw := gzip.NewWriter(&buf)
zw.Write([]byte("Hello, world!"))
zw.Close()
compressed := buf.Bytes()

// GZIP decompression
zr, err := gzip.NewReader(bytes.NewReader(compressed))
decompressed, err := io.ReadAll(zr)
zr.Close()

// Encoding binary data
type Person struct {
    Name string
    Age  int
}

// With gob
var buf bytes.Buffer
enc := gob.NewEncoder(&buf)
err := enc.Encode(person)
encoded := buf.Bytes()

var decoded Person
dec := gob.NewDecoder(bytes.NewReader(encoded))
err := dec.Decode(&decoded)

// With msgpack (3rd party)
import "github.com/vmihailenco/msgpack/v5"

encoded, err := msgpack.Marshal(person)
var decoded Person
err := msgpack.Unmarshal(encoded, &decoded)
```

### Working with Databases

```go
// SQL databases with database/sql
import (
    "database/sql"
    _ "github.com/lib/pq" // PostgreSQL driver
)

// Connect to database
db, err := sql.Open("postgres", "user=postgres dbname=mydb sslmode=disable")
if err != nil {
    log.Fatal(err)
}
defer db.Close()

// Ping the database
if err := db.Ping(); err != nil {
    log.Fatal(err)
}

// Execute a query
rows, err := db.Query("SELECT id, name FROM users WHERE age > $1", 18)
if err != nil {
    log.Fatal(err)
}
defer rows.Close()

// Iterate through rows
for rows.Next() {
    var id int
    var name string
    if err := rows.Scan(&id, &name); err != nil {
        log.Fatal(err)
    }
    fmt.Printf("ID: %d, Name: %s\n", id, name)
}

// Check for errors after iteration
if err := rows.Err(); err != nil {
    log.Fatal(err)
}

// Execute a statement
result, err := db.Exec("UPDATE users SET name = $1 WHERE id = $2", "John", 1)
if err != nil {
    log.Fatal(err)
}

// Get affected rows
affected, err := result.RowsAffected()

// Prepare a statement (reusable)
stmt, err := db.Prepare("INSERT INTO users(name, age) VALUES($1, $2)")
if err != nil {
    log.Fatal(err)
}
defer stmt.Close()

// Execute prepared statement multiple times
for _, user := range users {
    _, err := stmt.Exec(user.Name, user.Age)
    if err != nil {
        log.Fatal(err)
    }
}

// Transactions
tx, err := db.Begin()
if err != nil {
    log.Fatal(err)
}

// Execute statements within transaction
_, err = tx.Exec("INSERT INTO users(name) VALUES($1)", "John")
if err != nil {
    tx.Rollback()
    log.Fatal(err)
}

_, err = tx.Exec("UPDATE accounts SET balance = balance - $1 WHERE user_id = $2", 100, 1)
if err != nil {
    tx.Rollback()
    log.Fatal(err)
}

// Commit transaction
if err := tx.Commit(); err != nil {
    log.Fatal(err)
}
```

## Security Best Practices

````go
// Password hashing (with bcrypt)
import "golang.org/x/crypto/bcrypt"

// Hash a password
password := "mysecretpassword"
hashedPassword, err := bcrypt.GenerateFromPassword([]byte(password), bcrypt.DefaultCost)
if err != nil {
    log.Fatal(err)
}

// Verify a password
err = bcrypt.CompareHashAndPassword(hashedPassword, []byte(password))
if err == nil {
    fmt.Println("Password is correct!")
} else {
    fmt.Println("Password is incorrect!")
}

// Secure random numbers
import "crypto/rand"

// Generate random bytes
bytes := make([]byte, 32)
if _, err := rand.Read(bytes); err != nil {
    log.Fatal(err)
}

// Generate random string (e.g., token)
const letters = "abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789"
b := make([]byte, 32)
if _, err := rand.Read(b); err != nil {
    log.Fatal(err)
}

token := make([]byte, 32)
for i := range token {
    token[i] = letters[b[i]%byte(len(letters))]
}

// Input validation
import "regexp"

// Validate email
emailRegex := regexp.MustCompile(`^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}`)

// Single variable declaration
var name string
name = "John"

// Declaration with initialization
var name string = "John"

// Type inference
var name = "John"

// Short declaration (only inside functions)
name := "John"

// Multiple variable declaration
var a, b, c int
a, b, c = 1, 2, 3

// Multiple variable with type inference
var a, b, c = 1, 2, "three"

// Multiple variable short declaration
a, b, c := 1, 2, "three"

// Declaration with initialization block
var (
    name   string = "John"
    age    int    = 30
    active bool   = true
)
````

## Command Line Tools and Utilities

```go
// Useful Go commands
// Build a binary
go build -o myapp main.go

// Run a program
go run main.go

// Install a package to $GOPATH/bin
go install github.com/user/package@latest

// Format code
go fmt ./...

// Check code for potential problems
go vet ./...

// Analyze code for potential bugs and suggestions
go install golang.org/x/tools/go/analysis/passes/shadow/cmd/shadow@latest
shadow ./...

// Run tests
go test ./...
go test -v ./...
go test -cover ./...

// Generate code coverage report
go test -coverprofile=coverage.out ./...
go tool cover -html=coverage.out

// Trace execution
go test -trace=trace.out
go tool trace trace.out

// Clean build cache
go clean -cache

// Document packages
go doc fmt.Println

// Static analysis with golangci-lint (third-party tool)
golangci-lint run
```

## Cross-Compilation

```go
// Build for a different OS/architecture
GOOS=linux GOARCH=amd64 go build -o myapp-linux-amd64 main.go
GOOS=windows GOARCH=amd64 go build -o myapp-windows-amd64.exe main.go
GOOS=darwin GOARCH=amd64 go build -o myapp-mac-amd64 main.go

// Common GOOS values
// android, darwin, dragonfly, freebsd, linux, netbsd, openbsd, plan9, solaris, windows

// Common GOARCH values
// 386, amd64, arm, arm64, ppc64, ppc64le, mips, mipsle, mips64, mips64le, s390x

// Build with CGO disabled (static binary)
CGO_ENABLED=0 go build -o myapp main.go

// Reduce binary size with flags
go build -ldflags="-s -w" -o myapp main.go
// -s: Disables symbol table
// -w: Disables DWARF generation
```

## Common Gotchas and Tips

```go
// Closing channels
ch := make(chan int)
// Close when done sending
close(ch)
// Check if channel is closed when receiving
val, ok := <-ch
if !ok {
    // Channel is closed
}

// Nil slice vs empty slice
var s1 []int         // nil slice, s1 == nil, len(s1) == 0
s2 := []int{}        // empty slice, s2 != nil, len(s2) == 0
s3 := make([]int, 0) // empty slice, s3 != nil, len(s3) == 0

// Loop variable capture in goroutines
// Wrong way:
for i := 0; i < 10; i++ {
    go func() {
        fmt.Println(i) // All might print the same value (the last one)
    }()
}

// Correct way:
for i := 0; i < 10; i++ {
    i := i // Create a new variable in each iteration
    go func() {
        fmt.Println(i)
    }()
}

// Or pass as parameter:
for i := 0; i < 10; i++ {
    go func(i int) {
        fmt.Println(i)
    }(i)
}

// Avoid data races with proper synchronization
var counter int
var mu sync.Mutex

// Increment safely
mu.Lock()
counter++
mu.Unlock()

// Detect data races with race detector
// go run -race main.go
// go test -race ./...

// Handle errors properly
// Don't ignore errors
_, err := SomeFunction()
if err != nil {
    // Do something with the error
    return err
}

// Use context for cancellation/timeouts
ctx, cancel := context.WithTimeout(context.Background(), 2*time.Second)
defer cancel() // Always call cancel to avoid context leak

// Don't use global variables when avoidable
// Use dependency injection instead

// Mind map capacity for large datasets
// Pre-allocate when size is known
data := make([]int, 0, expectedSize)
```

## Web Development

### HTTP Middleware

```go
// Basic middleware pattern
type Middleware func(http.Handler) http.Handler

// Logger middleware
func LoggerMiddleware(next http.Handler) http.Handler {
    return http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
        start := time.Now()
        next.ServeHTTP(w, r)
        log.Printf("%s %s %s", r.Method, r.RequestURI, time.Since(start))
    })
}

// Auth middleware
func AuthMiddleware(next http.Handler) http.Handler {
    return http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
        token := r.Header.Get("Authorization")
        if !isValidToken(token) {
            http.Error(w, "Unauthorized", http.StatusUnauthorized)
            return
        }
        next.ServeHTTP(w, r)
    })
}

// Chaining middleware
func Chain(h http.Handler, middleware ...Middleware) http.Handler {
    for _, m := range middleware {
        h = m(h)
    }
    return h
}

// Usage
handler := http.HandlerFunc(myHandler)
http.Handle("/", Chain(handler, LoggerMiddleware, AuthMiddleware))
```

### API Design Patterns

```go
// RESTful handlers
type UserHandler struct {
    UserService UserService
}

func (h *UserHandler) GetUser(w http.ResponseWriter, r *http.Request) {
    userID := chi.URLParam(r, "id")
    
    user, err := h.UserService.GetByID(r.Context(), userID)
    if err != nil {
        if errors.Is(err, ErrUserNotFound) {
            http.Error(w, "User not found", http.StatusNotFound)
            return
        }
        http.Error(w, "Internal server error", http.StatusInternalServerError)
        return
    }
    
    w.Header().Set("Content-Type", "application/json")
    json.NewEncoder(w).Encode(user)
}

// Routing with gorilla/mux
router := mux.NewRouter()
router.HandleFunc("/users", GetUsers).Methods("GET")
router.HandleFunc("/users/{id}", GetUser).Methods("GET")
router.HandleFunc("/users", CreateUser).Methods("POST")
router.HandleFunc("/users/{id}", UpdateUser).Methods("PUT")
router.HandleFunc("/users/{id}", DeleteUser).Methods("DELETE")

// Routing with chi
router := chi.NewRouter()
router.Use(middleware.Logger)
router.Use(middleware.Recoverer)

router.Route("/users", func(r chi.Router) {
    r.Get("/", GetUsers)
    r.Post("/", CreateUser)
    r.Route("/{id}", func(r chi.Router) {
        r.Get("/", GetUser)
        r.Put("/", UpdateUser)
        r.Delete("/", DeleteUser)
    })
})

// API versioning
router.Route("/api", func(r chi.Router) {
    r.Route("/v1", func(r chi.Router) {
        r.Mount("/users", usersV1Router())
    })
    r.Route("/v2", func(r chi.Router) {
        r.Mount("/users", usersV2Router())
    })
})

// Content negotiation
func GetUser(w http.ResponseWriter, r *http.Request) {
    user, err := userService.GetUser(r.Context(), userID)
    if err != nil {
        http.Error(w, err.Error(), http.StatusInternalServerError)
        return
    }
    
    accept := r.Header.Get("Accept")
    switch {
    case strings.Contains(accept, "application/json"):
        w.Header().Set("Content-Type", "application/json")
        json.NewEncoder(w).Encode(user)
    case strings.Contains(accept, "application/xml"):
        w.Header().Set("Content-Type", "application/xml")
        xml.NewEncoder(w).Encode(user)
    default:
        w.Header().Set("Content-Type", "application/json")
        json.NewEncoder(w).Encode(user)
    }
}
```

### Templates

```go
// Parse a template
tmpl, err := template.ParseFiles("layout.html", "content.html")
if err != nil {
    log.Fatal(err)
}

// Parse multiple templates
tmpl, err := template.ParseGlob("templates/*.html")
if err != nil {
    log.Fatal(err)
}

// Execute a template
data := struct {
    Title string
    Users []User
}{
    Title: "User List",
    Users: []User{
        {Name: "John", Age: 30},
        {Name: "Alice", Age: 25},
    },
}
err = tmpl.Execute(w, data)
if err != nil {
    log.Fatal(err)
}

// Execute a specific template
err = tmpl.ExecuteTemplate(w, "user-page.html", data)
if err != nil {
    log.Fatal(err)
}

// Template functions
funcMap := template.FuncMap{
    "upper": strings.ToUpper,
    "formatDate": func(t time.Time) string {
        return t.Format("2006-01-02")
    },
}

tmpl, err := template.New("page.html").Funcs(funcMap).ParseFiles("page.html")

// Template with HTML escaping (default)
// In HTML: {{ .UserContent }}

// Template with unescaped HTML (use with caution)
// In HTML: {{ .TrustedHTML | safeHTML }}
funcMap := template.FuncMap{
    "safeHTML": func(s string) template.HTML {
        return template.HTML(s)
    },
}
```

## Cloud and Microservices

### Service Discovery and Configuration

```go
// Service registration and discovery with Consul
import "github.com/hashicorp/consul/api"

// Register service
client, _ := api.NewClient(api.DefaultConfig())
registration := &api.AgentServiceRegistration{
    ID:      "service-1",
    Name:    "web",
    Port:    8080,
    Address: "192.168.1.100",
    Check: &api.AgentServiceCheck{
        HTTP:     "http://192.168.1.100:8080/health",
        Interval: "10s",
        Timeout:  "1s",
    },
}
client.Agent().ServiceRegister(registration)

// Discover services
services, _, _ := client.Catalog().Service("web", "", nil)
for _, service := range services {
    fmt.Printf("ID: %s, Address: %s, Port: %d\n", service.ServiceID, service.ServiceAddress, service.ServicePort)
}

// Configuration with environment variables and Viper
import "github.com/spf13/viper"

func LoadConfig() (*Config, error) {
    viper.SetConfigName("config")
    viper.SetConfigType("yaml")
    viper.AddConfigPath(".")
    viper.AddConfigPath("./config")
    viper.AutomaticEnv()
    viper.SetEnvKeyReplacer(strings.NewReplacer(".", "_"))
    
    if err := viper.ReadInConfig(); err != nil {
        return nil, err
    }
    
    var config Config
    if err := viper.Unmarshal(&config); err != nil {
        return nil, err
    }
    
    return &config, nil
}
```

### Containerization

```go
// Example Dockerfile for Go application
FROM golang:1.20-alpine AS builder

WORKDIR /app
COPY go.mod go.sum ./
RUN go mod download

COPY . .
RUN CGO_ENABLED=0 GOOS=linux go build -o app ./cmd/server

FROM alpine:3.17
WORKDIR /app
COPY --from=builder /app/app .
COPY --from=builder /app/config ./config

EXPOSE 8080
CMD ["/app/app"]

// Multistage Dockerfile with minimal final image
FROM golang:1.20-alpine AS builder

WORKDIR /app
COPY go.mod go.sum ./
RUN go mod download

COPY . .
RUN CGO_ENABLED=0 GOOS=linux go build -ldflags="-s -w" -o app ./cmd/server

FROM scratch
COPY --from=builder /app/app /app
COPY --from=builder /etc/ssl/certs/ca-certificates.crt /etc/ssl/certs/

EXPOSE 8080
CMD ["/app"]
```

### Metrics and Monitoring

```go
// Prometheus metrics
import "github.com/prometheus/client_golang/prometheus"
import "github.com/prometheus/client_golang/prometheus/promhttp"

// Define metrics
var (
    httpRequestsTotal = prometheus.NewCounterVec(
        prometheus.CounterOpts{
            Name: "http_requests_total",
            Help: "Total number of HTTP requests",
        },
        []string{"method", "path", "status"},
    )
    
    httpRequestDuration = prometheus.NewHistogramVec(
        prometheus.HistogramOpts{
            Name:    "http_request_duration_seconds",
            Help:    "HTTP request duration in seconds",
            Buckets: prometheus.DefBuckets,
        },
        []string{"method", "path", "status"},
    )
)

func init() {
    // Register metrics
    prometheus.MustRegister(httpRequestsTotal)
    prometheus.MustRegister(httpRequestDuration)
}

// Metrics middleware
func MetricsMiddleware(next http.Handler) http.Handler {
    return http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
        start := time.Now()
        
        // Create response wrapper to capture status code
        rww := &responseWriterWrapper{ResponseWriter: w, statusCode: http.StatusOK}
        
        next.ServeHTTP(rww, r)
        
        // Record metrics
        duration := time.Since(start).Seconds()
        httpRequestsTotal.WithLabelValues(r.Method, r.URL.Path, fmt.Sprintf("%d", rww.statusCode)).Inc()
        httpRequestDuration.WithLabelValues(r.Method, r.URL.Path, fmt.Sprintf("%d", rww.statusCode)).Observe(duration)
    })
}

// Expose metrics endpoint
http.Handle("/metrics", promhttp.Handler())
```

## Design Patterns in Go

### Creational Patterns

```go
// Singleton pattern
type Singleton struct {
    // fields
}

var (
    instance *Singleton
    once     sync.Once
)

func GetInstance() *Singleton {
    once.Do(func() {
        instance = &Singleton{}
    })
    return instance
}

// Factory pattern
type Payment interface {
    Pay(amount float64) string
}

type CreditCardPayment struct{}
func (p *CreditCardPayment) Pay(amount float64) string {
    return fmt.Sprintf("Paid %.2f using credit card", amount)
}

type PayPalPayment struct{}
func (p *PayPalPayment) Pay(amount float64) string {
    return fmt.Sprintf("Paid %.2f using PayPal", amount)
}

func CreatePayment(method string) Payment {
    switch method {
    case "creditcard":
        return &CreditCardPayment{}
    case "paypal":
        return &PayPalPayment{}
    default:
        return nil
    }
}

// Builder pattern
type House struct {
    Windows int
    Doors   int
    Rooms   int
    HasGarage bool
    HasSwimmingPool bool
}

type HouseBuilder struct {
    house House
}

func NewHouseBuilder() *HouseBuilder {
    return &HouseBuilder{house: House{}}
}

func (b *HouseBuilder) Windows(count int) *HouseBuilder {
    b.house.Windows = count
    return b
}

func (b *HouseBuilder) Doors(count int) *HouseBuilder {
    b.house.Doors = count
    return b
}

func (b *HouseBuilder) Rooms(count int) *HouseBuilder {
    b.house.Rooms = count
    return b
}

func (b *HouseBuilder) WithGarage() *HouseBuilder {
    b.house.HasGarage = true
    return b
}

func (b *HouseBuilder) WithSwimmingPool() *HouseBuilder {
    b.house.HasSwimmingPool = true
    return b
}

func (b *HouseBuilder) Build() House {
    return b.house
}

// Usage
house := NewHouseBuilder().
    Windows(10).
    Doors(4).
    Rooms(5).
    WithGarage().
    Build()
```

### Structural Patterns

```go
// Adapter pattern
type LegacyPrinter interface {
    Print(s string) string
}

type ModernPrinter interface {
    PrintStored() string
}

type LegacyPrinterImpl struct{}

func (l *LegacyPrinterImpl) Print(s string) string {
    return fmt.Sprintf("Legacy: %s", s)
}

type PrinterAdapter struct {
    LegacyPrinter
    Msg string
}

func (p *PrinterAdapter) PrintStored() string {
    return p.Print(p.Msg)
}

// Decorator pattern
type Component interface {
    Operation() string
}

type ConcreteComponent struct{}

func (c *ConcreteComponent) Operation() string {
    return "ConcreteComponent"
}

type Decorator struct {
    component Component
}

func (d *Decorator) Operation() string {
    return d.component.Operation()
}

type ConcreteDecoratorA struct {
    Decorator
}

func NewConcreteDecoratorA(c Component) *ConcreteDecoratorA {
    return &ConcreteDecoratorA{Decorator: Decorator{component: c}}
}

func (d *ConcreteDecoratorA) Operation() string {
    return fmt.Sprintf("ConcreteDecoratorA(%s)", d.Decorator.Operation())
}

// Usage
component := &ConcreteComponent{}
decorator := NewConcreteDecoratorA(component)
result := decorator.Operation() // "ConcreteDecoratorA(ConcreteComponent)"
```

### Behavioral Patterns

```go
// Observer pattern
type Observer interface {
    Update(data string)
}

type Subject interface {
    Register(observer Observer)
    Deregister(observer Observer)
    NotifyAll()
}

type ConcreteObserver struct {
    ID string
}

func (o *ConcreteObserver) Update(data string) {
    fmt.Printf("Observer %s received: %s\n", o.ID, data)
}

type ConcreteSubject struct {
    observers []Observer
    data      string
}

func (s *ConcreteSubject) Register(observer Observer) {
    s.observers = append(s.observers, observer)
}

func (s *ConcreteSubject) Deregister(observer Observer) {
    for i, o := range s.observers {
        if o == observer {
            s.observers = append(s.observers[:i], s.observers[i+1:]...)
            break
        }
    }
}

func (s *ConcreteSubject) NotifyAll() {
    for _, observer := range s.observers {
        observer.Update(s.data)
    }
}

func (s *ConcreteSubject) SetData(data string) {
    s.data = data
    s.NotifyAll()
}

// Strategy pattern
type Strategy interface {
    Execute(a, b int) int
}

type AddStrategy struct{}

func (s *AddStrategy) Execute(a, b int) int {
    return a + b
}

type SubtractStrategy struct{}

func (s *SubtractStrategy) Execute(a, b int) int {
    return a - b
}

type MultiplyStrategy struct{}

func (s *MultiplyStrategy) Execute(a, b int) int {
    return a * b
}

type Context struct {
    strategy Strategy
}

func (c *Context) SetStrategy(strategy Strategy) {
    c.strategy = strategy
}

func (c *Context) ExecuteStrategy(a, b int) int {
    return c.strategy.Execute(a, b)
}

// Usage
context := &Context{}

context.SetStrategy(&AddStrategy{})
result := context.ExecuteStrategy(5, 3) // 8

context.SetStrategy(&SubtractStrategy{})
result = context.ExecuteStrategy(5, 3) // 2
```

## Go Assembly and Low-Level Programming

```go
// Using assembly in Go (example of a function in assembly)
// File: add.go
package myasm

// Add two integers
//go:noinline
func Add(a, b int64) int64

// File: add_amd64.s
TEXT ·Add(SB), $0-24
    MOVQ a+0(FP), AX  // Load first parameter into AX
    MOVQ b+8(FP), BX  // Load second parameter into BX
    ADDQ BX, AX       // Add BX to AX
    MOVQ AX, ret+16(FP) // Store result
    RET

// Using unsafe for low-level operations
import "unsafe"

// Convert []byte to string without copying
func BytesToString(b []byte) string {
    return *(*string)(unsafe.Pointer(&b))
}

// Convert string to []byte without copying
func StringToBytes(s string) []byte {
    return *(*[]byte)(unsafe.Pointer(
        &struct {
            string
            Cap int
        }{s, len(s)},
    ))
}

// Get pointer address
ptr := unsafe.Pointer(&myVar)
addr := uintptr(ptr)

// Pointer arithmetic (VERY DANGEROUS, USE WITH CAUTION)
type MyStruct struct {
    Field1 int
    Field2 string
}

s := MyStruct{Field1: 42, Field2: "hello"}
p := unsafe.Pointer(&s)

// Access field directly
field1Ptr := (*int)(p)
fmt.Println(*field1Ptr) // 42

// Access second field with offset
field2Ptr := (*string)(unsafe.Pointer(uintptr(p) + unsafe.Offsetof(s.Field2)))
fmt.Println(*field2Ptr) // "hello"
```

## Go with WebAssembly

```go
// Build a Go program for WebAssembly
// GOOS=js GOARCH=wasm go build -o main.wasm main.go

// Example main.go for WebAssembly
package main

import (
    "syscall/js"
)

func add(this js.Value, args []js.Value) interface{} {
    a := args[0].Int()
    b := args[1].Int()
    return a + b
}

func main() {
    c := make(chan struct{}, 0)
    
    // Register functions to be called from JavaScript
    js.Global().Set("goAdd", js.FuncOf(add))
    
    // Print a message to the JavaScript console
    js.Global().Get("console").Call("log", "Go WebAssembly initialized")
    
    // Keep the Go program running
    <-c
}

// HTML to load WebAssembly
/*
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <title>Go WebAssembly</title>
    <script src="wasm_exec.js"></script>
    <script>
        const go = new Go();
        WebAssembly.instantiateStreaming(fetch("main.wasm"), go.importObject)
            .then((result) => {
                go.run(result.instance);
                
                // Call Go function from JavaScript
                const result = goAdd(5, 3);
                console.log("Result:", result);
            });
    </script>
</head>
<body>
    <h1>Go WebAssembly Example</h1>
</body>
</html>
*/
```

## Specialized Libraries and Tools

### Networking and Protocols

```go
// gRPC server example
import (
    "google.golang.org/grpc"
    pb "myproject/proto"
)

type server struct {
    pb.UnimplementedGreeterServer
}

func (s *server) SayHello(ctx context.Context, req *pb.HelloRequest) (*pb.HelloReply, error) {
    return &pb.HelloReply{Message: "Hello " + req.Name}, nil
}

func main() {
    lis, err := net.Listen("tcp", ":50051")
    if err != nil {
        log.Fatalf("failed to listen: %v", err)
    }
    
    s := grpc.NewServer()
    pb.RegisterGreeterServer(s, &server{})
    
    if err := s.Serve(lis); err != nil {
        log.Fatalf("failed to serve: %v", err)
    }
}

// gRPC client example
conn, err := grpc.Dial("localhost:50051", grpc.WithInsecure())
if err != nil {
    log.Fatalf("did not connect: %v", err)
}
defer conn.Close()

client := pb.NewGreeterClient(conn)
ctx, cancel := context.WithTimeout(context.Background(), time.Second)
defer cancel()

reply, err := client.SayHello(ctx, &pb.HelloRequest{Name: "World"})
if err != nil {
    log.Fatalf("could not greet: %v", err)
}
log.Printf("Greeting: %s", reply.Message)

// WebSocket server
import "github.com/gorilla/websocket"

var upgrader = websocket.Upgrader{
    ReadBufferSize:  1024,
    WriteBufferSize: 1024,
    CheckOrigin: func(r *http.Request) bool {
        return true // Allow all origins for demo
    },
}

func handleWebSocket(w http.ResponseWriter, r *http.Request) {
    conn, err := upgrader.Upgrade(w, r, nil)
    if err != nil {
        log.Println(err)
        return
    }
    defer conn.Close()
    
    for {
        messageType, p, err := conn.ReadMessage()
        if err != nil {
            log.Println(err)
            return
        }
        
        // Echo the message back
        if err := conn.WriteMessage(messageType, p); err != nil {
            log.Println(err)
            return
        }
    }
}

// WebSocket client
conn, _, err := websocket.DefaultDialer.Dial("ws://localhost:8080/ws", nil)
if err != nil {
    log.Fatal("dial:", err)
}
defer conn.Close()

// Send message
err = conn.WriteMessage(websocket.TextMessage, []byte("Hello"))
if err != nil {
    log.Println("write:", err)
    return
}

// Read response
_, message, err := conn.ReadMessage()
if err != nil {
    log.Println("read:", err)
    return
}
fmt.Printf("Received: %s\n", message)
```

### Working with Audio and Images

```go
// Image processing
import (
    "image"
    "image/color"
    "image/png"
    "os"
)

// Create a new image
img := image.NewRGBA(image.Rect(0, 0, 100, 100))

// Set pixel colors
for y := 0; y < 100; y++ {
    for x := 0; x < 100; x++ {
        img.Set(x, y, color.RGBA{uint8(x * 255 / 100), uint8(y * 255 / 100), 100, 255})
    }
}

// Save image to file
file, err := os.Create("image.png")
if err != nil {
    log.Fatal(err)
}
defer file.Close()

err = png.Encode(file, img)
if err != nil {
    log.Fatal(err)
}

// Load image from file
file, err := os.Open("input.png")
if err != nil {
    log.Fatal(err)
}
defer file.Close()

img, err := png.Decode(file)
if err != nil {
    log.Fatal(err)
}

// Get image dimensions
bounds := img.Bounds()
width, height := bounds.Max.X, bounds.Max.Y

// Process image
for y := 0; y < height; y++ {
    for x := 0; x < width; x++ {
        oldColor := img.At(x, y)
        r, g, b, a := oldColor.RGBA()
        // Convert to 8-bit color values
        r8, g8, b8, a8 := uint8(r>>8), uint8(g>>8), uint8(b>>8), uint8(a>>8)
        // Do something with the color...
    }
}

// Audio processing with portaudio
import "github.com/gordonklaus/portaudio"

func main() {
    portaudio.Initialize()
    defer portaudio.Terminate()
    
    // Open default stream
    stream, err := portaudio.OpenDefaultStream(0, 2, 44100, 0, func(out []float32) {
        // Generate audio samples
        for i := range out {
            out[i] = float32(0.5 * math.Sin(2*math.Pi*440*float64(i)/44100))
        }
    })
    if err != nil {
        log.Fatal(err)
    }
    
    err = stream.Start()
    if err != nil {
        log.Fatal(err)
    }
    
    time.Sleep(2 * time.Second)
    
    err = stream.Stop()
    if err != nil {
        log.Fatal(err)
    }
}
```

## Memory Management and Garbage Collection

```go
// Manually trigger garbage collection
import "runtime"

runtime.GC() // Force garbage collection

// Get memory statistics
var mem runtime.MemStats
runtime.ReadMemStats(&mem)

fmt.Printf("Allocated memory: %d bytes\n", mem.Alloc)
fmt.Printf("Total allocated memory: %d bytes\n", mem.TotalAlloc)
fmt.Printf("System memory: %d bytes\n", mem.Sys)
fmt.Printf("Number of garbage collections: %d\n", mem.NumGC)

// Set garbage collection target percentage
// (value is ratio of allocated to freed memory that triggers GC)
debug.SetGCPercent(100) // Default is 100

// Set max memory fraction (ratio of available memory to use before GC)
debug.SetMaxHeap(100 * 1024 * 1024) // 100MB

// Prevent strings from being interned
var myString = strings.Clone("example") // Go 1.18+ prevents string interning

// Memory profiling
f, err := os.Create("mem.pprof")
if err != nil {
    log.Fatal(err)
}
defer f.Close()

if err := pprof.WriteHeapProfile(f); err != nil {
    log.Fatal(err)
}

// Run with profiling
// go run -memprofile=mem.pprof main.go

// Analyze with pprof
// go tool pprof mem.pprof
// Or interactive web UI:
// go tool pprof -http=:8080 mem.pprof
```

## Golang Testing Best Practices

```go
// Table driven tests with subtests
func TestCalculator(t *testing.T) {
    tests := []struct {
        name     string
        a, b     int
        op       string
        expected int
        wantErr  bool
    }{
        {"addition", 2, 3, "+", 5, false},
        {"subtraction", 5, 3, "-", 2, false},
        {"multiplication", 2, 3, "*", 6, false},
        {"division", 6, 3, "/", 2, false},
        {"division by zero", 6, 0, "/", 0, true},
        {"unknown operation", 6, 3, "?", 0, true},
    }
    
    for _, tt := range tests {
        t.Run(tt.name, func(t *testing.T) {
            result, err := Calculate(tt.a, tt.b, tt.op)
            
            if tt.wantErr {
                assert.Error(t, err)
                return
            }
            
            assert.NoError(t, err)
            assert.Equal(t, tt.expected, result)
        })
    }
}

// Test helpers
func setupTestCase(t *testing.T) func(t *testing.T) {
    t.Log("Setup test case")
    
    // Create test resources
    db := createTestDatabase()
    
    // Return a function to be deferred for cleanup
    return func(t *testing.T) {
        t.Log("Tear down test case")
        db.Close()
    }
}

func TestSomething(t *testing.T) {
    teardown := setupTestCase(t)
    defer teardown(t)
    
    // Test code here
}

// HTTP handler testing
func TestHandler(t *testing.T) {
    // Create a request
    req, err := http.NewRequest("GET", "/users?page=2", nil)
    if err != nil {
        t.Fatal(err)
    }
    
    // Create a response recorder
    rr := httptest.NewRecorder()
    
    // Create handler
    handler := http.HandlerFunc(GetUsersHandler)
    
    // Serve request
    handler.ServeHTTP(rr, req)
    
    // Check status code
    assert.Equal(t, http.StatusOK, rr.Code)
    
    // Check response body
    expected := `{"users":[...]}`
    assert.JSONEq(t, expected, rr.Body.String())
}
```

### Mocking and Test Doubles

```go
// Mocking with testify/mock
type MockUserService struct {
    mock.Mock
}

func (m *MockUserService) GetUser(id string) (*User, error) {
    args := m.Called(id)
    
    // First return value is *User
    if user := args.Get(0); user != nil {
        return user.(*User), args.Error(1)
    }
    
    return nil, args.Error(1)
}

func TestGetUserHandler(t *testing.T) {
    // Create mock
    mockService := new(MockUserService)
    
    // Set expectations
    mockService.On("GetUser", "123").Return(&User{ID: "123", Name: "John"}, nil)
    mockService.On("GetUser", "404").Return(nil, errors.New("user not found"))
    
    // Create handler with mock
    handler := &UserHandler{Service: mockService}
    
    // Test successful case
    req := httptest.NewRequest("GET", "/users/123", nil)
    rr := httptest.NewRecorder()
    
    handler.GetUser(rr, req, map[string]string{"id": "123"})
    
    assert.Equal(t, http.StatusOK, rr.Code)
    // Further assertions...
    
    // Verify expectations were met
    mockService.AssertExpectations(t)
}

// Using test doubles (manual mocks)
type StubUserRepository struct {
    users map[string]*User
}

func NewStubUserRepository() *StubUserRepository {
    return &StubUserRepository{
        users: map[string]*User{
            "123": {ID: "123", Name: "John"},
            "456": {ID: "456", Name: "Jane"},
        },
    }
}

func (s *StubUserRepository) GetByID(id string) (*User, error) {
    user, exists := s.users[id]
    if !exists {
        return nil, errors.New("user not found")
    }
    return user, nil
}

// Testing with spies
type SpyEmailSender struct {
    sentEmails []struct {
        To      string
        Subject string
        Body    string
    }
}

func (s *SpyEmailSender) SendEmail(to, subject, body string) error {
    s.sentEmails = append(s.sentEmails, struct {
        To      string
        Subject string
        Body    string
    }{to, subject, body})
    return nil
}

func TestNotificationService(t *testing.T) {
    spy := &SpyEmailSender{}
    service := NewNotificationService(spy)
    
    service.NotifyUser("user@example.com", "Hello")
    
    assert.Len(t, spy.sentEmails, 1)
    assert.Equal(t, "user@example.com", spy.sentEmails[0].To)
    assert.Equal(t, "Notification", spy.sentEmails[0].Subject)
    assert.Contains(t, spy.sentEmails[0].Body, "Hello")
}
```

### Test Coverage and Profiling

```go
// Run tests with coverage
// go test -cover ./...

// Generate coverage profile
// go test -coverprofile=coverage.out ./...

// View coverage in browser
// go tool cover -html=coverage.out

// Combine coverage from multiple packages
// go test -coverprofile=coverage.out ./...
// For each additional package:
// go test -coverprofile=profile.out ./additional/package
// go tool cover -html=coverage.out -o coverage.html

// Test with benchmarking and profiling
// go test -bench=. -benchmem -cpuprofile=cpu.out -memprofile=mem.out ./...

// Analyze CPU profile
// go tool pprof cpu.out
// (pprof) top
// (pprof) web  # Opens visualization in browser if graphviz is installed

// Analyze memory profile
// go tool pprof mem.out
// (pprof) top
// (pprof) web

// Continuous profiling
import _ "net/http/pprof"

func main() {
    go func() {
        log.Println(http.ListenAndServe("localhost:6060", nil))
    }()
    
    // Your application code...
}

// Then access:
// http://localhost:6060/debug/pprof/
// Run:
// go tool pprof http://localhost:6060/debug/pprof/heap
// go tool pprof http://localhost:6060/debug/pprof/profile (30s CPU profile)
// go tool pprof http://localhost:6060/debug/pprof/goroutine
```

### Property-Based Testing

```go
// Using gopter for property-based testing
import (
    "testing"
    "github.com/leanovate/gopter"
    "github.com/leanovate/gopter/gen"
    "github.com/leanovate/gopter/prop"
)

func TestReverse(t *testing.T) {
    parameters := gopter.DefaultTestParameters()
    parameters.MinSuccessfulTests = 1000
    
    properties := gopter.NewProperties(parameters)
    
    properties.Property("reverse twice is identity", prop.ForAll(
        func(s string) bool {
            return Reverse(Reverse(s)) == s
        },
        gen.AnyString(),
    ))
    
    properties.Property("reverse concatenation", prop.ForAll(
        func(a string, b string) bool {
            return Reverse(a + b) == Reverse(b) + Reverse(a)
        },
        gen.AnyString(),
        gen.AnyString(),
    ))
    
    // Run the tests
    properties.TestingRun(t)
}

// Using rapid for property-based testing
import (
    "testing"
    "pgregory.net/rapid"
)

func TestReverseProperties(t *testing.T) {
    rapid.Check(t, func(t *rapid.T) {
        s := rapid.String().Draw(t, "string")
        
        // Property: reverse twice is identity
        if Reverse(Reverse(s)) != s {
            t.Fatalf("Reverse(Reverse(%q)) = %q, want %q", s, Reverse(Reverse(s)), s)
        }
    })
}
```

### Visual Regression Testing

```go
// Using chromedp for visual testing
import (
    "context"
    "io/ioutil"
    "testing"
    
    "github.com/chromedp/chromedp"
)

func TestVisualRegression(t *testing.T) {
    // Create a new Chrome instance
    ctx, cancel := chromedp.NewContext(context.Background())
    defer cancel()
    
    // Capture a screenshot
    var buf []byte
    err := chromedp.Run(ctx,
        chromedp.Navigate("http://localhost:8080/mypage"),
        chromedp.WaitVisible("#content", chromedp.ByID),
        chromedp.Screenshot("#content", &buf, chromedp.ByID),
    )
    if err != nil {
        t.Fatal(err)
    }
    
    // Save screenshot
    if err := ioutil.WriteFile("screenshot.png", buf, 0644); err != nil {
        t.Fatal(err)
    }
    
    // Compare with baseline (simplified)
    baselineData, err := ioutil.ReadFile("baseline.png")
    if err != nil {
        t.Fatal(err)
    }
    
    // In a real test, use an image comparison library
    if len(buf) != len(baselineData) {
        t.Errorf("Screenshot differs from baseline")
    }
}
```

### Contract Testing

```go
// Consumer-driven contract testing
type UserServiceContract struct {
    GetUser func(id string) (*User, error)
}

func TestUserServiceContract(t *testing.T) {
    // Define the contract
    contract := UserServiceContract{
        GetUser: func(id string) (*User, error) {
            if id == "123" {
                return &User{ID: "123", Name: "John"}, nil
            }
            return nil, errors.New("user not found")
        },
    }
    
    // Test consumer functionality using the contract
    consumer := NewUserConsumer(contract.GetUser)
    
    // Test consumer behavior
    user, err := consumer.GetUserInfo("123")
    assert.NoError(t, err)
    assert.Equal(t, "John", user.Name)
    
    // Test error handling
    _, err = consumer.GetUserInfo("invalid")
    assert.Error(t, err)
    
    // Export contract for provider verification
    exportContract(contract)
}

// Provider verification
func TestUserServiceImplementation(t *testing.T) {
    // Load the contract
    contract := loadContract()
    
    // Real implementation
    service := NewUserService(NewUserRepository())
    
    // Verify contract expectations
    for id, expectation := range contract.GetUserExpectations {
        user, err := service.GetUser(id)
        
        if expectation.ShouldError {
            assert.Error(t, err)
        } else {
            assert.NoError(t, err)
            assert.Equal(t, expectation.ExpectedUser.ID, user.ID)
            assert.Equal(t, expectation.ExpectedUser.Name, user.Name)
        }
    }
}
```

## Advanced Concurrency Patterns

### Fan-out, Fan-in

```go
// Fan-out, fan-in pattern
func merge(cs ...<-chan int) <-chan int {
    var wg sync.WaitGroup
    out := make(chan int)
    
    // Function to copy from a channel to the output channel
    output := func(c <-chan int) {
        defer wg.Done()
        for n := range c {
            out <- n
        }
    }
    
    wg.Add(len(cs))
    // Fan-in from multiple input channels
    for _, c := range cs {
        go output(c)
    }
    
    // Close output channel when all inputs are done
    go func() {
        wg.Wait()
        close(out)
    }()
    
    return out
}

// Usage example
func main() {
    // Single input channel with numbers
    in := make(chan int)
    go func() {
        for i := 0; i < 10; i++ {
            in <- i
        }
        close(in)
    }()
    
    // Fan out to multiple workers (creating multiple channels)
    c1 := square(in)
    c2 := square(in)
    c3 := square(in)
    
    // Fan in from multiple channels to a single output channel
    for n := range merge(c1, c2, c3) {
        fmt.Println(n)
    }
}

// Processing function
func square(in <-chan int) <-chan int {
    out := make(chan int)
    go func() {
        defer close(out)
        for n := range in {
            time.Sleep(100 * time.Millisecond) // Simulate work
            out <- n * n
        }
    }()
    return out
}
```

### Context Cancellation

```go
// Cancellation with context
func doWorkWithTimeout(ctx context.Context) (Result, error) {
    ctx, cancel := context.WithTimeout(ctx, 2*time.Second)
    defer cancel()
    
    resultCh := make(chan Result, 1)
    errCh := make(chan error, 1)
    
    go func() {
        result, err := doWork()
        if err != nil {
            errCh <- err
            return
        }
        resultCh <- result
    }()
    
    select {
    case result := <-resultCh:
        return result, nil
    case err := <-errCh:
        return Result{}, err
    case <-ctx.Done():
        return Result{}, ctx.Err()
    }
}

// Propagating cancellation
func ProcessItems(ctx context.Context, items []Item) error {
    for _, item := range items {
        select {
        case <-ctx.Done():
            return ctx.Err()
        default:
            if err := processItem(ctx, item); err != nil {
                return err
            }
        }
    }
    return nil
}

// Cancellation with channels
func timeoutFunc(timeout time.Duration) (func(), <-chan bool) {
    done := make(chan bool)
    cancel := func() {
        close(done)
    }
    
    go func() {
        select {
        case <-done:
        case <-time.After(timeout):
            close(done)
        }
    }()
    
    return cancel, done
}

// Usage
func main() {
    cancel, done := timeoutFunc(5 * time.Second)
    defer cancel()
    
    for {
        select {
        case <-done:
            fmt.Println("Timeout or cancelled")
            return
        default:
            // Do work
            time.Sleep(100 * time.Millisecond)
        }
    }
}
```

### Rate Limiting

```go
// Simple rate limiting
func rateLimitedWorker(requests <-chan Request, results chan<- Result, rateLimit time.Duration) {
    throttle := time.NewTicker(rateLimit)
    defer throttle.Stop()
    
    for req := range requests {
        <-throttle.C // Wait for tick before processing
        result := processRequest(req)
        results <- result
    }
}

// Token bucket rate limiting
import "golang.org/x/time/rate"

func handleRequests() {
    // Allow 10 requests per second with a burst of 30
    limiter := rate.NewLimiter(10, 30)
    
    http.HandleFunc("/api", func(w http.ResponseWriter, r *http.Request) {
        // Wait for permission
        if err := limiter.Wait(r.Context()); err != nil {
            http.Error(w, "Too Many Requests", http.StatusTooManyRequests)
            return
        }
        
        // Process the request
        fmt.Fprintf(w, "Request processed at %v\n", time.Now())
    })
    
    http.ListenAndServe(":8080", nil)
}

// Rate limiting with worker pool
func rateLimitedWorkerPool(numWorkers int, rateLimit time.Duration) {
    requests := make(chan Request)
    limiter := time.NewTicker(rateLimit)
    defer limiter.Stop()
    
    // Launch workers
    var wg sync.WaitGroup
    for i := 0; i < numWorkers; i++ {
        wg.Add(1)
        go func(id int) {
            defer wg.Done()
            for req := range requests {
                <-limiter.C // Rate limit across all workers
                processRequest(req)
                fmt.Printf("Worker %d processed request %v\n", id, req)
            }
        }(i)
    }
    
    // Send requests
    for i := 0; i < 100; i++ {
        requests <- Request{ID: i}
    }
    close(requests)
    
    wg.Wait()
}
```

### Work Stealing

```go
// Work stealing pattern
type Task func()

// Worker represents a worker in the pool
type Worker struct {
    id          int
    tasks       chan Task // Local task queue
    globalTasks chan Task // Global task queue
    steal       chan chan Task // Channel to receive a victim's task queue for stealing
    done        chan struct{}  // Signal to stop the worker
}

// NewWorker creates a new worker
func NewWorker(id int, globalTasks chan Task, steal chan chan Task) *Worker {
    return &Worker{
        id:          id,
        tasks:       make(chan Task, 32), // Local queue with buffer
        globalTasks: globalTasks,
        steal:       steal,
        done:        make(chan struct{}),
    }
}

// Start the worker
func (w *Worker) Start() {
    go func() {
        for {
            // Try to get a task from local queue first
            select {
            case task := <-w.tasks:
                task()
                continue
            case <-w.done:
                return
            default:
                // Local queue is empty
            }
            
            // Try to get a task from the global queue
            select {
            case task := <-w.globalTasks:
                task()
            case victim := <-w.steal:
                // Try to steal from another worker
                select {
                case task := <-victim:
                    task()
                default:
                    // No tasks to steal
                }
            case <-w.done:
                return
            default:
                // No tasks available, continue
                time.Sleep(10 * time.Millisecond)
            }
        }
    }()
}

// Stop the worker
func (w *Worker) Stop() {
    close(w.done)
}

// Submit a task to the worker
func (w *Worker) Submit(task Task) {
    select {
    case w.tasks <- task:
        // Task submitted to local queue
    default:
        // Local queue is full, submit to global queue
        w.globalTasks <- task
    }
}

// WorkPool is a pool of workers with work stealing
type WorkPool struct {
    workers     []*Worker
    globalTasks chan Task
    steal       chan chan Task
}

// NewWorkPool creates a new work pool
func NewWorkPool(numWorkers int) *WorkPool {
    globalTasks := make(chan Task, 128)
    steal := make(chan chan Task, numWorkers)
    
    pool := &WorkPool{
        workers:     make([]*Worker, numWorkers),
        globalTasks: globalTasks,
        steal:       steal,
    }
    
    // Create and start workers
    for i := 0; i < numWorkers; i++ {
        worker := NewWorker(i, globalTasks, steal)
        pool.workers[i] = worker
        worker.Start()
    }
    
    return pool
}

// Submit a task to the pool
func (p *WorkPool) Submit(task Task) {
    // Pick a random worker
    worker := p.workers[rand.Intn(len(p.workers))]
    worker.Submit(task)
}

// Stop all workers
func (p *WorkPool) Stop() {
    for _, worker := range p.workers {
        worker.Stop()
    }
}

// Usage
func main() {
    pool := NewWorkPool(4)
    defer pool.Stop()
    
    for i := 0; i < 100; i++ {
        i := i // Capture loop variable
        pool.Submit(func() {
            fmt.Printf("Processing task %d\n", i)
            time.Sleep(100 * time.Millisecond)
        })
    }
    
    time.Sleep(2 * time.Second) // Wait for tasks to complete
}
```

### Semaphores

```go
// Using channels as semaphores
func processWithLimit(items []Item, concurrencyLimit int) {
    sem := make(chan struct{}, concurrencyLimit)
    var wg sync.WaitGroup
    
    for _, item := range items {
        wg.Add(1)
        
        go func(item Item) {
            defer wg.Done()
            
            sem <- struct{}{} // Acquire semaphore
            defer func() { <-sem }() // Release semaphore
            
            // Process item (limited concurrency)
            processItem(item)
        }(item)
    }
    
    wg.Wait()
}

// Weighted semaphore for different resource costs
type WeightedSemaphore struct {
    capacity int64
    used     int64
    mu       sync.Mutex
    cond     *sync.Cond
}

func NewWeightedSemaphore(capacity int64) *WeightedSemaphore {
    sem := &WeightedSemaphore{capacity: capacity}
    sem.cond = sync.NewCond(&sem.mu)
    return sem
}

func (s *WeightedSemaphore) Acquire(weight int64) {
    s.mu.Lock()
    defer s.mu.Unlock()
    
    for s.used+weight > s.capacity {
        s.cond.Wait()
    }
    
    s.used += weight
}

func (s *WeightedSemaphore) Release(weight int64) {
    s.mu.Lock()
    defer s.mu.Unlock()
    
    s.used -= weight
    s.cond.Broadcast()
}

// Usage
func main() {
    sem := NewWeightedSemaphore(10)
    var wg sync.WaitGroup
    
    for i := 0; i < 5; i++ {
        wg.Add(1)
        go func(id int) {
            defer wg.Done()
            
            weight := int64(id + 1) // Different costs for different tasks
            fmt.Printf("Task %d acquiring %d resources\n", id, weight)
            
            sem.Acquire(weight)
            defer sem.Release(weight)
            
            fmt.Printf("Task %d running with %d resources\n", id, weight)
            time.Sleep(1 * time.Second)
            fmt.Printf("Task %d completed\n", id)
        }(i)
    }
    
    wg.Wait()
}
```

## Systems Programming in Go

### Working with OS Signals

```go
// Handling OS signals
func main() {
    sigs := make(chan os.Signal, 1)
    done := make(chan bool, 1)
    
    // Register for signals
    signal.Notify(sigs, syscall.SIGINT, syscall.SIGTERM)
    
    // Start signal handler
    go func() {
        sig := <-sigs
        fmt.Println("Received signal:", sig)
        // Perform cleanup
        fmt.Println("Cleaning up...")
        done <- true
    }()
    
    fmt.Println("Server started. Press Ctrl+C to stop")
    <-done
    fmt.Println("Server stopped")
}

// Context-based signal handling
func main() {
    // Create a context that cancels on SIGINT or SIGTERM
    ctx, stop := signal.NotifyContext(context.Background(), syscall.SIGINT, syscall.SIGTERM)
    defer stop()
    
    // Start server
    server := http.Server{
        Addr: ":8080",
        Handler: http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
            fmt.Fprintf(w, "Hello at %s\n", time.Now())
        }),
    }
    
    go func() {
        if err := server.ListenAndServe(); err != http.ErrServerClosed {
            log.Fatalf("HTTP server error: %v", err)
        }
    }()
    
    <-ctx.Done()
    fmt.Println("Shutting down server...")
    
    // Create shutdown context with timeout
    shutdownCtx, cancel := context.WithTimeout(context.Background(), 10*time.Second)
    defer cancel()
    
    if err := server.Shutdown(shutdownCtx); err != nil {
        log.Fatalf("HTTP server shutdown error: %v", err)
    }
    
    fmt.Println("Server gracefully stopped")
}
```

### Working with Files and Directories

```go
// Create directory if it doesn't exist
func ensureDir(dir string) error {
    if _, err := os.Stat(dir); os.IsNotExist(err) {
        return os.MkdirAll(dir, 0755)
    }
    return nil
}

// Recursive file walking
func walkDir(root string) error {
    return filepath.Walk(root, func(path string, info os.FileInfo, err error) error {
        if err != nil {
            return err
        }
        
        if info.IsDir() {
            fmt.Println("Directory:", path)
        } else {
            fmt.Println("File:", path, "Size:", info.Size())
        }
        
        return nil
    })
}

// Concurrent file walking with filepath.WalkDir (Go 1.16+)
func walkDirConcurrent(root string) error {
    // Use semaphore to limit concurrency
    sem := make(chan struct{}, 20)
    var wg sync.WaitGroup
    
    var walkDirInternal func(path string) error
    walkDirInternal = func(path string) error {
        entries, err := os.ReadDir(path)
        if err != nil {
            return err
        }
        
        for _, entry := range entries {
            entryPath := filepath.Join(path, entry.Name())
            
            if entry.IsDir() {
                fmt.Println("Directory:", entryPath)
                
                wg.Add(1)
                sem <- struct{}{} // Acquire
                go func(dirPath string) {
                    defer wg.Done()
                    defer func() { <-sem }() // Release
                    
                    if err := walkDirInternal(dirPath); err != nil {
                        fmt.Println("Error:", err)
                    }
                }(entryPath)
            } else {
                info, err := entry.Info()
                if err != nil {
                    return err
                }
                fmt.Println("File:", entryPath, "Size:", info.Size())
            }
        }
        
        return nil
    }
    
    err := walkDirInternal(root)
    wg.Wait()
    return err
}

// Working with temporary files
func workWithTempFile() error {
    // Create temp file
    tmpfile, err := os.CreateTemp("", "example-*.txt")
    if err != nil {
        return err
    }
    defer os.Remove(tmpfile.Name()) // Clean up
    defer tmpfile.Close()
    
    // Write to temp file
    if _, err := tmpfile.Write([]byte("Hello, World!")); err != nil {
        return err
    }
    
    // Rewind to beginning
    if _, err := tmpfile.Seek(0, 0); err != nil {
        return err
    }
    
    // Read from temp file
    data, err := io.ReadAll(tmpfile)
    if err != nil {
        return err
    }
    
    fmt.Printf("Read from temp file: %s\n", data)
    return nil
}

// File locking
type FileLock struct {
    file *os.File
    path string
}

func NewFileLock(path string) (*FileLock, error) {
    file, err := os.OpenFile(path, os.O_CREATE|os.O_WRONLY, 0644)
    if err != nil {
        return nil, err
    }
    
    return &FileLock{
        file: file,
        path: path,
    }, nil
}

func (l *FileLock) Lock() error {
    // syscall.LOCK_EX = exclusive lock, syscall.LOCK_NB = non-blocking
    err := syscall.Flock(int(l.file.Fd()), syscall.LOCK_EX)
    if err != nil {
        return fmt.Errorf("failed to acquire lock: %w", err)
    }
    return nil
}

func (l *FileLock) Unlock() error {
    return syscall.Flock(int(l.file.Fd()), syscall.LOCK_UN)
}

func (l *FileLock) Release() error {
    if err := l.Unlock(); err != nil {
        return err
    }
    return l.file.Close()
}
```

### Working with Processes

```go
// Execute a command
func runCommand(name string, args ...string) (string, error) {
    cmd := exec.Command(name, args...)
    output, err := cmd.CombinedOutput()
    return string(output), err
}

// Execute a command with pipes
func runCommandWithPipes(name string, args ...string) error {
    cmd := exec.Command(name, args...)
    
    // Connect pipes
    stdout, err := cmd.StdoutPipe()
    if err != nil {
        return err
    }
    
    stderr, err := cmd.StderrPipe()
    if err != nil {
        return err
    }
    
    // Start the command
    if err := cmd.Start(); err != nil {
        return err
    }
    
    // Read output asynchronously
    go func() {
        scanner := bufio.NewScanner(stdout)
        for scanner.Scan() {
            fmt.Println("OUT:", scanner.Text())
        }
    }()
    
    go func() {
        scanner := bufio.NewScanner(stderr)
        for scanner.Scan() {
            fmt.Println("ERR:", scanner.Text())
        }
    }()
    
    // Wait for command to finish
    return cmd.Wait()
}

// Execute a command with context (timeout)
func runCommandWithTimeout(ctx context.Context, timeout time.Duration, name string, args ...string) (string, error) {
    ctx, cancel := context.WithTimeout(ctx, timeout)
    defer cancel()
    
    cmd := exec.CommandContext(ctx, name, args...)
    output, err := cmd.CombinedOutput()
    
    return string(output), err
}

// Execute a shell command
func runShellCommand(command string) (string, error) {
    cmd := exec.Command("sh", "-c", command)
    output, err := cmd.CombinedOutput()
    return string(output), err
}

// Get process info
func getProcessInfo(pid int) error {
    process, err := os.FindProcess(pid)
    if err != nil {
        return err
    }
    
    // On Unix systems, FindProcess always succeeds, so we need to check the process exists
    if err := process.Signal(syscall.Signal(0)); err != nil {
        return fmt.Errorf("process %d does not exist", pid)
    }
    
    fmt.Printf("Process %d exists\n", pid)
    return nil
}
```

## Network Programming

### TCP Server/Client

```go
// TCP server
func startTCPServer(address string) error {
    // Listen for incoming connections
    listener, err := net.Listen("tcp", address)
    if err != nil {
        return err
    }
    defer listener.Close()
    
    fmt.Printf("TCP server listening on %s\n", address)
    
    for {
        // Accept a connection
        conn, err := listener.Accept()
        if err != nil {
            fmt.Println("Error accepting connection:", err)
            continue
        }
        
        // Handle the connection in a new goroutine
        go handleConnection(conn)
    }
}

func handleConnection(conn net.Conn) {
    defer conn.Close()
    
    // Set a deadline for read/write operations
    conn.SetDeadline(time.Now().Add(10 * time.Second))
    
    // Read data from the connection
    buffer := make([]byte, 1024)
    n, err := conn.Read(buffer)
    if err != nil {
        fmt.Println("Error reading from connection:", err)
        return
    }
    
    // Process the data
    data := buffer[:n]
    fmt.Printf("Received: %s\n", data)
    
    // Write a response
    response := fmt.Sprintf("Server received: %s", data)
    _, err = conn.Write([]byte(response))
    if err != nil {
        fmt.Println("Error writing to connection:", err)
        return
    }
}

// TCP client
func connectToTCPServer(address string, message string) (string, error) {
    // Connect to the server
    conn, err := net.Dial("tcp", address)
    if err != nil {
        return "", err
    }
    defer conn.Close()
    
    // Set a deadline for read/write operations
    conn.SetDeadline(time.Now().Add(5 * time.Second))
    
    // Send a message
    _, err = conn.Write([]byte(message))
    if err != nil {
        return "", err
    }
    
    // Read the response
    buffer := make([]byte, 1024)
    n, err := conn.Read(buffer)
    if err != nil {
        return "", err
    }
    
    return string(buffer[:n]), nil
}
```

### UDP Server/Client

```go
// UDP server
func startUDPServer(address string) error {
    // Parse the address
    addr, err := net.ResolveUDPAddr("udp", address)
    if err != nil {
        return err
    }
    
    // Create a UDP connection
    conn, err := net.ListenUDP("udp", addr)
    if err != nil {
        return err
    }
    defer conn.Close()
    
    fmt.Printf("UDP server listening on %s\n", address)
    
    buffer := make([]byte, 1024)
    for {
        // Read from UDP connection
        n, clientAddr, err := conn.ReadFromUDP(buffer)
        if err != nil {
            fmt.Println("Error reading from UDP:", err)
            continue
        }
        
        data := buffer[:n]
        fmt.Printf("Received %d bytes from %s: %s\n", n, clientAddr, data)
        
        // Send a response
        response := fmt.Sprintf("Server received: %s", data)
        _, err = conn.WriteToUDP([]byte(response), clientAddr)
        if err != nil {
            fmt.Println("Error writing to UDP:", err)
        }
    }
}

// UDP client
func sendUDPMessage(address string, message string) (string, error) {
    // Parse the server address
    serverAddr, err := net.ResolveUDPAddr("udp", address)
    if err != nil {
        return "", err
    }
    
    // Create a UDP connection
    conn, err := net.DialUDP("udp", nil, serverAddr)
    if err != nil {
        return "", err
    }
    defer conn.Close()
    
    // Set a read deadline
    conn.SetReadDeadline(time.Now().Add(5 * time.Second))
    
    // Send message
    _, err = conn.Write([]byte(message))
    if err != nil {
        return "", err
    }
    
    // Read response
    buffer := make([]byte, 1024)
    n, _, err := conn.ReadFromUDP(buffer)
    if err != nil {
        return "", err
    }
    
    return string(buffer[:n]), nil
}
```

### HTTP/2 and gRPC

```go
// HTTP/2 server with TLS
import (
    "crypto/tls"
    "golang.org/x/net/http2"
)

func startHTTP2Server() error {
    // Create a TLS config
    tlsConfig := &tls.Config{
        // In a real application, use proper certificates
        // This is for testing only
        Certificates: []tls.Certificate{generateSelfSignedCert()},
        NextProtos:   []string{"h2"},
    }
    
    // Create an HTTP server
    server := &http.Server{
        Addr:      ":8443",
        TLSConfig: tlsConfig,
        Handler: http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
            fmt.Fprintf(w, "Protocol: %s\n", r.Proto)
            fmt.Fprintf(w, "Path: %s\n", r.URL.Path)
        }),
    }
    
    // Enable HTTP/2
    http2.ConfigureServer(server, &http2.Server{})
    
    // Start the server
    return server.ListenAndServeTLS("", "")
}

// Generate a self-signed certificate for testing
func generateSelfSignedCert() tls.Certificate {
    // This is a simplified example
    // In production, use proper certificates
    key := `-----BEGIN PRIVATE KEY-----
    ...
    -----END PRIVATE KEY-----`
    
    cert := `-----BEGIN CERTIFICATE-----
    ...
    -----END CERTIFICATE-----`
    
    certificate, err := tls.X509KeyPair([]byte(cert), []byte(key))
    if err != nil {
        panic(err)
    }
    
    return certificate
}

// HTTP/2 client
func makeHTTP2Request(url string) (string, error) {
    // Create a transport that uses HTTP/2
    transport := &http.Transport{
        TLSClientConfig: &tls.Config{
            InsecureSkipVerify: true, // For testing only, don't use in production
        },
    }
    
    // Enable HTTP/2
    err := http2.ConfigureTransport(transport)
    if err != nil {
        return "", err
    }
    
    // Create client
    client := &http.Client{
        Transport: transport,
    }
    
    // Make a request
    resp, err := client.Get(url)
    if err != nil {
        return "", err
    }
    defer resp.Body.Close()
    
    // Read response
    body, err := io.ReadAll(resp.Body)
    if err != nil {
        return "", err
    }
    
    return string(body), nil
}

// gRPC server (using a generated proto service)
import (
    "google.golang.org/grpc"
    pb "myapp/proto"
)

type greeterServer struct {
    pb.UnimplementedGreeterServer
}

func (s *greeterServer) SayHello(ctx context.Context, req *pb.HelloRequest) (*pb.HelloReply, error) {
    return &pb.HelloReply{Message: "Hello " + req.Name}, nil
}

func startGRPCServer(address string) error {
    // Create a listener
    lis, err := net.Listen("tcp", address)
    if err != nil {
        return err
    }
    
    // Create a gRPC server
    server := grpc.NewServer()
    
    // Register the service
    pb.RegisterGreeterServer(server, &greeterServer{})
    
    // Start the server
    fmt.Printf("gRPC server listening on %s\n", address)
    return server.Serve(lis)
}

// gRPC client
func callGRPCService(address string, name string) (string, error) {
    // Create a connection
    conn, err := grpc.Dial(address, grpc.WithInsecure())
    if err != nil {
        return "", err
    }
    defer conn.Close()
    
    // Create a client
    client := pb.NewGreeterClient(conn)
    
    // Set a timeout
    ctx, cancel := context.WithTimeout(context.Background(), 5*time.Second)
    defer cancel()
    
    // Make the RPC call
    resp, err := client.SayHello(ctx, &pb.HelloRequest{Name: name})
    if err != nil {
        return "", err
    }
    
    return resp.Message, nil
}
```

### WebSockets

```go
// WebSocket server (using gorilla/websocket)
import "github.com/gorilla/websocket"

var upgrader = websocket.Upgrader{
    ReadBufferSize:  1024,
    WriteBufferSize: 1024,
    CheckOrigin: func(r *http.Request) bool {
        return true // Allow all origins for this example
    },
}

func handleWebSocket(w http.ResponseWriter, r *http.Request) {
    // Upgrade HTTP connection to WebSocket
    conn, err := upgrader.Upgrade(w, r, nil)
    if err != nil {
        fmt.Println("Error upgrading to WebSocket:", err)
        return
    }
    defer conn.Close()
    
    // Set read deadline
    conn.SetReadDeadline(time.Now().Add(60 * time.Second))
    
    // Set handlers
    conn.SetPongHandler(func(string) error {
        conn.SetReadDeadline(time.Now().Add(60 * time.Second))
        return nil
    })
    
    // Send a welcome message
    err = conn.WriteMessage(websocket.TextMessage, []byte("Welcome to the WebSocket server!"))
    if err != nil {
        fmt.Println("Error writing message:", err)
        return
    }
    
    // Message handling loop
    for {
        messageType, p, err := conn.ReadMessage()
        if err != nil {
            if websocket.IsUnexpectedCloseError(err, websocket.CloseGoingAway, websocket.CloseAbnormalClosure) {
                fmt.Println("Error reading message:", err)
            }
            break
        }
        
        fmt.Printf("Received message: %s\n", p)
        
        // Echo the message back
        err = conn.WriteMessage(messageType, p)
        if err != nil {
            fmt.Println("Error writing message:", err)
            break
        }
    }
}

// Start the WebSocket server
func startWebSocketServer(address string) error {
    http.HandleFunc("/ws", handleWebSocket)
    
    fmt.Printf("WebSocket server listening on %s\n", address)
    return http.ListenAndServe(address, nil)
}

// WebSocket client
func connectToWebSocketServer(url string) error {
    // Connect to WebSocket server
    conn, _, err := websocket.DefaultDialer.Dial(url, nil)
    if err != nil {
        return err
    }
    defer conn.Close()
    
    // Start a goroutine to read messages
    done := make(chan struct{})
    go func() {
        defer close(done)
        for {
            _, message, err := conn.ReadMessage()
            if err != nil {
                fmt.Println("Error reading message:", err)
                return
            }
            fmt.Printf("Received: %s\n", message)
        }
    }()
    
    // Send messages periodically
    ticker := time.NewTicker(1 * time.Second)
    defer ticker.Stop()
    
    for i := 0; i < 10; i++ {
        select {
        case <-done:
            return nil
        case <-ticker.C:
            message := fmt.Sprintf("Message %d", i+1)
            err := conn.WriteMessage(websocket.TextMessage, []byte(message))
            if err != nil {
                return err
            }
        }
    }
    
    // Close connection properly
    err = conn.WriteMessage(websocket.CloseMessage, websocket.FormatCloseMessage(websocket.CloseNormalClosure, ""))
    if err != nil {
        return err
    }
    
    // Wait for the server to close the connection
    select {
    case <-done:
    case <-time.After(time.Second):
    }
    
    return nil
}
```

## Database Interactions

### SQL Databases

```go
// Common SQL operations (using database/sql)
import (
    "database/sql"
    _ "github.com/lib/pq" // PostgreSQL driver
)

// Connect to a database
func connectToDatabase() (*sql.DB, error) {
    // Connect to PostgreSQL
    connectionString := "host=localhost port=5432 user=postgres password=password dbname=testdb sslmode=disable"
    db, err := sql.Open("postgres", connectionString)
    if err != nil {
        return nil, err
    }
    
    // Verify connection
    if err := db.Ping(); err != nil {
        db.Close()
        return nil, err
    }
    
    // Set connection pool settings
    db.SetMaxOpenConns(25)
    db.SetMaxIdleConns(25)
    db.SetConnMaxLifetime(5 * time.Minute)
    
    return db, nil
}

// CRUD operations

// Create (insert)
func createUser(db *sql.DB, name string, email string, age int) (int, error) {
    // Using a named parameter query
    query := `
        INSERT INTO users (name, email, age, created_at)
        VALUES ($1, $2, $3, $4)
        RETURNING id
    `
    
    var id int
    err := db.QueryRow(query, name, email, age, time.Now()).Scan(&id)
    if err != nil {
        return 0, err
    }
    
    return id, nil
}

// Read (select)
func getUserByID(db *sql.DB, id int) (*User, error) {
    query := `
        SELECT id, name, email, age, created_at
        FROM users
        WHERE id = $1
    `
    
    var user User
    err := db.QueryRow(query, id).Scan(
        &user.ID,
        &user.Name,
        &user.Email,
        &user.Age,
        &user.CreatedAt,
    )
    
    if err == sql.ErrNoRows {
        return nil, fmt.Errorf("user with id %d not found", id)
    } else if err != nil {
        return nil, err
    }
    
    return &user, nil
}

// Read multiple records
func getAllUsers(db *sql.DB) ([]User, error) {
    query := `
        SELECT id, name, email, age, created_at
        FROM users
        ORDER BY name
    `
    
    rows, err := db.Query(query)
    if err != nil {
        return nil, err
    }
    defer rows.Close()
    
    var users []User
    for rows.Next() {
        var user User
        err := rows.Scan(
            &user.ID,
            &user.Name,
            &user.Email,
            &user.Age,
            &user.CreatedAt,
        )
        if err != nil {
            return nil, err
        }
        users = append(users, user)
    }
    
    // Check for errors from iterating over rows
    if err := rows.Err(); err != nil {
        return nil, err
    }
    
    return users, nil
}

// Update
func updateUser(db *sql.DB, id int, name string, email string, age int) error {
    query := `
        UPDATE users
        SET name = $1, email = $2, age = $3, updated_at = $4
        WHERE id = $5
    `
    
    result, err := db.Exec(query, name, email, age, time.Now(), id)
    if err != nil {
        return err
    }
    
    // Check if any rows were affected
    rowsAffected, err := result.RowsAffected()
    if err != nil {
        return err
    }
    
    if rowsAffected == 0 {
        return fmt.Errorf("user with id %d not found", id)
    }
    
    return nil
}

// Delete
func deleteUser(db *sql.DB, id int) error {
    query := `
        DELETE FROM users
        WHERE id = $1
    `
    
    result, err := db.Exec(query, id)
    if err != nil {
        return err
    }
    
    // Check if any rows were affected
    rowsAffected, err := result.RowsAffected()
    if err != nil {
        return err
    }
    
    if rowsAffected == 0 {
        return fmt.Errorf("user with id %d not found", id)
    }
    
    return nil
}

// Transactions
func transferFunds(db *sql.DB, fromAccountID, toAccountID int, amount float64) error {
    // Start a transaction
    tx, err := db.Begin()
    if err != nil {
        return err
    }
    
    // Defer rollback in case of error
    defer func() {
        if err != nil {
            tx.Rollback()
        }
    }()
    
    // Debit the first account
    debitQuery := `
        UPDATE accounts
        SET balance = balance - $1
        WHERE id = $2 AND balance >= $1
    `
    
    result, err := tx.Exec(debitQuery, amount, fromAccountID)
    if err != nil {
        return err
    }
    
    rowsAffected, err := result.RowsAffected()
    if err != nil {
        return err
    }
    
    if rowsAffected == 0 {
        return fmt.Errorf("insufficient funds in account %d", fromAccountID)
    }
    
    // Credit the second account
    creditQuery := `
        UPDATE accounts
        SET balance = balance + $1
        WHERE id = $2
    `
    
    _, err = tx.Exec(creditQuery, amount, toAccountID)
    if err != nil {
        return err
    }
    
    // Log the transaction
    logQuery := `
        INSERT INTO transaction_log (from_account_id, to_account_id, amount, transaction_date)
        VALUES ($1, $2, $3, $4)
    `
    
    _, err = tx.Exec(logQuery, fromAccountID, toAccountID, amount, time.Now())
    if err != nil {
        return err
    }
    
    // Commit the transaction
    return tx.Commit()
}

// Prepared statements
func preparedStatementExample(db *sql.DB) error {
    // Create a prepared statement
    stmt, err := db.Prepare(`
        INSERT INTO log_entries (message, severity, created_at)
        VALUES ($1, $2, $3)
    `)
    if err != nil {
        return err
    }
    defer stmt.Close()
    
    // Execute the statement multiple times
    for i := 0; i < 10; i++ {
        message := fmt.Sprintf("Log message %d", i)
        severity := "INFO"
        
        _, err := stmt.Exec(message, severity, time.Now())
        if err != nil {
            return err
        }
    }
    
    return nil
}
```
