---
title: Relacje zakotwiczenia i sposób znajdowania
description: Dowiedz się więcej o modelu koncepcyjnym stojącym za relacjami kotwicy. Dowiedz się, aby połączyć kotwice w przestrzeni i użyć interfejsu API w pobliżu, aby spełnić scenariusz znajdowania sposobu.
author: ramonarguelles
manager: vriveras
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/24/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: f2fd8f4b7d03be8822c3ec12e2be589054942ce3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74270603"
---
# <a name="anchor-relationships-and-way-finding-in-azure-spatial-anchors"></a>Zakotwiczenie relacji i znajdowania sposobu w zakotwiczeniu przestrzennym platformy Azure

Za pomocą relacji zakotwiczenia można tworzyć połączone kotwice w przestrzeni, a następnie zadawać następujące pytania:

* Czy w pobliżu znajdują się kotwice?
* Jak daleko są?

## <a name="examples"></a>Przykłady

Podłączone kotwy można używać w takich przypadkach:

* Pracownik musi wykonać zadanie, które polega na odwiedzeniu różnych lokalizacji w fabryce przemysłowej. Fabryka ma kotwice przestrzenne w każdej lokalizacji. HoloLens lub aplikacja mobilna pomaga prowadzić pracownika z jednej lokalizacji do następnej. Aplikacja najpierw prosi o pobliskie kotwice przestrzenne, a następnie prowadzi pracownika do następnej lokalizacji. Aplikacja wizualnie pokazuje ogólny kierunek i odległość do następnej lokalizacji.

* Muzeum tworzy przestrzenne kotwice na wystawach publicznych. Razem, kotwice te tworzą jednogodzinną wycieczkę po najważniejszych publicznych wystawach muzeum. Na wystawie publicznej zwiedzający mogą otworzyć aplikację rzeczywistości mieszanej muzeum na swoim urządzeniu mobilnym. Następnie kierują kamerę telefonu wokół przestrzeni, aby zobaczyć ogólny kierunek i odległość do innych publicznych wyświetlaczy na trasie. Gdy użytkownik przechodzi w kierunku wyświetlania publicznego, aplikacja aktualizuje ogólny kierunek i odległość, aby ułatwić prowadzenie użytkownika.

## <a name="set-up-way-finding"></a>Konfigurowanie znajdowania sposobu

Aplikacja, która używa kierunku linii wzroku i odległości między kotwicami w celu zapewnienia wskazówek, korzysta ze *sposobu znajdowania.* Sposób znajdowania różni się od nawigacji zakręt po kroku. W nawigacji "zakręt po kolei" użytkownicy są prowadzeni po ścianach, drzwiach i między piętrami. Dzięki znalezieniu sposobu użytkownik otrzymuje wskazówki dotyczące ogólnego kierunku miejsca docelowego. Ale wnioskowanie lub znajomość przestrzeni pomaga również użytkownikowi poruszać się po strukturze do miejsca docelowego.

Aby utworzyć środowisko znajdowania sposobów, najpierw przygotuj miejsce na środowisko i opracuj aplikację, z którą użytkownicy będą wchodzić w interakcje. Są to kroki koncepcyjne:

1. **Zaplanuj przestrzeń**: Zdecyduj, które lokalizacje w przestrzeni będą częścią sposobu znajdowania. W naszych scenariuszach kierownik fabryki lub koordynator wycieczki do muzeum może zdecydować, które lokalizacje należy uwzględnić w doświadczeniu w znajdowaniu sposobu.
2. **Połącz kotwice**: Odwiedź wybrane lokalizacje, aby utworzyć kotwice przestrzenne. Można to zrobić w trybie administracyjnym aplikacji użytkownika końcowego lub w zupełnie innej aplikacji. Połączysz lub powiąkszysz każdą kotwicę z innymi. Usługa utrzymuje te relacje.
3. **Uruchom środowisko użytkownika końcowego:** Użytkownicy uruchamiają aplikację, aby zlokalizować kotwicę, która może znajdować się w dowolnej z wybranych lokalizacji. Ogólny projekt powinien określać lokalizacje, w których użytkownicy mogą wprowadzić środowisko.
4. **Znajdź pobliskie kotwice**: Gdy użytkownik znajdzie kotwicę, aplikacja może zażądać pobliskich kotwic. Ta procedura zwraca pozę między urządzeniem a tymi kotwicami.
5. **Przewodnik użytkownika**: Aplikacja może użyć pozy do każdej z tych kotwic, aby udzielić wskazówek dotyczących ogólnego kierunku i odległości użytkownika. Na przykład plik danych z kamery w aplikacji może wyświetlać ikonę i strzałkę reprezentującą każde potencjalne miejsce docelowe, jak pokazano na poniższej ilustracji.
6. **Uściślanie wskazówek**: Podczas prowadzania przez użytkownika aplikacja może okresowo obliczać nową pozy między urządzeniem a kotwicą docelową. Aplikacja w dalszym ciągu uściślać wskazówki, które pomagają użytkownikowi dotrzeć do miejsca docelowego.

    ![Przykład sposobu, w jaki aplikacja może wyświetlać wskazówki dotyczące znajdowania sposobów](./media/meeting-spot.png)

## <a name="connect-anchors"></a>Łączenie kotew

Aby stworzyć sposób znajdowania, należy najpierw umieścić kotwice w wybranych miejscach. W tej sekcji zakładamy, że administrator aplikacji zakończył już tę pracę.

### <a name="connect-anchors-in-a-single-session"></a>Łączenie kotwic w jednej sesji

Aby podłączyć kotwice, należy:

1. Przejdź do pierwszej lokalizacji i utwórz kotwicę A za pomocą CloudSpatialAnchorSession.
2. Idź do drugiej lokalizacji. Podstawowa platforma MR/AR śledzi ruch.
3. Utwórz kotwicę B przy użyciu tego samego CloudSpatialAnchorSession. Kotwy A i B są teraz połączone. Usługa Zakotwiczenia przestrzenne utrzymuje tę relację.
4. Kontynuuj procedurę dla pozostałych kotew.

### <a name="connect-anchors-in-multiple-sessions"></a>Łączenie kotwic w wielu sesjach

Zakotwiczenia przestrzenne można łączyć w wielu sesjach. Za pomocą tej metody można utworzyć i połączyć niektóre kotwice w tym czasie, a następnie później utworzyć i połączyć więcej zakotwiczeń.

Aby połączyć kotwice w wielu sesjach:

1. Aplikacja tworzy kilka kotwic w jednym CloudSpatialAnchorSession.
2. W innym czasie aplikacja lokalizuje jedną z tych kotwic (na przykład Anchor A) przy użyciu nowego CloudSpatialAnchorSession.
3. Spacer do nowej lokalizacji. Podstawowa platforma rzeczywistości mieszanej lub rozszerzonej śledzi ruch.
4. Utwórz kotwicę C przy użyciu tego samego CloudSpatialAnchorSession. Zakotwiczenia A, B i C są teraz połączone. Usługa Zakotwiczenia przestrzenne utrzymuje tę relację.

Tę procedurę można kontynuować, aby uzyskać więcej zakotwiczenia i więcej sesji w czasie.

### <a name="verify-anchor-connections"></a>Weryfikowanie połączeń kotwicy

Aplikacja może sprawdzić, czy dwie kotwice są połączone, wystawiając zapytanie dotyczące pobliskich zakotwiczeń. Gdy wynik kwerendy zawiera zakotwiczenie docelowe, połączenie zakotwiczenia jest weryfikowane. Jeśli kotwice nie są połączone, aplikacja może spróbować połączyć je ponownie.

Oto kilka powodów, dla których zakotwiczenia mogą nie łączyć się z:

* Podstawowa platforma rzeczywistości mieszanej lub rozszerzonej straciła śledzenie podczas procesu łączenia kotwic.
* Z powodu błędu sieci podczas komunikacji z usługą Zakotwiczenia przestrzenne nie można utrwalić połączenia zakotwiczenia.

### <a name="find-sample-code"></a>Znajdź przykładowy kod

Aby znaleźć przykładowy kod, który pokazuje, jak łączyć kotwice i wykonywać pobliskie kwerendy, zobacz [Przykładowe aplikacje zakotwiczenia przestrzenne](https://github.com/Azure/azure-spatial-anchors-samples).
