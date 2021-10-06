# Framework template
- We use Echo framework for Go Backend development currently
- We can use [teq-echo](https://git.teqnological.asia/teq-go/teq-echo) repository and [teq-pkg](https://git.teqnological.asia/teq-go/teq-pkg) repository as templates at the beginning

# Linting
We mainly use [golangci-lint](https://golangci-lint.run/usage/install) or automated code checks. The below `.golangci.yml` config is for reference:

```yml
linters:
  enable-all: true
  disable:
    - gochecknoglobals
    - gochecknoinits
    - gomnd
    - gocognit
    - funlen
    - goerr113
    - gofumpt
    - wrapcheck
    - exhaustivestruct
    - ifshort
    - forcetypeassert
    - errorlint
    - cyclop
    - gomoddirectives
    - maligned
    - scopelint
    - golint
    - interfacer

run:
  timeout: 20m
  skip-dirs:
    - build
    - tmp
    - scripts

  # include test files or not, default is true
  tests: false

issues:
  exclude-use-default: true

linters-settings:
  goconst:
    min-occurrences: 5
  gocyclo:
    min-complexity: 100
  lll:
    line-length: 170
  tagliatelle:
    case:
      use-field-name: true
      rules:
        # any struct tag type can be used.
        # support string case: `camel`, `pascal`, `kebab`, `snake`, `goCamel`, `goPascal`, `goKebab`, `goSnake`, `upper`, `lower`
        json: snake
  gci:
    local-prefixes: your/git_repo/path
```

#Formatting
We use `.editorconfig` file for consistent of format on every editors or IDEs:

```
# top-most EditorConfig file
root = true

# Unix-style newlines with a newline ending every file
[*]
end_of_line = lf
indent_style = space
indent_size = 4

# Tab indentation (no size specified)
[Makefile]
indent_style = tab

[*.{js,php,yml}]
indent_size = 2
charset = utf-8

[*.{go,mk}]
indent_style = tab
```

# Naming
- Use **UpperCamelCase** for public and **lowerCamelCase** for private interfaces, structs, variables and functions.

```go
// Bad
type hello_world interface{}

type fizz_buzz struct{}

var my_array []string
my_array := []string{}

func foo_bar() {
  // some code
}

// Good
// public
type HelloWorld interface{}

type FizzBuzz struct{}

// private
var myArray []string
myArray := []string{}

func fooBar() {
  // some code
}
```

- Use `err` as a common variable name for error type.

```go
// Bad
someErr := returnErrorFunc()

// Good
err := returnErrorFunc()
```

- Use **lowercase** with **NO** underscore for package names. Prefer conciseness over long names.

```go
// Bad
package string_conversion
package operatingSystem
package documentation
package system_log
package io_utility

// Good
package strconv
package os
package doc
package syslog
package ioutil
```

- Use meaningful variable name for global/local/argument variables, the bool variable name must begin with `is`, the variable name of multiple values must end with `s / es`, the special word in variable name must be capitalized.

```
// Bad
var r *redis.Pool
country := getCountries()
order := getOrders()
idorder := order[0].ID
u := findUser(i)
userid := u.ID
existed := true
  
// Good
var RedisPool *redis.Pool
countries := getCountries()
orders := getOrders()
orderID := orders[0].ID
user := findUser(userID)
userID := user.ID
isExisted := true
```

# Imports
- Organize packages in group, separated by blank line between each group. This will be well supported by linter `goimports`

```go
import (
  // Standard library packages

  // Internal project packages

  // Third-party packages
)
```
Example:

```go
// Bad
import (
  "errors"
  "nimblehq/controllers"
  "net/http"
  "github.com/fizz/buzz"
  "nimblehq/models"
  "github.com/foo/bar"
)

// Good
import (
  "errors"
  "net/http"

  "nimblehq/controllers"
  "nimblehq/models"

  "github.com/fizz/buzz"
  "github.com/foo/bar"
)
```
- Sort packages by alphabetical order for each group.

```go
// Bad
import (
  "net/http"
  "errors"

  "nimblehq/models"
  "nimblehq/controllers"
)

// Good
import (
  "errors"
  "net/http"

  "nimblehq/controllers"
  "nimblehq/models"
)
```
- Use **lowercase** with **NO** underscore to rename imported packages. This is often needed to avoid package import conflicts.

```go
// Bad
import (
  "errors"
  goUrl "net/url"

  errors_config "mypackage/config/errors"
  "mypackage/config/url"
)

// Good
import (
  "errors"
  gourl "net/url"

  errorsconf "mypackage/config/errors"
  "mypackage/config/url"
)
```

- Should name module name in `go.mod` using `repository-hosting-domain/user-name/repository-name` pattern.

```go
// Bad
module my-project
  
// Good
module gitlab.com/husol/my-project
```

- Use module name pattern convention when importing an internal module.

```go
// Bad
import (
  "my-project/controllers"
  "my-project/models"

  "github.com/fizz/buzz"
  "github.com/foo/bar"
)

// Good
import (
  "github.com/nimblehq/my-project/controllers"
  "github.com/nimblehq/my-project/models"

  "github.com/fizz/buzz"
  "github.com/foo/bar"
)
```

# Declaration
- Prefer grouping all declarations into the block of type.

```go
// Bad
const failedStatus = "failed"
const pendingStatus = "pending"
const processedStatus = "processed"

var isChecked bool
var err error
  
// Good
const (
  failedStatus = "failed"
  pendingStatus = "pending"
  processedStatus = "processed"
)

var (
  isChecked bool
  err error
)
```

# Slices / Maps
- Prefer `s := make([]string, 0)` over `var s []string` when declaring an empty slice
- Prefer `s := make(map[int64]string)` over `var s map[int64]string` when declaring an empty map

# Functions
- Should declare the type for each parameter over declare single type for all parameters.

```go
// Bad
func fooBar(num1, num2 int, str1, str2 string) {
  // Some code
}

// Good
func fooBar(num1 int, num2 int, str1 string, str2 string) {
  // Some code
}
```

- Should unnamed over named return parameters for return values.

```go
// Bad
func fruit(name string) (color, taste string) {
  switch name {
  case "Banana", "Mango":
    color, taste = "Yellow", "Sweet"
  default:
    color, taste = "Unknown", "Unknown"
  }
  return
}

// Good
func fruit(name string) (string, string) {
  var color string
  var taste string

  switch name {
  case "Banana", "Mango":
    color, taste = "Yellow", "Sweet"
  default:
    color, taste = "Unknown", "Unknown"
  }
  return color, taste
}
```

- Prefer early return to avoid deep nesting.

```go
// Bad
func validUser(u *User) bool {
    if u != nil {
        if u.Id != nil {
            return true
        } else {
            return false
        }
    } else {
        return false
    }
}

// Good
func isValidUser(u *User) bool {
    if u == nil {
        return false
    }
      
    return u.Id != nil
}
```

- Should be one or two letter abbreviation as the receiver name in the receiver function. The receiver name should be consistent other receiver functions.

```go
// Bad
func (registration *Registration) New() {
    ...
    ...
}
  
func (serverHandler ServerHandler) ServeHTTP(rw ResponseWriter, r *Request) {
    ...
    ...
}
  
// Good
func (r *Registration) New() {
    ...
    ...
}
  
func (sh ServerHandler) ServeHTTP(rw ResponseWriter, r *Request) {
    ...
    ...
}
```

# Errors
- **SHOULD NOT** ignore errors using `_` variables if the function returns an error. Check them to make sure the function is completed.
- Should reuse the variable err instead of assigning errors to a new variable for error handling since errors must be handled right away.

```go
// Bad
func1Err := returnErrFunc1()
if func1Err != nil {
  // Error handling
}

func2Err := returnErrFunc2()
if func2Err != nil {
  // Error handing
}

// Good
err := returnErrFunc1()
if err != nil {
  // Error handing
}

err = returnErrFunc2()
if err != nil {
  // Error handing
}
```

- **DO NOT** use panic for handling normal errors. Use error and multiple return values.

# Testing
- Any test file must have a `_test.go` suffix as the `go test` command automatically executes any file with names that match the file pattern `*_test.go`.
- Any **test function** must have a `Test` prefix as the go test command automatically executes any function that match following pattern.

```go
func TestXxx(*testing.T) {
  // Testing code
}
```

# Error Codes
- The error code has format `{1 digit of service}{2 digits of model}{3 digits of error number}`
- The error code should start with `1` for common errors and `auth-service` errors 

  Example:

```go
// Define common error for Auth service in pkg
func ErrUnauthorized(err error) AppError {
    return AppError{
        Raw:       err,
	HTTPCode:  http.StatusUnauthorized,
	ErrorCode: "100010", // 1: Auth service; 00: Non-model; 010: Error number,
        Info: "record not found",
	Message: "Unauthorized!",
    }
}

// Define error for User service in user-service
func ErrUserGetList(err error) apperror.AppError {
    return apperror.AppError{
        Raw:       err,
	HTTPCode:  http.StatusInternalServerError,
	ErrorCode: "201010", // 2: User service; 01: User model; 010: Error number
        Info: "fail to get users",
        Message: "fail to get users",
    }
}
```
