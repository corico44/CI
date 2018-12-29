Conversors A/D
===================

![concepto de conversion](https://github.com/GarJor/CI/blob/master/Utilidades/conept.png)

> - **Vref:** Entre que i que anira la meva senyal
> - **Conversion order:** He de dir quan he de convertir. Permet programar cada quan he de convertir una mostra per a que sigui ben interpretada

### Versio FLASH
![versio flash](https://github.com/GarJor/CI/blob/master/Utilidades/flash.png)

> - **Comparador:** Dona 1 si Analog Input > el que surt de la resistencia, es a dir, si Vin+ > Vin- .
> - **Vref+ = VDD       Vref- = GND** .

Els 10 bits ens permeten saber on esta el **tall** (el punt en el 'Digital Thermometer Code' que es pasa del 0 al 1)



### D/A (digit to analog)

![versio flash](https://github.com/GarJor/CI/blob/master/Utilidades/DAconverter.png)

- Cada bit de menor pes */* menys voltatge a la sortida. Cada bit te la meitat de pes del seguent.

### A/D barat (aproximacio successiva)
![aproximacio successiva](https://github.com/GarJor/CI/blob/master/Utilidades/seccesiveaproach.png)

- Cada vegada que s'augmenta el comptador (**succesive aproximation register**) es compara i es torna a comparar fins que el valor del comptador es el valor que ha comptat.
- Em dona el valor dels 10 bits.
- Trigara mes o menys en funcio del volts que hi cardis

### A/D per cerca dicotomica (aproximacio logaritmica)

- Vaig poant uns succesivament, si el comptador em dona 1 es que el nombre esta ben col·locat. Si no, se'l canvia. Demanara el valor correcte dels 10 bits
