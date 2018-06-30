---
title: Azure stosu integracji datacenter — publikowanie punktów końcowych | Dokumentacja firmy Microsoft
description: Dowiedz się, jak publikować punkty końcowe platformy Azure stosu w centrum danych.
services: azure-stack
author: jeffgilb
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 06/29/2018
ms.author: jeffgilb
ms.reviewer: wamota
keywords: ''
ms.openlocfilehash: 0992846416ce77bccd23fda73f61568eb61c33fb
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37127639"
---
# <a name="azure-stack-datacenter-integration---publish-endpoints"></a>Azure stosu integracji datacenter — publikować punkty końcowe
Stos Azure konfiguruje wirtualnych adresów IP (VIP) do jego role infrastruktury. Te adresy VIP są przydzielone z puli publicznych adresów IP. Każdy adres VIP jest zabezpieczony z listy kontroli dostępu (ACL) w warstwie sieci zdefiniowanych przez oprogramowanie. Listy ACL są również używane przez przełączniki fizyczne (torach i BMC) dodatkowo zabezpieczyć rozwiązanie. Wpis DNS jest tworzony dla każdego punktu końcowego w strefie DNS zewnętrznego, który określono w czasie wdrażania.


Na poniższym diagramie architektury przedstawiono warstwy inną sieć i listy kontroli dostępu:

![Obraz strukturalnych](media/azure-stack-integrate-endpoints/Integrate-Endpoints-01.png)

## <a name="ports-and-protocols-inbound"></a>Porty i protokoły (ruch przychodzący)

Zestaw infrastruktury wirtualne adresy IP są wymagane do publikowania stosu Azure punktów końcowych sieci zewnętrznych. *Punktu końcowego (VIP)* tabela pokazuje każde punktu końcowego, wymagany port i protokół. Zapoznaj się z dokumentacją wdrożeniową dostawcy zasobów specyficznych dla punktów końcowych, które wymagają dodatkowych zasobów dostawców, takiego jak dostawca zasobów SQL.

Wewnętrzna infrastruktura adresy VIP nie są wyświetlane, ponieważ nie są one wymagane do publikowania stosu Azure.

> [!NOTE]
> Adresy VIP użytkownika są dynamiczne, zdefiniowany przez użytkowników się za pomocą formantu nie przez podmiot stosu Azure.


|Punkt końcowy (VIP)|Rekord hosta DNS|Protokół|Porty|
|---------|---------|---------|---------|
|AD FS|Adfs.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|Portal (administrator)|Adminportal.*&lt;region>.&lt;fqdn>*|HTTPS|443<br>12495<br>12499<br>12646<br>12647<br>12648<br>12649<br>12650<br>13001<br>13003<br>13010<br>13011<br>13012<br>13020<br>13021<br>13026<br>30015|
|Usługa Azure Resource Manager (administrator)|Adminmanagement.*&lt;region>.&lt;fqdn>*|HTTPS|443<br>30024|
|Portal (użytkownika)|Portal.*&lt;region>.&lt;fqdn>*|HTTPS|443<br>12495<br>12649<br>13001<br>13010<br>13011<br>13012<br>13020<br>13021<br>30015<br>13003|
|Usługa Azure Resource Manager (użytkownika)|Management.*&lt;region>.&lt;fqdn>*|HTTPS|443<br>30024|
|Graph|Graph.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|Listy odwołania certyfikatów|Crl.*&lt;region>.&lt;fqdn>*|HTTP|80|
|DNS|&#42;.*&lt;region>.&lt;fqdn>*|TCP I UDP|53|
|Magazyn kluczy (użytkownika)|&#42;.vault.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|Magazyn kluczy (administrator)|&#42;.adminvault.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|Kolejka magazynu|&#42;.queue.*&lt;region>.&lt;fqdn>*|HTTP<br>HTTPS|80<br>443|
|Tabela magazynu|&#42;.table.*&lt;region>.&lt;fqdn>*|HTTP<br>HTTPS|80<br>443|
|Storage Blob|&#42;.blob.*&lt;region>.&lt;fqdn>*|HTTP<br>HTTPS|80<br>443|
|Dostawca zasobów SQL|sqladapter.dbadapter.*&lt;region>.&lt;fqdn>*|HTTPS|44300-44304|
|Dostawca zasobów MySQL|mysqladapter.dbadapter.*&lt;region>.&lt;fqdn>*|HTTPS|44300-44304|
|App Service|&#42;.appservice.*&lt;region>.&lt;fqdn>*|TCP|80 (HTTP)<br>443 (HTTPS)<br>8172 (MSDeploy)|
|  |&#42;.scm.appservice.*&lt;region>.&lt;fqdn>*|TCP|443 (HTTPS)|
|  |api.appservice.*&lt;region>.&lt;fqdn>*|TCP|443 (HTTPS)<br>44300 (usługa azure Resource Manager)|
|  |ftp.appservice.*&lt;region>.&lt;fqdn>*|TCP, UDP|21, 1021, 10001-101000 (FTP)<br>990 (FTPS)|
|Bramy VPN Gateway|     |     |[Zobacz bramy sieci VPN — często zadawane pytania](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-vpn-faq#can-i-traverse-proxies-and-firewalls-using-point-to-site-capability).|
|     |     |     |     |

## <a name="ports-and-urls-outbound"></a>Porty i adresy URL (wychodzące)

Stos Azure obsługuje tylko serwery przezroczystego obiektu pośredniczącego. W przypadku wdrożenia w przypadku, gdy pasm przezroczystego obiektu pośredniczącego, aby serwer proxy tradycyjnych musisz zezwolić następujące porty i adresy URL, dla komunikacji wychodzącej:


|Przeznaczenie|Adres URL|Protokół|Porty|
|---------|---------|---------|---------|
|Tożsamość|login.windows.net<br>login.microsoftonline.com<br>graph.windows.net<br>https://secure.aadcdn.microsoftonline-p.com|HTTP<br>HTTPS|80<br>443|
|Syndykacja Marketplace|https://management.azure.com<br>https://&#42;.blob.core.windows.net<br>https://*.azureedge.net<br>https://&#42;.microsoftazurestack.com|HTTPS|443|
|Poprawek i aktualizacji|https://&#42;.azureedge.net|HTTPS|443|
|Rejestracja|https://management.azure.com|HTTPS|443|
|Sposób użycia|https://&#42;.microsoftazurestack.com<br>https://*.trafficmanager.NET|HTTPS|443|
|Usługa Windows Defender|. wdcp.microsoft.com<br>. wdcpalt.microsoft.com<br>*. updates.microsoft.com<br>*. witrynie download.microsoft.com<br>https://msdl.microsoft.com/download/symbols<br>http://www.microsoft.com/pkiops/crl<br>http://www.microsoft.com/pkiops/certs<br>http://crl.microsoft.com/pki/crl/products<br>http://www.microsoft.com/pki/certs<br>https://secure.aadcdn.microsoftonline-p.com<br>|HTTPS|80<br>443|
|NTP|     |UDP|123|
|DNS|     |TCP<br>UDP|53|
|     |     |     |     |



## <a name="next-steps"></a>Kolejne kroki

[Wymagania dotyczące usługi Azure stosu infrastruktury kluczy publicznych](azure-stack-pki-certs.md)
