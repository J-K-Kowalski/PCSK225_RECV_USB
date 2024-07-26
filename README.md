# PCSK225 RECEIVER WITH USB
## eCzas Radio PCSK-225 - Timeframe Receiver with USB

<ul>
<li>2024-07-25 - First version of the project</li>
</ul>

<p>Based on a receiver made of <a href="https://github.com/e-CzasPL/TimeReceiver225kHz" target="_blank">e-CzasPL Radio</a> I have developed a test receiver with a built-in serial/USB converter and a simple antenna amplifier on one transistor. The design is made in KiCAD 8.
</p>
<img src="img/Odb225JKK_TEST4a.png" width=500px> 

<p>Receiver contains three basic blocks
<ol>
<li>USB interface on FT230X + LDO 3.3V
<li>DSPic33FJ128 processor with optional TCXO generator and PicDIV divider
<li>Radio interface on Si4735 chip with antenna amplifier on FET transistor
</ol> 

<b>NOTE</b> - The firmware comes from the eCzasPL project. I don't have the source codes.


### USB interface

<p>I built the USB interface on the FT230XS chip, one of the simplest serial/USB chips. I used a USB-C 2.0 connector for data transfer and a 5V power supply.
<p>To protect the circuit against reverse polarity (and such things happen in experiments), I used the Q2 transistor. Resistors R2 R3 inform the host computer that 5V is required. LED7 and LED8 show the transmission status.
<p> The U3 chip is a simple LDO with an output voltage of 3.3V. The whole circuit consumes about 100mA. This regulator, with a significant excess, provides such a current intensity. The external 3.3V power connector (when USB and LDO are not mounted) is protected by the Q4 transistor. The additional input of the 5V power supply is protected only by diode D1.

### DSPic33FJ128 processor

<p>Based on the eCzas original project, I used the DSPIC33FJ128MC804-PT processor indicated in the eCzasPL projections in an easy (relatively) manual mount housing. As standard, I included 100nF blocking capacitors on the appropriate pins, I2C pull-up resistors, and a quartz generator on a typical 10MHz quartz. The LED1 - LED4 LED set signals the status of the receiver. The device has two serial interfaces: NMEA and TEST. 
<p>The NMEA interface on pin (1) of the connector sends an NMEA frame $GPRMC - which allows the use of standard libraries available for microcontrollers and computers for time decoding. The second output, TEST on pin (3), sends information about the state of the chip in text form. Both interfaces operate at a speed of 115200 baud.
<p>Optionally, a high-precision TCXO generator can be installed, which generates a frequency of 10MHZ and can power the CPU clock. This significantly increases the accuracy of the work. To obtain a 1PPS signal, a PIC12F675 chip is used (in two versions of the housing, of course, only one of them needs to be installed). PICDIV source: http://www.leapsecond.com/pic/picdiv.htm, assembler & build-instructions: https://github.com/aewallin/PICDIV.


<p> NOTE - micro-stepping and PICDIV synchronization still need to be implemented in the eCzasPL Radio firmware.

### HF Receiver

<p>Ferrite antenna, e.g., from https://telzam.pl/anteny-dcf/ (also made by antennas at 225kHz), is connected via the ANT0 connector to the FET transistor, which acts as an amplifier. It is optional, and it is possible to short-circuit the appropriate JP2 fields so that the signal reaches the input of the Si4735 chip directly. This circuit is combined with a 32kHz watch quartz, which, using the SSB patch, allows for a mixed sideband frequency (upper USB) of 1kHz. This signal obtained from the LOUT output is amplified in a single-stage U$1A amplifier, and the signal feeds the processor's ADC. I used the second amplifier of the U$1B circuit as a 1kHz signal comparator.

### Output signals

<p>The NMEA output should have data as shown below. After getting synchronized, it appears every 1 minute. In frame $PGGUM, the first 9 bytes are the same as the encoded time frame, and the last is the time since the last received valid frame. An RMC (Recommended Minimum Specific GNSS Data) frame was used to synchronize the time. The receiver does not know its geographical location, so for compatibility with GNSS receivers, static coordinates are sent in the frame <a href="https://www.google.pl/maps/place/52%C2%B014'30.6%22N+21%C2%B000'03.0%22E/@52.24183,20.9982597,17z/data=!3m1!4b1!4m4!3m3!8m2!3d52.24183!4d21.00084?entry=ttu" target=_blank>Laboratory of Time and Frequency of the Central Office of Measures in Warsaw (52.24183N,  21.00084E)</a>.

<p>A detailed description is provided in <a href="https://github.com/e-CzasPL/TimeReceiver225kHz/blob/main/doc/e-CzasPL-Opis-projektu-przykladowego-modulu-odbiorczego-e-Czas-Radio.pdf">documentation</a> on page 8.


<p>

<img src="img/NMEA.png" width=700>

<p> On the TEST output, the system sends diagnostic data. STS: MAX/MIN - maximum and minimum level with ADC (12 bit) - can help optimize the 1 kHz audio level per CPU input. IIR - is "offset" - shifting the 1kHz fringe - from the nominal frequency, showing the "detuning" of the front end - controlled by this small quartz.  This allows the DSP filter to keep up with the usable signal. 
<p>
<img src="img/test.png" width=500>

## LED Signals
<ul>
<li>LED1 - DCD Blue - signaling of recognition of the beginning of the data frame (after the keyword is detected) – remains lit until the end of the frame transmission,
<li>LED2 - RTC Green - Informs about receiving the correct time and entering it into the RTC, if it is not possible to update the RTC from the radio transmission, the diode is turned off after 24 hours,
<li>LED3 - PPS Yellow - 1PPS 1PPS signal synchronized with the received time signal,
<li>LED4 - FRM Orange - lit after the correct reception of the data frame, calculation of correction codes and confirmation of data correctness. The rising edge informs about the beginning of the second (transmitted in the data frame).
</ul>

# PCSK225 ODBIORNIK Z USB
## eCzas Radio PCSK-225 - Odbiornik ramki czasu z USB


<p> Na bazie odbiornika uroszczonego z <a href="https://github.com/e-CzasPL/TimeReceiver225kHz" target="_blank">e-CzasPL Radio</a> opracowałem testowy odbiornik z wbudowanym konwerterem serial/USB oraz prostym wzmacniaczem antenowym na jednym tranzystorze. Projekt jest wykonany w KiCAD 8.
</p>
<img src="img/Odb225JKK_TEST4a.png" width=500px> 

<p>Odbiornik zawiera trzy podstawowe bloki
<ol>
<li>Interfejs USB na układzie FT230X + LDO 3.3V
<li>Procesor DSPic33FJ128 wraz z opcjonalnym generatorem TCXO i dzielnikiem PicDIV
<li>Interfejsem radiowym na układzie Si4735 ze wzmacniaczem antenowym na tranzystorze FET
</ol>

<b>UWAGA</b> - Firmware pochodzi z projektu eCzasPL, nie posiadam kodów źródłowych


### Interfejs USB

<p>Interfejs USB zbudowałem na układzie FT230XS, jednym z najprostszych układów typu serial/USB. Zastosowałem typowe złącze USB-C 2.0 do przesyłania danych i zasilania napięciem 5V.
<p>Do zabezpieczenia układu przed odwrotną polaryzacją (a takie rzeczy się zdarzają przy eksperymentach) zastosowałem tranzystor Q2. Rezystory R2, R3 informują komputer hosta że wymagane jest napięcie 5V. stan transmisji pokazują diody LED7 i LED8.
<p> Układ U3 jest prostym LDO o napięciu wyjściowym 3.3V. Cały układ pobiera ok 100mA, ten stabilizator z dużym  nadmiarem zapewnia takie natężenie prądu. Złącze zewnętrznego zasilania 3.3V (gdy nie jest zamontowany USB i LDO) jest zabezpieczone tranzystorem Q4. Wejście dodatkowe zasilania 5V zabezpieczone jest tylko diodą D1.

### Procesor DSPic33FJ128

<p>W układzie zastosowałem wskazany w projekcje eCzasPL procesor DSPIC33FJ128MC804-PT w łatwej (stosunkowo) do montażu ręcznego obudowie. Standardowo dołączyłem kondensatory blokujące 100nF na odpowiednich pinach, rezystory podciągające I2C, generator kwarcowy na typowym kwarcu 10MHz. Zestaw ledów LED1 - LED4 sygnalizuje status odbiornika. Układ ma dwa interfejsy szeregowe: NMEA i TEST. 
<p>Interfejs NMEA na pinie (1) złącza wysyła ramkę NMEA $GPRMC - co pozwoliło na stosowania do dekodowania czasu standardowych bibliotek dostępnych dla mikrokontrolerów i komputerów. Drugie wyjście TEST na pinie (3) wysyła informacje o stanie układu w postaci tekstowej. Obydwa interfejsy pracują w szybkości 115200 bodów.
<p>Opcjonalnie można zamontować precyzyjny generator TCXO który generuje częstotliwość 10MHZ i może zasilać zegar procesora. Znacznie zwiększa to dokładność pracy. W celu uzyskania sygnału 1PPS zastosowano układ PIC12F675 (w dwóch wersjach obudowy, oczywiście nalezy zamontować tylko jeden z nich). Kod źródłowy PICDIV: http://www.leapsecond.com/pic/picdiv.htm, assembler & build-instructions: https://github.com/aewallin/PICDIV.
	
<p> UWAGA - microsteping i synchronizacja PICDIV nie jest jeszcze zaimplementowana w firmware eCzasPL Radio.

### Odbiornik HF

<p>Antena ferrytowa np. z https://telzam.pl/anteny-dcf/ (wykonują także anteny na 225kHz) podłączona jest przez złącze ANT0 do tranzystora FET który pełni rolę wzmacniacza. Jest on opcjonalny i można zewrzeć odpowiednie pola JP2 aby sygnał docierał bezpośrednio do wejścia układu Si4735. Układ ten jest połączony z kwarcem zegarkowym 32kHz, co przy użyciu patcha SSB pozwala na uzyskanie zmieszanej częstotliwości wstęgi bocznej (górnej USB) o częstotliwości 1kHz. Ten sygnał uzyskiwany z wyjścia LOUT jest wzmacniany w jednostopniowym wzmacniaczu U$1A i sygnał zasila przetwornik ADC w procesorze. Drugi wzmacniacz układu U$1B użyłem jako komparator sygnału 1kHz.

### Sygnały wyjściowe

<p>Na wyjściu NMEA powinny byc dane jak pokazano poniżej. Po uzyskaniu synchronizacji pojawia się co 1min. ramka $PGGUM, pierwsze 9 bajtów to kojejne bajty zakodowanej ramki czasu, ostatni to czas od ostatnio odebranej poprawnej ramki. Do synchronizacji czasu zastosowano ramkę RMC (Recommended Minimum Specific GNSS Data). Odbiornik nie zna swojego położenia geograficznego, dlatego dla zachowania kompatybilności z odbiornikami GNSS w ramce przesyłane są statyczne współrzędne <a  href="https://www.google.pl/maps/place/52%C2%B014'30.6%22N+21%C2%B000'03.0%22E/@52.24183,20.9982597,17z/data=!3m1!4b1!4m4!3m3!8m2!3d52.24183!4d21.00084?entry=ttu" target=_blank>Laboratorium Czasu i Częstotliwości Głównego Urzędu Miar w Warszawie (52.24183N, 21.00084E)</a>.

<p>Dokładny opis zawarty jest w <a href="https://github.com/e-CzasPL/TimeReceiver225kHz/blob/main/doc/e-CzasPL-Opis-projektu-przykladowego-modulu-odbiorczego-e-Czas-Radio.pdf">dokumentacji</a>  na stronie 8.


<p>

<img src="img/NMEA.png" width=700>

<p> Na wyjściu TEST układ wysyła dane diagnostyczne. STS: MAX/MIN - maksymalny i minimalny poziom z ADC (12 bitów) - może być pomocny przy optymalizacji poziomu audio 1 kHz na wejście procesora. IIR - to "offset" - przesunięcie prążka 1kHz - od częstotliwości nominalnej, pokazuje "odstrojenie" frontendu - sterowanego tym kwarcem zegarkowym.  Umożliwia to nadążenie filtrem DSP za sygnałem użytecznym. 
<p>
<img src="img/test.png" width=500>

## Sygnały LED
<ul>
<li>LED1 - DCD Blue - sygnalizacja rozpoznania początku ramki danych (po wykryciu słowa kluczowego) – pozostaje zapalona do końca transmisji ramki,
<li>LED2 - RTC Green - Informuje o odebraniu poprawnego czasu i wpisaniu go do RTC, w przypadku braku możliwości aktualizacji RTC z transmisji radiowej dioda jest gaszona po 24 godzinach,
<li>LED3 - PPS Yellow - 1PPS Sygnał 1PPS zsynchronizowany z odebranym sygnałem czasu,
<li>LED4 - FRM Orange - zapalana po poprawnym odebraniu ramki danych, przeliczeniu kodów korekcyjnych oraz potwierdzeniu poprawności danych. Narastające zbocze informuje o początku sekundy (przesłanej w ramce danych).
</ul>

	

	
