---
title: Safelist adresy URL portalu platformy Azure na zaporze lub serwerze proxy
description: Dodaj te adresy URL do pomijania serwera proxy, aby komunikować się z witryną Azure portal i jej usługami
services: azure-portal
keywords: ''
author: mgblythe
ms.author: mblythe
ms.date: 04/10/2020
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 399c64c88e78079432fcf7c09dafd199da83358b
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81255053"
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
