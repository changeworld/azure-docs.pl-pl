---
title: Jak skonfigurować replikację geograficzną dla usługi Azure Cache for Redis | Dokumenty firmy Microsoft
description: Dowiedz się, jak replikować pamięć podręczną platformy Azure dla wystąpień Redis w regionach geograficznych.
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 03/06/2019
ms.author: yegu
ms.openlocfilehash: ce50c665fa79c361f638fda4ec373d5215c407f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74129417"
---
# <a name="how-to-set-up-geo-replication-for-azure-cache-for-redis"></a>Jak skonfigurować replikację geograficzną dla usługi Azure Cache for Redis

Replikacja geograficzna zapewnia mechanizm łączenia dwóch warstwy Premium azure cache dla wystąpień Redis. Jedna pamięć podręczna jest wybierana jako podstawowa połączona pamięć podręczna, a druga jako pomocnicza połączona pamięć podręczna. Pomocnicza połączona pamięć podręczna staje się tylko do odczytu, a dane zapisane w podstawowej pamięci podręcznej są replikowane do pomocniczej połączonej pamięci podręcznej. Ta funkcja może służyć do replikowania pamięci podręcznej w regionach platformy Azure. Ten artykuł zawiera przewodnik dotyczący konfigurowania replikacji geograficznej dla warstwy Premium w pamięci podręcznej platformy Azure dla wystąpień Redis.

## <a name="geo-replication-prerequisites"></a>Wymagania wstępne replikacji geograficznej

Aby skonfigurować replikację geograficzną między dwiema pamięciami podręcznymi, muszą być spełnione następujące wymagania wstępne:

- Obie pamięci podręczne są pamięciami podręcznymi [warstwy Premium.](cache-premium-tier-intro.md)
- Obie pamięci podręczne są w tej samej subskrypcji platformy Azure.
- Pomocnicza połączona pamięć podręczna ma ten sam rozmiar pamięci podręcznej lub większy rozmiar pamięci podręcznej niż podstawowa połączona pamięć podręczna.
- Obie pamięci podręczne są tworzone i w stanie uruchomionym.

Niektóre funkcje nie są obsługiwane w przypadku replikacji geograficznej:

- Trwałość nie jest obsługiwana w przypadku replikacji geograficznej.
- Klastrowanie jest obsługiwane, jeśli obie pamięci podręczne mają włączone klastrowanie i mają taką samą liczbę fragmentów.
- Obsługiwane są bufory w tej samej sieci wirtualnej.
- Pamięci podręczne w różnych sieciach wirtualnych są obsługiwane z zastrzeżeniami. Aby uzyskać więcej [informacji, zobacz Czy mogę używać replikacji geograficznej z pamięciami podręcznymi w sieci wirtualnej?](#can-i-use-geo-replication-with-my-caches-in-a-vnet)

Po skonfigurowaniu replikacji geograficznej do połączonej pary pamięci podręcznej obowiązują następujące ograniczenia:

- Pomocnicza połączona pamięć podręczna jest tylko do odczytu; można z niego odczytać, ale nie możesz zapisać na nim żadnych danych. 
- Wszystkie dane, które znajdowały się w pomocniczej połączonej pamięci podręcznej przed dodaniem łącza, zostaną usunięte. Jeśli jednak replikacja geograficzna zostanie później usunięta, zreplikowane dane pozostaną w pomocniczej połączonej pamięci podręcznej.
- Nie można [skalować](cache-how-to-scale.md) żadnej pamięci podręcznej, gdy bufory są połączone.
- Nie można [zmienić liczby fragmentów,](cache-how-to-premium-clustering.md) jeśli pamięć podręczna ma włączone klastrowanie.
- Nie można włączyć trwałości w żadnej z pamięci podręcznej.
- Można [eksportować](cache-how-to-import-export-data.md#export) z każdej pamięci podręcznej.
- Nie można [zaimportować](cache-how-to-import-export-data.md#import) do pomocniczej połączonej pamięci podręcznej.
- Nie można usunąć połączonej pamięci podręcznej ani grupy zasobów, która je zawiera, dopóki nie odłączysz pamięci podręcznej. Aby uzyskać więcej informacji, zobacz [Dlaczego operacja nie powiodła się podczas próby usunięcia połączonej pamięci podręcznej?](#why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache)
- Jeśli pamięci podręczne znajdują się w różnych regionach, koszty transferu danych wychodzących sieci mają zastosowanie do danych przeniesionych między regionami. Aby uzyskać więcej informacji, zobacz [Ile kosztuje replikowanie moich danych w regionach platformy Azure?](#how-much-does-it-cost-to-replicate-my-data-across-azure-regions)
- Automatyczna funkcja trybu failover nie występuje między podstawową i pomocniczą połączonyą pamięcią podręczną. Aby uzyskać więcej informacji i informacji na temat pracy awaryjnej aplikacji klienckiej, zobacz [Jak działa niepowodzenie w stosunku do pomocniczej połączonej pamięci podręcznej?](#how-does-failing-over-to-the-secondary-linked-cache-work)

## <a name="add-a-geo-replication-link"></a>Dodawanie łącza replikacji geograficznej

1. Aby połączyć dwie pamięci podręczne w celu replikacji geograficznej, kliknij z pięścią opcję **Replikacja geograficzna** z menu Zasób pamięci podręcznej, która ma być podstawową połączonyą pamięcią podręczną. Następnie kliknij pozycję **Dodaj łącze replikacji pamięci podręcznej** z bloku **Replikacja geograficzna.**

    ![Dodaj link](./media/cache-how-to-geo-replication/cache-geo-location-menu.png)

2. Kliknij nazwę zamierzonej dodatkowej pamięci podręcznej z listy **Zgodne pamięci podręczne.** Jeśli pomocnicza pamięć podręczna nie jest wyświetlana na liście, sprawdź, czy są spełnione [wymagania wstępne replikacji](#geo-replication-prerequisites) geograficznej dla pomocniczej pamięci podręcznej. Aby filtrować pamięci podręczne według regionu, kliknij region na mapie, aby wyświetlić tylko te pamięci podręczne na liście **Zgodne pamięci podręczne.**

    ![Pamięci podręczne zgodne z replikacją geograficzną](./media/cache-how-to-geo-replication/cache-geo-location-select-link.png)
    
    Można również rozpocząć proces łączenia lub wyświetlić szczegóły dotyczące pomocniczej pamięci podręcznej przy użyciu menu kontekstowego.

    ![Menu kontekstowe replikacji geograficznej](./media/cache-how-to-geo-replication/cache-geo-location-select-link-context-menu.png)

3. Kliknij **przycisk Łącze,** aby połączyć dwie pamięci podręczne i rozpocząć proces replikacji.

    ![Bufory łączy](./media/cache-how-to-geo-replication/cache-geo-location-confirm-link.png)

4. Postęp procesu replikacji można wyświetlić w bloku **Replikacja geograficzna.**

    ![Stan łączenia](./media/cache-how-to-geo-replication/cache-geo-location-linking.png)

    Można również wyświetlić stan łączenia w **bloku Przegląd** dla pamięci podręcznej podstawowej i pomocniczej.

    ![Stan pamięci podręcznej](./media/cache-how-to-geo-replication/cache-geo-location-link-status.png)

    Po zakończeniu procesu replikacji **stan łącza** zmienia się na **Zakończony pomyślnie**.

    ![Stan pamięci podręcznej](./media/cache-how-to-geo-replication/cache-geo-location-link-successful.png)

    Podstawowa połączona pamięć podręczna pozostaje dostępna do użycia podczas procesu łączenia. Pomocnicza połączona pamięć podręczna nie jest dostępna, dopóki proces łączenia nie zostanie zakończony.

## <a name="remove-a-geo-replication-link"></a>Usuwanie łącza replikacji geograficznej

1. Aby usunąć łącze między dwiema pamięciami podręcznymi i zatrzymać replikację geograficzną, kliknij pozycję **Odłącz pamięć podręczną** od bloku **Replikacja geograficzna.**
    
    ![Odłączanie pamięci podręcznych](./media/cache-how-to-geo-replication/cache-geo-location-unlink.png)

    Po zakończeniu procesu odłączania pomocnicza pamięć podręczna jest dostępna zarówno dla odczytów, jak i zapisów.

>[!NOTE]
>Po usunięciu łącza replikacji geograficznej zreplikowane dane z podstawowej połączonej pamięci podręcznej pozostają w pomocniczej pamięci podręcznej.
>
>

## <a name="geo-replication-faq"></a>Często zadawane pytania dotyczące replikacji geograficznej

- [Czy można używać replikacji geograficznej z pamięcią podręczną warstwy Standardowa lub Podstawowa?](#can-i-use-geo-replication-with-a-standard-or-basic-tier-cache)
- [Czy moja pamięć podręczna jest dostępna do użycia podczas procesu łączenia lub odłączania?](#is-my-cache-available-for-use-during-the-linking-or-unlinking-process)
- [Czy mogę połączyć ze sobą więcej niż dwie pamięci podręczne?](#can-i-link-more-than-two-caches-together)
- [Czy mogę połączyć dwie pamięci podręczne z różnych subskrypcji platformy Azure?](#can-i-link-two-caches-from-different-azure-subscriptions)
- [Czy mogę połączyć dwie pamięci podręczne o różnych rozmiarach?](#can-i-link-two-caches-with-different-sizes)
- [Czy można używać replikacji geograficznej z włączonym klastrem?](#can-i-use-geo-replication-with-clustering-enabled)
- [Czy mogę używać replikacji geograficznej z pamięciami podręcznymi w sieci wirtualnej?](#can-i-use-geo-replication-with-my-caches-in-a-vnet)
- [Jaki jest harmonogram replikacji dla replikacji geograficznej Redis?](#what-is-the-replication-schedule-for-redis-geo-replication)
- [Jak długo trwa replikacja geograficzna?](#how-long-does-geo-replication-replication-take)
- [Czy gwarantowany jest punkt odzyskiwania replikacji?](#is-the-replication-recovery-point-guaranteed)
- [Czy mogę zarządzać replikacją geograficzną za pomocą programu PowerShell lub interfejsu wiersza polecenia platformy Azure?](#can-i-use-powershell-or-azure-cli-to-manage-geo-replication)
- [Ile kosztuje replikowanie moich danych w regionach platformy Azure?](#how-much-does-it-cost-to-replicate-my-data-across-azure-regions)
- [Dlaczego operacja nie powiodła się podczas próby usunięcia połączonej pamięci podręcznej?](#why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache)
- [Jakiego regionu należy używać w pomocniczej połączonej pamięci podręcznej?](#what-region-should-i-use-for-my-secondary-linked-cache)
- [Jak działa niepowodzenie w przypadku pomocniczej połączonej pamięci podręcznej?](#how-does-failing-over-to-the-secondary-linked-cache-work)

### <a name="can-i-use-geo-replication-with-a-standard-or-basic-tier-cache"></a>Czy można używać replikacji geograficznej z pamięcią podręczną warstwy Standardowa lub Podstawowa?

Nie, replikacja geograficzna jest dostępna tylko w pamięci podręcznej warstwy Premium.

### <a name="is-my-cache-available-for-use-during-the-linking-or-unlinking-process"></a>Czy moja pamięć podręczna jest dostępna do użycia podczas procesu łączenia lub odłączania?

- Podczas łączenia podstawowa połączona pamięć podręczna pozostaje dostępna po zakończeniu procesu łączenia.
- Podczas łączenia pomocnicza połączona pamięć podręczna nie jest dostępna, dopóki proces łączenia nie zostanie zakończony.
- Po odłączeniu, obie pamięci podręczne pozostają dostępne po zakończeniu procesu odłączania.

### <a name="can-i-link-more-than-two-caches-together"></a>Czy mogę połączyć ze sobą więcej niż dwie pamięci podręczne?

Nie, można połączyć ze sobą tylko dwie pamięci podręczne.

### <a name="can-i-link-two-caches-from-different-azure-subscriptions"></a>Czy mogę połączyć dwie pamięci podręczne z różnych subskrypcji platformy Azure?

Nie, obie pamięci podręczne muszą znajdować się w tej samej subskrypcji platformy Azure.

### <a name="can-i-link-two-caches-with-different-sizes"></a>Czy mogę połączyć dwie pamięci podręczne o różnych rozmiarach?

Tak, o ile pomocnicza połączona pamięć podręczna jest większa niż podstawowa połączona pamięć podręczna.

### <a name="can-i-use-geo-replication-with-clustering-enabled"></a>Czy można używać replikacji geograficznej z włączonym klastrem?

Tak, tak długo, jak obie pamięci podręczne mają taką samą liczbę fragmentów.

### <a name="can-i-use-geo-replication-with-my-caches-in-a-vnet"></a>Czy mogę używać replikacji geograficznej z pamięciami podręcznymi w sieci wirtualnej?

Tak, replikacja geograficzna pamięci podręcznej w sieciach wirtualnych jest obsługiwana z zastrzeżeniami:

- Replikacja geograficzna między pamięciami podręcznymi w tej samej sieci wirtualnej jest obsługiwana.
- Obsługiwana jest również replikacja geograficzna między pamięciami podręcznymi w różnych sieciach wirtualnych.
  - Jeśli sieci wirtualne znajdują się w tym samym regionie, można je połączyć za pomocą [komunikacji równorzędnej sieci wirtualnej](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) lub [połączenia sieci wirtualnej](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways#V2V)bramy sieci VPN.
  - Jeśli sieci wirtualne znajdują się w różnych regionach, replikacja geograficzna przy użyciu komunikacji równorzędnej sieci wirtualnej nie jest obsługiwana z powodu ograniczenia z podstawowymi wewnętrznymi modułami równoważenia obciążenia. Aby uzyskać więcej informacji na temat ograniczeń komunikacji równorzędnej sieci wirtualnej, zobacz [Sieć wirtualna — komunikacja równorzędna — wymagania i ograniczenia](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering#requirements-and-constraints). Zalecanym rozwiązaniem jest użycie połączenia sieci wirtualnej bramy sieci VPN z siecią wirtualną.

Za pomocą [tego szablonu platformy Azure](https://azure.microsoft.com/resources/templates/201-redis-vnet-geo-replication/)można szybko wdrożyć dwie replikowane geograficznie pamięci podręczne w sieci wirtualnej połączonej z połączeniem sieci wirtualnej sieci wirtualnej bramy sieci VPN.

### <a name="what-is-the-replication-schedule-for-redis-geo-replication"></a>Jaki jest harmonogram replikacji dla replikacji geograficznej Redis?

Replikacja jest ciągła i asynchroniczna i nie odbywa się zgodnie z określonym harmonogramem. Wszystkie zapisy wykonane do podstawowego są natychmiast i asynchronicznie replikowane na pomocniczej.

### <a name="how-long-does-geo-replication-replication-take"></a>Jak długo trwa replikacja geograficzna?

Replikacja jest przyrostowa, asynchronicznego i ciągłego i czas zajęty nie różni się znacznie od opóźnienia w różnych regionach. W pewnych okolicznościach pomocniczej pamięci podręcznej może być wymagane do pełnej synchronizacji danych z podstawowego. Czas replikacji w tym przypadku zależy od liczby czynników, takich jak: obciążenie podstawowej pamięci podręcznej, dostępna przepustowość sieci i opóźnienie między regionami. Odkryliśmy, że czas replikacji dla pełnej pary replikowanej geograficznie 53 GB może wynosić od 5 do 10 minut.

### <a name="is-the-replication-recovery-point-guaranteed"></a>Czy gwarantowany jest punkt odzyskiwania replikacji?

W przypadku pamięci podręcznej w trybie replikacji geograficznej trwałość jest wyłączona. Jeśli para replikowana geograficznie jest odłączona, na przykład zainicjowana przez klienta usługa failover, pomocnicza połączona pamięć podręczna przechowuje zsynchronizowane dane do tego momentu. W takich sytuacjach nie jest gwarantowany żaden punkt odzyskiwania.

Aby uzyskać punkt odzyskiwania, [Eksportuj](cache-how-to-import-export-data.md#export) z obu pamięci podręcznej. Później można [zaimportować](cache-how-to-import-export-data.md#import) do podstawowej połączonej pamięci podręcznej.

### <a name="can-i-use-powershell-or-azure-cli-to-manage-geo-replication"></a>Czy mogę zarządzać replikacją geograficzną za pomocą programu PowerShell lub interfejsu wiersza polecenia platformy Azure?

Tak, replikacją geograficzną można zarządzać za pomocą portalu Azure, programu PowerShell lub interfejsu wiersza polecenia platformy Azure. Aby uzyskać więcej informacji, zobacz [dokumenty programu PowerShell](https://docs.microsoft.com/powershell/module/az.rediscache/?view=azps-1.4.0#redis_cache) lub [dokumenty interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/redis/server-link?view=azure-cli-latest).

### <a name="how-much-does-it-cost-to-replicate-my-data-across-azure-regions"></a>Ile kosztuje replikowanie moich danych w regionach platformy Azure?

Podczas korzystania z replikacji geograficznej dane z podstawowej połączonej pamięci podręcznej są replikowane do pomocniczej połączonej pamięci podręcznej. Transfer danych nie jest pobierany, jeśli dwie połączone pamięci podręczne znajdują się w tym samym regionie. Jeśli dwie połączone pamięci podręczne znajdują się w różnych regionach, opłata za transfer danych jest kosztem transferu danych wychodzących z sieci danych przemieszczających się po każdym regionie. Aby uzyskać więcej informacji, zobacz [Szczegóły cen przepustowości](https://azure.microsoft.com/pricing/details/bandwidth/).

### <a name="why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache"></a>Dlaczego operacja nie powiodła się podczas próby usunięcia połączonej pamięci podręcznej?

Nie można usunąć pamięci podręcznych replikowanych geograficznie i ich grup zasobów podczas połączenia, dopóki nie usuniesz łącza replikacji geograficznej. Jeśli spróbujesz usunąć grupę zasobów, która zawiera jedną lub obie połączone pamięci podręczne, inne zasoby w `deleting` grupie zasobów zostaną usunięte, ale grupa `running` zasobów pozostanie w stanie, a wszystkie połączone pamięci podręczne w grupie zasobów pozostaną w stanie. Aby całkowicie usunąć grupę zasobów i połączone w niej pamięci podręczne, odłącz pamięć podręczną zgodnie z opisem w [obszarze Usuń łącze replikacji geograficznej](#remove-a-geo-replication-link).

### <a name="what-region-should-i-use-for-my-secondary-linked-cache"></a>Jakiego regionu należy używać w pomocniczej połączonej pamięci podręcznej?

Ogólnie rzecz biorąc zaleca się, aby pamięć podręczna istniała w tym samym regionie platformy Azure, co aplikacja, która uzyskuje do niej dostęp. W przypadku aplikacji z oddzielnymi regionami podstawowymi i rezerwowymi zaleca się, aby podstawowe i pomocnicze pamięci podręczne istniały w tych samych regionach. Aby uzyskać więcej informacji na temat sparowanych regionów, zobacz [Najważniejsze wskazówki — sparowane regiony platformy Azure](../best-practices-availability-paired-regions.md).

### <a name="how-does-failing-over-to-the-secondary-linked-cache-work"></a>Jak działa niepowodzenie w przypadku pomocniczej połączonej pamięci podręcznej?

Automatyczna praca awaryjna w regionach platformy Azure nie jest obsługiwana w przypadku pamięci podręcznych replikowanych geograficznie. W scenariuszu odzyskiwania po awarii klienci powinni wywołać cały stos aplikacji w skoordynowany sposób w ich regionie kopii zapasowej. Umożliwienie poszczególnym składnikom aplikacji decydowania o tym, kiedy samodzielnie przełączyć się na kopie zapasowe, może negatywnie wpłynąć na wydajność. Jedną z kluczowych zalet redis jest to, że jest to magazyn o bardzo małych opóźnieniach. Jeśli główna aplikacja klienta znajduje się w innym regionie niż jego pamięć podręczna, dodatkowy czas podróży w obie strony będzie miał zauważalny wpływ na wydajność. Z tego powodu możemy uniknąć awarii automatycznie z powodu przejściowych problemów z dostępnością.

Aby rozpocząć pracę awaryjną inicjowaną przez klienta, należy najpierw odłączyć pamięć podręczną. Następnie zmień klienta Redis, aby użyć punktu końcowego połączenia (dawniej połączone) pomocniczej pamięci podręcznej. Gdy dwie pamięci podręczne są odłączone, pomocnicza pamięć podręczna staje się ponownie zwykłą pamięcią podręczną odczytu i zapisu i akceptuje żądania bezpośrednio od klientów Redis.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [warstwie Azure Cache for Redis Premium.](cache-premium-tier-intro.md)
