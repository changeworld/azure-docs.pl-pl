---
title: Wymagania dotyczące systemu Microsoft Azure Data Box Gateway | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o oprogramowaniu i wymagania sieciowe dla bramy pola danych platformy Azure
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 10/17/2018
ms.author: alkohli
ms.openlocfilehash: da22c09a227069af0eeb42ab67a59189ae494185
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/11/2018
ms.locfileid: "53256676"
---
# <a name="azure-data-box-gateway-system-requirements-preview"></a>Wymagania systemowe bramy pola danych platformy Azure (wersja zapoznawcza)

W tym artykule opisano wymagania systemowe dla rozwiązania Microsoft Azure Data Box Gateway oraz klientów nawiązywania połączenia z bramą pola danych platformy Azure. Firma Microsoft zaleca, aby zapoznać się z informacjami dokładnie przed wdrożeniem bramy pola danych i następnie wrócić do niego zgodnie z potrzebami podczas wdrażania i kolejna operacja.

Wymagania systemowe dla urządzenia wirtualnego bramy pola danych obejmują:

- **Wymagania programowe dotyczące hostów** — zawiera opis obsługiwanych platform, przeglądarki dla interfejsu użytkownika konfiguracji lokalnej, klientów protokołu SMB i wszelkie dodatkowe wymagania dla hostów, które łączą się z urządzeniem.
- **Wymagania dotyczące sieci dla urządzenia** — informacje na temat wszelkie wymagania sieciowe związane z działaniem urządzenia wirtualnego.

> [!IMPORTANT]
> Usługa Data Box Gateway jest dostępna w wersji zapoznawczej. Zapoznaj się z [warunkami użytkowania wersji zapoznawczej](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) przed wdrożeniem tego rozwiązania. 

## <a name="specifications-for-the-virtual-device"></a>Specyfikacje dotyczące urządzeń wirtualnych

Bazowego systemu hosta dla bramy pole danych jest w stanie dedykować następujących zasobów, aby aprowizować urządzenie wirtualne:

| Specyfikacje                                          | Opis              |
|---------------------------------------------------------|--------------------------|
| Procesory wirtualne (rdzenie)   | Minimum 4 |            
| Memory (Pamięć)  | Minimum 8 GB|
| Dostępność|Jeden węzeł|
| Dyski| Dysk systemu operacyjnego: 250 GB <br> Dysk z danymi: Co najmniej 2 TB, cienka obsługiwana i musi być objęta dysków SSD|
| Interfejsy sieciowe|Co najmniej 1 wirtualny interfejs sieciowy|


## <a name="supported-os-for-clients-connected-to-device"></a>Obsługiwane systemy operacyjne dla klientów podłączonych do urządzenia

Poniżej przedstawiono listę obsługiwanych systemów operacyjnych dla klientów lub hostów podłączonych do bramy pola danych.

| **System operacyjny i platformy** | **Wersje** |
| --- | --- |
| Windows Server |2012 R2 <br> 2016 |
| Windows |8, 10 |
| SUSE Linux |Enterprise Server 12 (x86_64)|
| Ubuntu |16.04.3 LTS|
| CentOS | 7.0 |

## <a name="supported-protocols-for-clients-accessing-device"></a>Obsługiwane protokoły dla klientów uzyskujących dostęp do urządzenia

|**Protokół** |**Wersje**   |**Uwagi**  |
|---------|---------|---------|
|SMB    | 2.X, 3.X      | Protokół SMB 1 nie jest obsługiwane.|
|NFS     | W wersji 3 i 4        |         |

## <a name="supported-virtualization-platforms-for-device"></a>Platformy obsługiwane wirtualizacji dla urządzenia

| **System operacyjny i platformy**  |**Wersje**   |**Uwagi**  |
|---------|---------|---------|
|Funkcja Hyper-V  |  2012 R2 <br> 2016  |         |
|Host VMware ESXi     | 6.0 <br> 6.5        |Narzędzia VMware nie są obsługiwane.         |


## <a name="supported-storage-accounts"></a>Konta magazynu obsługiwane

Poniżej przedstawiono listę kont magazynu obsługiwanych dla bramy pola danych.

| **Konto magazynu** | **Uwagi** |
| --- | --- |
| Wdrożenie klasyczne | Standardowa (Standard) |
| Ogólne zastosowanie  |Standardowe; obsługiwane są zarówno V1 i V2. Obsługiwane są zarówno gorące i chłodne warstwy. |


## <a name="supported-storage-types"></a>Obsługiwane typy

Poniżej przedstawiono listę typów magazynu obsługiwane dla bramy pola danych.

| **Format pliku** | **Uwagi** |
| --- | --- |
| Usługa Azure blokowych obiektów blob | |
| Obiekt blob typu page platformy Azure  | |
| Azure Files | |

## <a name="supported-browsers-for-local-web-ui"></a>Przeglądarki obsługiwane przez lokalnego internetowego interfejsu użytkownika

Poniżej przedstawiono listę przeglądarek obsługiwanych dla lokalnego internetowego interfejsu użytkownika urządzenia wirtualnego.

|Przeglądarka  |Wersje  |Dodatkowe wymagania dotyczące/uwagi  |
|---------|---------|---------|
|Google Chrome   |Najnowsza wersja         |         |
|Microsoft Edge    | Najnowsza wersja        |         |
|Internet Explorer     | Najnowsza wersja        |         |
|FireFox    |Najnowsza wersja         |         |


## <a name="networking-requirements"></a>Wymagania dotyczące sieci

Poniższa tabela zawiera listę portów, które muszą być otwarte w zaporze, aby umożliwić SMB, w chmurze lub ruch związany z zarządzaniem. W tej tabeli *w* lub *dla ruchu przychodzącego* odnosi się do kierunku z które dostępu przychodzących żądań klienta do Twojego urządzenia. *Limit* lub *wychodzącego* Określa kierunek, w którym urządzenie bramy pola danych wysyła dane zewnętrznie, poza wdrożenia: na przykład, ruch wychodzący do Internetu.

| Nr portu.| Wewnątrz lub na zewnątrz | Zakres portów| Wymagane|   Uwagi                                                             |                                                                                     |
|--------|---------|----------|--------------|----------------------|---------------|
| TCP 80 (HTTP)|Wyjście|WAN |Nie|Wychodząca przez port służy do dostępu do Internetu do pobierania aktualizacji. <br>Serwer proxy ruchu wychodzącego w sieci web jest użytkownika. |                          
| TCP 443 (HTTPS)|Wyjście|WAN|Yes|Wychodząca przez port jest używany do uzyskiwania dostępu do danych w chmurze.<br>Serwer proxy ruchu wychodzącego w sieci web jest użytkownika.|   
| UDP 53 (DNS)|Wyjście|WAN|W niektórych przypadkach<br>Zobacz uwagi|Ten port jest wymagany tylko wtedy, gdy używasz serwera DNS internetowego.<br>Zalecamy używanie lokalnego serwera DNS. |
| UDP 123 (NTP)|Wyjście|WAN|W niektórych przypadkach<br>Zobacz uwagi|Ten port jest wymagany tylko wtedy, gdy korzystają z serwera NTP oparty na Internecie.  |
| UDP 67 (DHCP)|Wyjście|WAN|W niektórych przypadkach<br>Zobacz uwagi|Ten port jest wymagany tylko wtedy, gdy jest używany serwer DHCP.  |
| TCP 80 (HTTP)|W|Sieć LAN|Yes|Jest to port wejściowy dla lokalnego interfejsu użytkownika na urządzeniu na potrzeby zarządzania lokalnego. <br>Uzyskiwanie dostępu do lokalnego interfejsu użytkownika za pośrednictwem protokołu HTTP automatycznie spowoduje przekierowanie do protokołu HTTPS.  | 
| TCP 443 (HTTPS)|W|Sieć LAN|Yes|Jest to port wejściowy dla lokalnego interfejsu użytkownika na urządzeniu na potrzeby zarządzania lokalnego. | 

## <a name="url-patterns-for-firewall-rules"></a>Wzorce adresów URL dla reguł zapory

Administratorzy sieci często można skonfigurować reguły zapory zaawansowane, na podstawie wzorców adresów URL, do filtrowania przychodzącego i wychodzącego ruchu. Urządzenie Data Box bramy i Usługa bramy pola danych są zależne od innych aplikacji firmy Microsoft, takich jak usługi Azure Service Bus, Azure Active Directory Access Control, kont magazynu i serwerami usługi Microsoft Update. Wzorce adresów URL skojarzone z tymi aplikacjami umożliwia konfigurowanie reguł zapory. Jest ważne dowiedzieć się, że wzorce adresów URL skojarzone z tymi aplikacjami można zmienić. Wymaga to co z kolei administrator sieci monitorować i aktualizować reguły zapory dla bramy pola danych jako i potrzebny.

Firma Microsoft zaleca ustawienie reguły zapory dla ruchu wychodzącego, oparte na stałe adresy IP, liberally, w większości przypadków bramy pola danych. Jednak można użyć poniższych informacji do ustawiania reguły zapory zaawansowanych, które są potrzebne do tworzenia bezpiecznych środowisk.

> [!NOTE]
> - Urządzenia (źródło) adresy IP powinny być zawsze ustawiony wszystkie interfejsy sieciowe z obsługą chmury.
> - Miejsce docelowe adresy IP powinien być ustawiony na [zakresy IP centrów danych platformy Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653).

|     Wzorzec URL                                                                                                                                                                                                                                                                                                                                                                                                                                       |     Składnik/funkcji                                                                             |
|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------|
|    https://*.databoxedge.azure.com/*<br>https://*.servicebus.windows.net/*<br>https://login.windows.net                                                                                                                                                                                                                                                                                                        |    Usługa Azure Data Box bramy<br>Azure Service Bus<br>Usługa uwierzytelniania    |
|    http://*.backup.windowsazure.com                                                                                                                                                                                                                                                                                                                                                                                                                   |    Aktywacja urządzenia                                                                                    |
|    http://crl.microsoft.com/pki/*   http://www.microsoft.com/pki/*                                                                                                                                                                                                                                                                                                                                                                                    |    Odwoływanie certyfikatów                                                                               |
|    https://*.core.windows.net/* https://*. data.microsoft.com http://*. msftncsi.com                                                                                                                                                                                                                                                                                                                                                                |    Konta usługi Azure storage i monitorowania                                                                |
|    http://windowsupdate.microsoft.com<br>http://*. windowsupdate.microsoft.com<br>https://*. windowsupdate.microsoft.com<br>http://*. update.microsoft.com<br>https://*. update.microsoft.com<br>http://*. windowsupdate.com<br>http://download.microsoft.com<br>http://*. download.windowsupdate.com<br>http://wustat.windows.com<br>http://ntservicepack.microsoft.com<br>http://*. ws.microsoft.com<br>https://*. ws.microsoft.com<br>http://*.MP.microsoft.com        |    Serwerami usługi Microsoft Update                                                                             |
|    http://*.Deploy.akamaitechnologies.com                                                                                                                                                                                                                                                                                                                                                                                                             |    Akamai CDN                                                                                           |
|    https://*.partners.extranet.microsoft.com/*                                                                                                                                                                                                                                                                                                                                                                                                        |    Pakiet dla pomocy technicznej                                                                                      |
|    http://*.Data.microsoft.com                                                                                                                                                                                                                                                                                                                                                                                                                        |    Usługa telemetrii w Windows, zobacz aktualizację komfort i diagnostycznych telemetrii      |
|                                                                                                                                                                                                                                                                                                                                                                                                                                                       |                                                                                                         |



## <a name="internet-bandwidth"></a>Przepustowością Internetu

Poniższe wymagania dotyczą minimalnej przepustowości Internetu, dostępne dla urządzenia bramy pola danych.

- Twoja usługa Data Box Gateway dysponuje przez cały czas dedykowanym połączeniem internetowym o przepustowości co najmniej 20 Mb/s. Ta przepustowość nie powinna być współużytkowana z żadną inną aplikacją. 
- Twoja brama Data Gateway pole ma dedykowany 32 przepustowości Internetu MB/s (lub więcej) przy użyciu ograniczanie przepustowości sieci.

## <a name="next-step"></a>Następny krok

* [Wdrażanie bramy usługi Azure Data Box](data-box-gateway-deploy-prep.md)

