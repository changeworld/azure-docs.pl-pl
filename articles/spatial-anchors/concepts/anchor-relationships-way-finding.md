---
title: Relacje zakotwiczenia i sposób znalezienia w Azure przestrzenne kotwice | Dokumentacja firmy Microsoft
description: Więcej informacji na temat modelu koncepcyjnego za relacje zakotwiczenia. Dowiedz się, do łączenia z kotwice w obrębie przestrzeni i spełniają scenariusz, w sposób wyszukiwania za pomocą pobliskich interfejsu API.
author: ramonarguelles
manager: vicenterivera
services: azure-spatial-anchors
ms.author: ramonarguelles
ms.date: 02/24/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: 619cd051eccce3434469ae909f69496a254d0d9a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60565145"
---
# <a name="anchor-relationships-and-way-finding-in-azure-spatial-anchors"></a>Relacje zakotwiczenia i sposób znalezienia w kotwic przestrzenne platformy Azure

Korzystając z relacji zakotwiczenia można tworzyć kotwic połączonych w miejscu i następnie zadawać pytania, takie jak te:

* Czy istnieją kotwice w pobliżu?
* Jak daleko używają?

## <a name="examples"></a>Przykłady

W takich przypadkach można użyć kotwic połączone:

* Proces roboczy musi zakończyć zadanie, która obejmuje, odwiedzając różnych miejscach w fabryce przemysłowych. Fabryka ma kotwic przestrzennego w każdej lokalizacji. HoloLens lub aplikacji mobilnej pomaga przewodnik proces roboczy z jednej lokalizacji do następnego. Najpierw pyta, czy pobliskich kotwic przestrzenne i następnie zawiera informacje na temat procesu roboczego do następnej lokalizacji aplikacji. Aplikacja wizualnie przedstawia ogólne kierunku i odległość do następnej lokalizacji.

* Muzeów tworzy przestrzenne kotwic Wyświetla publicznych. Kotwice te tworzą razem godzinna Przewodnik po przykładzie muzeów essential Wyświetla publicznych. Obrazu publicznego osoby odwiedzające można otworzyć muzeów rzeczywistości mieszanej na swoim urządzeniu przenośnym. Następnie wskazują ich aparatu phone wokół miejsca, aby wyświetlić ogólne kierunku i odległość do innych publicznych wyświetla na samouczka. Użytkownik przegląda kierunku publicznego wyświetlania, aplikacja aktualizuje ogólne kierunku i odległość, aby ułatwić prowadzą użytkownika.

## <a name="set-up-way-finding"></a>Skonfiguruj sposób wyszukiwania

Używa aplikacji, która używa kierunek linii wzroku i odległość między kotwic wytyczne *sposób wyszukiwania*. Znajdowanie sposób różni się od Włącz, wyłącz nawigacji. W nawigacji Włącz, wyłącz, a użytkownicy są prowadzeni wokół ściany, za pośrednictwem drzwi oraz między podłogi. Sposób znalezienia uzyskuje wskazówek na temat ogólne kierunku docelowym użytkownik. Ale wnioskowania lub znajomości miejsce pomaga również użytkownika przejdź za pośrednictwem struktury do miejsca docelowego.

Aby utworzyć środowisko sposób wyszukiwania, przygotuj spację środowiska i opracowywanie aplikacji, którą użytkownicy będą korzystać z. Poniżej przedstawiono ogólne kroki:

1. **Planowanie przestrzeni**: Zdecyduj, lokalizacji, w których w przestrzeni będzie częścią środowiska sposób wyszukiwania. W naszym scenariuszach nadzorca fabryki lub koordynatora samouczek muzeów zdecydować o lokalizacji, w których do uwzględnienia w środowisku sposób wyszukiwania.
2. **Połącz kotwic**: Można znaleźć w wybranej lokalizacji, aby utworzyć przestrzenne zakotwiczenia. Można to zrobić w trybie administratora, użytkownik końcowy aplikacji lub w innej aplikacji całkowicie. Będziesz połączyć lub dotyczą każdego zakotwiczenia innym osobom. Usługa zapewnia te relacje.
3. **Uruchom środowisko użytkownika końcowego**: Użytkownicy uruchomią aplikację można zlokalizować elementu zakotwiczenia, który może być w dowolnej z wybranej lokalizacji. Ogólny projekt, należy określić lokalizacje, w którym użytkownicy mogą wprowadzać doświadczenia.
4. **Znajdź w pobliżu kotwic**: Po użytkownik znajdzie kotwicy, w pobliżu kotwice zażądać aplikacji. Ta procedura zwraca ułożenie wiadomości pomiędzy urządzeniem a te zakotwiczenia.
5. **Przewodnik po użytkownik**: Aplikacja może używać ułożenia do każdego z tych kotwic, aby zapewnić wskazówki dotyczące użytkownika ogólne kierunku i odległość. Na przykład aparatu, źródła danych w aplikacji może wyświetlać ikonę i strzałkę, aby reprezentować każdej potencjalnej lokalizacji docelowej, jak przedstawiono na poniższym obrazie.
6. **Uściślij wskazówki**: Jako użytkownik przegląda, aplikacja okresowo obliczyć nowe ułożenie wiadomości pomiędzy urządzeniem a zakotwiczenia docelowego. Aplikacja będzie nadal uściślić wskazówkami wskazówki, które pomagają użytkownikowi pojawić się w lokalizacji docelowej.

    ![Przykładowy sposób aplikację można wyświetlić sposób wyszukiwania wskazówki](./media/meeting-spot.png)

## <a name="connect-anchors"></a>Łączenia zakotwiczeń

Aby utworzyć środowisko sposób wyszukiwania, należy najpierw umieścić kotwic w wybranej lokalizacji. W tej sekcji przyjęto założenie, że administrator aplikacji zostało już zakończone tę pracę.

### <a name="connect-anchors-in-a-single-session"></a>Połącz kotwice w jednej sesji

Aby połączyć kotwic:

1. Zapoznaj się do pierwszej lokalizacji, a następnie utwórz zakotwiczenia A za pomocą CloudSpatialAnchorSession.
2. Zapoznaj się z innej lokalizacji. Podstawowej platformy MR/AR śledzi przepływu.
3. Tworzenie B zakotwiczenia przy użyciu tego samego CloudSpatialAnchorSession. Kotwice A i B są teraz połączone. Usługa przestrzenne kotwic utrzymuje tę relację.
4. Kontynuuj tę procedurę dla pozostałych zakotwiczenia.

### <a name="connect-anchors-in-multiple-sessions"></a>Połącz kotwice w wielu sesjach

Możesz połączyć przestrzenne kotwic na wiele sesji. Przy użyciu tej metody, można utworzyć i jednocześnie połączyć kilka kotwic i później utworzyć i połączyć więcej zakotwiczenia. 

Połączenie kotwic wiele sesji:

1. Aplikacja tworzy kilka kotwice w jednym CloudSpatialAnchorSession. 
2. W innym czasie aplikacja lokalizuje jedną z tych kotwice (na przykład element zakotwiczenia) przy użyciu nowego CloudSpatialAnchorSession.
3. Zapoznaj się do nowej lokalizacji. Podstawowej platformy rzeczywistości mieszanej lub rzeczywistości rozszerzonej śledzi przepływu.
4. Utwórz zakotwiczenia C przy użyciu tego samego CloudSpatialAnchorSession. Kotwice A, B i C są teraz połączone. Usługa przestrzenne kotwic utrzymuje tę relację.

Możesz kontynuować tę procedurę dla więcej zakotwiczenia i więcej sesji, wraz z upływem czasu.

### <a name="verify-anchor-connections"></a>Sprawdź połączenia zakotwiczenia

Aplikacja może Sprawdź, czy dwie kotwice połączenie wysyłając kwerendy do pobliskich kotwic. Jeśli wynik zapytania zawiera zakotwiczenia docelowego, zakotwiczenia zweryfikowaniu połączenia kliknij. Jeśli kotwic nie są połączone, aplikacja spróbować połączyć je ponownie. 

Oto kilka powodów, dlaczego kotwic może zakończyć się niepowodzeniem:

* Podstawowej rzeczywistości mieszanej lub rzeczywistości rozszerzonej platformy utraty śledzenia w trakcie procesu łączenia zakotwiczeń.
* Ze względu na błąd sieciowy podczas komunikacji z usługą kotwic przestrzennych nie utrwalone połączenia zakotwiczenia.

### <a name="find-sample-code"></a>Znajdź przykładowy kod

Przykładowy kod, który pokazuje, jak połączyć kotwic oraz wykonywanie zapytań w pobliżu, możesz znaleźć [przestrzenne kotwic przykładowe aplikacje](https://github.com/Azure/azure-spatial-anchors-samples).
