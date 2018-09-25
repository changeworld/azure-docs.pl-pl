---
title: Używanie usługi Azure DNS z innymi usługami platformy Azure | Dokumentacja firmy Microsoft
description: Zrozumieć, jak używać usługi Azure DNS do rozpoznawania nazw dla innych usług platformy Azure
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
ms.openlocfilehash: 5d9886e16aa1921963f3d91d0fbd4da9287f7e54
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46989146"
---
# <a name="how-azure-dns-works-with-other-azure-services"></a>Jak usługa Azure DNS współpracuje z innymi usługami platformy Azure

System DNS Azure jest hostowanej usługi DNS zarządzania i nazwę rozwiązania. Dzięki temu można utworzyć publicznej nazwy DNS na inne aplikacje i usługi, które zostały wdrożone na platformie Azure. Tworzenie nazwę usługi platformy Azure w domenie niestandardowej jest proste i polega na dodanie rekordu poprawnego typu usługi.

* Dla dynamicznie przydzielone adresy IP można utworzyć rekord CNAME w systemie DNS, który mapowany na nazwę DNS, który Azure utworzony dla usługi. Standardy systemu DNS zapobiec użyciu rekordu CNAME na wierzchołku strefy, ale zamiast tego użyj rekordu aliasu. Aby uzyskać więcej informacji, zobacz [samouczek: Konfigurowanie rekordu aliasu do odwoływania się do platformy Azure publiczny adres IP](tutorial-alias-pip.md).
* Statycznie przydzielonych adresów IP, może utworzyć rekord A systemu DNS przy użyciu dowolnej nazwy w tym *domenę* nazwy w wierzchołku strefy.

W poniższej tabeli przedstawiono typy obsługiwanych rekordów, które mogą służyć do różnych usług platformy Azure. Jak widać w tej tabeli, system DNS Azure obsługuje tylko rekordy DNS dla zasobów sieciowych połączone z Internetem. Usługa DNS platformy Azure nie można używać do rozpoznawania nazw wewnętrznych, prywatne adresy.

| Usługa platformy Azure | Interfejs sieciowy | Opis |
| --- | --- | --- |
| Application Gateway |[Publiczny adres IP frontonu](dns-custom-domain.md#public-ip-address) |Można utworzyć rekord DNS A lub CNAME. |
| Moduł równoważenia obciążenia |[Publiczny adres IP frontonu](dns-custom-domain.md#public-ip-address)  |Można utworzyć rekord DNS A lub CNAME. Moduł równoważenia obciążenia może mieć adres publiczny adres IP protokołu IPv6, który jest dynamicznie przypisywany. W związku z tym należy utworzyć rekord CNAME dla adresu IPv6. |
| Traffic Manager |Publiczna nazwa |Możesz utworzyć rekord aliasu, który mapuje nazwę trafficmanager.net przypisane do profilu usługi Traffic Manager. Aby uzyskać więcej informacji, zobacz [samouczek: Konfigurowanie rekordu aliasu do obsługi języka apex nazw domen przy użyciu usługi Traffic Manager](tutorial-alias-tm.md). |
| Usługa w chmurze |[Publiczny adres IP](dns-custom-domain.md#public-ip-address) |Statycznie przydzielonych adresów IP można utworzyć rekord A systemu DNS. Dynamicznie przydzielone adresy IP, należy utworzyć rekord CNAME, który jest mapowany do *cloudapp.net* nazwy.|
| App Service | [Zewnętrzny adres IP](dns-custom-domain.md#app-service-web-apps) |Dla zewnętrznych adresów IP można utworzyć rekord A systemu DNS. W przeciwnym razie należy utworzyć rekord CNAME, który mapuje nazwę azurewebsites.net. Aby uzyskać więcej informacji, zobacz [zamapować niestandardową nazwę domeny na aplikację platformy Azure](../app-service/app-service-web-tutorial-custom-domain.md) |
| Maszyny wirtualne usługi Resource Manager |[Publiczny adres IP](dns-custom-domain.md#public-ip-address) |Maszyny wirtualne usługi Resource Manager może mieć publicznych adresów IP. Maszyna wirtualna z publicznego adresu IP mogą być również za modułem równoważenia obciążenia. Można utworzyć, A systemu DNS, CNAME lub rekordu aliasu dla publicznego adresu. Tej niestandardowej nazwy może służyć do pominięcia adresów VIP modułu równoważenia obciążenia. |
| Klasyczne maszyny wirtualne |[Publiczny adres IP](dns-custom-domain.md#public-ip-address) |Klasyczne maszyny wirtualne utworzone przy użyciu programu PowerShell lub interfejsu wiersza polecenia można skonfigurować za pomocą dynamiczny lub statyczny (zastrzeżony) wirtualny adres. Możesz utworzyć rekord CNAME DNS lub rekord, odpowiednio. |
