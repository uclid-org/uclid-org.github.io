## uclid-docs

Check out mdBook documentation here: [mdBook](https://rust-lang.github.io/mdBook/index.html)

---

#### Install and build:
1. Install Rust on your local machine to get access to `cargo`, which is Rust package manager
	a. https://www.rust-lang.org/tools/install 
1. Install mdbook (precompiled binaries are available [here](https://github.com/rust-lang/mdBook/releases) or do `cargo install mdbook`)
2. Install mdbook-bib (precompiled binaries are available [here](https://github.com/francisco-perez-sorrosal/mdbook-bib/releases) or do `cargo install mdbook-bib`)
3. In `mdbook/` run `mdbook serve` and go to localhost (usually http://127.0.0.1:3000 or http://localhost:3000) in your browser

#### Directory structure:

- book.toml: config file
- book: mdBook are built to here
- src: write stuff here
	- SUMMARY.md: describes structure of mdBook (used by mdbook), [summary docs](https://rust-lang.github.io/mdBook/format/summary.html)
	- other files: source Markdown (files as specified in SUMMARY.md)

#### Owners:

Please subscribe to adding stuff to the documentation by adding entries to [owners.md](owners.md)
