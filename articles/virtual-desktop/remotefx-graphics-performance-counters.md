---
title: Diagnozowanie problemów z wydajnością grafiki w pulpitu zdalnego - Azure
description: W tym artykule opisano sposób użycia funkcji RemoteFX grafiki liczników w sesji protokołu remote desktop protocol do diagnozowania problemów z wydajnością za pomocą grafiki w Windows pulpitu wirtualnego.
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: troubleshoot
ms.date: 05/23/2019
ms.author: v-chjenk
ms.openlocfilehash: 0b4113f1e0024415135aa99d1fb4e881efe448a3
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2019
ms.locfileid: "66499267"
---
# <a name="diagnose-graphics-performance-issues-in-remote-desktop"></a>Diagnozowanie problemów z wydajnością grafiki w pulpitu zdalnego

System nie działa zgodnie z oczekiwaniami, należy zidentyfikować źródło problemu. Ten artykuł pomaga zidentyfikować i eliminowanie wąskich gardeł wydajności powiązane grafiki podczas sesji protokołu RDP (Remote Desktop).

## <a name="find-your-remote-session-name"></a>Znajdowanie nazwy sesji zdalnej

Potrzebna będzie nazwą sesji zdalnej do identyfikowania grafiki liczników wydajności. Postępuj zgodnie z instrukcjami w tej sekcji, aby określić nazwę sesji zdalnej usługi Windows wirtualnego Desktop w wersji zapoznawczej.

1. Otwórz wiersz polecenia Windows z sesji zdalnej.
2. Uruchom **qwinsta** polecenia.
    - Jeśli sesja znajduje się w wielu sesji maszyny wirtualnej (VM): Sufiks dla każdej nazwy licznika jest tym samym sufiksem nazwę sesji, takie jak "protokołu rdp-tcp 37."
    - Jeśli sesja jest hostowany na maszynie wirtualnej, który obsługuje wirtualne graficznych (vGPU): Liczniki są przechowywane na serwerze, a nie na maszynie wirtualnej. Wystąpienia liczników obejmują nazwę maszyny Wirtualnej, zamiast numeru nazwę sesji, takie jak "Win8 Enterprise maszyny Wirtualnej."

>[!NOTE]
> Chociaż liczniki RemoteFX w nazwach, obejmują one grafiki pulpitu zdalnego w vGPU realizację scenariuszy.

## <a name="access-performance-counters"></a>Dostęp do liczników wydajności

Liczniki wydajności w grafice RemoteFX ułatwić wykrywanie wąskich gardeł, która ułatwia śledzenie elementów, takich jak kodowanie czas ramki i pominięte ramki.

Po określeniu nazwy sesji zdalnej, wykonaj te instrukcje dotyczące grafiki RemoteFX liczniki wydajności są zbierane dla sesji zdalnej.

1. Wybierz **Start** > **narzędzia administracyjne** > **monitora wydajności**.
2. W **monitora wydajności** okna dialogowego rozwiń **narzędzia do monitorowania**, wybierz opcję **monitora wydajności**, a następnie wybierz pozycję **Dodaj**.
3. W **Dodaj liczniki** okno dialogowe z **dostępne liczniki** listy, rozwiń wartość obiektu licznika wydajności dla RemoteFX grafiki.
4. Wybierz liczniki do monitorowania.
5. W **wystąpień wybranego obiektu** , wybierz konkretne wystąpienia można monitorować dla wybranych liczników, a następnie wybierz pozycję na liście **Dodaj**. Aby wybrać wszystkie wystąpienia liczników dostępna, wybierz **wszystkie wystąpienia**.
6. Po dodaniu liczników, wybierz **OK**.

Wybrane liczniki wydajności będą wyświetlane na ekranie monitora wydajności.

>[!NOTE]
>Każdej aktywnej sesji na hoście ma własne wystąpienie każdego licznika wydajności.

## <a name="diagnosis"></a>Diagnostyka

Problemy z wydajnością powiązane grafiki ogólnie można podzielić na cztery kategorie:

- Niska szybkość klatek
- Losowe wstrzymania
- Duże opóźnienie danych wejściowych
- Jakość ramki niska

Rozpocznij od adresowania niska szybkość klatek, losowych wstrzymania i duże opóźnienie danych wejściowych. Następna sekcja poinformuje, które liczniki wydajności mierzą każdej kategorii.

### <a name="performance-counters"></a>Liczniki wydajności

Ten rozdział pomaga w identyfikacji wąskich gardeł.

Najpierw sprawdź licznik Frames/Second danych wyjściowych. Mierzy liczbę klatek udostępnione do klienta. Jeśli ta wartość jest mniejsza niż licznik Frames/Second dane wejściowe, ramki jest pomijany. Aby zidentyfikować wąskie gardło, za pomocą ramek liczniki pomijane/sekundę.

Istnieją trzy typy ramek liczniki pomijane na sekundę:

- Ramki pominięte na sekundę (za mało zasobów sieciowych)
- Ramki pominięte na sekundę (klient niewystarczające zasoby)
- Ramki pominięte na sekundę (serwera są niewystarczające zasoby)

Śledzi licznika o wysokiej wartości dla każdej ramki liczniki pomijane/sekundę wskazuje, czy problem jest związany z zasobem. Na przykład jeśli klient nie dekodowanie i obecne ramki przy użyciu stawki stosowanej serwera oferuje ramek, licznik ramek pomijane/sekundę (niewystarczające zasoby klienta) będzie wysoka.

Jeśli licznik Frames/Second danych wyjściowych odpowiada licznika Frames/Second dane wejściowe, jeszcze nadal masz lag nietypowe lub wstrzymujących działanie, problem może być Średni czas kodowania. Kodowanie jest synchroniczne procesu, który występuje na serwerze, w tym scenariuszu w pojedynczej sesji (vGPU) i na maszynie Wirtualnej, w tym scenariuszu wielu sesji. Średni czas kodowania powinna być poniżej 33 ms. Jeśli Średni czas kodowania jest poniżej 33 ms, ale nadal masz problemy z wydajnością, może to być problem z aplikacji lub systemu operacyjnego, którego używasz.

Aby uzyskać więcej informacji na temat diagnozowania problemów dotyczących aplikacji, zobacz [liczniki wydajności opóźnienia dane wejściowe użytkownika](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-rdsh-performance-counters).

Ponieważ protokół RDP obsługuje Średni czas kodowania 33 ms, obsługuje on szybkość klatek danych wejściowych, maksymalnie 30 klatek na sekundę. Należy pamiętać, że 33 ms maksymalnej obsługiwanej szybkości klatek. W wielu przypadkach szybkość klatek doświadczonym przez użytkownika będzie niższa, w zależności od tego, jak często ramkę podano dla protokołu RDP przez źródło. Na przykład zadania, takie jak oglądanie wideo wymaga pełnej wejściowych szybkości 30 klatek na sekundę, ale mniej zadań Obciążenie zasobów, takich jak rzadko edycji dokumentu programu word nie wymagają wysoki współczynnik klatek na sekundę dla zajmującymi.

Aby zdiagnozować problemy z jakością ramki, należy użyć licznika jakość ramki. Ten licznik wyraża jakość ramki danych wyjściowych jako wartość procentowa jakość ramki źródła. Obniżenie jakości mogą wynikać z funkcją RemoteFX lub mogą być wbudowane w źródle grafiki. Jeśli funkcja RemoteFX spowodowało obniżenie jakości, problem może być brak zasobów w sieci lub serwera do wysyłania zawartości pozwala uzyskać większą wierność.

## <a name="mitigation"></a>Środki zaradcze

Jeśli zasoby serwera są przyczyną wąskie gardło, wypróbuj jedną z następujących czynności w celu zwiększenia wydajności:

- Zmniejsz liczbę sesji na hoście.
- Zwiększ ilość pamięci i zasobów obliczeniowych zasobów na serwerze.
- Usuń rozwiązanie połączenia.

Jeśli zasoby sieciowe powodują wąskie gardło, wypróbuj jedną z następujących czynności, aby zwiększyć dostępność sieci sesji:

- Zmniejsz liczbę sesji na hoście.
- Usuń rozwiązanie połączenia.
- Korzystanie z wyższej przepustowości sieci.

Jeśli zasoby klienta powodują wąskie gardło, wykonaj jedną lub obie następujące czynności, aby zwiększyć wydajność:

- Zainstaluj najnowsze klienta pulpitu zdalnego.
- Zwiększ ilość pamięci i zasobów na komputerze klienckim obliczeniowych.

> [!NOTE]
> Obecnie nie obsługujemy licznika Frames/Second źródła. Na razie licznika Frames/Second źródła będzie zawsze ustawiana na 0.

## <a name="next-steps"></a>Kolejne kroki

- Aby utworzyć maszynę wirtualną platformy Azure zoptymalizowane pod kątem procesora GPU, zobacz [skonfigurować GPU przyspieszenie jednostki (GPU) dla środowiska Windows wirtualnego Desktop w wersji zapoznawczej](https://docs.microsoft.com/azure/virtual-desktop/configure-vm-gpu).
- Omówienie śledzi Rozwiązywanie problemów i eskalacji, zobacz [Rozwiązywanie problemów — omówienie, opinie i pomoc techniczna](https://docs.microsoft.com/azure/virtual-desktop/troubleshoot-set-up-overview).
- Aby dowiedzieć się więcej na temat usługi w wersji zapoznawczej, zobacz [Windows Desktop w wersji zapoznawczej środowiska](https://docs.microsoft.com/azure/virtual-desktop/environment-setup).
