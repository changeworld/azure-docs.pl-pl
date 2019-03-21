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
ms.date: 01/16/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 632fa14bd96eaee2ca58b59dd855584c1fd961e8
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/18/2019
ms.locfileid: "58010093"
---
# <a name="app-service-environment-management-addresses"></a>Adresy zarządzania środowiska usługi aplikacji

App Service Environment (ASE) to wdrożenie usługi Azure App Service w podsieci w usłudze Azure Virtual Network (VNet).  Środowisko ASE muszą być dostępne z płaszczyzny zarządzania używany przez usługę Azure App Service.  Ruch związany z zarządzaniem tym środowisku ASE jest przesyłany w sieci kontrolowanej przez użytkownika. Jeśli ten ruch jest blokowany, rozsyłanymi ASE zostanie wstrzymane. Aby uzyskać szczegółowe informacje o zależnościach sieci środowiska ASE, przeczytaj [sieć zagadnienia i App Service Environment][networking]. Aby uzyskać ogólne informacje na temat środowiska ASE, można uruchomić z [wprowadzenie do usługi App Service Environment][intro].

Wszystkie środowiska ASE ma publicznych adresów VIP, który ruch związany z zarządzaniem trafia do. Ruch przychodzący zarządzania, ze te adresy pochodzą z na portach 454 i 455 na publicznych adresów VIP środowiska ASE. Ten dokument zawiera listę adresów źródła usługi App Service dla zarządzania ruchu do środowiska ASE. Te adresy są dostępne w tagu usługi o nazwie AppServiceManagement.

Tag usługi o nazwie AppServiceManagement w sieciowych grupach zabezpieczeń można użyć w celu blokowania ruchu przychodzącego zarządzania do środowiska ASE.  

Można skonfigurować adresy podane poniżej w tabeli tras. Jest to ważne w przypadku, gdy działania środowiska ASE w życie tunelowania gdzie może wystąpić w przeciwnym razie problemu z routingiem asymetrycznym w sieci wirtualnej. Aby uzyskać więcej informacji na temat konfigurowania środowiska ASE do działania w środowisku, w których ruch wychodzący jest przesyłana w środowisku lokalnym, przeczytaj [Konfigurowanie środowiska ASE z wymuszonym tunelowaniem][forcedtunnel]

## <a name="list-of-management-addresses"></a>Lista adresów zarządzania ##

| Region | Adresy |
|--------|-----------|
| We wszystkich regionach publicznych | 70.37.57.58, 157.55.208.185, 52.174.22.21, 13.94.149.179, 13.94.143.126, 13.94.141.115, 52.178.195.197, 52.178.190.65, 52.178.184.149, 52.178.177.147, 13.75.127.117, 40.83.125.161, 40.83.121.56, 40.83.120.64, 52.187.56.50, 52.187.63.37, 52.187.59.251, 52.187.63.19, 52.165.158.140, 52.165.152.214, 52.165.154.193, 52.165.153.122, 104.44.129.255, 104.44.134.255, 104.44.129.243, 104.44.129.141, 23.102.188.65, 191.236.154.88, 13.64.115.203, 65.52.193.203, 70.37.89.222, 52.224.105.172, 23.102.135.246, 52.225.177.153, 65.52.172.237, 52.151.25.45, 40.124.47.188 |
| Microsoft Azure Government | 23.97.29.209, 13.72.53.37, 13.72.180.105, 23.97.0.17, 23.97.16.184 |

## <a name="configuring-a-network-security-group"></a>Konfigurowanie sieciowej grupy zabezpieczeń

Przy użyciu grup zabezpieczeń sieci nie trzeba już martwić się o poszczególne adresy lub utrzymywania własnej konfiguracji. Brak adresów IP usługi tag o nazwie AppServiceManagement, który jest aktualizowany na bieżąco ze wszystkimi adresami. Aby użyć tego tagu usługi IP w sieciowej grupie zabezpieczeń, przejdź do portalu, Otwórz interfejs użytkownika grup zabezpieczeń sieci i wybierz reguły zabezpieczeń ruchu przychodzącego. Jeśli masz już istniejącą regułę dla ruchu przychodzącego zarządzania go edytować. Jeśli ta sieciowa grupa zabezpieczeń nie został utworzony za pomocą środowiska ASE lub w przypadku wszystkich nowych, następnie wybierz pozycję **Dodaj**. W obszarze Źródło listy rozwijanej wybierz **Tag usługi**.  W obszarze tag usługi źródłowej, wybierz **AppServiceManagement**. Ustaw źródło zakresów portów \*, docelowe **wszelkie**, docelowe zakresy portów do **454 455**, protokół do **TCP**i działania w celu **Zezwalaj** . Jeśli wykonujesz reguły, a następnie należy ustawić priorytet. 

![Tworzenie sieciowej grupy zabezpieczeń za pomocą tag usługi][1]

## <a name="configuring-a-route-table"></a>Konfigurowanie tabelę tras

Adresy zarządzania można umieścić w tabeli tras z następnym przeskokiem do Internetu, aby upewnić się, że cały ruch przychodzący zarządzania jest w stanie wrócić do tej samej ścieżce. Te trasy są wymagane, gdy Konfigurowanie wymuszonego tunelowania. Aby utworzyć tabelę tras, można użyć witryny portal, programu PowerShell lub wiersza polecenia platformy Azure.  Polecenia, aby utworzyć tabelę tras z wiersz polecenia programu PowerShell przy użyciu wiersza polecenia platformy Azure są poniżej. 

    $rg = "resource group name"
    $rt = "route table name"
    $location = "azure location"
    az network route-table create --name $rt --resource-group $rg --location $location
    az network route-table route create -g $rg --route-table-name $rt -n 70.37.57.58 --next-hop-type Internet --address-prefix 70.37.57.58/32 
    az network route-table route create -g $rg --route-table-name $rt -n 157.55.208.185 --next-hop-type Internet --address-prefix 157.55.208.185/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.174.22.21 --next-hop-type Internet --address-prefix 52.174.22.21/32 
    az network route-table route create -g $rg --route-table-name $rt -n 13.94.149.179 --next-hop-type Internet --address-prefix 13.94.149.179/32
    az network route-table route create -g $rg --route-table-name $rt -n 13.94.143.126 --next-hop-type Internet --address-prefix 13.94.143.126/32
    az network route-table route create -g $rg --route-table-name $rt -n 13.94.141.115 --next-hop-type Internet --address-prefix 13.94.141.115/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.178.195.197 --next-hop-type Internet --address-prefix 52.178.195.197/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.178.190.65 --next-hop-type Internet --address-prefix 52.178.190.65/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.178.184.149 --next-hop-type Internet --address-prefix 52.178.184.149/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.178.177.147 --next-hop-type Internet --address-prefix 52.178.177.147/32
    az network route-table route create -g $rg --route-table-name $rt -n 13.75.127.117 --next-hop-type Internet --address-prefix 13.75.127.117/32
    az network route-table route create -g $rg --route-table-name $rt -n 40.83.125.161 --next-hop-type Internet --address-prefix 40.83.125.161/32
    az network route-table route create -g $rg --route-table-name $rt -n 40.83.121.56 --next-hop-type Internet --address-prefix 40.83.121.56/32
    az network route-table route create -g $rg --route-table-name $rt -n 40.83.120.64 --next-hop-type Internet --address-prefix 40.83.120.64/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.187.56.50 --next-hop-type Internet --address-prefix 52.187.56.50/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.187.63.37 --next-hop-type Internet --address-prefix 52.187.63.37/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.187.59.251 --next-hop-type Internet --address-prefix 52.187.59.251/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.187.63.19 --next-hop-type Internet --address-prefix 52.187.63.19/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.165.158.140 --next-hop-type Internet --address-prefix 52.165.158.140/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.165.152.214 --next-hop-type Internet --address-prefix 52.165.152.214/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.165.154.193 --next-hop-type Internet --address-prefix 52.165.154.193/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.165.153.122 --next-hop-type Internet --address-prefix 52.165.153.122/32
    az network route-table route create -g $rg --route-table-name $rt -n 104.44.129.255 --next-hop-type Internet --address-prefix 104.44.129.255/32
    az network route-table route create -g $rg --route-table-name $rt -n 104.44.134.255 --next-hop-type Internet --address-prefix 104.44.134.255/32
    az network route-table route create -g $rg --route-table-name $rt -n 104.44.129.243 --next-hop-type Internet --address-prefix 104.44.129.243/32
    az network route-table route create -g $rg --route-table-name $rt -n 104.44.129.141 --next-hop-type Internet --address-prefix 104.44.129.141/32
    az network route-table route create -g $rg --route-table-name $rt -n 23.102.188.65 --next-hop-type Internet --address-prefix 23.102.188.65/32
    az network route-table route create -g $rg --route-table-name $rt -n 191.236.154.88 --next-hop-type Internet --address-prefix 191.236.154.88/32
    az network route-table route create -g $rg --route-table-name $rt -n 13.64.115.203 --next-hop-type Internet --address-prefix 13.64.115.203/32
    az network route-table route create -g $rg --route-table-name $rt -n 65.52.193.203 --next-hop-type Internet --address-prefix 65.52.193.203/32
    az network route-table route create -g $rg --route-table-name $rt -n 70.37.89.222 --next-hop-type Internet --address-prefix 70.37.89.222/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.224.105.172 --next-hop-type Internet --address-prefix 52.224.105.172/32
    az network route-table route create -g $rg --route-table-name $rt -n 23.102.135.246 --next-hop-type Internet --address-prefix 23.102.135.246/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.225.177.153 --next-hop-type Internet --address-prefix 52.225.177.153/32
    az network route-table route create -g $rg --route-table-name $rt -n 65.52.172.237 --next-hop-type Internet --address-prefix 65.52.172.237/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.151.25.45 --next-hop-type Internet --address-prefix 52.151.25.45/32
    az network route-table route create -g $rg --route-table-name $rt -n 40.124.47.188 --next-hop-type Internet --address-prefix 40.124.47.188/32

Po utworzeniu tabeli tras, należy ustawić go na podsieci środowiska ASE.  

## <a name="get-your-management-addresses-from-api"></a>Pobierz swoje adresy zarządzania z interfejsu API ##

Możesz wyświetlić listę adresów zarządzania, które pasują do środowiska ASE przy użyciu następującego wywołania interfejsu API.

    get /subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Web/hostingEnvironments/<ASE Name>/inboundnetworkdependenciesendpoints?api-version=2016-09-01

Interfejs API zwraca dokument JSON, który zawiera wszystkie adresy ruchu przychodzącego dla środowiska ASE. Lista adresów zawiera adresy zarządzania wirtualne adresy IP używane przez środowisko ASE i zakres adresów podsieci środowiska ASE, sam.  

Do wywołania interfejsu API za pomocą [armclient](https://github.com/projectkudu/ARMClient) Użyj następujących poleceń, ale Zastąp identyfikator subskrypcji, grupy zasobów i nazwę środowiska ASE.  

    armclient login
    armclient get /subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Web/hostingEnvironments/<ASE Name>/inboundnetworkdependenciesendpoints?api-version=2016-09-01


<!--IMAGES-->
[1]: ./media/management-addresses/managementaddr-nsg.png

<!-- LINKS -->
[networking]: ./network-info.md
[intro]: ./intro.md
[forcedtunnel]: ./forced-tunnel-support.md
