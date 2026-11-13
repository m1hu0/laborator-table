# Buton de hint în meciul contra botului

## De ce
Când jucătorul e confuz și nu știe ce să mute, primește un indiciu care îl deblochează, dar îl lasă să gândească singur cât se poate. Hintul nu trebuie să-l penalizeze și nici să-i umfle scorul.

## Obiective
- Un buton de hint în joc, lângă Înapoi/Refă.
- Funcționează în două trepte:
  1. La prima apăsare se luminează piesele care trebuie mutate, fără destinații.
  2. La a doua apăsare, în aceeași tură, apare mutarea întreagă: piesele, destinațiile și notația (ex. „13/7 8/7”).
- Limită: 3 hinturi pe meci. Numărul rămas apare pe buton. Ambele trepte din aceeași tură consumă un singur hint. *(propunerea mea)*
- O mutare făcută după hint nu primește notă și nu intră în PR. În lista de mutări și în raport apare cu un semn de hint în loc de notă.

## Ce nu intră
- Hint în quiz: acolo există deja „Soluția”.
- Hint în lecții sau în pip count.
- Variante „o mutare bună oarecare”: arătăm mereu cea mai bună mutare, după GNU.

## Cum se face
- Când apeși, motorul GNU analizează poziția de la începutul turei cu zarurile curente (același apel ca la evaluarea de după mutare).
- Dacă ai mutat deja o parte din piese, hintul arată cea mai bună mutare care continuă ce ai făcut. Dacă ce ai mutat nu se potrivește cu nicio mutare bună, hintul îți spune să dai „Înapoi” și arată mutarea de la începutul turei. *(propunerea mea)*
- Butonul e dezactivat când nu e rândul tău, când hinturile s-au terminat sau când motorul GNU nu e disponibil (atunci arată „Hint indisponibil”).
- Lumina de hint dispare când muți o piesă, apeși Înapoi/Refă sau se termină tura.
- „Joc nou” resetează numărul la 3.

## Variante luate în calcul
1. Mutarea întreagă de la prima apăsare: deblochează sigur, dar înveți puțin.
2. **Aleasă:** două trepte, întâi piesele, apoi mutarea întreagă. Te obligă să gândești și ai și o ieșire.
3. O mutare bună aleasă la întâmplare: derutantă, nu știi dacă exista una mai bună.

## Lăsat la alegerea constructorului
- Pictograma butonului (în stilul săgeților Înapoi/Refă, fără emoji) și culoarea luminii (alamă, ca selecția existentă).
- Semnul de hint din lista de mutări.
