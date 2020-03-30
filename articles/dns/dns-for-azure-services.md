---
title: Korzystanie z usługi Azure DNS z innymi usługami platformy Azure
description: W tej ścieżce szkoleniowej rozpocznij pracę nad używaniem usługi Azure DNS do rozpoznawania nazw innych usług platformy Azure
services: dns
documentationcenter: na
author: rohinkoul
manager: kumudD
tags: azure dns
ms.assetid: e9b5eb94-7984-4640-9930-564bb9e82b78
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 09/21/2016
ms.author: rohink
ms.openlocfilehash: fa2c1ced6405c967ca33562d6215b304b8507e5a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76937255"
---
# <a name="how-azure-dns-works-with-other-azure-services"></a>Jak usługa Azure DNS współpracuje z innymi usługami platformy Azure

Usługa Azure DNS to usługa zarządzania i rozpoznawania nazw hostowanych dns. Można go używać do tworzenia publicznych nazw DNS dla innych aplikacji i usług, które można wdrożyć na platformie Azure. Tworzenie nazwy usługi platformy Azure w domenie niestandardowej jest proste. Wystarczy dodać rekord odpowiedniego typu dla usługi.

* W przypadku dynamicznie przydzielanych adresów IP można utworzyć rekord CNAME DNS, który jest mapowany na nazwę DNS utworzoną przez platformę Azure dla twojej usługi. Standardy DNS uniemożliwiają używanie rekordu CNAME dla wierzchołka strefy. Zamiast tego można użyć rekordu aliasu. Aby uzyskać więcej informacji, zobacz [Samouczek: Konfigurowanie rekordu aliasu w celu odwoływania się do publicznego adresu IP platformy Azure](tutorial-alias-pip.md).
* W przypadku statycznie przydzielonych adresów IP można utworzyć rekord DNS A przy użyciu dowolnej nazwy, która zawiera *nazwę domeny nagiej* w wierzchołku strefy.

W poniższej tabeli przedstawiono obsługiwane typy rekordów, których można używać w różnych usługach platformy Azure. Jak pokazano w tabeli, usługa Azure DNS obsługuje tylko rekordy DNS dla zasobów sieciowych przeznaczonych do internetu. Usługi Azure DNS nie można używać do rozpoznawania nazw adresów wewnętrznych, prywatnych.

| Usługa platformy Azure | Interfejs sieciowy | Opis |
| --- | --- | --- |
| Azure Application Gateway |[Publiczny adres IP frontu](dns-custom-domain.md#public-ip-address) |Można utworzyć rekord DNS A lub CNAME. |
| Azure Load Balancer |[Publiczny adres IP frontu](dns-custom-domain.md#public-ip-address) |Można utworzyć rekord DNS A lub CNAME. Moduł równoważenia obciążenia może mieć publiczny adres IP IPv6, który jest dynamicznie przypisywany. Utwórz rekord CNAME dla adresu IPv6. |
| Azure Traffic Manager |Nazwa publiczna |Można utworzyć rekord aliasu, który mapuje nazwę trafficmanager.net przypisaną do profilu usługi Traffic Manager. Aby uzyskać więcej informacji, zobacz [Samouczek: Konfigurowanie rekordu aliasu do obsługi nazw domen wierzchołka za pomocą usługi Traffic Manager](tutorial-alias-tm.md). |
| usług Azure Cloud Services |[Publiczny adres IP](dns-custom-domain.md#public-ip-address) |W przypadku statycznie przydzielonych adresów IP można utworzyć rekord DNS A. W przypadku dynamicznie przydzielanych adresów IP należy utworzyć rekord CNAME, który jest mapowany na nazwę *cloudapp.net.*|
| Azure App Service | [Zewnętrzny adres IP](dns-custom-domain.md#app-service-web-apps) |W przypadku zewnętrznych adresów IP można utworzyć rekord DNS A. W przeciwnym razie należy utworzyć rekord CNAME, który mapuje na nazwę azurewebsites.net. Aby uzyskać więcej informacji, zobacz [Mapowanie niestandardowej nazwy domeny do aplikacji platformy Azure](../app-service/app-service-web-tutorial-custom-domain.md). |
| Maszyny wirtualne usługi Azure Resource Manager |[Publiczny adres IP](dns-custom-domain.md#public-ip-address) |Maszyny wirtualne Menedżera zasobów mogą mieć publiczne adresy IP. Maszyna wirtualna z publicznym adresem IP również może znajdować się za modułem równoważenia obciążenia. Można utworzyć rekord DNS A, CNAME lub alias dla adresu publicznego. Tej niestandardowej nazwy można użyć do ominięcia adresu VIP w równoważku obciążenia. |
| Klasyczne maszyny wirtualne |[Publiczny adres IP](dns-custom-domain.md#public-ip-address) |Klasyczne maszyny wirtualne utworzone przy użyciu programu PowerShell lub interfejsu wiersza polecenia można skonfigurować przy użyciu dynamicznego lub statycznego (zastrzeżonego) adresu wirtualnego. Można utworzyć cname DNS lub rekord A, odpowiednio. |
