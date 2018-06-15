---
title: Omówienie protokołu IPv6 dla modułu równoważenia obciążenia Azure | Dokumentacja firmy Microsoft
description: Opis obsługi protokołu IPv6 dla modułu równoważenia obciążenia w Azure i maszyn wirtualnych z równoważeniem obciążenia.
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
keywords: Protokół IPv6, usługi równoważenia obciążenia azure, podwójnego stosu, publiczny adres ip, natywnego protokołu ipv6, mobile, iot
ms.assetid: 6a1d583f-a305-40fd-a94b-fa42e1943bbb
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 9622ad4922aa98efe093e7f809a490a8797eb1fd
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2018
ms.locfileid: "30189668"
---
# <a name="overview-of-ipv6-for-azure-load-balancer"></a>Omówienie protokołu IPv6 dla modułu równoważenia obciążenia Azure


>[!NOTE] 
>Usługa Azure Load Balancer obsługuje dwie warstwy: Podstawowa i Standardowa. W tym artykule omówiono usługę Load Balancer w warstwie Podstawowa. Aby uzyskać więcej informacji na temat standardowe usługi równoważenia obciążenia, zobacz [Omówienie usługi równoważenia obciążenia standardowego](load-balancer-standard-overview.md).

Internetowy usług równoważenia obciążenia można wdrożyć przy użyciu adresu IPv6. Oprócz połączenia IPv4 dzięki temu następujące możliwości:

* Natywny end-to-end łączności IPv6 między maszynami wirtualnymi Azure (maszyny wirtualne) i publiczny klientów internetowych za pośrednictwem usługi równoważenia obciążenia.
* Natywny end-to-end wychodzącego łączności IPv6 między maszynami wirtualnymi i publiczny klientów obsługujących protokół Internet IPv6.

Poniżej przedstawiono funkcje protokołu IPv6 dla modułu równoważenia obciążenia Azure.

![Moduł równoważenia obciążenia Azure z protokołu IPv6](./media/load-balancer-ipv6-overview/load-balancer-ipv6.png)

Po wdrożeniu IPv4 lub Internet włączony protokół IPv6 klienta może komunikować się z publicznych adresów IPv4 lub IPv6 (lub nazwy hostów) usługi równoważenia obciążenia Azure internetowych. Load balancer kieruje pakiety IPv6 jako prywatne adresy IPv6 maszyn wirtualnych przy użyciu translatora adresów sieciowych (NAT). IPv6 Internet klienta nie może komunikować się bezpośrednio przy użyciu adresu IPv6 maszyn wirtualnych.

## <a name="features"></a>Funkcje

Natywnej obsługi protokołu IPv6 dla maszyn wirtualnych wdrożonych za pośrednictwem usługi Azure Resource Manager zapewnia:

1. Równoważeniem obciążenia IPv6 usług dla klientów protokołu IPv6 w Internecie
2. Punkty końcowe natywnego protokołu IPv6 i IPv4 na maszynach wirtualnych ("dwa skumulowany")
3. Przychodzący i wychodzący zainicjowane natywnego połączenia protokołu IPv6
4. Obsługiwane protokoły TCP, UDP i HTTP (S) Włącz pełną gamę architektury usługi

## <a name="benefits"></a>Korzyści

Ta funkcja umożliwia następujące kluczowe korzyści:

* Spełnia przepisy dla instytucji rządowych wymagające nowych aplikacji i udostępniane klientom obsługującym tylko protokół IPv6
* Włącz mobile i Internetu rzeczy (IOT) deweloperów na potrzeby adresów rosnącym mobile & IOT rynkach skumulowany dwóch maszyn wirtualnych platformy Azure (IPv4 i IPv6)

## <a name="details-and-limitations"></a>Szczegółowe informacje i ograniczenia

Szczegóły

* Usługa Azure DNS zawiera zarówno IPv4 A i IPv6 AAAA rejestruje nazwę i odpowiada rekordami zarówno dla usługi równoważenia obciążenia. Klient będzie preferował adresów (IPv4 lub IPv6) do komunikacji z.
* Gdy maszyna wirtualna inicjuje połączenie do publicznego urządzenia połączone z Internetem IPv6, maszyny Wirtualnej źródłowy adres IPv6 jest adres sieciowy translacji (NAT) usługi równoważenia obciążenia na publiczny adres IPv6.
* Maszyny wirtualne z systemem operacyjnym Linux musi być skonfigurowana do odbierania adresu IP protokołu IPv6 za pośrednictwem protokołu DHCP. Wiele obrazów systemu Linux w galerii Azure są już skonfigurowane do obsługi protokołu IPv6 bez żadnych modyfikacji. Aby uzyskać więcej informacji, zobacz [Konfigurowanie DHCPv6 dla maszyn wirtualnych systemu Linux](load-balancer-ipv6-for-linux.md)
* Jeśli wybierzesz sondy kondycji za pomocą przez moduł równoważenia obciążenia, utworzyć sondy IPv4 i używać go z punktami końcowymi IPv4 i IPv6. Jeśli usługa na maszynie Wirtualnej ulegnie awarii, punkty końcowe IPv4 i IPv6 są wyjmowane z obrotu.

Ograniczenia

* Nie można dodać reguły równoważenia obciążenia IPv6 w portalu Azure. Reguły można tworzyć tylko za pomocą szablonu, interfejsu wiersza polecenia, programu PowerShell.
* Nie może uaktualnić istniejących maszyn wirtualnych do używania adresów IPv6. Należy wdrożyć nowe maszyny wirtualne.
* Na jednym interfejsem sieciowym w każdej maszyny Wirtualnej można przypisać jeden adres IPv6.
* Publiczne adresy IPv6 nie można przypisać do maszyny Wirtualnej. Mogą one zostać przypisana tylko do modułu równoważenia obciążenia.
* Nie można skonfigurować wstecznego wyszukiwania DNS dla sieci publicznych adresów IPv6.
* Maszyny wirtualne przy użyciu adresów IPv6 nie może być członkami usługi w chmurze platformy Azure. Można podłączyć do sieci wirtualnej platformy Azure (VNet), a komunikują się ze sobą za pośrednictwem ich adresów IPv4.
* Adresy IPv6 prywatnej można wdrożyć na poszczególnych maszynach wirtualnych w grupie zasobów, ale nie można wdrożyć w grupie zasobów za pomocą zestawów skali.
* Maszyny wirtualne platformy Azure nie może połączyć się za pośrednictwem protokołu IPv6 do innych maszyn wirtualnych, usług platformy Azure lub urządzeń lokalnych. Można tylko komunikują się z usługi równoważenia obciążenia Azure za pośrednictwem protokołu IPv6. Jednak mogą się komunikować te inne zasoby przy użyciu protokołu IPv4.
* Ochrony sieciowej grupy zabezpieczeń (NSG) dla protokołu IPv4 jest obsługiwane w przypadku wdrożeń podwójny stos (IPv4 i IPv6). Grupy NSG nie dotyczą punktów końcowych protokołu IPv6.
* Punkt końcowy IPv6 na maszynie Wirtualnej nie jest uwidaczniana bezpośrednio do Internetu. Jest za modułem równoważenia obciążenia. Tylko te porty, które są określone w zasadach usługi równoważenia obciążenia są dostępne za pośrednictwem protokołu IPv6.
* Zmiana parametru IdleTimeout dla protokołu IPv6 jest **obecnie nieobsługiwane**. Wartość domyślna to czterech minut.
* Zmiana parametru elementu loadDistributionMethod dla protokołu IPv6 jest **obecnie nieobsługiwane**.
* Zastrzeżone adresy IP protokołu IPv6 (gdzie IPAllocationMethod = static) są **obecnie nieobsługiwane**.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak wdrożyć usługę równoważenia obciążenia w przypadku adresu IPv6.

* [Dostępność IPv6 według regionu](https://go.microsoft.com/fwlink/?linkid=828357)
* [Wdrażanie równoważenia obciążenia w przypadku adresu IPv6 przy użyciu szablonu](load-balancer-ipv6-internet-template.md)
* [Wdrażanie równoważenia obciążenia w przypadku adresu IPv6 przy użyciu programu Azure PowerShell](load-balancer-ipv6-internet-ps.md)
* [Wdrażanie równoważenia obciążenia w przypadku adresu IPv6 przy użyciu wiersza polecenia platformy Azure](load-balancer-ipv6-internet-cli.md)
