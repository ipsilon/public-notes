# Pairing: Linear function evaluation + point addition/doubling in Jacobian coordinates

###### tags: `evmmax`, `pairing`, `ECC`, `PBC`

## Abstract

For bn254 curve Ate pairing main part is the Miller loop. It is described in details by [High-Speed Software Implementation of the Optimal Ate Pairing over Barreto–Naehrig Curves (Algorithm 1)](https://eprint.iacr.org/2010/354.pdf). In this algorithm lines `4,6,8,12,13` are computed using one common method which benefits from common intermediate values which are used in point doubling, points addition and linear function evaluation at an affine point. Unfortunately algorithms implementing these presented in this paper have typos. To find what are proper methods to compute this we had to recalculate them once again.
We used points adding and doubling formulas for Jacobian coordinates described in [Cryptography/Prime Curve/Jacobian Coordinates](https://en.wikibooks.org/wiki/Cryptography/Prime_Curve/Jacobian_Coordinates) and extended these formulas with linear function  evaluation. Linear function is a chord for adding and tangent at point on the curve for doubling. We also have to remember that we calculate everything for twisted curve and make a transition to the curve in the end using untwisting isomorphism.

## Linear function

Let's remind some basis equation.
$f(x,y)=(Y_0-y)-\lambda(x-X_0)$ is a linear function 
where $(x_0,y_0)$ is a point on the line $f(x,y)=0$ and $\lambda$ is a slope of the line.

### Tangent at point $P_0'=(X_0',Y_0')$

We denote the twisted curve as $E'$ so the tangent line to $E'$ in point $P_0'=(X_0',Y_0')$ as $f'_{P_0'}$ 

Then $\lambda' = \frac{3X_0'^2}{2Y_0'}$ (for more details see Section 2.1.2 (Eq. 2.5) of [Pairings for beginners](https://static1.squarespace.com/static/5fdbb09f31d71c1227082339/t/5ff394720493bd28278889c6/1609798774687/PairingsForBeginners.pdf))

Moving to Jacobian coordinates by substituting $X_0'=\frac{X_0'}{Z_0'^2}$ and $Y_0'=\frac{Y_0'}{Z_0'^3}$, we get

$\lambda'=\frac{3\frac{X_0'^2}{Z_0'^4}}{2\frac{Y_0'}{Z_0'^3}}=\frac{3X_0'^2}{2Y_0'Z_0'}$.

Substituting similarly in $f'$ and assuming that $z=1$ coordinate for $x$ and $y$ we get $f$ as follows:

$f'_{P_0'}(x,y)=(\frac{Y_0'}{Z_0'^3} - y)-\lambda'(\frac{X_0'}{Z_0'^2}-x)
=(\frac{Y_0' - yZ_0'^3}{Z_0'^3})-\lambda(\frac{X_0' - xZ_0'^2}{Z_0'^2})$

Now we can substitute $\lambda'$ to the line equation $f'$

$f'_{P_0'}(x,y)=(\frac{Y_0' - yZ_0'^3}{Z_0'^3})-\frac{3X_0'^2}{2Y_0'Z_0'}(\frac{X_0' - xZ_0'^2}{Z_0'^2})
=\frac{2Y_0'(Y_0' - yZ_0'^3)-3X_0'^2(X_0' - xZ_0'^2)}{2Y_0'Z_0'^3}
=\frac{2Y_0'^2-2yY_0'Z_0'^3-3X_0'^3+3xX_0'^2Z_0'^2}{2Y_0'Z_0'^3}$

To untwist $f'$ we need to apply untwisting isomorphism $\psi : E' \to E: (x', y', z') → (\omega^2x', \omega^3y',z')=(x,y,z)$ where based on [Extension towers definition](https://hackmd.io/@jpw/bn254#Field-extension-towers) $\omega^6=\xi$ and $\xi$ is not a quadratic nor cubic residue in $F_{p^2}$. Moreover $\xi=(9+u)$. Having this we can apply $\psi$ substituting $X_0'=\omega^2X_0$, $Y_0'=\omega^3Y_0$ and $Z_0'=Z_0$, hence


$f_{P_0}(x,y)=\frac{2Y_0^2\omega^6-2yY_0\omega^3Z_0^3-3X_0^3\omega^6+3xX_0^2\omega^4Z_0^2}{2Y_0\omega^3Z_0^3}
=\frac{2Y_0^2\omega^3-2yY_0Z_0^3-3X_0^3\omega^3+3xX_0^2\omega Z_0^2}{2Y_0Z_0^3}
=\frac{-2yY_0Z_0^3+3xX_0^2Z_0^2\omega+(2Y_0^2-3X_0^3)\omega^3}{2Y_0Z_0^3}$
Applying equation $\omega^2=v$ according to [Extension towers definition](https://hackmd.io/@jpw/bn254#Field-extension-towers) we get $\omega^3=\omega v$, hence


###### Formula 1
$f_{P_0}(x,y)=\frac{-2yY_0Z_0^3+3xX_0^2Z_0^2\omega+(2Y_0^2-3X_0^3)\omega v}{2Y_0Z_0^3}$


### Chord line intersecting $E'$  in $P0'=(X_0',Y_0')$ and $P1'=(X_1',Y_1')$ 

Most of the equations look the same. Only $\lambda'$ is different. For a line going through two different points $P_0=(X_0',Y_0')$ and $P_1=(X_1',Y_1')$ on the twisted curve we have:

$\lambda'=\frac{Y_1'-Y_0'}{X_1'-X_0'}$ ((for more details see Section 2.1.2 (Eq. 2.4) of [Pairings for beginners](https://static1.squarespace.com/static/5fdbb09f31d71c1227082339/t/5ff394720493bd28278889c6/1609798774687/PairingsForBeginners.pdf)))

for Jacobian coordinates:

$\lambda'=\frac{\frac{Y_1'}{Z_1'^3}-\frac{Y_0'}{Z_0'^3}}{\frac{X_1'}{Z_1'^2}-\frac{X_0'}{Z_0'^2}}
=\frac{\frac{Y_1'Z_0'^3-Y_0'Z_1'^3}{Z_1'^3Z_0'^3}}{\frac{X_1'Z_0'^2-X_0'Z_1'^2}{Z_1'^2Z_0'^2}}
=\frac{Y_1'Z_0'^3-Y_0'Z_1'^3}{Z_1'Z_0'(X_1'Z_0'^2-X_0'Z_1'^2)}$

substituting $\lambda'$ into $f'$ we get:


$f'_{P_0'}(x,y)=(\frac{Y_0' - yZ_0'^3}{Z_0'^3})-\frac{Y_1'Z_0'^3-Y_0'Z_1'^3}{Z_1'Z_0'(X_1'Z_0'^2-X_0'Z_1'^2)}(\frac{X_0' - xZ_0'^2}{Z_0'^2})
=\frac{(Y_0' - yZ_0'^3)Z_1'(X_1'Z_0'^2-X_0'Z_1'^2)-(Y_1'Z_0'^3-Y_0'Z_1'^3)(X_0' - xZ_0'^2)}{Z_1'Z_0'^3(X_1'Z_0'^2-X_0'Z_1'^2)}$

substituting once again $X_0'=\omega^2X_0$, $Y_0'=\omega^3Y_0$ and $Z_0'=Z_0$ to get $f$

###### Formula 2

$f_{P_0}(x,y)=\frac{(Y_0\omega^3 - yZ_0^3)Z_1(X_1\omega^2Z_0^2-X_0\omega^2Z_1^2)-(Y_1\omega^3Z_0^3-Y_0\omega^3Z_1^3)(X_0\omega^2 - xZ_0^2)}{Z_1Z_0^3(X_1\omega^2Z_0^2-X_0\omega^2Z_1^2)}=\\
\frac{(Y_0\omega^3 - yZ_0^3)Z_1(X_1Z_0^2-X_0Z_1^2)-(Y_1\omega Z_0^3-Y_0\omega Z_1^3)(X_0\omega^2 - xZ_0^2)}{Z_1Z_0^3(X_1Z_0^2-X_0Z_1^2)}=\\
\frac{y(X_0Z_0^3Z_1^2 - X_1Z_0^5)+\omega x(Y_1Z_0^5-Y_0Z_1^3Z_0^2)+\omega v(X_1Y_0Z_0^2Z_1-X_0Y_0Z_1^3-X_0Y_1Z_0^3+X_0Y_0Z_1^3)}{Z_1Z_0^3(X_1Z_0^2-X_0Z_1^2)}$

### Algorithm 1
Combined [Formula 1](#Formula-1) and [Point Doubling (4M + 6S or 4M + 4S)](https://en.wikibooks.org/wiki/Cryptography/Prime_Curve/Jacobian_Coordinates#Point_Doubling_(4M_+_6S_or_4M_+_4S)) for Jacobian coordinates

```cpp=
inline constexpr ecc::JacPoint<Fq2> lin_func_and_dbl(const ecc::JacPoint<Fq2>& Q, std::array<Fq2, 3>& t) noexcept
{
    const auto& x = Q.x;
    const auto& y = Q.y;
    const auto& z = Q.z;

    const auto y_squared = y * y;
    const auto x_squared = x * x;
    const auto z_squared = z * z;
    const auto y_4 = y_squared * y_squared;
    const auto _4y_4 = y_4 + y_4 + y_4 + y_4;

    const auto R = y_squared + y_squared;
    const auto A = (x + R);
    const auto S = A * A - x_squared - _4y_4;  // 2xR = (x+R)^2 - x^2 - R^2
    const auto M = x_squared + x_squared + x_squared;

    const auto N = y + z;

    const auto Xp = M * M - (S + S);
    const auto Yp = M * (S - Xp) - (_4y_4 + _4y_4);
    const auto Zp = N * N - y_squared - z_squared;  // 2yz = (y+z)^2 - y^2 - z^2

    t[0] = Zp * z_squared;
    t[1] = M * z_squared;
    t[2] = R - M * x;

    return ecc::JacPoint<Fq2>{Xp, Yp, Zp};
```

### Algorithm 2
Combined [Formula 2](#Formula-2) and [Point Addition (12M + 4S)](https://en.wikibooks.org/wiki/Cryptography/Prime_Curve/Jacobian_Coordinates#Point_Addition_(12M_+_4S)) for Jacobian coordinates. 
Note: This algorithm is simplified for $Z_1=1$ as for pairing we always add $Q$ point in affine coordinates.

```cpp=
[[nodiscard]] inline constexpr ecc::JacPoint<Fq2> lin_func_and_add(const ecc::JacPoint<Fq2>& P0, const ecc::Point<Fq2>& P1, std::array<Fq2, 3>& t) noexcept
{
    const auto& x0 = P0.x;
    const auto& y0 = P0.y;
    const auto& z0 = P0.z;

    const auto& x1 = P1.x;
    const auto& y1 = P1.y;

    const auto z0_squared = z0 * z0;
    const auto z0_cubed = z0 * z0_squared;

    const auto U2 = x1 * z0_squared;
    const auto S2 = y1 * z0_cubed;
    const auto H = U2 - x0;  // x1 * z0^2 - x0 * z1^2
    const auto R = S2 - y0;  // y1 * z0^3 - y0 * z1 ^3

    const auto H_squared = H * H;
    const auto H_cubed = H * H_squared;
    const auto R_squared = R * R;

    const auto V = x0 * H_squared;

    const auto X3 = R_squared - H_cubed - (V + V);
    const auto Y3 = R * (x0 * H_squared - X3) - y0 * H_cubed;
    const auto Z3 = H * z0;

    t[0] = (z0 * z0_squared * x0 - U2 * z0_cubed);
    t[1] = (S2 * z0_squared - y0 * z0_squared);
    t[2] = y0 * U2 - x0 * S2;

    return ecc::JacPoint<Fq2>{X3, Y3, Z3};
}

```

### Notes

Calculated denominators for both cases disappears under final exponentiation transformation. 

$(2Y_0Z_0^3)^\frac{p^{12}-1}{r}=1$
$(Z_1Z_0^3(X_1Z_0^2-X_0Z_1^2))^\frac{p^{12}-1}{r}=1$

because these values are member of proper subgroup of $F_{p^{12}}$. They are members of $F_{p^2}$. So we can omit them in our computation.

In provided algorithms we utilize a formula which change two multiplies into one multiply and 2 subtractions. Which in cheaper.
$(x+y)^2=x^2+2xy+y^2 \implies 2xy=(x+y)^2-x^2-y^2$

## Comparison with original algorithm

After a short analysis of Algorithm 26 from [High-Speed Software Implementation of the Optimal Ate Pairing over Barreto–Naehrig Curves](https://eprint.iacr.org/2010/354.pdf) it looks that at least for this algorithm the formulas for line evaluation are miscalculated in a couple of places. After substitution of intermediates $tmp_i$ we get different equations:

- $tmp0=2(Z_TZ_Q^2)$ but it should be $tmp0=Z_TZ_Q^2$. This is not a problem because $2$ disappears in final exponentiation.
- $tmp1=-2(3X_Q^2Z_Q^2)$ but it should be $tmp1=3X_Q^2Z_Q^2$. This is a problem because after final exponentiation we get invalid sign for this value.
- $tmp6=-3X_Q^2+3X_Q^3+9X_Q^4-4Y_Q^2$ which is not even close to what should be here: $2Y_Q^2-3X_Q^3$.

Similar issues can be found in Algorithm 27 (Point addition and line evaluation) of this paper, but we are not going into details.

## Conclusions

It was a fairly simple derivation but we believe it's needed to avoid doubts and misunderstandings by the implementers. It's possible to introduce a couple more optimizations and get better results. Additionally, one may consider to pre-calculate the lines in a separated loop.

These algorithms are implemented and used by [evmone](https://github.com/ethereum/evmone) being developed by [Ipsilon](https://notes.ethereum.org/@ipsilon/about).

