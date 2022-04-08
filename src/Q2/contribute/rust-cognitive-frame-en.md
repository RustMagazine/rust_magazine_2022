# Learning Rust You Need a Cognitive Frame

Author: Handong Zhang

---

## Preface

If learning Rust feels hard, painful or confusing, you might want to read this article.  But if you feel comfortable learning Rust, then this article is useless to you and can be ignored.

Although there is a wealth of Rust learning materials available, Rust is a relatively expensive language to learn. The cost of learning varies slightly for people with different language experience. 

Although there is a wealth of Rust learning materials available, Rust is a language with a relatively high learning curve.The cost of learning varies slightly for people with different language experience.

Before learning Rust, You need to know what the mental cost of learning Rust will be.

**People with zero foundation** need to make up some necessary computer science related fundamentals as well as build an understanding of programming. Beginning to learn programming is a big challenge in itself.

**People who have some basic programming skills** can be a bit of a hindrance to learning Rust, although it can be helpful.

**People with only C experience** have the following challenges when learning Rust:

- Rust programming paradigm. The `C` language is procedural, while `Rust` is a hybrid programming paradigm that supports both object-oriented and functional programming styles. It is easy for someone coming from C to write Rust in a procedural style, and while it is possible to write full functionality in Rust in a procedural style, the advantages of Rust are lost in the code architecture. Concepts related to programming paradigms: `generics` `/ trait` / `error handling` /` structs` / `enums`.
- Ownership and borrowing checks. `C` manages memory manually, but `Rust` manages it with ownership. `C` uses pointers, but in Rust, pointers are abstracted safely to references, requiring borrow checks. These are the concepts that `C` developers must master.
- `Unsafe Rust` safe abstraction. `C` developers need to understand the `Unsafe Rust `coding specification and how to do safe abstraction, which is important, especially when calling each other with C. 
- Macros. Rust declaration macros similar to the C language declaration macros, are code replacement, but more powerful than C, these should also be considered a learning challenge. rust there are more powerful procedural macros, you can learn in the early stages of learning without considering, and then learn later.

**People with Cpp experience**, who by default have some knowledge of C, have the following challenges when learning Rust.

- Rust programming paradigm. The hybrid paradigm of Rust is different from  Cpp. Rust is not a pure object-oriented language and does not have constructors. the generics, traits, enumerations, and error handling in Rust are also important for Cpp developers to learn. 
- Ownership and borrowing checks. Because Rust also introduces the same RAII-based smart pointers as Cpp, memory management is easier to understand for those with experience in Cpp 11 and above. However, Cpp developers with no experience in this area will have some difficulty.
- `Unsafe Rust` safe abstraction. This is the same as for beginners from C. It requires some understanding of the Unsafe Rust coding specification.
- Generic and Procedural Macros. Cpp developers have templates, but Rust has generics, which are not as powerful as Cpp templates, but have procedural macros to compensate. That is, Cpp template programming is equivalent to a combination of Rust's generics and procedural macros. If Cpp developers want to pursue the effects of templating in Rust, then they need to master generics and procedural macros.

**People with only experience in GC languages**, such as Java, Python, Ruby, Haskell, etc., face essentially the same challenges in learning Rust as Cpp, but the learning curve is much steeper because most people using the GC language do not have a deep enough understanding of the underlying memory management.

Thus, the steepness of the learning curve for Rust has to do not only with the complexity of the language itself, but also with each individual's programming base.

## Rust Cognitive Frame

Once you realize the root cause of the Rust learning curve, you will find that your previous programming knowledge does not transfer smoothly to learning the Rust language.

So, you need a general learning framework that you can follow to create a learning plan to combat this learning curve and get started with Rust and keep learning.

This learning frame is simple, and requires only two things to understand.

1. **First, don't be in a hurry, keep a beginner's mindset, and treat it in stages. ** 
2. **Second, at each stage of the learning process, do not just have input, but also maintain the output. **

It is said to be a learning frame, but it is actually a cognitive frame. Next, let's look at the specifics.

## Learning Rust in stages

**For those who have some basic programming skills**, there are at least three stages to learning Rust.

1. **Learn the Rust syntax as a whole**. Get a good understanding of the Rust syntax and language features. 
2. **Rust basic ownership concepts need some deep understanding**. 
3. **Deep domain learning**. After the above two phases are completed, you can put the domain into practice and learn it further in depth.

### Stage I:  Comprehensive Understanding of the Rust Syntax 

The goal of this first phase is to gain a comprehensive understanding of the Rust syntax.

What do you need to do?

1. Gain a more comprehensive understanding of the Rust syntax. 
2. Classify the Rust syntax.

This phase is not for you to learn Rust in one go, so don't get too bogged down in what you don't understand, and allow yourself to keep a list of questions if you don't understand it for a while. The focus of this phase is to gain a comprehensive understanding of the Rust syntax and build a syntax structure in your mind, with a focus on categorization. The syntax should be sorted into categories. For example, data types, control flow, structures, traits and generics, macros, etc., and what each of them does.

#### Recommended Study Materials for Stage I

1. [The Rust Programming Language](https://doc.rust-lang.org/book/#the-rust-programming-language)
2. [The Rust Cargo Book](https://doc.rust-lang.org/cargo/)
3. Others,  free or paid introductory Rust materials.

When looking at these materials, you should focus on the first stage of learning: a comprehensive understanding of the Rust syntax.

Once you have a basic understanding of the Rust syntax, you can test yourself with the official [Rust by example](https://doc.rust-lang.org/rust-by-example/) and [Rustlings](https://github.com/rust-lang/) to test the results of your learning.

#### Recommended practice projects for stage II

There are some misconceptions about the choice of first-stage practice projects, and many people like to use Rust to solve algorithmic problems to learn Rust. But in fact, the learning effect is not good.

Because of Rust ownership restrictions, it is not as flexible and free as other languages for implementing some algorithms and data structures. If you don't have a deep understanding of Rust ownership, it's easier to give up on Rust when you encounter difficulties in solving problems, and it's not easy to stick with it.

So at this stage, it is better to do projects that give you a sense of accomplishment. The general principle is: combine your own experience in your domain, choose simple projects to start.

Here are some recommended practice projects.

1. Combine the official [Command Line Apps in Rust](https://rust-cli.github.io/book/index.html) book to implement a simple terminal application, such as reading a CSV file or something.
2. Go to the GitHub open source repository for case inspiration.
   1. for example, implement a terminal typing practice application like [toipe](https://github.com/Samyak2/toipe).
   2. or a simple editor like [kyun ](https://github.com/file-acomplaint/kyun).
3. Others.

### Stage II: Mastering Key Concepts of the Rust Language

Once you have achieved the learning goals of stage 1, you are ready to begin stage 2.

The goal of stage 2 is to master the key concepts of the Rust language. The following concepts are covered: 

1. Ownership and borrowing checks. 

2. Type systems and programming paradigms. 

3. Unsafe Rust and macros.

The focus is on Ownership and Borrowing checks. Only after mastering these two concepts can you get started with the Rust language. The other concepts can be mastered gradually as you work on projects, but you can't be ignorant of them.

#### Recommended Study Materials for Stage II

- [《Rust for Rustaceans》](https://nostarch.com/rust-rustaceans)
- [《Programming Rust, Second Edition》](https://www.oreilly.com/library/view/programming-rust-2nd/9781492052586/)
-  [Rust Design Patterns](https://rust-unofficial.github.io/patterns/)
- Rust Standard Library Documentation
- [Too Many lists](https://rust-unofficial.github.io/too-many-lists/)
- other advanced Rust books, and free or paid learning materials.

It is not necessary to read all of the materials recommended here. you can focus on "Ownership and borrowing checks" in depth. then go through the concepts of "Type Systems and Programming Paradigms", "Macros", and "Unsafe Rust", and also dive into the concepts of Rust design patterns. After that, take the time to read the Rust standard library documentation in depth to understand the Rust coding specification.

After the above, you'll have a good foundation to put Rust into production practice.

#### Recommended practice projects for stage II

Combine your experience in your domain of expertise and choose simple projects, starting from the simple to the deep.

At the same time, you can also start reading the source code of some of the better-known projects to start learning.

- At this point, you can read 《Too Many Lists》 to implement a linked list, or you can solve LeetCode to test your understanding of the Rust ownership mechanism.
- GitHub project for inspiration, for example:
  - [smallvec](https://github.com/servo/rust-smallvec)
  - [toydb](https://github.com/erikgrinaker/toydb)
  - Others. 

### Stage III : Deep into the real-world domain

After the above two stages of learning, learners can commit to their own real-world projects and start learning. If they do not have their own real projects, they can participate in the open source project contributions.

This process is a long-term process of learning and applying Rust. For example, Rust concurrency and asynchronous development-related content, key concepts about type systems, programming paradigms, macros, and in-depth learning and application of Unsafe Rust are all done in this stage.

##  Keeping up with input and output

The learning process is about maintaining a balance between input and output.

What do I mean by input? Drawing various knowledge from various learning resources is called input.

Output can take many forms. Writing an article, participating in a project, or doing a sharing are all outputs.

Only when input and output are kept in a cycle will the human brain be in thinking mode and what you input will be precipitated into structured memory.

So, in addition to input, you have to keep yourself in output during the above learning phase. That way your energy and time will not be wasted.

