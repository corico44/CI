INDEX
======
+ [Conversors A/D](#conversors-ad)
+ [Versio FLASH](#versio-flash)
+ [Digit to analog converter (DAC)](da-(digit-to-analog-converter-->-dac))
+ [A/D barat (aproximacio successiva)](ad-barat)
+ [A/D per cerca dicotomica (aproximacio logaritmica)](a`/d-per-cerca-dicotomica-(aproximacio-logaritmica))
+ [Temps de conversio](#temps-de-conversio)

## Conversors A/D

![concepto de conversion](https://github.com/GarJor/CI/blob/master/Utilidades/conept.png)

- **Vref:** Entre que i que anira la meva senyal
- **Conversion order:** He de dir quan he de convertir. Permet programar cada quan he de convertir una mostra per a que sigui ben interpretada

## Versio FLASH
![versio flash](https://github.com/GarJor/CI/blob/master/Utilidades/flash.png)

- **Comparador:** Dona 1 si Analog Input > el que surt de la resistencia, es a dir, si Vin+ > Vin- .
- **Decoder:** Retorna un valor de 10 bits. Aquest valor permet saber on està el **tall** (el punt en el 'Digital Thermometer Code' que es pasa del 0 al 1)
> - **Ref+ = VDD **  **Ref- = GND** (si el voltatge va de 0 a 5).





## D/A (digit to analog converter -> DAC)

![versio flash](https://github.com/GarJor/CI/blob/master/Utilidades/DAconverter.png)

- Cada bit de menor pes aporta menys voltatge a la sortida que els d'un pes més gran, ja que passa per mes resistencies. Cada bit te la meitat de pes del seguent.

## A/D barat (aproximacio successiva)

![aproximacio barata](https://github.com/GarJor/CI/blob/master/Utilidades/DAbarat.png)

- Cada vegada que s'augmenta el comptador (SAR) i es compara (la seva conversio a analogic) amb el valor analògic, si el valor analogic es més gran es suma 1 al comptador i es torna a comparar fins que el valor del comptador (en analog.) sigui = al valor analogic.
- Em dona el valor dels 10 bits.
- Trigara mes o menys en funcio del volts que hi cardis
- La caixeta S/H es un circuit **Sample & Hold**, i serveix per evitar que el valor analogic canvii mentre encara no ha acabat la conversio:

![sample/hold](https://github.com/GarJor/CI/blob/master/Utilidades/sample&hold.png)

> Un cop el condensador esta carregat sobre l'interruptor per evitar que el valor del Vin canvii.

## A/D per cerca dicotomica (aproximacio logaritmica)

![aproximacio successiva](https://github.com/GarJor/CI/blob/master/Utilidades/seccesiveaproach.png)

- Vaig poant uns succesivament, si el comptador em dona 1 es que el nombre esta ben col·locat. Si no, se'l canvia. Demanara el valor correcte dels 10 bits
> el Vin tambe compta amb un circuit S/H 

## Temps de conversio

