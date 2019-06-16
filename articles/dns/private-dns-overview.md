---
title: Co to jest Azure prywatnej strefy DNS?
description: Przegląd prywatnej strefy DNS, obsługującego usługę w systemie Microsoft Azure.
services: dns
author: vhorne
ms.service: dns
ms.topic: overview
ms.date: 6/12/2019
ms.author: victorh
ms.openlocfilehash: a8548b4972d5853f09630ae3e9ded05ed6fee32b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67076381"
---
# <a name="what-is-azure-private-dns"></a>Co to jest Azure prywatnej strefy DNS?

> [!IMPORTANT]
> Prywatnej usługi DNS platformy Azure jest obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

System nazw domen lub DNS, odpowiada za tłumaczenia (lub rozpoznawanie) nazwę usługi na jej adres IP.  System DNS Azure jest usługa hostingowa przeznaczona dla domen DNS, która umożliwia rozpoznawanie nazw przy użyciu infrastruktury Microsoft Azure. Oprócz obsługi domeny DNS połączone z Internetem, system DNS Azure obsługuje również prywatne strefy DNS.

Prywatnej usługi DNS platformy Azure zapewnia niezawodną i bezpieczną usługę DNS do zarządzania i rozpoznawanie nazw domeny w sieci wirtualnej bez konieczności dodawania niestandardowego rozwiązania DNS. Przy użyciu prywatnych stref DNS, można użyć nazwy domeny niestandardowej, a nie nazwy platformy Azure, które muszą być dostępne już dzisiaj. Przy użyciu niestandardowych nazw domen pomaga dostosować architektury sieci wirtualnej w zależności do potrzeb swojej organizacji. Oferuje ona rozpoznawanie nazw maszyn wirtualnych (VM) w sieci wirtualnej, a także między sieciami wirtualnymi. Ponadto można skonfigurować nazwy stref z widokiem split-horizon, co pozwala prywatnej i publicznej strefie DNS mieć nazwę.

Aby rozwiązać rekordy prywatnej strefy DNS z sieci wirtualnej, możesz połączyć sieć wirtualną w strefie. Połączone sieci wirtualne mają pełny dostęp i czy może rozpoznawać wszystkie rekordy DNS, opublikowane w prywatnej strefy. Ponadto można również włączyć autorejestracją przez łącze sieci wirtualnej. Jeśli włączysz autorejestracją przez łącze sieci wirtualnej, rekordy DNS dla maszyn wirtualnych w tej sieci wirtualnej są rejestrowane w prywatnej strefy. Po włączeniu autorejestracją system DNS Azure aktualizuje również rekordy strefy zawsze wtedy, gdy maszyna wirtualna jest tworzona, zmiany jego "adres IP lub został usunięty.

![Omówienie systemu DNS](./media/private-dns-overview/scenario.png)

> [!NOTE]
> Najlepszym rozwiązaniem jest nieużywanie konta *.local* domeny dla swojej prywatnej strefy DNS. Nie wszystkie systemy operacyjne obsługują to.

## <a name="benefits"></a>Korzyści

Prywatnej usługi DNS platformy Azure zapewnia następujące korzyści:

* **Eliminuje konieczność stosowania niestandardowego rozwiązania DNS**. Wielu klientów utworzone wcześniej, niestandardowych rozwiązań DNS, aby zarządzać strefami DNS w sieci wirtualnej. Możesz teraz zarządzać strefami DNS przy użyciu natywnej infrastruktury platformy Azure, co spowoduje usunięcie obciążeń tworzenia i zarządzania niestandardowego rozwiązania DNS.

* **Użyj wszystkie popularne typy rekordów DNS**. Usługa DNS platformy Azure obsługuje rekordów A, AAAA, CNAME, MX, PTR, SOA, SRV i TXT.

* **Automatyczne hostname zarządzania rekordami**. Wraz z hostingu niestandardowych rekordów DNS, Azure automatycznie utrzymuje rekordy nazw hosta dla maszyn wirtualnych w określonej sieci wirtualnej. W tym scenariuszu można zoptymalizować nazwy domeny, które umożliwia bez konieczności tworzenia niestandardowego rozwiązania DNS lub modyfikowania aplikacji.

* **Rozpoznawanie nazw hostów między sieciami wirtualnymi**. W odróżnieniu od nazwy hosta platformy Azure prywatnych stref DNS mogą być współużytkowane między sieciami wirtualnymi. Ta funkcja upraszcza scenariusze między siecią i odnajdowania usług, takie jak komunikacja równorzędna sieci wirtualnych.

* **Dobrze znane narzędzia i środowisko użytkownika**. Aby skrócić czas nauki, ta usługa korzysta z ustalonymi narzędzi usługi Azure DNS (witryna Azure portal, programu Azure PowerShell, wiersza polecenia platformy Azure, szablony usługi Azure Resource Manager i interfejsu API REST).

* **Obsługa w systemie DNS split-horizon**. Dzięki usłudze Azure DNS można utworzyć strefy o tej samej nazwie, które nawiązują do różnych odpowiedzi z sieci wirtualnej i z publicznej sieci internet. Typowy scenariusz użycia split-horizon DNS jest zapewnienie dedykowanych wersji usługi do użytku w Twojej sieci wirtualnej.

* **Dostępne we wszystkich regionach platformy Azure**. Funkcja strefami prywatnymi usługi Azure DNS jest dostępna we wszystkich regionach platformy Azure w chmurze publicznej Azure.

## <a name="capabilities"></a>Możliwości

Usługa DNS platformy Azure zapewnia następujące możliwości:

* **Automatycznej rejestracji maszyn wirtualnych z sieci wirtualnej połączonej z prywatnej strefy z włączoną autorejestracją**. Maszyny wirtualne są zarejestrowane (dodany) do strefy prywatnej jako rekordy, wskazując ich prywatnych adresów IP. Po usunięciu maszyny wirtualnej w łącze sieci wirtualnej przy użyciu autorejestracją włączone usługi Azure DNS również automatycznie usuwa odpowiedni rekord DNS z połączonych prywatnej strefy.

* **Do przodu rozpoznawania nazw DNS jest obsługiwana dla sieci wirtualnych, które są połączone z prywatnej strefy**. Dla sieci wirtualnej między rozpoznawania nazw DNS jest niezależne jawne taki sposób, że wirtualne sieci równorzędne ze sobą. Można jednak nawiązać komunikację równorzędną między sieciami wirtualnymi w innych sytuacjach (na przykład ruch HTTP).

* **Wyszukiwanie wsteczne DNS jest obsługiwana w ramach zakresu sieci wirtualnej**. Wyszukiwanie wsteczne DNS, aby uzyskać prywatny adres IP w sieci wirtualnej przypisany do prywatnej strefy zwraca nazwę FQDN, która zawiera nazwę hosta/rekordu i nazwę strefy, jako sufiks.

## <a name="other-considerations"></a>Inne zagadnienia

Usługa DNS platformy Azure ma następujące ograniczenia:

* Sieć wirtualną tylko jeden rejestracji jest dozwoloną na strefę prywatną.
* Maksymalnie 10 rozpoznawanie sieci wirtualne są dozwolone na strefę prywatną. Ten limit zostanie usunięta, gdy ta funkcja jest ogólnie dostępna.
* Określonej sieci wirtualnej można połączyć tylko jedną strefę prywatnego jako sieć wirtualną rejestracji.
* Określonej sieci wirtualnej można połączyć maksymalnie 10 stref prywatnych jako sieć wirtualną rozpoznawania. Ten limit zostanie usunięta, gdy ta funkcja jest ogólnie dostępna.
* Jeśli określisz sieć wirtualną rejestracji, rekordy DNS dla maszyn wirtualnych z tej sieci wirtualnej, które są zarejestrowane do prywatnej strefy nie są widoczne i możliwe do pobierania z programu Azure Powershell i interfejsów API interfejsu wiersza polecenia platformy Azure. Rekordy maszyny Wirtualnej w rzeczywistości są zarejestrowane i zostanie rozwiązany pomyślnie.
* Odwrotnego DNS działa tylko w przypadku prywatnej przestrzeni adresów IP w sieci wirtualnej rejestracji.
* Odwrotne DNS dla prywatnego adresu IP, który nie jest zarejestrowany w prywatnej strefy (na przykład, prywatny adres IP dla maszyny wirtualnej w sieci wirtualnej, która zostanie połączona jako sieć wirtualną rozpoznawania prywatnej strefy) zwraca *internal.cloudapp.net* sufiks DNS. Jednak ten sufiks nie można rozpoznać.
* Sieć wirtualna musi być całkowicie pusty po raz pierwszy możesz połączyć strefę prywatną jako sieć wirtualną rejestracji lub rozpoznawania. Jednak sieci wirtualnej następnie może być pusty dla przyszłych połączeń jako rejestracji lub rozpoznawania sieci wirtualnej, do innych stref prywatnych.
* Obecnie warunkowego przesyłania dalej nie jest obsługiwane (na przykład w przypadku włączania rozpoznawania między sieciami Azure i lokalnego). Aby dowiedzieć się, jak jak klienci mogą weź pod uwagę, w tym scenariuszu za pośrednictwem innych mechanizmów, zobacz [rozpoznawanie nazw dla maszyn wirtualnych i wystąpień roli](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).
* Odwrotnego DNS dotyczy tylko przestrzeń prywatnych adresów IP w sieci wirtualnej połączonej
* Odwrotnym systemem DNS, aby uzyskać prywatny adres IP dla sieci wirtualnej połączonej zwraca "internal.cloudapp.net" jako domyślny sufiks dla maszyny wirtualnej. Dla sieci wirtualnych, które są połączone strefę prywatną z włączoną autorejestracją odwrotnego systemu DNS dla prywatny adres IP zwraca 2 nazw FQDN jednego z domyślną sufiks *internal.cloudapp.net* , a drugi z sufiksem stref prywatnych.
* Warunkowego przesyłania dalej nie jest obsługiwane. Na przykład aby włączyć rozpoznawanie między platformą Azure i lokalnej sieci. Dowiedz się, jak włączyć ten scenariusz przy użyciu innych mechanizmów. Zobacz [rozpoznawanie nazw dla maszyn wirtualnych i wystąpień roli](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)
 


## <a name="pricing"></a>Cennik

Aby uzyskać informacje o cenach, zobacz [cennik usługi Azure DNS](https://azure.microsoft.com/pricing/details/dns/).

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się, jak utworzyć prywatną strefę w usłudze Azure DNS przy użyciu [programu Azure PowerShell](./private-dns-getstarted-powershell.md) lub [wiersza polecenia platformy Azure](./private-dns-getstarted-cli.md).

* Przeczytaj o niektórych typowych [scenariusze prywatnej strefy](./private-dns-scenarios.md) , które można realizować ze strefami prywatnymi w usłudze Azure DNS.

* Typowe pytania i odpowiedzi na pytania dotyczące stref prywatnych w usłudze Azure DNS, w tym określone zachowanie można oczekiwać na niektóre rodzaje operacji, zobacz [— często zadawane pytania do prywatnej DNS](./dns-faq-private.md).

* Więcej informacji na temat stref i rekordów DNS, odwiedzając [DNS strefy i rekordy Przegląd](dns-zones-records.md).

* Poznaj inne kluczowe [możliwości sieciowe](../networking/networking-overview.md) platformy Azure.
