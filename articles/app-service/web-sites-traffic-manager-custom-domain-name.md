---
title: Konfigurowanie nazwy DNS dla aplikacji, które używają usługi Traffic Manager — usługi Azure App Service
description: Użyj niestandardowej nazwy domeny dla aplikacji sieci web w usłudze Azure App Service, która obejmuje usługi Traffic Manager na potrzeby równoważenia obciążenia.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.assetid: 0f96c0e7-0901-489b-a95a-e3b66ca0a1c2
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2016
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: b207d9e3123245e5cb7dddb625f7488886591ae5
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62128533"
---
# <a name="configuring-a-custom-domain-name-for-a-web-app-in-azure-app-service-using-traffic-manager"></a>Konfigurowanie niestandardowej nazwy domeny dla aplikacji sieci web w usłudze Azure App Service przy użyciu usługi Traffic Manager
[!INCLUDE [web-selector](../../includes/websites-custom-domain-selector.md)]

[!INCLUDE [intro](../../includes/custom-dns-web-site-intro-traffic-manager.md)]

Ten artykuł zawiera ogólne instrukcje dotyczące korzystania z niestandardowej nazwy domeny za pomocą [usługi App Service](overview.md) aplikację, która jest zintegrowana z usługą [usługi Traffic Manager](../traffic-manager/traffic-manager-overview.md) Równoważenie obciążenia sieciowego.

[!INCLUDE [tmwebsitefooter](../../includes/custom-dns-web-site-traffic-manager-notes.md)]

[!INCLUDE [introfooter](../../includes/custom-dns-web-site-intro-notes.md)]

<a name="understanding-records"></a>

## <a name="understanding-dns-records"></a>Informacje o rekordach DNS
[!INCLUDE [understandingdns](../../includes/custom-dns-web-site-understanding-dns-traffic-manager.md)]

<a name="bkmk_configsharedmode"></a>

## <a name="configure-your-web-apps-for-standard-mode"></a>Konfigurowanie aplikacji sieci web dla trybu standardowego
[!INCLUDE [modes](../../includes/custom-dns-web-site-modes-traffic-manager.md)]

<a name="bkmk_configurecname"></a>

## <a name="add-a-dns-record-for-your-custom-domain"></a>Dodaj rekord DNS dla domeny niestandardowej
> [!NOTE]
> Jeśli zostały nabyte domeny przy użyciu usługi Azure App Service Web Apps, a następnie pominąć, wykonując kroki i odwoływać się do ostatniego kroku [kupić domenę dla aplikacji sieci Web](manage-custom-dns-buy-domain.md) artykułu.
> 
> 

Aby skojarzyć domenę niestandardową z aplikacji sieci web w usłudze Azure App Service, należy dodać nowy wpis w tabeli DNS dla domeny niestandardowej. Możesz to zrobić za pomocą narzędzia do zarządzania od dostawcy domeny.

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

Gdy różnią się szczegółowe informacje na temat każdego dostawcy domeny, możesz zamapować *z* niestandardowej nazwy domeny (takie jak **contoso.com**) *do* nazwę domeny usługi Traffic Manager ( **contoso.trafficmanager.NET**) który jest zintegrowany z aplikacją sieci web.

> [!NOTE]
> Jeśli rekord jest już używany, i chcesz prewencyjnego powiązać rozwiązanie aplikacji, możesz utworzyć dodatkowe rekordu CNAME. Na przykład, aby powiązać prewencyjnego **www\.contoso.com** do aplikacji sieci web należy utworzyć rekord CNAME z **awverify.www** do **contoso.trafficmanager.net**. Następnie można dodać "www\.contoso.com" w aplikacji sieci Web bez konieczności zmieniania rekord CNAME "www". Aby uzyskać więcej informacji, zobacz [DNS utworzyć rekordy dla aplikacji sieci web w domenie niestandardowej][CREATEDNS].

Po zakończeniu dodawania lub modyfikowania rekordów DNS dostawcy domeny, Zapisz zmiany.

<a name="enabledomain"></a>

## <a name="enable-traffic-manager"></a>Włączanie usługi Traffic Manager
[!INCLUDE [modes](../../includes/custom-dns-web-site-enable-on-traffic-manager.md)]

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji, odwiedź stronę [Centrum deweloperów środowiska Node.js](https://azure.microsoft.com/develop/nodejs/).

<!-- URL List -->

[CREATEDNS]: ../dns/dns-web-sites-custom-domain.md
