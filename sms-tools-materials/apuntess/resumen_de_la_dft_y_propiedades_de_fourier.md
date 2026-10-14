# Guía Intuitiva y Resumen: La Transformada Discreta de Fourier (DFT) y sus Propiedades

---

## Parte 1: La Transformada Discreta de Fourier (Tema 2)

> **La gran idea:** En el dominio del tiempo vemos cómo cambia la presión del aire instante a instante (una onda compleja), pero nuestro oído escucha *frecuencias* (notas musicales). La DFT es el "prisma" matemático que descompone una onda de sonido compleja en los tonos puros (sinusoides) que la forman[cite: 3].

### 1. ¿Cómo funciona la DFT? (Funciones Base y Producto Escalar)

Antes de ver la fórmula general, hay que entender el mecanismo interno. ¿Cómo sabe la DFT si una frecuencia concreta está presente en nuestra señal de audio? **Comparándola.**

* **Las "plantillas" de comparación (Funciones Base):**
  La DFT genera $N$ señales de prueba perfectas, llamadas funciones base, que son exponenciales complejas (combinaciones de coseno y seno gracias a la fórmula de Euler)[cite: 6]:
  $$s_k^*[n] = e^{-j2\pi kn/N} = \cos(2\pi kn/N) - j\sin(2\pi kn/N)$$
  *Intuición:* ¿Por qué usamos exponenciales complejas (coseno + seno) en lugar de solo un coseno? Porque la señal de entrada podría estar desplazada en el tiempo. Si solo usáramos un coseno y la señal de entrada fuera un seno (desfasada 90°), al multiplicarlas se cancelarían y creeríamos que esa frecuencia no existe. Usando coseno (parte real) y seno (parte imaginaria) a la vez, "atrapamos" la frecuencia sin importar en qué instante de tiempo empiece.

* **El detector de similitud (Producto Escalar):**
  En álgebra, el producto escalar entre dos vectores mide cuánto se parecen. En la DFT hacemos exactamente eso: multiplicamos nuestra señal $x[n]$ muestra a muestra por la plantilla de la frecuencia $k$ y sumamos todo[cite: 7, 16]:
  $$\langle x, s_k \rangle = \sum_{n=0}^{N-1} x[n] s_k^*[n]$$
  * *Si la frecuencia $k$ está en el audio:* Las subidas y bajadas coinciden, los productos son positivos y la suma da un número grande[cite: 8, 10].
  * *Si la frecuencia $k$ NO está en el audio:* Las ondas van descompasadas, la mitad de las multiplicaciones son positivas y la otra mitad negativas, por lo que al sumar todo se cancela y da $0$[cite: 8, 10].

---

### 2. La Ecuación de la DFT y la "Rejilla" de Frecuencias

Uniendo el producto escalar y las funciones base, obtenemos la **ecuación de análisis de la DFT**[cite: 4, 7]:

$$X[k] = \sum_{n=0}^{N-1} x[n]e^{-j2\pi kn/N} \quad \text{para } k = 0, \dots, N-1$$

* **¿Qué significa cada letra?**[cite: 4]
  * $x[n]$: Tu señal de audio en el tiempo ($n$ es el número de muestra, de $0$ a $N-1$)[cite: 3, 4].
  * $X[k]$: El resultado del análisis para el "casillero" o *bin* de frecuencia número $k$[cite: 3, 4].
  * $N$: El número total de muestras que tomamos (y también el número de frecuencias que analizamos)[cite: 3, 5].

* **La limitación de la rejilla (*Bins* de frecuencia):**
  La DFT no puede analizar infinitas frecuencias continuas; solo pone a prueba $N$ frecuencias fijas (como las teclas de un piano, no como un violín)[cite: 5]. La frecuencia real en Hercios ($f_k$) a la que corresponde cada casillero $k$ depende de la frecuencia de muestreo ($f_s$)[cite: 4, 5]:
  $$\omega_k = \frac{2\pi k}{N} \text{ (radianes)} \quad \longrightarrow \quad f_k = k \frac{f_s}{N} \text{ (Hz)}$$
  La separación entre un casillero y el siguiente (resolución de la rejilla) es[cite: 5]:
  $$\Delta f = \frac{f_s}{N}$$
  *Intuición:* Si quieres distinguir dos notas muy cercanas, necesitas una rejilla más fina ($\Delta f$ más pequeño), lo que te obliga a tomar más muestras (aumentar $N$)[cite: 5].

---

### 3. ¿Cómo leemos el resultado? (Magnitud y Fase)

Como hemos multiplicado por números complejos, cada resultado $X[k]$ es un número complejo[cite: 3, 9]. En lugar de mirarlo como "parte real y parte imaginaria", lo pasamos a forma polar[cite: 9]:

$$X[k] = \vert{}X[k]\vert{}e^{j\angle X[k]}$$

* **Magnitud ($\vert{}X[k]\vert{}$):** Es la altura del pico en la gráfica. Nos dice **"cuánto"** hay de esa frecuencia (el volumen o fuerza de ese armónico)[cite: 3, 9].
* **Fase ($\angle X[k]$):** Es el ángulo. Nos dice **"dónde"** empieza la onda en el tiempo (su alineación temporal)[cite: 3, 9].
* *Nota práctica:* Para analizar qué nota suena nos fijamos casi siempre en la magnitud, pero si queremos reconstruir el sonido, la fase es indispensable (sin ella, las ondas se sumarían desordenadas en el tiempo)[cite: 9, 17].

---

### 4. ¿Qué pasa cuando analizamos sinusoides reales y complejas?

> **Conector:** Ya sabemos que la DFT tiene una rejilla fija de frecuencias ($k$). Pero, ¿qué pasa si el sonido real tiene una frecuencia que cae justo en medio de dos casilleros de nuestra rejilla?[cite: 5, 11]

1. **Sinusoide compleja que cae EXACTAMENTE en un *bin* ($k_0$):**
   $$x_1[n] = e^{j2\pi k_0 n/N} \implies X_1[k] = \begin{cases} N & \text{si } k = k_0 \\ 0 & \text{si } k \neq k_0 \end{cases}$$
   *Intuición:* Encaje perfecto. Toda la energía se concentra en una única aguja de altura $N$ en el casillero $k_0$. El resto vale cero[cite: 5, 10, 16].

2. **Sinusoide fuera de *bin* (*Off-bin* / Fuga espectral o *Leakage*):**
   Si metemos una frecuencia que equivale, por ejemplo, al *bin* $k_0 = 7.5$ (que no existe porque $k$ solo toma números enteros)[cite: 11]:
   $$x_2[n] = e^{j(2\pi f_0 n + \phi)} \implies X_2[k] = e^{j\phi} \frac{1 - e^{-j2\pi(k/N - f_0)N}}{1 - e^{-j2\pi(k/N - f_0)}}$$
   *Intuición:* Como el *bin* $7.5$ no existe, la energía no desaparece: se "derrama" o reparte entre los casilleros vecinos (el 7 y el 8 tendrán picos altos, el 6 y el 9 algo menos, etc.)[cite: 5, 11]. En vez de una aguja limpia, vemos una montaña ensanchada[cite: 11]. A esto se le llama **fuga espectral (*spectral leakage*)**[cite: 16, 41].

3. **Sinusoides del mundo real (Coseno real):**
   En el mundo físico el audio no tiene números imaginarios, son ondas reales[cite: 12, 15]. Por las identidades de Euler, un coseno real se compone de la suma de dos exponenciales complejas (una girando hacia adelante con frecuencia $+k_0$ y otra hacia atrás con $-k_0$)[cite: 6, 12]:
   $$x_3[n] = A_0 \cos(2\pi k_0 n / N) = \frac{A_0}{2}e^{j2\pi k_0 n/N} + \frac{A_0}{2}e^{-j2\pi k_0 n/N}$$
   Su DFT da como resultado[cite: 12]:
   $$X_3[k] = \begin{cases} N\frac{A_0}{2} & \text{para } k = k_0, -k_0 \\ 0 & \text{para el resto de } k \end{cases}$$
   *Intuición:* Por culpa de que las señales de audio son reales, el espectro siempre aparece duplicado en espejo: la mitad de la amplitud va a la frecuencia positiva ($+k_0$) y la otra mitad a la frecuencia negativa ($-k_0$)[cite: 12, 16].

---

### 5. El camino de vuelta: La Transformada Inversa (IDFT)

> **Conector:** Si la DFT desmonta el puzzle en piezas de frecuencias, la IDFT toma todas esas piezas y las vuelve a sumar para reconstruir el sonido original muestra a muestra[cite: 2, 13, 16].

$$x[n] = \frac{1}{N} \sum_{k=0}^{N-1} X[k]e^{j2\pi kn/N} \quad \text{para } n = 0, 1, \dots, N-1$$

* **¿Cómo leer la fórmula?** Toma cada onda pura $e^{j2\pi kn/N}$, multiplícala por su peso y desfase $X[k]$, súmalas todas juntas y divide entre $N$ (para compensar que en la ida los picos se multiplicaban por $N$)[cite: 10, 13, 19].
* **Simetría conjugada:** Para que al sumar todas estas ondas complejas las partes imaginarias se cancelen entre sí y nos devuelvan un audio real, el espectro de cualquier señal real cumple siempre que el lado negativo es el reflejo conjugado del positivo (misma magnitud, fase con signo opuesto)[cite: 14, 27]:
  $$X[k] = \vert{}X[k]\vert{}e^{j\angle X[k]} \quad \text{y} \quad X[-k] = \vert{}X[k]\vert{}e^{-j\angle X[k]}$$

---

## Parte 2: Propiedades de la Transformada de Fourier (Tema 3)

> **Conector:** Ahora que conocemos la herramienta básica (DFT/IDFT), ¿cómo se comporta el espectro cuando manipulamos el sonido en el tiempo (sumar instrumentos, retrasar una pista, cortar un fragmento)? Conocer estas reglas nos ahorra cálculos y explica fenómenos clave del procesado de audio[cite: 27, 42].

### 1. Propiedades Matemáticas y su Significado Físico

* **Linealidad (El principio del mezclador de audio):**
  $$a x_1[n] + b x_2[n] \Longleftrightarrow a X_1[k] + b X_2[k]$$
  *Intuición:* Si grabas una trompeta y un piano juntos, el espectro de la mezcla es simplemente la suma de los espectros de cada uno por separado[cite: 23, 24]. Las frecuencias no interfieren creando frecuencias nuevas; esto permite los modelos aditivos de síntesis de sonido[cite: 23].

* **Desplazamiento en el tiempo (*Time Shift*):**
  $$x[n - n_0] \Longleftrightarrow e^{-j2\pi k n_0 / N} X[k]$$
  *Intuición:* Si tocas la misma nota 2 segundos más tarde ($n - n_0$), **la magnitud $\vert{}X[k]\vert{}$ no cambia en absoluto** (sigue habiendo exactamente las mismas frecuencias con la misma fuerza)[cite: 25, 26]. Lo único que cambia es que todas las ondas empiezan más tarde, lo que se traduce en sumar una rampa lineal a la fase ($-2\pi k n_0 / N$)[cite: 25, 34].

* **Simetría (El truco de centrar la señal):**
  * Si $x[n]$ es **real**: $\vert{}X[k]\vert{}$ es par (simétrica en espejo) y $\angle X[k]$ es impar (antisimétrica)[cite: 14, 27].
  * Si $x[n]$ es **real y par** (simétrica respecto al instante $n=0$): La parte imaginaria desaparece ($\Im\{X[k]\} = 0$) y la fase es plana ($0$ o $\pi$)[cite: 27].
  * *Intuición:* Como una función coseno es simétrica respecto a $n=0$, si centramos nuestro fragmento de audio alrededor de $n=0$, eliminamos los senos (parte imaginaria) y hacemos que la fase sea limpísima de leer y requiera menos memoria de cálculo[cite: 25, 27, 38].

---

### 2. Convolución, Filtrado y Enventanado

$$\text{DFT}(x_1[n] * x_2[n]) \Longleftrightarrow X_1[k] \times X_2[k]$$

Esta propiedad funciona en ambas direcciones y explica dos de las operaciones más importantes en audio[cite: 29, 30, 31]:

1. **Convolución en el tiempo = Multiplicación en frecuencia (Filtrado y Reverb):**
   Aplicar un filtro (ecualizador) o la reverberación de una catedral a un sonido en el tiempo requiere una operación muy costosa llamada convolución ($*$)[cite: 29, 31]. Gracias a Fourier, podemos pasar el audio a frecuencias, simplemente **multiplicar** su espectro por la curva del ecualizador ($X_1[k] \times X_2[k]$), y volver al tiempo[cite: 29, 31].
2. **Multiplicación en el tiempo = Convolución en frecuencia (El efecto de las Ventanas):**
   Cuando cortamos un fragmento (*frame*) de audio para analizarlo, en realidad estamos multiplicando el audio infinito por una **ventana** ($w[n]$, como una ventana de Hamming o Hann que sube y baja suavemente para evitar clics en los bordes)[cite: 17, 18, 30, 40].
   *Intuición:* Multiplicar el audio por la ventana en el tiempo hace que, en el espectro, cada aguja pura del sonido se "convolucione" (se fusione) con la forma espectral de la ventana[cite: 30, 40]. Por eso en la vida real nunca vemos agujas infinitamente finas, sino lóbulos con la forma de la ventana que hayamos elegido[cite: 18, 30, 40].

---

### 3. Herramientas de Visualización y Verificación

* **Identidad de Parseval (Conservación de la energía):**
  $$\sum_{n=0}^{N-1} \vert{}x[n]\vert{}^2 = \frac{1}{N} \sum_{k=0}^{N-1} \vert{}X[k]\vert{}^2$$
  *Intuición:* La energía total del sonido no se crea ni se destruye al pasar de tiempo a frecuencia[cite: 32]. Es una excelente prueba para verificar en código que no te has equivocado programando tu DFT[cite: 32].

* **Escala en Decibelios (dB):**
  $$X_{\text{dB}}[k] = 20 \log_{10}\left(\frac{\max(\vert{}X[k]\vert{}, \epsilon)}{\text{ref}}\right)$$
  *Intuición:* El oído humano percibe el volumen de forma logarítmica. En una gráfica lineal ($\vert{}X[k]\vert{}$), un armónico principal alto aplasta visualmente a los armónicos pequeños haciéndolos parecer cero[cite: 33]. Al aplicar $20\log_{10}$, comprimimos los picos grandes y estiramos los pequeños para ver todos los detalles del timbre (se usa un valor mínimo $\epsilon$ para no calcular $\log(0) = -\infty$)[cite: 33].

* **Desenrollado de fase (*Phase Unwrapping*):**
  *Intuición:* Como los ángulos van de $-\pi$ a $+\pi$ ($-180^\circ$ a $+180^\circ$), cuando la fase crece continuamente da un salto brusco de $+\pi$ a $-\pi$, creando una gráfica en diente de sierra ilegible[cite: 34]. El comando `unwrap` detecta esos cortes artificiales y une los trozos para mostrarnos la línea continua real[cite: 3, 34].

---

### 4. Trucos Prácticos de Procesado: *Zero-Padding*, *Zero-Phase* y FFT

> **Conector:** Al llevar estas ecuaciones al ordenador para analizar audio real, aplicamos tres técnicas estándar que hacen que el análisis sea rápido, visualmente claro y libre de artefactos en la fase[cite: 22, 40, 42].

* **1. *Zero-Padding* (Relleno con ceros: $N > M$):**
  Tomamos un fragmento de audio de longitud $M$ (ej. $M=256$ muestras) y le añadimos ceros al final hasta llegar a un tamaño de DFT mayor $N$ (ej. $N=1024$)[cite: 17, 35]:
  $$x_{\text{zp}}[n] = \begin{cases} x[n] & \text{para } 0 \le n < M \\ 0 & \text{en otro caso} \end{cases}$$
  *Intuición crucial ($M$ vs $N$):* Añadir ceros **NO añade información nueva** ni separa dos frecuencias que ya estuvieran mezcladas (eso solo lo da capturar más audio real aumentando $M$)[cite: 17, 35, 40, 42]. Lo que hace el *zero-padding* es calcular más puntos intermedios de la misma curva espectral (**interpolación**), dibujando un espectro mucho más suave que nos permite localizar con precisión dónde está la punta de cada pico[cite: 35, 40].

* **2. *Zero-Phase Windowing* (Enventanado de fase cero):**
  *El problema:* Los ordenadores leen vectores desde el índice $0$ hasta $N-1$, por lo que una ventana normal queda centrada en la mitad del vector ($M/2$), no en $0$. Por la propiedad de desplazamiento (*shift*), ese retraso de $M/2$ ensucia todo el espectro de fase con una rampa gigante[cite: 25, 38, 40].
  *La solución:* Cortamos el fragmento de audio enventanado por la mitad: ponemos la mitad derecha al principio del `fftbuffer` (desde el índice $0$), los ceros del *zero-padding* en el centro, y la mitad izquierda al final del `fftbuffer`[cite: 38]. Como la DFT es periódica, el final del vector equivale a los tiempos negativos, ¡así que la señal queda matemáticamente centrada en $n=0$ y la fase sale limpia![cite: 38, 40, 41]

* **3. Transformada Rápida de Fourier (FFT - Algoritmo de Cooley-Tukey):**
  *Intuición:* Calcular la ecuación de la DFT directamente exige multiplicar cada una de las $N$ muestras por cada una de las $N$ frecuencias: un coste de $\mathcal{O}(N^2)$ operaciones (muy lento para tamaños grandes)[cite: 36, 37]. La **FFT no es una transformada distinta**, da exactamente el mismo resultado que la DFT, pero aprovecha las simetrías de las ondas dividiendo el problema recursivamente en mitades ($N/2$) cuando $N$ es potencia de 2 ($512, 1024, 2048\dots$)[cite: 36]. Esto reduce el coste a $\mathcal{O}(N \log_2 N)$, pasando de tardar segundos a fracciones de milisegundo[cite: 36, 37].

---

### 5. Resumen del Flujo Completo (Análisis y Síntesis en Código)

Cuando juntas todas las piezas anteriores, el flujo de trabajo estándar para procesar un fragmento de audio es[cite: 17, 18, 39, 40]:

1. **Seleccionar y Enventanar:** Cortas un fragmento de audio de tamaño $M$ y lo multiplicas por una ventana suave ($w[n]$, ej. Hamming) para evitar cortes bruscos en los extremos (controlar el *leakage*)[cite: 17, 18, 40, 41].
2. **Buffer de Fase Cero + *Zero-Padding*:** Colocas la señal centrada en $n=0$ dentro de un búfer de tamaño $N$ (siendo $N > M$ y potencia de 2) rellenando el hueco central con ceros[cite: 36, 38].
3. **FFT (Análisis):** Calculas la FFT para obtener la magnitud en dB (`mX`) y la fase desenrollada (`pX`)[cite: 17, 38, 39].
4. **IFFT (Resíntesis):** A partir de `mX` y `pX`, reconstruyes el espectro complejo, aplicas la transformada inversa (`IFFT`) y deshaces el centrado del búfer para recuperar tu onda de sonido en el tiempo `y_frame`[cite: 17, 39, 40].