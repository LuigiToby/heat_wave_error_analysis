---
created: 2025-11-23T07:32:47
art: "![[|100]]"
source: "[[]]"
link: "[link]()"
status: active
---
#atomic #academy/202520/MATE2301 
___
# FTCS y Leapfrog
---
## 1. ¿Qué es el método FTCS?

**FTCS** significa _Forward Time – Centered Space_  
y es uno de los métodos numéricos explícitos más simples para resolver ecuaciones en derivadas parciales.

Se usa para aproximar soluciones de la ecuación de calor:

$$  
\alpha u_{xx} = u_t 
$$

La idea principal es reemplazar las derivadas por diferencias finitas:

### Derivada en el tiempo (Forward)

$$
u_t \approx \frac{u^{n+1}_i - u^{n}_i}{\Delta t}  
$$

### Derivada segunda en espacio (Centered)
$$ 
u_{xx} \approx \frac{u^{n}_{i+1} - 2u^{n}_i + u^{n}_{i-1}}{\Delta x^2}  
$$

Sustituyendo ambas en la ecuación se obtiene:


$$ u^{n+1}_i = u^n_i + r\left(u^n_{i+1} - 2u^n_i + u^n_{i-1}\right)  $$

donde:

$$ 
r = \frac{\alpha \Delta t}{\Delta x^2}  
$$

Este esquema permite avanzar la solución en el tiempo con cálculos muy simples.

## 2. ¿Por qué funciona tan bien para la ecuación de **calor**?

La ecuación de calor es **difusiva**: suaviza rápidamente la solución.  
Ese suavizamiento natural “perdona” errores numéricos.

FTCS es estable **solo si** se cumple la condición de estabilidad [CFL](https://en.wikipedia.org/wiki/Courant%E2%80%93Friedrichs%E2%80%93Lewy_condition):

$$ 
r = \frac{\alpha \Delta t}{\Delta x^2} \le 0.5  
$$
![[Pasted image 20251123080816.png]]
Fuente: https://www.youtube.com/watch?v=WBWY46ynRk0

Si se respeta esta condición:

- la solución numérica no explota
    
- las oscilaciones espurias se amortiguan
    
- el error disminuye al refinar la malla
    

Por eso FTCS es una opción razonable para la ecuación de calor.

## 3. ¿Por qué FTCS NO funciona para la ecuación de **onda**?

La ecuación de onda:

$$
a u_{xx}=u_{tt}  
$$

no es difusiva, es **hiperbólica**.  
Transporta información mediante oscilaciones que NO se amortiguan.

Cuando intentas aplicar FTCS, necesitas aproximar $u_{tt}$, lo que lleva naturalmente a un esquema del tipo:

$$
u^{n+1}_i = 2u^{n}_i - u^{n-1}_i + c^2( u^n_{i+1} - 2u^n_i + u^n_{i-1})  
$$

Este **NO es FTCS**, sino el método **Leapfrog** o “salto de rana”.  
FTCS simplemente no puede aproximar de manera estable la segunda derivada en el tiempo.

## 4. ¿Qué pasa si usamos FTCS en la ecuación de onda?
- el error no se disipa
- cualquier pequeña oscilación crece
- la solución explota numéricamente
- aparecen ondas no-físicas que amplifican ruido en cada paso de tiempo
- no existe un valor de $\Delta t$ que garantice estabilidad absoluta
Mientras el calor “mata” las altas frecuencias, la onda las _amplifica_.  
Por eso FTCS es **compatible con la física difusiva**, pero **incompatible con la física hiperbólica**.
## 5. ¿Qué método si funciona para la ecuación de onda?

El esquema estándar es **Leapfrog (centrado en tiempo)**:
$$  
u^{n+1}_i =  
2u^n_i - u^{n-1}_i

- \left(\frac{a\Delta t}{\Delta x}\right)^2  
    \left(u^n_{i+1} - 2u^n_i + u^n_{i-1}\right)  
$$
Y aquí la condición de estabilidad es:
$$  
\frac{a\Delta t}{\Delta x} \le 1  
$$
Este método respeta la física de propagación sin amortiguar ni soplar las oscilaciones.
![[Euler_leapfrog_comparison.gif|center]]
![[Wave-2D-Gaussian-Mixed.gif|center]]
Fuente: https://gist.github.com/TheBoyRoy05/ee488fea8a51204fa0da654f26b214b4
## Resumen

| Propiedad             | Ecuación de Calor         | Ecuación de Onda                        |
| --------------------- | ------------------------- | --------------------------------------- |
| Tipo                  | Difusiva                  | Hiperbólica                             |
| FTCS                  | Funciona, estable con CFL | Inestable para cualquier paso de tiempo |
| Comportamiento físico | Suaviza                   | Oscila                                  |
| Método recomendado    | FTCS, Crank–Nicolson      | Leapfrog, Lax–Wendroff                  |
## Takeaway
- **FTCS funciona porque imita la física difusiva**: el método suaviza errores tal como la ecuación suaviza la temperatura.
- **FTCS falla en la onda porque viola su física**: introduce amortiguamiento artificial o inestabilidad explosiva.
- El método debe coincidir con la naturaleza de la ecuación.
# Sources
1.  https://en.wikipedia.org/wiki/FTCS_scheme
2. https://www.youtube.com/watch?v=B4rziSNUpAA&t=854s
3. https://en.wikipedia.org/wiki/Leapfrog_integration
___