Q: What is the core rule for package naming in a Go library?
A: Use short, lowercase, single-word names — no underscores, no camelCase, no stutter. The package name is part of the API: `bytes.Buffer` not `bytes.BytesBuffer`. If your package is named `parse`, don't export `ParseParser`.

---

Q: What does "accept interfaces, return structs" mean in Go library design?
A: Function parameters should accept the narrowest interface the implementation needs (e.g., `io.Reader`) to maximize caller flexibility. Return concrete types so callers can access the full surface area without type assertions and so you can add methods later without breaking the interface.

---

Q: What is the functional options pattern and when should you use it?
A: Export a variadic `...Option` parameter to `New()` instead of a config struct. Each `Option` is a function that mutates internal config. Use it when a type has many optional settings with sensible defaults — callers opt-in to what they need, and adding new options is backward-compatible.

---

Q: Why should a Go library never call log.Fatal, os.Exit, or panic in non-exceptional paths?
A: These hijack program control from the caller. A library must surface errors via return values and let the application decide how to handle them. panic is only acceptable for unrecoverable programmer errors (e.g., nil required argument).

---

Q: What is the zero-value usability principle in Go library design?
A: A type should be usable without initialization wherever possible. If `var b bytes.Buffer` works without calling `New`, callers never need a constructor for the simple case. Design structs so their zero value is a valid, ready state.

---

Q: How should errors be designed in a Go library?
A: Return errors as the last return value. Define exported sentinel errors (var ErrNotFound = errors.New(...)) or exported error types for conditions callers need to check. Wrap with fmt.Errorf("context: %w", err) to add context while preserving unwrapping. Never lose the original error.

---

Q: What is error type vs sentinel error and when do you use each?
A: Sentinel: a package-level var callers compare with errors.Is — use for distinct, stateless conditions (ErrTimeout). Error type: a struct implementing error — use when callers need structured data from the error (status code, field name). Prefer sentinels for simplicity; types when data is needed.

---

Q: Why should a Go library avoid global state and init()?
A: Global state makes libraries unpredictable when imported by multiple packages and impossible to test in isolation. init() runs implicitly and can cause subtle ordering bugs or unwanted side effects. Prefer explicit initialization via constructors and inject dependencies as parameters.

---

Q: How should a library handle context.Context?
A: Accept ctx as the first parameter on any function that does I/O, calls external services, or can block. Never store a context in a struct. Respect cancellation early — check ctx.Err() or use select. Don't pass context.Background() internally; thread the caller's context all the way down.

---

Q: What is the minimum viable interface surface principle?
A: Export only what callers actually need. Unexported types, functions, and fields are your implementation freedom — once exported, they are a public contract. Start with the smallest API and expand; you can add but you cannot remove without a major version bump.

---

Q: What makes an interface in Go well-designed for a library?
A: Keep interfaces small — ideally 1-3 methods. Name single-method interfaces after the method + -er (io.Reader, fmt.Stringer). Define interfaces where they are *used*, not where they are *implemented*. Never export an interface just to "document" a concrete type — let the concrete type speak for itself.

---

Q: How do you handle backward compatibility when evolving a Go library?
A: Follow semver via Go modules. For v1+, never remove or change exported signatures — add new functions/methods instead. For breaking changes, increment the major version in the module path (module/v2). Use //Deprecated: godoc comments to signal removal candidates before a major bump.

---

Q: What is the export_test.go pattern?
A: A file in the package (not package foo_test) named export_test.go that re-exports unexported identifiers specifically for testing. This lets black-box tests in package foo_test reach internal state without polluting the public API.

---

Q: How should a Go library document its public API?
A: Every exported symbol must have a godoc comment starting with its name. Package-level doc goes in doc.go or above the package declaration. Document behavior, not implementation. Use Example functions (func ExampleFoo()) — they appear in pkg.go.dev and are executed as tests.

---

Q: When should a Go library use embedding vs explicit field delegation?
A: Embed when you genuinely want to promote the full interface of the inner type to callers (e.g., embedding sync.Mutex for locking). Use explicit delegation when you want to control the surface — embedding exposes every method and locks you into the embedded type's API as part of yours.

---

Q: How should a library expose concurrency — channels or callbacks?
A: Prefer callbacks (function parameters) for event-driven APIs — they compose better and avoid goroutine leaks. Return channels only when callers need to fan-out or select across multiple sources. Always document ownership: who closes the channel, whether it is buffered, and what guarantees ordering.

---

Q: What is the WithXxx constructor pattern for extending a library type?
A: Instead of exposing a mutable setter, provide constructors like WithTimeout(d time.Duration) or WithLogger(l Logger) that return a new copy of the type with one field changed. This keeps types immutable from the caller's perspective and makes chaining explicit and safe.

---

Q: How should a Go library manage its own dependencies?
A: Keep the dependency tree minimal — every import you add becomes a transitive dependency for all consumers. Prefer the standard library. If an external dep is needed only for a feature subset, split it into a sub-package (e.g., mylib/otelexporter) so callers opt-in at the import level.

---

Q: What is the difference between a Fake, a Stub, and a Mock in Go testing?
A: Fake: a real, working implementation of an interface that is simpler than production (e.g., in-memory store instead of Postgres). Stub: returns hardcoded values for specific inputs, no behavior. Mock: verifies that specific calls were made — couples tests to implementation details. Prefer Fakes; they test behavior, not interaction.

---

Q: Should a Go library ship its own Fake implementations?
A: Yes, when the library defines interfaces callers must implement. Provide a Fake in a testonly sub-package (e.g., mylib/fake or mylib/testing) so consumers can write tests without building their own. This is the httptest pattern — the stdlib ships httptest.Server so callers don't fake HTTP themselves.

---

Q: What makes a good Fake in Go?
A: It implements the same interface as the real thing, stores state in memory, is safe for concurrent use if the real implementation is, and exposes inspection hooks (e.g., Calls() []Request, Reset()) so tests can assert on what happened. It should not be a test framework dependency — plain Go structs, no third-party mock libraries.

---

Q: How does interface design in a library enable consumers to write Fakes?
A: If your library accepts narrow interfaces rather than concrete types, consumers can substitute a Fake at the call site with no code changes. If you return concrete types that embed unexported fields or require internal constructors, faking becomes impossible. Testability is a signal of good interface design.
