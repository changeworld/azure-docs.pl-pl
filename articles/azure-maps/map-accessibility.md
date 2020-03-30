---
title: Tworzenie aplikacji mapy z ułatwieniami dostępu za pomocą usługi Azure Maps | Mapy platformy Microsoft Azure
description: W tym artykule dowiesz się, jak utworzyć aplikację z funkcjami ułatwień dostępu przy użyciu usług Microsoft Azure Maps.
services: azure-maps
author: rbrundritt
ms.author: richbrun
ms.date: 12/10/2019
ms.topic: conceptual
ms.service: azure-maps
manager: cpendleton
ms.openlocfilehash: 5fa9e159fa0ac76ce8c585936059fb7f3151c7c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79473323"
---
# <a name="building-an-accessible-application"></a>Tworzenie aplikacji z ułatwieniami dostępu

Ponad 20% użytkowników Internetu potrzebuje dostępnych aplikacji internetowych. W związku z tym należy upewnić się, że aplikacja jest zaprojektowana w taki sposób, aby każdy użytkownik mógł z niej łatwo korzystać. Zamiast myśleć o dostępności jako zestaw zadań do wykonania, należy myśleć o tym jako część ogólnego środowiska użytkownika. Im bardziej dostępna aplikacja, tym więcej osób może z niej korzystać. 

Jeśli chodzi o bogatą zawartość interaktywną, taką jak mapa, niektóre typowe zagadnienia dotyczące dostępności to:
- Obsługa czytnika ekranu dla użytkowników, którzy mają trudności z wyświetlaniem aplikacji sieci web.
- Mieć wiele metod interakcji z aplikacją internetową i nawigacji, takich jak mysz, dotyk i klawiatura.
- Upewnij się, że kontrast kolorów jest taki, aby kolory nie mieszają się ze sobą i stały się trudne do odróżnienia od siebie. 

Zestaw Azure Maps Web SDK jest fabrycznie wyposażony w wiele funkcji ułatwień dostępu, takich jak:
- Opisy czytnika ekranu, gdy mapa jest przesuwana i gdy użytkownik koncentruje się na formancie lub wyskakującym okienku.
- Obsługa myszy, dotyku i klawiatury.
- Dostępne wsparcie kontrastu kolorów w stylu mapy drogowej.

Szczegółowe informacje dotyczące pełnej zgodności ułatwień dostępu dla wszystkich produktów firmy Microsoft można znaleźć [tutaj](https://cloudblogs.microsoft.com/industry-blog/government/2018/09/11/accessibility-conformance-reports/). Wyszukaj "Usługa Azure Maps web", aby znaleźć dokument specjalnie dla zestawu SDK usługi Azure Maps Web. 

## <a name="navigating-the-map"></a>Poruszanie się po mapie

Istnieje kilka różnych sposobów powiększania, przesuwania, obracania i rozbijania mapy. Poniżej przedstawiono wszystkie sposoby poruszania się po mapie.

**Powiększanie mapy**

- Za pomocą myszy kliknij dwukrotnie mapę, aby powiększyć jeden poziom.
- Za pomocą myszy przewiń kółko, aby powiększyć mapę.
- Korzystając z ekranu dotykowego, dotknij mapy dwoma palcami i zsuń się, aby pomniejszyć lub rozłożyć palce, aby powiększyć.
- Za pomocą ekranu dotykowego naciśnij dwukrotnie mapę, aby powiększyć jeden poziom.
- Gdy mapa jest skupiona, użyj`+`znaku Plus (`=`) lub znaku Równości ( ), aby powiększyć jeden poziom.
- Gdy mapa jest skupiona, użyj znaku`-`Minus, łącznika ( ) lub Podkreślenia (`_`), aby pomniejszyć jeden poziom.
- Korzystanie z kontrolki zoomu za pomocą klawiatury, klawiszy dotykowych lub klawiszy / klawiszy enter.
- Naciśnij i `Shift` przytrzymaj przycisk, a następnie naciśnij lewy przycisk myszy na mapie i przeciągnij, aby narysować obszar, aby powiększyć mapę.

**Przesuwanie mapy**

- Za pomocą myszy naciśnij lewy przycisk myszy na mapie i przeciągnij w dowolnym kierunku.
- Za pomocą ekranu dotykowego dotknij mapy i przeciągnij w dowolnym kierunku.
- Po skoncentrowaniu mapy użyj klawiszy strzałek, aby przenieść mapę.

**Obracanie mapy**

- Za pomocą myszy naciśnij prawy przycisk myszy na mapie i przeciągnij w lewo lub w prawo. 
- Za pomocą ekranu dotykowego dotknij mapy dwoma palcami i obróć.
- Po skoncentrowaniu mapy użyj klawisza shift oraz klawiszy strzałek w lewo lub w prawo.
- Korzystanie z kontroli obrotu za pomocą myszy, klawiszy lub klawiszy/ klawiszy enter.

**Rozbij mapę**

- Za pomocą myszy naciśnij prawy przycisk myszy na mapie i przeciągnij w górę lub w dół. 
- Za pomocą ekranu dotykowego dotknij mapy dwoma palcami i przeciągnij ją razem w górę lub w dół.
- Po skoncentrowaniu mapy użyj klawisza shift oraz klawiszy strzałek w górę lub w dół. 
- Korzystanie z kontroli skoku za pomocą myszy, klawiszy lub klawiszy / klawiszy enter.

## <a name="change-the-map-style"></a>Zmienianie stylu mapy

Nie wszyscy deweloperzy chcą, aby wszystkie możliwe style map były dostępne w ich aplikacji. Jeśli deweloper wyświetli kontrolkę selektora stylu mapy, użytkownik może zmienić styl mapy za pomocą myszy, dotyku lub klawiatury za pomocą klawisza tab lub enter. Deweloper może określić, które style mapy mają być dostępne w formancie selektora stylu mapy. Ponadto deweloper może programowo ustawić i zmienić styl mapy.

**Korzystanie z dużego kontrastu**

- Po załadowaniu formantu mapy sprawdza, czy wysoki kontrast jest włączony, a przeglądarka go obsługuje.
- Kontrolka mapy nie monitoruje trybu wysokiego kontrastu urządzenia. Jeśli tryb urządzenia ulegnie zmianie, mapa nie zostanie 2019 r. W związku z tym użytkownik będzie musiał ponownie załadować mapę, odświeżając stronę.
- Po wykryciu wysokiego kontrastu styl mapy automatycznie przełączy się na wysoki kontrast, a wszystkie wbudowane kontrolki będą używać stylu wysokiego kontrastu. Na przykład ZoomControl, PitchControl, CompassControl, StyleControl i inne wbudowane formanty użyją stylu wysokiego kontrastu.
- Istnieją dwa rodzaje wysokiego kontrastu, jasne i ciemne. Jeśli typ wysokiego kontrastu może zostać wykryty przez formanty mapy, zachowanie mapy zostanie odpowiednio dostosowane. Jeśli świeci, zostanie załadowany grayscale_light styl mapy. Jeśli nie można wykryć typu lub jest ciemny, zostanie załadowany styl high_contrast_dark.
- W przypadku tworzenia formantów niestandardowych, warto wiedzieć, czy wbudowane formanty używają stylu wysokiego kontrastu. Deweloperzy mogą dodać klasę css na div kontenera mapy, aby sprawdzić. Klasy css, które zostaną `high-contrast-dark` `high-contrast-light`dodane są i . Aby sprawdzić użycie języka JavaScript, użyj:

```javascript
map.getMapContainer().classList.contains("high-contrast-dark")
```

lub, użyj:

```javascript
map.getMapContainer().classList.contains("high-contrast-light")
```

## <a name="keyboard-shortcuts"></a>Skróty klawiaturowe

Mapa ma wbudowaną liczbę skrótów klawiaturowych, które ułatwiają korzystanie z mapy. Te skróty klawiaturowe działają, gdy mapa ma fokus.

| Klucz      | Akcja                            |
|----------|-----------------------------------|
| `Tab` | Nawiguj po formantych i wyskakujących okienkach na mapie. |
| `ESC` | Przenieś fokus z dowolnego elementu na mapie do elementu mapy najwyższego poziomu. |
| `Ctrl` + `Shift` + `D` | Przełącz poziom szczegółów czytnika ekranu.  |
| Klawisz ze strzałką w lewo | Przesuwanie mapy w lewo o 100 pikseli |
| Klawisz ze strzałką w prawo | Przesuwanie mapy w prawo o 100 pikseli |
| Klawisz strzałki w dół | Przesuwanie mapy w dół o 100 pikseli |
| Strzałka w górę | Przesuwanie mapy o 100 pikseli |
| `Shift`+ strzałka w górę | Zwiększenie skoku mapy o 10 stopni |
| `Shift`+ strzałka w dół | Zmniejsz wysokość mapy o 10 stopni |
| `Shift`+ strzałka w prawo | Obróć mapę o 15 stopni w kierunku zgodnym z ruchem wskazówek zegara |
| `Shift`+ strzałka w lewo | Obróć mapę o 15 stopni w kierunku przeciwnym do ruchu wskazówek zegara |
| Znak Plus`+`( <sup>*</sup>) lub`=`znak Równości ( ) | Powiększanie |
| Znak minus, łącznik`-`( <sup>*</sup>)`_`lub Podkreślenie ( ) | Pomniejszanie | 
| `Shift`+ przeciągnij myszą na mapie, aby narysować obszar | Powiększanie obszaru |

<sup>*</sup>Te skróty klawiszowe zwykle współużytkują ten sam klawisz na klawiaturze. Te skróty zostały dodane w celu poprawy środowiska użytkownika. Nie ma również znaczenia, czy użytkownik używa klawisza shift, czy nie dla tych skrótów.

## <a name="screen-reader-support"></a>Obsługa czytnika zawartości ekranu

Użytkownicy mogą poruszać się po mapie za pomocą klawiatury. Jeśli czytnik ekranu jest uruchomiony, mapa powiadomi użytkownika o zmianach w jego stanie. Na przykład użytkownicy są powiadamiani o zmianach mapy, gdy mapa jest przesuwana lub powiększana. Domyślnie mapa zawiera uproszczone opisy, które wykluczają poziom powiększenia i współrzędne środka mapy. Użytkownik może przełączać poziom szczegółowości tych opisów za `Ctrl`  +  `Shift`  +  `D`pomocą skrótu klawiatury .

Wszelkie dodatkowe informacje umieszczone na mapie bazowej powinny mieć odpowiednie informacje tekstowe dla użytkowników czytnika ekranu. W razie potrzeby należy dodać atrybuty ARIA , alt i [tytuł(2).](https://www.w3.org/WAI/standards-guidelines/aria/) 

## <a name="make-popups-keyboard-accessible"></a>Udostępnij klawiaturę wyskakujących okienka

Znacznik lub symbol jest często używany do reprezentowania lokalizacji na mapie. Dodatkowe informacje o lokalizacji są zazwyczaj wyświetlane w wyskakującym okienku, gdy użytkownik wchodzi w interakcję ze znacznikiem. W większości aplikacji wyskakujące okienka są wyświetlane, gdy użytkownik kliknie lub dotknie znacznika. Jednak kliknięcie i stuknięcie wymaga od użytkownika użycia myszy i ekranu dotykowego. Dobrą praktyką jest udostępnienie wyskakujących okienków podczas korzystania z klawiatury. Tę funkcję można osiągnąć, tworząc okno podręczne dla każdego punktu danych i dodając je do mapy. 

Poniższy przykład ładuje punkty zainteresowania na mapie za pomocą warstwy symboli i dodaje wyskakujące okienko do mapy dla każdego punktu zainteresowania. Odwołanie do każdego okna podręcznego jest przechowywane we właściwościach każdego punktu danych. Można go również pobrać dla znacznika, na przykład po kliknięciu znacznika. Po skupieniu się na mapie naciśnięcie klawisza tab pozwoli użytkownikowi przejść przez każde wyskakujące okienko na mapie.

<br/>

<iframe height='500' scrolling='no' title='Tworzenie aplikacji z ułatwieniami dostępu' src='//codepen.io/azuremaps/embed/ZoVyZQ/?height=504&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz Pióro <a href='https://codepen.io/azuremaps/pen/ZoVyZQ/'>Tworzenie aplikacji</a> dostępnych<a href='https://codepen.io/azuremaps'>@azuremaps</a>przez usługi Azure Maps ( ) na <a href='https://codepen.io'>funkcji CodePen</a>. </iframe>

<br/>

## <a name="additional-accessibility-tips"></a>Dodatkowe wskazówki dotyczące ułatwień dostępu

Oto kilka dodatkowych wskazówek, aby aplikacja mapowania sieci web była bardziej dostępna.

- Jeśli wyświetlasz wiele interaktywnych danych punktowych na mapie, należy rozważyć zmniejszenie bałaganu i użyć klastrowania. 
- Upewnij się, że współczynnik kontrastu kolorów między tekstem/symbolami a kolorami tła wynosi 4,5:1 lub więcej.
- Utrzymuj krótkie, opisowe i znaczące komunikaty czytnika ekranu (atrybuty ARIA, alt i tytuł). Unikaj niepotrzebnego żargonu i akronimów.
- Spróbuj zoptymalizować wiadomości wysyłane do czytnika ekranu, aby zapewnić krótkie istotne informacje, które są łatwe do strawienia przez użytkownika. Na przykład, jeśli chcesz zaktualizować czytnik ekranu z dużą częstotliwością, na przykład gdy mapa jest w ruchu, należy rozważyć wykonanie następujących punktów:
    - Poczekaj, aż mapa zakończy przenoszenie, aby zaktualizować czytnik ekranu.
    - Przepustnicy aktualizacji do raz na kilka sekund. 
    - Łączenie wiadomości w logiczny sposób. 
- Unikaj przekazywania informacji tylko za pomocą koloru. Użyj tekstu, ikon lub wzorów, aby uzupełnić lub zastąpić kolor. Niektóre zagadnienia:
    - Jeśli używa się warstwy bąbelkowej do pokazywania względnej wartości między punktami danych, należy rozważyć skalowanie promienia każdego bąbelka, kolorowanie bąbelka lub obu. 
    - Rozważ użycie warstwy symboli z różnymi ikonami dla różnych kategorii metrycznych, takich jak trójkąty, gwiazdki i kwadraty. Warstwa symboli obsługuje również skalowanie rozmiaru ikony. Można również wyświetlić etykietę tekstową.
    - W przypadku wyświetlania danych linii szerokość może służyć do reprezentowania wagi lub rozmiaru. Desenie tablicy kreskowej może służyć do reprezentowania różnych kategorii linii. Warstwa symbolu może być używana w połączeniu z linią, aby nałożyć ikony wzdłuż linii. Użycie ikony strzałki jest przydatne do wyświetlania przepływu lub kierunku linii.
    - W przypadku wyświetlania danych wielokątów wzorzec, taki jak paski, może być używany jako alternatywa dla koloru. 
- Niektóre wizualizacje, takie jak mapy cieplne, warstwy kafli i warstwy obrazów, nie są dostępne dla użytkowników z wadami wzroku. Niektóre zagadnienia:
    - Niech czytnik ekranu opisze, co warstwa jest wyświetlana po dodaniu do mapy. Jeśli na przykład zostanie wyświetlona warstwa kafli radaru pogodowego, czytnik ekranu powiedz "Dane radaru pogodowego są nakładane na mapę".
- Ogranicz ilość funkcji, które wymagają aktywowania wskaźnika myszy. Te funkcje będą niedostępne dla użytkowników, którzy korzystają z klawiatury lub urządzenia dotykowego do interakcji z aplikacją. Pamiętaj, że nadal dobrą praktyką jest styl aktywowania zawartości interaktywnej, takiej jak klikalne ikony, łącza i przyciski.
- Spróbuj nawigować po aplikacji za pomocą klawiatury. Upewnij się, że kolejność kart jest logiczna.
- Jeśli tworzysz skróty klawiaturowe, spróbuj ograniczyć go do dwóch klawiszy lub mniej. 

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o ułatwieniach dostępu w modułach SDK sieci Web.

> [!div class="nextstepaction"]
> [Ułatwienia dostępu do narzędzi do rysowania](drawing-tools-interactions-keyboard-shortcuts.md)

Dowiedz się więcej o tworzeniu aplikacji z ułatwieniami dostępu w programie Microsoft Learn:

> [!div class="nextstepaction"]
> [Ścieżka szkoleniowa digital badge w działaniu w działaniu](https://ready.azurewebsites.net/learning/track/2940)

Zapoznaj się z tymi przydatnymi narzędziami ułatwień dostępu:
> [!div class="nextstepaction"]
> [Tworzenie aplikacji z ułatwieniami dostępu](https://developer.microsoft.com/windows/accessible-apps)

> [!div class="nextstepaction"]
> [Przegląd WAI-ARIA](https://www.w3.org/WAI/standards-guidelines/aria/)

> [!div class="nextstepaction"]
> [Narzędzie do oceny ułatwień dostępu do sieci (WAVE)](https://wave.webaim.org/)

> [!div class="nextstepaction"]
> [Sprawdzanie kontrastu kolorów WebAim](https://webaim.org/resources/contrastchecker/)

> [!div class="nextstepaction"]
> [Brak symulatora wizyjnego kawy](https://chrome.google.com/webstore/detail/nocoffee/jjeeggmbnhckmgdhmgdckeigabjfbddl?hl=en-US)
