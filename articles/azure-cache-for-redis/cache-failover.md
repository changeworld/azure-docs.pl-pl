---
title: Tryb failover i stosowanie poprawek — Azure Cache for Redis
description: Dowiedz się więcej o pracy awaryjnej, poprawkach i procesie aktualizacji usługi Azure Cache for Redis.
author: asasine
ms.service: cache
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: adsasine
ms.openlocfilehash: 6ff33bd594181aabc4fd7d55ce33f780a0d06086
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74122187"
---
# <a name="failover-and-patching-for-azure-cache-for-redis"></a>Tryb failover i poprawki dla usługi Azure Cache for Redis

Aby utworzyć odporne i pomyślne aplikacje klienckie, ważne jest, aby zrozumieć pracy awaryjnej w kontekście usługi Azure Cache dla usługi Redis. Praca awaryjna może być częścią planowanych operacji zarządzania lub może być spowodowana nieplanowanymi awariami sprzętu lub sieci. Typowe użycie pracy awaryjnej pamięci podręcznej występuje, gdy usługa zarządzania łata pliki binarne usługi Azure Cache for Redis. W tym artykule opisano, co jest pracy awaryjnej, jak występuje podczas poprawiania i jak utworzyć odporną aplikację kliencką.

## <a name="what-is-a-failover"></a>Co to jest przejście awaryjne?

Zacznijmy od przeglądu pracy awaryjnej dla usługi Azure Cache dla redis.

### <a name="a-quick-summary-of-cache-architecture"></a>Szybkie podsumowanie architektury pamięci podręcznej

Pamięć podręczna jest zbudowana z wielu maszyn wirtualnych z oddzielnymi, prywatnymi adresami IP. Każda maszyna wirtualna, znana również jako węzeł, jest połączona z współdzielonym modułem równoważenia obciążenia za pomocą jednego wirtualnego adresu IP. Każdy węzeł uruchamia proces serwera Redis i jest dostępny za pomocą nazwy hosta i portów Redis. Każdy węzeł jest uważany za węzeł główny lub węzeł repliki. Gdy aplikacja kliencka łączy się z pamięcią podręczną, jej ruch przechodzi przez ten moduł równoważenia obciążenia i jest automatycznie kierowany do węzła głównego.

W podstawowej pamięci podręcznej pojedynczy węzeł jest zawsze wzorcem. W pamięci podręcznej standardowej lub premium istnieją dwa węzły: jeden jest wybrany jako wzorzec, a drugi jest repliką. Ponieważ standardowe i premium pamięci podręczne mają wiele węzłów, jeden węzeł może być niedostępny, podczas gdy drugi kontynuuje przetwarzanie żądań. Klastrowane pamięci podręczne są wykonane z wielu fragmentów, z których każdy z różnych węzłów wzorca i repliki. Jeden fragment może być w dół, podczas gdy inne pozostają dostępne.

> [!NOTE]
> Podstawowa pamięć podręczna nie ma wielu węzłów i nie oferuje umowy dotyczącej poziomu usług (SLA) dotyczącej jej dostępności. Podstawowe pamięci podręczne są zalecane tylko do celów programowania i testowania. Użyj standardowej lub premium pamięci podręcznej dla wdrożenia wielu węzłów, aby zwiększyć dostępność.

### <a name="explanation-of-a-failover"></a>Wyjaśnienie trybu failover

Praca awaryjna występuje, gdy węzeł repliki promuje się stać się węzłem głównym, a stary węzeł główny zamyka istniejące połączenia. Po wejściu węzła głównego powraca, zauważa zmiany ról i obniża się stać się repliką. Następnie łączy się z nowym wzorcem i synchronizuje dane. Przewija się w podróży awaryjnej może być planowane lub nieplanowane.

*Planowana operacja awaryjna* odbywa się podczas aktualizacji systemu, takich jak poprawki Redis lub uaktualnienia systemu operacyjnego i operacji zarządzania, takich jak skalowanie i ponowne uruchamianie. Ponieważ węzły otrzymują wcześniejsze powiadomienie o aktualizacji, mogą wspólnie wymieniać role i szybko aktualizować moduł równoważenia obciążenia zmiany. Planowana przerój awaryjny zazwyczaj kończy się w mniej niż 1 sekundę.

*Nieplanowana praca awaryjna* może się zdarzyć z powodu awarii sprzętu, awarii sieci lub innych nieoczekiwanych awarii do węzła głównego. Węzeł repliki promuje się do wzorca, ale proces trwa dłużej. Węzeł repliki musi najpierw wykryć, że jego węzeł główny nie jest dostępny, zanim będzie mógł zainicjować proces pracy awaryjnej. Węzeł repliki musi również sprawdzić, czy ten nieplanowany błąd nie jest przejściowy lub lokalny, aby uniknąć niepotrzebnego pracy awaryjnej. To opóźnienie w wykrywaniu oznacza, że nieplanowana funkcja pracy awaryjnej zazwyczaj kończy się w ciągu 10 do 15 sekund.

## <a name="how-does-patching-occur"></a>Jak następuje łatanie?

Usługa Azure Cache for Redis regularnie aktualizuje pamięć podręczną za pomocą najnowszych funkcji i poprawek platformy. Aby załatać pamięć podręczną, usługa wykonaj następujące kroki:

1. Usługa zarządzania wybiera jeden węzeł do załatania.
1. Jeśli wybrany węzeł jest węzłem głównym, odpowiedni węzeł repliki wspólnie promuje się. Ta promocja jest uważana za planowaną przemijkę awaryjną.
1. Wybrany węzeł uruchamia się ponownie, aby podjąć nowe zmiany i powraca jako węzeł repliki.
1. Węzeł repliki łączy się z węzłem głównym i synchronizuje dane.
1. Po zakończeniu synchronizacji danych proces poprawek powtarza się dla pozostałych węzłów.

Ponieważ łatanie jest planowaną przebłyskiem awaryjnym, węzeł repliki szybko promuje się, aby stać się wzorcem i rozpoczyna obsługę żądań i nowych połączeń. Podstawowe pamięci podręczne nie mają węzła repliki i są niedostępne do czasu zakończenia aktualizacji. Każdy fragment klastrowanej pamięci podręcznej jest łatany oddzielnie i nie zamyka połączeń z innym fragmentem.

> [!IMPORTANT]
> Węzły są załatane po jednym na raz, aby zapobiec utracie danych. Podstawowe pamięci podręczne będą miały utratę danych. Klastrowane pamięci podręczne są załatane jeden fragment naraz.

Wiele pamięci podręcznych w tej samej grupie zasobów i regionie są również załatane po jednym na raz.  Pamięci podręczne, które znajdują się w różnych grupach zasobów lub różnych regionach mogą być załatane jednocześnie.

Ponieważ pełna synchronizacja danych odbywa się przed powtórzeniem procesu, utrata danych jest mało prawdopodobne, aby wystąpić podczas korzystania z pamięci podręcznej Standardowej lub Premium. Możesz dodatkowo zabezpieczyć się przed utratą [danych, eksportując](cache-how-to-import-export-data.md#export) dane i włączając [trwałość.](cache-how-to-premium-persistence.md)

## <a name="additional-cache-load"></a>Dodatkowe obciążenie pamięci podręcznej

Za każdym razem, gdy nastąpi tryb failover, standardowe i premium pamięci podręczne należy replikować dane z jednego węzła do drugiego. Ta replikacja powoduje pewien wzrost obciążenia zarówno w pamięci serwera, jak i procesora CPU. Jeśli wystąpienie pamięci podręcznej jest już mocno obciążone, aplikacje klienckie mogą wystąpić zwiększone opóźnienie. W skrajnych przypadkach aplikacje klienckie mogą odbierać wyjątki przesuwu. Aby zmniejszyć wpływ tego dodatkowego obciążenia, [należy skonfigurować](cache-configure.md#memory-policies) ustawienie pamięci podręcznej. `maxmemory-reserved`

## <a name="how-does-a-failover-affect-my-client-application"></a>Jak przewija się w prawo pracy awaryjnej aplikacji klienckiej?

Liczba błędów widzianych przez aplikację kliencką zależy od liczby operacji oczekujących na to połączenie w czasie pracy awaryjnej. Każde połączenie, które jest kierowane przez węzeł, który zamknął swoje połączenia, będą widzieć błędy. Wiele bibliotek klienckich może zgłaszać różne typy błędów podczas przerywania połączeń, w tym wyjątki przesuwu, wyjątki połączeń lub wyjątki gniazda. Liczba i typ wyjątków zależy od tego, gdzie w ścieżce kodu żądanie jest, gdy pamięć podręczna zamyka swoje połączenia. Na przykład operacja, która wysyła żądanie, ale nie otrzymała odpowiedzi po wystąpieniu pracy awaryjnej może uzyskać wyjątek przesuwu. Nowe żądania na zamkniętym obiekcie połączenia odbierają wyjątki połączeń, dopóki ponowne połączenie nie zostanie pomyślnie połączone.

Większość bibliotek klienckich próbuje ponownie połączyć się z pamięcią podręczną, jeśli są skonfigurowane do tego. Jednak nieprzewidziane błędy mogą od czasu do czasu umieścić obiekty biblioteki w stanie nieodwracalnym. Jeśli błędy utrzymują się dłużej niż wstępnie skonfigurowany czas, obiekt połączenia powinny być odtworzone. W Microsoft.NET i innych językach obiektowych odtworzenie połączenia bez ponownego uruchamiania aplikacji można wykonać za pomocą [wzorca Lazy\<T\> ](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#reconnecting-with-lazyt-pattern).

### <a name="how-do-i-make-my-application-resilient"></a>Jak sprawić, by moja aplikacja była odporna?

Ponieważ nie można całkowicie uniknąć pracy awaryjnej, zapisz aplikacje klienckie dla odporności na podziały połączeń i żądania nie powiodło się. Mimo że większość bibliotek klienta automatycznie ponownie połączyć się z punktu końcowego pamięci podręcznej, kilka z nich próbuje ponowić próbę żądania nie powiodło się. W zależności od scenariusza aplikacji może mieć sens użycie logiki ponawiania próby z wycofywania.

Aby przetestować odporność aplikacji klienckiej, należy użyć [ponownego uruchomienia](cache-administration.md#reboot) jako ręcznego wyzwalacza dla przerw w połączeniu. Ponadto zaleca się [zaplanowanie aktualizacji](cache-administration.md#schedule-updates) w pamięci podręcznej. Poinformuj usługę zarządzania, aby zastosowała poprawki środowiska uruchomieniowego Redis podczas określonych okien tygodniowych. Te okna są zazwyczaj okresy, gdy ruch aplikacji klienckiej jest niski, aby uniknąć potencjalnych incydentów.

### <a name="client-network-configuration-changes"></a>Zmiany konfiguracji sieci klienta

Niektóre zmiany konfiguracji sieci po stronie klienta mogą powodować błędy "Brak dostępności połączenia". Takie zmiany mogą obejmować:

- Zamiana wirtualnego adresu IP aplikacji klienckiej między miejscami przejściowymi i produkcyjnymi.
- Skalowanie rozmiaru lub liczby wystąpień aplikacji.

Takie zmiany mogą powodować problem z łącznością, który trwa mniej niż jedną minutę. Aplikacja kliencka prawdopodobnie utraci połączenie z innymi zasobami sieci zewnętrznej oprócz usługi Azure Cache for Redis.

## <a name="next-steps"></a>Następne kroki

- [Zaplanuj aktualizacje](cache-administration.md#schedule-updates) pamięci podręcznej.
- Przetestuj odporność aplikacji przy użyciu [ponownego uruchomienia .](cache-administration.md#reboot)
- [Konfigurowanie](cache-configure.md#memory-policies) rezerwacji pamięci i zasad.
