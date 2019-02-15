---
title: Azure Stack — Integracja z centrum danych — publikowanie punktów końcowych | Dokumentacja firmy Microsoft
description: Dowiedz się, jak publikowanie punktów końcowych usługi Azure Stack w centrum danych
services: azure-stack
author: jeffgilb
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 02/06/2019
ms.author: jeffgilb
ms.reviewer: wamota
ms.lastreviewed: 02/06/2019
ms.openlocfilehash: c3b27291fc413310393cd0270ec750de14a4985b
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2019
ms.locfileid: "56270066"
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

> [!Note]
> Począwszy od aktualizacji 1811 porty w zakresie 12495 30015 są już nie muszą być otwarte ze względu na dodanie [Host rozszerzenia](azure-stack-extension-host-prepare.md).

|Punkt końcowy (VIP)|Rekord hosta DNS|Protokół|Porty|
|---------|---------|---------|---------|
|AD FS|Adfs.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|Portal (administrator)|Adminportal.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|Adminhosting | *.adminhosting.\<region>.\<fqdn> | HTTPS | 443 |
|Usługa Azure Resource Manager (administrator)|Adminmanagement.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|Portal (użytkownik)|Portal.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|Usługa Azure Resource Manager (użytkownik)|Management.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|Graph|Graph.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|Lista odwołania certyfikatów|Crl.*&lt;region>.&lt;fqdn>*|HTTP|80|
|DNS|&#42;.*&lt;region>.&lt;fqdn>*|TCP I UDP|53|
|Hosting | *.hosting.\<region>.\<fqdn> | HTTPS | 443 |
|Usługa Key Vault (użytkownik)|&#42;.vault.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|Usługa Key Vault (administrator)|&#42;.adminvault.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|Kolejka magazynu|&#42;.queue.*&lt;region>.&lt;fqdn>*|HTTP<br>HTTPS|80<br>443|
|Tabela magazynu|&#42;.table.*&lt;region>.&lt;fqdn>*|HTTP<br>HTTPS|80<br>443|
|Storage Blob|&#42;.blob.*&lt;region>.&lt;fqdn>*|HTTP<br>HTTPS|80<br>443|
|Dostawca zasobów programu SQL|sqladapter.dbadapter.*&lt;region>.&lt;fqdn>*|HTTPS|44300-44304|
|Dostawca zasobów programu MySQL|mysqladapter.dbadapter.*&lt;region>.&lt;fqdn>*|HTTPS|44300-44304|
|App Service|&#42;.appservice.*&lt;region>.&lt;fqdn>*|TCP|80 (HTTP)<br>443 (HTTPS)<br>8172 (MSDeploy)|
|  |&#42;.scm.appservice.*&lt;region>.&lt;fqdn>*|TCP|443 (HTTPS)|
|  |api.appservice.*&lt;region>.&lt;fqdn>*|TCP|443 (HTTPS)<br>44300 (usługa azure Resource Manager)|
|  |ftp.appservice.*&lt;region>.&lt;fqdn>*|TCP, UDP|21, 1021, 10001-10100 (FTP)<br>990 (FTPS)|
|Bramy sieci VPN|     |     |[Zobacz bramy sieci VPN — często zadawane pytania](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-vpn-faq#can-i-traverse-proxies-and-firewalls-using-point-to-site-capability).|
|     |     |     |     |

## <a name="ports-and-urls-outbound"></a>Porty i adresy URL (ruch wychodzący)

Usługa Azure Stack obsługuje tylko serwery z przezroczystym serwerem proxy. W przypadku wdrożenia w przypadku, gdy pasm przezroczystym serwerem proxy, aby serwer proxy tradycyjnych musisz zezwolić na następujących portów i adresów URL dla komunikacji wychodzącej:

> [!Note]  
> Usługa Azure Stack nie obsługuje przy użyciu usługi ExpressRoute w celu uzyskania dostępu do usług platformy Azure, przedstawione w poniższej tabeli.

|Przeznaczenie|Docelowy adres URL|Protokół|Porty|Sieć źródłowa|
|---------|---------|---------|---------|---------|
|Tożsamość|login.windows.net<br>login.microsoftonline.com<br>graph.windows.net<br>https://secure.aadcdn.microsoftonline-p.com<br>office.com|HTTP<br>HTTPS|80<br>443|Publiczne wirtualne adresy IP — wartość/27<br>Infrastruktura publicznych sieci|
|Syndykacja witryny Marketplace|https://management.azure.com<br>https://&#42;.blob.core.windows.net<br>https://*.azureedge.net<br>https://&#42;.microsoftazurestack.com|HTTPS|443|Publiczne wirtualne adresy IP — wartość/27|
|Akt & poprawki|https://&#42;.azureedge.net|HTTPS|443|Publiczne wirtualne adresy IP — wartość/27|
|Rejestracja|https://management.azure.com|HTTPS|443|Publiczne wirtualne adresy IP — wartość/27|
|Sposób użycia|https://&#42;.microsoftazurestack.com<br>https://*.trafficmanager.net |HTTPS|443|Publiczne wirtualne adresy IP — wartość/27|
|Windows Defender|.wdcp.microsoft.com<br>.wdcpalt.microsoft.com<br>*. updates.microsoft.com<br>*. witrynie download.microsoft.com<br>https://msdl.microsoft.com/download/symbols<br>`https://www.microsoft.com/pkiops/crl`<br>`https://www.microsoft.com/pkiops/certs`<br>`https://crl.microsoft.com/pki/crl/products`<br>`https://www.microsoft.com/pki/certs`<br>https://secure.aadcdn.microsoftonline-p.com<br>|HTTPS|80<br>443|Publiczne wirtualne adresy IP — wartość/27<br>Infrastruktura publicznych sieci|
|NTP|(Dostarczone dla wdrożenia serwera IP NTP)|UDP|123|Publiczne wirtualne adresy IP — wartość/27|
|DNS|(Dostarczone dla wdrożenia serwera IP DNS)|TCP<br>UDP|53|Publiczne wirtualne adresy IP — wartość/27|
|LISTY CRL|Adres URL (w ramach punktów dystrybucji listy CRL na Twój certyfikat)|HTTP|80|Publiczne wirtualne adresy IP — wartość/27|
|LDAP|Podany integracji narzędzia Graph lasu usługi Active Directory|TCP<br>UDP|389|Publiczne wirtualne adresy IP — wartość/27|
|LDAP SSL|Podany integracji narzędzia Graph lasu usługi Active Directory|TCP|636|Publiczne wirtualne adresy IP — wartość/27|
|LDAP GC|Podany integracji narzędzia Graph lasu usługi Active Directory|TCP|3268|Publiczne wirtualne adresy IP — wartość/27|
|LDAP GC SSL|Podany integracji narzędzia Graph lasu usługi Active Directory|TCP|3269|Publiczne wirtualne adresy IP — wartość/27|
|AD FS|Usługi AD FS metadanych podany punkt końcowy elementu integracji usług AD FS|TCP|443|Publiczne wirtualne adresy IP — wartość/27|
|     |     |     |     |     |

> [!Note]  
> Wychodzące adresy URL są równoważone za pomocą usługi Azure traffic manager, aby zapewnić najlepszą łączność możliwe na podstawie lokalizacji geograficznej. Adresy URL równoważenia obciążenia, zaktualizować i zmienić punktów końcowych zaplecza bez wywierania wpływu na klientów firmy Microsoft. Firma Microsoft udostępnia listę adresów IP dla ze zrównoważonym obciążeniem: adresy URL. Należy używać urządzenia, które obsługuje filtrowanie według adresu URL, a nie adresu IP.

## <a name="next-steps"></a>Kolejne kroki

[Wymagania dotyczące usługi Azure Stack infrastruktury kluczy publicznych](azure-stack-pki-certs.md)
