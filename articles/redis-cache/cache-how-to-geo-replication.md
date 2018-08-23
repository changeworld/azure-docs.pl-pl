---
title: Jak skonfigurować replikację geograficzną dla usługi Azure Redis Cache | Dokumentacja firmy Microsoft
description: Dowiedz się, jak replikować swoich wystąpień usługi Azure Redis Cache w wielu regionach geograficznych.
services: redis-cache
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.assetid: 375643dc-dbac-4bab-8004-d9ae9570440d
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 09/15/2017
ms.author: wesmc
ms.openlocfilehash: 644a7414365ded53acb20bb2f9ae9d086f263765
ms.sourcegitcommit: a2ae233e20e670e2f9e6b75e83253bd301f5067c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/13/2018
ms.locfileid: "42054769"
---
# <a name="how-to-configure-geo-replication-for-azure-redis-cache"></a>Jak skonfigurować replikację geograficzną dla usługi Azure Redis Cache

Replikacja geograficzna udostępnia mechanizm do łączenia dwóch wystąpień usługi Azure Redis Cache warstwy Premium. Jeden pamięci podręcznej nazw jest wyznaczony jako podstawowy połączonej pamięci podręcznej, a drugi jako pomocniczy połączonej pamięci podręcznej. Pomocniczy połączonej pamięci podręcznej staje się tylko do odczytu, a dane zapisywane w głównej pamięci podręcznej są replikowane do dodatkowej połączonej pamięci podręcznej. Ta funkcja może służyć do replikowania pamięci podręcznej w różnych regionach platformy Azure. Ten artykuł zawiera wskazówki w przypadku konfigurowania replikacji geograficznej dla swoich wystąpień usługi Azure Redis Cache warstwy Premium.

## <a name="geo-replication-prerequisites"></a>Wymagania wstępne replikacji geograficznej

Aby skonfigurować replikację geograficzną między dwoma pamięci podręcznych, muszą być spełnione następujące wymagania wstępne:

- Musi być zarówno pamięci podręcznych [w warstwie Premium](cache-premium-tier-intro.md) zapisuje w pamięci podręcznej.
- Zarówno pamięci podręcznej musi być w tej samej subskrypcji platformy Azure.
- Pomocniczy połączonej pamięci podręcznej musi być w tej samej warstwie cenowej lub warstwy cenowej większych niż podstawowy połączonej pamięci podręcznej.
- Jeśli podstawowy połączonej pamięci podręcznej ma włączone klastrowanie, dodatkowej połączonej pamięci podręcznej musi mieć włączone z taką samą liczbę fragmentów jako podstawowy połączonej pamięci podręcznej klastrowanie.
- Zarówno pamięci podręczne, które muszą zostać utworzone w stanie uruchomienia.
- Stan trwały nie mogą być włączone na obu pamięci podręcznej.
- Replikacja geograficzna między pamięci podręczne w tej samej sieci Wirtualnej jest obsługiwana. 
- Replikacja geograficzna między pamięciami podręcznymi w wirtualnych sieciach równorzędnych, w tym samym regionie jest obecnie w wersji zapoznawczej. Dwie sieci wirtualne muszą zostać skonfigurowane w taki sposób, że zasoby w sieci wirtualne będą mogli dotrzeć do siebie nawzajem za pośrednictwem połączenia protokołu TCP.
- Replikacja geograficzna między pamięciami podręcznymi w wirtualnych sieciach równorzędnych w różnych regionach nie jest jeszcze obsługiwana, ale będzie wkrótce dostępna w wersji zapoznawczej.

Po skonfigurowaniu replikacji geograficznej do pary usługi połączonej pamięci podręcznej są stosowane następujące ograniczenia:

- Pomocniczy połączonej pamięci podręcznej jest tylko do odczytu. można odczytać z niego, ale nie można zapisać danych do niego. 
- Wszelkie dane, które były w dodatkowej połączonej pamięci podręcznej, przed dodaniem linku jest usuwany. Jeśli replikacja geograficzna później usunięte jednak, replikowanych danych pozostaje w dodatkowej połączonej pamięci podręcznej.
- Nie można zainicjować [skalowanie w operacji](cache-how-to-scale.md) na obu pamięci podręcznej lub [zmienić liczbę fragmentów](cache-how-to-premium-clustering.md) Jeśli włączonym klastrowaniu pamięci podręcznej.
- Nie można włączyć trwałość na obu pamięci podręcznej.
- Można użyć [eksportu](cache-how-to-import-export-data.md#export) przy użyciu obu cache, ale można je tylko [importu](cache-how-to-import-export-data.md#import) do głównej połączonej pamięci podręcznej.
- Nie można usunąć połączonej pamięci podręcznej lub grupy zasobów, w którym się znajdują, dopóki nie usuniesz link replikacji geograficznej. Aby uzyskać więcej informacji, zobacz [dlaczego operacja kończy się niepowodzeniem podczas próby usunięcia Moje połączonej pamięci podręcznej?](#why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache)
- Jeśli dwa pamięci podręcznych znajdują się w różnych regionach, kosztów sieciowego ruchu wychodzącego dotyczą dane replikowane między regionami w pomocniczej połączonej pamięci podręcznej. Aby uzyskać więcej informacji, zobacz [ile kosztuje do replikacji danych między regionami platformy Azure?](#how-much-does-it-cost-to-replicate-my-data-across-azure-regions)
- Istnieje nie automatyczny tryb failover do pomocniczej połączonej pamięci podręcznej, jeśli głównej pamięci podręcznej (i jego repliką) przestaną działać. Aby aplikacje klienckie trybu failover należy ręcznie usuń link replikacji geograficznej i wskazują aplikacji klienckich pamięci podręcznej, który był wcześniej dodatkowej połączonej pamięci podręcznej. Aby uzyskać więcej informacji, zobacz [przechodzenie w tryb failover do pomocniczej pamięci podręcznej połączone działanie?](#how-does-failing-over-to-the-secondary-linked-cache-work)

## <a name="add-a-geo-replication-link"></a>Dodaj link replikacji geograficznej

1. Aby połączyć dwa pamięci podręcznych premium dla replikacji geograficznej, kliknij przycisk **Geografickou replikaci** z menu zasobów służyć jako podstawowy połączonej pamięci podręcznej w pamięci podręcznej, a następnie kliknij **Dodaj link replikacji pamięci podręcznej** z **Geografickou replikaci** bloku.

    ![Dodaj łącze](./media/cache-how-to-geo-replication/cache-geo-location-menu.png)

2. Kliknij nazwę żądanej pomocniczej pamięci podręcznej z **zgodne pamięci podręczne** listy. Jeśli żądanej pamięci podręcznej nie jest wyświetlane na liście, upewnij się, że [wymagań wstępnych replikacji geograficznej](#geo-replication-prerequisites) dla żądanego pomocniczej pamięci podręcznej są spełnione. Aby filtrować pamięci podręcznych według regionu, kliknij odpowiedni region na mapie, aby wyświetlić tylko te pamięci podręczne w regionie **zgodne pamięci podręczne** listy.

    ![Zgodne pamięci podręczne replikacji geograficznej](./media/cache-how-to-geo-replication/cache-geo-location-select-link.png)
    
    Można również zainicjować proces łączenia lub wyświetlić szczegółowe informacje o pomocniczej pamięci podręcznej za pomocą menu kontekstowego.

    ![Menu kontekstowe replikacji geograficznej](./media/cache-how-to-geo-replication/cache-geo-location-select-link-context-menu.png)

3. Kliknij przycisk **łącze** łączą się ze sobą dwie pamięci podręczne i rozpocząć proces replikacji.

    ![Pamięci podręczne łącza](./media/cache-how-to-geo-replication/cache-geo-location-confirm-link.png)

4. Możesz wyświetlić postęp procesu replikacji na **Geografickou replikaci** bloku.

    ![Stan połączeń](./media/cache-how-to-geo-replication/cache-geo-location-linking.png)

    Można również wyświetlić stan połączeń na **Przegląd** bloku podstawowego i pomocniczego buforów.

    ![Stan pamięci podręcznej](./media/cache-how-to-geo-replication/cache-geo-location-link-status.png)

    Po zakończeniu procesu replikacji **Link stanu** zmieni się na **Powodzenie**.

    ![Stan pamięci podręcznej](./media/cache-how-to-geo-replication/cache-geo-location-link-successful.png)

    W trakcie łączenia głównej połączonej pamięci podręcznej pozostaje dostępna do użycia, ale dodatkowej połączonej pamięci podręcznej nie jest dostępne, dopóki nie zakończy proces łączenia.

## <a name="remove-a-geo-replication-link"></a>Usuń link replikacji geograficznej

1. Kliknij, aby usunąć łącze między dwoma pamięci podręczne i zatrzymanie replikacji geograficznej **odłączyć pamięci podręcznych** z **Geografickou replikaci** bloku.
    
    ![Odłącz pamięci podręczne](./media/cache-how-to-geo-replication/cache-geo-location-unlink.png)

    Po zakończeniu tego procesu, odłączanie pomocnicza pamięć podręczna jest dostępna dla odczytów i zapisów.

>[!NOTE]
>Po usunięciu link replikacji geograficznej zreplikowanych danych z podstawowego połączonej pamięci podręcznej pozostaje w pomocniczej pamięci podręcznej.
>
>

## <a name="geo-replication-faq"></a>Replikacja geograficzna — często zadawane pytania

- [Czy można używać replikacji geograficznej, z pamięcią podręczną warstwy Standard lub Basic?](#can-i-use-geo-replication-with-a-standard-or-basic-tier-cache)
- [Przepełnieniu pamięci podręcznej jest dostępna do użycia w procesie łączenie lub rozłączanie?](#is-my-cache-available-for-use-during-the-linking-or-unlinking-process)
- [Czy można połączyć więcej niż dwóch pamięci podręcznych razem?](#can-i-link-more-than-two-caches-together)
- [Czy można połączyć dwa pamięci podręcznych z różnych subskrypcji platformy Azure?](#can-i-link-two-caches-from-different-azure-subscriptions)
- [Czy można połączyć dwa pamięci podręczne o różnych rozmiarach?](#can-i-link-two-caches-with-different-sizes)
- [Przy włączonym klastrowaniu można używać replikacji geograficznej?](#can-i-use-geo-replication-with-clustering-enabled)
- [Z mojej pamięciami podręcznymi w sieci Wirtualnej można używać replikacji geograficznej?](#can-i-use-geo-replication-with-my-caches-in-a-vnet)
- [Co to jest harmonogram replikacji dla replikacji geograficznej usługi Redis?](#what-is-the-replication-schedule-for-redis-geo-replication)
- [Jak replikacja geografickou replikaci długo?](#how-long-does-geo-replication-replication-take)
- [Gwarantuje punktu odzyskiwania replikacji](#is-the-replication-recovery-point-guaranteed)
- [Do zarządzania replikacją geograficzną można użyć programu PowerShell lub wiersza polecenia platformy Azure?](#can-i-use-powershell-or-azure-cli-to-manage-geo-replication)
- [Ile kosztuje do replikacji danych między regionami platformy Azure?](#how-much-does-it-cost-to-replicate-my-data-across-azure-regions)
- [Dlaczego operacja kończy się niepowodzeniem podczas próby usunięcia Moje połączonej pamięci podręcznej?](#why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache)
- [Jakie regionie Użyj mojej dodatkowej połączonej pamięci podręcznej](#what-region-should-i-use-for-my-secondary-linked-cache)
- [Jak działa przechodzenie w tryb failover do pomocniczej połączonej pamięci podręcznej?](#how-does-failing-over-to-the-secondary-linked-cache-work)

### <a name="can-i-use-geo-replication-with-a-standard-or-basic-tier-cache"></a>Czy można używać replikacji geograficznej, z pamięcią podręczną warstwy Standard lub Basic?

Nie, replikacja geograficzna dostępnej wyłącznie dla podręczna w warstwie Premium.

### <a name="is-my-cache-available-for-use-during-the-linking-or-unlinking-process"></a>Przepełnieniu pamięci podręcznej jest dostępna do użycia w procesie łączenie lub rozłączanie?

- Podczas łączenia ze sobą dwie pamięci podręcznych, dla replikacji geograficznej, podstawowy połączonej pamięci podręcznej pozostaje dostępna do użycia, lecz dodatkowej połączonej pamięci podręcznej nie jest dostępne, dopóki nie zakończy proces łączenia.
- Podczas usuwania link replikacji geograficznej między dwoma pamięci podręcznych, zarówno pamięci podręcznych pozostają dostępne do użycia.

### <a name="can-i-link-more-than-two-caches-together"></a>Czy można połączyć więcej niż dwóch pamięci podręcznych razem?

Nie, korzystając z replikacji geograficznej można połączyć tylko dwa pamięci podręcznych ze sobą.

### <a name="can-i-link-two-caches-from-different-azure-subscriptions"></a>Czy można połączyć dwa pamięci podręcznych z różnych subskrypcji platformy Azure?

Pamięci podręczne, nie musi być w tej samej subskrypcji platformy Azure.

### <a name="can-i-link-two-caches-with-different-sizes"></a>Czy można połączyć dwa pamięci podręczne o różnych rozmiarach?

Tak, tak długo, jak dodatkowej połączonej pamięci podręcznej jest większy niż podstawowy połączonej pamięci podręcznej.

### <a name="can-i-use-geo-replication-with-clustering-enabled"></a>Przy włączonym klastrowaniu można używać replikacji geograficznej?

Tak, tak długo, jak zarówno pamięci podręcznych ma taką samą liczbę fragmentów.

### <a name="can-i-use-geo-replication-with-my-caches-in-a-vnet"></a>Z mojej pamięciami podręcznymi w sieci Wirtualnej można używać replikacji geograficznej?

Tak, replikacja geograficzna pamięci podręczne w sieciach wirtualnych są obsługiwane. 

- Replikacja geograficzna między pamięci podręczne w tej samej sieci Wirtualnej jest obsługiwana.
- Replikacja geograficzna między pamięciami podręcznymi w różnych sieciach wirtualnych jest również obsługiwany, tak długo, jak te dwie sieci wirtualne są skonfigurowane w taki sposób, że zasoby w sieci wirtualne będą mogli dotrzeć do siebie nawzajem za pośrednictwem połączenia protokołu TCP.

### <a name="what-is-the-replication-schedule-for-redis-geo-replication"></a>Co to jest harmonogram replikacji dla replikacji geograficznej usługi Redis?

Replikacja nie jest realizowane zgodnie z harmonogramem określonym jest ciągłe i asynchroniczne, tj wszystkie zapisy, które są gotowe do podstawowej są natychmiast asynchronicznie replikowane na pomocniczym.

### <a name="how-long-does-geo-replication-replication-take"></a>Jak replikacja geografickou replikaci długo?

Replikacja jest przyrostowy, asynchronicznego i ciągłe i czas zazwyczaj nie jest znacznie różni się od opóźnienia między regionami. W pewnych okolicznościach w pewnych porach pomocniczy może być konieczne wykonać pełną synchronizację danych z serwera podstawowego. W takim przypadku podczas replikacji jest zależne od szeregu czynników, takich jak: obciążenia głównej pamięci podręcznej przepustowość dostępną na komputerze z pamięci podręcznej, między opóźnienie region itd. Na przykład w oparciu o pewne testy, które znaleźliśmy się tego czasu replikacji, aby uzyskać pełne 53 GB z replikacją geograficzną pair wschodzie USA i regionach zachodnie stany USA mogą znajdować się gdziekolwiek od 5 do 10 minut.

### <a name="is-the-replication-recovery-point-guaranteed"></a>Gwarantuje punktu odzyskiwania replikacji

Obecnie dla pamięci podręcznej w trybie z replikacją geograficzną, trwałość i importu/eksportu funkcja jest wyłączona. Dlatego w przypadku klientów zainicjować trybu failover lub w przypadkach, gdy łącze replikacji zostało przerwane między pary replikowanej geograficznie pomocniczej zostaną zachowane w pamięci danych, który był synchronizowany z serwera podstawowego, aż do tego punktu w czasie. Nie ma żadnej gwarancji punktu odzyskiwania w takich sytuacjach.

### <a name="can-i-use-powershell-or-azure-cli-to-manage-geo-replication"></a>Do zarządzania replikacją geograficzną można użyć programu PowerShell lub wiersza polecenia platformy Azure?

W tym momencie można zarządzać tylko replikację geograficzną za pomocą witryny Azure portal.

### <a name="how-much-does-it-cost-to-replicate-my-data-across-azure-regions"></a>Ile kosztuje do replikacji danych między regionami platformy Azure?

Korzystając z replikacji geograficznej, dane z podstawowego połączonej pamięci podręcznej są replikowane do dodatkowej połączonej pamięci podręcznej. Jeśli dwa połączonej pamięci podręcznej znajdują się w tym samym regionie platformy Azure, nie ma opłat za transfer danych. Jeśli dwa połączonej pamięci podręcznej znajdują się w różnych regionach platformy Azure, opłat transferu danych replikacji geograficznej jest koszt przepustowości replikowania tych danych do innego regionu platformy Azure. Aby uzyskać więcej informacji, zobacz [przepustowość — szczegóły cennika](https://azure.microsoft.com/pricing/details/bandwidth/).

### <a name="why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache"></a>Dlaczego operacja kończy się niepowodzeniem podczas próby usunięcia Moje połączonej pamięci podręcznej?

Gdy dwa pamięci podręczne są połączone ze sobą, nie można usunąć pamięci podręcznej lub grupy zasobów, w którym się znajdują, dopóki nie usuniesz link replikacji geograficznej. Jeśli spróbujesz usunąć grupę zasobów, który zawiera jedną lub obie połączone pamięci podręcznych innych zasobów w grupie zasobów zostaną usunięte, ale grupa zasobów pozostaje w `deleting` stanu i wszystkie połączone pamięci podręczne w grupie zasobów, pozostają w `running`stanu. Aby zakończyć usuwanie grupy zasobów i połączone pamięci podręczne w nim, przerwać łącze replikacji geograficznej, zgodnie z opisem w [Usuń link replikacji geograficznej](#remove-a-geo-replication-link).

### <a name="what-region-should-i-use-for-my-secondary-linked-cache"></a>Jakie regionie Użyj mojej dodatkowej połączonej pamięci podręcznej

Ogólnie rzecz biorąc zaleca się dla pamięci podręcznej istnieje w tym samym regionie platformy Azure, co aplikacja, która uzyskuje do niej dostęp. Jeśli aplikacja ma region podstawowy i rezerwowy, pamięci podręcznych podstawowe i pomocnicze powinny istnieć w tych tych samych regionach. Aby uzyskać więcej informacji na temat sparowanych regionów, zobacz [najlepsze rozwiązania — Azure sparowane regiony](../best-practices-availability-paired-regions.md).

### <a name="how-does-failing-over-to-the-secondary-linked-cache-work"></a>Jak działa przechodzenie w tryb failover do pomocniczej połączonej pamięci podręcznej?

W wersji początkowej replikacji geograficznej usługi Azure Redis Cache nie obsługuje automatycznej pracy awaryjnej w regionach platformy Azure. Replikacja geograficzna jest używana głównie w scenariuszu odzyskiwania po awarii. W przypadku scenariusza odzyskiwania distater klientów należy uzupełnić całego stosu aplikacji w regionie kopii zapasowej w skoordynowany sposób, a nie o poszczególnych składników aplikacji zdecyduj, kiedy należy przełączyć się do ich kopie zapasowe własnych. Jest to szczególnie istotne w pamięci podręcznej Redis. Jedną z kluczowych zalet usługi Redis jest on magazynu bardzo małym opóźnieniem. Jeśli używany przez aplikację pamięci podręcznej Redis wprowadza tryb failover do innego regionu platformy Azure, ale warstwy obliczeniowej nie, czas obiegu dodano musi zauważalnego wpływu na wydajność. Z tego powodu firma Microsoft chce uniknąć niepowodzenia pamięci podręcznej Redis za pośrednictwem automatycznie ze względu na problemy z dostępnością przejściowy.

Obecnie Aby zainicjować trybu failover, należy do usunięcia łącza replikacji geograficznej w witrynie Azure portal, a następnie Zmień punkt końcowy połączenia klienta pamięci podręcznej Redis z podstawowego połączonej pamięci podręcznej do pomocniczej pamięci podręcznej (dawniej połączone). Po dwóch pamięci podręczne są odłączyć, replik ponownie staje się regularne pamięci podręcznej odczytu / zapisu i akceptuje żądania bezpośrednio od klientów usługi Redis.


## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o [usługi Azure Redis Cache w warstwie Premium](cache-premium-tier-intro.md).

