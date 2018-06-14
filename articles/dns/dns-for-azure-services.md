---
title: Za pomocą usługi Azure DNS z innymi usługami Azure | Dokumentacja firmy Microsoft
description: Opis sposobu korzystania z usługi Azure DNS do rozpoznawania nazw dla innych usług platformy Azure
services: dns
documentationcenter: na
author: KumudD
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
ms.author: kumud
ms.openlocfilehash: 6d052bc82c35aa3f2fdf5b5820e3901bd5c4080d
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/30/2017
ms.locfileid: "25989847"
---
# <a name="how-azure-dns-works-with-other-azure-services"></a>Działanie usługi Azure DNS z innymi usługami Azure

Usługa DNS platformy Azure jest hostowanej usługi DNS zarządzania i nazwy rozwiązania. Pozwala na tworzenie publicznej nazwy DNS dla innych aplikacji i usług, które zostały wdrożone na platformie Azure. Tworzenie nazwę usługi Azure w domeny niestandardowej jest tak proste, jak dodawanie rekordu poprawnego typu usługi.

* W przypadku dynamicznie przydzielone adresy IP należy utworzyć rekord CNAME systemu DNS, który jest mapowany na nazwę DNS utworzony dla usługi Azure. Standardy usługi DNS uniemożliwić przy użyciu rekordu CNAME dla wierzchołku strefy.
* Dla statycznie przydzielone adresy IP, można utworzyć rekord A systemu DNS przy użyciu dowolnej nazwy, w tym *naked domeny* nazwie w wierzchołku strefy.

W poniższej tabeli przedstawiono typy obsługiwanych rekordów, które mogą służyć do różnych usług platformy Azure. Jak widać z tej tabeli, usługi DNS platformy Azure obsługuje tylko rekordy DNS dla zasobów sieciowych połączonych z Internetem. Usługa DNS platformy Azure nie może służyć do rozpoznawania nazw wewnętrznych adresów prywatnych.

| Usługa platformy Azure | Interfejs sieciowy | Opis |
| --- | --- | --- |
| Application Gateway |[Frontonu publicznego adresu IP](dns-custom-domain.md#public-ip-address) |Można utworzyć rekord DNS A lub CNAME. |
| Moduł równoważenia obciążenia |[Frontonu publicznego adresu IP](dns-custom-domain.md#public-ip-address)  |Można utworzyć rekord DNS A lub CNAME. Moduł równoważenia obciążenia mają adres IPv6 publicznego adresu IP, który jest przypisywany dynamicznie. W związku z tym należy utworzyć rekord CNAME dla adresu IPv6. |
| Traffic Manager |Nazwa publicznego |Można utworzyć tylko rekord CNAME, który jest mapowany na nazwę trafficmanager.net przypisane do profilu Menedżera ruchu. Aby uzyskać więcej informacji, zobacz [działa jak Menedżera ruchu](../traffic-manager/traffic-manager-overview.md#traffic-manager-example). |
| Usługę w chmurze, |[Publiczny adres IP](dns-custom-domain.md#public-ip-address) |Dla statycznie przydzielone adresy IP należy utworzyć rekord A systemu DNS. W przypadku dynamicznie przydzielone adresy IP, należy utworzyć rekord CNAME, który jest mapowany na *cloudapp.net* nazwy.|
| App Service | [Zewnętrznym adresem IP](dns-custom-domain.md#app-service-web-apps) |Dla zewnętrznych adresów IP należy utworzyć rekord A systemu DNS. W przeciwnym razie należy utworzyć rekord CNAME, który jest mapowany na nazwę azurewebsites.net. Aby uzyskać więcej informacji, zobacz [zamapować niestandardowej nazwy domeny do aplikacji Azure](../app-service/app-service-web-tutorial-custom-domain.md) |
| Maszyny wirtualne usługi Resource Manager |[Publiczny adres IP](dns-custom-domain.md#public-ip-address) |Menedżer zasobów maszyn wirtualnych może mieć publiczny adres IP. Maszyny Wirtualnej przy użyciu adresu publicznego adresu IP może być również za modułem równoważenia obciążenia. Można utworzyć rekord DNS A lub CNAME dla publicznego adresu. Tej nazwy niestandardowego może służyć do obejścia adresu VIP modułu równoważenia obciążenia. |
| Klasyczne maszyny wirtualne |[Publiczny adres IP](dns-custom-domain.md#public-ip-address) |Klasyczne maszyny wirtualne utworzone za pomocą programu PowerShell lub interfejsu wiersza polecenia można skonfigurować za pomocą dynamicznej lub statycznej (zastrzeżony) wirtualny adres. Można utworzyć rekordu CNAME systemu DNS lub rekord, odpowiednio. |
