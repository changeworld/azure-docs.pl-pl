---
title: Wprowadzenie do warstwy Azure Cache for Redis Premium
description: Dowiedz się, jak tworzyć i zarządzać obsługą redis persistence, klastrowania Redis i sieci wirtualnej dla pamięci podręcznej azure premium dla wystąpień Redis
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 07/05/2017
ms.author: yegu
ms.openlocfilehash: aadcc13d2397f10ea40f06d1259c86b9a179c38b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74121673"
---
# <a name="introduction-to-the-azure-cache-for-redis-premium-tier"></a>Wprowadzenie do warstwy Azure Cache for Redis Premium
Usługa Azure Cache for Redis to rozproszona, zarządzana pamięć podręczna, która pomaga tworzyć wysoce skalowalne i responsywne aplikacje, zapewniając superszybki dostęp do danych. 

Nowa warstwa Premium to warstwa gotowa do pracy w przedsiębiorstwie, która zawiera wszystkie funkcje warstwy standardowej i inne, takie jak lepsza wydajność, większe obciążenia, odzyskiwanie po awarii, importowanie/eksportowanie i zwiększone zabezpieczenia. Kontynuuj czytanie, aby dowiedzieć się więcej o dodatkowych funkcjach warstwy pamięci podręcznej Premium.

## <a name="better-performance-compared-to-standard-or-basic-tier"></a>Lepsza wydajność w porównaniu do warstwy Standardowa lub Podstawowa
**Lepsza wydajność w warstwie Standardowa lub Podstawowa.** Pamięci podręczne w warstwie Premium są wdrażane na sprzęcie, który ma szybsze procesory i zapewnia lepszą wydajność w porównaniu do warstwy podstawowej lub standardowej. Pamięci podręczne warstwy premium mają wyższą przepływność i mniejsze opóźnienia. 

**Przepływność dla tej samej wielkości pamięci podręcznej jest wyższa w warstwie Premium w porównaniu do warstwy standardowej.** Na przykład przepływność pamięci podręcznej 53 GB P4 (Premium) wynosi 250 K żądań na sekundę w porównaniu do 150K dla C6 (Standard).

Aby uzyskać więcej informacji na temat rozmiaru, przepływności i przepustowości w pamięci podręcznej premium, zobacz [Azure Cache for Redis — często zadawane pytania](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)

## <a name="redis-data-persistence"></a>Trwałość danych Redis
Warstwa Premium umożliwia utrwalanie danych pamięci podręcznej na koncie usługi Azure Storage. W podstawowej/standardowej pamięci podręcznej wszystkie dane są przechowywane tylko w pamięci. W przypadku podstawowych problemów z infrastrukturą może do czasu utraty danych. Zalecamy użycie funkcji trwałości danych Redis w warstwie Premium, aby zwiększyć odporność na utratę danych. Usługa Azure Cache for Redis oferuje opcje RDB i AOF (wkrótce) w [redis trwałość.](https://redis.io/topics/persistence) 

Aby uzyskać instrukcje dotyczące konfigurowania trwałości, zobacz [Jak skonfigurować trwałość dla pamięci podręcznej platformy Azure w warstwie Premium dla programu Redis.](cache-how-to-premium-persistence.md)

## <a name="redis-cluster"></a>Klaster Redis
Jeśli chcesz utworzyć pamięci podręczne większe niż 53 GB lub chcesz niezależnego fragmentu danych w wielu węzłach Redis, możesz użyć klastrowania Redis, który jest dostępny w warstwie Premium. Każdy węzeł składa się z pary pamięci podręcznej podstawowej/repliki zarządzanej przez platformę Azure w celu zapewnienia wysokiej dostępności. 

**Klastrowanie Redis zapewnia maksymalną skalę i przepływność.** Przepływność zwiększa się liniowo w miarę zwiększania liczby fragmentów (węzłów) w klastrze. Na przykład: Jeśli utworzysz klaster P4 10 fragmentów, dostępna przepływność wynosi 250K * 10 = 2,5 miliona żądań na sekundę. Zobacz [azure cache for Redis — często zadawane pytania,](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use) aby uzyskać więcej informacji na temat rozmiaru, przepływności i przepustowości w pamięci podręcznej w warstwie Premium.

Aby rozpocząć korzystanie z klastrowania, zobacz [Jak skonfigurować klastrowanie dla pamięci podręcznej premium Azure dla programu Redis.](cache-how-to-premium-clustering.md)

## <a name="enhanced-security-and-isolation"></a>Zwiększone bezpieczeństwo i izolacja
Pamięci podręczne utworzone w warstwie Podstawowa lub Standardowa są dostępne w publicznym Internecie. Dostęp do pamięci podręcznej jest ograniczony na podstawie klucza dostępu. W warstwie Premium można dodatkowo zapewnić, że tylko klienci w określonej sieci mogą uzyskać dostęp do pamięci podręcznej. Można wdrożyć usługę Azure Cache for Redis w sieci wirtualnej platformy Azure .You can deploy Azure Cache for Redis in an [Azure Virtual Network (VNet)](https://azure.microsoft.com/services/virtual-network/).You can deploy Azure Cache for Redis in an Azure Virtual Network (VNet) . Aby jeszcze bardziej ograniczyć dostęp do serwera Redis, można użyć wszystkich funkcji VNet, np. podsieci, zasad kontroli dostępu oraz innych funkcji.

Aby uzyskać więcej informacji, zobacz [Jak skonfigurować obsługę sieci wirtualnej dla pamięci podręcznej premium Azure dla programu Redis](cache-how-to-premium-vnet.md).

## <a name="importexport"></a>Import/Export
Import/Eksport to operacja zarządzania danymi usługi Azure Cache for Redis, która umożliwia importowanie danych do pamięci podręcznej Azure Cache for Redis lub eksportowanie danych z usługi Azure Cache for Redis przez importowanie i eksportowanie migawki pamięci podręcznej usługi Azure Cache for Redis Database (RDB) z pamięci podręcznej premium do obiektu blob strony na koncie usługi Azure Storage. Dzięki temu można przeprowadzić migrację między różnymi wystąpieniami usługi Azure Cache dla redis lub wypełnić pamięć podręczną danymi przed użyciem.

Import może służyć do przynoszenia zgodnych z Redis plików RDB z dowolnego serwera Redis działającego w dowolnej chmurze lub środowisku, w tym redis działającego w systemie Linux, Windows lub dowolnym dostawcy chmury, takiego jak Amazon Web Services i innych. Importowanie danych to prosty sposób na utworzenie pamięci podręcznej z wstępnie wypełnionymi danymi. Podczas procesu importowania usługa Azure Cache for Redis ładuje pliki RDB z magazynu platformy Azure do pamięci, a następnie wstawia klucze do pamięci podręcznej.

Eksport umożliwia eksportowanie danych przechowywanych w pamięci podręcznej platformy Azure dla redis do plików RDB zgodnych z redis. Za pomocą tej funkcji można przenieść dane z jednej pamięci podręcznej platformy Azure dla wystąpienia Redis do innego lub do innego serwera Redis. Podczas procesu eksportowania plik tymczasowy jest tworzony na maszynie Wirtualnej, która obsługuje wystąpienie serwera usługi Azure Cache for Redis, a plik jest przekazywalny do wyznaczonego konta magazynu. Po zakończeniu operacji eksportowania ze stanem sukcesu lub niepowodzenia plik tymczasowy jest usuwany.

Aby uzyskać więcej informacji, zobacz [Jak importować dane i eksportować dane z usługi Azure Cache for Redis](cache-how-to-import-export-data.md).

## <a name="reboot"></a>Ponowne uruchamianie
Warstwa premium umożliwia ponowne uruchomienie jednego lub więcej węzłów pamięci podręcznej na żądanie. Dzięki temu można przetestować aplikację pod kątem odporności w przypadku awarii. Można ponownie uruchomić następujące węzły.

* Węzeł główny pamięci podręcznej
* Węzeł pomocniczy pamięci podręcznej
* Zarówno węzły podstawowe, jak i pomocnicze pamięci podręcznej
* Podczas korzystania z pamięci podręcznej premium z klastrowania, można ponownie uruchomić podstawowy, pomocniczy lub oba węzły dla poszczególnych fragmentów w pamięci podręcznej

Aby uzyskać więcej informacji, zobacz [Ponowne uruchamianie](cache-administration.md#reboot) i [ponowne uruchamianie często zadawanych pytań](cache-administration.md#reboot-faq).

>[!NOTE]
>Funkcja ponownego uruchamiania jest teraz włączona dla wszystkich warstw Usługi Azure Cache dla redis.
>
>

## <a name="schedule-updates"></a>Aktualizacje harmonogramu
Funkcja zaplanowanych aktualizacji umożliwia wyznaczenie okna konserwacji pamięci podręcznej. Po określeniu okna konserwacji wszystkie aktualizacje serwera Redis są dokonywane w tym oknie. Aby wyznaczyć okno konserwacji, wybierz żądane dni i określ godzinę rozpoczęcia okna konserwacji dla każdego dnia. Należy zauważyć, że czas okna konserwacji jest w czasie UTC. 

Aby uzyskać więcej informacji, zobacz [Planowanie aktualizacji](cache-administration.md#schedule-updates) i Planowanie aktualizacji — często [zadawane pytania](cache-administration.md#schedule-updates-faq).

> [!NOTE]
> Tylko aktualizacje serwera Redis są dokonywane podczas okna zaplanowanej konserwacji. Okno konserwacji nie ma zastosowania do aktualizacji platformy Azure lub aktualizacji systemu operacyjnego maszyny Wirtualnej.
> 
> 

## <a name="geo-replication"></a>Replikacja geograficzna

**Replikacja geograficzna** zapewnia mechanizm łączenia dwóch warstwy Premium azure cache dla wystąpień Redis. Jedna pamięć podręczna jest wyznaczona jako podstawowa połączona pamięć podręczna, a druga jako pomocnicza połączona pamięć podręczna. Pomocnicza połączona pamięć podręczna staje się tylko do odczytu, a dane zapisane w podstawowej pamięci podręcznej są replikowane do pomocniczej połączonej pamięci podręcznej. Ta funkcja może służyć do replikowania pamięci podręcznej w regionach platformy Azure.

Aby uzyskać więcej informacji, zobacz [Jak skonfigurować replikację geograficzną dla pamięci podręcznej Azure dla programu Redis](cache-how-to-geo-replication.md).


## <a name="to-scale-to-the-premium-tier"></a>Skalowanie do warstwy premium
Aby skalować do warstwy premium, po prostu wybierz jedną z warstw premium w bloku **warstwy cenowej Zmień.** Można również skalować pamięć podręczną do warstwy premium przy użyciu programu PowerShell i interfejsu wiersza polecenia. Aby uzyskać instrukcje krok po kroku, zobacz [Jak skalować pamięć podręczną platformy Azure dla redis](cache-how-to-scale.md) i [jak zautomatyzować operację skalowania.](cache-how-to-scale.md#how-to-automate-a-scaling-operation)

## <a name="next-steps"></a>Następne kroki
Utwórz pamięć podręczną i eksploruj nowe funkcje warstwy premium.

* [Jak skonfigurować trwałość dla pamięci podręcznej platformy Azure w warstwie Premium dla redis](cache-how-to-premium-persistence.md)
* [Jak skonfigurować obsługę sieci wirtualnej dla pamięci podręcznej premium Azure dla redis](cache-how-to-premium-vnet.md)
* [Jak skonfigurować klastrowanie dla pamięci podręcznej platformy Azure w warstwie Premium dla redis](cache-how-to-premium-clustering.md)
* [Jak importować dane i eksportować dane z usługi Azure Cache for Redis](cache-how-to-import-export-data.md)
* [Jak administrować pamięcią podręczną platformy Azure dla redis](cache-administration.md)

