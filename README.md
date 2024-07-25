# PCSK225 RECEIVER WITH USB
## eCzas Radio PCSK-225 - Timeframe Receiver with USB

<ul>
<li>2024-07-25 - First version of the project</li>
</ul>

<p> Na bazie odbiornika uroszczonego z <a href="https://github.com/e-CzasPL/TimeReceiver225kHz" target="_blank">e-CzasPL Radio</a> opracowałem testowy odbiornik z wbudowanym konwerterem serial/USB oraz prostym wzmacniaczem antenowym na jednym tranzystorze. Projekt jest wykonany w KiCAD 8.
</p>
<img src="img/Odb225JKK_TEST4.png" width=500px> 

<p>Odbiornik zawiera trzy podstawowe bloki
<ol>
<li>Interfejs USB na układzie FT230X + LDO 3.3V
<li>Procesor DSPic33FJ128 wraz z opcjonalnym generatorem TCXO i dzielnikiem PicDIV
<li>Interfejsem radiowym na układzie Si4735 ze wzmacniaczem antenowym na tranzystorze FET
</ol>



