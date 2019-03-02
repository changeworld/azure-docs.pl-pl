---
title: Relacje zakotwiczenia i sposób znalezienia w Azure przestrzenne kotwice | Dokumentacja firmy Microsoft
description: Opis modelu koncepcyjnego za relacje zakotwiczenia. Opisano proces łączenia zakotwiczeń w ramach spację, a proces przy użyciu pobliskich interfejsu API, aby zrealizować scenariusz, w sposób wyszukiwania. Po wyjaśniających modelu koncepcyjnego, wskaż deweloperom na naszych przykładowych aplikacji, które obsługują blisko siebie, więc można rozpocząć pracę, realizacji tego scenariusza w ich własnych aplikacjach.
author: ramonarguelles
manager: vicenterivera
services: azure-spatial-anchors
ms.author: ramonarguelles
ms.date: 02/24/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: 3d1ee0b25fbbf0ef895bdf6ff8afad71ff82de25
ms.sourcegitcommit: c712cb5c80bed4b5801be214788770b66bf7a009
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/01/2019
ms.locfileid: "57217176"
---
# <a name="anchor-relationships-and-way-finding-in-azure-spatial-anchors"></a>Relacje zakotwiczenia i sposób znalezienia w kotwic przestrzenne platformy Azure

Relacje zakotwiczenia umożliwiają tworzyć kotwic połączonych w miejscu, a następnie poproś pytania o nich, takich jak:

* Czy istnieją kotwice w pobliżu?
* Jak daleko używają?

## <a name="examples"></a>Przykłady

Przykładowe — przypadki użycia, które można włączyć za pomocą połączonych kotwic obejmują:

1. Proces roboczy musi przeprowadzić procedurę, która obejmuje, odwiedzając różnych miejscach w fabryce przemysłowe. Fabryka umieścił przestrzenne kotwice w każdej lokacji zaangażowanych w procedurze. HoloLens lub aplikacji mobilnej pomaga przewodnik proces roboczy z jednej lokalizacji do następnego. Będzie on najpierw poprosić o kotwic przestrzennych, pobliskich, a następnie przeprowadzają procesu roboczego do następnej lokalizacji. Aplikacja wyświetla visual wskaźniki — informacje ogólne kierunku i odległość do następnej lokalizacji do ukończenia zadania.

2. Muzeów tworzy przestrzenne kotwic Wyświetla publicznych, tworzonych określonych samouczek za pośrednictwem muzeów, takie jak "Samouczek godzinna essential zawiera publiczny". W przypadku gości u jednego ekranu publicznych, mogą otwierać muzeów rzeczywistości mieszanej na swoim urządzeniu przenośnym. Następnie one telefon punktu, wokół miejsca oraz za pośrednictwem kanału informacyjnego aparatu i zobacz ogólne kierunku i odległość do innych publicznych wyświetla na samouczka. Użytkownik uruchamia przeprowadzenie kierunku jeden publiczny Wyświetla, aplikacja stopniowo aktualizuje ogólne kierunku i odległość, aby ułatwić użytkownikom Przewodnik ma.

## <a name="way-finding"></a>Sposób wyszukiwania

Wyobraź sobie, że aplikacja używa kierunek "linii wzroku" i odległość między kotwic zapewnienie wskazówek wskazówki dla użytkowników. Nazywamy tego scenariusza ogólny sposób wyszukiwania. Należy pamiętać, że znajdowanie sposób różni się od Włącz, wyłącz nawigacji. W nawigacji Włącz, wyłącz, a użytkownicy są prowadzeni wokół ściany, za pośrednictwem drzwi oraz między podłogi. W sposób znalezienia użytkownika są dostarczane wskazówek na temat ogólne kierunku miejsca docelowego. Ale wnioskowania użytkownika lub znajomości miejsce pomaga również do nawigowania struktury do miejsca docelowego.

Tworzenie środowiska wyszukiwania sposób polega na przygotowania miejsca na środowisko i opracowywania aplikacji, które użytkownicy końcowi będą wchodzić w interakcje z. Ogólne etapy obejmują:

1. Planowanie miejsce: Określ lokalizacje w obrębie przestrzeni, które uczestniczą w środowisku sposób wyszukiwania. W wcześniejszych przykładów to działanie może być przeprowadzana przez nadzorca fabryki lub koordynatora samouczek muzeów.
2. Łączenie kotwic: Ktoś odwiedza wybranej lokalizacji i tworzy istnieją kotwice przestrzennych. To zadanie można przeprowadzić wyłącznie do trybu administratora aplikacji użytkownika końcowego lub innej aplikacji. Przez ten proces kotwicę każdego jest połączona lub związane z innym osobom. Te relacje są obsługiwane w usłudze.
3. Uruchamianie środowiska użytkownika końcowego: Pierwszym krokiem dla użytkowników końcowych jest zlokalizować jednego kotwic przy użyciu aplikacji, która może znajdować się w jednym z wybranej lokalizacji. Określanie lokalizacji, w którym użytkownicy końcowi mogą wprowadzać środowisko jest częścią ogólnego środowiska projektowania.
4. Wyszukiwanie w pobliżu kotwic: Po użytkownik znajduje się na jednej kotwicy, w pobliżu kotwic zażądać aplikacji. Ta procedura zwraca ułożenie wiadomości pomiędzy urządzeniem a te zakotwiczenia.
5. Przeprowadzi użytkownika: Aplikacja korzystać z zalet ułożenie do każdego z tych kotwic do renderowania wskazówek przydatne wskazówki na temat swoje ogólne kierunku i odległość. Na przykład, może to być ikony i strzałkę na aparat źródła danych w aplikacji mobilnej, reprezentujący każdej potencjalnej lokalizacji docelowej, tak jak na poniższej ilustracji.
6. Rafinacja wskazówek: Jako użytkownik przegląda, aplikacja okresowo obliczyć nowe ułożenie wiadomości pomiędzy urządzeniem a zakotwiczenia docelowego. Aplikacja będzie nadal uściślić wskazówkami wskazówki, które pomagają użytkownikowi pojawić się w lokalizacji docelowej.

![Miejsce spotkania](./media/meeting-spot.png)

## <a name="connecting-anchors"></a>Łączenia zakotwiczeń

Aby utworzyć środowisko sposób wyszukiwania, trzeba umieścić kotwic połączonych w wybranej lokalizacji. Poniżej przyjmiemy, że ta operacja jest wykonywana przez administratora aplikacji.

### <a name="connecting-anchors-in-a-single-session"></a>Łączenie kotwice w jednej sesji

Etapy łączenia zakotwiczeń są:

1. Administrator przeprowadzający do pierwszej lokalizacji i tworzy zakotwiczenia A za pomocą CloudSpatialAnchorSession.
2. Administrator opisano w drugiej lokalizacji nadal podstawowej platformy MR/AR w celu śledzenia użytkowników.
3. Administrator tworzy zakotwiczenia B z tego samego CloudSpatialAnchorSession. Kotwice A i B są teraz połączone i ta relacja jest obsługiwana przez usługę Azure przestrzenne kotwice.
4. Kontynuuj tę procedurę dla wszystkich kotwic, które chcesz się połączyć.

### <a name="multiple-sessions"></a>Wiele sesji

Można też połączyć przestrzenne kotwic na wiele sesji. Ta metoda umożliwia tworzenie i łączenie niektórych kotwice w tym samym czasie i później utworzyć i połączyć więcej kotwic. Do łączenia zakotwiczeń z wielu sesji:

1. Aplikacja tworzy kilka kotwice w jednym CloudSpatialAnchorSession.
2. Później na przykład na inny dzień aplikacji lokalizuje jedną z tych kotwic za pomocą nowego CloudSpatialAnchorSession (na przykład element zakotwiczenia).
3. Użytkownik przegląda do nowej lokalizacji, nadal podstawowej platformy MR/AR w celu śledzenia użytkowników.
4. Przy użyciu tego samego CloudSpatialAnchorSession, użytkownik tworzy kotwic C. zakotwiczenia A, B i C są połączone i ta relacja jest obsługiwana przez kotwic przestrzenne platformy Azure.
5. Możesz kontynuować tę procedurę dla więcej zakotwiczenia i więcej sesji, wraz z upływem czasu.

### <a name="verifying-anchor-connections"></a>Weryfikowanie połączeń zakotwiczenia

Aplikacja może Sprawdź, czy dwie kotwice połączenie wysyłając kwerendy do pobliskich kotwic. Jeśli wynik zapytania zawiera zakotwiczenia wybraną docelową, aplikacja ma potwierdzenia, że kotwic są połączone. Jeśli nie są one połączone, aplikacja ponowić procedury połączenie ponownie. Oto kilka powodów, dlaczego kotwic może zakończyć się niepowodzeniem:

1. Podstawowy MR/AR obiekt śledzący utraty śledzenia w trakcie procesu łączenia zakotwiczeń.
2. Wystąpił błąd sieci podczas komunikacji z usługą Azure przestrzenne kotwic i nie zostać utrwalone połączenia zakotwiczenia.

### <a name="sample-code"></a>Przykładowy kod

Widać przykładowy kod, który pokazuje, jak połączyć kotwic i wykonaj zapytania w pobliżu. Zapoznaj się [kotwic przestrzenne Azure przykładowe aplikacje](https://github.com/Azure/azure-spatial-anchors-samples) w witrynie GitHub.
