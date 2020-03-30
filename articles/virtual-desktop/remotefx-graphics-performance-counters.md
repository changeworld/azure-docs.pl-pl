---
title: Diagnozowanie problemów z wydajnością grafiki Pulpit zdalny — Azure
description: W tym artykule opisano sposób używania liczników grafiki RemoteFX w sesjach protokołu pulpitu zdalnego do diagnozowania problemów z wydajnością grafiki w programie Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 05/23/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 84cee86dbddff77f6142925eec01889cf793a466
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127553"
---
# <a name="diagnose-graphics-performance-issues-in-remote-desktop"></a>Diagnozowanie problemów z wydajnością grafiki na pulpicie zdalnym

Aby zdiagnozować problemy z jakością środowiska z sesjami zdalnymi, liczniki zostały dostarczone w sekcji Grafika RemoteFX w Monitorze wydajności. Ten artykuł ułatwia zidentyfikowanie i naprawienie wąskich gardeł wydajności związanych z grafiką podczas sesji protokołu RDP (Remote Desktop Protocol) przy użyciu tych liczników.

## <a name="find-your-remote-session-name"></a>Znajdowanie nazwy sesji zdalnej

Do zidentyfikowania liczników wydajności grafiki potrzebna jest nazwa sesji zdalnej. Postępuj zgodnie z instrukcjami w tej sekcji, aby zidentyfikować wystąpienie każdego licznika.

1. Otwórz wiersz polecenia systemu Windows z sesji zdalnej.
2. Uruchom polecenie **qwinsta** i znajdź nazwę sesji.
    - Jeśli sesja jest hostowana na maszynie wirtualnej z wieloma sesjami: Wystąpienie każdego licznika jest sufiksy przez ten sam numer, który sufiksy nazwę sesji, takich jak "rdp-tcp 37."
    - Jeśli sesja jest hostowana na maszynie Wirtualnej, która obsługuje wirtualne jednostki przetwarzania grafiki (vGPU): Wystąpienie każdego licznika jest przechowywane na serwerze, a nie na maszynie Wirtualnej. Wystąpienia licznika zawierają nazwę maszyny Wirtualnej zamiast numeru w nazwie sesji, na przykład "Win8 Enterprise VM".

>[!NOTE]
> Podczas gdy liczniki mają RemoteFX w swoich nazwach, zawierają one grafiki pulpitu zdalnego w scenariuszach vGPU, jak również.

## <a name="access-performance-counters"></a>Dostęp do liczników wydajności

Po określiniu nazwy sesji zdalnej postępuj zgodnie z tymi instrukcjami, aby zebrać liczniki wydajności remoteFX graphics dla sesji zdalnej.

1. Wybierz **pozycję Uruchom** > Monitor wydajności**narzędzi administracyjnych** > **Performance Monitor**.
2. W oknie dialogowym **Monitor wydajności** rozwiń węzeł **Narzędzia monitorowania**, wybierz pozycję **Monitor wydajności**, a następnie wybierz pozycję **Dodaj**.
3. W oknie dialogowym **Dodawanie liczników** z listy **Dostępne liczniki** rozwiń sekcję Dla grafiki RemoteFX.
4. Wybierz liczniki, które mają być monitorowane.
5. Na liście **Wystąpienia zaznaczonych obiektów** wybierz konkretne wystąpienia, które mają być monitorowane dla wybranych liczników, a następnie wybierz pozycję **Dodaj**. Aby zaznaczyć wszystkie dostępne wystąpienia liczników, wybierz **opcję Wszystkie wystąpienia**.
6. Po dodaniu liczników wybierz przycisk **OK**.

Wybrane liczniki wydajności pojawią się na ekranie Monitora wydajności.

>[!NOTE]
>Każda aktywna sesja na hoście ma własne wystąpienie każdego licznika wydajności.

## <a name="diagnose-issues"></a>Diagnozowanie problemów

Problemy z wydajnością związane z grafiką zazwyczaj dzielą się na cztery kategorie:

- Niska liczba klatek na sekundę
- Losowe stragany
- Duże opóźnienia wejścia
- Niska jakość ramki

### <a name="addressing-low-frame-rate-random-stalls-and-high-input-latency"></a>Rozwiązanie problemu niskiej liczby klatek na sekundę, losowych przeciągnięcia i dużych opóźnień wejściowych

Najpierw sprawdź licznik Ramki wyjściowe/Drugi. Mierzy liczbę ramek udostępnionych klientowi. Jeśli ta wartość jest mniejsza niż licznik Klatki wejściowe/Druga, klatki są pomijane. Aby zidentyfikować wąskie gardło, należy użyć ramek pominiętych/drugich liczników.

Istnieją trzy typy liczników pominiętych/drugich:

- Pominięte ramki/druga (niewystarczające zasoby serwera)
- Pominięte ramki/druga (niedostateczne zasoby sieciowe)
- Pominięte ramki/sekunda (niewystarczające zasoby klienta)

Wysoka wartość dla dowolnego z ramek pominięte/drugi liczniki oznacza, że problem jest związany z zasobem licznik utworów. Na przykład jeśli klient nie dekoduje i nie przedstawia ramek z taką samą szybkością, jak serwer udostępnia ramki, licznik Ramki pominięte/Drugie (niewystarczające zasoby klienta) będzie wysoki.

Jeśli licznik Wyjściowe/Drugi odpowiada licznikowi Input Frames/Second, ale nadal można zauważyć nietypowe opóźnienie lub przeciągnięcia, winowajcą może być średni czas kodowania. Kodowanie jest procesem synchroniczną, który występuje na serwerze w scenariuszu jednej sesji (vGPU) i na maszynie wirtualnej w scenariuszu wielu sesji. Średni czas kodowania powinien być poniżej 33 ms. Jeśli średni czas kodowania jest poniżej 33 ms, ale nadal masz problemy z wydajnością, może to być problem z używaną aplikacją lub systemem operacyjnym.

Aby uzyskać więcej informacji na temat diagnozowania problemów związanych z aplikacjami, zobacz [Liczniki wydajności Opóźnienia wprowadzania danych użytkownika](/windows-server/remote/remote-desktop-services/rds-rdsh-performance-counters/).

Ponieważ protokół RDP obsługuje średni czas kodowania wynoszący 33 ms, obsługuje szybkość wprowadzania klatek do 30 klatek na sekundę. Należy zauważyć, że 33 ms to maksymalna obsługiwana liczba klatek na sekundę. W wielu przypadkach liczba klatek na sekundę doświadczana przez użytkownika będzie niższa, w zależności od tego, jak często ramka jest dostarczana do protokołu RDP przez źródło. Na przykład zadania takie jak oglądanie wideo wymagają pełnej szybkości wprowadzania klatek na sekundę 30 klatek na sekundę, ale mniej obciążające obliczeń zadania, takie jak rzadko edytowanie dokumentu, skutkują znacznie niższą wartością ramek wejściowych/sekundę bez pogorszenia jakości środowiska użytkownika.

### <a name="addressing-poor-frame-quality"></a>Rozwiązanie problemu niskiej jakości ramki

Użyj licznika Jakość ramki, aby zdiagnozować problemy z jakością ramki. Licznik ten wyraża jakość ramki wyjściowej jako procent jakości ramki źródłowej. Utrata jakości może być spowodowana remotefx, lub może być nieodłącznym elementem źródła grafiki. Jeśli remoteFX spowodował utratę jakości, problem może być brak zasobów sieciowych lub serwera do wysyłania zawartości o wyższej wierności.

## <a name="mitigation"></a>Środki zaradcze

Jeśli zasoby serwera powodują wąskie gardło, spróbuj wykonać jedną z następujących metod, aby zwiększyć wydajność:

- Zmniejsz liczbę sesji na hosta.
- Zwiększ ilość pamięci i zasobów obliczeniowych na serwerze.
- Upuść rozdzielczość połączenia.

Jeśli zasoby sieciowe powodują wąskie gardło, wypróbuj jedną z następujących metod, aby poprawić dostępność sieci na sesję:

- Zmniejsz liczbę sesji na hosta.
- Użyj sieci o wyższej przepustowości.
- Upuść rozdzielczość połączenia.

Jeśli zasoby klienta powodują wąskie gardło, spróbuj wykonać jedną z następujących metod, aby zwiększyć wydajność:

- Zainstaluj najnowszego klienta pulpitu zdalnego.
- Zwiększ ilość pamięci i zasobów obliczeniowych na komputerze klienckim.

> [!NOTE]
> Obecnie nie obsługujemy licznika Ramki źródłowe/Drugi. Na razie licznik Ramki źródłowe/Drugi będzie zawsze wyświetlany 0.

## <a name="next-steps"></a>Następne kroki

- Aby utworzyć maszynę wirtualną platformy Azure zoptymalizowaną pod kątem procesora graficznego, zobacz [Konfigurowanie akceleracji procesora graficznego (GPU) dla środowiska pulpitu wirtualnego systemu Windows](configure-vm-gpu.md).
- Aby zapoznać się z omówieniem ścieżek rozwiązywania problemów i eskalacji, zobacz [Omówienie rozwiązywania problemów, opinie i pomoc techniczna](troubleshoot-set-up-overview.md).
- Aby dowiedzieć się więcej o usłudze, zobacz [Środowisko pulpitu systemu Windows](environment-setup.md).
