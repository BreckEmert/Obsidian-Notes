Database of latex symbols:
https://www.cmor-faculty.rice.edu/~heinken/latex/symbols.pdf

To left align text, put `& ` at the point you want to begin left-alignment:
```
$$
\begin{align}
& H_0: \text{survival curves across 2 or more groups are equivalent} \\  
& H_A: \text{survival curves across 2 or more groups are not equivalent}
\end{align}
$$
```
$$
\begin{align}
& H_0: \text{survival curves across 2 or more groups are equivalent} \\  
& H_A: \text{survival curves across 2 or more groups are not equivalent}
\end{align}
$$

```
$$
\begin{align}
h(t|X_1 = 0) & = h_0(t)\exp(b_1 * 0) \\
             & = h_0(t)\exp(0) \\
			 & = h_0(t)
\end{align}
$$
```
$$
\begin{align}
h(t|X_1 = 0) & = h_0(t)\exp(b_1 * 0) \\
             & = h_0(t)\exp(0) \\
			 & = h_0(t)
\end{align}
$$

- To make ellipses, use `\dots`.  `\cdots` explicitly does vertically-aligned dots, though `\dots` seems to detect the proper placement.
- To make a 'given' symbol with spaces around it, use `\mid`: $\Pr(y = k \mid x = x_0)$
- To insert a small arbitrary space, use `\`, `\:`, or `\;`.

`$\in$` $=$ $\in$ 