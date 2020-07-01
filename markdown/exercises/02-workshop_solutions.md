---
author: "Chris Rackauckas"
title: "DifferentialEquations.jl Workshop Exercise Solutions"
---
````julia
using DifferentialEquations
````


````
Error: ArgumentError: Package DifferentialEquations not found in current pa
th:
- Run `import Pkg; Pkg.add("DifferentialEquations")` to install the Differe
ntialEquations package.
````



````julia
using Sundials
````


````
Error: ArgumentError: Package Sundials not found in current path:
- Run `import Pkg; Pkg.add("Sundials")` to install the Sundials package.
````



````julia
using BenchmarkTools
````


````
Error: ArgumentError: Package BenchmarkTools not found in current path:
- Run `import Pkg; Pkg.add("BenchmarkTools")` to install the BenchmarkTools
 package.
````



````julia
using Plots
````


````
Error: ArgumentError: Package Plots not found in current path:
- Run `import Pkg; Pkg.add("Plots")` to install the Plots package.
````





# Problem 1: Investigating Sources of Randomness and Uncertainty in a Biological System

## Part 1: Simulating the Oregonator ODE model

````julia
using DifferentialEquations, Plots
````


````
Error: ArgumentError: Package DifferentialEquations not found in current pa
th:
- Run `import Pkg; Pkg.add("DifferentialEquations")` to install the Differe
ntialEquations package.
````



````julia
function orego(du,u,p,t)
  s,q,w = p
  y1,y2,y3 = u
  du[1] = s*(y2+y1*(1-q*y1-y2))
  du[2] = (y3-(1+y1)*y2)/s
  du[3] = w*(y1-y3)
end
p = [77.27,8.375e-6,0.161]
prob = ODEProblem(orego,[1.0,2.0,3.0],(0.0,360.0),p)
````


````
Error: UndefVarError: ODEProblem not defined
````



````julia
sol = solve(prob)
````


````
Error: UndefVarError: solve not defined
````



````julia
plot(sol)
````


````
Error: UndefVarError: plot not defined
````



````julia
plot(sol,vars=(1,2,3))
````


````
Error: UndefVarError: plot not defined
````





## Part 2: Investigating Stiffness

````julia
using BenchmarkTools
````


````
Error: ArgumentError: Package BenchmarkTools not found in current path:
- Run `import Pkg; Pkg.add("BenchmarkTools")` to install the BenchmarkTools
 package.
````



````julia
prob = ODEProblem(orego,[1.0,2.0,3.0],(0.0,50.0),p)
````


````
Error: UndefVarError: ODEProblem not defined
````



````julia
@btime sol = solve(prob,Tsit5())
````


````
Error: LoadError: UndefVarError: @btime not defined
in expression starting at none:1
````



````julia
@btime sol = solve(prob,Rodas5())
````


````
Error: LoadError: UndefVarError: @btime not defined
in expression starting at none:1
````





## (Optional) Part 3: Specifying Analytical Jacobians (I)

## (Optional) Part 4: Automatic Symbolicification and Analytical Jacobian Calculations

## Part 5: Adding stochasticity with stochastic differential equations

````julia
function orego(du,u,p,t)
  s,q,w = p
  y1,y2,y3 = u
  du[1] = s*(y2+y1*(1-q*y1-y2))
  du[2] = (y3-(1+y1)*y2)/s
  du[3] = w*(y1-y3)
end
function g(du,u,p,t)
  du[1] = 0.1u[1]
  du[2] = 0.1u[2]
  du[3] = 0.1u[3]
end
p = [77.27,8.375e-6,0.161]
prob = SDEProblem(orego,g,[1.0,2.0,3.0],(0.0,30.0),p)
````


````
Error: UndefVarError: SDEProblem not defined
````



````julia
sol = solve(prob,SOSRI())
````


````
Error: UndefVarError: SOSRI not defined
````



````julia
plot(sol)
````


````
Error: UndefVarError: plot not defined
````



````julia
sol = solve(prob,ImplicitRKMil()); plot(sol)
````


````
Error: UndefVarError: ImplicitRKMil not defined
````



````julia
sol = solve(prob,ImplicitRKMil()); plot(sol)
````


````
Error: UndefVarError: ImplicitRKMil not defined
````





## Part 6: Gillespie jump models of discrete stochasticity

## Part 7: Probabilistic Programming / Bayesian Parameter Estimation with DiffEqBayes.jl + Turing.jl (I)

The data was generated with:

````julia
function orego(du,u,p,t)
  s,q,w = p
  y1,y2,y3 = u
  du[1] = s*(y2+y1*(1-q*y1-y2))
  du[2] = (y3-(1+y1)*y2)/s
  du[3] = w*(y1-y3)
end
p = [60.0,1e-5,0.2]
prob = ODEProblem(orego,[1.0,2.0,3.0],(0.0,30.0),p)
````


````
Error: UndefVarError: ODEProblem not defined
````



````julia
sol = solve(prob,Rodas5(),abstol=1/10^14,reltol=1/10^14)
````


````
Error: UndefVarError: Rodas5 not defined
````





## (Optional) Part 8: Using DiffEqBiological's Reaction Network DSL

# Problem 2: Fitting Hybrid Delay Pharmacokinetic Models with Automated Responses (B)

## Part 1: Defining an ODE with Predetermined Doses

````julia
function onecompartment(du,u,p,t)
  Ka,Ke = p
  du[1] = -Ka*u[1]
  du[2] =  Ka*u[1] - Ke*u[2]
end
p = (Ka=2.268,Ke=0.07398)
prob = ODEProblem(onecompartment,[100.0,0.0],(0.0,90.0),p)
````


````
Error: UndefVarError: ODEProblem not defined
````



````julia

tstops = [24,48,72]
condition(u,t,integrator) = t ∈ tstops
affect!(integrator) = (integrator.u[1] += 100)
cb = DiscreteCallback(condition,affect!)
````


````
Error: UndefVarError: DiscreteCallback not defined
````



````julia
sol = solve(prob,Tsit5(),callback=cb,tstops=tstops)
````


````
Error: UndefVarError: Tsit5 not defined
````



````julia
plot(sol)
````


````
Error: UndefVarError: plot not defined
````





## Part 2: Adding Delays

````julia
function onecompartment_delay(du,u,h,p,t)
  Ka,Ke,τ = p
  delayed_depot = h(p,t-τ)[1]
  du[1] = -Ka*u[1]
  du[2] =  Ka*delayed_depot - Ke*u[2]
end
p = (Ka=2.268,Ke=0.07398,τ=6.0)
h(p,t) = [0.0,0.0]
prob = DDEProblem(onecompartment_delay,[100.0,0.0],h,(0.0,90.0),p)
````


````
Error: UndefVarError: DDEProblem not defined
````



````julia

tstops = [24,48,72]
condition(u,t,integrator) = t ∈ tstops
affect!(integrator) = (integrator.u[1] += 100)
cb = DiscreteCallback(condition,affect!)
````


````
Error: UndefVarError: DiscreteCallback not defined
````



````julia
sol = solve(prob,MethodOfSteps(Rosenbrock23()),callback=cb,tstops=tstops)
````


````
Error: UndefVarError: Rosenbrock23 not defined
````



````julia
plot(sol)
````


````
Error: UndefVarError: plot not defined
````





## Part 3: Automatic Differentiation (AD) for Optimization (I)

## Part 4: Fitting Known Quantities with DiffEqParamEstim.jl + Optim.jl

The data was generated with

````julia
p = (Ka = 0.5, Ke = 0.1, τ = 4.0)
````


````
(Ka = 0.5, Ke = 0.1, τ = 4.0)
````





## Part 5: Implementing Control-Based Logic with ContinuousCallbacks (I)

## Part 6: Global Sensitivity Analysis with the Morris and Sobol Methods

# Problem 3: Differential-Algebraic Equation Modeling of a Double Pendulum (B)

## Part 1: Simple Introduction to DAEs: Mass-Matrix Robertson Equations
````julia
function f(du, u, p, t)
    du[1] = -p[1]*u[1] + p[2]*u[2]*u[3]
    du[2] = p[1]*u[1] - p[2]*u[2]*u[3] - p[3]*u[2]*u[2] 
    du[3] = u[1] + u[2] + u[3] - 1.
end
M = [1 0 0; 0 1 0; 0 0 0.]
p = [0.04, 10^4, 3e7]
u0 = [1.,0.,0.]
tspan = (0., 1e6)
prob = ODEProblem(ODEFunction(f, mass_matrix = M), u0, tspan, p)
````


````
Error: UndefVarError: ODEFunction not defined
````



````julia
sol = solve(prob, Rodas5())
````


````
Error: UndefVarError: Rodas5 not defined
````



````julia
plot(sol, xscale=:log10, tspan=(1e-6, 1e5), layout=(3,1))
````


````
Error: UndefVarError: plot not defined
````





## Part 2: Solving the Implicit Robertson Equations with IDA
````julia
# Robertson Equation DAE Implicit form 
function h(out, du, u, p, t)
    out[1] = -p[1]*u[1] + p[2]*u[2]*u[3] - du[1]
    out[2] = p[1]*u[1] - p[2]*u[2]*u[3] - p[3]*u[2]*u[2] - du[2]
    out[3] = u[1] + u[2] + u[3] - 1.
end
p = [0.04, 10^4, 3e7]
du0 = [-0.04, 0.04, 0.0]
u0 = [1.,0.,0.]
tspan = (0., 1e6)
differential_vars = [true, true, false]
prob = DAEProblem(h, du0, u0, tspan, p, differential_vars = differential_vars)
````


````
Error: UndefVarError: DAEProblem not defined
````



````julia
sol = solve(prob, IDA())
````


````
Error: UndefVarError: IDA not defined
````



````julia
plot(sol, xscale=:log10, tspan=(1e-6, 1e5), layout=(3,1))
````


````
Error: UndefVarError: plot not defined
````





## Part 3: Manual Index Reduction of the Single Pendulum
Consider the equation: 
$$
x^2 + y^2 = L
$$
Differentiating once with respect to time: 
$$
2x\dot{x} + 2y\dot{y} = 0 
$$
A second time: 
$$
\begin{align}
{\dot{x}}^2 + x\ddot{x} + {\dot{y}}^2 + y\ddot{y} &= 0  \\
u^2 + v^2 + x(\frac{x}{mL}T) + y(\frac{y}{mL}T - g) &= 0  \\
u^2 + v^2 + \frac{x^2 + y^2}{mL}T - yg &= 0 \\
u^2 + v^2 + \frac{T}{m} - yg &= 0
\end{align}
$$

Our final set of equations is hence
$$
\begin{align}
   \ddot{x} &= \frac{x}{mL}T \\
   \ddot{y} &= \frac{y}{mL}T - g \\
   \dot{x} &= u \\
   \dot{y} &= v \\
   u^2 + v^2 -yg + \frac{T}{m} &= 0
\end{align}
$$

We finally obtain $T$ into the third equation. 
This required two differentiations with respect 
to time, and so our system of equations went from 
index 3 to index 1. Now our solver can handle the 
index 1 system. 

## Part 4: Single Pendulum Solution with IDA
````julia
function f(out, da, a, p, t)
   (L, m, g) = p
   u, v, x, y, T = a
   du, dv, dx, dy, dT = da
   out[1] = x*T/(m*L) - du
   out[2] = y*T/(m*L) - g - dv
   out[3] = u - dx
   out[4] = v - dy
   out[5] = u^2 + v^2 - y*g + T/m
   nothing
end

# Release pendulum from top right 
u0 = zeros(5)
u0[3] = 1.0
du0 = zeros(5)
du0[2] = 9.81

p = [1,1,9.8]
tspan = (0.,100.)

differential_vars = [true, true, true, true, false]
prob = DAEProblem(f, du0, u0, tspan, p, differential_vars = differential_vars)
````


````
Error: UndefVarError: DAEProblem not defined
````



````julia
sol = solve(prob, IDA())
````


````
Error: UndefVarError: IDA not defined
````



````julia
plot(sol, vars=(3,4))
````


````
Error: UndefVarError: plot not defined
````





## Part 5: Solving the Double Penulum DAE System
For the double pendulum: 
The equations for the second ball are the same
as the single pendulum case. That is, the equations 
for the second ball are:
$$
\begin{align}
   \ddot{x_2} &= \frac{x_2}{m_2L_2}T_2 \\
   \ddot{y_2} &= \frac{y_2}{m_2L_2}T_2 - g \\
   \dot{x_2} &= u \\
   \dot{y_2} &= v \\
   u_2^2 + v_2^2 -y_2g + \frac{T_2}{m_2} &= 0
\end{align}
$$
For the first ball, consider $x_1^2 + y_1^2 = L $
$$
\begin{align}
x_1^2 + x_2^2 &= L \\
2x_1\dot{x_1} + 2y_1\dot{y_1} &= 0 \\
\dot{x_1}^2 + \dot{y_1}^2 + x_1(\frac{x_1}{m_1L_1}T_1 - \frac{x_2}{m_1L_2}T_2) + y_1(\frac{y_1}{m_1L_1}T_1 - g - \frac{y_2}{m_1L_2}T_2) &= 0 \\
u_1^2 + v_1^2 + \frac{T_1}{m_1} - \frac{x_1x_2 + y_1y_2}{m_1L_2}T_2 &= 0 
\end{align}
$$

So the final equations are: 
$$
\begin{align}
   \dot{u_2} &= x_2*T_2/(m_2*L_2)
   \dot{v_2} &= y_2*T_2/(m_2*L_2) - g
   \dot{x_2} &= u_2
   \dot{y_2} &= v_2 
   u_2^2 + v_2^2 -y_2*g + \frac{T_2}{m_2} &=  0
   
   \dot{u_1} &= x_1*T_1/(m_1*L_1) - x_2*T_2/(m_2*L_2)
   \dot{v_1} &= y_1*T_1/(m_1*L_1) - g - y_2*T_2/(m_2*L_2)
   \dot{x_1} &= u_1
   \dot{y_1} &= v_1
   u_1^2 + v_1^2 + \frac{T_1}{m_1} + 
                \frac{-x_1*x_2 - y_1*y_2}{m_1L_2}T_2 - y_1g &= 0
\end{align}
$$
````julia
function f(out, da, a, p, t)
   L1, m1, L2, m2, g = p

   u1, v1, x1, y1, T1, 
   u2, v2, x2, y2, T2 = a

   du1, dv1, dx1, dy1, dT1,
   du2, dv2, dx2, dy2, dT2 = da 

   out[1]  = x2*T2/(m2*L2) - du2
   out[2]  = y2*T2/(m2*L2) - g - dv2
   out[3]  = u2 - dx2
   out[4]  = v2 - dy2
   out[5]  = u2^2 + v2^2 -y2*g + T2/m2
   
   out[6]  = x1*T1/(m1*L1) - x2*T2/(m2*L2) - du1
   out[7]  = y1*T1/(m1*L1) - g - y2*T2/(m2*L2) - dv1
   out[8]  = u1 - dx1
   out[9]  = v1 - dy1
   out[10] = u1^2 + v1^2 + T1/m1 + 
                (-x1*x2 - y1*y2)/(m1*L2)*T2 - y1*g
   nothing
end

# Release pendulum from top right
u0 = zeros(10)
u0[3] = 1.0
u0[8] = 1.0
du0 = zeros(10)
du0[2] = 9.8
du0[7] = 9.8

p = [1,1,1,1,9.8]
tspan = (0.,100.)

differential_vars = [true, true, true, true, false, 
                     true, true, true, true, false]
prob = DAEProblem(f, du0, u0, tspan, p, differential_vars = differential_vars)
````


````
Error: UndefVarError: DAEProblem not defined
````



````julia
sol = solve(prob, IDA())
````


````
Error: UndefVarError: IDA not defined
````



````julia

plot(sol, vars=(3,4))
````


````
Error: UndefVarError: plot not defined
````



````julia
plot(sol, vars=(8,9))
````


````
Error: UndefVarError: plot not defined
````





# Problem 4: Performance Optimizing and Parallelizing Semilinear PDE Solvers (I)
## Part 1: Implementing the BRUSS PDE System as ODEs

````julia
using OrdinaryDiffEq, Sundials, Plots
````


````
Error: ArgumentError: Package OrdinaryDiffEq not found in current path:
- Run `import Pkg; Pkg.add("OrdinaryDiffEq")` to install the OrdinaryDiffEq
 package.
````



````julia

# initial condition
function init_brusselator_2d(xyd)
    N = length(xyd)
    u = zeros(N, N, 2)
    for I in CartesianIndices((N, N))
        x = xyd[I[1]]
        y = xyd[I[2]]
        u[I,1] = 22*(y*(1-y))^(3/2)
        u[I,2] = 27*(x*(1-x))^(3/2)
    end
    u
end

N = 32

xyd_brusselator = range(0,stop=1,length=N)

u0 = vec(init_brusselator_2d(xyd_brusselator))

tspan = (0, 22.)

p = (3.4, 1., 10., xyd_brusselator)

brusselator_f(x, y, t) = ifelse((((x-0.3)^2 + (y-0.6)^2) <= 0.1^2) &&
                                (t >= 1.1), 5., 0.)


using LinearAlgebra, SparseArrays
du = ones(N-1)
D2 = spdiagm(-1 => du, 0=>fill(-2.0, N), 1 => du)
D2[1, N] = D2[N, 1] = 1
D2 = 1/step(xyd_brusselator)^2*D2
tmp = Matrix{Float64}(undef, N, N)
function brusselator_2d_op(du, u, (D2, tmp, p), t)
    A, B, α, xyd = p
    dx = step(xyd)
    N = length(xyd)
    α = α/dx^2
    du = reshape(du, N, N, 2)
    u = reshape(u, N, N, 2)
    @views for i in axes(u, 3)
        ui = u[:, :, i]
        dui = du[:, :, i]
        mul!(tmp, D2, ui)
        mul!(dui, ui, D2')
        dui .+= tmp
    end

    @inbounds begin
        for I in CartesianIndices((N, N))
            x = xyd[I[1]]
            y = xyd[I[2]]
            i = I[1]
            j = I[2]

            du[i,j,1] = α*du[i,j,1] + B + u[i,j,1]^2*u[i,j,2] - (A + 1)*u[i,j,1] + brusselator_f(x, y, t)
            du[i,j,2] = α*du[i,j,2] + A*u[i,j,1] - u[i,j,1]^2*u[i,j,2]
        end
    end
    nothing
end

prob1 = ODEProblem(brusselator_2d_op, u0, tspan, (D2, tmp, p))
````


````
Error: UndefVarError: ODEProblem not defined
````



````julia

sol1 = @time solve(prob1, TRBDF2(autodiff=false));
````


````
Error: UndefVarError: TRBDF2 not defined
````





Visualizing the solution (works best in a terminal):
````julia
gr()
````


````
Error: UndefVarError: gr not defined
````



````julia
function plot_sol(sol)
    off = N^2
    for t in sol.t[1]:0.1:sol.t[end]
        solt = sol(t)
        plt1 = surface(reshape(solt[1:off], N, N), zlims=(0, 5), leg=false)
        surface!(plt1, reshape(solt[off+1:end], N, N), zlims=(0, 5), leg=false)
        display(plt1)
        sleep(0.05)
    end
    nothing
end

plot_sol(sol1)
````


````
Error: UndefVarError: sol1 not defined
````






## Part 2: Optimizing the BRUSS Code

````julia
function brusselator_2d_loop(du, u, p, t)
    A, B, α, xyd = p
    dx = step(xyd)
    N = length(xyd)
    α = α/dx^2
    limit = a -> let N=N
        a == N+1 ? 1 :
        a == 0 ? N :
        a
    end
    II = LinearIndices((N, N, 2))

    @inbounds begin
        for I in CartesianIndices((N, N))
            x = xyd[I[1]]
            y = xyd[I[2]]
            i = I[1]
            j = I[2]
            ip1 = limit(i+1)
            im1 = limit(i-1)
            jp1 = limit(j+1)
            jm1 = limit(j-1)

            ii1 = II[i,j,1]
            ii2 = II[i,j,2]

            du[II[i,j,1]] = α*(u[II[im1,j,1]] + u[II[ip1,j,1]] + u[II[i,jp1,1]] + u[II[i,jm1,1]] - 4u[ii1]) +
            B + u[ii1]^2*u[ii2] - (A + 1)*u[ii1] + brusselator_f(x, y, t)

            du[II[i,j,2]] = α*(u[II[im1,j,2]] + u[II[ip1,j,2]] + u[II[i,jp1,2]] + u[II[i,jm1,2]] - 4u[II[i,j,2]]) +
            A*u[ii1] - u[ii1]^2*u[ii2]
        end
    end
    nothing
end

prob2 = ODEProblem(brusselator_2d_loop, u0, tspan, p)
````


````
Error: UndefVarError: ODEProblem not defined
````



````julia

sol2 = @time solve(prob2, TRBDF2())
````


````
Error: UndefVarError: TRBDF2 not defined
````



````julia
sol2_2 = @time solve(prob2, CVODE_BDF())
````


````
Error: UndefVarError: CVODE_BDF not defined
````





## Part 3: Exploiting Jacobian Sparsity with Color Differentiation

````julia
using SparseDiffTools
````


````
Error: ArgumentError: Package SparseDiffTools not found in current path:
- Run `import Pkg; Pkg.add("SparseDiffTools")` to install the SparseDiffToo
ls package.
````



````julia

sparsity_pattern = sparsity!(brusselator_2d_loop,similar(u0),u0,p,2.0)
````


````
Error: UndefVarError: sparsity! not defined
````



````julia
jac_sp = sparse(sparsity_pattern)
````


````
Error: UndefVarError: sparsity_pattern not defined
````



````julia
jac = Float64.(jac_sp)
````


````
Error: UndefVarError: jac_sp not defined
````



````julia
colors = matrix_colors(jac)
````


````
Error: UndefVarError: matrix_colors not defined
````



````julia
prob3 = ODEProblem(ODEFunction(brusselator_2d_loop, colorvec=colors,jac_prototype=jac_sp), u0, tspan, p)
````


````
Error: UndefVarError: colors not defined
````



````julia
sol3 = @time solve(prob3, TRBDF2())
````


````
Error: UndefVarError: TRBDF2 not defined
````





## (Optional) Part 4: Structured Jacobians

## (Optional) Part 5: Automatic Symbolicification and Analytical Jacobian

## Part 6: Utilizing Preconditioned-GMRES Linear Solvers

````julia
using DiffEqOperators
````


````
Error: ArgumentError: Package DiffEqOperators not found in current path:
- Run `import Pkg; Pkg.add("DiffEqOperators")` to install the DiffEqOperato
rs package.
````



````julia
using Sundials
````


````
Error: ArgumentError: Package Sundials not found in current path:
- Run `import Pkg; Pkg.add("Sundials")` to install the Sundials package.
````



````julia
using AlgebraicMultigrid: ruge_stuben, aspreconditioner, smoothed_aggregation
````


````
Error: ArgumentError: Package AlgebraicMultigrid not found in current path:
- Run `import Pkg; Pkg.add("AlgebraicMultigrid")` to install the AlgebraicM
ultigrid package.
````



````julia
prob6 = ODEProblem(ODEFunction(brusselator_2d_loop, jac_prototype=JacVecOperator{Float64}(brusselator_2d_loop, u0)), u0, tspan, p)
````


````
Error: UndefVarError: JacVecOperator not defined
````



````julia
II = Matrix{Float64}(I, N, N)
Op = kron(Matrix{Float64}(I, 2, 2), kron(D2, II) + kron(II, D2))
Wapprox = -I+Op
#ml = ruge_stuben(Wapprox)
ml = smoothed_aggregation(Wapprox)
````


````
Error: UndefVarError: smoothed_aggregation not defined
````



````julia
precond = aspreconditioner(ml)
````


````
Error: UndefVarError: aspreconditioner not defined
````



````julia
sol_trbdf2 = @time solve(prob6, TRBDF2(linsolve=LinSolveGMRES())); # no preconditioner
````


````
Error: UndefVarError: LinSolveGMRES not defined
````



````julia
sol_trbdf2 = @time solve(prob6, TRBDF2(linsolve=LinSolveGMRES(Pl=lu(Wapprox)))); # sparse LU
````


````
Error: UndefVarError: LinSolveGMRES not defined
````



````julia
sol_trbdf2 = @time solve(prob6, TRBDF2(linsolve=LinSolveGMRES(Pl=precond))); # AMG
````


````
Error: UndefVarError: precond not defined
````



````julia
sol_cvodebdf = @time solve(prob2, CVODE_BDF(linear_solver=:GMRES));
````


````
Error: UndefVarError: CVODE_BDF not defined
````





## Part 7: Exploring IMEX and Exponential Integrator Techniques (E)

````julia
function laplacian2d(du, u, p, t)
    A, B, α, xyd = p
    dx = step(xyd)
    N = length(xyd)
    du = reshape(du, N, N, 2)
    u = reshape(u, N, N, 2)
    @inbounds begin
        α = α/dx^2
        limit = a -> let N=N
            a == N+1 ? 1 :
            a == 0 ? N :
            a
        end
        for I in CartesianIndices((N, N))
            x = xyd[I[1]]
            y = xyd[I[2]]
            i = I[1]
            j = I[2]
            ip1 = limit(i+1)
            im1 = limit(i-1)
            jp1 = limit(j+1)
            jm1 = limit(j-1)
            du[i,j,1] = α*(u[im1,j,1] + u[ip1,j,1] + u[i,jp1,1] + u[i,jm1,1] - 4u[i,j,1])
            du[i,j,2] = α*(u[im1,j,2] + u[ip1,j,2] + u[i,jp1,2] + u[i,jm1,2] - 4u[i,j,2])
        end
    end
    nothing
end
function brusselator_reaction(du, u, p, t)
    A, B, α, xyd = p
    dx = step(xyd)
    N = length(xyd)
    du = reshape(du, N, N, 2)
    u = reshape(u, N, N, 2)
    @inbounds begin
        for I in CartesianIndices((N, N))
            x = xyd[I[1]]
            y = xyd[I[2]]
            i = I[1]
            j = I[2]
            du[i,j,1] = B + u[i,j,1]^2*u[i,j,2] - (A + 1)*u[i,j,1] + brusselator_f(x, y, t)
            du[i,j,2] = A*u[i,j,1] - u[i,j,1]^2*u[i,j,2]
        end
    end
    nothing
end
prob7 = SplitODEProblem(laplacian2d, brusselator_reaction, u0, tspan, p)
````


````
Error: UndefVarError: SplitODEProblem not defined
````



````julia
sol7 = @time solve(prob7, KenCarp4())
````


````
Error: UndefVarError: KenCarp4 not defined
````



````julia
M = MatrixFreeOperator((du,u,p)->laplacian2d(du, u, p, 0), (p,), size=(2*N^2, 2*N^2), opnorm=1000)
````


````
Error: UndefVarError: MatrixFreeOperator not defined
````



````julia
prob7_2 = SplitODEProblem(M, brusselator_reaction, u0, tspan, p)
````


````
Error: UndefVarError: SplitODEProblem not defined
````



````julia
sol7_2 = @time solve(prob7_2, ETDRK4(krylov=true), dt=1)
````


````
Error: UndefVarError: ETDRK4 not defined
````



````julia
prob7_3 = SplitODEProblem(DiffEqArrayOperator(Op), brusselator_reaction, u0, tspan, p)
````


````
Error: UndefVarError: DiffEqArrayOperator not defined
````



````julia
sol7_3 = solve(prob7_3, KenCarp4());
````


````
Error: UndefVarError: KenCarp4 not defined
````





## Part 8: Work-Precision Diagrams for Benchmarking Solver Choices

````julia
using DiffEqDevTools
````


````
Error: ArgumentError: Package DiffEqDevTools not found in current path:
- Run `import Pkg; Pkg.add("DiffEqDevTools")` to install the DiffEqDevTools
 package.
````



````julia
abstols = 0.1 .^ (5:8)
reltols = 0.1 .^ (1:4)
sol = solve(prob3,CVODE_BDF(linear_solver=:GMRES),abstol=1/10^7,reltol=1/10^10)
````


````
Error: UndefVarError: CVODE_BDF not defined
````



````julia
test_sol = TestSolution(sol)
````


````
Error: UndefVarError: TestSolution not defined
````



````julia
probs = [prob2, prob3, prob6]
````


````
Error: UndefVarError: prob2 not defined
````



````julia
setups = [Dict(:alg=>CVODE_BDF(),:prob_choice => 1),
          Dict(:alg=>CVODE_BDF(linear_solver=:GMRES), :prob_choice => 1),
          Dict(:alg=>TRBDF2(), :prob_choice => 1),
          Dict(:alg=>TRBDF2(linsolve=LinSolveGMRES(Pl=precond)), :prob_choice => 3),
          Dict(:alg=>TRBDF2(), :prob_choice => 2)
         ]
````


````
Error: UndefVarError: CVODE_BDF not defined
````



````julia
labels = ["CVODE_BDF (dense)" "CVODE_BDF (GMRES)" "TRBDF2 (dense)" "TRBDF2 (sparse)" "TRBDF2 (GMRES)"]
wp = WorkPrecisionSet(probs,abstols,reltols,setups;appxsol=[test_sol,test_sol,test_sol],save_everystep=false,numruns=3,
  names=labels, print_names=true, seconds=0.5)
````


````
Error: UndefVarError: test_sol not defined
````



````julia
plot(wp)
````


````
Error: UndefVarError: plot not defined
````





## Part 9: GPU-Parallelism for PDEs (E)

## Part 10: Adjoint Sensitivity Analysis for Gradients of PDEs

# Problem 5: Global Parameter Sensitivity and Optimality with GPU and Distributed Ensembles (B)

## Part 1: Implementing the Henon-Heiles System (B)

````julia
function henon(dz,z,p,t)
  p₁, p₂, q₁, q₂ = z[1], z[2], z[3], z[4]
  dp₁ = -q₁*(1 + 2q₂)
  dp₂ = -q₂-(q₁^2 - q₂^2)
  dq₁ = p₁
  dq₂ = p₂

  dz .= [dp₁, dp₂, dq₁, dq₂]
  return nothing
end

u₀ = [0.1, 0.0, 0.0, 0.5]
prob = ODEProblem(henon, u₀, (0., 1000.))
````


````
Error: UndefVarError: ODEProblem not defined
````



````julia
sol = solve(prob, Vern9(), abstol=1e-14, reltol=1e-14)
````


````
Error: UndefVarError: Vern9 not defined
````



````julia

plot(sol, vars=[(3,4,1)], tspan=(0,100))
````


````
Error: UndefVarError: plot not defined
````





## (Optional) Part 2: Alternative Dynamical Implmentations of Henon-Heiles (B)

````julia
function henon(ddz,dz,z,p,t)
  p₁, p₂ = dz[1], dz[2]
  q₁, q₂ = z[1], z[2]
  ddq₁ = -q₁*(1 + 2q₂)
  ddq₂ = -q₂-(q₁^2 - q₂^2)

  ddz .= [ddq₁, ddq₂]
end

p₀ = u₀[1:2]
q₀ = u₀[3:4]
prob2 = SecondOrderODEProblem(henon, p₀, q₀, (0., 1000.))
````


````
Error: UndefVarError: SecondOrderODEProblem not defined
````



````julia
sol = solve(prob2, DPRKN6(), abstol=1e-10, reltol=1e-10)
````


````
Error: UndefVarError: DPRKN6 not defined
````



````julia

plot(sol, vars=[(3,4)], tspan=(0,100))
````


````
Error: UndefVarError: plot not defined
````



````julia

H(p, q, params) = 1/2 * (p[1]^2 + p[2]^2) + 1/2 * (q[1]^2 + q[2]^2 + 2q[1]^2 * q[2] - 2/3*q[2]^3)

prob3 = HamiltonianProblem(H, p₀, q₀, (0., 1000.))
````


````
Error: UndefVarError: HamiltonianProblem not defined
````



````julia
sol = solve(prob3, DPRKN6(), abstol=1e-10, reltol=1e-10)
````


````
Error: UndefVarError: DPRKN6 not defined
````



````julia

plot(sol, vars=[(3,4)], tspan=(0,100))
````


````
Error: UndefVarError: plot not defined
````





## Part 3: Parallelized Ensemble Solving

In order to solve with an ensamble we need some initial conditions.
````julia
function generate_ics(E,n)
  # The hardcoded values bellow can be estimated by looking at the
  # figures in the Henon-Heiles 1964 article
  qrange = range(-0.4, stop = 1.0, length = n)
  prange = range(-0.5, stop = 0.5, length = n)
  z0 = Vector{Vector{typeof(E)}}()
  for q in qrange
    V = H([0,0],[0,q],nothing)
    V ≥ E && continue
    for p in prange
      T = 1/2*p^2
      T + V ≥ E && continue
      z = [√(2(E-V-T)), p, 0, q]
      push!(z0, z)
    end
  end
  return z0
end

z0 = generate_ics(0.125, 10)

function prob_func(prob,i,repeat)
  @. prob.u0 = z0[i]
  prob
end

ensprob = EnsembleProblem(prob, prob_func=prob_func)
````


````
Error: UndefVarError: EnsembleProblem not defined
````



````julia
sim = solve(ensprob, Vern9(), EnsembleThreads(), trajectories=length(z0))
````


````
Error: UndefVarError: Vern9 not defined
````



````julia

plot(sim, vars=(3,4), tspan=(0,10))
````


````
Error: UndefVarError: plot not defined
````





## Part 4: Parallelized GPU Ensemble Solving

In order to use GPU parallelization we must make all inputs
(initial conditions, tspan, etc.) `Float32` and the function
definition should be in the in-place form, avoid bound checking and
return `nothing`.

````julia
using DiffEqGPU
````


````
Error: ArgumentError: Package DiffEqGPU not found in current path:
- Run `import Pkg; Pkg.add("DiffEqGPU")` to install the DiffEqGPU package.
````



````julia

function henon_gpu(dz,z,p,t)
  @inbounds begin
    dz[1] = -z[3]*(1 + 2z[4])
    dz[2] = -z[4]-(z[3]^2 - z[4]^2)
    dz[3] = z[1]
    dz[4] = z[2]
  end
  return nothing
end

z0 = generate_ics(0.125f0, 50)
prob_gpu = ODEProblem(henon_gpu, Float32.(u₀), (0.f0, 1000.f0))
````


````
Error: UndefVarError: ODEProblem not defined
````



````julia
ensprob = EnsembleProblem(prob_gpu, prob_func=prob_func)
````


````
Error: UndefVarError: EnsembleProblem not defined
````



````julia
sim = solve(ensprob, Tsit5(), EnsembleGPUArray(), trajectories=length(z0))
````


````
Error: UndefVarError: Tsit5 not defined
````




# Problem 6: Training Neural Stochastic Differential Equations with GPU acceleration (I)

## Part 1: Constructing and Training a Basic Neural ODE

## Part 2: GPU-accelerating the Neural ODE Process

## Part 3: Defining and Training a Mixed Neural ODE

## Part 4: Constructing a Basic Neural SDE

## Part 5: Optimizing the training behavior with minibatching (E)