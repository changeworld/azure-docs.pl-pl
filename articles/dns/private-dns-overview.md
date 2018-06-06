---
title: Użyj usługi Azure DNS dla domen prywatnej | Dokumentacja firmy Microsoft
description: Przegląd prywatnej DNS obsługującego usługę w systemie Microsoft Azure.
services: dns
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2018
ms.author: kumud
ms.openlocfilehash: 0ee3b18b7f874c4f6b7b2c9c559aa7e393ad7d8d
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34700608"
---
# <a name="use-azure-dns-for-private-domains"></a>Użyj usługi Azure DNS dla domen prywatnych
System nazw domen lub DNS, jest odpowiedzialny za tłumaczenia (lub rozpoznawania) nazwę usługi na adres IP. Usługa hostingu domen DNS, usługa DNS platformy Azure udostępnia rozpoznawanie nazw przy użyciu infrastruktury Microsoft Azure. Oprócz obsługi internetowy domen DNS, usługi Azure DNS teraz obsługuje również prywatnej domen DNS jako funkcja w wersji zapoznawczej. 
 
Usługa DNS platformy Azure zapewnia niezawodne, bezpieczne usługę DNS do zarządzania i rozpoznawanie nazw domeny w sieci wirtualnej bez konieczności dodawania niestandardowego rozwiązania DNS. Przy użyciu prywatnych stref DNS, można użyć nazwy domeny niestandardowej, a nie nazwy platformy Azure obecnie dostępne. Przy użyciu niestandardowych nazw domen pomaga dostosować architektury sieci wirtualnej w zależności do potrzeb organizacji. Udostępnia rozpoznawanie nazw do maszyn wirtualnych (VM) w ramach sieci wirtualnej i między sieciami wirtualnymi. Ponadto można skonfigurować nazwy stref z widokiem typu horizon podziału, dzięki czemu prywatnych i publicznych strefy DNS do tej samej nazwie.

![Omówienie systemu DNS](./media/private-dns-overview/scenario.png)

[!INCLUDE [private-dns-public-preview-notice](../../includes/private-dns-public-preview-notice.md)]

## <a name="benefits"></a>Korzyści

Usługa DNS platformy Azure zapewnia następujące korzyści:

* **Eliminuje to potrzebę rozwiązania niestandardowe DNS**. Wcześniej wielu klientów utworzyć niestandardowe DNS rozwiązania do zarządzania strefy DNS w sieci wirtualnej. Teraz można przeprowadzać zarządzania strefy DNS przy użyciu natywnej infrastruktury Azure usuwa obciążeń tworzenia i zarządzania rozwiązania niestandardowe DNS.

* **Użyj wszystkie popularne typy rekordów DNS**. Usługa DNS platformy Azure obsługuje rekordów A, AAAA, CNAME, MX, NS, PTR, SOA, SRV i TXT.

* **Zarządzanie rekordami automatyczne hostname**. Wraz z hosting niestandardowych rekordów DNS, Azure automatycznie obsługuje rejestruje nazwę hosta dla maszyn wirtualnych w określonej sieci wirtualnych. W tym scenariuszu można zoptymalizować nazwy domeny, które umożliwia bez konieczności tworzenia niestandardowych rozwiązań DNS lub modyfikowania aplikacji.

* **Rozpoznawanie nazwy hosta między sieciami wirtualnymi**. W odróżnieniu od nazwy hosta dostarczone do usługi Azure prywatnej stref DNS może być udostępniane między sieciami wirtualnymi. Ta funkcja upraszcza scenariusze granic sieci i odnajdywania usługi, takie jak sieci wirtualnej komunikacji równorzędnej.

* **Znanych narzędzi i środowisko użytkownika**. Aby skrócić czas nauki, tej oferty nowej używa ustalonym narzędzi usługi Azure DNS (środowiska PowerShell, szablonów usługi Azure Resource Manager i interfejsu API REST).

* **Obsługa w systemie DNS typu horizon podziału**. Z usługi Azure DNS można utworzyć strefy o takiej samej nazwie, które rozwiązanie do różnych odpowiedzi z sieci wirtualnej i z publicznej sieci internet. Typowy scenariusz w DNS typu horizon podziału jest zapewnienie dedykowanych wersji usługi do użytku w Twojej sieci wirtualnej.

* **Dostępna we wszystkich regionach platformy Azure**. Funkcja prywatnej stref DNS platformy Azure jest dostępna we wszystkich regionach platformy Azure w chmurze publicznej Azure. 


## <a name="capabilities"></a>Możliwości

Usługa DNS platformy Azure oferuje następujące możliwości:
 
* **Automatyczna rejestracja maszyn wirtualnych z jednego sieci wirtualnej połączonego strefę prywatnej, co sieć wirtualna rejestracji**. Maszyny wirtualne są zarejestrowane (dodany) do strefy prywatnej jako rejestruje wskazujące ich prywatnych adresów IP. Maszynę wirtualną w rejestracji jest usuwany sieci wirtualnej Azure automatycznie usuwa odpowiedniego DNS rekordów z połączonego strefy prywatnych. 

  > [!NOTE]
  > Domyślnie rejestracji sieci wirtualnych jest również działać jako rozwiązania sieci wirtualnych, w tym sensie, że działa rozpoznawanie nazw DNS dla strefy z dowolną z maszyn wirtualnych w ramach sieci wirtualnej rejestracji. 

* **Rozpoznawanie nazw DNS w przód jest obsługiwana w sieci wirtualne, które są połączone z prywatnej strefy jako sieci wirtualne rozpoznawania**. Dla sieci wirtualnej między rozpoznawania nazw DNS Brak nie jawne zależności taki sposób, że sieci wirtualne są połączyć ze sobą za pomocą. Jednak klientów może być elementów równorzędnych sieci wirtualnych w innych sytuacjach (na przykład ruch HTTP).

* **Wyszukiwanie wsteczne DNS jest obsługiwana w zakresie sieci wirtualnej**. Wyszukiwanie wsteczne DNS prywatnego adresu IP w sieci wirtualnej przypisane do strefy prywatnej zwróci nazwy FQDN, która zawiera nazwę hosta/rekordu, a także nazwę strefy jako sufiks. 


## <a name="limitations"></a>Ograniczenia

Usługa Azure DNS podlega następującym ograniczeniom:

* Prywatne strefy dozwolone jest tylko jedna rejestracja sieci wirtualnej.
* Maksymalnie 10 rozpoznawania sieci wirtualne są dozwolone dla stref prywatnych.
* Określonej sieci wirtualnej może być połączony do tylko jednej strefie prywatnej, co sieć wirtualna rejestracji.
* Określonej sieci wirtualnej może być połączony do maksymalnie 10 stref prywatnej jako rozwiązania sieci wirtualnej.
* Jeżeli określono sieć wirtualną rejestracji, rekordy DNS dla maszyn wirtualnych z tej sieci wirtualnej, które są zarejestrowane do prywatnego strefy nie są widoczne lub pobieranie z programu Azure Powershell i interfejsów API interfejsu wiersza polecenia Azure, ale rekordów maszyny Wirtualnej są w rzeczywistości zarejestrowane i zostanie Rozwiąż pomyślnie.
* Wstecznego DNS dotyczy tylko prywatnej przestrzeni IP w sieci wirtualnej rejestracji.
* Reverse DNS dla prywatnego adresu IP, który nie jest zarejestrowany w strefie prywatny (na przykład prywatnego adresu IP dla maszyny wirtualnej w sieci wirtualnej, która jest połączona jako rozwiązania wirtualnej sieci prywatnej strefy) zwraca *internal.cloudapp.net* jako sufiks DNS. Jednak ten sufiks nie jest rozpoznawana. 
* Sieć wirtualna musi być pusta (to znaczy, że maszyna wirtualna nie istnieje żaden rekord) po jego początkowo (oznacza to, po raz pierwszy) łącza do prywatnego strefy jako rejestracji bądź rozpoznawanie nazw sieci wirtualnej. Jednak sieci wirtualnej można następnie niepustym przyszłych łączenia jako rejestracji bądź rozpoznawanie nazw sieci wirtualnej, do innych stref prywatnych. 
* W tej chwili warunkowego przesyłania dalej nie jest obsługiwane (na przykład w przypadku włączania rozpoznawania między sieciami Azure i lokalnego programu). Aby dowiedzieć się jak klientów można uzyskać w tym scenariuszu za pośrednictwem innych mechanizmów, zobacz [rozpoznawanie nazw dla maszyn wirtualnych i wystąpień roli](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

Na typowe pytania i odpowiedzi dotyczące prywatnej stref w usłudze Azure DNS, włączając określone zachowanie rejestracji i rozpoznawania DNS można oczekiwać na niektóre rodzaje operacji, zobacz [— często zadawane pytania](./dns-faq.md#private-dns).  


## <a name="pricing"></a>Cennik

Funkcja prywatne stref DNS jest bezpłatne w publicznej wersji zapoznawczej. Podczas ogólnej dostępności funkcja oferuje opartej na użyciu modelu cenowego podobny do istniejącej usługi Azure DNS oferty. 


## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak utworzyć strefę prywatnych w usłudze Azure DNS za pomocą [programu Azure PowerShell](./private-dns-getstarted-powershell.md) lub [interfejsu wiersza polecenia Azure](./private-dns-getstarted-cli.md).

Przeczytaj informacje o niektóre typowe [scenariusze prywatnej strefy](./private-dns-scenarios.md) który można uzyskać z prywatnej stref w usłudze Azure DNS.

Na typowe pytania i odpowiedzi dotyczące prywatnej stref w usłudze Azure DNS, włączając określone zachowanie można oczekiwać na niektóre rodzaje operacji, zobacz [— często zadawane pytania](./dns-faq.md#private-dns). 

Więcej informacji na temat stref DNS i rekordy odwiedzając [DNS strefy i rejestruje omówienie](dns-zones-records.md).

Poznaj inne kluczowe [możliwości sieciowe](../networking/networking-overview.md) platformy Azure. 

