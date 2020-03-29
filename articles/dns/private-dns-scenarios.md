---
title: Scenariusze dla stref prywatnych — usługa Azure DNS
description: W tym artykule dowiesz się o typowych scenariuszach korzystania z prywatnych stref DNS platformy Azure.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 10/05/2019
ms.author: rohink
ms.openlocfilehash: ab850adb2e9a25778d5f44ba711eb0762fe562c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76939339"
---
# <a name="azure-dns-private-zones-scenarios"></a>Scenariusze stref prywatnych usługi Azure DNS

Strefy prywatne usługi Azure DNS zapewniają rozpoznawanie nazw w sieci wirtualnej, a także między sieciami wirtualnymi. W tym artykule przyjrzymy się niektórym typowym scenariuszom, które można zrealizować za pomocą tej funkcji.

## <a name="scenario-name-resolution-scoped-to-a-single-virtual-network"></a>Scenariusz: Rozpoznawanie nazw o zakresie do jednej sieci wirtualnej
W tym scenariuszu masz sieć wirtualną na platformie Azure, która ma wiele zasobów platformy Azure w nim, w tym maszyn wirtualnych (maszyn wirtualnych). Chcesz rozwiązać zasoby z sieci wirtualnej za pomocą określonej nazwy domeny (strefa DNS) i potrzebujesz rozpoznawania nazw, aby była prywatna i nie była dostępna z Internetu. Ponadto dla maszyn wirtualnych w sieci wirtualnej, trzeba platformy Azure, aby automatycznie zarejestrować je w strefie DNS. 

Ten scenariusz jest przedstawiony poniżej. Sieć wirtualna o nazwie "A" zawiera dwie maszyny wirtualne (VNETA-VM1 i VNETA-VM2). Każdy z nich ma prywatne adresy IP skojarzone. Po utworzeniu strefy prywatnej o nazwie contoso.com i łączenia tej sieci wirtualnej jako sieci wirtualnej rejestracji usługa Azure DNS automatycznie utworzy dwa rekordy A w strefie, jak pokazano. Teraz kwerendy DNS z VNETA-VM1, aby rozwiązać VNETA-VM2.contoso.com otrzymają odpowiedź DNS, która zawiera prywatny adres IP VNETA-VM2. Ponadto kwerenda odwrotnego DNS (PTR) dla prywatnego adresu IP VNETA-VM1 (10.0.0.1) wydana z VNETA-VM2 otrzyma odpowiedź DNS, która zawiera nazwę VNETA-VM1, zgodnie z oczekiwaniami. 

![Pojedyncza rozdzielczość sieci wirtualnej](./media/private-dns-scenarios/single-vnet-resolution.png)

## <a name="scenario-name-resolution-across-virtual-networks"></a>Scenariusz: Rozpoznawanie nazw w sieciach wirtualnych

W tym scenariuszu jest bardziej typowy przypadek, w którym należy skojarzyć strefę prywatną z wieloma sieciami wirtualnymi. W tym scenariuszu można dopasować architektury, takie jak model Koncentrator i szprycha, w którym znajduje się centralna sieć wirtualna centrum, z którą jest połączonych wiele innych sieci wirtualnych Szprychy. Central hub sieci wirtualnej mogą być połączone jako rejestracji sieci wirtualnej do strefy prywatnej, a szprychy sieci wirtualne mogą być połączone jako rozpoznawania sieci wirtualnych. 

Na poniższym diagramie przedstawiono prostą wersję tego scenariusza, w której istnieją tylko dwie sieci wirtualne — A i B. A jest wyznaczony jako sieć wirtualna rejestracji i B jest wyznaczony jako rozdzielczość sieci wirtualnej. Intencją jest dla obu sieci wirtualnych do udostępniania wspólnej strefy contoso.com. Po utworzeniu strefy i rozpoznawania i rejestracji sieci wirtualnych są połączone ze strefą, platforma Azure automatycznie rejestruje rekordy DNS dla maszyn wirtualnych (VNETA-VM1 i VNETA-VM2) z sieci wirtualnej A. Można również ręcznie dodać rekordy DNS do strefy dla maszyn wirtualnych w sieci wirtualnej Rozdzielczość B. W przypadku tej konfiguracji należy obserwować następujące zachowanie dla kwerend DNS do przodu i do tyłu:
* Kwerenda DNS z VNETB-VM1 w sieci wirtualnej rozdzielczość B, dla VNETA-VM1.contoso.com, otrzyma odpowiedź DNS zawierającą prywatny adres IP VNETA-VM1.
* Kwerenda odwróconego DNS (PTR) z sieci VNETB-VM2 w sieci wirtualnej Rozdzielczość B dla wersji 10.1.0.1 otrzyma odpowiedź DNS zawierającą VNETB-VM1.contoso.com FQDN.  
* Kwerenda odwróconego DNS (PTR) z VNETB-VM3 w sieci wirtualnej Rozdzielczość B dla wersji 10.0.0.1 otrzyma program NXDOMAIN. Powodem jest to, że kwerendy reverse DNS są ograniczone tylko do tej samej sieci wirtualnej. 


![Wiele rozdzielczości sieci wirtualnej](./media/private-dns-scenarios/multi-vnet-resolution.png)

## <a name="scenario-split-horizon-functionality"></a>Scenariusz: Funkcja split-horizon

W tym scenariuszu masz przypadek użycia, w którym chcesz zrealizować różne zachowanie rozpoznawania DNS w zależności od tego, gdzie znajduje się klient (wewnątrz platformy Azure lub obecnie w Internecie), dla tej samej strefy DNS. Na przykład może mieć prywatną i publiczną wersję aplikacji, która ma różne funkcje lub zachowanie, ale chcesz użyć tej samej nazwy domeny dla obu wersji. Ten scenariusz można zrealizować za pomocą usługi Azure DNS, tworząc publiczną strefę DNS, a także strefę prywatną o tej samej nazwie.

Na poniższym diagramie przedstawiono ten scenariusz. Masz sieć wirtualną A, która ma dwie maszyny wirtualne (VNETA-VM1 i VNETA-VM2), które mają przypisane zarówno prywatne adresy IP, jak i publiczne adresy IP. Utworzysz publiczną strefę DNS o nazwie contoso.com i rejestrujesz publiczne serwery IP dla tych maszyn wirtualnych jako rekordy DNS w strefie. Należy również utworzyć prywatną strefę DNS o nazwie contoso.com określającą A jako sieć wirtualną rejestracji. Platforma Azure automatycznie rejestruje maszyny wirtualne jako rekordy A w strefie prywatnej, wskazując ich prywatne adresy IP.

Teraz, gdy klient internetowy wystawia kwerendę DNS w celu wyszukania VNETA-VM1.contoso.com, platforma Azure zwróci rekord publiczny adres IP ze strefy publicznej. Jeśli ta sama kwerenda DNS jest wydawana z innej maszyny Wirtualnej (na przykład: VNETA-VM2) w tej samej sieci wirtualnej A, platforma Azure zwróci rekord prywatnego adresu IP ze strefy prywatnej. 

![Podziel rozdzielczość Briana](./media/private-dns-scenarios/split-brain-resolution.png)

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat prywatnych stref DNS, zobacz [Using Azure DNS for private domains (Używanie usługi Azure DNS dla domen prywatnych)](private-dns-overview.md).

Dowiedz się, jak [utworzyć prywatną strefę DNS](./private-dns-getstarted-powershell.md) w usłudze Azure DNS.

Dowiedz się więcej o strefach i rekordach DNS, odwiedzając: [omówienie stref i rekordów DNS](dns-zones-records.md).

Poznaj inne kluczowe [możliwości sieciowe](../networking/networking-overview.md) platformy Azure.

