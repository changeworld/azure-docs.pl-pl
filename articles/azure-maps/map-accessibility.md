---
title: Tworzenie dostępnej aplikacji z Azure Maps | Microsoft Docs
description: Jak skompilować dostępną aplikację przy użyciu Azure Maps
services: azure-maps
author: rbrundritt
ms.author: richbrun
ms.date: 12/10/2019
ms.topic: conceptual
ms.service: azure-maps
manager: cpendleton
ms.openlocfilehash: 4d997bcb5bbbb66a06bea998577f8163910afce8
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/31/2019
ms.locfileid: "75561307"
---
# <a name="building-an-accessible-application"></a>Tworzenie dostępnej aplikacji

Maksymalnie 20% użytkowników Internetu jest potrzebnych dla dostępnych aplikacji sieci Web. W związku z tym ważne jest, aby upewnić się, że aplikacja została zaprojektowana tak, aby każdy użytkownik mógł ją łatwo wykorzystać. Nie trzeba traktować ich jako zestawu zadań do ukończenia, ponieważ jest to część ogólnego środowiska użytkownika. Im bardziej dostępna aplikacja, tym więcej osób, które mogą z niej korzystać. 

Gdy zestaw zawiera rozbudowaną zawartość interaktywną, taką jak mapa, niektóre typowe zagadnienia dotyczące ułatwień dostępu są następujące:
- Obsługa czytnika ekranu dla użytkowników, którzy mają trudności z oglądaniem aplikacji sieci Web.
- Mają wiele metod współpracy z aplikacją sieci Web i nawigowaniem w niej, takich jak mysz, dotyk i klawiatura.
- Upewnij się, że kolory nie mieszają się ze sobą i trudno się odróżnić od siebie. 

Zestaw SDK sieci Web Azure Maps zawiera prekompilowaną z wieloma funkcjami ułatwień dostępu, takimi jak:
- Opisy czytnika ekranu, gdy mapa jest przenoszona i gdy użytkownik koncentruje się na kontrolce lub wyskakującym okienku.
- Obsługa myszy, dotyku i klawiatury.
- Obsługa kontrastu kolorów dostępna w stylu mapy drogowej.

Szczegółowe informacje o zgodności z pełnymi ułatwieniami dostępu dla wszystkich produktów firmy Microsoft można znaleźć [tutaj](https://cloudblogs.microsoft.com/industry-blog/government/2018/09/11/accessibility-conformance-reports/). Wyszukaj ciąg "Azure Maps Web", aby znaleźć dokument przeznaczony dla Azure Maps Web SDK. 

## <a name="navigating-the-map"></a>Nawigowanie po mapie
Istnieje kilka różnych sposobów, w których mapowanie może być powiększane, przesuwane, obracane i mierzone. Poniżej przedstawiono szczegółowe informacje na temat różnych sposobów nawigowania po mapie.

**Powiększ mapę**
- Za pomocą myszy kliknij dwukrotnie mapę, aby powiększyć na jednym poziomie.
- Za pomocą myszy przewiń kółko, aby powiększyć mapę.
- Korzystając z ekranu dotykowego, dotknij mapy dwoma palcami i szczypania ze sobą, aby pomniejszyć lub rozłożyć palcami od siebie do powiększania.
- Korzystając z ekranu dotykowego, dwukrotnie naciśnij mapę, aby powiększyć na jednym poziomie.
- Używając mapy, użyj znaku plusa (`+`) lub znaku równości (`=`), aby powiększyć jeden poziom.
- Używając mapy, użyj znaku minus, łącznika (`-`) lub podkreślenia (`_`), aby pomniejszyć jeden poziom.
- Za pomocą kontrolki powiększenie za pomocą myszy, dotyku lub klawisza Tab/klawisz ENTER.
- Naciśnij i przytrzymaj przycisk `Shift` i naciśnij lewy przycisk myszy w dół mapy i przeciągnij, aby narysować obszar, w którym chcesz powiększyć mapę.

**Panoramowanie mapy**
- Korzystając z myszy, naciśnij przycisk myszy w dół na mapie i przeciągnij w dowolnym kierunku.
- Korzystając z ekranu dotykowego, dotknij mapy i przeciągnij w dowolnym kierunku.
- Korzystając z mapy, użyj klawiszy strzałek, aby przenieść mapę.

**Obróć mapę**
- Za pomocą myszy naciśnij prawy przycisk myszy na mapie i przeciągnij w lewo lub w prawo. 
- Korzystając z ekranu dotykowego, dotknij mapy dwoma palcami i Obróć.
- Korzystając z mapy, użyj klawisza Shift i klawiszy strzałek w lewo lub w prawo.
- Za pomocą kontrolki rotacji z klawiszami myszy, dotyku lub klawiatury/ENTER.

**Wysokość mapy**
- Za pomocą myszy naciśnij prawy przycisk myszy na mapie i przeciągnij w górę lub w dół. 
- Korzystając z ekranu dotykowego, dotknij mapy dwoma palcami i przeciągnij je w górę lub w dół.
- Korzystając z mapy, użyj klawisza Shift oraz klawiszy strzałek w górę lub w dół. 
- Za pomocą kontrolki gęstość z klawiszem myszy, dotykiem lub klawiszem Tab/ENTER.

**Zmiana stylu mapy** Nie wszyscy deweloperzy będą chcieć, aby wszystkie możliwe style mapy były dostępne w swojej aplikacji. Deweloper może programowo ustawiać i zmieniać styl mapy zgodnie z potrzebami. Jeśli deweloper wyświetli kontrolkę selektor stylu mapy, użytkownik będzie mógł zmienić styl mapy za pomocą myszy, dotyku lub klawiatury przy użyciu klawiszy Tab/ENTER. Deweloper może określić, które style mapy mają być dostępne w kontrolce selektor stylów mapy. 

## <a name="keyboard-shortcuts"></a>Skróty klawiaturowe

Mapa zawiera wbudowane skróty klawiaturowe, które ułatwiają korzystanie z mapy. Te skróty klawiaturowe działają, gdy mapa ma fokus.

| Klucz      | Działanie                            |
|----------|-----------------------------------|
| `Tab` | Nawigowanie między kontrolkami i okienkami podręcznymi na mapie. |
| `ESC` | Przenieś fokus z dowolnego elementu na mapie do elementu mapy najwyższego poziomu. |
| `Ctrl` + `Shift` + `D` | Przełącz poziom szczegółów czytnika ekranu.  |
| Klawisz ze strzałką w lewo | Przesuń mapę w lewo o 100 pikseli |
| Klawisz ze strzałką w prawo | Przesuń mapę w prawo 100 pikseli |
| Klawisz Strzałka w dół | Przesuń mapę w dół o 100 pikseli |
| Klawisz Strzałka w górę | Przesuń mapę w górę o 100 pikseli |
| `Shift` + Strzałka w górę | Zwiększ wysokość mapy o 10 stopni |
| `Shift` + Strzałka w dół | Zmniejsz wysokość mapy o 10 stopni |
| `Shift` + Strzałka w prawo | Obróć mapę o 15 stopni w prawo |
| `Shift` + Strzałka w lewo | Obróć mapę o 15 stopni w lewo |
| Znak plus (`+`) <sup>*</sup>lub znak równości (`=`) | Powiększenie |
| Znak minus, łącznik (`-`) lub podkreślenie <sup>*</sup>(`_`) | Pomniejszenie | 
| `Shift` i przeciągnij myszą na mapie, aby rysować obszar | Powiększ do obszaru |

<sup>*</sup> Te kluczowe skróty zazwyczaj współdzielą ten sam klucz na klawiaturze. Dodano je w celu usprawnienia środowiska użytkownika, tak że nie ma znaczenia, czy użytkownik korzysta z klawisza Shift, czy nie dla tych skrótów.

## <a name="screen-reader-support"></a>Obsługa czytnika ekranu

Użytkownicy mogą nawigować po mapie przy użyciu klawiatury. Jeśli czytnik ekranu jest uruchomiony, mapa będzie powiadamiać użytkownika o zmianach stanu. Na przykład użytkownicy są powiadamiani o zmianach mapy, gdy mapa jest przesuwana lub powiększa. Domyślnie mapa zawiera uproszczone opisy, które wykluczają poziom powiększenia i współrzędne centrum mapy. Użytkownik może przełączać poziom szczegółowości tych opisów przy użyciu krótkiego wycinania klawiatury `Ctrl` + `Shift` + `D`.

Wszelkie dodatkowe informacje umieszczane na mapie podstawowej powinny mieć odpowiednie informacje tekstowe dla użytkowników czytnika ekranu. Należy pamiętać o dodaniu [dostępnych bogatych aplikacji internetowych (Aria)](https://www.w3.org/WAI/standards-guidelines/aria/), klawiszy Alt i atrybutów tytułu, tam gdzie to konieczne. 

## <a name="make-popups-keyboard-accessible"></a>Zapewnianie dostępu do klawiatury podręcznej

Znacznik lub symbol jest często używany do reprezentowania lokalizacji na mapie. Dodatkowe informacje o lokalizacji są zwykle wyświetlane w oknie podręcznym, gdy użytkownik współdziała ze znacznikiem. W przypadku większości aplikacji okna podręczne są wyświetlane, gdy użytkownik kliknie lub naciśnie znacznik, ale wymaga, aby użytkownik korzystał z myszy lub ekranu dotykowego. Dobrym sposobem jest udostępnienie okien podręcznych w przypadku korzystania z klawiatury. Można to osiągnąć przez utworzenie okna podręcznego dla każdego punktu danych i dodanie go do mapy. 

Poniższy przykład ładuje punkty zainteresowań na mapie przy użyciu warstwy symboli i dodaje podręczne do mapy dla każdego punktu zainteresowania. Odwołanie do każdego okna podręcznego jest przechowywane we właściwościach każdego punktu danych, dzięki czemu można go również pobrać dla znacznika, na przykład po kliknięciu znacznika. Po skoncentrowaniu się na mapie naciśnięcie klawisza Tab umożliwi użytkownikowi przechodzenie przez poszczególne okienka podręczne na mapie.

<br/>

<iframe height='500' scrolling='no' title='Utwórz dostępną aplikację' src='//codepen.io/azuremaps/embed/ZoVyZQ/?height=504&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióro <a href='https://codepen.io/azuremaps/pen/ZoVyZQ/'>Udostępnij dostępną aplikację</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>. </iframe>

<br/>

## <a name="additional-accessibility-tips"></a>Dodatkowe wskazówki dotyczące ułatwień dostępu

Poniżej znajdują się dodatkowe porady ułatwiające dostęp do aplikacji mapowania sieci Web.

- W przypadku wyświetlania dużej ilości danych punktów na mapie należy rozważyć zmniejszenie bałaganu i użycie klastrowania. 
- Upewnij się, że współczynnik kontrastu kolorów między tekstem i symbolami i kolorami tła to 4.5:1 lub więcej.
- Należy zachować krótką, opisową i zrozumiałą komunikaty o atrybutach ARIA, Alt i title. Unikaj zbędnych żargon i akronimów.
- Spróbuj zoptymalizować komunikaty wysyłane do czytnika ekranu, aby zapewnić zwięzłe informacje, które są łatwe do zamieszania przez użytkownika. Na przykład jeśli chcesz zaktualizować czytnik ekranu z dużą częstotliwością, np. podczas przesuwania mapy, rozważ wykonanie następujących czynności:
    - Zaczekaj na zakończenie przesuwania mapy w celu zaktualizowania czytnika ekranu.
    - Ogranicz aktualizacje do co kilka sekund. 
    - Łączenie komunikatów w sposób logiczny. 
- Unikaj przekazywania informacji tylko za pomocą koloru. Użyj tekstu, ikon lub wzorców, aby uzupełnić lub zastąpić kolor. Niektóre zagadnienia:
    - Jeśli używasz warstwy bąbelkowej do wyświetlania wartości względnej między punktami danych, rozważ przeskalowanie promienia każdego bąbelka, a także alternatywę do kolorowania. 
    - Rozważ użycie warstwy symbol z różnymi ikonami dla różnych kategorii metryk, takich jak trójkąty, gwiazdki i kwadraty. Warstwa symboli obsługuje również skalowanie rozmiaru ikony. Etykieta tekstowa może również być wyświetlana.
    - W przypadku wyświetlania danych liniowych szerokość może być używana do reprezentowania wagi lub rozmiaru. Wzorzec tablic kreskowych może służyć do reprezentowania różnych kategorii linii. Warstwa symboli może być używana w połączeniu z linią do nakładania ikon wzdłuż linii. Użycie ikony strzałki jest przydatne do wyświetlania przepływu lub kierunku linii.
    - W przypadku wyświetlania danych wielokątów wzorzec, taki jak paski, może być używany jako alternatywa dla koloru. 
- Niektóre wizualizacje, takie jak map cieplnych, warstwy kafelków i warstwy obrazów, nie są dostępne dla użytkowników z upośledzeniem programu Vision. Niektóre zagadnienia:
    - Zapoznaj się z czytnikiem ekranu opisującym, co warstwa jest wyświetlana po dodaniu do mapy. Na przykład, jeśli zostanie wyświetlona warstwa kafelków radarowych, czytniki ekranu mówią, jak "dane radaru Pogoda na mapie".
- Ogranicz liczbę funkcji, które wymagają przesuwania wskaźnika myszy. Nie będą one dostępne dla użytkowników korzystających z klawiatury lub urządzenia dotykowego do korzystania z aplikacji. Należy pamiętać, że nadal dobrym sposobem jest posiadanie stylu aktywowania zawartości interaktywnej, takiej jak ikony klikania, linki i przyciski.
- Spróbuj przejść do aplikacji przy użyciu klawiatury. Upewnij się, że kolejność tabulacji jest logiczna.
- Jeśli tworzysz skróty klawiaturowe, spróbuj ograniczyć je do dwóch kluczy lub mniej. 

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o ułatwieniach dostępu w modułach zestawu SDK sieci Web.

> [!div class="nextstepaction"]
> [Ułatwienia dostępu narzędzi do rysowania](drawing-tools-interactions-keyboard-shortcuts.md)

Dowiedz się więcej o projektowaniu dostępnych aplikacji przy użyciu Microsoft Learn:

> [!div class="nextstepaction"]
> [Dostępność w obszarze działania ścieżka uczenia cyfrowego](https://ready.azurewebsites.net/learning/track/2940)

Zapoznaj się z tymi przydatnymi narzędziami ułatwień dostępu:
> [!div class="nextstepaction"]
> [Tworzenie aplikacji z ułatwieniami dostępu](https://developer.microsoft.com/windows/accessible-apps)

> [!div class="nextstepaction"]
> [POCZEKA — Omówienie ARIA](https://www.w3.org/WAI/standards-guidelines/aria/)

> [!div class="nextstepaction"]
> [Narzędzie do oceny ułatwień dostępu w sieci Web (WAVE)](https://wave.webaim.org/)

> [!div class="nextstepaction"]
> [WebAim — Narzędzie sprawdzania kontrastu kolorów](https://webaim.org/resources/contrastchecker/)

> [!div class="nextstepaction"]
> [Brak symulatora z obsługą kawy](https://chrome.google.com/webstore/detail/nocoffee/jjeeggmbnhckmgdhmgdckeigabjfbddl?hl=en-US)
