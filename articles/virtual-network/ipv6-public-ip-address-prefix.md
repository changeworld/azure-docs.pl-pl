---
title: Prefiks adresu IPv6 publicznych w sieci wirtualnej platformy Azure
titlesuffix: Azure Virtual Network
description: Dowiedz się więcej o publicznym prefiksie adresu IPv6 w sieci wirtualnej platformy Azure.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/15/2019
ms.author: kumud
ms.openlocfilehash: 8254a7d86d5cadc2ddc03940f4ab2d08de74bd86
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72965169"
---
# <a name="reserved-public-ipv6-address-prefix-preview"></a>Zarezerwowany publiczny prefiks adresu IPv6 (wersja zapoznawcza)

Na platformie Azure sieci wirtualne (IPv4+ IPv6) (IPv4+IPv6) (IPv4) i maszyny wirtualne (VM) są domyślnie bezpieczne, ponieważ nie mają łączności z Internetem. Możesz łatwo dodać łączność internetową IPv6 do modułów równoważenia obciążenia platformy Azure i maszyn wirtualnych za pomocą publicznych adresów IPv6, które można uzyskać z platformy Azure.

Wszystkie publiczne adresy IP, które można zarezerwować są skojarzone z regionu platformy Azure do wyboru i z subskrypcji platformy Azure. Możesz przenieść zarezerwowany (statyczny) publiczny adres IP IPv6 między dowolnymi modułami równoważenia obciążenia platformy Azure lub maszynami wirtualnymi w ramach subskrypcji. Możesz całkowicie rozszycić publiczny adres IP IPv6 i będzie on przechowywany do użytku, gdy będziesz gotowy.

> [!WARNING]
> Należy zachować ostrożność, aby nie usuwać publicznych adresów IP przypadkowo. Usunięcie publicznego adresu IP powoduje usunięcie go z subskrypcji i nie będzie można go odzyskać (nawet za pomocą pomocy technicznej platformy Azure).

Oprócz rezerwacji poszczególnych adresów IPv6 można zarezerwować ciągłe zakresy adresów Protokołu IPv6 platformy Azure (znany jako prefiks IP) do użytku.  Podobnie jak w indywidualnych adresach IP, zarezerwowane prefiksy są skojarzone z wybranym regionem platformy Azure i z subskrypcją platformy Azure. Rezerwowanie przewidywalnego, ciągłego zakresu adresów ma wiele zastosowań. Na przykład można znacznie uprościć *białą listę* adresów IP aplikacji hostowanych na platformie Azure przez firmę i klientów, ponieważ statyczne zakresy adresów IP mogą być łatwo zaprogramowane w zapory lokalne.  W razie potrzeby można utworzyć poszczególne publiczne adresy IP z prefiksu IP, a po usunięciu tych poszczególnych publicznych adresów IP są one *zwracane* do zastrzeżonego zakresu, aby można było ich użyć ponownie później. Wszystkie adresy IP w prefiksie IP są zarezerwowane do wyłącznego użytku do czasu usunięcia prefiksu.

> [!Important]
> IPv6 dla usługi Azure Virtual Network jest obecnie w publicznej wersji zapoznawczej. Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać szczegółowe informacje, zobacz [Dodatkowe warunki użytkowania wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="ipv6-prefix-sizes"></a>Rozmiary prefiksów IPv6
Dostępne są następujące publiczne rozmiary prefiksów IP:

-  Minimalny rozmiar prefiksu IPv6: /127 = 2 adresy
-  Maksymalny rozmiar prefiksu IPv6: /124 = 16 adresów

Rozmiar prefiksu jest określony jako rozmiar maski cidr (Classless Inter-Domain Routing). Na przykład maska /128 reprezentuje indywidualny adres IPv6, ponieważ adresy IPv6 składają się ze 128 bitów.

## <a name="pricing"></a>Cennik
 
Aby uzyskać koszty związane z korzystaniem z publicznych adresów IP platformy Azure, zarówno pojedynczych adresów IP, jak i zakresów adresów IP, zobacz [Cennik publicznych adresów IP](https://azure.microsoft.com/pricing/details/ip-addresses/).

## <a name="limitations"></a>Ograniczenia
IPv6 jest obsługiwany na podstawowych publicznych adresów IP tylko z "dynamiczne" alokacji, co oznacza, że adres IPv6 zmieni się po usunięciu i ponownego wdrożenia aplikacji (VM lub modułów równoważenia obciążenia) na platformie Azure. Standardowa obsługa publicznego adresu IP protokołu IPv6 jest przeznaczona wyłącznie na alokację statyczną (zastrzeżoną), chociaż standardowe moduły równoważenia obciążenia INTERNAL mogą również obsługiwać alokację dynamiczną z poziomu podsieci, do której są przypisane.  

Najlepszym rozwiązaniem jest używanie standardowych publicznych usług IP i standardowych modułów równoważenia obciążenia dla aplikacji IPv6.

## <a name="next-steps"></a>Następne kroki
- Zarezerwuj publiczny [prefiks adresu IPv6](ipv6-reserve-public-ip-address-prefix.md).
- Dowiedz się więcej o [adresach IPv6](ipv6-overview.md).
- Dowiedz [się, jak tworzyć i używać publicznych usług IP](virtual-network-public-ip-address.md) (zarówno IPv4, jak i IPv6) na platformie Azure.
