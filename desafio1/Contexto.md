# Análisis de Errores Numéricos en Amortización de Créditos
## Integrantes:
- Misael Del Carpio Gamboa, 10017754
- Juan De Dios Cruz Copa, 13789037
- Maximiliano Gomez Mallo, 1480221

## 1. Contexto: ¿Qué es un crédito amortizable?

Cuando pides un préstamo, el banco calcula una **cuota mensual fija** que cubre intereses y una parte del capital. Cada mes pagas lo mismo, pero la proporción entre intereses y capital cambia.

Parámetros del problema:

| Símbolo | Significado |
|---------|-------------|
| $P$     | Capital inicial (monto del préstamo) |
| $i$     | Tasa de interés mensual |
| $n$     | Número total de pagos (meses) |
| $C$     | Cuota mensual fija |
| $S_k$   | Saldo teórico al final del mes $k$ |
| $S_k^*$ | Saldo real (con redondeos) al final del mes $k$ |

---

## 2. Fórmulas Teóricas

### Cuota mensual fija

$$C = P \cdot \frac{i(1+i)^n}{(1+i)^n - 1}$$

### Saldo teórico al final del mes $k$

$$S_k = P \cdot \frac{(1+i)^n - (1+i)^k}{(1+i)^n - 1}$$

Esta es la **función teórica**: el saldo exacto según el modelo matemático, sin ningún redondeo.

---

## 3. El Problema: El Redondeo

En la práctica, los bancos trabajan con **centavos** (2 decimales). Cada mes se redondea:
- La cuota $C$ al centavo más cercano
- Los intereses del mes al centavo más cercano
- El abono a capital al centavo más cercano

Esto introduce un **pequeño error en cada período** que se va **acumulando** a lo largo del crédito.

### Error Absoluto en el mes $k$

$$E_a^{(k)} = |S_k - S_k^*|$$

### Error Relativo en el mes $k$

$$E_r^{(k)} = \frac{|S_k - S_k^*|}{S_k} \times 100\%$$

---

## 4. Ejemplo Numérico Completo

### Parámetros

| Parámetro | Valor |
|-----------|-------|
| Capital $P$ | \$10,000.00 |
| Tasa mensual $i$ | 1.5% (0.015) |
| Plazo $n$ | 12 meses |

### Paso 1: Calcular la cuota exacta

$$C = 10{,}000 \cdot \frac{0.015 \cdot (1.015)^{12}}{(1.015)^{12} - 1}$$

$$C = 10{,}000 \cdot \frac{0.015 \cdot 1.19562}{0.19562} = 10{,}000 \cdot \frac{0.017934}{0.19562}$$

$$C = 10{,}000 \cdot 0.091680 = \$916.80 \text{ (exacto: } \$916.7995...)$$

- **Cuota exacta:** \$916.7995...
- **Cuota redondeada:** \$916.80

### Paso 2: Tabla de amortización (teórica vs. real)

Se muestra mes a mes la diferencia entre el saldo teórico (fórmula exacta) y el saldo real (con redondeos acumulados).

| Mes $k$ | Saldo Teórico $S_k$ | Interés Real | Abono Capital Real | Saldo Real $S_k^*$ | $E_a$ (USD) | $E_r$ (%) |
|---------|----------------------|--------------|---------------------|--------------------|-------------|------------|
| 0       | \$10,000.00          | —            | —                   | \$10,000.00        | \$0.00      | 0.000%     |
| 1       | \$9,233.20           | \$150.00     | \$766.80            | \$9,233.20         | \$0.00      | 0.000%     |
| 2       | \$8,454.90           | \$138.50     | \$778.30            | \$8,454.90         | \$0.00      | 0.000%     |
| 3       | \$7,664.92           | \$126.82     | \$789.98            | \$7,664.93         | \$0.01      | 0.0001%    |
| 4       | \$6,863.10           | \$114.97     | \$801.83            | \$6,863.12         | \$0.02      | 0.0003%    |
| 5       | \$6,049.25           | \$102.95     | \$813.85            | \$6,049.28         | \$0.03      | 0.0005%    |
| 6       | \$5,223.14           | \$90.74      | \$826.06            | \$5,223.18         | \$0.04      | 0.0008%    |
| 7       | \$4,384.59           | \$78.35      | \$838.45            | \$4,384.64         | \$0.05      | 0.0011%    |
| 8       | \$3,533.36           | \$65.77      | \$851.03            | \$3,533.42         | \$0.06      | 0.0017%    |
| 9       | \$2,669.26           | \$53.00      | \$863.80            | \$2,669.33         | \$0.07      | 0.0026%    |
| 10      | \$1,792.05           | \$40.04      | \$876.76            | \$1,792.13         | \$0.08      | 0.0045%    |
| 11      | \$901.49             | \$26.88      | \$889.92            | \$901.58           | \$0.09      | 0.0100%    |
| 12      | \$0.00               | \$13.52      | \$901.49            | **\$0.09**         | \$0.09      | —          |

> **Observación clave:** Al final del mes 12, el saldo real no llega exactamente a \$0.00 sino que queda un residuo de \$0.09. Ese es el **error acumulado** de todos los redondeos.

---

## 5. Análisis del Error Acumulado

### ¿Cómo crece el error?

El error absoluto **crece mes a mes** porque cada redondeo introduce una pequeña discrepancia que se suma a la anterior. Es un ejemplo clásico de **propagación de errores de redondeo**.

```
Error acumulado
$0.09 |                                        ●
$0.08 |                                   ●
$0.07 |                              ●
$0.06 |                         ●
$0.05 |                    ●
$0.04 |               ●
$0.03 |          ●
$0.02 |     ●
$0.01 |  ●
$0.00 |●
       1  2  3  4  5  6  7  8  9  10 11 12   Mes
```

### Error relativo: disminuye al principio, crece al final

El error relativo es pequeño al inicio (el saldo es grande) y se dispara al final (el saldo se acerca a cero), lo que es matemáticamente esperado:

$$E_r^{(12)} = \frac{0.09}{0.00^+} \to \text{indeterminado}$$

Por eso en el mes 12 no se calcula: el denominador es prácticamente cero.

---

## 6. ¿Qué hace el banco con ese residuo?

Existen tres estrategias comunes, cada una con su propio impacto en el error:

**Estrategia 1: Último pago ajustado**
El banco cobra en el último mes: $C_{12} = C + \text{residuo} = \$916.80 + \$0.09 = \$916.89$

$E_a$ final = \$0.00 ✓

**Estrategia 2: Absorber el residuo**
El banco asume la diferencia. El cliente paga siempre \$916.80.

$E_a$ final = \$0.09 (lo pierde el banco)

**Estrategia 3: Redondear la cuota hacia arriba desde el inicio**
Cobrar \$916.81 en lugar de \$916.80. Esto genera un error en sentido contrario que compensa los redondeos.

---

## 7. Escalando el problema: ¿Qué pasa con créditos grandes?

Si en lugar de \$10,000 el crédito fuera de \$1,000,000 (un crédito hipotecario a 240 meses), el error acumulado puede ser significativo:

| Capital | Plazo | Error acumulado estimado |
|---------|-------|--------------------------|
| \$10,000    | 12 meses  | ~\$0.09    |
| \$100,000   | 60 meses  | ~\$1.20    |
| \$1,000,000 | 240 meses | ~\$15.00   |

Para un banco con **100,000 créditos hipotecarios** activos, esos \$15 por crédito representan **\$1,500,000** de descalce si no se gestiona correctamente.

---

## 8. Proceso Iterativo: Encontrar la tasa dado un pago observado

Un caso relacionado: el cliente observa que su banco le cobra \$920.00 al mes (no \$916.80). ¿Qué tasa le está cobrando realmente?

Necesitamos resolver para $i$:

$$920.00 = 10{,}000 \cdot \frac{i(1+i)^{12}}{(1+i)^{12} - 1}$$

No tiene solución analítica. Usamos **bisección**:

| Iteración | $i_{inf}$ | $i_{sup}$ | $i_{medio}$ | $C(i_{medio})$ | $E_a$ (USD) |
|-----------|-----------|-----------|-------------|-----------------|-------------|
| 1         | 0.015     | 0.020     | 0.01750     | \$935.07        | \$15.07     |
| 2         | 0.015     | 0.01750   | 0.01625     | \$927.40        | \$7.40      |
| 3         | 0.015     | 0.01625   | 0.01563     | \$920.15        | \$0.15      |
| 4         | 0.015     | 0.01563   | 0.01531     | \$916.96        | \$3.04      |
| 5         | 0.01531   | 0.01563   | 0.01547     | \$918.55        | \$1.55      |
| 6         | 0.01547   | 0.01563   | 0.01555     | \$919.35        | \$0.65      |
| 7         | 0.01555   | 0.01563   | 0.01559     | \$919.75        | \$0.25      |
| 8         | 0.01559   | 0.01563   | 0.01561     | \$919.95        | \$0.05      |
| 9         | 0.01561   | 0.01563   | 0.01562     | \$920.05        | \$0.05      |
| 10        | 0.01561   | 0.01562   | 0.015615    | \$920.00        | \$0.00      |

> **Tasa real encontrada:** $i^* \approx 1.5615\%$ mensual (18.74% anual), cuando el banco decía cobrar 18% anual (1.5% mensual). El error relativo en la tasa es:

$$E_r = \frac{|0.015615 - 0.015|}{0.015} \times 100 = 4.1\%$$

Un error aparentemente pequeño en la tasa genera \$3.20 más por mes, o **\$38.40 al año** que el cliente paga de más sin saberlo.

---

## 9. Resumen y Conclusiones

| Concepto | Rol en este problema |
|----------|---------------------|
| **Función teórica** | Fórmula de saldo $S_k$ y cuota $C$ |
| **Valor observado** | Saldo real $S_k^*$ calculado con redondeos |
| **Error absoluto** | Diferencia en pesos entre saldo teórico y real |
| **Error relativo** | Qué proporción representa ese error sobre el saldo |
| **Propagación** | El error crece mes a mes por acumulación de redondeos |
| **Proceso iterativo** | Bisección para encontrar la tasa real dado un pago |
| **Criterio de paro** | $E_a < \$0.01$ o $E_r < 0.001\%$ |

### Lección principal

> Un redondeo de centavos parece insignificante, pero **se propaga y acumula**. En créditos grandes o portafolios masivos, ignorar el análisis de errores puede significar millones en descalces contables o en cobros incorrectos a clientes.