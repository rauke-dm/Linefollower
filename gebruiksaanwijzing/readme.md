# Gebruiksaanwijzing

### Stroomvoorziening
De Line Follower Robot wordt voorzien van stroom door de twee lithium batterijen die achteraan gemonteerd zijn op de robot. Deze worden gebruikt wanneer de robot zelfstandig zijn taak moet uitvoeren. De schakelaar helemaal achteraan de robot moet enkel worden omhoog gezet.
Het is echter ook mogelijk om de robot van stroom te voorzien door gebruik te maken van de USB poort die aanwezig is op de Arduino Leonardo. Indien u de robot via deze kabel aansluit op de USB-poort van een computer zal de Arduino van stroom voorzien zijn en kan de robot dus ook rijden. 

! Voorzie de robot nooit van beide stroomvoorzieningen tegelijkertijd. De USB poort van de computer kan hierdoor beschadigd raken. ! 

### Opstarten
Om de robot op te starten, koppel je hem los van de USB kabel en plaats je de 2 18650 batterijen in de batterijhouder volgens de instructies op de houder. 
Zorg dat de wielen van de robot vrij zijn en dus niet gehinderd door een onderliggend oppervlak, bedrading of vingers. Schakel nu de schakelaar omhoog om de robot van stroom te voorzien. 
Maak een verbinding tussen uw GSM met een seriële poort app en de bluetooth module van de robot. Bij het invullen en versturen van het commando ‘SET RUN’ zal de robot automatisch de lijn beginnen volgen. Als de robot de lijn kwijt is zal hij automatisch stoppen met rijden. Ook bij nogmaals invoeren van het commando ‘SET RUN’ zal de robot stoppen. 
Als u klaar bent dan hoeft u enkel de schakelaar terug omlaag te zetten en is de robot uitgeschakeld. 

### draadloze communicatie
Zet de robot aan zoals in vorige onderwerpen is aangehaald. Open de bluethoot instellingen van uw GSM en verbind met het apparaat 'HC-05' met toegangscode '4321'.  Open een app op uw GSM met de toepassing van een seriële bluethooth poort. Hier kan u nu een connectie leggen tussen uw GSM en de robot om commando's door te sturen naar de robot. 

#### commando's
debug: Vraag alle parameters op die ingesteld zijn op de robot. 
set run: Laat de robot rijden over de lijn of laat de robot stoppen met rijden.  
set cycle [µs]: Bepaal de tijd waarin de robot al zijn instellingen overloopt en uitvoert.   
set power [0..255]: Geef de kracht in waarmee de wielen zullen draaien. 
set kp [0..]: De waarde die bepaald hoeveel we het vermogen van een motor zullen bijsturen.     
calibrate black: Leer de robot wat zwart is. 
calibrate white  Leer de robot wat wit is. 


