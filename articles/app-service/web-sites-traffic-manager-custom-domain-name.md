---
title: Konfigurowanie niestandardowej nazwy domeny dla aplikacji sieci web w usłudze Azure App Service, który używa Menedżera ruchu do równoważenia obciążenia.
description: Użyj nazwy domeny niestandardowej dla aplikacji sieci web w usłudze Azure App Service, która obejmuje usługi Traffic Manager w programie Równoważenie obciążenia.
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
ms.openlocfilehash: c78fb7883559e46ebaa1d8dab59a15c55fb76fdf
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/09/2018
ms.locfileid: "27713855"
---
# <a name="configuring-a-custom-domain-name-for-a-web-app-in-azure-app-service-using-traffic-manager"></a>Konfigurowanie niestandardowej nazwy domeny dla aplikacji sieci web w usłudze Azure App Service przy użyciu Menedżera ruchu
[!INCLUDE [web-selector](../../includes/websites-custom-domain-selector.md)]

[!INCLUDE [intro](../../includes/custom-dns-web-site-intro-traffic-manager.md)]

W tym artykule przedstawiono ogólne instrukcje dotyczące korzystania z niestandardowej nazwy domeny z [usługi aplikacji](app-service-web-overview.md) aplikacji, który jest zintegrowany z [Traffic Manager](../traffic-manager/traffic-manager-overview.md) Równoważenie obciążenia sieciowego.

[!INCLUDE [tmwebsitefooter](../../includes/custom-dns-web-site-traffic-manager-notes.md)]

[!INCLUDE [introfooter](../../includes/custom-dns-web-site-intro-notes.md)]

<a name="understanding-records"></a>

## <a name="understanding-dns-records"></a>Opis rekordów DNS
[!INCLUDE [understandingdns](../../includes/custom-dns-web-site-understanding-dns-traffic-manager.md)]

<a name="bkmk_configsharedmode"></a>

## <a name="configure-your-web-apps-for-standard-mode"></a>Konfigurowanie aplikacji sieci web dla Tryb standardowy
[!INCLUDE [modes](../../includes/custom-dns-web-site-modes-traffic-manager.md)]

<a name="bkmk_configurecname"></a>

## <a name="add-a-dns-record-for-your-custom-domain"></a>Dodaj rekord DNS dla domeny niestandardowej
> [!NOTE]
> Jeśli zostały nabyte domeny przy użyciu aplikacji sieci Web usługi aplikacji Azure Pomiń następujące kroki i odwoływać się do ostatniego kroku [kupić domenę dla aplikacji sieci Web](custom-dns-web-site-buydomains-web-app.md) artykułu.
> 
> 

Aby skojarzyć domenę niestandardową z aplikacji sieci web w usłudze Azure App Service, należy dodać nowy wpis w tabeli DNS dla domeny niestandardowej. Można to zrobić za pomocą narzędzia do zarządzania przez dostawcę domeny.

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

Gdy różnią się szczegółowe informacje na temat każdego z dostawców domeny mapy *z* niestandardową nazwę domeny (takich jak **contoso.com**) *do* nazwę domeny usługi Traffic Manager ( **contoso.trafficmanager.NET**) może być zintegrowana z aplikacji sieci web.
   
> [!NOTE]
> Jeśli rekord jest już w użyciu, i chcesz preemptively powiązać go z aplikacji, można utworzyć dodatkowy rekord CNAME. Na przykład, aby powiązać preemptively **www.contoso.com** do aplikacji sieci web, należy utworzyć rekord CNAME z **awverify.www** do **contoso.trafficmanager.net**. Następnie można dodać "www.contoso.com" do aplikacji sieci Web bez konieczności zmieniania rekord CNAME "www". Aby uzyskać więcej informacji, zobacz [rekordy DNS Utwórz dla aplikacji sieci web w domenie niestandardowych][CREATEDNS].
> 
> 

Po zakończeniu dodawania lub modyfikowania rekordów DNS u dostawcy usługi domeny, należy zapisać zmiany.

<a name="enabledomain"></a>

## <a name="enable-traffic-manager"></a>Menedżer ruchu
[!INCLUDE [modes](../../includes/custom-dns-web-site-enable-on-traffic-manager.md)]

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji, odwiedź stronę [Centrum deweloperów środowiska Node.js](/develop/nodejs/).

[!INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- URL List -->

[CREATEDNS]: ../dns/dns-web-sites-custom-domain.md
