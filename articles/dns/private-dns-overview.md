---
title: Co to jest prywatna strefa DNS platformy Azure?
description: W tym artykule rozpocznij pracę z omówieniem prywatnej usługi hostingu DNS na platformie Microsoft Azure.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: overview
ms.date: 6/12/2019
ms.author: rohink
ms.openlocfilehash: 97b266398b3ea46d09b04524dad34922f21b1a95
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "76939286"
---
# <a name="what-is-azure-private-dns"></a>Co to jest prywatna strefa DNS platformy Azure?

System nazw domen (DNS) jest odpowiedzialny za tłumaczenie (lub rozpoznawanie) nazwy usługi na jego adres IP.  Usługa Azure DNS to usługa hostingowa dla domen DNS, zapewniająca rozpoznawanie nazw przy użyciu infrastruktury platformy Microsoft Azure. Oprócz obsługi internetowych domen DNS usługa Azure DNS obsługuje również prywatne strefy DNS.

Usługa Azure Private DNS zapewnia niezawodną, bezpieczną usługę DNS do zarządzania i rozpoznawania nazw domen w sieci wirtualnej bez konieczności dodawania niestandardowego rozwiązania DNS. Korzystając z prywatnych stref DNS, można użyć własnych niestandardowych nazw domen, a nie nazw dostarczonych przez platformę Azure dostępnych obecnie. Używanie niestandardowych nazw domen pomaga dostosować architekturę sieci wirtualnej do potrzeb organizacji. Zapewnia rozpoznawanie nazw maszyn wirtualnych (VM) w sieci wirtualnej i między sieciami wirtualnymi. Ponadto można skonfigurować nazwy stref z widokiem podzielonego horyzontu, który umożliwia prywatną i publiczną strefę DNS współużytkować nazwę.

Aby rozpoznać rekordy prywatnej strefy DNS z sieci wirtualnej, należy połączyć sieć wirtualną ze strefą. Połączone sieci wirtualne mają pełny dostęp i mogą rozpoznawać wszystkie rekordy DNS opublikowane w strefie prywatnej. Ponadto można również włączyć autoregistration na łącze sieci wirtualnej. Jeśli włączysz autoregistration na łącze sieci wirtualnej, rekordy DNS dla maszyn wirtualnych w tej sieci wirtualnej są rejestrowane w strefie prywatnej. Gdy autoregistration jest włączona, usługa Azure DNS aktualizuje również rekordy strefy za każdym razem, gdy maszyna wirtualna jest tworzona, zmienia swój adres IP lub jest usuwany.

![Omówienie systemu DNS](./media/private-dns-overview/scenario.png)

> [!NOTE]
> Najlepszym rozwiązaniem jest nieużywanie domeny *lokalnej* dla prywatnej strefy DNS. Nie wszystkie systemy operacyjne to obsługują.

## <a name="benefits"></a>Korzyści

Usługa Azure Private DNS zapewnia następujące korzyści:

* **Eliminuje potrzebę stosowania niestandardowych rozwiązań DNS**. Wcześniej wielu klientów tworzyła niestandardowe rozwiązania DNS do zarządzania strefami DNS w swojej sieci wirtualnej. Teraz można zarządzać strefami DNS przy użyciu natywnej infrastruktury platformy Azure, która usuwa ciężar tworzenia niestandardowych rozwiązań DNS i zarządzania nimi.

* **Użyj wszystkich typowych typów rekordów DNS**. Usługa Azure DNS obsługuje rekordy A, AAAA, CNAME, MX, PTR, SOA, SRV i TXT.

* **Automatyczne zarządzanie rekordami nazwy hosta**. Wraz z hostowania niestandardowych rekordów DNS, Platforma Azure automatycznie przechowuje rekordy nazwy hosta dla maszyn wirtualnych w określonych sieciach wirtualnych. W tym scenariuszu można zoptymalizować nazwy domen, których używasz bez konieczności tworzenia niestandardowych rozwiązań DNS lub modyfikowania aplikacji.

* **Rozdzielczość nazwy hosta między sieciami wirtualnymi**. W przeciwieństwie do nazw hostów dostarczonych przez platformę Azure prywatne strefy DNS mogą być współużytkowane między sieciami wirtualnymi. Ta funkcja upraszcza scenariusze między sieciami i odnajdowania usług, takie jak komunikacja równorzędna sieci wirtualnej.

* **Znane narzędzia i doświadczenie użytkownika**. Aby zmniejszyć krzywą uczenia się, ta usługa używa ugruntowanych narzędzi azure DNS (Azure portal, Azure PowerShell, Azure CLI, Szablony usługi Azure Resource Manager i interfejs API REST).

* **Obsługa systemu DNS podzielonego horyzontu**. Za pomocą usługi Azure DNS można tworzyć strefy o tej samej nazwie, które rozróżniają różne odpowiedzi z poziomu sieci wirtualnej i z publicznego Internetu. Typowym scenariuszem dla systemu DNS podzielonego horyzontu jest zapewnienie dedykowanej wersji usługi do użycia w sieci wirtualnej.

* **Dostępne we wszystkich regionach platformy Azure**. Funkcja strefy prywatnej usługi Azure DNS jest dostępna we wszystkich regionach platformy Azure w chmurze publicznej platformy Azure.

## <a name="capabilities"></a>Możliwości

Usługa Azure DNS udostępnia następujące funkcje:

* **Automatyczna rejestracja maszyn wirtualnych z sieci wirtualnej, która jest połączona ze strefą prywatną z włączoną autoregistration**. Maszyny wirtualne są rejestrowane (dodawane) do strefy prywatnej jako rekordy A wskazujące ich prywatne adresy IP. Po usunięciu maszyny wirtualnej w łączu sieci wirtualnej z włączoną autoregistracją usługa Azure DNS automatycznie usuwa odpowiedni rekord DNS z połączonej strefy prywatnej.

* **Przekazywanie rozdzielczości DNS jest obsługiwane w sieciach wirtualnych połączonych ze strefą prywatną**. W przypadku rozpoznawania DNS sieci między wirtualnej nie ma jawnej zależności, tak aby sieci wirtualne były ze sobą równorzędne. Jednak można równorzędne sieci wirtualne dla innych scenariuszy (na przykład ruchu HTTP).

* **Wyszukiwanie wstecznego DNS jest obsługiwane w zakresie sieci wirtualnej**. Odwróć wyszukiwanie DNS prywatnego adresu IP w sieci wirtualnej przypisanej do strefy prywatnej zwraca nazwę FQDN zawierającą nazwę hosta/rekordu oraz nazwę strefy jako sufiks.

## <a name="other-considerations"></a>Inne zagadnienia

Usługa Azure DNS ma następujące ograniczenia:

* Określona sieć wirtualna może być połączona tylko z jedną strefą prywatną, jeśli jest włączona automatyczna rejestracja rekordów DNS maszyn wirtualnych. Można jednak połączyć wiele sieci wirtualnych z jedną strefą DNS.
* Odwrócony DNS działa tylko w przypadku prywatnej przestrzeni IP w połączonej sieci wirtualnej
* Odwróć dns dla prywatnego adresu IP dla połączonej sieci wirtualnej zwraca *internal.cloudapp.net* jako domyślny sufiks dla maszyny wirtualnej. W przypadku sieci wirtualnych połączonych ze strefą prywatną z włączoną autoregistracją wsteczny system DNS dla prywatnego adresu IP zwraca dwa nazwy FQDN: jeden z domyślnym sufiksem *internal.cloudapp.net* a drugi z sufiksem strefy prywatnej.
* Przekazywanie warunkowe nie jest obecnie obsługiwane natywnie. Aby włączyć rozpoznawanie między platformą Azure i sieciami lokalnymi. Zobacz [Rozpoznawanie nazw maszyn wirtualnych i wystąpień ról](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)
 
## <a name="pricing"></a>Cennik

Aby uzyskać informacje o cenach, zobacz [Cennik DNS platformy Azure](https://azure.microsoft.com/pricing/details/dns/).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak utworzyć strefę prywatną w usłudze Azure DNS przy użyciu [programu Azure PowerShell](./private-dns-getstarted-powershell.md) lub [interfejsu wiersza polecenia platformy Azure.](./private-dns-getstarted-cli.md)

* Przeczytaj o niektórych typowych [scenariuszach strefy prywatnej,](./private-dns-scenarios.md) które można zrealizować za pomocą stref prywatnych w usłudze Azure DNS.

* Aby uzyskać typowe pytania i odpowiedzi dotyczące stref prywatnych w usłudze Azure DNS, w tym określone zachowanie, jakich można oczekiwać w przypadku niektórych rodzajów operacji, zobacz [często zadawane pytania dotyczące prywatnego DNS](./dns-faq-private.md).

* Dowiedz się więcej o strefach i rekordach DNS, odwiedzając [omówienie stref i rekordów DNS](dns-zones-records.md).

* Poznaj inne kluczowe [możliwości sieciowe](../networking/networking-overview.md) platformy Azure.
