---
title: Jak skonfigurować replikację geograficzną dla usługi Azure Cache dla pamięci podręcznej Redis | Dokumentacja firmy Microsoft
description: Dowiedz się, jak replikować pamięci podręcznej dla wystąpień pamięci podręcznej Redis Azure w regionach geograficznych.
services: cache
documentationcenter: ''
author: yegu-ms
manager: jhubbard
editor: ''
ms.assetid: 375643dc-dbac-4bab-8004-d9ae9570440d
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache
ms.devlang: na
ms.topic: article
ms.date: 03/06/2019
ms.author: yegu
ms.openlocfilehash: 4254175955c3560c7bd0fdd08c6b60c318238b76
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60552355"
---
# <a name="how-to-configure-geo-replication-for-azure-cache-for-redis"></a>Jak skonfigurować replikację geograficzną dla usługi Azure Cache dla pamięci podręcznej Redis

Replikacja geograficzna udostępnia mechanizm do łączenia dwóch warstwa Premium usługi Azure Cache wystąpienia usługi Redis. Co pamięć podręczna zostanie wybrany jako podstawowy połączonej pamięci podręcznej, a drugi jako pomocniczy połączonej pamięci podręcznej. Pomocniczy połączonej pamięci podręcznej staje się tylko do odczytu, a dane zapisywane w głównej pamięci podręcznej są replikowane do dodatkowej połączonej pamięci podręcznej. Ta funkcja może służyć do replikowania pamięci podręcznej w różnych regionach platformy Azure. Ten artykuł zawiera przewodnik dotyczący konfigurowania replikacji geograficznej w warstwie Premium usługi Azure Cache dla wystąpień usługi Redis.

## <a name="geo-replication-prerequisites"></a>Wymagania wstępne replikacji geograficznej

Aby skonfigurować replikację geograficzną między dwoma pamięci podręcznych, muszą być spełnione następujące wymagania wstępne:

- Zarówno pamięci podręczne są [w warstwie Premium](cache-premium-tier-intro.md) zapisuje w pamięci podręcznej.
- Zarówno pamięci podręcznych znajdują się w tej samej subskrypcji platformy Azure.
- Pomocniczy połączonej pamięci podręcznej jest taki sam rozmiar pamięci podręcznej lub większy rozmiar pamięci podręcznej niż podstawowy połączonej pamięci podręcznej.
- Zarówno pamięci podręczne są tworzone w stanie uruchomienia.

Niektóre funkcje nie są obsługiwane za pomocą replikacji geograficznej:

- Stan trwały nie jest obsługiwana przy użyciu replikacji geograficznej.
- Klaster jest obsługiwany, jeśli obie pamięci podręcznych ma włączone klastrowanie i ma taką samą liczbę fragmentów.
- Pamięci podręczne w tej samej sieci Wirtualnej są obsługiwane.
- Pamięci podręczne w różnych sieciach wirtualnych są obsługiwane przy użyciu ostrzeżenia. Zobacz [korzystać replikację geograficzną za pomocą mojej pamięci podręczne w sieci Wirtualnej?](#can-i-use-geo-replication-with-my-caches-in-a-vnet) Aby uzyskać więcej informacji.

Po skonfigurowaniu replikacji geograficznej do pary usługi połączonej pamięci podręcznej są stosowane następujące ograniczenia:

- Pomocniczy połączonej pamięci podręcznej jest tylko do odczytu. można odczytać z niego, ale nie można zapisać danych do niego. 
- Wszelkie dane, które były w dodatkowej połączonej pamięci podręcznej, przed dodaniem linku jest usuwany. Jeśli replikacja geograficzna zostanie później usunięta jednak replikowane dane pozostają w dodatkowej połączonej pamięci podręcznej.
- Nie można [skalowania](cache-how-to-scale.md) albo pamięci podręcznej, gdy są połączone pamięci podręcznych.
- Nie można [zmienić liczbę fragmentów](cache-how-to-premium-clustering.md) Jeśli włączonym klastrowaniu pamięci podręcznej.
- Nie można włączyć trwałość na obu pamięci podręcznej.
- Możesz [wyeksportować](cache-how-to-import-export-data.md#export) z obu pamięci podręcznej.
- Nie można [importu](cache-how-to-import-export-data.md#import) do dodatkowej połączonej pamięci podręcznej.
- Nie można usunąć połączonej pamięci podręcznej lub grupy zasobów, w którym się znajdują, dopóki pamięci podręczne. Aby uzyskać więcej informacji, zobacz [dlaczego operacja kończy się niepowodzeniem podczas próby usunięcia Moje połączonej pamięci podręcznej?](#why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache)
- W przypadku pamięci podręcznych w różnych regionach, kosztów sieciowego ruchu wychodzącego dotyczą danych przenoszone między regionami. Aby uzyskać więcej informacji, zobacz [ile kosztuje do replikacji danych między regionami platformy Azure?](#how-much-does-it-cost-to-replicate-my-data-across-azure-regions)
- Automatyczny tryb failover nie zachodzi między podstawowych i pomocniczych połączonej pamięci podręcznej. Aby uzyskać więcej informacji i uzyskać informacje na temat trybu failover aplikacja kliencka, zobacz [przechodzenie w tryb failover do pomocniczej pamięci podręcznej połączone działanie?](#how-does-failing-over-to-the-secondary-linked-cache-work)

## <a name="add-a-geo-replication-link"></a>Dodaj link replikacji geograficznej

1. Połączyć dwa pamięci podręcznych razem dla replikacji geograficznej, pierwszej kliknij **Geografickou replikaci** przejdź do menu zasobu w pamięci podręcznej ma być podstawowy połączonej pamięci podręcznej. Następnie kliknij przycisk **Dodaj link replikacji pamięci podręcznej** z **Geografickou replikaci** bloku.

    ![Dodaj łącze](./media/cache-how-to-geo-replication/cache-geo-location-menu.png)

2. Kliknij nazwę zamierzony pomocniczej pamięci podręcznej z **zgodne pamięci podręczne** listy. Jeśli Twoje pomocniczej pamięci podręcznej nie jest wyświetlana na liście, upewnij się, że [wymagań wstępnych replikacji geograficznej](#geo-replication-prerequisites) dla pomocniczej pamięci podręcznej są spełnione. Aby filtrować pamięci podręcznych według regionu, kliknij region na mapie, aby wyświetlić tylko te pamięci podręczne w regionie **zgodne pamięci podręczne** listy.

    ![Zgodne pamięci podręczne replikacji geograficznej](./media/cache-how-to-geo-replication/cache-geo-location-select-link.png)
    
    Można również rozpocząć proces łączenia lub wyświetlić szczegółowe informacje o pomocniczej pamięci podręcznej za pomocą menu kontekstowego.

    ![Menu kontekstowe replikacji geograficznej](./media/cache-how-to-geo-replication/cache-geo-location-select-link-context-menu.png)

3. Kliknij przycisk **łącze** łączą się ze sobą dwie pamięci podręczne i rozpocząć proces replikacji.

    ![Pamięci podręczne łącza](./media/cache-how-to-geo-replication/cache-geo-location-confirm-link.png)

4. Możesz wyświetlić postęp procesu replikacji na **Geografickou replikaci** bloku.

    ![Stan połączeń](./media/cache-how-to-geo-replication/cache-geo-location-linking.png)

    Można również wyświetlić stan połączeń na **Przegląd** bloku podstawowego i pomocniczego buforów.

    ![Stan pamięci podręcznej](./media/cache-how-to-geo-replication/cache-geo-location-link-status.png)

    Po zakończeniu procesu replikacji **Link stanu** zmieni się na **Powodzenie**.

    ![Stan pamięci podręcznej](./media/cache-how-to-geo-replication/cache-geo-location-link-successful.png)

    Podstawowy połączonej pamięci podręcznej pozostaje dostępna do użycia podczas łączenia. Pomocniczy połączonej pamięci podręcznej jest niedostępna, dopóki nie zakończy proces łączenia.

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

- Podczas łączenia, podstawowy połączonej pamięci podręcznej pozostaje dostępna, gdy kończy proces łączenia.
- Podczas łączenia, dodatkowej połączonej pamięci podręcznej jest niedostępna, dopóki nie zakończy proces łączenia.
- Podczas odłączania, zarówno pamięci podręcznych pozostają dostępne podczas kończenia odłączanie procesu.

### <a name="can-i-link-more-than-two-caches-together"></a>Czy można połączyć więcej niż dwóch pamięci podręcznych razem?

Nie można połączyć tylko dwa pamięci podręcznych ze sobą.

### <a name="can-i-link-two-caches-from-different-azure-subscriptions"></a>Czy można połączyć dwa pamięci podręcznych z różnych subskrypcji platformy Azure?

Pamięci podręczne, nie musi być w tej samej subskrypcji platformy Azure.

### <a name="can-i-link-two-caches-with-different-sizes"></a>Czy można połączyć dwa pamięci podręczne o różnych rozmiarach?

Tak, tak długo, jak dodatkowej połączonej pamięci podręcznej jest większy niż podstawowy połączonej pamięci podręcznej.

### <a name="can-i-use-geo-replication-with-clustering-enabled"></a>Przy włączonym klastrowaniu można używać replikacji geograficznej?

Tak, tak długo, jak zarówno pamięci podręcznych ma taką samą liczbę fragmentów.

### <a name="can-i-use-geo-replication-with-my-caches-in-a-vnet"></a>Z mojej pamięciami podręcznymi w sieci Wirtualnej można używać replikacji geograficznej?

Tak, replikacja geograficzna pamięci podręczne w sieciach wirtualnych jest obsługiwany ostrzeżenia:

- Replikacja geograficzna między pamięci podręczne w tej samej sieci Wirtualnej jest obsługiwana.
- Replikacja geograficzna między pamięciami podręcznymi w różnych sieciach wirtualnych jest również obsługiwany.
  - Jeśli te sieci wirtualne znajdują się w tym samym regionie, należy utworzyć połączenie między nimi przy użyciu [komunikacja równorzędna sieci wirtualnych](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) lub [połączenia bramy sieci VPN sieci Wirtualnej między SIECIAMI wirtualnymi](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways#V2V).
  - Jeśli te sieci wirtualne znajdują się w różnych regionach, replikację geograficzną za pomocą komunikacji równorzędnej nie jest obsługiwane ze względu na ograniczenia przy użyciu podstawowych wewnętrznych modułów równoważenia obciążenia. Aby uzyskać więcej informacji na temat ograniczeń komunikacji równorzędnej sieci Wirtualnej, zobacz [sieci wirtualnej — komunikacja równorzędna — wymagania i ograniczenia](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering#requirements-and-constraints). Zalecanym rozwiązaniem jest używanie połączenia bramy sieci VPN sieci Wirtualnej między SIECIAMI wirtualnymi.

Za pomocą [ten szablon Azure](https://azure.microsoft.com/resources/templates/201-redis-vnet-geo-replication/), można szybko wdrożyć dwóch buforów z replikacją geograficzną w sieci Wirtualnej, połączone za pomocą połączenia bramy sieci VPN sieci Wirtualnej między SIECIAMI wirtualnymi.

### <a name="what-is-the-replication-schedule-for-redis-geo-replication"></a>Co to jest harmonogram replikacji dla replikacji geograficznej usługi Redis?

Replikacja jest ciągłe i asynchroniczne i nie jest realizowane zgodnie z określonym harmonogramem. Wszystkie zapisy, które są gotowe do podstawowej są natychmiast i asynchronicznie replikowane na serwerze pomocniczym.

### <a name="how-long-does-geo-replication-replication-take"></a>Jak replikacja geografickou replikaci długo?

Replikacja jest przyrostowy, asynchronicznego i ciągłe i czas nie jest znacznie różni się od opóźnienia między regionami. W pewnych okolicznościach pomocnicza pamięć podręczna może być konieczne wykonać pełną synchronizację danych z serwera podstawowego. W takim przypadku podczas replikacji jest zależne od szeregu czynników, takich jak: obciążenia głównej pamięci podręcznej, dostępnej przepustowości i opóźnienia między regionami. Znaleźliśmy czasu replikacji dla pary replikowanej geograficznie 53GB, może być dowolnym miejscu od 5 do 10 minut.

### <a name="is-the-replication-recovery-point-guaranteed"></a>Gwarantuje punktu odzyskiwania replikacji

Dla pamięci podręcznej w trybie z replikacją geograficzną trwałości jest wyłączona. W przypadku odłączone, takie jak tryb failover inicjowane przez klienta parę replikowanej geograficznie pomocniczej połączonej pamięci podręcznej przechowuje zsynchronizowane dane do tego punktu w czasie. Brak punktu odzyskiwania jest gwarantowane w takich sytuacjach.

Aby uzyskać punkt odzyskiwania [wyeksportować](cache-how-to-import-export-data.md#export) z obu pamięci podręcznej. Możesz później [importu](cache-how-to-import-export-data.md#import) do głównej połączonej pamięci podręcznej.

### <a name="can-i-use-powershell-or-azure-cli-to-manage-geo-replication"></a>Do zarządzania replikacją geograficzną można użyć programu PowerShell lub wiersza polecenia platformy Azure?

Tak, replikacji geograficznej mogą być zarządzane przy użyciu witryny Azure portal, programu PowerShell lub wiersza polecenia platformy Azure. Aby uzyskać więcej informacji, zobacz [dokumentów dotyczących programu PowerShell](https://docs.microsoft.com/powershell/module/az.rediscache/?view=azps-1.4.0#redis_cache) lub [wiersza polecenia platformy Azure docs](https://docs.microsoft.com/cli/azure/redis/server-link?view=azure-cli-latest).

### <a name="how-much-does-it-cost-to-replicate-my-data-across-azure-regions"></a>Ile kosztuje do replikacji danych między regionami platformy Azure?

Korzystając z replikacji geograficznej, dane z podstawowego połączonej pamięci podręcznej są replikowane do dodatkowej połączonej pamięci podręcznej. Brak opłat za transfer danych, jeśli dwa połączonej pamięci podręcznej znajdują się w tym samym regionie. Jeśli dwa połączonej pamięci podręcznej znajdują się w różnych regionach, opłata za transfer danych jest koszt wychodzącego sieci danych wysyłanych w dowolnym regionie. Aby uzyskać więcej informacji, zobacz [przepustowość — szczegóły cennika](https://azure.microsoft.com/pricing/details/bandwidth/).

### <a name="why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache"></a>Dlaczego operacja kończy się niepowodzeniem podczas próby usunięcia Moje połączonej pamięci podręcznej?

Nie można usunąć pamięci podręcznych z replikacją geograficzną i ich grup zasobów, gdy połączone, dopóki nie usuniesz link replikacji geograficznej. Jeśli spróbujesz usunąć grupę zasobów, który zawiera jedną lub obie połączone pamięci podręcznych innych zasobów w grupie zasobów zostaną usunięte, ale grupa zasobów pozostaje w `deleting` stanu i wszystkie połączone pamięci podręczne w grupie zasobów, pozostają w `running`stanu. Aby całkowicie usunąć grupę zasobów wraz z połączonej pamięci podręczne w nim, Rozłącz pamięci podręczne, zgodnie z opisem w [Usuń link replikacji geograficznej](#remove-a-geo-replication-link).

### <a name="what-region-should-i-use-for-my-secondary-linked-cache"></a>Jakie regionie Użyj mojej dodatkowej połączonej pamięci podręcznej

Ogólnie rzecz biorąc zaleca się dla pamięci podręcznej istnieje w tym samym regionie platformy Azure, co aplikacja, która uzyskuje do niej dostęp. Zalecane jest, aby w przypadku aplikacji przy użyciu oddzielnych regionach podstawowych i rezerwowego pamięci podręcznych podstawowych i pomocniczych występują w tych regionach tego samego. Aby uzyskać więcej informacji na temat sparowanych regionów, zobacz [najlepsze rozwiązania — Azure sparowane regiony](../best-practices-availability-paired-regions.md).

### <a name="how-does-failing-over-to-the-secondary-linked-cache-work"></a>Jak działa przechodzenie w tryb failover do pomocniczej połączonej pamięci podręcznej?

Automatycznej pracy awaryjnej w różnych regionach platformy Azure nie jest obsługiwana w przypadku replikowanej geograficznie pamięci podręcznych. W przypadku scenariusza odzyskiwania po awarii klientom należy przywołać całego stosu aplikacji w sposób skoordynowany w ich kopii zapasowej regionie. Umożliwienie poszczególnych aplikacji, które składniki zdecyduj, kiedy należy przełączyć się do ich kopie zapasowe własnych niekorzystnie wpłynąć na wydajność. Jedną z kluczowych zalet usługi Redis jest on magazynu bardzo małym opóźnieniem. W przypadku aplikacji głównej klienta znajduje się w regionie innym niż jego pamięci podręcznej, dodano czas błądzenia musi zauważalnego wpływu na wydajność. Z tego powodu firma Microsoft uniknąć automatycznego przechodzenia w tryb failover z powodu problemów dotyczących dostępności przejściowy.

Aby uruchomić tryb failover inicjowane przez klienta, najpierw należy odłączyć pamięci podręcznych. Następnie zmień klienta pamięci podręcznej Redis, aby używać punkt końcowy połączenia pomocniczej pamięci podręcznej (dawniej połączone). Po dwóch pamięci podręczne nie są połączone, pomocnicza pamięć podręczna stanie się ponownie z regularnych pamięci podręcznej odczytu / zapisu i akceptuje żądania bezpośrednio od klientów usługi Redis.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o [Azure pamięci podręcznej Redis w warstwie Premium](cache-premium-tier-intro.md).
