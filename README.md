# Go Plugin Example

The code in this repository shows how to use the new `plugin` package in Go 1.8 (see https://tip.golang.org/pkg/plugin/).  
A Go plugin is package compiled with the `-buildmode=plugin` which creates a shared object (`.so`) library file instead of the standard archive (`.a`) library file.  
As you will see here, using the standard library's `plugin` package, Go can dynamically load the shared object file at runtime to access exported elements such as functions an variables.

## Requirements
The plugin system requires Go version 1.8.  At this time, it is only supports plugin on Linux.  

## A Pluggable Greeting System
The demo in this repository implements a simple greeting system.  Each plugin package (directories `./eng` and `./chi`) implements code that prints a greeting meesage in a different lanaguage.  File `./greeter.go` uses the new Go `plugin` package to load the pluggable modules and displays the proper message using passed command-line parameters.

Let us see how this is done.


## The Plugin
To create a pluggable package is simple.  Simply create a regular Go package designated as `main`. Use the capitalization rule to indicate functions and variables that are exported as part of the plugin.  This is shown below in file  `./eng/greeter.go`.  This plugin is responsible for displaying a message in `English`.  

File [./eng/greeter.go](./eng/greeter.go)

Notice a few things about the pluggable module:

- Pluggable packages are basically regular Go packages
- The package must be marked `main`
- The exported variables and functions can be of any type (I found no documented restrictions). A Symbol is a pointer to a variable or function.

### Compiling the Plugins
The plugin package is compiled using the normal Go toolchain.  The only requirement is to use the `buildmode=plugin` compilation flag as shown below:

```
go build -buildmode=plugin -o eng/eng.so eng/greeter.go
go build -buildmode=plugin -o chi/chi.so chi/greeter.go
```
The compilation step will create `./eng/eng.so` and `./chi/chi.so` plugin files respectively.

### Using the Plugins
Once the plugin modules are available, they can be loaded dynamically using the Go standard library's `plugin` package.  Let us examine file [./greeter.go](./greeter.go), the driver program that loads and uses the plugin at runtime.

For instance, when the program is executed it prints a greeting in English or Chinese 
using the passed parameter to select the plugin to load for the appropriate language.
```
> go run greeter.go english
Hello Universe
```
Or to do it in Chinese:
```
> go run greeter.go chinese
你好宇宙
```