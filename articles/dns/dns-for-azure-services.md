---
title: Używać usługi Azure DNS z innymi usługami platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać usługi Azure DNS do rozpoznawania nazw dla innych usług platformy Azure
services: dns
documentationcenter: na
author: vhorne
manager: jeconnoc
editor: ''
tags: azure dns
ms.assetid: e9b5eb94-7984-4640-9930-564bb9e82b78
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 09/21/2016
ms.author: victorh
ms.openlocfilehash: dcf209d2036d2686bea0b51380db3cd2473d04a6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61293196"
---
# <a name="how-azure-dns-works-with-other-azure-services"></a>Jak usługa Azure DNS współpracuje z innymi usługami platformy Azure

System DNS Azure jest hostowanej usługi DNS zarządzania i nazwę rozwiązania. Służy do tworzenia publicznej nazwy DNS dla innych aplikacji i usług, które można wdrożyć na platformie Azure. Tworzenie nazwę usługi platformy Azure w domenie niestandardowej jest proste. Po prostu dodaj rekord poprawnego typu usługi.

* Dla dynamicznie przydzielone adresy IP można utworzyć rekord CNAME w systemie DNS, który mapowany na nazwę DNS, który Azure utworzony dla usługi. Standardy systemu DNS uniemożliwić przy użyciu rekordu CNAME dla wierzchołku strefy. Zamiast tego można użyć rekordu aliasu. Aby uzyskać więcej informacji, zobacz [Samouczek: Konfigurowanie rekordu aliasu do odwoływania się do platformy Azure publiczny adres IP](tutorial-alias-pip.md).
* Statycznie przydzielonych adresów IP, może utworzyć rekord A systemu DNS przy użyciu dowolną nazwę, która zawiera *domenę* nazwy w wierzchołku strefy.

W poniższej tabeli przedstawiono typy obsługiwanych rekordów, które można użyć dla różnych usług platformy Azure. Jak pokazano w tabeli usługi Azure DNS obsługuje tylko rekordy DNS dla zasobów sieciowych połączone z Internetem. Usługa DNS platformy Azure nie można używać do rozpoznawania nazw wewnętrznych, prywatne adresy.

| Usługa platformy Azure | Interfejs sieciowy | Opis |
| --- | --- | --- |
| Azure Application Gateway |[Publiczny adres IP frontonu](dns-custom-domain.md#public-ip-address) |Można utworzyć rekord DNS A lub CNAME. |
| Azure Load Balancer |[Publiczny adres IP frontonu](dns-custom-domain.md#public-ip-address) |Można utworzyć rekord DNS A lub CNAME. Moduł równoważenia obciążenia może mieć IPv6 publicznego adresu IP, który jest dynamicznie przypisywany. Utwórz rekord CNAME dla adresu IPv6. |
| Azure Traffic Manager |Publiczna nazwa |Możesz utworzyć rekord aliasu, który mapuje nazwę trafficmanager.net przypisane do profilu usługi Traffic Manager. Aby uzyskać więcej informacji, zobacz [Samouczek: Konfigurowanie rekordu aliasu do obsługi języka apex nazw domen przy użyciu usługi Traffic Manager](tutorial-alias-tm.md). |
| Usług Azure Cloud Services |[Publiczny adres IP](dns-custom-domain.md#public-ip-address) |Statycznie przydzielonych adresów IP można utworzyć rekord A systemu DNS. Dynamicznie przydzielone adresy IP, należy utworzyć rekord CNAME, który jest mapowany do *cloudapp.net* nazwy.|
| Usługa Azure App Service | [Zewnętrzny adres IP](dns-custom-domain.md#app-service-web-apps) |Dla zewnętrznych adresów IP można utworzyć rekord A systemu DNS. W przeciwnym razie należy utworzyć rekord CNAME, który mapuje nazwę azurewebsites.net. Aby uzyskać więcej informacji, zobacz [zamapować niestandardową nazwę domeny na aplikację platformy Azure](../app-service/app-service-web-tutorial-custom-domain.md). |
| Maszyny wirtualne platformy Azure Resource Manager |[Publiczny adres IP](dns-custom-domain.md#public-ip-address) |Maszyny wirtualne usługi Resource Manager może mieć publiczne adresy IP. Maszyna wirtualna z publicznym adresem IP można również za modułem równoważenia obciążenia. Można utworzyć, A systemu DNS, CNAME lub rekordu aliasu dla publicznego adresu. Pomijanie adresów VIP modułu równoważenia obciążenia, można użyć tej niestandardowej nazwy. |
| Klasyczne maszyny wirtualne |[Publiczny adres IP](dns-custom-domain.md#public-ip-address) |Klasyczne maszyny wirtualne tworzone za pomocą programu PowerShell lub interfejsu wiersza polecenia można skonfigurować za pomocą dynamiczny lub statyczny (zastrzeżony) wirtualny adres. Możesz utworzyć rekord CNAME DNS lub rekord A, odpowiednio. |
