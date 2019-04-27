---
title: Scenariusze dotyczące usługi Azure DNS Private Zones
description: Omówienie typowe scenariusze dotyczące korzystania z usługi Azure DNS Private Zones.
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 03/15/2018
ms.author: victorh
ms.openlocfilehash: 409595febded7b242eae876ebb2cb35ae4999e5e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60686866"
---
# <a name="azure-dns-private-zones-scenarios"></a>Scenariusze usługi Azure DNS Private Zones
Usługa Azure DNS Private Zones umożliwia rozpoznawanie nazw w sieci wirtualnej także między sieciami wirtualnymi. W tym artykule przyjrzymy się kilka typowych scenariuszy, które można realizować za pomocą tej funkcji. 

[!INCLUDE [private-dns-public-preview-notice](../../includes/private-dns-public-preview-notice.md)]

## <a name="scenario-name-resolution-scoped-to-a-single-virtual-network"></a>Scenariusz: Rozpoznawanie nazw ograniczone do pojedynczej sieci wirtualnej
W tym scenariuszu masz sieć wirtualną platformy Azure, która ma wiele zasobów platformy Azure, łącznie z maszyn wirtualnych (VM). Chcesz usunąć zasoby z w ramach sieci wirtualnej za pośrednictwem określoną nazwę domeny (strefy DNS) i należy rozpoznawania nazw, prywatne i nie jest dostępny z Internetu. Ponadto w przypadku maszyn wirtualnych w sieci Wirtualnej należy platformy Azure, aby automatycznie zarejestrować je w strefie DNS. 

Ten scenariusz jest opisany poniżej. Sieć wirtualną o nazwie "A" zawiera dwie maszyny wirtualne (maszyna VM1 zachodzi komunikacja równorzędna między i komunikacja równorzędna między VM2). Każdy z nich ma prywatne adresy IP skojarzone. Po utworzeniu strefy prywatnej o nazwie contoso.com i link ta sieć wirtualna jako sieć wirtualną rejestracji, usługa Azure DNS automatycznie utworzy dwa rekordy A w strefie jak pokazano. Teraz zapytania DNS z maszyny VM1 zachodzi komunikacja równorzędna między rozwiązaniem VM2.contoso.com zachodzi komunikacja równorzędna między otrzyma odpowiedź DNS, który zawiera prywatny adres IP dla maszyny VM2 zachodzi komunikacja równorzędna między. Ponadto zapytania odwrotnego systemu DNS (PTR), aby uzyskać prywatny adres IP z zachodzi komunikacja równorzędna między-VM1 (10.0.0.1) wystawionego maszyny VM2 zachodzi komunikacja równorzędna między otrzyma odpowiedź DNS, który zawiera nazwę zachodzi komunikacja równorzędna między-VM1, zgodnie z oczekiwaniami. 

![Pojedynczy rozpoznawanie sieci wirtualnej](./media/private-dns-scenarios/single-vnet-resolution.png)

## <a name="scenario-name-resolution-across-virtual-networks"></a>Scenariusz: Rozpoznawanie nazw w sieciach wirtualnych

Ten scenariusz jest bardziej powszechne przypadek, w których należy skojarzyć strefę prywatną z wieloma sieciami wirtualnymi. W tym scenariuszu można umieścić architektury, na przykład model Gwiazda — w przypadku, gdy istnieje centralnej sieci wirtualnej koncentratora, z których wiele innych szprych sieci wirtualne są połączone. Centralna centralnej sieci wirtualnej mogą być połączone jako sieć wirtualną rejestracji na strefę prywatną i mogą być połączone sieci wirtualne będące Szprychami jako sieciami wirtualnymi rozpoznawania. 

Na poniższym diagramie przedstawiono proste wersję w tym scenariuszu w przypadku, gdy istnieją tylko dwie sieci wirtualne - A i B. Wyznaczony jako sieć wirtualną rejestracji A i B jest wyznaczony jako sieć wirtualną rozpoznawania. Celem jest, obie sieci wirtualne udostępnić typowych strefie contoso.com. Po utworzeniu strefy i rozdzielczość i rejestracji w sieci wirtualne są połączone do strefy, Azure zostanie automatycznie rejestruje rekordy DNS dla maszyn wirtualnych (maszyny VM1 zachodzi komunikacja równorzędna między i komunikacja równorzędna między VM2) z A. sieci wirtualnej Można również ręcznie dodawać rekordy DNS do strefy dla maszyn wirtualnych w sieci wirtualnej rozpoznawania B. W przypadku takiej konfiguracji można zauważyć na następujące zachowanie dotyczące wykonywalna i wsteczna zapytania DNS:
* Zapytania DNS od VM1 VNETC w sieć wirtualną rozpoznawania B dla VM1.contoso.com zachodzi komunikacja równorzędna między, otrzyma zawierająca prywatny adres IP z maszyny VM1 zachodzi komunikacja równorzędna między mógł zweryfikować odpowiedź DNS.
* Kwerendy odwrotnego DNS (PTR) z maszyny VM2 VNETC w sieć wirtualną rozpoznawania B, aby uzyskać 10.1.0.1, otrzyma zawierający nazwę FQDN VNETC-VM1.contoso.com mógł zweryfikować odpowiedź DNS. Przyczyną jest to, że zapytania odwrotnego systemu DNS są ograniczone do tej samej sieci wirtualnej. 
* Kwerendy odwrotnego DNS (PTR) z maszyny VM3 VNETC w sieć wirtualną rozpoznawania B, 10.0.0.1, otrzyma NXDOMAIN. Przyczyną jest to, że zapytania odwrotnego systemu DNS tylko są ograniczone do tej samej sieci wirtualnej. 


![Wiele rozwiązań sieci wirtualnej](./media/private-dns-scenarios/multi-vnet-resolution.png)

## <a name="scenario-split-horizon-functionality"></a>Scenariusz: Funkcja split-Horizon

W tym scenariuszu masz przypadek użycia, w którym chcesz weź pod uwagę różne zachowanie rozpoznawania DNS, w zależności od tego, gdzie klient znajduje się (wewnątrz usługi Azure lub na zewnątrz w Internecie), dla tej samej strefie DNS. Na przykład mogą mieć prywatnych i publicznych wersji aplikacji, który zawiera różne funkcje lub zachowania, ale chcesz użyć tej samej nazwy domeny dla obu wersji. W tym scenariuszu można realizować za pomocą usługi Azure DNS, tworząc strefę DNS publicznych, jak również strefę prywatną o takiej samej nazwie.

W tym scenariuszu przedstawiono na poniższym diagramie. Masz sieć wirtualną A, który ma dwie maszyny wirtualne (maszyna VM1 zachodzi komunikacja równorzędna między i komunikacja równorzędna między VM2) mających zarówno prywatne adresy IP i przydzielane publiczne adresy IP. Tworzenie strefy DNS publicznego o nazwie contoso.com i zarejestrować publicznych adresów IP dla tych maszyn wirtualnych jako rekordów DNS w strefie. Możesz również utworzyć strefy prywatnej strefy DNS jest określana skrótem contoso.com, określając A jako sieć wirtualną rejestracji. Azure powoduje automatyczne zarejestrowanie maszyny wirtualne jako rekordy do strefy prywatne, wskazując ich prywatnych adresów IP.

Teraz gdy klienta internetowego wysyła zapytanie DNS, aby wyszukać zachodzi komunikacja równorzędna między VM1.contoso.com, Azure zwróci publiczny adres IP rekordu ze strefy publicznej. Jeśli tego samego zapytania DNS są wydawane z innej maszyny Wirtualnej (na przykład: VNETA-VM2) w tej samej sieci wirtualnej, A, Azure zwróci rekordu prywatny adres IP z prywatnej strefy. 

![Rozpoznawanie Brian podziału](./media/private-dns-scenarios/split-brain-resolution.png)

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji na temat prywatnych stref DNS, zobacz [Using Azure DNS for private domains (Używanie usługi Azure DNS dla domen prywatnych)](private-dns-overview.md).

Dowiedz się, jak [tworzenia prywatnej strefy DNS](./private-dns-getstarted-powershell.md) w usłudze Azure DNS.

Dowiedz się więcej o stref i rekordów DNS, odwiedzając: [Strefy i rekordy przegląd DNS](dns-zones-records.md).

Poznaj inne kluczowe [możliwości sieciowe](../networking/networking-overview.md) platformy Azure.

