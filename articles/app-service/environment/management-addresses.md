---
title: Adresy zarządzania środowiska usługi aplikacji — Azure
description: Wyświetla listę adresów zarządzania używane z poleceniem środowiska usługi App Service
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: a7738a24-89ef-43d3-bff1-77f43d5a3952
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/29/2018
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 7fb39886b19a2229188821eb39d4fb8a5928bb43
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/12/2018
ms.locfileid: "53276693"
---
# <a name="app-service-environment-management-addresses"></a>Adresy zarządzania środowiska usługi aplikacji

App Service Environment (ASE) to wdrożenie usługi Azure App Service w podsieci w usłudze Azure Virtual Network (VNet).  Środowisko ASE muszą być dostępne z płaszczyzny zarządzania używany przez usługę Azure App Service.  Ruch związany z zarządzaniem tym środowisku ASE jest przesyłany w sieci kontrolowanej przez użytkownika. Jeśli ten ruch jest blokowany, rozsyłanymi ASE zostanie wstrzymane. Aby uzyskać szczegółowe informacje o zależnościach sieci środowiska ASE, przeczytaj [sieć zagadnienia i App Service Environment][networking]. Aby uzyskać ogólne informacje na temat środowiska ASE, można uruchomić z [wprowadzenie do usługi App Service Environment][intro].

Ten dokument zawiera listę adresów źródłowych usługi App Service dla zarządzania ruchu do środowiska ASE i udostępnia dwie ważne do celów.  

1. Te adresy można użyć do tworzenia grup zabezpieczeń sieci do blokowania ruchu przychodzącego.  
2. Możesz tworzyć tras, przy użyciu tych adresów do obsługi wymuszonego tunelowania wdrożeń. Aby uzyskać więcej informacji na temat konfigurowania środowiska ASE do działania w środowisku, w których ruch wychodzący jest przesyłana w środowisku lokalnym, przeczytaj [Konfigurowanie środowiska ASE z wymuszonym tunelowaniem][forcedtunnel]

Wszystkie środowiska ASE ma publicznych adresów VIP, który ruch związany z zarządzaniem trafia do. Ruch przychodzący zarządzania, ze te adresy pochodzą z na portach 454 i 455 na publicznych adresów VIP środowiska ASE.  

## <a name="list-of-management-addresses"></a>Lista adresów zarządzania ##

| Region | Adresy |
|--------|-----------|
| We wszystkich regionach publicznych | 70.37.57.58 157.55.208.185, 52.174.22.21, 13.94.149.179, 13.94.143.126, 13.94.141.115, 52.178.195.197, 52.178.190.65, 52.178.184.149, 52.178.177.147, 13.75.127.117, 40.83.125.161, 40.83.121.56, 40.83.120.64, 52.187.56.50, 52.187.63.37, 52.187.59.251 52.187.63.19, 52.165.158.140, 52.165.152.214, 52.165.154.193, 52.165.153.122, 104.44.129.255, 104.44.134.255, 104.44.129.243, 104.44.129.141, 23.102.188.65, 191.236.154.88, 13.64.115.203, 65.52.193.203, 70.37.89.222, 52.224.105.172, 23.102.135.246 |
| Microsoft Azure dla instytucji rządowych (Fairfax lub MAG) | 23.97.29.209, 13.72.53.37, 13.72.180.105, 23.97.0.17, 23.97.16.184 |

## <a name="get-your-management-addresses-from-api"></a>Pobierz swoje adresy zarządzania z interfejsu API ##

Możesz wyświetlić listę adresów zarządzania, które pasują do środowiska ASE przy użyciu następującego wywołania interfejsu API.

    get /subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Web/hostingEnvironments/<ASE Name>/inboundnetworkdependenciesendpoints?api-version=2016-09-01

Interfejs API zwraca dokument JSON, który zawiera wszystkie adresy ruchu przychodzącego dla środowiska ASE. Lista adresów zawiera adresy zarządzania wirtualne adresy IP używane przez środowisko ASE i zakres adresów podsieci środowiska ASE, sam.  

Do wywołania interfejsu API za pomocą [armclient](https://github.com/projectkudu/ARMClient) Użyj następujących poleceń, ale Zastąp identyfikator subskrypcji, grupy zasobów i nazwę środowiska ASE.  

    armclient login
    armclient get /subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Web/hostingEnvironments/<ASE Name>/inboundnetworkdependenciesendpoints?api-version=2016-09-01


<!-- LINKS -->
[networking]: ./network-info.md
[intro]: ./intro.md
[forcedtunnel]: ./forced-tunnel-support.md
