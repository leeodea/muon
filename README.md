# Muon

Muon is a modern low-level programming language, inspired by C, C#, Go, Rust and Python.

## Design principles

1. **Strongly, statically typed.**

2. **Data oriented.** Just functions, structs and enums. NO: classes, inheritance, properties, etc.

3. **No runtime.** Lack of a runtime makes the language simpler, reduces application startup latency and makes it easy to use Muon code from other languages.

4. **Extremely minimal core.** A language should not dictate dependencies. There is a standard library, but it is completely optional.

5. **High performance.** Strive for parity with C.

6. **Flexible memory management.** Programmers can switch between allocators dynamically and can define their own allocators.

7. **Avoid common memory safety pitfalls.** Memory is initialized to zero. Array bounds are checked (can be turned off where needed).

8. **No undefined behavior.** Undefined behavior can lead to [various](https://blog.regehr.org/archives/213), [hard-to-spot](https://blog.regehr.org/archives/226), [bugs](https://blog.regehr.org/archives/232). In Muon, all behavior, including platform-specific behavior, is defined.

9. **Ergonomics matter.** Programmers spend a lot of time working with a language, so ergonomics are important. Muon has:
	- Type inference for function return values and locals
	- [Generics](docs/muon_by_example.md#generic-structs)
	- Order independent declarations
	- Newline as statement separator
	- [Uniform function call syntax](docs/muon_by_example.md#ufcs)
	- [Reference type notation](docs/muon_by_example.md#reference-type-notation)
	- Namespaces

10. **Fail fast.** Usually, error reporting/handling happens via return values. For unrecoverable errors and errors that a caller is not prepared to handle, Muon provides [abandonment](docs/muon_by_example.md#error-handling-abandonment).

11. **Small(-ish) language.** Strive for a small, simple language. Having fewer ways to do something encourages a more consistent, focused ecosystem.

12. **Fast & snappy tools.** Provide tools centered around fast feedback and improving program understanding. E.g.: language server, REPL, hot reloading, debuggers, profilers.

## Example

A glimpse of Muon:

	Array {
		countOccurrences(items Array<T>) {
			map := Map.create<T, int>()
			for items {
				count := map.getOrDefault(it)
				map.addOrUpdate(it, count + 1)
			}
			return map
		}
	}
	
	main() {
		::currentAllocator = Memory.newArenaAllocator(4096)
		s := "How much wood could a wood chuck chuck if a wood chuck could chuck wood?"
		freq := s.split(' ').countOccurrences() // Equivalent to: Array.countOccurrences(ref string.split(s, ' '))
		for e in freq {
			Stdout.writeLine(format("word: {}, count: {}", e.key, e.value))
		}
	}

## Current state

**Tools**. [The compiler](docs/getting_started.md) implements error recovery and has column accurate error reporting, which should make for a pleasant command line experience. [More tools are planned](docs/roadmap.md) for later this year.

**Compiler output**. The compiler currently outputs C code.This means that we inherit C's undefined behavior model, which goes against the goals listed above! An LLVM backend is in the works which will avoid any undefined behavior.

**Performance**. The compiler is fairly fast. The following numbers were obtained by compiling the Muon compiler (which is itself written in Muon), which is ~11.6K lines of code, on a 4Ghz core i7.  

* Parser: 2.4 million lines/second.
* Type checker: 2.3 million lines/second.
* Code generator: 1.6 million lines/second.

The compiler is single threaded right now and there's lots of room for further improvement. One major caveat: after the above stages have finished, a C compiler still needs to run to generate the final binary, which usually takes up the most time. The LLVM backend will (hopefully) reduce this.

**Supported platforms**. The main focus is on Windows, Linux and macOS. However, because the compiler generates C code, it should be able to target most platforms. There is one restriction: the compiler assumes a 32-bit architecture. 64-bit support is in the works.

## Getting started

To get started with Muon, see [getting started](docs/getting_started.md). To learn more about the language, see [Muon by example](docs/muon_by_example.md).

Also, check out the [roadmap](docs/roadmap.md) to see which features are coming up.

## Patreon

Check out [Muon's Patreon page](https://www.patreon.com/nickmqb) if you'd like to support the project financially. If you can spare a few bucks a month, that would be very cool!

## License

[MIT](LICENSE)
