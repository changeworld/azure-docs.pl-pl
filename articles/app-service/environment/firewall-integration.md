---
title: Zablokowanie ruchu wychodzącego w usłudze Azure App Service Environment
description: Opisuje sposób integrowania z zapory usługi Azure, aby zabezpieczyć ruch wychodzący
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 955a4d84-94ca-418d-aa79-b57a5eb8cb85
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/24/2018
ms.author: ccompy
ms.openlocfilehash: 5f2dd31488ae61bec061a81986a208bd328bf39b
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2018
ms.locfileid: "49093624"
---
# <a name="locking-down-an-app-service-environment"></a>Blokowanie środowiska usługi App Service

App Service Environment (ASE) ma wiele elementów zewnętrznych, które wymaga dostępu do do prawidłowego. Środowisko ASE znajduje się w klienta usługi Azure Virtual Network (VNet). Klienci muszą zezwalać na ruchu zależności środowiska ASE, która jest problemem dla klientów, które chcesz zablokować wszystkie ruch wychodzący z sieci wirtualnej.

Istnieje kilka zależności dla ruchu przychodzącego, które ma środowisko ASE. Nie można wysłać ruchu przychodzącego zarządzania za pomocą urządzenia zapory. Źródłowe adresy dla tego ruchu są znane i są publikowane w [adresy zarządzania środowiska App Service Environment](https://docs.microsoft.com/azure/app-service/environment/management-addresses) dokumentu. Reguły sieciowej grupy zabezpieczeń można utworzyć za pomocą tych informacji do zabezpieczenia ruchu przychodzącego.

Wychodzące zależności środowiska ASE prawie całkowicie są definiowane przy użyciu nazw FQDN, które nie mają adresów statycznych spodem. Brak statycznych adresów oznacza, że sieciowe grupy zabezpieczeń (NSG) nie można zablokować ruch wychodzący ze środowiska ASE. Zmianie adresów wystarczająco często nie jeden skonfigurować reguły na podstawie bieżącego rozdzielczości i użycie w celu utworzenia grup zabezpieczeń sieci. 

Rozwiązania do zabezpieczania wychodzące adresy zaletą korzystania z urządzenia zapory, które można kontrolować ruch wychodzący na podstawie nazw domen. Zespół Azure Networking wstrzymał nowe urządzenie sieciowe w wersji zapoznawczej o nazwie zapory usługi Azure. Zaporę usługi Azure jest w stanie ograniczenie wychodzących HTTP oraz ruch HTTPS na podstawie nazwy DNS miejsca docelowego.  

## <a name="configuring-azure-firewall-with-your-ase"></a>Konfigurowanie zapory platformy Azure za pomocą środowiska ASE 

Dostępne są następujące kroki, aby zablokować ruch wychodzący ze środowiska ASE przy użyciu zapory platformy Azure:

1. Tworzenie zapory platformy Azure w sieci wirtualnej, gdzie środowisko ASE są lub będą. [Usługa Azure zawiera zapory](https://docs.microsoft.com/azure/firewall/)
2. W interfejsie użytkownika zapory platformy Azure wybierz Tag FQDN środowiska usługi aplikacji
3. Utwórz tabelę tras z adresami zarządzania z [adresy zarządzania środowiska App Service Environment]( https://docs.microsoft.com/azure/app-service/environment/management-addresses) z następnym przeskokiem do Internetu. Wpisy tabeli trasy są wymagane, aby uniknąć problemów z routingiem asymetrycznym. 
4. Dodawanie tras do zależności adresów IP wymienionych poniżej, w zależności adresu IP z następnym przeskokiem do Internetu. 
5. Dodaj trasę do tabeli tras dla 0.0.0.0/0 za pomocą następnego przeskoku jest urządzenia sieciowego zapory usługi Azure
6. Tworzenie punktów końcowych usługi dla podsieci środowiska ASE, Azure SQL i Azure Storage
7. Przypisz tabeli tras, który został utworzony do podsieci środowiska ASE  

## <a name="application-traffic"></a>Ruch aplikacji 

Powyższych czynności umożliwi środowisku ASE działają bez problemów. Nadal należy skonfigurować rzeczy, które należy uwzględnić wymagania w zakresie aplikacji. Istnieją dwa problemy w przypadku aplikacji w środowisku ASE, który jest skonfigurowany z zapory usługi Azure.  

- Zależności aplikacji, nazwy FQDN, należy dodać do zapory usługi Azure lub tabeli tras
- Trasy musi zostać utworzony dla adresów, które ruchu będą pochodzić z, aby uniknąć problemów z routingu asymetrycznego

Twoje aplikacje są zależne, muszą zostać dodane do zapory usługi Azure. Tworzenie reguł aplikacji, które zezwalają na ruch HTTP/HTTPS i reguł sieciowych dla wszystkich innych. 

Jeśli znasz zakresu adresów, który ruch związany z żądaniami aplikacji będą pochodzić z, można dodać, że do tabeli tras przypisany do podsieci środowiska ASE. W przypadku dużych lub nieokreślony zakres adresów można użyć urządzenia sieciowego, takich jak Application Gateway zapewniają jednego adresu do dodania do tabeli tras. Aby uzyskać więcej informacji na temat konfigurowania bramy aplikacji przy użyciu środowiska ASE wewnętrznego modułu równoważenia obciążenia, przeczytaj [integrowanie środowiska ASE wewnętrznego modułu równoważenia obciążenia przy użyciu bramy aplikacji](https://docs.microsoft.com/azure/app-service/environment/integrate-with-application-gateway)


## <a name="dependencies"></a>Zależności

Azure App Service ma szeregu elementów zewnętrznych. One może być kategorycznie podzielone na kilka głównych obszarów:

- Punkt końcowy usługi, który stanie usługi należy skonfigurować punkty końcowe usługi z Jeśli chcesz zablokować wychodzącego ruchu sieciowego.
- Punktami końcowymi adresów IP nie zostały opisane z nazwą domeny. Może to być problem dla urządzeń zapory, które oczekują cały ruch HTTPS do korzystania z nazw domen. Punktami końcowymi adresów IP, należy dodać do tabeli tras, który jest ustawiony na podsieci środowiska ASE.
- Punktów końcowych HTTP/HTTPS nazwy FQDN można umieścić w urządzenia zapory.
- Symbol wieloznaczny HTTP/HTTPS punkty końcowe są zależności, które mogą się różnić, za pomocą środowiska ASE na podstawie liczby kwalifikatorów. 
- Zależności systemu Linux są tylko problemem, Jeżeli wdrażasz aplikacje systemu Linux w środowisku ASE. Gdy nie wdrażasz aplikacje systemu Linux w środowisku ASE, te adresy nie muszą zostać dodane do zapory. 


#### <a name="service-endpoint-capable-dependencies"></a>Zależności zdolne do punktu końcowego usługi 

| Endpoint |
|----------|
| Azure SQL |
| Azure Storage |
| Usługa Azure KeyVault |


#### <a name="ip-address-dependencies"></a>Zależności adresu IP 

| Endpoint |
|----------|
| 40.77.24.27:443 |
| 13.82.184.151:443 |
| 13.68.109.212:443 |
| 13.90.249.229:443 |
| 13.91.102.27:443 |
| 104.45.230.69:443 |
| 168.62.226.198:12000 |


#### <a name="fqdn-httphttps-dependencies"></a>Zależności w pełni kwalifikowaną nazwę domeny HTTP/HTTPS 

| Endpoint |
|----------|
|graph.windows.net:443 |
|login.live.com:443 |
|Login.Windows.com:443 |
|Login.Windows.NET:443 |
|login.microsoftonline.com:443 |
|Client.wns.Windows.com:443 |
|definitionupdates.microsoft.com:443 |
|go.microsoft.com:80 |
|go.microsoft.com:443 |
|www.microsoft.com:80 |
|www.microsoft.com:443 |
|wdcpalt.microsoft.com:443 |
|wdcp.microsoft.com:443 |
|OCSP.msocsp.com:443 |
|mscrl.microsoft.com:443 |
|mscrl.microsoft.com:80 |
|CRL.microsoft.com:443 |
|crl.microsoft.com:80 |
|www.Thawte.com:443 |
|crl3.digicert.com:80 |
|OCSP.digicert.com:80 |
|csc3 — 2009-2.crl.verisign.com:80 |
|CRL.VeriSign.com:80 |
|OCSP.VeriSign.com:80 |
|azperfcounters1.blob.Core.Windows .net: 443 |
|azurewatsonanalysis prod.core.windows.net:443 |
|Global.Metrics.nsatc.NET:80   |
|AZ prod.metrics.nsatc.net:443 |
|antares.Metrics.nsatc.NET:443 |
|azglobal black.azglobal.metrics.nsatc.net:443 |
|azglobal red.azglobal.metrics.nsatc.net:443 |
|dostawcy antares black.antares.metrics.nsatc.net:443 |
|dostawcy antares red.antares.metrics.nsatc.net:443 |
|maupdateaccount.blob.Core.Windows.NET:443 |
|clientconfig.Passport.NET:443 |
|Packages.microsoft.com:443 |
|schemas.microsoft.com:80 |
|schemas.microsoft.com:443 |
|Management.Core.Windows.NET:443 |
|Management.Core.Windows.NET:80 |
|www.msftconnecttest.com:80 |
|shavamanifestcdnprod1.azureedge .net: 443 |
|Sprawdzanie poprawności v2.sls.microsoft.com:443 |
|flighting.CP.WD.microsoft.com:443 |
|DMD.metaservices.microsoft.com:80 |
|Admin.Core.Windows.NET:443 |
|azureprofileruploads.blob.Core.Windows.NET:443 |
|azureprofileruploads2.blob.Core.Windows .net: 443 |
|azureprofileruploads3.blob.Core.Windows .net: 443 |
|azureprofileruploads4.blob.Core.Windows .net: 443 |
|azureprofileruploads5.blob.Core.Windows .net: 443 |

#### <a name="wildcard-httphttps-dependencies"></a>Symbol wieloznaczny HTTP/HTTPS zależności 

| Endpoint |
|----------|
|gr-Prod -\*. cloudapp.net:443 |
| \*. management.azure.com:443 |
| \*. update.microsoft.com:443 |
| \*. windowsupdate.microsoft.com:443 |
|grmdsprod\*mini\*. servicebus.windows.net:443 |
|grmdsprod\*dodaje się olej\*. servicebus.windows.net:443 |
|grsecprod\*mini\*. servicebus.windows.net:443 |
|grsecprod\*dodaje się olej\*. servicebus.windows.net:443 |
|graudprod\*mini\*. servicebus.windows.net:443 |
|graudprod\*dodaje się olej\*. servicebus.windows.net:443 |

#### <a name="linux-dependencies"></a>Zależności systemu Linux 

| Endpoint |
|----------|
|wawsinfraprodbay063.blob.Core.Windows .net: 443 |
|1.docker.io:443 rejestru |
|auth.docker.IO:443 |
|Production.cloudflare.docker.com:443 |
|Download.docker.com:443 |
|US.Archive.ubuntu.com:80 |
|Download.mono project.com:80 |
|Packages.treasuredata.com:80|
|Security.ubuntu.com:80 |

