---
layout: post
title: "11D Supergravity"
description: "A small, info-filled, notelet for solving 11D SUGRA in M-Theory"
date: 2025-04-03
category: [study notes, physics]
tags: [Physics, M-Theory, Notes, Walkthrough]
comments: false
math: true
---

# What is 11D Supergravity?
In **M-theory**, the **low-energy effective action** is given by **11-dimensional supergravity (11D SUGRA)**, which is the unique maximal supergravity theory in 11 dimensions. This action describes the dynamics of gravity and fields in **11-dimensional spacetime** and serves as the classical limit of M-theory.

# **Physical Meaning of the Action Integral**

- The **first integral** represents **Einstein-Hilbert gravity** with a **4-form flux**.
    
- The **second integral** is a **topological term** that plays a crucial role in M-theory dynamics.
    
- This action leads to the **field equations of 11D supergravity**, which govern M-theory’s low-energy behavior.

## **Key Solutions from This Action**

1 **Freund-Rubin Compactification**:
    
- Splitting **11D spacetime** into $AdS_4 \times S^7$ or $AdS_7 \times S^4$.
        
- Gives rise to **4D and 7D supergravity theories**.
        
2 **M2 and M5 Brane Solutions**:
    
- The action supports solutions that describe fundamental objects in **M-theory**:
    
- **M2-branes (membranes)**
    
- **M5-branes (5-dimensional extended objects)**
            
3 **Black Hole and Black Brane Solutions**:
            
  Solutions describe higher-dimensional black holes and their thermodynamics.

            
## **Relation to String Theory**

- **M-theory** in 11D reduces to **10D Type IIA supergravity** via **dimensional reduction**.
    
- The 3-form field $C_3$ couples to **M2-branes**, analogous to how gauge fields couple to particles.
    
- Compactifying the extra dimension on a small circle gives the **Type IIA string theory**, where the **D0-brane** corresponds to momentum in the extra dimension.


# Solving the Action Integrals

### Step 1: **Identify the Action Components**

The given action $S$ consists of:

- **Einstein-Hilbert term**: $R$, representing gravity.
    
- **Gauge field strength terms**: $F_4^2$ and $G_2^2$.
    
- **Dilaton-dependent term**: $e^{-4\phi} \mathcal{L}_{\text{mod}}$.
    
- **Higher-order corrections**: $\mathcal{O}(F_6)$ and $\mathcal{O}(\nabla^6)$.
    

### Step 2: **Derive the Equations of Motion**

To obtain field equations:

1 Vary the action with respect to the metric $g_{11}$ to get Einstein's equations in 11D supergravity.
    
2 Vary with respect to the gauge fields $F_4$ and $G_2$ to obtain their field equations.
    
3 If present, consider variations with respect to the scalar field $ϕ$ to obtain its equation of motion.
    

### Step 3: **Solve the Gravitational Equations**E
- Use the Einstein equation derived from $\delta S / \delta g_{\mu\nu} = 0$.
    
- Depending on symmetries, solve for the metric $g_{\mu\nu}$ (e.g., using ansatz solutions like Freund-Rubin compactifications).
    

### Step 4: **Solve the Gauge Field Equations**
\- Solve the Maxwell-type equations obtained from varying $S$ with respect to $A_\mu$, the gauge field potential.


### Step 5: **Analyze the Scalar Field Equation**
\- If a dilaton field $ϕ$ is present, solve its equation by considering energy-momentum contributions.


### Step 6: **Consider Higher-Order Corrections**
\- If $\mathcal{O}(F_6)$ and $\mathcal{O}(\nabla^6)$ terms are small, solve perturbatively.
    

### Step 7: **Check for Special Solutions**
\- Look for known solutions like black hole metrics, flux compactifications, or plane waves.


# Solving with Derived Field Equations

### **Step 1: Consider a Simplified Action**

Ignoring higher-order corrections $ \mathcal{O}(F_6)$ and $\mathcal{O}(\nabla^6) $, we take a simplified version of the action: $S=\frac{1}{2\kappa_{11}^2} \int_{\mathcal{M}_{11}} d^{11}x \sqrt{-g_{11}} \left( R - \frac{1}{2} F_4^2 \right)$ 

where:

- $R$ is the Ricci scalar (describes gravity).
    
- $F_4 = dA_3$ is the 4-form field strength from the 3-form gauge field $A_3$.
    


### **Step 2: Derive Einstein’s Equations**

Varying the action with respect to the metric gμνg_{\mu\nu}, we get Einstein’s equation:

$$R = \frac{1}{2} \left( F_{\mu\alpha\beta\gamma} F_{\nu}^{\alpha\beta\gamma} - \frac{1}{8} g_{\mu\nu} F_4^2 \right)$$

This tells us how the metric is affected by the presence of the 4-form field $F_4$.


### **Step 3: Solve for a Simple Metric – Freund-Rubin Ansatz**

One of the famous solutions in 11D supergravity is the **Freund-Rubin compactification**, where we assume the spacetime is split into:

$\mathcal{M}_{11} = AdS_4 \times S^7$

meaning a 4D Anti-de Sitter space (AdS) and a 7-sphere $S^7$.

We make the ansatz:

$F_4 = \lambda \text{Vol}_{AdS_4}$

where λ\lambda is a constant and $\text{Vol}_{AdS_4}$ is the volume form of $AdS_4$.

Solving the Einstein equations under this assumption gives:

$R = - \frac{3}{L^2} g_{\mu\nu} \quad \text{for AdS}_4, \quad R_{ab} = \frac{6}{L^2} g_{ab} \quad \text{for } S^7$

where $L$ is the radius of curvature related to λ\lambda.

This is the **$AdS4_4 × S7^7$** solution of 11D supergravity, which is important in string/M-theory.


### **Step 4: Interpret the Solution**

- This describes a 4-dimensional curved space (AdS) with a 7-sphere.
    
- It’s a background solution for **M-theory** that leads to many physical insights, such as the **AdS/CFT correspondence**.
    
# Simplifying the Given Action

To simplify the given action into **Step 1**, we focus on the most essential terms and discard the complex corrections. Here’s how:

### **Step 1: Identify the Key Terms**

The given action is:

$$S = \frac{1}{2\kappa_{11}^2} \int_{\mathcal{M}_{11}} d^{11}x \sqrt{-g_{11}} \left( R - \frac{1}{2} F_4^2 - \frac{1}{4} G_2^2 + e^{-4\phi} \mathcal{L}_{\text{mod}} + \mathcal{O}(F_6) + \mathcal{O}(\nabla^6) \right)$$

where:

- $R$ is the Ricci scalar (describes curvature/gravity).
    
- $F_4^2$ and $G_2^2 are gauge field contributions.
    
- $e^{-4\phi} \mathcal{L}_{\text{mod}}$ represents additional matter fields.
    
- $\mathcal{O}(F_6)$ and $\mathcal{O}(\nabla^6)$ are higher-order corrections.
    

### **Step 2: Simplify to Essential Terms**

If we want to solve the Einstein equations in 11D supergravity, we focus only on the **gravitational and gauge field terms**, ignoring matter and higher-order corrections:
$$S \approx \frac{1}{2\kappa_{11}^2} \int_{\mathcal{M}_{11}} d^{11}x \sqrt{-g_{11}} \left( R - \frac{1}{2} F_4^2 \right)$$


This is the **simplified supergravity action** in 11D.

### **Why Simplify This Way?**

- The **Ricci scalar RR** governs Einstein’s equations.
    
- The **$F_4^2$ term** introduces gauge field dynamics.
    
- The higher-order corrections are often negligible in leading-order approximations.
    

Now, we can **return back to Step 2**, which involves deriving the Einstein field equations.
