+++
title = "Hello, world!"
date = 2023-06-27
draft = false

[taxonomies]
tags = ["random-thoughts"]

[extra]
lang = "en"
toc = true
comment = true
math = true
mermaid = true
outdate_alert = false
outdate_alert_days = 120
+++

This is the first **@curryrasul's** post and I'll try to use different features here!

<!-- more -->

## Syntax highlighting

```Rust
fn main() -> Result<()> {
    let a = 10;

    for i in 1..10 {
        println!("{i}");
    }
}
```

___

## KaTex    

$$
\begin{aligned} 
F(x) = \int_{-\infty}^{+\infty}f(x)e^{-x}dt 
\end{aligned}
$$

___

## Mermaid

<div align="center">

{% mermaid() %}
graph LR
    A[Square Rect] -- Link text --> B((Circle))
    A --> C(Round Rect)
    B --> D{Rhombus}
    C --> D
{% end %}

</div>

___