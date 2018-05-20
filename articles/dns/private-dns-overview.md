---
title: Za pomocą usługi Azure DNS dla domen prywatnej | Dokumentacja firmy Microsoft
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
ms.openlocfilehash: 677fc66b66d6c17806a313f2fac3a15e8e1775ba
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2018
---
# <a name="using-azure-dns-for-private-domains"></a>Za pomocą usługi Azure DNS dla domen prywatnych
System nazw domen lub DNS, jest odpowiedzialny za tłumaczenia (lub rozpoznawania) nazwę usługi na adres IP. Usługa DNS platformy Azure to Usługa hostingu domen DNS, rozpoznawania nazw przy użyciu infrastruktury Microsoft Azure.  Oprócz internetowy domen DNS usługi Azure DNS teraz obsługuje również prywatnej domen DNS jako funkcja w wersji zapoznawczej.  
 
Usługa DNS platformy Azure zapewnia niezawodne, bezpieczne usługę DNS do zarządzania i rozpoznawanie nazw domeny w sieci wirtualnej bez konieczności dodawania niestandardowego rozwiązania DNS. Strefy DNS prywatne umożliwiają używanie własne nazwy domeny niestandardowej, a nie nazwy platformy Azure obecnie dostępne.  Przy użyciu niestandardowych nazw domen pomaga dostosować architektury sieci wirtualnej w zależności do potrzeb organizacji. Udostępnia rozpoznawanie nazw dla maszyn wirtualnych w ramach sieci wirtualnej i między sieciami wirtualnymi. Ponadto można skonfigurować nazwy stref z widokiem typu horizon podziału — umożliwiając prywatnych i publicznych strefę DNS o tej samej nazwie.

![Omówienie systemu DNS](./media/private-dns-overview/scenario.png)

[!INCLUDE [private-dns-public-preview-notice](../../includes/private-dns-public-preview-notice.md)]

## <a name="benefits"></a>Korzyści

* **Eliminuje to potrzebę niestandardowych rozwiązań DNS.** Wcześniej wielu klientów utworzyć niestandardowe DNS rozwiązania do zarządzania strefy DNS w sieci wirtualnej.  Teraz można zrobić zarządzania strefy DNS przy użyciu natywnej infrastruktury platformy Azure, co spowoduje usunięcie obciążeń tworzenia i zarządzania rozwiązania niestandardowe DNS.

* **Użyj wszystkie popularne typy rekordów DNS.**  Usługa DNS platformy Azure obsługuje rekordów A, AAAA, CNAME, MX, NS, PTR, SOA, SRV i TXT.

* **Zarządzanie rekordu automatyczne nazwy hosta.** Wraz z hosting niestandardowych rekordów DNS, Azure automatycznie obsługuje rejestruje nazwę hosta dla maszyn wirtualnych w określonej sieci wirtualnych.  Dzięki temu można zoptymalizować nazwy domeny, które umożliwia bez konieczności tworzenia niestandardowych rozwiązań DNS lub modyfikowania aplikacji.

* **Rozpoznawanie nazwy hosta między sieciami wirtualnymi.** W odróżnieniu od nazwy hosta dostarczone do usługi Azure prywatnej stref DNS może być udostępniane między sieciami wirtualnymi.  Ta funkcja upraszcza granic sieci i usług odnajdywania scenariuszy, takich jak sieci wirtualnej komunikacji równorzędnej.

* **Znanych narzędzi i środowisko użytkownika.** Aby skrócić czas nauki, to nową ofertę używa już ustalonym narzędzi usługi Azure DNS (środowiska PowerShell, interfejsu API REST usługi Resource Manager szablonów).

* **Obsługa w systemie DNS typu horizon podziału.** Usługa DNS platformy Azure umożliwia tworzenie stref o takiej samej nazwie, która rozwiązać do różnych odpowiedzi z sieci wirtualnej i z publicznej sieci Internet.  Typowy scenariusz w DNS typu horizon podziału jest zapewnienie dedykowanych wersji usługi do użytku w Twojej sieci wirtualnej.

* **Dostępna we wszystkich regionach platformy Azure.** Strefy DNS prywatnego Azure jest dostępna we wszystkich regionach platformy Azure w chmurze publicznej Azure. 


## <a name="capabilities"></a>Możliwości 
* Automatyczna rejestracja maszyn wirtualnych z jednego sieci wirtualnej połączone prywatnej strefy jako rejestracji sieci wirtualnej. Maszyn wirtualnych będzie zarejestrowany (dodany) do strefy prywatnej jako rejestruje wskazujące ich prywatnych adresów IP. Ponadto podczas maszynę wirtualną w sieci wirtualnej zostaje usunięta rejestracji Azure również automatycznie usunie odpowiedniego rekordu DNS z połączonego strefy prywatnych. Należy pamiętać, że sieci wirtualne rejestracji również domyślnie pełnić rolę rozpoznawania sieci wirtualnych w tym rozpoznawania nazw DNS dla strefy będą działać na dowolnym maszyn wirtualnych w ramach sieci wirtualnej rejestracji. 
* Przesyła obsługiwane w sieci wirtualne, które są połączone z prywatnej strefy jako sieci wirtualne rozpoznawania rozpoznawania nazw DNS. Dla sieci wirtualnej między rozpoznawania nazw DNS, nie istnieje żadne jawne zależności że sieci wirtualne można połączyć ze sobą za pomocą. Jednakże klienci mogą chcieć elementów równorzędnych sieci wirtualnych w innych sytuacjach (np: ruch HTTP).
* Wyszukiwanie wsteczne DNS obsługiwane w zakresie sieci Wirtualnej. Wstecznego wyszukiwania DNS dla prywatnego adresu IP w sieci wirtualnej przypisane do strefy prywatnego zwróci nazwy FQDN, która zawiera nazwę hosta/rekordu, a także nazwę strefy jako sufiks. 


## <a name="limitations"></a>Ograniczenia
* Sieć wirtualna 1 rejestracji dla stref prywatnego
* Maksymalnie 10 sieciami wirtualnymi rozpoznawania dla stref prywatnych
* Danej sieci wirtualnej może odnosić się tylko do jednej strefie prywatnego co sieć wirtualna rejestracji
* Danej sieci wirtualnej może odnosić się do maksymalnie 10 stref prywatnego jako rozwiązania sieci wirtualnej
* Jeżeli określono sieć wirtualną rejestracji, rekordy DNS dla maszyn wirtualnych z tej sieci wirtualnej, które są zarejestrowane do strefy prywatnych nie będą widoczne lub pobieranie z programu Powershell/CLI/interfejsów API, ale rekordów maszyny Wirtualnej w rzeczywistości są rejestrowane i zostanie rozwiązany pomyślnie
* Wstecznego DNS działa tylko dla miejsca prywatnego adresu IP w sieci wirtualnej rejestracji
* Reverse DNS dla prywatnego adresu IP, który nie jest zarejestrowany w strefie prywatnej (np: prywatnego adresu IP dla maszyny wirtualnej w sieci wirtualnej połączonego jako rozwiązania wirtualnej sieci prywatnej strefę) zwróci "internal.cloudapp.net" jako sufiks DNS, jednak ten sufiks nie jest rozpoznawana.   
* Sieć wirtualna musi być pusta (tzn. żadne rekordy maszyny Wirtualnej) podczas początkowego (tj. po raz pierwszy) łączenia ze strefy prywatnej, co sieć wirtualna rejestracji bądź rozpoznawanie nazw. Jednak sieci wirtualnej można następnie niepustym przyszłych łączenia jako rejestracji bądź rozpoznawanie nazw sieci wirtualnej, do innych stref prywatnych. 
* W tej chwili warunkowego przesyłania dalej nie jest obsługiwana, na przykład dotyczące włączania rozpoznawania między sieciami Azure i lokalnego programu. Dokumentację dotyczącą sposobu klientów można uzyskać w tym scenariuszu za pośrednictwem innych mechanizmów, zobacz [rozpoznawanie nazwy dla maszyn wirtualnych i wystąpień roli](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)

Zalecamy również należy również przeczytanie w na [— często zadawane pytania](./dns-faq.md#private-dns) dla niektórych typowych pytań i odpowiedzi na prywatnego stref w usłudze Azure DNS, włączając określone zachowanie Rejestracja i rozpoznawanie DNS można oczekiwać, że na niektóre rodzaje operacji. 


## <a name="pricing"></a>Cennik

Strefy DNS prywatnego jest bezpłatne w publicznej wersji zapoznawczej. Podczas ogólnej dostępności tej funkcji będą używać opartej na użyciu modelu cenowego podobne do istniejącej usługi Azure DNS oferty. 


## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak utworzyć strefę prywatnego za pomocą usługi Azure DNS [PowerShell](./private-dns-getstarted-powershell.md) lub [interfejsu wiersza polecenia](./private-dns-getstarted-cli.md).

Zapoznaj się z informacjami o niektórych typowych scenariuszach, które można realizować za pomocą funkcji stref prywatnych w usłudze Azure DNS, w temacie [Private Zone scenarios (Scenariusze dotyczące stref prywatnych)](./private-dns-scenarios.md).

Przeczytaj na [— często zadawane pytania](./dns-faq.md#private-dns) dla niektórych typowych pytań i odpowiedzi na prywatnego stref w usłudze Azure DNS, włączając określone zachowanie można oczekiwać, że na niektóre rodzaje operacji. 

Więcej informacji na temat stref DNS i rekordy odwiedzając: [DNS strefy i rejestruje omówienie](dns-zones-records.md).

Poznaj inne kluczowe [możliwości sieciowe](../networking/networking-overview.md) platformy Azure.

