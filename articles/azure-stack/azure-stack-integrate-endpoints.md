---
title: Azure Stack — Integracja z centrum danych — publikowanie punktów końcowych | Dokumentacja firmy Microsoft
description: Dowiedz się, jak publikowanie punktów końcowych usługi Azure Stack w centrum danych
services: azure-stack
author: jeffgilb
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 12/06/2018
ms.author: jeffgilb
ms.reviewer: wamota
ms.lastreviewed: 12/06/2018
keywords: ''
ms.openlocfilehash: 5946f62821d05bd9036b9fc0e6b0fc8daa74c5dc
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/30/2019
ms.locfileid: "55241206"
---
# <a name="azure-stack-datacenter-integration---publish-endpoints"></a>Azure Stack — Integracja z centrum danych — publikowanie punktów końcowych

Usługa Azure Stack konfiguruje wirtualnych adresów IP (VIP) dla ich ról infrastruktury. Te adresy VIP są przydzielone z puli publicznych adresów IP. Każdy adres VIP jest zabezpieczony za pomocą listy kontroli dostępu (ACL) w warstwie sieci definiowanej przez oprogramowanie. Listy ACL są również używane w przełącznikach fizycznych (Filtruj i kontrolera zarządzania płytą GŁÓWNĄ) dodatkowo zabezpieczyć rozwiązanie. Wpis DNS jest tworzony dla każdego punktu końcowego w strefie DNS zewnętrznego, który określono w czasie wdrażania.


Poniższy diagram architektury przedstawia warstwy innej sieci i listami kontroli dostępu:

![Obraz strukturalnych](media/azure-stack-integrate-endpoints/Integrate-Endpoints-01.png)

## <a name="ports-and-protocols-inbound"></a>Porty i protokoły (ruch przychodzący)

Zbiór infrastruktury adresów VIP jest wymagany dla publikacji usługi Azure Stack punktów końcowych sieci zewnętrznych. *Punktu końcowego (VIP)* tabeli przedstawiono każdy punkt końcowy, wymaganego portu i protokołu. Zapoznaj się z dokumentacją wdrożeniową dostawcy zasobów specyficznych dla punktów końcowych, które wymagają dostawców dodatkowych zasobów, takich jak dostawcy zasobów bazy danych SQL.

Nie są wyświetlane wewnętrznej infrastruktury adresów VIP, ponieważ nie są wymagane dla publikowania usługi Azure Stack.

> [!Note]  
> Użytkownik VIP są dynamiczne, zdefiniowane przez samych użytkowników z żadnej kontroli przez operatora infrastruktury Azure Stack.

|Punkt końcowy (VIP)|Rekord hosta DNS|Protokół|Porty|
|---------|---------|---------|---------|
|AD FS|Adfs.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|Portal (administrator)|Adminportal.*&lt;region>.&lt;fqdn>*|HTTPS|443<br>12495<br>12499<br>12646<br>12647<br>12648<br>12649<br>12650<br>13001<br>13003<br>13010<br>13011<br>13012<br>13020<br>13021<br>13026<br>30015|
|Adminhosting | *.adminhosting.\<region>.\<fqdn> | HTTPS | 443 |
|Usługa Azure Resource Manager (administrator)|Adminmanagement.*&lt;region>.&lt;fqdn>*|HTTPS|443<br>30024|
|Portal (użytkownik)|Portal.*&lt;region>.&lt;fqdn>*|HTTPS|443<br>12495<br>12649<br>13001<br>13010<br>13011<br>13012<br>13020<br>13021<br>30015<br>13003|
|Usługa Azure Resource Manager (użytkownik)|Management.*&lt;region>.&lt;fqdn>*|HTTPS|443<br>30024|
|Graph|Graph.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|Lista odwołania certyfikatów|Crl.*&lt;region>.&lt;fqdn>*|HTTP|80|
|DNS|&#42;.*&lt;region>.&lt;fqdn>*|TCP I UDP|53|
|Hosting | *.hosting.\<region>.\<fqdn> | HTTPS | 443 |
|Usługa Key Vault (użytkownik)|&#42;.vault.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|Usługa Key Vault (administrator)|&#42;.adminvault.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|Kolejka magazynu|&#42;.queue.*&lt;region>.&lt;fqdn>*|HTTP<br>HTTPS|80<br>443|
|Tabela magazynu|&#42;.table.*&lt;region>.&lt;fqdn>*|HTTP<br>HTTPS|80<br>443|
|Storage Blob|&#42;.blob.*&lt;region>.&lt;fqdn>*|HTTP<br>HTTPS|80<br>443|
|Dostawcy zasobów bazy danych SQL|sqladapter.dbadapter.*&lt;region>.&lt;fqdn>*|HTTPS|44300-44304|
|Dostawcy zasobów MySQL|mysqladapter.dbadapter.*&lt;region>.&lt;fqdn>*|HTTPS|44300-44304|
|App Service|&#42;.appservice.*&lt;region>.&lt;fqdn>*|TCP|80 (HTTP)<br>443 (HTTPS)<br>8172 (MSDeploy)|
|  |&#42;.scm.appservice.*&lt;region>.&lt;fqdn>*|TCP|443 (HTTPS)|
|  |api.appservice.*&lt;region>.&lt;fqdn>*|TCP|443 (HTTPS)<br>44300 (usługa azure Resource Manager)|
|  |ftp.appservice.*&lt;region>.&lt;fqdn>*|TCP, UDP|21, 1021, 10001-10100 (FTP)<br>990 (FTPS)|
|Bramy sieci VPN|     |     |[Zobacz bramy sieci VPN — często zadawane pytania](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-vpn-faq#can-i-traverse-proxies-and-firewalls-using-point-to-site-capability).|
|     |     |     |     |

## <a name="ports-and-urls-outbound"></a>Porty i adresy URL (ruch wychodzący)

Usługa Azure Stack obsługuje tylko serwery z przezroczystym serwerem proxy. W przypadku wdrożenia w przypadku, gdy pasm przezroczystym serwerem proxy, aby serwer proxy tradycyjnych musisz zezwolić na następujących portów i adresów URL dla komunikacji wychodzącej:

> [!Note]  
> Usługa Azure Stack nie obsługuje używania Express Route do uzyskania dostępu do usług platformy Azure, przedstawione w poniższej tabeli.

|Przeznaczenie|Adres URL|Protokół|Porty|
|---------|---------|---------|---------|
|Tożsamość|login.windows.net<br>login.microsoftonline.com<br>graph.windows.net<br>https://secure.aadcdn.microsoftonline-p.com<br>office.com|HTTP<br>HTTPS|80<br>443|
|Syndykacja witryny Marketplace|https://management.azure.com<br>https://&#42;.blob.core.windows.net<br>https://*.azureedge.net<br>https://&#42;.microsoftazurestack.com|HTTPS|443|
|Akt & poprawki|https://&#42;.azureedge.net|HTTPS|443|
|Rejestracja|https://management.azure.com|HTTPS|443|
|Sposób użycia|https://&#42;.microsoftazurestack.com<br>https://*.trafficmanager.net|HTTPS|443|
|Windows Defender|.wdcp.microsoft.com<br>.wdcpalt.microsoft.com<br>*. updates.microsoft.com<br>*. witrynie download.microsoft.com<br>https://msdl.microsoft.com/download/symbols<br>`https://www.microsoft.com/pkiops/crl`<br>`https://www.microsoft.com/pkiops/certs`<br>`https://crl.microsoft.com/pki/crl/products`<br>`https://www.microsoft.com/pki/certs`<br>https://secure.aadcdn.microsoftonline-p.com<br>|HTTPS|80<br>443|
|NTP|(Dostarczone dla wdrożenia serwera IP NTP)|UDP|123|
|DNS|(Dostarczone dla wdrożenia serwera IP DNS)|TCP<br>UDP|53|
|LISTY CRL|Adres URL (w ramach punktów dystrybucji listy CRL na Twój certyfikat)|HTTP|80|
|Tworzenie kopii zapasowych|(IP lub nazwa FQDN serwera plików zewnętrznego docelowego)|SMB|445|
|     |     |     |     |

> [!Note]  
> Wychodzące adresy URL są równoważone za pomocą usługi Azure traffic manager, aby zapewnić najlepszą łączność możliwe na podstawie lokalizacji geograficznej. Adresy URL równoważenia obciążenia, zaktualizować i zmienić punktów końcowych zaplecza bez wywierania wpływu na klientów firmy Microsoft. Firma Microsoft udostępnia listę adresów IP dla ze zrównoważonym obciążeniem: adresy URL. Należy używać urządzenia, które obsługuje filtrowanie według adresu URL, a nie adresu IP.

> [!Note]  
> W 1809 infrastruktura kopii zapasowej usługa komunikuje się z serwerem plików zewnętrznych z sieci publicznych adresów VIP. Przed 1809 usługa przekazywane za pośrednictwem sieci infrastruktury publicznych. Jeśli w danym środowisku nie zezwala na dostęp do zasobów infrastruktury z sieci publicznych adresów VIP, zastosuj najnowsze [poprawkę 1809](azure-stack-update-1809.md#post-update-steps) dla usługi Azure Stack. Ta poprawka będzie powrót infrastruktury usługi kopii zapasowej do sieci publicznych infrastruktury. Jeśli należy zastosować poprawkę 1809 w 1811, infrastruktury usługi kopii zapasowej są pozostaje w sieci publicznych infrastruktury. Jeśli nie zostanie zastosowana poprawka, aktualizacja przeniesienie usługi sieci publicznych infrastruktury.

## <a name="next-steps"></a>Kolejne kroki

[Wymagania dotyczące usługi Azure Stack infrastruktury kluczy publicznych](azure-stack-pki-certs.md)
