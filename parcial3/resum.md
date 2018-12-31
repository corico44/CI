INDEX
======
+ [**Tema 6: A/D**](#conversors-ad-adc)
  + [Conversors A/D](#conversors-ad-adc)
  + [Digit to analog converter (DAC)](#da-digit-to-analog-converter---dac)
  + [A/D versio FLASH](#ad-versio-flash)
  + [A/D barat](#ad-barat)
  + [A/D per cerca dicotomica (aproximacio successiva)](#ad-per-cerca-dicotomica-aproximacio-successiva)
  + [Temps de conversio](#temps-de-conversio)
  + [Cada quan he de convertir?](#cada-quant-temps-he-de-convertir)
      + [Teorema de Fourier](#teorema-de-fourier)
      + [Teorema de Sannon-Nyquist](#teorema-de-shannon-nyquist)
      + [Teorema de l'Enginyer](#teorema-de-lenginyer)
  + [Exemples](#exemples)
      + [Calcul de Vref- i Vref+ d'un conversor A/D](#calcul-de-vref--i-vref-dun-conversor-ad)
  + [Annexos Tema 6](#annexos-tema-6)
+ [**Tema 7: Comunicacio serie**](#tema-7-comunicació-serie)
  + [Comunicació en sèrie](#comunicació-en-sèrie)
  + [Linea Sèrie (RS-232)](#linea-sèrie-rs-232)
      + [Transmissio](#transmissio)
      + [Recepcio](#recepcio)
  + [Errors en la transmissio de dades](#errors-en-la-transmissio-de-dades)
  + [Serial Peripheral interface (SPI)](#serial-peripheral-interface-spi)
  + [Modul MSSP](#modul-mssp)
      + [El mode SPI](#el-mode-spi-mssp-module)
      + [Operacio SPI](#operacio-spi)
  + [Exemples](#exemples)
      + [Calcul aproximacio optima velocitat](#calcul-aproximacio-optima-velocitat)
  + [Annexos Tema 7](#annexos-tema-7)


# **Tema 6:** A/D
## Conversors A/D (ADC)

![concepto de conversion](https://github.com/GarJor/CI/blob/master/Utilidades/conept.png)

- **Vref:** Entre que i que anira la meva senyal
- **Conversion order:** He de dir quan he de convertir. Permet programar cada quan he de convertir una mostra per a que sigui ben interpretada

+ El Pic18 te un ADC de 10 bits [d'aproximacio successiva](#ad-per-cerca-dicotomica-aproximacio-successiva).
També conté els següents registres:
    + A/D Result High Register ([ADRESH](#result-format-adresh--adresl))
    + A/D Result Low Register ([ADRESL](#result-format-adresh--adresl))
    + A/D Control Register 0 ([ADCON0](#adcon0-register)) (source selection)
    + A/D Control Register 1 ([ADCON1](#adcon1-register)) (reference selection)
    + A/D Control Register 2 ([ADCON2](#adcon2-register)) (timing selections)  
> A més també haurem de tenir en compte els parametres:
> + ANSELX per a la configuració de pins
> + ADIF, ADIE i ADIP per a la AD interrupt  



[torna a l'index](#index)

## D/A (digit to analog converter -> DAC)
![versio flash](https://github.com/GarJor/CI/blob/master/Utilidades/DAC.png)
![versio flash](https://github.com/GarJor/CI/blob/master/Utilidades/DAconverter.png)

- Cada bit de menor pes aporta menys voltatge a la sortida que els d'un pes més gran, ja que passa per mes resistencies. Cada bit te la meitat de pes del seguent.

[torna a l'index](#index)
## A/D versio FLASH
![versio flash](https://github.com/GarJor/CI/blob/master/Utilidades/flash.png)

- **Comparador:** Dona 1 si Analog Input >= el que surt de la resistencia, es a dir, si Vin+ >= Vin- .
- **Decoder:** Retorna un valor de 10 bits. Aquest valor permet saber on està el **tall** (el punt en el 'Digital Thermometer Code' que es pasa del 0 al 1)
> - **Ref+ <= VDD** i  **Ref- >= GND** .

[torna a l'index](#index)
## A/D barat

![aproximacio barata](https://github.com/GarJor/CI/blob/master/Utilidades/DAbarat.png)

- Cada vegada que s'augmenta el comptador (*SAR*) i es compara (la seva conversio a analogic) amb el valor analògic, si el valor analogic es més gran es suma 1 al comptador i es torna a comparar fins que el valor del comptador (en analog.) sigui = al valor analogic.
- Em dona el valor dels 10 bits.
- Trigara mes o menys en funcio del volts que hi cardis
- La caixeta S/H es un circuit **Sample & Hold**, i serveix per evitar que el valor analogic canvii mentre encara no ha acabat la conversio:

![sample/hold](https://github.com/GarJor/CI/blob/master/Utilidades/sample&hold.png)

> Un cop el condensador esta carregat s'obre l'interruptor per evitar que el valor del Vin canvii.

[torna a l'index](#index)
## A/D per cerca dicotomica (aproximacio successiva)

![aproximacio successiva](https://github.com/GarJor/CI/blob/master/Utilidades/seccesiveaproach.png)

- Vaig posant uns succesivament començant per l'esquerra del registre, si el comparador em retorna 1 (osigui, mentre que el valor del Analog Input >= ell valor que dona el DAC) deixo el 1 que he col·locat. Si no, el canvio per un 0 i vaig a poosar un 1 a la posicio seguent per repetir el proces fins que acabi amb els 10 bits.

> Noteu que realment el que acabem fent es desplaçar '1' a cap a la dreta fins que trobes la posicio correcte. Per tant el que fem es anar dividint entre 2 --> cerca dicotomica.

> El Vin tambe compta amb un circuit S/H 

[torna a l'index](#index)
## Temps de conversio

+ Es el temps desde que entra una dada fins que acaba la seva conversió. Hi ha dos fases:
  1. *Sampling*
  2. *Convert*
  
+ **Sampling:** Cal calcular-ho. Hem de saber quan triga a carregarse el condensador per a que no ens dongui error.
![sampling time](https://github.com/GarJor/CI/blob/master/Utilidades/sampleTime.png)
+ **Convert:** Per al PIC18F45K22 assumirem l'esquema d'aproximacio logaritmica, segons el qual haura de pasar per cada un dels bit i comprovar que el valor sigui correcte o no, per tant tindrem:
    
    **1** (inicial) + **10** (bits) + **1** (final) = **12** clocks (*A/D te el seu propi temps de clock*) 
    
 > Per tant el temps el calcularem: TAD = D/F<sub>osc</sub>  *on D es el divisor que s'aplica*.
 
 > El temps total de la mostra sera TACQ+12*TAD
 
 [torna a l'index](#index)   
 ## Cada quant temps he de convertir?
 
 #### Teorema de Fourier
 ![teorema fourier](https://github.com/GarJor/CI/blob/master/Utilidades/fourier.png)
 + Qualsevol funcio temporal es pot expressar com una suma de funcions sinusoïdals i cosinusoidals.
 > Aixo en permet veure quines freqüencies te la teva señal.
  
 #### Teorema de Shannon-Nyquist
 + f<sub>mostreig</sub> > 2 * f<sub>max senyal</sub> 
 #### Teorema de l'Enginyer
 + f<sub>mostreig</sub> = 10 * f<sub>max senyal</sub> 
 
 [torna a l'index](#index)
 ## Exemples
 #### Calcul de Vref- i Vref+ d'un conversor A/D
 > **Un conversor AD de 10 bits llegeix el valor digital 255. Si Vss=0V, Vdd=5V, Fosc=8MHz, Vin
(entrada a l’AD) és de 0,5V, quín valor tenen Vref- i Vref+ del conversor?**

> ***valor digital*** = ***rang maxim digital*** * (***Vin*** - **Vref-**)/(**Vref+** - **Vref-**)

> Substituim:
>
>	255 = 1023*(0.5-**Vref-**)/(**Vref+** - **Vref-**)
>
> *Nota: El rang maxim es 1023 ja que el conversor ens dona el valor digital en 10 bits --> 2<sup>10</sup> - 1* .
>
> *Nota2:  Hi ha un interval de solucions pero hem de tenir en compte que **Vref-** ha de ser >=  ***Vss*** i < ***Vin*** .*
>
> A partir de la formula trobem que:  
> **Vref-** = 0V, i **Vref+** = 2V, compleix les condicions.

[torna a l'index](#index)
 ## Annexos Tema 6:
 ### ADCON0 register
 ![ADCON0](https://github.com/GarJor/CI/blob/master/Utilidades/ADCON0.png)
 
 [torna a l'index](#index)
 ### ADCON1 register
 ![ADCON1](https://github.com/GarJor/CI/blob/master/Utilidades/ADCON1.png)
 
 [torna a l'index](#index)
 ### ADCON2 register
 ![ADCON2](https://github.com/GarJor/CI/blob/master/Utilidades/ADCON2.png)
 
 [torna a l'index](#index)
 ### Result Format (ADRESH | ADRESL)
 ![Result Format](https://github.com/GarJor/CI/blob/master/Utilidades/resultFormat.png)
 
 [torna a l'index](#index)
 # **Tema 7:** Comunicació serie
 ## Comunicació en sèrie
 ![serial comunication](https://github.com/GarJor/CI/blob/master/Utilidades/serialcomunication.png)
 + S'utilitza un *shift register* (un registre que envia bits un a un) connectat a un altre *shift register*
 + Cal sincronitzar el clock:
    - Amb cable --> **Síncrona**
    - sense cable --> **Asíncrona**
> Alternativa -> *Comunicacio en paral·lel* : Conectats 8 pins amb 8 pins --> Molt costós
+ La comunicacio pot ser:
    - **Full-Duplex:** es pot parlar i escoltar alhora.
    - **Half-Duplex:** Walkie-Talkie.
    
[torna a l'index](#index)
## Linea Sèrie (RS-232)

+ Els pins associoats al RS-232 son:
  - [RC6/TX1/CK1](#transmissio) i [RC7/RX1/DT1](#recepcio) (USART1)
  - [RD6/TX2/CK2](#transmissio) i [RD7/TX2/DT2](#recepcio) (USART2)
+ Els regitres associats al RS-232 son:
  - Transmit status register ([TXSTA](#txstax-i-rcstax)) - Transmit register (TXREG)
  - Receive status register ([RCSTA](#txstax-i-rcstax)) - Receive register (RCREG)
  - Baud rate generate register ([SPBRG](#spbrg-register))
  
  
  ![assoc. baud](https://github.com/GarJor/CI/blob/master/Utilidades/REGassociatedbBaud.png)
> ALGUNS CONCEPTES:
> - *Velocitat:* 2400, 4800, 9600... bps (bits per second a.k.a baud)
> - *Bits per byte:* 5, 6, 7 i 8
> - *Paritat:* Detectar un error
> - *Bits d'Stop:* 1, 1+1/2, 2 --> Ens serveix per donar temps a màquines lentes.
> - La unitat de informacio es el caracter, no el byte.
> - El temps d'un bit es l'invers de la velocitat.


> Seran necessaris **3 cables** per tal de no fregir els components: **RxD, TxD i GND**


+ Al principi de la comunicacio, quan esta parada, hi ha un bit a 1. Quan el posem a 0 (**bit de start**) es quan comença la comunicació; s'envien els 8 bits, el *bit de paritat* i un 1 que es el **bit d'Stop** :

![serial chronogram](https://github.com/GarJor/CI/blob/master/Utilidades/serialchronogram.png)

+ El **bit de paritat** serveix per datectar si hagut soroll que ha molestat a la conexio i es dona indicant la paritat (o no) del nombre de d'1s de la dada.

#### Transmissio
![EUSART transmit](https://github.com/GarJor/CI/blob/master/Utilidades/USARTtransmit.png)
#### Recepcio
![EUSART reception](https://github.com/GarJor/CI/blob/master/Utilidades/USARTrecieve.png)
 > La recepcio comença un cop s'ha detectat el *bit de START*
+ La dada rebuda en el *Shift Register* es guarda en FIFO (RCREG)
+ Si es reben tres caracters en una fila, el FIFO es desborda -> [Overrun Error](#errors-en-la-transmissio-de-dades)
+ RCIF esta high fins que FIFO esta a buit 


[torna a l'index](#index)
## Errors en la transmissio de dades
+ **Farming error:** Errors amb el start i el stop
> **Error de Clock:** el maxim d'error que pot haver es del 10%. *(es un error de Farming)* 
+ **Receiver overrun:** Quan va massa rapid; la cpu no ha llegit les dades durant un temps
+ **Pariry errors:** problemes de paritat


[torna a l'index](#index)
## Serial Peripheral interface (SPI)
+ El bus SPI és un estàndard **síncron** d'enllaços de dades en sèrie que opera en mode **full duplex**.
+ Els dispositius es comuniquen en mode **master/slave**. On el dispositiu *master* inicialitza el marc de dades.
+ Es permeten multiples dispositius *slave* amb lineas individuals de seleccio de *salve*: (chip select)
![esquema SPI](https://github.com/GarJor/CI/blob/master/Utilidades/SPIm-s.png)  

[torna a l'index](#index)
## Modul MSSP
+ Dos modes de operacio:
    - SPI
    - Inter-integrated circuit (I2C)
    > Els dos modes no poden estar actius alhora ja que comperteixen els mateixos pins
+ pot interactuar amb *EEPROM serial, shift registers, display drivers,
A/D converters, D/A converters, sensors digitals de temperatura , time-of-day
chips, etc.*
+ Els dispositius es divideixen en master i slave (en qualsevol dels dos modes d'operacio)
+ El modul utilitza **3 pins**:
    - Serial data out (*SDO*)—RC5/SDO
    - Serial data in (*SDI*)—RC4/SDI/SDA
    - Serial clock (*SCK*)—RC3/SCK/LVDIN
    > un 4rt pin (*SS*) pot ser utilitzat en el mode slave  
       
[torna a l'index](#index)

#### El mode SPI (MSSP module)
![mode SPI](https://github.com/GarJor/CI/blob/master/Utilidades/MSSPspimode.png)  
+ 8 bits de dades son intercanviats de forma sincrona en una operacio
+ En **slave mode** els 4 senyals son utilitzats *(SDO, SDI, SCK, SS)*
+ EN **master mode** el pin *SS* no es necessari.
+ Els registres per a la [operacio SPI](#operacio-spi) son:
    - MSSP control register 1 (SSPCON1)
    - MSSP status register (SSPSTAT)
    - Serial receive/transmit buffer (SSPBUF)
    - MSSP shift register (SSPSR)
    > aquest ultim no es accesible directament
 + **una escritura a SSPBUF escriura tambe a SSPSR**  
 
[torna a l'index](#index)
#### Operacio SPI
![operacio SPI](https://github.com/GarJor/CI/blob/master/Utilidades/spiOperation.png)  
> Es mostra una simplificacio del circuit de coneccio entre un SPI master i un slave. (Conceptualment es un *shift register* de 16 bits dividit en dues parts.)
+
+
+
+
[torna a l'index](#index)
## Exemples
### Calcul aproximacio optima velocitat
> **Amb un Oscil·lador de 10MHz volem configurar la línia sèrie a 9600bps.
Quina és la millor aproximació que podem tenir? Ens suposarà algun problema?**  
> Hem de provar tots els escenaris, observant la [taula](#spbrg-register):  
> 8 bits: 9600 = 10MHz / (**64** (n+1)) -> n = 15,276 -> n= 15. La velocitat real és 9765,625 bps.
>
> 8 o 16: 9600 = 10MHz / (**16** (n+1)) -> n = 64,104 -> n= 64. La velocitat real és 9615,384 bps.
>
>8 o 16: 9600 = 10MHz / (**4** (n+1)) -> n = 259,417 -> n= 259. La velocitat real és 9615,384 bps.
>
> Qualsevol de les dues opcions darreres és equivalent, podrem triar una o altra depenent de si
volem que sigui síncron o asíncron o volem usar el BGR en mode 8 o 16 bits. La darrera ha de
ser necessàriament en 16 bits ja que n=259 no cap en 8
> 9615,384 representa un error de menys de l’ 1% respecte 9600 per tant no representarà cap
problema.

[torna a l'index](#index)

### Calcul error velocitat 
>  **Volem enviar a 38400 bps. Quin error tenim?**
>
>    38400 = F<sub>osc</sub> / 64*(n+1)  --> n=2,25 --> 2

[torna a l'index](#index)
## Annexos Tema 7:
### TXSTAx i RCSTAx
![TXSTAx i RCSTAx](https://github.com/GarJor/CI/blob/master/Utilidades/TXSTAX%26RCSTAX.png)

[torna a l'index](#index)
### SPBRG register
![SPBRG](https://github.com/GarJor/CI/blob/master/Utilidades/SPBRG.png)

[torna a l'index](#index)


