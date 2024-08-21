+++
title = 'Mandelbrot Set Viewer in Rust and Wasm'
date = 2024-08-20T22:18:56+02:00
draft = true
tags = ["mandelbrot", "rust", "wasm"]
summary = "Mandelbrot Set is one of the fascinating objects in mathematics, as it has an easy definition but the result is extremely complex. This blog explores it by implementing a viewer in Rust, Wasm and Typescript"
keywords = ["mandelbrot", "rust", "wasm"]
description = "Exploring the Mandelbrot Set with an implementation in Rust, Wasm and Typescript"
+++

> This project was inspired by an excellent list of challenging projects shared [here](https://www.andreinc.net/2024/03/28/programming-projects-ideas#write-a-tiling-window-manager). It's an ideal opportunity to sharpen my Rust programming skills while also diving into WebAssembly. And it's also my first **Blog post** 🎉

Mandelbrot set is one of the fascinating objects in mathematics, as it has an easy definition but the result is extremely complex. Its beautiful nature not only inspired mathematicians, but also artists, like David Hockney for a painting and John Updike in some pages.

Zooming in you can find copies of itself, seahorse structures, spirals, islands and many other interesting structures. "It illustrates how profound intricacy can emerge from the simplest of rules — much like life itself" [^1]. 

The formal definition states that it is the set obtained from the quadratic recurrence equation
$$
z_{n+1} = z_n + c
$$
with $z_0= c$, where points $c$ for which the orbit of $z_n$ does not tend to infinity are in the set [^2] . This means that a complex number $c$ belongs to the set if, when starting from $z_0=0$ and applying the iteration, $|z_n|$ remains bounded $\forall n>0$.

Since I am not a mathematician, I don't want to spend too much time on the mathematical details right now and I'll skip directly to the plotting algorithms, starting from the simplest one, the **Escape time algorithm**.

## Escape Time algorithm
This algorithm determines how quickly the sequence escapes to infinity for each point of the complex plane. It assigns a color to each pixel based on the number of iterations it takes for the magnitude of $z_n$ to reach a certain threshold. The algorithm can be written like this:
```python
    for (p_x, p_y) in pixels:
        x_0 = scale(x)
        y_0 = scale(y)
        
        iteration = 0

        while x**2 + y**2 < 4 and iteration < max_iteration:
            x_temp = x**2 - y**2 + x_0
            y = 2*x*y + y_0
            x = x_temp
            iteration = iteration + 1

        color = palette[iteration]
```
The values are checked during each iteration to see whether they have reached a critical "escape" condition, or "bailout". If that condition is reached, the calculation is stopped, the pixel is drawn, and the next _x_, _y_ point is examined. For values within the Mandelbrot set, escape will never occur, so a bail condition (in the pseudo code $max\\_iteration$) should be set.

With this simple algorithm, this is the result:
![Mandelbrot First Version](/mandelbort_v1.png)
As can be seen, this simple method creates some bands of color, which are not attractive as the smooth colors. The problem is that the number of iterations until escape is an integer, resulting in a stair-step function. It is sufficient to transform the iteration count like this:
$$
m = n + 1 - \frac{ \ln\ln|z|}{\ln2}
$$
where $n$ is the iteration count found by the naive escape algorithm and $|z|$ its the norm of the complex number at exit.
The result is the following:
![Mandelbrot First Version with Smooth Coloring](/mandelbrot_v1_smooth.png)
Much better!


[^1]: https://www.quantamagazine.org/the-quest-to-decode-the-mandelbrot-set-maths-famed-fractal-20240126/
[^2]: https://mathworld.wolfram.com/MandelbrotSet.html
