---
author: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/09/2018
ms.author: nitinme
ms.openlocfilehash: fccc036a5e0422508f7ebc3370a4b5faa5176dc2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60527914"
---
Bing obsługuje wyróżnianie, która oznacza terminów zapytania trafień (lub inne warunki tego Bing znajduje odpowiednich) ciągów wyświetlania niektórych odpowiedzi. Na przykład strony sieci Web firmy `name`, `displayUrl`, i `snippet` pola może oznaczyć terminów zapytania.

Domyślnie Bing nie uwzględnia wyróżnianie znaczników w wyświetlanych ciągów. Aby dołączyć znaczników, obejmują `textDecorations` parametru w żądaniu zapytania i ustaw ją na **true**. Bing oznacza terminów zapytania przy użyciu znaków E000 i E001 Unicode do oznaczania początku i na koniec okresu jej obowiązywania. Na przykład jeśli wyrażenie kwerendy jest pływających Dinghy albo termin istnieje w tym polu, termin jest ujęty w znaki wyróżnianie trafień, jak pokazano w poniższym przykładzie:  
  
![Wyróżnianie trafień](./media/cognitive-services-bing-hit-highlighting/bing-hit-highlighting.PNG) 

Przed wyświetleniem ciągu w interfejsie użytkownika, należy zastąpić znaki Unicode przy użyciu znaków, które są odpowiednie dla Twojej format wyświetlania. Na przykład jeśli wyświetlasz tekst w formacie HTML, użytkownik może wyróżnić wyszukiwanego terminu, zastępując E000 za pomocą < b\> i E001 za pomocą < /b\>. Jeśli nie chcesz zastosować formatowanie, usuń znaczniki z ciągu. 

Bing udostępnia opcję użycia znaczników znaków Unicode ani tagów HTML. Aby określić znaczniki, które do użycia, należy dołączyć `textFormat` parametr zapytania. Aby oznaczyć zawartość ze znakami Unicode, należy ustawić `textFormat` aby Raw (ustawienie domyślne) i oznaczanie zawartości przy użyciu tagów HTML, ustaw `textFormat` w formacie HTML. 
  
Jeśli `textDecorations` jest **true**, Bing może obejmować następujące znaczniki w wyświetlacza odpowiedzi. Jeśli nie ma odpowiednika HTML, komórki tabeli HTML jest pusty.

|Unicode|HTML|Opis
|-|-|-
|U+E000|\<b>|Oznacza początek wyrażenia kwerendy (wyróżnianie trafień)
|U+E001|\</b>|Oznacza koniec wyszukiwanego terminu
|U+E002|\<i>|Oznacza początek kursywą zawartości 
|U+E003|\</i>|Oznacza koniec zawartości kursywą
|U+E004|\<br/>|Znaki końca wiersza
|U+E005||Oznacza początek numeru telefonu
|U+E006||Oznacza koniec numeru telefonu
|U+E007||Oznacza początek adresu
|U+E008||Oznacza koniec adresu
|U+E009|\&nbsp;|Oznacza spacja nierozdzielająca
|U+E00C|\<strong>|Oznacza początek bold zawartości
|U+E00D|\</ strong >|Oznacza koniec zawartości pogrubienia
|U+E00E||Oznacza początek zawartości, w których tło powinny być cieńszego niż jego otaczającego tła
|U+E00F||Oznacza koniec zawartości, w których tło powinny być cieńszego niż jego otaczającego tła
|U+E010||Oznacza początek zawartości, w których tło powinny być ciemniejsze od jego otaczającego tła
|U+E011||Oznacza koniec zawartości, w których tło powinny być ciemniejsze od jego otaczającego tła
|U+E012|\<del>|Oznacza początek zawartości, która powinna być przekreślone
|U+E013|\</del>|Oznacza koniec zawartości, która powinna być przekreślone
|U+E016|\<sub>|Oznacza początek zawartości indeksu dolnego
|U+E017|\</ Sub->|Oznacza koniec zawartości indeksu dolnego
|U+E018|\<sup>|Oznacza początek superscript zawartości
|U+E019|\</ sup >|Oznacza koniec superscript zawartości

W poniższym przykładzie przedstawiono `Computation` odpowiedź, która zawiera indeksu dolnego znaczniki log(2) wyszukiwanego terminu. `expression` Pole zawiera znaczników tylko wtedy, gdy `textDecoration` jest **true**.

![znaczniki obliczeń](./media/cognitive-services-bing-hit-highlighting/bing-markers-computation.PNG) 

Jeśli żądanie nie żądania dekoracje, wyrażenie będzie log10(2). 
  
