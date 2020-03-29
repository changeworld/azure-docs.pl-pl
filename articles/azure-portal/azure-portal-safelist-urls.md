---
title: Safelist adresy URL portalu platformy Azure na zaporze lub serwerze proxy
description: Dodaj te adresy URL do pomijania serwera proxy, aby komunikować się z witryną Azure portal i jej usługami
services: azure-portal
keywords: ''
author: mgblythe
ms.author: mblythe
ms.date: 01/29/2020
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: c5bba7296a05cfbb72698a991ece1ef298689bd1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76900657"
---
# <a name="safelist-the-azure-portal-urls-on-your-firewall-or-proxy-server"></a>Safelist adresy URL portalu platformy Azure na zaporze lub serwerze proxy

Lokalne urządzenia zabezpieczające można skonfigurować w celu ominięcia ograniczeń zabezpieczeń adresów URL portalu Azure. Ta konfiguracja może zwiększyć wydajność i łączność między siecią lokalną lub szerokosądową a chmurą platformy Azure.

Administratorzy sieci często wdrażają serwery proxy, zapory lub inne urządzenia. Urządzenia te pomagają zabezpieczyć i dać kontrolę nad tym, jak użytkownicy uzyskują dostęp do Internetu. Reguły mające na celu ochronę użytkowników mogą czasami blokować lub spowalniać legalny ruch internetowy związany z działalnością biznesową. Ten ruch obejmuje komunikację między Tobą a platformą Azure. Aby zoptymalizować łączność między siecią a witryną Azure portal i jej usługami, zalecamy dodanie adresów URL witryny Azure portal do listy bezpiecznych.

## <a name="azure-portal-urls-for-proxy-bypass"></a>Adresy URL portalu platformy Azure dla pomijania serwera proxy

Punkty końcowe adresu URL do listy bezpiecznych dla witryny Azure portal są specyficzne dla chmury platformy Azure, w której jest wdrażana organizacja. Aby zezwolić ruchowi sieciowe do tych punktów końcowych na ominięcie ograniczeń, wybierz chmurę. Następnie dodaj listę adresów URL do serwera proxy lub zapory.

#### <a name="public-cloud"></a>[Chmura publiczna](#tab/public-cloud)

```
*.aadcdn.microsoftonline-p.com
*.aka.ms
*.applicationinsights.io
*.azure.com
*.azure.net
*.azureafd.net
*.azure-api.net
*.azuredatalakestore.net
*.azureedge.net
*.loganalytics.io
*.microsoft.com
*.microsoftonline.com
*.microsoftonline-p.com
*.msauth.net
*.msftauth.net
*.trafficmanager.net
*.visualstudio.com
*.windows.net
*.windows-int.net
```

#### <a name="us-government-cloud"></a>[Chmura rządu Stanów Zjednoczonych](#tab/us-government-cloud)

```
*.azure.us
*.loganalytics.us
*.microsoft.us
*.microsoftonline.us
*.msauth.net
*.usgovcloudapi.net
*.usgovtrafficmanager.net
*.windowsazure.us
```

#### <a name="china-government-cloud"></a>[Chińska chmura rządów](#tab/china-government-cloud)

```
*.azure.cn
*.microsoft.cn
*.microsoftonline.cn
*.chinacloudapi.cn
*.trafficmanager.cn
*.chinacloudsites.cn
*.windowsazure.cn
```
---

> [!NOTE]
> Ruch do tych punktów końcowych używa standardowych portów TCP dla HTTP (80) i HTTPS (443).
>
>
## <a name="next-steps"></a>Następne kroki

Potrzebujesz safelist adresy IP? Pobierz listę zakresów adresów IP centrum danych platformy Microsoft Azure dla swojej chmury:

* [Cały świat](https://www.microsoft.com/download/details.aspx?id=56519)
* [Rząd Stanów Zjednoczonych](https://www.microsoft.com/download/details.aspx?id=57063)
* [Niemcy](https://www.microsoft.com/download/details.aspx?id=57064)
* [Chiny](https://www.microsoft.com/download/details.aspx?id=57062)

Inne usługi firmy Microsoft używają dodatkowych adresów URL i adresów IP do łączności. Aby zoptymalizować łączność sieciową dla usług Microsoft 365, zobacz [Konfigurowanie sieci dla usługi Office 365](/office365/enterprise/set-up-network-for-office-365).
