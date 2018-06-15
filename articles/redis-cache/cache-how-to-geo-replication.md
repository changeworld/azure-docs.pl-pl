---
title: Jak skonfigurować replikację geograficzną dla pamięci podręcznej Redis Azure | Dokumentacja firmy Microsoft
description: Informacje o replikacji z wystąpienia pamięci podręcznej Redis Azure w regionach geograficznych.
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
ms.openlocfilehash: 883683f6af7943fa4da49095c9a15aefd5cfa719
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/19/2018
ms.locfileid: "27911374"
---
# <a name="how-to-configure-geo-replication-for-azure-redis-cache"></a>Jak skonfigurować replikację geograficzną dla pamięci podręcznej Redis Azure

Replikacja geograficzna udostępnia mechanizm do konsolidacji dwa wystąpienia pamięci podręcznej Redis Azure warstwy Premium. Jedna pamięć podręczna jest wyznaczony jako głównej połączonego pamięci podręcznej, a drugi jako dodatkowej pamięci podręcznej połączony. Dodatkowej pamięci podręcznej połączonego staje się tylko do odczytu, a dane zapisywane w pamięci podręcznej podstawowej są replikowane do dodatkowej połączonego pamięci podręcznej. Ta funkcja umożliwia replikowanie pamięci podręcznej w regionach platformy Azure. Ten artykuł zawiera przewodnik dotyczący konfigurowania — replikacja geograficzna dla swoich wystąpień pamięci podręcznej Redis Azure warstwy Premium.

## <a name="geo-replication-prerequisites"></a>Replikacja geograficzna wymagania wstępne

Aby skonfigurować replikację geograficzną między dwoma pamięci podręcznych, muszą być spełnione następujące wymagania wstępne:

- Zarówno pamięci podręcznej musi być [warstwy Premium](cache-premium-tier-intro.md) przechowuje w pamięci podręcznej.
- Zarówno pamięci podręcznej musi być w tej samej subskrypcji platformy Azure.
- Połączone dodatkowej pamięci podręcznej musi być tej samej warstwie cenowej lub większy poziom cenowy niż podstawowy połączonego pamięci podręcznej.
- Jeśli klaster włączone głównej połączonego pamięci podręcznej, dodatkowej pamięci podręcznej połączonego musi mieć klaster włączone z taką samą liczbę fragmentów jako podstawowy połączonego pamięci podręcznej.
- Zarówno pamięci podręcznych muszą zostać utworzone i uruchomione.
- Trwałości nie mogą być włączone na obu pamięci podręcznej.
- Replikacja geograficzna między pamięci podręcznych w tej samej sieci Wirtualnej jest obsługiwane. Replikacja geograficzna między pamięci podręcznych w różnych sieci wirtualnych jest również obsługiwany, jak długo dwie sieci wirtualne są skonfigurowane w taki sposób, że zasoby w sieci wirtualne są dostęp do siebie za pośrednictwem połączeń TCP.

Po skonfigurowaniu replikacji geograficznej parę z połączonego pamięci podręcznej, obowiązują następujące ograniczenia:

- Połączone dodatkowej pamięci podręcznej jest tylko do odczytu. można odczytać z niego, ale nie można zapisać danych do niego. 
- Wszystkie dane sprzed w dodatkowej pamięci podręcznej połączonego łącze zostało dodane zostaną usunięte. Jeśli replikacja geograficzna następnie usunięciu jednak replikowanych danych pozostaje w połączonych dodatkowej pamięci podręcznej.
- Nie można zainicjować [operacji skalowania](cache-how-to-scale.md) na obu pamięci podręcznej lub [zmienić liczbę fragmentów](cache-how-to-premium-clustering.md) Jeśli pamięć podręczna zawiera klastrowanie włączone.
- Nie można włączyć trwałości na obu pamięci podręcznej.
- Można użyć [wyeksportować](cache-how-to-import-export-data.md#export) z albo pamięcią podręczną, ale możesz tylko [importu](cache-how-to-import-export-data.md#import) do głównej połączonego pamięci podręcznej.
- Nie można usunąć połączonej pamięci podręcznej lub grupy zasobów, którym się znajdują, przed usunięciem łącza replikacji geograficznej. Aby uzyskać więcej informacji, zobacz [Dlaczego operacji nie próba Usuń Moje połączonego pamięci podręcznej?](#why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache)
- W przypadku dwóch pamięci podręcznych w różnych regionach, koszty wyjście sieci dotyczą dane replikowane w regionach do połączonego dodatkowej pamięci podręcznej. Aby uzyskać więcej informacji, zobacz [ile kosztuje replikowanie danych w regionach platformy Azure?](#how-much-does-it-cost-to-replicate-my-data-across-azure-regions)
- Jest nie automatycznej pracy awaryjnej do połączonego dodatkowej pamięci podręcznej, jeśli podstawowy pamięci podręcznej (i jego repliką) przestaną działać. Aby aplikacje klienckie trybu failover należy ręcznie usunąć łącza replikacji geograficznej i aplikacje klienckie do pamięci podręcznej, który był wcześniej dodatkowej pamięci podręcznej połączonego punktu. Aby uzyskać więcej informacji, zobacz [jak działa awarii do połączonego dodatkowej pamięci podręcznej?](#how-does-failing-over-to-the-secondary-linked-cache-work)

## <a name="add-a-geo-replication-link"></a>Dodaj łącze — replikacja geograficzna

1. Aby połączyć dwie premium pamięci podręcznych za replikację geograficzną, kliknij przycisk **— replikacja geograficzna** z menu zasobów pamięci podręcznej pełnić rolę serwera podstawowego połączone pamięci podręcznej, a następnie kliknij przycisk **łącza replikacji pamięci podręcznej Dodaj** z **— replikacja geograficzna** bloku.

    ![Dodaj łącze](./media/cache-how-to-geo-replication/cache-geo-location-menu.png)

2. Kliknij nazwę żądanego dodatkowej pamięci podręcznej z **zgodne pamięci podręcznych** listy. Jeśli żądany pamięci podręcznej nie jest wyświetlana na liście, upewnij się, że [wymagania wstępne — replikacja geograficzna](#geo-replication-prerequisites) spełnione są odpowiednie dodatkowej pamięci podręcznej. Aby filtrować pamięci podręcznych według regionu, kliknij odpowiedni region na mapie, aby wyświetlić tylko tych pamięci podręcznych w **zgodne pamięci podręcznych** listy.

    ![Replikacja geograficzna zgodne pamięci podręczne](./media/cache-how-to-geo-replication/cache-geo-location-select-link.png)
    
    Można również zainicjować proces łączenia lub wyświetlić szczegóły dotyczące dodatkowej pamięci podręcznej za pomocą menu kontekstowego.

    ![Menu kontekstowe — replikacja geograficzna](./media/cache-how-to-geo-replication/cache-geo-location-select-link-context-menu.png)

3. Kliknij przycisk **łącze** Połącz dwa pamięci podręcznych i rozpocząć proces replikacji.

    ![Pamięci podręczne łącza](./media/cache-how-to-geo-replication/cache-geo-location-confirm-link.png)

4. Postęp procesu replikacji można wyświetlić na **— replikacja geograficzna** bloku.

    ![Łączenie stanu](./media/cache-how-to-geo-replication/cache-geo-location-linking.png)

    Można również wyświetlić stan połączeń na **omówienie** bloku dla buforów podstawowego i pomocniczego.

    ![Stan pamięci podręcznej](./media/cache-how-to-geo-replication/cache-geo-location-link-status.png)

    Po zakończeniu procesu replikacji **Link stanu** zmienia się na **zakończyło się pomyślnie**.

    ![Stan pamięci podręcznej](./media/cache-how-to-geo-replication/cache-geo-location-link-successful.png)

    Podczas procesu łączenia głównej połączonego pamięci podręcznej pozostaje dostępna do użycia, ale dodatkowej połączonego bufor nie jest dostępny, dopóki nie zakończy proces łączenia.

## <a name="remove-a-geo-replication-link"></a>Usuń łącze — replikacja geograficzna

1. Kliknij, aby usunąć połączenie między dwoma pamięci podręcznych i zatrzymać replikację geograficzną **odłączyć pamięci podręcznych** z **— replikacja geograficzna** bloku.
    
    ![Odłącz pamięci podręczne](./media/cache-how-to-geo-replication/cache-geo-location-unlink.png)

    Po zakończeniu procesu rozłączanie dodatkowej pamięci podręcznej jest dostępna dla odczytów i zapisów.

>[!NOTE]
>Usunięcie łącza replikacji geograficznej replikowane dane z głównej połączonego pamięci podręcznej pozostaje w dodatkowej pamięci podręcznej.
>
>

## <a name="geo-replication-faq"></a>Replikacja geograficzna — często zadawane pytania

- [Replikacja geograficzna można używać z pamięci podręcznej warstwy standardowa lub Basic?](#can-i-use-geo-replication-with-a-standard-or-basic-tier-cache)
- [Moje pamięci podręcznej jest dostępny do użytku podczas procesu łączenia lub odłączanie?](#is-my-cache-available-for-use-during-the-linking-or-unlinking-process)
- [Czy można połączyć więcej niż dwa pamięci podręcznych razem?](#can-i-link-more-than-two-caches-together)
- [Czy można połączyć dwóch pamięci podręczne z różnych subskrypcji platformy Azure?](#can-i-link-two-caches-from-different-azure-subscriptions)
- [Czy można połączyć dwóch buforów o różnych rozmiarach?](#can-i-link-two-caches-with-different-sizes)
- [Replikacja geograficzna można używać z włączoną funkcją klastrowania?](#can-i-use-geo-replication-with-clustering-enabled)
- [Replikacja geograficzna można używać z mojej pamięci podręcznych w sieci Wirtualnej?](#can-i-use-geo-replication-with-my-caches-in-a-vnet)
- [Co to jest harmonogram replikacji replikacja geograficzna Redis?](#what-is-the-replication-schedule-for-redis-geo-replication)
- [Jak długo replikacji — replikacja geograficzna podąża?](#how-long-does-geo-replication-replication-take)
- [Punkt odzyskiwania replikacji jest gwarantowana?](#is-the-replication-recovery-point-guaranteed)
- [Do zarządzania — replikacja geograficzna może używać programu PowerShell lub interfejsu wiersza polecenia Azure?](#can-i-use-powershell-or-azure-cli-to-manage-geo-replication)
- [Ile kosztuje replikowanie danych w regionach platformy Azure?](#how-much-does-it-cost-to-replicate-my-data-across-azure-regions)
- [Dlaczego operacji nie próba Usuń Moje połączonego pamięci podręcznej?](#why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache)
- [Jakie region Użyj mojej dodatkowej pamięci podręcznej połączonego](#what-region-should-i-use-for-my-secondary-linked-cache)
- [Jak działa awarii do połączonego dodatkowej pamięci podręcznej?](#how-does-failing-over-to-the-secondary-linked-cache-work)

### <a name="can-i-use-geo-replication-with-a-standard-or-basic-tier-cache"></a>Replikacja geograficzna można używać z pamięci podręcznej warstwy standardowa lub Basic?

Nie, replikacja geograficzna dostępnej tylko dla pamięci podręcznej warstwy Premium.

### <a name="is-my-cache-available-for-use-during-the-linking-or-unlinking-process"></a>Moje pamięci podręcznej jest dostępny do użytku podczas procesu łączenia lub odłączanie?

- Gdy łączący dwa pamięci podręcznych za replikację geograficzną, podstawowego połączonego pamięci podręcznej pozostaje dostępna do użycia, ale dodatkowej połączonego bufor nie jest dostępny, dopóki nie zakończy proces łączenia.
- Podczas usuwania łącza replikacji geograficznej między dwoma pamięci podręcznych, zarówno pamięci podręcznych pozostają dostępne do użycia.

### <a name="can-i-link-more-than-two-caches-together"></a>Czy można połączyć więcej niż dwa pamięci podręcznych razem?

Nie, używając — replikacja geograficzna można połączyć tylko dwa pamięci podręcznych razem.

### <a name="can-i-link-two-caches-from-different-azure-subscriptions"></a>Czy można połączyć dwóch pamięci podręczne z różnych subskrypcji platformy Azure?

Pamięci podręcznych, nie musi być w tej samej subskrypcji platformy Azure.

### <a name="can-i-link-two-caches-with-different-sizes"></a>Czy można połączyć dwóch buforów o różnych rozmiarach?

Tak, jak połączonych dodatkowej pamięci podręcznej jest większy niż podstawowy połączonego pamięci podręcznej.

### <a name="can-i-use-geo-replication-with-clustering-enabled"></a>Replikacja geograficzna można używać z włączoną funkcją klastrowania?

Tak, jak długo zarówno pamięci podręcznych ma taką samą liczbę fragmentów.

### <a name="can-i-use-geo-replication-with-my-caches-in-a-vnet"></a>Replikacja geograficzna można używać z mojej pamięci podręcznych w sieci Wirtualnej?

Tak, replikacja geograficzna pamięci podręcznych w sieci wirtualnych są obsługiwane. 

- Replikacja geograficzna między pamięci podręcznych w tej samej sieci Wirtualnej jest obsługiwane.
- Replikacja geograficzna między pamięci podręcznych w różnych sieci wirtualnych jest również obsługiwany, jak długo dwie sieci wirtualne są skonfigurowane w taki sposób, że zasoby w sieci wirtualne są dostęp do siebie za pośrednictwem połączeń TCP.

### <a name="what-is-the-replication-schedule-for-redis-geo-replication"></a>Co to jest harmonogram replikacji replikacja geograficzna Redis?

Replikacja odbywa się zgodnie z określonym harmonogramem jest tj ciągłe i asynchroniczne wszystkie zapisy gotowe do podstawowych natychmiast asynchronicznie są replikowane na serwerze pomocniczym.

### <a name="how-long-does-geo-replication-replication-take"></a>Jak długo replikacji — replikacja geograficzna podąża?

Replikacja jest przyrostowy, asynchroniczne i ciągłe i czas trwania zazwyczaj nie jest znacznie różne od opóźnień w regionach. W pewnych okolicznościach w pewnych porach pomocniczej musiał wykonać pełną synchronizację danych z serwera podstawowego. W takim przypadku podczas replikacji jest zależny od wielu czynników, takich jak: obciążenie głównej pamięci podręcznej przepustowość dostępną na komputerze pamięci podręcznej między opóźnienia region itp. Na przykład niektórych testów, które znaleziono limit tego czasu replikacji dla pełnej 53 GB z replikacją geograficzną Sparuj w Azji nam i regiony zachodnie stany USA, może być dowolnym od 5 do 10 minut.

### <a name="is-the-replication-recovery-point-guaranteed"></a>Punkt odzyskiwania replikacji jest gwarantowana?

Obecnie do pamięci podręcznej w trybie replikacją geograficzną, funkcji trwałości i importu/eksportu jest wyłączona. Dlatego w przypadku klienta inicjowane pracy awaryjnej lub w przypadkach, gdy łącze replikacji zostało przerwane między replikacją geograficzną pary pomocniczy zostaną zachowane w pamięci danych, który jest synchronizowany z serwera podstawowego, do tego punktu w czasie. Nie ma żadnej gwarancji punktu odzyskiwania, w takich sytuacjach.

### <a name="can-i-use-powershell-or-azure-cli-to-manage-geo-replication"></a>Do zarządzania — replikacja geograficzna może używać programu PowerShell lub interfejsu wiersza polecenia Azure?

W tej chwili można zarządzać tylko replikacja geograficzna, przy użyciu portalu Azure.

### <a name="how-much-does-it-cost-to-replicate-my-data-across-azure-regions"></a>Ile kosztuje replikowanie danych w regionach platformy Azure?

Używając — replikacja geograficzna, dane z głównej połączonego pamięci podręcznej są replikowane do dodatkowej połączonego pamięci podręcznej. Jeśli dwa połączone pamięci podręcznych znajdują się w tym samym regionie Azure, jest bezpłatna do transferu danych. Jeśli dwa połączone pamięci podręcznych znajdują się w różnych regionach platformy Azure, opłat transfer danych — replikacja geograficzna jest kosztów przepustowości replikacji danych do innego regionu systemu Azure. Aby uzyskać więcej informacji, zobacz [szczegóły cennika przepustowości](https://azure.microsoft.com/pricing/details/bandwidth/).

### <a name="why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache"></a>Dlaczego operacji nie próba Usuń Moje połączonego pamięci podręcznej?

Gdy dwa pamięci podręcznych są połączone ze sobą, nie można usunąć pamięci podręcznej lub grupę zasobów, która zawiera je przed usunięciem łącza replikacji geograficznej. Jeśli próbujesz usunąć grupę zasobów, która zawiera jedną lub obie połączonych buforów, inne zasoby w grupie zasobów zostaną usunięte, lecz pozostaje grupy zasobów w `deleting` stanu i wszystkie połączone pamięci podręcznej w grupie zasobów pozostają w `running`stanu. Aby ukończyć usuwanie grupy zasobów i połączone pamięci podręcznych w nim, przerwać łącze replikacji geograficznej zgodnie z opisem w [Usuń łącze replikacji geograficznej](#remove-a-geo-replication-link).

### <a name="what-region-should-i-use-for-my-secondary-linked-cache"></a>Jakie region Użyj mojej dodatkowej pamięci podręcznej połączonego

Ogólnie rzecz biorąc zaleca się dla istnieje w tym samym regionie Azure, jak aplikacja, która uzyskuje on dostęp do pamięci podręcznej. Jeśli aplikacja ma regionu podstawowego i rezerwowej, pamięci podręcznych podstawowych i pomocniczych powinna istnieć w tym samym regionach. Aby uzyskać więcej informacji na temat sparowanego regionów, zobacz [najlepszych rozwiązań — regiony platformy Azure sparowanym](../best-practices-availability-paired-regions.md).

### <a name="how-does-failing-over-to-the-secondary-linked-cache-work"></a>Jak działa awarii do połączonego dodatkowej pamięci podręcznej?

W wersji początkowej replikacji geograficznej pamięć podręczna Redis Azure nie obsługuje automatycznej pracy awaryjnej w regionach platformy Azure. Replikacja geograficzna jest używany głównie w przypadku odzyskiwania po awarii. W przypadku odzyskiwania distater klientów należy wyświetlić całego stosu aplikacji w regionie kopii zapasowej w skoordynowany sposób zamiast umożliwienie poszczególnych składników aplikacji zadecydować o czasie przełączyć się do ich kopie zapasowe na ich własnych. Jest to szczególnie istotne w pamięci podręcznej Redis. Jedną z kluczowych zalet Redis jest magazynem bardzo małe opóźnienia. Jeśli używany przez aplikację pamięci podręcznej Redis awaryjnie w innym regionie Azure, ale nie będzie warstwie obliczeniowej, czas obiegu dodano mają zauważalnego wpływu na wydajność. Z tego powodu chcemy uniknąć niepowodzenie Redis za pośrednictwem automatycznie z powodu problemów dotyczących dostępności przejściowej.

Aktualnie Aby zainicjować trybu failover, należy Usuń łącze replikacji geograficznej w portalu Azure, a następnie zmień punktu końcowego połączenia klienta pamięci podręcznej Redis z głównej połączonego pamięci podręcznej (dawniej połączonych) dodatkowej pamięci podręcznej. Gdy dwa pamięci podręcznych są usunąć skojarzenia, replika staje się regularne pamięci podręcznej odczytu i zapisu ponownie i akceptuje żądania bezpośrednio od klientów pamięci podręcznej Redis.


## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o [warstwy pamięci podręcznej Redis Azure Premium](cache-premium-tier-intro.md).

