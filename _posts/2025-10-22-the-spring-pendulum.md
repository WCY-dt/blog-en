---
layout:     post
title:      "The Spring Pendulum"
date:       2025-10-22 16:30:00 +0800
categories: mathematics
tags:       simulation langrange physics
summary:    "The spring pendulum is a classic example of a physical system that illustrates the principles of Lagrangian mechanics. This article explores how to apply Lagrange's equations to model the motion of a spring pendulum and demonstrates its dynamic behavior through numerical simulations."
series:     Algorithms
series_index: 2
mathjax:   true
---

Below is an implementation of a spring pendulum:

{% result title="Spring Pendulum Animation" height=400px hide=code %}
```html
<svg id="spring-pendulum-svg" width="400" height="300"></svg>
```

```css
* {
    box-sizing: border-box;
}

body {
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    height: 100%;
    margin: 0;
    background-color: #f0f0f0;
}

svg {
    border: 1px solid #ccc;
    background-color: white;
}
```

```javascript
const svg = document.getElementById('spring-pendulum-svg');
const width = svg.clientWidth;
const height = svg.clientHeight;

let L0 = 100;           /* Natural length of the spring */
let k = 0.5;            /* Spring constant */
let m = 2;              /* Mass */
const g = 9.81;         /* Gravitational acceleration */

let L_prime = 0;        /* Initial spring extension */
let v = 0;              /* Initial extension velocity */
let theta = Math.PI / 4;/* Initial angle (45 degrees) */
let omega = 0;          /* Initial angular velocity */
let dt = 0.35;          /* Time step size */

let animationId;

function simulateSpringPendulum() {
    const spring_force1 = L_prime > 0 ? (k / m) * L_prime : 0;
    const k1_L = v;
    const k1_v = (L0 + L_prime) * omega * omega + g * Math.cos(theta) - spring_force1;
    const k1_theta = omega;
    const k1_omega = - (2 * v * omega + g * Math.sin(theta)) / (L0 + L_prime);

    const k2_L = v + 0.5 * k1_v * dt;
    const spring_force2 = (L_prime + 0.5 * k1_L * dt) > 0 ? (k / m) * (L_prime + 0.5 * k1_L * dt) : 0;
    const k2_v = (L0 + L_prime + 0.5 * k1_L * dt) * (omega + 0.5 * k1_omega * dt) ** 2
                  + g * Math.cos(theta + 0.5 * k1_theta * dt)
                  - spring_force2;
    const k2_theta = omega + 0.5 * k1_omega * dt;
    const k2_omega = - (2 * (v + 0.5 * k1_v * dt) * (omega + 0.5 * k1_omega * dt)
                        + g * Math.sin(theta + 0.5 * k1_theta * dt))
                        / (L0 + L_prime + 0.5 * k1_L * dt);

    const k3_L = v + 0.5 * k2_v * dt;
    const spring_force3 = (L_prime + 0.5 * k2_L * dt) > 0 ? (k / m) * (L_prime + 0.5 * k2_L * dt) : 0;
    const k3_v = (L0 + L_prime + 0.5 * k2_L * dt) * (omega + 0.5 * k2_omega * dt) ** 2
                  + g * Math.cos(theta + 0.5 * k2_theta * dt)
                  - spring_force3;
    const k3_theta = omega + 0.5 * k2_omega * dt;
    const k3_omega = - (2 * (v + 0.5 * k2_v * dt) * (omega + 0.5 * k2_omega * dt)
                        + g * Math.sin(theta + 0.5 * k2_theta * dt))
                        / (L0 + L_prime + 0.5 * k2_L * dt);

    const k4_L = v + k3_v * dt;
    const spring_force4 = (L_prime + k3_L * dt) > 0 ? (k / m) * (L_prime + k3_L * dt) : 0;
    const k4_v = (L0 + L_prime + k3_L * dt) * (omega + k3_omega * dt) ** 2
                  + g * Math.cos(theta + k3_theta * dt)
                  - spring_force4;
    const k4_theta = omega + k3_omega * dt;
    const k4_omega = - (2 * (v + k3_v * dt) * (omega + k3_omega * dt)
                        + g * Math.sin(theta + k3_theta * dt))
                        / (L0 + L_prime + k3_L * dt);

    L_prime += (k1_L + 2 * k2_L + 2 * k3_L + k4_L) / 6 * dt;
    v += (k1_v + 2 * k2_v + 2 * k3_v + k4_v) / 6 * dt;
    theta += (k1_theta + 2 * k2_theta + 2 * k3_theta + k4_theta) / 6 * dt;
    omega += (k1_omega + 2 * k2_omega + 2 * k3_omega + k4_omega) / 6 * dt;
}

function drawSpringPendulum(L_prime, theta) {
    svg.innerHTML = ''; /* Clear SVG content */

    const L_total = L0 + L_prime;
    const x_m = width / 2 + L_total * Math.sin(theta);
    const y_m = height / 4 + L_total * Math.cos(theta);

    /* Draw spring */
    const spring = document.createElementNS("http://www.w3.org/2000/svg", "line");
    spring.setAttribute("x1", width / 2);
    spring.setAttribute("y1", height / 4);
    spring.setAttribute("x2", x_m);
    spring.setAttribute("y2", y_m);
    spring.setAttribute("stroke", "black");
    spring.setAttribute("stroke-width", "1");
    svg.appendChild(spring);

    /* Draw mass block */
    const circle = document.createElementNS("http://www.w3.org/2000/svg", "circle");
    circle.setAttribute("cx", x_m);
    circle.setAttribute("cy", y_m);
    circle.setAttribute("r", "10");
    circle.setAttribute("fill", "#d74514");
    svg.appendChild(circle);
}

function animate() {
    simulateSpringPendulum();
    drawSpringPendulum(L_prime, theta);
    animationId = requestAnimationFrame(animate);
}

animate();
```
{% endresult %}

This article introduces the use of Lagrange's equations to describe the motion of both simple and spring pendulums, and demonstrates their dynamic behavior through numerical simulations.

## Lagrangian Mechanics

***Lagrangian Mechanics*** is a theoretical framework for describing the dynamics of physical systems. It is based on the concept of the ***​​Lagrangian***​​, denoted by $$\mathcal{L}$$, which is defined as the difference between the system's kinetic energy $$T$$ and its potential energy $$U$$:

$$
\mathcal{L} = T - U
$$

From the Lagrangian, the equations of motion for the system can be derived using the ***Euler-Lagrange Equation***:

$$
\frac{d}{dt} \left(\frac{\partial \mathcal{L}}{\partial \dot{q}_i}\right) - \frac{\partial \mathcal{L}}{\partial q_i} = 0
$$

Here, $$q_i$$ represents a generalized coordinate of the system, and $$\dot{q}_i$$ is its corresponding generalized velocity.

***Ordinary Differential Equation (ODE)***​​ like the Euler-Lagrange equation can be solved using numerical methods to simulate the system's dynamic behavior.

In practical computation, the typical procedure involves first expressing the system's kinetic and potential energy, then calculating the Lagrangian, and finally solving the Euler-Lagrange equation to obtain the equations of motion.

## The Runge-Kutta Method

Consider the general form of an ordinary differential equation:

$$
\frac{dy}{dt} = f\left(t, y\right), \quad y\left(t_0\right) = y_0
$$

For this equation, the ***Euler Method*** provides a simple numerical solution:

$$
y_{n+1} = y_n + f\left(t_n, y_n\right) \Delta t
$$

However, the Euler method has low precision

- If $$\Delta t$$ is large, it easily produces accumulated errors
- If $$\Delta t$$ is small, the computational cost increases significantly
- For stiff equations, the Euler method may be unstable

To improve precision, more advanced numerical integration methods can be adopted, such as the ***Runge-Kutta Method***.

Compared to the Euler method, which uses the slope at the interval start $$\left(t_n, y_n\right)$$, the Runge-Kutta method updates the value of $$y$$ by computing a weighted average of slopes at multiple points within the interval, thereby improving precision.

The classic form of the Runge-Kutta method is the ***fourth-order Runge-Kutta method (RK4)***, with the update formula:

$$
\begin{aligned}
k_1 &= f\left(t_n, y_n\right) \\
k_2 &= f\left(t_n + \frac{\Delta t}{2}, y_n + \frac{\Delta t}{2} k_1\right) \\
k_3 &= f\left(t_n + \frac{\Delta t}{2}, y_n + \frac{\Delta t}{2} k_2\right) \\
k_4 &= f\left(t_n + \Delta t, y_n + \Delta t k_3\right) \\
y_{n+1} &= y_n + \frac{\Delta t}{6} \left(k_1 + 2 k_2 + 2 k_3 + k_4\right)
\end{aligned}
$$

where

- $$k_1$$ is the slope at the start of the interval
- $$k_2$$ is the slope at the midpoint of the interval estimated using $$k_1$$
- $$k_3$$ is the slope at the midpoint of the interval estimated using $$k_2$$
- $$k_4$$ is the slope at the end of the interval estimated using $$k_2$$

Finally, based on the Taylor expansion, the weights for each slope are obtained, and $$y_{n+1}$$ is computed comprehensively.

> Here is the detailed algorithm for the Taylor expansion.
>
> For the RK4 numerical solution $$y_{n+1} = y_n + \Delta t \left(a_1 k_1 + a_2 k_2 + a_3 k_3 + a_4 k_4\right)$$, we want it to match the Taylor expansion of the analytical solution as closely as possible in terms of the orders of $$\Delta t$$.
>
> We have:
>
> - $$k_1 = f\left(t_n, y_n\right)$$​
> - $$k_2 = f\left(t_n + \frac{\Delta t}{2}, y_n + \frac{\Delta t}{2} k_1\right)$$​
> - $$k_3 = f\left(t_n + \frac{\Delta t}{2}, y_n + \frac{\Delta t}{2} k_2\right)$$​
> - $$k_4 = f\left(t_n + \Delta t, y_n + \Delta t k_3\right)$$​
>
> Substituting the expanded $$k_1$$, $$k_2$$, $$k_3$$, $$k_4$$ into the expression for $$y_{n+1}$$ and comparing with the Taylor expansion of the analytical solution yields a set of equations for the coefficients of each order of $$\Delta t$$.
>
> Comparing the coefficients of $$\Delta t$$, we have:
>
> - First-order term: $$a_1 + a_2 + a_3 + a_4 = 1$$
> - Second-order term: $$\frac{1}{2} a_2 + \frac{1}{2} a_3 + a_4 = \frac{1}{2}$$
> - Third-order term: $$\frac{1}{4} a_2 + \frac{1}{4} a_3 + a_4 = \frac{1}{6}$$
> - Fourth-order term: $$\frac{1}{8} a_2 + \frac{1}{8} a_3 + a_4 = \frac{1}{24}$$
>
> Solving this system of equations gives:
>
> $$
> a_1 = \frac{1}{6}, \quad a_2 = \frac{1}{3}, \quad a_3 = \frac{1}{3}, \quad a_4 = \frac{1}{6}
> $$
>
> Therefore, the update formula for the RK4 method is:
>
> $$
> y_{n+1} = y_n + \frac{\Delta t}{6} \left(k_1 + 2 k_2 + 2 k_3 + k_4\right)
> $$

The RK4 method has a local truncation error of $$O\left(\Delta t^5\right)$$ and a global truncation error of $$O\left(\Delta t^4\right)$$, significantly better than the Euler method's $$O\left(\Delta t^2\right)$$.

## The Simple Pendulum

<img src="/assets/post/images/pendulum1.svg" alt="Simple Pendulum" style="width:min(250px,100%);"/>

Assume a mass $$m$$ object is suspended from a fixed point by an inextensible, massless string of length $$L$$, forming a simple pendulum system. Let $$\theta$$ be the angle between the pendulum bob and the vertical direction, then the Cartesian coordinates of the pendulum bob can be expressed as:

$$
\begin{aligned}
x_m &= L \sin{\theta} \\
y_m &= -L \cos{\theta}
\end{aligned}
$$

Find its first derivatives with respect to time:

$$
\begin{aligned}
\dot{x}_m &= L \dot{\theta}\cos{\theta} \\
\dot{y}_m &= L \dot{\theta}\sin{\theta}
\end{aligned}
$$

The kinetic energy is:

$$
\begin{aligned}
T &= \frac{1}{2} m {\nu}^2 \\
  &= \frac{1}{2} m \left(\dot{x}_m^2 + \dot{y}_m^2\right) \\
  &= \frac{1}{2} m \left(L^2 \dot{\theta}^2 \cos^2{\theta} + L^2 \dot{\theta}^2 \sin^2{\theta}\right) \\
  &= \frac{1}{2} m L^2 \dot{\theta}^2
\end{aligned}
$$

The potential energy is:

$$
\begin{aligned}
U &= m g h \\
  &= m g \left(-L \cos{\theta}\right) \\
  &= -m g L \cos{\theta}
\end{aligned}
$$

From this, the Lagrangian is calculated as:

$$
\begin{aligned}
\mathcal{L} &= T - U \\
            &= \frac{1}{2} m L^2 \dot{\theta}^2 -\left(- m g L \cos{\theta}\right) \\
            &= \frac{1}{2} m L^2 \dot{\theta}^2 + m g L \cos{\theta} \\
            &= m L \left(\frac{1}{2} L \dot{\theta}^2 + g \cos{\theta}\right)
\end{aligned}
$$

Solve the Euler-Lagrange differential equation[^1]:

$$
\begin{aligned}
\frac{d}{dt} \left(\frac{\partial \mathcal{L}}{\partial \dot{\theta}}\right) - \frac{\partial \mathcal{L}}{\partial \theta} &= 0 \\
\frac{d}{dt} \left(\frac{\partial }{\partial \dot{\theta}} \left[m L \left(\frac{1}{2} L \dot{\theta}^2 + g \cos{\theta}\right)\right]\right) \\
- \frac{\partial }{\partial \theta} \left[m L \left(\frac{1}{2} L \dot{\theta}^2 + g \cos{\theta}\right)\right] &= 0 \\
\frac{d}{dt} \left[\frac{\partial }{\partial \dot{\theta}} \left(\frac{1}{2} L \dot{\theta}^2 + g \cos{\theta}\right)\right] \\
- \frac{\partial }{\partial \theta} \left(\frac{1}{2} L \dot{\theta}^2 + g \cos{\theta}\right) &= 0 \\
\frac{d}{dt} \left[\frac{\partial }{\partial \dot{\theta}} \left(\frac{1}{2} L \dot{\theta}^2\right) + \frac{\partial }{\partial \dot{\theta}} \left(g \cos{\theta}\right)\right] \\
- \left[\frac{\partial }{\partial \theta} \left(\frac{1}{2} L \dot{\theta}^2\right) + \frac{\partial }{\partial \theta} \left(g \cos{\theta}\right)\right] &= 0 \\
\frac{d}{dt} \left(L \dot{\theta} + 0\right) - \left[0 + \left(- g \sin{\theta}\right)\right] &= 0 \\
\frac{d}{dt} \left(L \dot{\theta}\right) + g \sin{\theta} &= 0 \\
L \ddot{\theta} + g \sin{\theta} &= 0
\end{aligned}
$$

$$
\ddot{\theta} = -\frac{g \sin{\theta}}{L}
$$

Given initial conditions $$\theta(0) = \theta_0$$, $$\dot{\theta}(0) = \omega_0$$, the equation for $$\ddot{\theta}$$ obtained above can be used for numerical simulation, iteratively calculating the values of $$\theta(t)$$ and $$\dot{\theta}(t)$$:

```javascript
let theta = theta0; // initial angle
let omega = omega0; // initial angular velocity
let dt = 0.01;      // time step size

function simulatePendulum() {
    let alpha = - g * Math.sin(theta) / L; // calculate angular acceleration
    omega += alpha * dt;                   // update angular velocity
    theta += omega * dt;                   // update angle
}

while (true) {
    simulatePendulum(); // continuous simulation
    // Here you can add code to draw or record the values of theta and omega
}
```

However, this method produces larger errors when the angle is large, so more accurate numerical integration methods like the Runge-Kutta method can be used to improve simulation precision.

The Runge-Kutta method uses fourth-order approximation to update state variables:

$$
\begin{aligned}
k_1^{\theta} &= \omega \\
k_1^{\omega} &= -\frac{g}{L} \sin{\theta} \\
k_2^{\theta} &= \omega + 0.5 k_1^{\omega} dt \\
k_2^{\omega} &= -\frac{g}{L} \sin{\left(\theta + 0.5 k_1^{\theta} dt\right)} \\
k_3^{\theta} &= \omega + 0.5 k_2^{\omega} dt \\
k_3^{\omega} &= -\frac{g}{L} \sin{\left(\theta + 0.5 k_2^{\theta} dt\right)} \\
k_4^{\theta} &= \omega + k_3^{\omega} dt \\
k_4^{\omega} &= -\frac{g}{L} \sin{\left(\theta + k_3^{\theta} dt\right)} \\
\theta &= \theta + \frac{1}{6} \left(k_1^{\theta} + 2 k_2^{\theta} + 2 k_3^{\theta} + k_4^{\theta}\right) \\
\omega &= \omega + \frac{1}{6} \left(k_1^{\omega} + 2 k_2^{\omega} + 2 k_3^{\omega} + k_4^{\omega}\right)
\end{aligned}
$$

```javascript
let theta = theta0; // initial angle
let omega = omega0; // initial angular velocity
let dt = 0.01;      // time step size

function simulatePendulum() {
    let k1_theta = omega;
    let k1_omega = - (g / L) * Math.sin(theta);
    let k2_theta = omega + 0.5 * k1_omega * dt;
    let k2_omega = - (g / L) * Math.sin(theta + 0.5 * k1_theta * dt);
    let k3_theta = omega + 0.5 * k2_omega * dt;
    let k3_omega = - (g / L) * Math.sin(theta + 0.5 * k2_theta * dt);
    let k4_theta = omega + k3_omega * dt;
    let k4_omega = - (g / L) * Math.sin(theta + k3_theta * dt);

    theta += (k1_theta + 2 * k2_theta + 2 * k3_theta + k4_theta) / 6;
    omega += (k1_omega + 2 * k2_omega + 2 * k3_omega + k4_omega) / 6;
}

while (true) {
    simulatePendulum(); // continuous simulation
    // Here you can add code to draw or record the values of theta and omega
}
```

Below is the complete code example:

{% result title="Pendulum Simulation" height=600px hide=code %}
```html
<div class="settings-wrap">
    <label for="L-input">L: <input type="number" id="L-input" value="150" step="1" min="50" max="300"></label>
    <label for="m-input">m: <input type="number" id="m-input" value="1" step="0.1" min="0.1" max="10"></label>
    <label for="dt-input">Time Step Size: <input type="number" id="dt-input" value="0.35" step="0.01" min="0.01" max="1"></label>
</div>
<div class="settings-wrap">
    <label for="theta-slider">Initial Angle: <span id="theta-value">0.785</span></label>
    <input type="range" id="theta-slider" min="0" max="6.2832" step="0.01" value="0.785">
</div>
<svg id="pendulum-svg" width="400" height="400"></svg>
<button id="pause-btn" class="pause-btn" style="margin-top: 10px;">Pause</button>
‌‌‍```

```css
* {
    box-sizing: border-box;
}

body {
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    height: 100%;
    margin: 0;
    background-color: #f0f0f0;
}

svg {
    border: 1px solid #ccc;
    background-color: white;
}

.settings-wrap {
    display: flex;
    flex-wrap: wrap;
    gap: 10px;
    margin-bottom: 10px;
}

.settings-wrap label {
    font-size: 14px;
}

.settings-wrap input {
    margin-left: 5px;
}

.settings-wrap input[type="number"] {
    width: 50px;
}

.settings-wrap input[type="range"] {
    width: 180px;
}

.pause-btn {
    outline: none;
    border: none;
    background-color: #d74514;
    color: white;
    border-radius: 4px;
    padding: 4px 16px;
    font-size: 16px;
    cursor: pointer;
}

.pause-btn:hover {
    background-color: #a32d0f;
}
```

```javascript
const svg = document.getElementById('pendulum-svg');
const width = svg.clientWidth;
const height = svg.clientHeight;

let L = 150;  /* string length */
let m = 1;    /* mass */
const g = 9.81; /* gravitational acceleration */

let theta = Math.PI / 4;  /* initial angle */
let omega = 0;            /* initial angular velocity */
let dt = 0.35;          /* time step size */

let animationId;
let isPaused = false;

const thetaSlider = document.getElementById('theta-slider');
const thetaValue = document.getElementById('theta-value');
const LInput = document.getElementById('L-input');
const mInput = document.getElementById('m-input');
const dtInput = document.getElementById('dt-input');
const pauseBtn = document.getElementById('pause-btn');

function simulatePendulum() {
    const k1_theta = omega;
    const k1_omega = - (g / L) * Math.sin(theta);

    const k2_theta = omega + 0.5 * k1_omega * dt;
    const k2_omega = - (g / L) * Math.sin(theta + 0.5 * k1_theta * dt);

    const k3_theta = omega + 0.5 * k2_omega * dt;
    const k3_omega = - (g / L) * Math.sin(theta + 0.5 * k2_theta * dt);

    const k4_theta = omega + k3_omega * dt;
    const k4_omega = - (g / L) * Math.sin(theta + k3_theta * dt);

    theta += (k1_theta + 2 * k2_theta + 2 * k3_theta + k4_theta) / 6 * dt;
    omega += (k1_omega + 2 * k2_omega + 2 * k3_omega + k4_omega) / 6 * dt;
}

function drawPendulum(theta) {
    svg.innerHTML = ''; /* clear SVG content */

    const x_m = width / 2 + L * Math.sin(theta);
    const y_m = height / 4 + L * Math.cos(theta);

    /* draw string */
    const line = document.createElementNS("http://www.w3.org/2000/svg", "line");
    line.setAttribute("x1", width / 2);
    line.setAttribute("y1", height / 4);
    line.setAttribute("x2", x_m);
    line.setAttribute("y2", y_m);
    line.setAttribute("stroke", "black");
    line.setAttribute("stroke-width", "1");
    svg.appendChild(line);

    /* draw mass block */
    const circle = document.createElementNS("http://www.w3.org/2000/svg", "circle");
    circle.setAttribute("cx", x_m);
    circle.setAttribute("cy", y_m);
    circle.setAttribute("r", "10");
    circle.setAttribute("fill", "#d74514");
    svg.appendChild(circle);
}

function animate() {
    simulatePendulum();
    drawPendulum(theta);
    animationId = requestAnimationFrame(animate);
}

function resetAnimation() {
    cancelAnimationFrame(animationId);
    theta = parseFloat(thetaSlider.value);
    omega = 0; /* fixed initial angular velocity to 0 */
    L = parseFloat(LInput.value);
    m = parseFloat(mInput.value);
    dt = parseFloat(dtInput.value);
    if (!isPaused) {
        animate();
    }
}

pauseBtn.addEventListener('click', () => {
    if (isPaused) {
        isPaused = false;
        animate();
        pauseBtn.textContent = 'Pause';
    } else {
        isPaused = true;
        cancelAnimationFrame(animationId);
        pauseBtn.textContent = 'Resume';
    }
});

thetaSlider.addEventListener('input', () => {
    thetaValue.textContent = thetaSlider.value;
    resetAnimation();
});

LInput.addEventListener('input', resetAnimation);
mInput.addEventListener('input', resetAnimation);
dtInput.addEventListener('input', resetAnimation);

animate();
```
{% endresult %}

## The Spring Pendulum

<img src="/assets/post/images/pendulum2.svg" alt="Spring Pendulum" style="width:min(250px,100%);"/>

Assume a mass $$m$$ object is connected to a fixed point by a spring with natural length $$L_0$$ and spring constant $$k$$. Let $$\theta$$ be the angle between the pendulum bob and the vertical direction, and $$L^\prime$$ be the extension of the spring, then the Cartesian coordinates of the pendulum bob can be expressed as:

$$
\begin{aligned}
x_m &= (L_0 + L^\prime) \sin{\theta} \\
y_m &= -(L_0 + L^\prime) \cos{\theta}
\end{aligned}
$$

Find its first derivatives with respect to time:

$$
\begin{aligned}
\dot{x}_m &= \dot{L}^\prime \sin{\theta} + \left(L_0 + L^\prime\right) \dot{\theta} \cos{\theta} \\
\dot{y}_m &= -\dot{L}^\prime \cos{\theta} + \left(L_0 + L^\prime\right) \dot{\theta} \sin{\theta}
\end{aligned}
$$

The kinetic energy is:

$$
\begin{aligned}
T =& \frac{1}{2} m {\nu}^2 \\
  =& \frac{1}{2} m \left(\dot{x}_m^2 + \dot{y}_m^2\right) \\
  =& \frac{1}{2} m \\
  & \left({\left[\dot{L}^\prime \sin{\theta} + \left(L_0 + L^\prime\right) \dot{\theta} \cos{\theta}\right]}^2 + {\left[-\dot{L}^\prime \cos{\theta} + \left(L_0 + L^\prime\right) \dot{\theta} \sin{\theta}\right]}^2\right) \\
  =& \frac{1}{2} m \left[\dot{L}^{\prime 2} \left(\sin^2{\theta} + \cos^2{\theta}\right) + \left(L_0 + L^\prime\right)^2 \dot{\theta}^2 \left(\cos^2{\theta} + \sin^2{\theta}\right)\right] \\
  =& \frac{1}{2} m \left[\dot{L}^{\prime 2} + \left(L_0 + L^\prime\right)^2 \dot{\theta}^2\right]
\end{aligned}
$$

The potential energy is:

$$
\begin{aligned}
U =& U_{\text{gravity}} + U_{\text{spring}} \\
  =& m g h + \frac{1}{2} k L^{\prime 2} \\
  =& m g \left[-\left(L_0 + L^\prime\right) \cos{\theta}\right] + \frac{1}{2} k L^{\prime 2} \\
  =& -m g \left(L_0 + L^\prime\right) \cos{\theta} + \frac{1}{2} k L^{\prime 2}
\end{aligned}
$$

From this, the Lagrangian is calculated as:

$$
\begin{aligned}
\mathcal{L} =& T - U \\
            =& \frac{1}{2} m \left[\dot{L}^{\prime 2} + \left(L_0 + L^\prime\right)^2 \dot{\theta}^2\right] \\
            & - \left[-m g \left(L_0 + L^\prime\right) \cos{\theta} + \frac{1}{2} k L^{\prime 2}\right] \\
            =& \frac{1}{2} m \left[\dot{L}^{\prime 2} + \left(L_0 + L^\prime\right)^2 \dot{\theta}^2\right] \\
            & + m g \left(L_0 + L^\prime\right) \cos{\theta} - \frac{1}{2} k L^{\prime 2}
\end{aligned}
$$

Solve the Euler-Lagrange differential equation:

$$
\begin{aligned}
\frac{d}{dt} \left(\frac{\partial \mathcal{L}}{\partial \dot{L}^\prime}\right) - \frac{\partial \mathcal{L}}{\partial L^\prime} &= 0 \\
\frac{d}{dt} \left(\frac{\partial }{\partial \dot{L}^\prime} \left[\frac{1}{2} m \left(\dot{L}^{\prime 2} + \left(L_0 + L^\prime\right)^2 \dot{\theta}^2\right) + m g \left(L_0 + L^\prime\right) \cos{\theta} - \frac{1}{2} k L^{\prime 2}\right]\right) \\
- \frac{\partial }{\partial L^\prime} \left[\frac{1}{2} m \left(\dot{L}^{\prime 2} + \left(L_0 + L^\prime\right)^2 \dot{\theta}^2\right) + m g \left(L_0 + L^\prime\right) \cos{\theta} - \frac{1}{2} k L^{\prime 2}\right] &= 0 \\
\frac{d}{dt} \left[m \dot{L}^\prime\right] - \left[m \left(L_0 + L^\prime\right) \dot{\theta}^2 + m g \cos{\theta} - k L^\prime\right] &= 0 \\
m \ddot{L}^\prime - m \left(L_0 + L^\prime\right) \dot{\theta}^2 - m g \cos{\theta} + k L^\prime &= 0
\end{aligned}
$$

$$
\begin{aligned}
\ddot{L}^\prime &= \left(L_0 + L^\prime\right) \dot{\theta}^2 + g \cos{\theta} - \frac{k}{m} L^\prime \\
\ddot{\theta} &= -\frac{2 \dot{L}^\prime \dot{\theta} + g \sin{\theta}}{L_0 + L^\prime}
\end{aligned}
$$

Given initial conditions $$L^\prime(0) = L_0^\prime$$, $$\dot{L}^\prime(0) = v_0$$, $$\theta(0) = \theta_0$$, $$\dot{\theta}(0) = \omega_0$$, the equations for $$\ddot{L}^\prime$$ and $$\ddot{\theta}$$ obtained above can be used for numerical simulation, iteratively calculating the values of $$L^\prime(t)$$, $$\dot{L}^\prime(t)$$, $$\theta(t)$$ and $$\dot{\theta}(t)$$:

```javascript
let L0 = 100;           // spring natural length
let k = 0.5;            // spring constant
let m = 1;              // mass
let g = 9.81;           // gravitational acceleration
let L_prime = L0_prime; // initial spring extension
let v = v0;             // initial extension velocity
let theta = theta0;     // initial angle
let omega = omega0;     // initial angular velocity
let dt = 0.01;          // time step size

function simulateSpringPendulum() {
    let alpha_L = (L0 + L_prime) * omega * omega + g * Math.cos(theta) - (k / m) * L_prime; // calculate extension acceleration
    let alpha_theta = - (2 * v * omega + g * Math.sin(theta)) / (L0 + L_prime);              // calculate angular acceleration
    v += alpha_L * dt;                   // update extension velocity
    L_prime += v * dt;                   // update extension
    omega += alpha_theta * dt;           // update angular velocity
    theta += omega * dt;                 // update angle
}

while (true) {
    simulateSpringPendulum(); // continuous simulation
    // Here you can add code to draw or record the values of L_prime, v, theta and omega
}
```

Similarly, the Runge-Kutta method can be used to improve simulation precision.

$$
\begin{aligned}
k_1^{L} =& v \\
k_1^{v} =& \left(L_0 + L^\prime\right) \omega^2 + g \cos{\theta} - \frac{k}{m} L^\prime \\
k_1^{\theta} =& \omega \\
k_1^{\omega} =& -\frac{2 v \omega + g \sin{\theta}}{L_0 + L^\prime} \\
k_2^{L} =& v + 0.5 k_1^{v} dt \\
k_2^{v} =& \left(L_0 + L^\prime + 0.5 k_1^{L} dt\right) \left(\omega + 0.5 k_1^{\omega} dt\right)^2 \\
& + g \cos{\left(\theta + 0.5 k_1^{\theta} dt\right)} - \frac{k}{m} \left(L^\prime + 0.5 k_1^{L} dt\right) \\
k_2^{\theta} =& \omega + 0.5 k_1^{\omega} dt \\
k_2^{\omega} =& -\frac{2 \left(v + 0.5 k_1^{v} dt\right) \left(\omega + 0.5 k_1^{\omega} dt\right) + g \sin{\left(\theta + 0.5 k_1^{\theta} dt\right)}}{L_0 + L^\prime + 0.5 k_1^{L} dt} \\
k_3^{L} =& v + 0.5 k_2^{v} dt \\
k_3^{v} =& \left(L_0 + L^\prime + 0.5 k_2^{L} dt\right) \left(\omega + 0.5 k_2^{\omega} dt\right)^2 \\
& + g \cos{\left(\theta + 0.5 k_2^{\theta} dt\right)} - \frac{k}{m} \left(L^\prime + 0.5 k_2^{L} dt\right) \\
k_3^{\theta} =& \omega + 0.5 k_2^{\omega} dt \\
k_3^{\omega} =& -\frac{2 \left(v + 0.5 k_2^{v} dt\right) \left(\omega + 0.5 k_2^{\omega} dt\right) + g \sin{\left(\theta + 0.5 k_2^{\theta} dt\right)}}{L_0 + L^\prime + 0.5 k_2^{L} dt} \\
k_4^{L} =& v + k_3^{v} dt \\
k_4^{v} =& \left(L_0 + L^\prime + k_3^{L} dt\right) \left(\omega + k_3^{\omega} dt\right)^2 \\
& + g \cos{\left(\theta + k_3^{\theta} dt\right)} - \frac{k}{m} \left(L^\prime + k_3^{L} dt\right) \\
k_4^{\theta} =& \omega + k_3^{\omega} dt \\
k_4^{\omega} =& -\frac{2 \left(v + k_3^{v} dt\right) \left(\omega + k_3^{\omega} dt\right) + g \sin{\left(\theta + k_3^{\theta} dt\right)}}{L_0 + L^\prime + k_3^{L} dt} \\
L^\prime =& L^\prime + \frac{1}{6} \left(k_1^{L} + 2 k_2^{L} + 2 k_3^{L} + k_4^{L}\right) \\
v =& v + \frac{1}{6} \left(k_1^{v} + 2 k_2^{v} + 2 k_3^{v} + k_4^{v}\right) \\
\theta =& \theta + \frac{1}{6} \left(k_1^{\theta} + 2 k_2^{\theta} + 2 k_3^{\theta} + k_4^{\theta}\right) \\
\omega =& \omega + \frac{1}{6} \left(k_1^{\omega} + 2 k_2^{\omega} + 2 k_3^{\omega} + k_4^{\omega}\right)
\end{aligned}
$$

```javascript
let L0 = 100;           // spring natural length
let k = 0.5;            // spring constant
let m = 1;              // mass
let g = 9.81;           // gravitational acceleration
let L_prime = L0_prime; // initial spring extension
let v = v0;             // initial extension velocity
let theta = theta0;     // initial angle
let omega = omega0;     // initial angular velocity
let dt = 0.01;          // time step size

function simulateSpringPendulum() {
    const k1_L = v;
    const k1_v = (L0 + L_prime) * omega * omega + g * Math.cos(theta) - (k / m) * L_prime;
    const k1_theta = omega;
    const k1_omega = - (2 * v * omega + g * Math.sin(theta)) / (L0 + L_prime);

    const k2_L = v + 0.5 * k1_v * dt;
    const k2_v = (L0 + L_prime + 0.5 * k1_L * dt) * (omega + 0.5 * k1_omega * dt) ** 2
                  + g * Math.cos(theta + 0.5 * k1_theta * dt)
                  - (k / m) * (L_prime + 0.5 * k1_L * dt);
    const k2_theta = omega + 0.5 * k1_omega * dt;
    const k2_omega = - (2 * (v + 0.5 * k1_v * dt) * (omega + 0.5 * k1_omega * dt)
                        + g * Math.sin(theta + 0.5 * k1_theta * dt))
                        / (L0 + L_prime + 0.5 * k1_L * dt);

    const k3_L = v + 0.5 * k2_v * dt;
    const k3_v = (L0 + L_prime + 0.5 * k2_L * dt) * (omega + 0.5 * k2_omega * dt) ** 2
                  + g * Math.cos(theta + 0.5 * k2_theta * dt)
                  - (k / m) * (L_prime + 0.5 * k2_L * dt);
    const k3_theta = omega + 0.5 * k2_omega * dt;
    const k3_omega = - (2 * (v + 0.5 * k2_v * dt) * (omega + 0.5 * k2_omega * dt)
                        + g * Math.sin(theta + 0.5 * k2_theta * dt))
                        / (L0 + L_prime + 0.5 * k2_L * dt);

    const k4_L = v + k3_v * dt;
    const k4_v = (L0 + L_prime + k3_L * dt) * (omega + k3_omega * dt) ** 2
                  + g * Math.cos(theta + k3_theta * dt)
                  - (k / m) * (L_prime + k3_L * dt);
    const k4_theta = omega + k3_omega * dt;
    const k4_omega = - (2 * (v + k3_v * dt) * (omega + k3_omega * dt)
                        + g * Math.sin(theta + k3_theta * dt))
                        / (L0 + L_prime + k3_L * dt);

    L_prime += (k1_L + 2 * k2_L + 2 * k3_L + k4_L) / 6 * dt;
    v += (k1_v + 2 * k2_v + 2 * k3_v + k4_v) / 6 * dt;
    theta += (k1_theta + 2 * k2_theta + 2 * k3_theta + k4_theta) / 6 * dt;
    omega += (k1_omega + 2 * k2_omega + 2 * k3_omega + k4_omega) / 6 * dt;
}

while (true) {
    simulateSpringPendulum(); // continuous simulation
    // Here you can add code to draw or record the values of L_prime, v, theta and omega
}
```

Before implementation, we need to note that the spring can only produce tension, not compression, so when the spring extension $$L^\prime < 0$$, the spring force should be $$0$$, and the object will only be subject to gravity.

Below is the complete code example:

{% result title="Spring Pendulum Simulation" height=630px hide=code %}
```html
<div class="settings-wrap">
    <label for="L0-input">L0: <input type="number" id="L0-input" value="100" step="1" min="50" max="200"></label>
    <label for="Lprime-input">L': <input type="number" id="Lprime-input" value="0" step="1" min="0" max="100"></label>
    <label for="k-input">k: <input type="number" id="k-input" value="0.5" step="0.1" min="0.1" max="5"></label>
</div>
<div class="settings-wrap">
    <label for="m-input">m: <input type="number" id="m-input" value="2" step="0.1" min="0.1" max="10"></label>
    <label for="dt-input">Time Step Size: <input type="number" id="dt-input" value="0.35" step="0.01" min="0.01" max="1"></label>
</div>
<div class="settings-wrap">
    <label for="theta-slider">Initial Angle: <span id="theta-value">0.785</span></label>
    <input type="range" id="theta-slider" min="0" max="6.2832" step="0.01" value="0.785">
</div>
<svg id="spring-pendulum-svg" width="400" height="400"></svg>
<button id="pause-btn" class="pause-btn" style="margin-top: 10px;">Pause</button>
‌‌‍```

```css
* {
    box-sizing: border-box;
}

body {
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    height: 100%;
    margin: 0;
    background-color: #f0f0f0;
}

svg {
    border: 1px solid #ccc;
    background-color: white;
}

.settings-wrap {
    display: flex;
    flex-wrap: wrap;
    gap: 10px;
    margin-bottom: 10px;
}

.settings-wrap label {
    font-size: 14px;
}

.settings-wrap input {
    margin-left: 5px;
}

.settings-wrap input[type="number"] {
    width: 50px;
}

.settings-wrap input[type="range"] {
    width: 180px;
}

.pause-btn {
    outline: none;
    border: none;
    background-color: #d74514;
    color: white;
    border-radius: 4px;
    padding: 4px 16px;
    font-size: 16px;
    cursor: pointer;
}

.pause-btn:hover {
    background-color: #a32d0f;
}
```

```javascript
const svg = document.getElementById('spring-pendulum-svg');
const width = svg.clientWidth;
const height = svg.clientHeight;

let L0 = 100;           /* spring natural length */
let k = 0.5;            /* spring constant */
let m = 2;              /* mass */
const g = 9.81;         /* gravitational acceleration */

let L_prime = 0;          /* initial spring extension */
let v = 0;                /* initial extension velocity */
let theta = Math.PI / 4;  /* initial angle */
let omega = 0;            /* initial angular velocity */
let dt = 0.35;            /* time step size */

let animationId;
let isPaused = false;

const LprimeInput = document.getElementById('Lprime-input');
const thetaSlider = document.getElementById('theta-slider');
const thetaValue = document.getElementById('theta-value');
const L0Input = document.getElementById('L0-input');
const kInput = document.getElementById('k-input');
const mInput = document.getElementById('m-input');
const dtInput = document.getElementById('dt-input');
const pauseBtn = document.getElementById('pause-btn');

function simulateSpringPendulum() {
    const spring_force1 = L_prime > 0 ? (k / m) * L_prime : 0;
    const k1_L = v;
    const k1_v = (L0 + L_prime) * omega * omega + g * Math.cos(theta) - spring_force1;
    const k1_theta = omega;
    const k1_omega = - (2 * v * omega + g * Math.sin(theta)) / (L0 + L_prime);

    const k2_L = v + 0.5 * k1_v * dt;
    const spring_force2 = (L_prime + 0.5 * k1_L * dt) > 0 ? (k / m) * (L_prime + 0.5 * k1_L * dt) : 0;
    const k2_v = (L0 + L_prime + 0.5 * k1_L * dt) * (omega + 0.5 * k1_omega * dt) ** 2
                  + g * Math.cos(theta + 0.5 * k1_theta * dt)
                  - spring_force2;
    const k2_theta = omega + 0.5 * k1_omega * dt;
    const k2_omega = - (2 * (v + 0.5 * k1_v * dt) * (omega + 0.5 * k1_omega * dt)
                        + g * Math.sin(theta + 0.5 * k1_theta * dt))
                        / (L0 + L_prime + 0.5 * k1_L * dt);

    const k3_L = v + 0.5 * k2_v * dt;
    const spring_force3 = (L_prime + 0.5 * k2_L * dt) > 0 ? (k / m) * (L_prime + 0.5 * k2_L * dt) : 0;
    const k3_v = (L0 + L_prime + 0.5 * k2_L * dt) * (omega + 0.5 * k2_omega * dt) ** 2
                  + g * Math.cos(theta + 0.5 * k2_theta * dt)
                  - spring_force3;
    const k3_theta = omega + 0.5 * k2_omega * dt;
    const k3_omega = - (2 * (v + 0.5 * k2_v * dt) * (omega + 0.5 * k2_omega * dt)
                        + g * Math.sin(theta + 0.5 * k2_theta * dt))
                        / (L0 + L_prime + 0.5 * k2_L * dt);

    const k4_L = v + k3_v * dt;
    const spring_force4 = (L_prime + k3_L * dt) > 0 ? (k / m) * (L_prime + k3_L * dt) : 0;
    const k4_v = (L0 + L_prime + k3_L * dt) * (omega + k3_omega * dt) ** 2
                  + g * Math.cos(theta + k3_theta * dt)
                  - spring_force4;
    const k4_theta = omega + k3_omega * dt;
    const k4_omega = - (2 * (v + k3_v * dt) * (omega + k3_omega * dt)
                        + g * Math.sin(theta + k3_theta * dt))
                        / (L0 + L_prime + k3_L * dt);

    L_prime += (k1_L + 2 * k2_L + 2 * k3_L + k4_L) / 6 * dt;
    v += (k1_v + 2 * k2_v + 2 * k3_v + k4_v) / 6 * dt;
    theta += (k1_theta + 2 * k2_theta + 2 * k3_theta + k4_theta) / 6 * dt;
    omega += (k1_omega + 2 * k2_omega + 2 * k3_omega + k4_omega) / 6 * dt;
}

function drawSpringPendulum(L_prime, theta) {
    svg.innerHTML = ''; /* clear SVG content */

    const L_total = L0 + L_prime;
    const x_m = width / 2 + L_total * Math.sin(theta);
    const y_m = height / 4 + L_total * Math.cos(theta);

    /* calculate tightness */
    let color;
    let dashArray = "none";
    if (L_prime <= 0) {
        color = "gray";
        dashArray = "5,5";
    } else {
        const max_L = 60; /* assume maximum extension is 60 */
        const tightness = Math.min(1, L_prime / max_L);
        const hue = 120 - 120 * tightness; /* from green (120) to red (0) */
        color = `hsl(${hue}, 100%, 50%)`;
    }

    /* draw spring */
    const spring = document.createElementNS("http://www.w3.org/2000/svg", "line");
    spring.setAttribute("x1", width / 2);
    spring.setAttribute("y1", height / 4);
    spring.setAttribute("x2", x_m);
    spring.setAttribute("y2", y_m);
    spring.setAttribute("stroke", color);
    spring.setAttribute("stroke-width", "1");
    spring.setAttribute("stroke-dasharray", dashArray);
    svg.appendChild(spring);

    /* draw mass block */
    const circle = document.createElementNS("http://www.w3.org/2000/svg", "circle");
    circle.setAttribute("cx", x_m);
    circle.setAttribute("cy", y_m);
    circle.setAttribute("r", "10");
    circle.setAttribute("fill", "#d74514");
    svg.appendChild(circle);
}

function animate() {
    simulateSpringPendulum();
    drawSpringPendulum(L_prime, theta);
    animationId = requestAnimationFrame(animate);
}

function resetAnimation() {
    cancelAnimationFrame(animationId);
    L_prime = parseFloat(LprimeInput.value);
    v = 0; /* fixed initial extension velocity to 0 */
    theta = parseFloat(thetaSlider.value);
    omega = 0; /* fixed initial angular velocity to 0 */
    L0 = parseFloat(L0Input.value);
    k = parseFloat(kInput.value);
    m = parseFloat(mInput.value);
    dt = parseFloat(dtInput.value);
    if (!isPaused) {
        animate();
    }
}

pauseBtn.addEventListener('click', () => {
    if (isPaused) {
        isPaused = false;
        animate();
        pauseBtn.textContent = 'Pause';
    } else {
        isPaused = true;
        cancelAnimationFrame(animationId);
        pauseBtn.textContent = 'Resume';
    }
});

LprimeInput.addEventListener('input', resetAnimation);
thetaSlider.addEventListener('input', () => {
    thetaValue.textContent = thetaSlider.value;
    resetAnimation();
});
L0Input.addEventListener('input', resetAnimation);
kInput.addEventListener('input', resetAnimation);
mInput.addEventListener('input', resetAnimation);
dtInput.addEventListener('input', resetAnimation);

animate();
```
{% endresult %}

Here we use the color of the string to represent the tightness of the spring.

You can try increasing the initial angle and observe the motion of the spring pendulum when the spring is slack.

> This code seems to have a bug; the ball gets stuck if it goes out of the canvas range, you can try to fix it.

## Discussion

In the above content, we see that it seems we can only use numerical methods to simulate the trajectory of the spring pendulum. Is it impossible to obtain an analytical solution?

In fact, the equations of motion for the spring pendulum are a coupled system of nonlinear differential equations, which generally cannot be solved analytically. However, under certain specific conditions, the equations can be linearized to obtain approximate analytical solutions. For example, when the swing angle is small and the spring extension is small, we can substitute $$\sin{\theta} \approx \theta$$ and $$L^\prime \approx 0$$ into the equations, resulting in a simplified linear system of differential equations, from which approximate analytical solutions can be derived.

However, such approximate solutions are only valid within the range of small angles and small extensions; for cases of large angles or large extensions, we still need to rely on numerical methods for simulation.

For the choice of numerical solution methods, in addition to the Runge-Kutta method introduced above, other numerical integration methods can be considered, such as the Verlet integration method, Leapfrog method, etc. These methods have better energy conservation properties when dealing with physical systems, making them suitable for long-term simulations.

However, even with more advanced numerical methods, our final results may still have issues due to the precision limitations of floating-point arithmetic. For example, JavaScript has a famous floating-point precision problem: $$0.1 + 0.2 = 0.30000000000000004$$:

{% iframe code_runner height=250px language=javascript code=console.log(0.1%20+%200.2); %}

Such precision errors accumulate over long-term numerical simulations, eventually causing the results to deviate from the true situation. Therefore, when performing numerical simulations, it may be necessary to incorporate some numerical stabilization techniques, such as adaptive time step adjustment, energy correction, etc., to reduce error accumulation.

[^1]: Partial reference: [Nima Aghdaii - Physics Simulation](https://nima101.github.io/physics_sim)
