Bing obsługuje trafień wyróżnianie, która oznacza terminów zapytania (lub inne warunki tej usługi Bing znajduje zastosowanie) w ciągach wyświetlania niektórych odpowiedzi. Na przykład strony `name`, `displayUrl`, i `snippet` pola może oznaczyć terminów zapytania.

Domyślnie Bing nie zawiera wyróżnianie znaczników w wyświetlanych ciągów. Aby uwzględnić znaczników, obejmują `textDecorations` parametr w żądaniu zapytania i ustaw ją na **true**. Bing oznacza terminów zapytania przy użyciu znaków E000 i E001 Unicode, aby oznaczyć początek i koniec termin. Na przykład wyrażenie kwerendy jest wypływających Dinghy, albo termin istnieje w polu termin jest ujęta w znaki wyróżnienia trafień, jak pokazano w poniższym przykładzie:  
  
![Wyróżnianie trafień](./media/cognitive-services-bing-hit-highlighting/bing-hit-highlighting.PNG) 

Przed wyświetleniem ciągu w interfejsie użytkownika, należy zastąpić znakami, które są odpowiednie dla Twojej format wyświetlania znaków Unicode. Na przykład, jeśli wyświetlasz tekst w formacie HTML, może być wyróżnieniu wyszukiwanego terminu zastępując E000 z < b\> i E001 z < /b\>. Jeśli nie chcesz zastosować formatowanie, usuń znaczniki z ciągu. 

Bing udostępnia opcję użycia znaków Unicode lub tagi HTML jako znaczniki. Aby określić, które znaczników do użycia, obejmują `textFormat` parametr zapytania. Aby oznaczyć zawartość ze znaków Unicode, należy ustawić `textFormat` aby Raw (ustawienie domyślne) i oznacz zawartość przy użyciu tagów HTML, ustaw `textFormat` do formatu HTML. 
  
Jeśli `textDecorations` jest **true**, Bing mogą obejmować następujące znaczniki w wyświetlanych ciągów odpowiedzi. Jeśli nie ma odpowiednika HTML, komórek tabeli HTML jest pusta.

|Unicode|HTML|Opis
|-|-|-
|U + E000|\<b >|Oznacza początek wyszukiwanego terminu (wyróżnianie trafień)
|U + E001|\</b >|Oznacza koniec wyszukiwanego terminu
|U + E002|\<i >|Oznacza początek kursywy zawartości 
|U + E003|\</i >|Oznacza koniec kursywy zawartości
|U + E004|\<br / >|Znaki końca wiersza
|U + E005||Oznacza początek numeru telefonu
|U + E006||Oznacza koniec numeru telefonu
|U + E007||Oznacza początek adresu
|U + E008||Oznacza koniec adresu
|U + E009|\&nbsp;|Oznacza twardej spacji
|U + E00C|\<strong >|Oznacza początek bold zawartości
|U + E00D|\</ strong >|Oznacza koniec bold zawartości
|U + E00E||Oznacza początek zawartości, w których tło powinna być jaśniejsze od otaczającego tła
|U + E00F||Oznacza koniec zawartości, w których tło powinna być jaśniejsze od otaczającego tła
|U + E010||Oznacza początek zawartości, w których tło powinna być ciemniejsze od otaczającego tła
|U + E011||Oznacza koniec zawartości, w których tło powinna być ciemniejsze od otaczającego tła
|U + E012|\<del >|Oznacza początek zawartość, która powinna być przekreślone
|U + E013|\</ del >|Oznacza koniec zawartości, która powinna być przekreślone
|U + E016|\<Sub >|Oznacza początek zawartości indeksu dolnego
|U + E017|\</ sub >|Oznacza koniec zawartości indeksu dolnego
|U + E018|\<sup >|Oznacza początek superscript zawartości
|U + E019|\</ sup >|Oznacza koniec superscript zawartości

W poniższym przykładzie przedstawiono `Computation` odpowiedzi, zawierającą znaczniki indeksu dolnego dla log(2) wyszukiwanego terminu. `expression` Pole zawiera znaczniki tylko wtedy, gdy `textDecoration` jest **true**.

![znaczniki obliczeń](./media/cognitive-services-bing-hit-highlighting/bing-markers-computation.PNG) 

Jeśli żądanie nie żądał dekoracje, wyrażenie będzie log10(2). 
  
