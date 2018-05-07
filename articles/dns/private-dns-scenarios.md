---
title: Scenariusze dotyczące usługi Azure DNS prywatnego stref | Dokumentacja firmy Microsoft
description: Omówienie typowych scenariuszy przy użyciu stref DNS prywatnego Azure.
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
ms.openlocfilehash: de543913d4f8264fa8e5b3bca0c510c99c479cae
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/03/2018
---
# <a name="azure-dns-private-zones-scenarios"></a>Scenariusze stref DNS prywatnego Azure
Strefy DNS prywatnego Azure zapewniają rozpoznawania nazw w sieci wirtualnej również między sieciami wirtualnymi. W tym artykule opisano kilka typowych scenariuszy, które mogą być realizowana za pomocą tej funkcji. 

[!INCLUDE [private-dns-public-preview-notice](../../includes/private-dns-public-preview-notice.md)]

## <a name="scenario-name-resolution-scoped-to-a-single-virtual-network"></a>Scenariusz: Rozpoznawanie nazw ograniczone do jednej sieci wirtualnej
W tym scenariuszu masz sieci wirtualnej na platformie Azure, który ma wiele zasobów platformy Azure w ramach tego tym maszynach wirtualnych (VM). Chcesz usunąć go z sieci wirtualnych za pośrednictwem określoną nazwę domeny (strefy DNS), i wymagają rozpoznawania nazw prywatnych i nie jest dostępny z Internetu. Ponadto dla maszyn wirtualnych w sieci Wirtualnej, należy Azure, aby automatycznie zarejestrować je w strefie DNS. 

Ten scenariusz jest opisany poniżej. Sieć wirtualna o nazwie "A" zawiera dwie maszyny wirtualne (VNETA VM1 i maszyny VM2 VNETA). Każdy z tych ma prywatnych adresów IP skojarzone. Po utworzyć strefę prywatnej o nazwie contoso.com i połączyć tej sieci wirtualnej co sieć wirtualna rejestracji usługi Azure DNS automatycznie utworzyć dwa rekordy A w strefie jak pokazano. Teraz zapytania DNS z VM1 VNETA rozwiązywać VNETA VM2.contoso.com otrzyma odpowiedź DNS, który zawiera prywatnego adresu IP z maszyny VM2 VNETA. Ponadto zapytania wstecznego DNS (PTR) dla prywatnego adresu IP z VNETA-VM1 (10.0.0.1) wystawiony na podstawie maszyny VM2 VNETA otrzyma odpowiedź DNS, który zawiera nazwę VNETA VM1, zgodnie z oczekiwaniami. 

![Pojedynczy rozpoznawania sieci wirtualnej](./media/private-dns-scenarios/single-vnet-resolution.png)

## <a name="scenario-name-resolution-across-virtual-networks"></a>Scenariusz: Rozpoznawanie nazw w sieciach wirtualnych

Ten scenariusz jest bardziej typowych przypadkach, w których należy skojarzyć strefy prywatnych z wieloma sieciami wirtualnymi. W tym scenariuszu można zmieścić architektury, takie jak model gwiazdy przypadku centralnej sieci wirtualnej koncentratora, które wielu innych gwiazdy sieci wirtualne są połączone. Centralnej Centrum sieci wirtualnej można jej połączyć jako rejestracji sieci wirtualnej do prywatnej strefy i jako rozwiązania sieci wirtualne można połączyć sieci wirtualnych gwiazdy. 

Na poniższym diagramie przedstawiono prosty wersji w tym scenariuszu w przypadku, gdy istnieją tylko dwa sieci wirtualnych - A i B. A jest wyznaczona jako sieć wirtualną rejestracji i B jest oznaczony jako rozwiązania sieci wirtualnej. W zamierzeniu obie sieci wirtualne udostępnić wspólnej strefie contoso.com. Po utworzeniu strefy i rozdzielczość i rejestracji sieci wirtualne są połączone z strefy, Azure automatyczne rejestrowanie rekordów DNS dla maszyn wirtualnych (VNETA VM1 i maszyny VM2 VNETA) z A. sieci wirtualnej Można również ręcznie dodać rekordy DNS do strefy dla maszyn wirtualnych w sieci wirtualnej rozpoznawania B. W przypadku tej konfiguracji widoczny na następujące zachowanie dotyczące zapytań DNS do przodu i wyszukiwania wstecznego:
* Zapytanie DNS VNETB VM1 w sieci wirtualnej rozpoznawania B dla VNETA VM1.contoso.com, otrzyma zawierający prywatnego adresu IP z VNETA VM1 odpowiedzi DNS.
* Zapytania wstecznego DNS (PTR) z maszyny VM2 VNETB w sieci wirtualnej rozpoznawania B dla 10.1.0.1, otrzyma zawierający nazwę FQDN VNETB-VM1.contoso.com odpowiedzi DNS. Przyczyną jest to, że zapytania DNS wstecznego ograniczone do tej samej sieci wirtualnej. 
* Zapytania wstecznego DNS (PTR) z VNETB VM3 w sieci wirtualnej rozpoznawania B, 10.0.0.1, otrzyma NXDOMAIN. Przyczyną jest to, że zapytania DNS wstecznego tylko ograniczone do tej samej sieci wirtualnej. 


![Wiele rozdzielczości sieci wirtualnej](./media/private-dns-scenarios/multi-vnet-resolution.png)

## <a name="scenario-split-horizon-functionality"></a>Scenariusz: Podziału zakresu funkcji

W tym scenariuszu należy przypadek użycia, w której chcesz zrealizować inny sposób rozpoznawania DNS, w zależności od tego, gdzie znajduje się klient (wewnątrz Azure lub wylogowanie w Internecie), strefy DNS. Na przykład może być prywatne i publiczne wersji aplikacji, która ma różne funkcje lub działanie, ale chcesz użyć tej samej nazwy domeny dla obu wersji. W tym scenariuszu można uzyskać z usługi Azure DNS przez utworzenie strefy DNS publicznego, a także strefę prywatnej o takiej samej nazwie.

W tym scenariuszu przedstawiono na poniższym diagramie. Sieć wirtualna A, która ma dwie maszyny wirtualne (VNETA VM1 i maszyny VM2 VNETA) mających zarówno prywatnych adresów IP, a publiczne adresy IP przydzielone. Tworzenie strefy DNS publicznej o nazwie contoso.com i zarejestrować publicznych adresów IP dla tych maszyn wirtualnych jako rekordów DNS w strefie. Można również utworzyć strefy DNS prywatnego skrót contoso.com określająca rejestracji sieci wirtualnej, A. Azure automatycznie rejestruje maszyn wirtualnych jako rejestruje do strefy prywatne, wskazujące ich prywatnych adresów IP.

Teraz po klienta internetowego wysyła zapytanie DNS do odszukania VNETA VM1.contoso.com, Azure zwrócą rekordu publicznego adresu IP z publicznego strefy. Jeśli zapytanie DNS tego samego wystawiony przez inną maszynę Wirtualną (na przykład: maszyny VM2 VNETA) w tej samej sieci wirtualnej A, Azure zwróci rekordu prywatnego adresu IP z prywatnej strefy. 

![Rozdzielczość Brianowi podziału](./media/private-dns-scenarios/split-brain-resolution.png)

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji na temat prywatnych stref DNS, zobacz [Using Azure DNS for private domains (Używanie usługi Azure DNS dla domen prywatnych)](private-dns-overview.md).

Dowiedz się, jak [utworzyć strefę DNS prywatnej](./private-dns-getstarted-powershell.md) w usłudze Azure DNS.

Więcej informacji na temat stref DNS i rekordy odwiedzając: [DNS strefy i rejestruje omówienie](dns-zones-records.md).

Poznaj inne kluczowe [możliwości sieciowe](../networking/networking-overview.md) platformy Azure.

