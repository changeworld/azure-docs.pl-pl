---
title: Adresy zarządzania
description: Znajdź adresy zarządzania używane do sterowania środowiskiem usługi aplikacji. Skonfigurowano je w tabeli tras, aby uniknąć problemów z routingiem asymetrycznym.
author: ccompy
ms.assetid: a7738a24-89ef-43d3-bff1-77f43d5a3952
ms.topic: article
ms.date: 11/13/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 7d7f97552e8faadee1af928a9ce4e1eea2df476e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74687111"
---
# <a name="app-service-environment-management-addresses"></a>Adresy zarządzania środowiskiem usługi app service

Środowisko usługi aplikacji (ASE) to wdrożenie pojedynczej dzierżawy usługi Azure App Service, która działa w sieci wirtualnej platformy Azure.The App Service Environment (ASE) is a single tenant deployment of the Azure App Service that runs in your Azure Virtual Network (VNet).  Chociaż ase działa w sieci wirtualnej, nadal musi być dostępny z wielu dedykowanych adresów IP, które są używane przez usługę Azure App Service do zarządzania usługą.  W przypadku środowiska ASE ruch zarządzania przechodzi przez sieć kontrolowaną przez użytkownika. Jeśli ten ruch jest zablokowany lub nieprawidłowo, ASE zostanie zawieszony. Aby uzyskać szczegółowe informacje na temat zależności sieci ASE, przeczytaj [zagadnienia dotyczące sieci i środowiska usługi app service][networking]. Aby uzyskać ogólne informacje na temat środowiska ASE, można rozpocząć od [wprowadzenia do środowiska usługi app service][intro].

Wszystkie asy mają publiczny VIP, do którego wchodzi ruch zarządny. Przychodzący ruch zarządzania z tych adresów pochodzi z portów 454 i 455 na publiczny vip ase. Ten dokument zawiera listę adresów źródłowych usługi App Service dla ruchu zarządzania do ASE. Adresy te są również w tagu usługi IP o nazwie AppServiceManagement.

Adresy wymienione poniżej można skonfigurować w tabeli tras, aby uniknąć asymetrycznych problemów routingu z ruchem zarządzania. Trasy działają na ruch na poziomie IP i nie mają świadomości kierunku ruchu lub że ruch jest częścią komunikatu odpowiedzi TCP. Jeśli adres odpowiedzi dla żądania TCP jest inny niż adres, do którego został wysłany, występuje problem z routingiem asymetrycznym. Aby uniknąć asymetrycznych problemów routingu z ruchem zarządzania ASE, należy upewnić się, że odpowiedzi są wysyłane z powrotem z tego samego adresu, na który zostały wysłane. Aby uzyskać szczegółowe informacje na temat konfigurowania środowiska ASE do pracy w środowisku, w którym ruch wychodzący jest wysyłany lokalnie, przeczytaj artykuł [Konfigurowanie środowiska ASE z wymuszonym tunelowaniem][forcedtunnel]

## <a name="list-of-management-addresses"></a>Lista adresów zarządzania ##

| Region | Adresy |
|--------|-----------|
| Wszystkie regiony publiczne | 13.64.115.203, 13.66.140.0, 13.67.8.128, 13.69.64.128, 13.69.227.128, 13.70.73.128, 13.71.170.64, 13.71.194.129, 13.75.34.192, 13.75.127.117, 13.77.50.128, 13.78.109.0, 13.89.171.0, 13.94.141.115, 13.94.143.126, 13.94.149.179, 20.36.106.128, 20.36.114.64, 23.100.226.236, 23.102.135.246, 23.102.188.65, 40.69.106.128, 40.70.146.128, 40.71.13.64, 40.74.100.64, 40.78.194.128, 40.79.130.64, 40.79.178.128, 40.83.120.64, 40.83.121.56, 40.83.125.161, 40.90.240.166, 40.91.126.196, 40.112.242.192, 40.119.4.111, 40.124.47.188, 51.140.146.64, 51.140.210.128, 52.151.25.45, 52.162.80.89, 52.162.106.192, 52.165.152.214, 52.165.153.122, 52.165.154.193, 52.165.158.140, 52.174.22.21, 52.178.177.147, 52.178.184.149, 52.178.190.65, 52.178.195.197, 52.187.56.50, 52.187.59.251, 52.187.63.19, 52.187.63.37, 52.224.105.172, 52.225.177.153, 52.231.18.64, 52.231.146.128, 65.52.14.230, 65.52.172.237, 65.52.193.203, 70.37.57.58, 70.37.89.222, 104.43.242.137, 104.44.129.141, 104.44.129.243, 104.44.129.255, 104.44.134.255, 104.208.54.11, 104.211.81.64, 104.211.146.128, 104.214.49.0, 157.55.176.93, 157.55.208.185, 191.233.203.64, 191.236.154.88 |
| Platforma Microsoft Azure dla instytucji rządowych | 23.97.29.209, 13.72.53.37, 13.72.180.105, 23.97.0.17, 23.97.16.184 |

## <a name="configuring-a-network-security-group"></a>Konfigurowanie sieciowej grupy zabezpieczeń

Dzięki grupom zabezpieczeń sieciowych nie musisz martwić się o poszczególne adresy ani utrzymywać własnej konfiguracji. Istnieje tag usługi IP o nazwie AppServiceManagement, który jest aktualizowany ze wszystkimi adresami. Aby użyć tego tagu usługi IP w sieciowej grupy zabezpieczeń, przejdź do portalu, otwórz interfejs użytkownika sieciowych grup zabezpieczeń i wybierz pozycję Przychodzące reguły zabezpieczeń. Jeśli masz wcześniej istniejącą regułę dla ruchu zarządzania przychodzącego, edytuj ją. Jeśli ta nsg nie została utworzona za pomocą ase, lub jeśli jest wszystko nowe, a następnie wybierz **Dodaj**. W obszarze listy rozwijanej Źródło wybierz pozycję **Tag usługi**.  W obszarze tagu Usługi źródłowej wybierz pozycję **AppServiceManagement**. Ustaw zakresy portów \*źródłowych na , Kierunek na **Dowolny**, Zakresy portów docelowych na **454-455,** Protokół do **TCP**i **Akcja,** aby zezwolić . Jeśli tworzysz regułę, musisz ustawić priorytet. 

![tworzenie nsg z tagiem usługi][1]

## <a name="configuring-a-route-table"></a>Konfigurowanie tabeli marszruty

Adresy zarządzania można umieścić w tabeli tras z następnym przeskokiem internetu, aby zapewnić, że cały ruch zarządzania przychodzącymi jest w stanie wrócić tą samą ścieżką. Trasy te są potrzebne podczas konfigurowania wymuszonego tunelowania. Aby utworzyć tabelę tras, można użyć portalu, programu PowerShell lub interfejsu wiersza polecenia platformy Azure.  Polecenia do tworzenia tabeli marszruty przy użyciu interfejsu wiersza polecenia platformy Azure z monitu programu PowerShell znajdują się poniżej. 

    $rg = "resource group name"
    $rt = "route table name"
    $location = "azure location"
    $managementAddresses = "13.64.115.203", "13.66.140.0", "13.67.8.128", "13.69.64.128", "13.69.227.128", "13.70.73.128", "13.71.170.64", "13.71.194.129", "13.75.34.192", "13.75.127.117", "13.77.50.128", "13.78.109.0", "13.89.171.0", "13.94.141.115", "13.94.143.126", "13.94.149.179", "20.36.106.128", "20.36.114.64", "23.100.226.236", "23.102.135.246", "23.102.188.65", "40.69.106.128", "40.70.146.128", "40.71.13.64", "40.74.100.64", "40.78.194.128", "40.79.130.64", "40.79.178.128", "40.83.120.64", "40.83.121.56", "40.83.125.161", "40.90.240.166", "40.91.126.196", "40.112.242.192", "40.119.4.111", "40.124.47.188", "51.140.146.64", "51.140.210.128", "52.151.25.45", "52.162.80.89", "52.162.106.192", "52.165.152.214", "52.165.153.122", "52.165.154.193", "52.165.158.140", "52.174.22.21", "52.178.177.147", "52.178.184.149", "52.178.190.65", "52.178.195.197", "52.187.56.50", "52.187.59.251", "52.187.63.19", "52.187.63.37", "52.224.105.172", "52.225.177.153", "52.231.18.64", "52.231.146.128", "65.52.14.230", "65.52.172.237", "65.52.193.203", "70.37.57.58", "70.37.89.222", "104.43.242.137", "104.44.129.141", "104.44.129.243", "104.44.129.255", "104.44.134.255", "104.208.54.11", "104.211.81.64", "104.211.146.128", "104.214.49.0", "157.55.176.93", "157.55.208.185", "191.233.203.64", "191.236.154.88"

    az network route-table create --name $rt --resource-group $rg --location $location
    foreach ($ip in $managementAddresses) {
        az network route-table route create -g $rg --route-table-name $rt -n $ip --next-hop-type Internet --address-prefix ($ip + "/32")
    }

Po utworzeniu tabeli tras należy ustawić ją w podsieci ASE.  

## <a name="get-your-management-addresses-from-api"></a>Pobierz adresy zarządzania z interfejsu API ##

Można wyświetlić listę adresów zarządzania, które pasują do ase z następujących wywołań interfejsu API.

    get /subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Web/hostingEnvironments/<ASE Name>/inboundnetworkdependenciesendpoints?api-version=2016-09-01

Interfejs API zwraca dokument JSON, który zawiera wszystkie adresy przychodzące dla systemu ASE. Lista adresów zawiera adresy zarządzania, adres VIP używany przez ase i sam zakres adresów podsieci ASE.  

Aby wywołać interfejs API z [armclient](https://github.com/projectkudu/ARMClient) należy użyć następujących poleceń, ale zastąpić w identyfikator subskrypcji, grupa zasobów i nazwę ASE.  

    armclient login
    armclient get /subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Web/hostingEnvironments/<ASE Name>/inboundnetworkdependenciesendpoints?api-version=2016-09-01


<!--IMAGES-->
[1]: ./media/management-addresses/managementaddr-nsg.png

<!-- LINKS -->
[networking]: ./network-info.md
[intro]: ./intro.md
[forcedtunnel]: ./forced-tunnel-support.md
