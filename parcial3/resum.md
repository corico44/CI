INDEX
======
+ [**Tema 6: A/D**](#conversors-ad-adc)
  + [Conversors A/D](#conversors-ad-adc)
  + [Versio FLASH](#versio-flash)
  + [Digit to analog converter (DAC)](#da-digit-to-analog-converter---dac)
  + [A/D barat](#ad-barat)
  + [A/D per cerca dicotomica (aproximacio successiva)](#ad-per-cerca-dicotomica-aproximacio-successiva)
  + [Temps de conversio](#temps-de-conversio)
  + [Cada quan he de convertir?](#cada-quant-temps-he-de-convertir)
      + [Teorema de Fourier](#teorema-de-fourier)
      + [Teorema de Sannon-Nyquist](#teorema-de-shannon-nyquist)
      + [Teorema de l'Enginyer](#teorema-de-lenginyer)
  + [Annexos Tema 6](#annexos-tema-6)
+ [**Tema 7: Comunicacio serie**](#tema-7-comunicacio-serie)




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

## Versio FLASH
![versio flash](https://github.com/GarJor/CI/blob/master/Utilidades/flash.png)

- **Comparador:** Dona 1 si Analog Input >= el que surt de la resistencia, es a dir, si Vin+ >= Vin- .
- **Decoder:** Retorna un valor de 10 bits. Aquest valor permet saber on està el **tall** (el punt en el 'Digital Thermometer Code' que es pasa del 0 al 1)
> - **Ref+ = VDD **  **Ref- = GND** (si el voltatge va de 0 a 5).





## D/A (digit to analog converter -> DAC)
![versio flash](https://github.com/GarJor/CI/blob/master/Utilidades/DAC.png)
![versio flash](https://github.com/GarJor/CI/blob/master/Utilidades/DAconverter.png)

- Cada bit de menor pes aporta menys voltatge a la sortida que els d'un pes més gran, ja que passa per mes resistencies. Cada bit te la meitat de pes del seguent.

## A/D barat

![aproximacio barata](https://github.com/GarJor/CI/blob/master/Utilidades/DAbarat.png)

- Cada vegada que s'augmenta el comptador (*SAR*) i es compara (la seva conversio a analogic) amb el valor analògic, si el valor analogic es més gran es suma 1 al comptador i es torna a comparar fins que el valor del comptador (en analog.) sigui = al valor analogic.
- Em dona el valor dels 10 bits.
- Trigara mes o menys en funcio del volts que hi cardis
- La caixeta S/H es un circuit **Sample & Hold**, i serveix per evitar que el valor analogic canvii mentre encara no ha acabat la conversio:

![sample/hold](https://github.com/GarJor/CI/blob/master/Utilidades/sample&hold.png)

> Un cop el condensador esta carregat s'obre l'interruptor per evitar que el valor del Vin canvii.

## A/D per cerca dicotomica (aproximacio successiva)

![aproximacio successiva](https://github.com/GarJor/CI/blob/master/Utilidades/seccesiveaproach.png)

- Vaig posant uns succesivament començant per l'esquerra del registre, si el comparador em retorna 1 (osigui, mentre que el valor del Analog Input >= ell valor que dona el DAC) deixo el 1 que he col·locat. Si no, el canvio per un 0 i vaig a poosar un 1 a la posicio seguent per repetir el proces fins que acabi amb els 10 bits.

> Noteu que realment el que acabem fent es desplaçar '1' a cap a la dreta fins que trobes la posicio correcte. Per tant el que fem es anar dividint entre 2 --> cerca dicotomica.

> El Vin tambe compta amb un circuit S/H 

## Temps de conversio

+ Es el temps desde que entra una dada fins que acaba la seva conversió. Hi ha dos fases:
  1. *Sampling*
  2. *Convert*
  
+ **Sampling:** Cal calcular-ho. Hem de saber quan triga a carregarse el condensador per a que no ens dongui error.
![sampling time](https://github.com/GarJor/CI/blob/master/Utilidades/sampleTime.png)
+ **Convert:** Per al PIC18F45K22 assumirem l'esquema d'aproximacio logaritmica, segons el qual haura de pasar per cada un dels bit i comprovar que el valor sigui correcte o no, per tant tindrem:
    
    **1** (inicial) + **10** (bits) + **1** (final) = **12** clocks (*A/D te el seu propi temps de clock*) 
    
 ## Cada quant temps he de convertir?
 
 #### Teorema de Fourier
 ![teorema fourier](https://github.com/GarJor/CI/blob/master/Utilidades/fourier.png)
 + Qualsevol funcio temporal es pot expressar com una suma de funcions sinusoïdals i cosinusoidals.
 > Aixo en permet veure quines freqüencies te la teva señal.
  
 #### Teorema de Shannon-Nyquist
 + f<sub>mostreig</sub> > 2 * f<sub>max senyal</sub> 
 #### Teorema de l'Enginyer
 + f<sub>mostreig</sub> = 10 * f<sub>max senyal</sub> 
 
 ## Annexos Tema 6:
 ### ADCON0 register
 ![ADCON0](https://github.com/GarJor/CI/blob/master/Utilidades/ADCON0.png)
 ### ADCON1 register
 ![ADCON1](https://github.com/GarJor/CI/blob/master/Utilidades/ADCON1.png)
 ### ADCON2 register
 ![ADCON2](https://github.com/GarJor/CI/blob/master/Utilidades/ADCON2.png)
 ### Result Format (ADRESH | ADRESL)
  ![Result Format](https://github.com/GarJor/CI/blob/master/Utilidades/resultFormat.png)
 # **Tema 7:** Comunicacio serie
