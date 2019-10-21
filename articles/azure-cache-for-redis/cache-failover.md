---
title: Tryb failover i stosowanie poprawek — pamięć podręczna platformy Azure dla Redis | Microsoft Docs
description: Dowiedz się więcej na temat trybu failover, poprawek i procesu aktualizacji dla usługi Azure cache for Redis.
services: cache
author: asasine
ms.assetid: 928b9b9c-d64f-4252-884f-af7ba8309af6
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: adsasine
ms.openlocfilehash: 305511efe86d2b241ef5014d9c3f0501cfd3fbdc
ms.sourcegitcommit: 9a4296c56beca63430fcc8f92e453b2ab068cc62
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/20/2019
ms.locfileid: "72675904"
---
# <a name="failover-and-patching-for-azure-cache-for-redis"></a>Tryb failover i stosowanie poprawek dla usługi Azure cache for Redis

Zrozumienie, jakie jest przejście w tryb failover w kontekście usługi Azure cache for Redis, ma kluczowe znaczenie dla tworzenia odpornych i pomyślnych aplikacji klienckich. Typowa przyczyna przełączenia w tryb failover pamięci podręcznej pochodzi z usługi zarządzania poprawek plików binarnych Redis. W tym artykule opisano, jak działa tryb failover, jak występują podczas stosowania poprawek i jak utworzyć odporną aplikację kliencką.

## <a name="what-is-a-failover"></a>Co to jest tryb failover?

### <a name="a-quick-summary-of-our-architecture"></a>Krótkie podsumowanie naszej architektury

Pamięć podręczna jest zbudowana z wielu maszyn wirtualnych z oddzielnymi prywatnymi adresami IP. Każda maszyna wirtualna, znana także jako węzeł, jest połączona z udostępnionym modułem równoważenia obciążenia z pojedynczym wirtualnym adresem IP. Każdy węzeł uruchamia proces serwera Redis i jest dostępny za pomocą nazwy hosta i portów Redis. Każdy węzeł jest traktowany jako główny lub węzeł repliki. Gdy aplikacja kliencka nawiązuje połączenie z pamięcią podręczną, ruch przechodzi przez ten moduł równoważenia obciążenia i jest automatycznie kierowany do węzła głównego.

W podstawowej pamięci podręcznej pojedynczy węzeł jest zawsze serwerem głównym. W pamięci podręcznej w warstwie Standardowa lub Premium istnieją dwa węzły, w których jedna z nich została wybrana jako główna, a druga to replika. Ponieważ pamięć podręczna w warstwach Standardowa i Premium ma wiele węzłów, jeden węzeł może być niedostępny, a drugi nadal przetwarza żądania. Klastrowane pamięci podręczne składają się z wielu fragmentów, z których każdy ma odrębne węzły główne i repliki. Jeden fragmentu może nie działać, gdy pozostałe pozostają dostępne.

> [!NOTE]
> Podstawowa pamięć podręczna nie ma wielu węzłów i nie oferuje umowy SLA dotyczącej dostępności. Podstawowa pamięć podręczna jest zalecana tylko w celach deweloperskich i testowych. Użyj pamięci podręcznej standardowa lub Premium do wdrożenia wielowęzłowego, aby zwiększyć dostępność.

### <a name="a-failover-explained"></a>Wyjaśniono przejście w tryb failover

Tryb failover występuje, gdy węzeł repliki promuje się, aby stał się węzłem głównym, a stary węzeł główny zamyka istniejące połączenia. Po utworzeniu kopii zapasowej węzła głównego zostanie wyświetlony komunikat zmiana ról i obniżenie poziomu w celu przetworzenia repliki. Następnie nawiąże połączenie z nowym wzorcem i zsynchronizuje dane. Przejście w tryb failover może być planowane lub nieplanowane.

Planowana praca w trybie failover odbywa się podczas aktualizacji systemu, takich jak poprawki Redis lub uaktualnienia systemu operacyjnego oraz operacje zarządzania, takie jak skalowanie i ponowne uruchamianie. Ponieważ węzły otrzymują zaawansowaną informację o aktualizacji, mogą wspólnie wymieniać role i szybko aktualizować moduł równoważenia obciążenia zmiany. Planowana praca w trybie failover powinna być zakończona w czasie krótszym niż 1 sekunda.

Nieplanowana praca w trybie failover może wystąpić z powodu awarii sprzętu, awarii sieci lub innych nieoczekiwanych awarii węzła głównego. Węzeł repliki przepromuje się do serwera głównego, ale proces trwa dłużej. Węzeł repliki musi najpierw wykryć, że jego węzeł główny nie jest dostępny, zanim będzie mógł zainicjować proces przełączania do trybu failover. Węzeł repliki musi również sprawdzić, czy nieplanowany błąd nie jest przejściowy ani lokalny, aby uniknąć overeager trybu failover. To opóźnienie wykrywania oznacza, że nieplanowane przełączenie w tryb failover zwykle kończy się w okresie od 10 do 15 sekund.

## <a name="how-does-patching-occur"></a>Jak występuje poprawka?

Usługa Azure cache for Redis regularnie przeprowadza konserwację w celu zaktualizowania pamięci podręcznej przy użyciu najnowszych funkcji i poprawek platformy. Aby poprawić pamięć podręczną, usługa wykonuje następujące czynności:

1. Usługa zarządzania wybiera jeden węzeł, który ma zostać poprawiony.
1. Jeśli wybrany węzeł jest węzłem głównym, jego węzeł repliki wspólnie promuje sam siebie. Promocja jest uznawana za planowane przejście w tryb failover.
1. Wybrany węzeł zostanie uruchomiony ponownie w celu przeprowadzenia nowych zmian i zostanie przywrócony jako węzeł repliki. Węzły repliki łączą się z węzłem głównym i synchronizują dane.
1. Po zakończeniu synchronizacji danych proces stosowania poprawek powtarza się dla pozostałych węzłów.

Ponieważ stosowanie poprawek jest planowanym trybem failover, węzeł repliki szybko promuje się do wzorca i rozpoczyna żądania obsługi oraz nowe połączenia. Podstawowe pamięci podręczne nie mają węzła repliki i są niedostępne do momentu ukończenia aktualizacji. Każdy fragmentu klastrowanej pamięci podręcznej jest instalowany oddzielnie i nie zamyka połączeń z innym fragmentu.

> [!IMPORTANT]
> W celu zapobieżenia utracie danych węzły są w tej chwili poprawiane. Pamięć podręczna w warstwie Podstawowa będzie miała utratę danych. Klastrowane pamięci podręczne są poprawiane po jednym fragmentu w danym momencie.

Wiele pamięci podręcznych w tej samej grupie zasobów i w tym samym regionie jest również instalowanych pojedynczo.  Pamięci podręczne, które znajdują się w różnych grupach zasobów lub różnych regionach mogą być jednocześnie poprawiane.

Ponieważ pełna synchronizacja danych jest wykonywana przed powtórzeniem procesu, podczas korzystania z pamięci podręcznej standardowej lub Premium nie występuje utrata danych. Można zwiększyć ochronę przed utratą danych przy użyciu [eksportowania](cache-how-to-import-export-data.md#export) danych i włączania [trwałości](cache-how-to-premium-persistence.md).

### <a name="additional-cache-load"></a>Dodatkowe obciążenie pamięci podręcznej

Zawsze, gdy nastąpi przełączenie w tryb failover, warstwy pamięci podręcznej standardowa i Premium muszą replikować dane z jednego węzła do drugiego. Ta replikacja powoduje wzrost obciążenia zarówno pamięci serwera, jak i procesora CPU. Jeśli wystąpienie pamięci podręcznej jest już silnie załadowane, aplikacje klienckie mogą powodować zwiększone opóźnienia. W skrajnych przypadkach aplikacje klienckie mogą odbierać wyjątki dotyczące limitu czasu. [Skonfiguruj](cache-configure.md#memory-policies) ustawienie `maxmemory-reserved` pamięci podręcznej, aby zmniejszyć wpływ tego dodatkowego obciążenia.

## <a name="how-does-a-failover-impact-my-client-application"></a>Jak działa tryb failover dla mojej aplikacji klienckiej?

Liczba błędów widzianych przez aplikację kliencką zależy od liczby operacji oczekujących na to połączenie w momencie przejścia w tryb failover. Każde połączenie, które jest kierowane przez węzeł zamykające połączenia, będzie widzieć błędy. Wiele bibliotek klienckich może generować różne typy błędów, w tym wyjątki przekroczenia limitu czasu, wyjątki połączeń lub wyjątki gniazda po przerwaniu połączeń. Liczba i typ wyjątków zależy od tego, gdzie znajduje się w ścieżce kodu żądania, gdy pamięć podręczna zamknie połączenia. Na przykład operacja wysyłająca żądanie, ale nie otrzymała odpowiedzi, gdy nastąpi przełączenie w tryb failover może uzyskać wyjątek limitu czasu. Nowe żądania dotyczące zamkniętego obiektu połączenia będą otrzymywać wyjątki połączeń do momentu pomyślnego ponownego nawiązania połączenia.

Większość bibliotek klienta podejmie próbę ponownego nawiązania połączenia z pamięcią podręczną, jeśli została skonfigurowana w taki sposób, ale nieprzewidziane usterki mogą czasami umieścić obiekty biblioteki w stanie niemożliwym do odzyskania. Jeśli błędy będą przechowywane dłużej niż wcześniej skonfigurowany czas, należy ponownie utworzyć obiekt połączenia. W programie .NET i innych językach zorientowanych na obiekty, ponowne utworzenie połączenia bez ponownego uruchamiania aplikacji można wykonać przy użyciu [\<T \> wzorzec z opóźnieniem](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#reconnecting-with-lazyt-pattern).

### <a name="what-should-i-do-in-my-application"></a>Co mam zrobić w mojej aplikacji?

Ponieważ nie można całkowicie uniknąć przełączenia w tryb failover, należy napisać aplikacje klienckie pod kątem odporności na przerwy połączenia i Nieudane żądania. Mimo że większość bibliotek klienta automatycznie ponownie nawiązuje połączenie z punktem końcowym pamięci podręcznej, kilka bibliotek klienta próbuje ponowić Nieudane żądania. W zależności od scenariusza aplikacji, logika ponawiania z powrotem może mieć sens.

Aby przetestować odporność aplikacji klienta, należy [przeprowadzić ponowny rozruch](cache-administration.md#reboot) jako wyzwalacz ręczny dla przerw w połączeniach. Ponadto zaleca się [zaplanowanie aktualizacji](cache-administration.md#schedule-updates) w pamięci podręcznej w celu poinformowania usługi zarządzania o konieczności zastosowania poprawek środowiska uruchomieniowego Redis w określonych tygodniach. Te okna są zwykle wybierane na okresy, gdy ruch aplikacji klienckich jest niższy, aby uniknąć potencjalnych zdarzeń.

### <a name="client-network-configuration-changes"></a>Zmiany konfiguracji sieci klienta

Niektóre zmiany konfiguracji sieci po stronie klienta mogą wyzwalać błędy "Brak dostępnego połączenia".  Wymiana wirtualnego adresu IP aplikacji klienckiej między miejscami tymczasowym i produkcyjnym lub skalowanie rozmiaru/liczby wystąpień aplikacji może spowodować problem z łącznością, który jest krótszy niż jedna minuta. Aplikacja kliencka prawdopodobnie utraci połączenie z innymi zasobami sieci zewnętrznej oprócz Redis.

## <a name="next-steps"></a>Następne kroki

- [Zaplanuj aktualizacje](cache-administration.md#schedule-updates) pamięci podręcznej.
- Przetestuj odporność aplikacji przy użyciu [ponownego uruchomienia](cache-administration.md#reboot).
- [Konfigurowanie](cache-configure.md#memory-policies) rezerwacji pamięci i zasad.
