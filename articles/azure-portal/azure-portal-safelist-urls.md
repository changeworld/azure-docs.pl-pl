---
title: Safelist adresy URL Azure Portal na zaporze lub serwerze proxy
description: Dodaj te adresy URL do obejścia serwera proxy, aby komunikować się z Azure Portalą i jej usługami
services: azure-portal
keywords: ''
author: mgblythe
ms.author: mblythe
ms.date: 01/29/2020
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: c5bba7296a05cfbb72698a991ece1ef298689bd1
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/31/2020
ms.locfileid: "76900657"
---
# <a name="safelist-the-azure-portal-urls-on-your-firewall-or-proxy-server"></a>Safelist adresy URL Azure Portal na zaporze lub serwerze proxy

Lokalne urządzenia zabezpieczeń można skonfigurować w celu obejścia ograniczeń zabezpieczeń dla adresów URL Azure Portal. Ta konfiguracja może zwiększyć wydajność i łączność między siecią lokalną lub rozległą a chmurą platformy Azure.

Administratorzy sieci często wdrażają serwery proxy, zapory lub inne urządzenia. Te urządzenia pomagają zabezpieczyć i zapewnić kontrolę nad tym, jak użytkownicy uzyskują dostęp do Internetu. Reguły służące do ochrony użytkowników mogą czasami blokować lub spowalniać słuszny ruch internetowy związany z firmą. Ten ruch obejmuje komunikację między ty i platformą Azure. Aby zoptymalizować łączność między siecią a Azure Portal i jej usługami, zalecamy dodanie Azure Portal adresów URL do safelist.

## <a name="azure-portal-urls-for-proxy-bypass"></a>Adresy URL Azure Portal dla obejścia serwera proxy

Punkty końcowe URL Safelist dla Azure Portal są specyficzne dla chmury platformy Azure, w której wdrożono organizację. Aby zezwolić na ruch sieciowy do tych punktów końcowych w celu obejścia ograniczeń, wybierz chmurę. Następnie Dodaj listę adresów URL do serwera proxy lub zapory.

#### <a name="public-cloudtabpublic-cloud"></a>[Chmura publiczna](#tab/public-cloud)

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

#### <a name="us-government-cloudtabus-government-cloud"></a>[Chmura dla instytucji rządowych USA](#tab/us-government-cloud)

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

#### <a name="china-government-cloudtabchina-government-cloud"></a>[Chmura dla instytucji rządowych](#tab/china-government-cloud)

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
> Ruch do tych punktów końcowych używa standardowych portów TCP dla protokołu HTTP (80) i HTTPS (443).
>
>
## <a name="next-steps"></a>Następne kroki

Chcesz Safelist adresy IP? Pobierz listę zakresów adresów IP centrum danych Microsoft Azure w chmurze:

* [Na całym świecie](https://www.microsoft.com/download/details.aspx?id=56519)
* [Rząd Stanów Zjednoczonych](https://www.microsoft.com/download/details.aspx?id=57063)
* [Niemcy](https://www.microsoft.com/download/details.aspx?id=57064)
* [Chiny](https://www.microsoft.com/download/details.aspx?id=57062)

Inne usługi firmy Microsoft używają dodatkowych adresów URL i adresów IP na potrzeby łączności. Aby zoptymalizować łączność sieciową dla usług Microsoft 365 Services, zobacz [Konfigurowanie sieci dla pakietu Office 365](/office365/enterprise/set-up-network-for-office-365).
