+++
title = "Rust Flow"
date = 2021-06-07
+++

## A Brief History

Reading through the Rust Lang book was a deceptively exciting and an engaging experience. The whole feeling of building out these super fast cargos of code was thrilling. Later, I found a Rust Youtube channel and one video where several people were discussing the internals of async/wait. Unless you are a Rust Developer team you probably aren't going to find much value. Going back to the Rust website you will find several different options for learning. My favorite of course is learning by example. Let's go through those first.

## Why Rust?
I don't remember how I discovered Rust, but I do remember stumbling upon [Writing an OS in Rust by Philipp Oppermann](https://os.phil-opp.com/). I think it was this time that I decided that I need to take a look at Rust. Besides, I like the challenge of learning new things, so creating my own operating system and learning a new language seemed like a good fit.

## Development Modals
Two types of development in devops. Trunk based development or Gitflow development. 	
 ![01c240a9997abf3a7a2c8ed798460016.png](/1404e3fa397148fc9dfb911479ad5777.png)
 ![f8c40ca98fa0a5e06f017bf71eb096b7.png](/9bf2e6d053aa486eb6e89d4789567b0b.png)
-  [Scott Wlaschin - Talk Session: Domain Modeling Made Functional](https://www.youtube.com/watch?v=PLFl95c-IiU) 
 - [Parse don't validate](https://lexi-lambda.github.io/blog/2019/11/05/parse-don-t-validate/) 

# Goal
"We will thus have a bias for **boring and correct solutions**, even if they incur a performance overhead that could be optimized away with a more careful and chiseled approach." [Luca Palmieri ](https://www.lpalmieri.com/)

# Tooling
Rust has first class tooling. 
## Official Tools
- [Install rust using rustup](https://rustup.rs) -- rustup is an installer for
the systems programming language Rust 
- [Official Documentation](https://doc.rust-lang.org/book/) The Rust programming language helps you write faster, more reliable software.

## Unofficial Tools
- [Jetbrains - IDEA with IntelliJ Rust support](https://www.jetbrains.com/idea/)
"On the other hand, IntelliJ Rust forces you to work with a JetBrains’ IDE, which you might or might not be willing to. If you’d like to stick to your editor of choice look for its [Rust Analyzer](https://marketplace.visualstudio.com/items?itemName=matklad.rust-analyzer) integration/plugin."
- [VS Code - Rust in peace (ext)](https://marketplace.visualstudio.com/items?itemName=gilescope.rust-in-peace) - An opinionated Rust bundle for Visual Studio Code.


# CI/CD in Rust

## Official Linter (clippy)
A [collection of lints](https://rust-lang.github.io/rust-clippy/master/index.html) to catch common mistakes and improve your Rust code.

Once you have rustup and the latest stable release (at least Rust 1.29) installed, run the following command:
`rustup component add clippy`
Now you can run Clippy by invoking the following command:
`cargo clippy`
Clippy can automatically apply some lint suggestions. Note that this is still experimental and only supported on the nightly Rust channel:
`cargo clippy --fix -Z unstable-options`

## Official Formatter
Install the official rust formatter
`rustup component add rustfmt`
You can format your whole project with
`cargo fmt`
Later in our CI/CD pipeline we will add `cargo fmt -- --check` to do this automatically for us.

# Security Testing
Install the official security audit tool
`cargo install cargo-audit`
Run the audit tool to scan your dep tree
`cargo audit`
We will add this to our CI/CD pipeline to handle this automatically before each commit.

# CI/CD pipeline configs
- [GitLab CI](https://gist.github.com/LukeMathWalker)
- GitHub Actions;
- CircleCI;
- GitLab CI;
- Travis.
- [Git pre-hook](https://git-scm.com/book/en/v2/Customizing-Git-Git-Hooks)

# Documentation
Auto document your project
`cargo doc --open`

# Http Server	

   - [***actix-web***](https://actix.rs/) -  [Code Examples](https://github.com/actix/examples) 
   - [**rocket**](https://rocket.rs/) [*Code Examples*](https://github.com/SergioBenitez/Rocket/tree/master/examples)
   - [***tide***](https://github.com/http-rs/tide) [*Code Examples*](https://github.com/http-rs/tide/tree/master/examples)
   - [***warp***](https://github.com/seanmonstar/warp) [*Code Examples*](https://github.com/seanmonstar/warp/tree/master/examples)
   -  [Developer Experience on Rust http servers](https://fasterthanli.me/articles/image-decay-as-a-service)
   -  TL;DR - No real winners so far as of writing


## actix-web
We will use actix-web for our http server
 - Well documented
 - http2 support
 - production ready
 - needs tokio for async/wait
 - largest community 
 - plugin ready

# Installing Rust Deps

`cargo install cargo-edit`
![0f534b8bffb03a1f75d4474320976202.png](/c5367421f66340ccbe1179914911a953.png)
Now you can add deps by using cargo add:
`cargo add actix-web --vers 4.0.0-beta.5`
![c4f9c2aa272ece3c5f3ff2db8dbe06be.png](/caab27788c704c52a8556e89eb732944.png)
You can check your build for errors without building by doing
`cargo check`

# Start the rust http server
- Install the boilerplate code from the repo:
`git clone https://github.com/tylerharpool/rust-server.git`
`cd rust-server`
`cargo run`
visit http://localhost:8080/hey
![d69afcfdaf1c69535a574b151ce63242.png](/0df473c829564038ac72cdf62967f419.png)

# Understanding the code

main.rs
```
#[actix_web::main]
async fn main() -> std::io::Result<()> {
    HttpServer::new(|| {
        App::new()
            .service(hello)
            .service(echo)
            .route("/hey", web::get().to(manual_hello))
            .route("/blog", web::get().to(blog))
    })
    .bind("127.0.0.1:8080")?
    .run()
    .await
}


use actix_web::{get, post, web, App, HttpResponse, HttpServer, Responder};

#[get("/")]
async fn hello() -> impl Responder {
    HttpResponse::Ok().body("Hello World!")
}

#[post("/echo")]
async fn echo(req_body: String) -> impl Responder {
    HttpResponse::Ok().body(req_body)
}

async fn manual_hello() -> impl Responder {
    HttpResponse::Ok().body("Hey there!")
}

async fn blog() -> impl Responder {
    HttpResponse::Ok().body("Welcome to my Blog")
}
```


## Macros

The Hello World example on the Rust Website mentions println! outputs the text to the terminal. For us to create the hello world application in Rust we should use this highly efficient macro, but do not mistake it for a function because that would be wrong. Macros differ to fn by the use of ! after the constant.

We can see: macro_rules!, printLn! in the code below: The Rust people, want you to remember that using Macros is highly efficient because its not a string like you would find in C, but a Abstract Syntax Tree, so you will not get as many bugs.

Here is the basic Hello World app:
```
// This is a simple macro named `say_hello`.
macro_rules! say_hello {
    // `()` indicates that the macro takes no argument.
    () => {
        // The macro will expand into the contents of this block.
        println!("Hello!");
    };
}

fn main() {
    // This call will expand into `println!("Hello");`
    say_hello!()
}
```

## Analyze Hello World
 The Rust Documentation says, "Rust provides a powerful macro system that allows metaprogramming."  Paul Graham's essay "What Made Lisp Different" explains it like this:

**Macros can be described as "writing code that writes code"**

 * How do we build new Macros?
 * What are Macros benefits over fn?

# Async/wait
Rust is not asynchronous out-of-the-box. The entrypoint fn main() is a synchronous function. If we add async to our main() function we will get an error. The problem is HTTP::Server is an async function, so we need to figure out a way to turn main() into an asynchronous function. 

## Futures
Rust has a feature called [Futures](https://doc.rust-lang.org/beta/src/core/future/future.rs.html#33-101). A future is a value that may not have finished computing yet. The benefits of a future is that it makes your code more efficient. By using futures you are freeing up threads so your program can continue working while the value is being returned.

Example from [Rust Documentation:](https://doc.rust-lang.org/beta/std/future/trait.Future.html)
```
#[must_use = "futures do nothing unless you `.await` or poll them"]
pub trait Future {
    type Output;
    pub fn poll(
        self: Pin<&mut Self>, 
        cx: &mut Context<'_>
    ) -> Poll<Self::Output>;
}
```

## Poll method
A [poll](https://doc.rust-lang.org/beta/std/future/trait.Future.html#the-poll-method) tries to return a value from an async function. The poll iterates and continues its task of resolving a value until it resolves or it will continue polling until it resolves in the future. You don't call poll directory, but instead will call the .async method which will in turn get you the futures value. If main() is our entrypoint, how are we going to call poll on it? We can't. We are expected to call our `#[actix_web::main]` macro instead. Remember the essay?  We have `Code creating code`.
 

## Procedural macros
Expand all of your macros in your code without passing it to the compiler. Let's use cargo-expand to figure out what is going on with actix_web::main.
`cargo install cargo-expand`
![27f286d349daa422dea7c67191ff3270.png](/253e5b9a2f9e4d0db4026e9621220e28.png)