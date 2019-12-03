---
title: Konfigurowanie nazw DNS przy użyciu Traffic Manager
description: Dowiedz się, jak skonfigurować domenę niestandardową dla aplikacji Azure App Service, która integruje się z Traffic Manager na potrzeby równoważenia obciążenia.
ms.assetid: 0f96c0e7-0901-489b-a95a-e3b66ca0a1c2
ms.topic: article
ms.date: 08/17/2016
ms.custom: seodec18
ms.openlocfilehash: 9139b83f1f2920da47b4a0d440f622626d41c938
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2019
ms.locfileid: "74689274"
---
# <a name="configuring-a-custom-domain-name-for-a-web-app-in-azure-app-service-using-traffic-manager"></a>Konfigurowanie niestandardowej nazwy domeny dla aplikacji sieci Web w Azure App Service przy użyciu Traffic Manager
[!INCLUDE [web-selector](../../includes/websites-custom-domain-selector.md)]

[!INCLUDE [intro](../../includes/custom-dns-web-site-intro-traffic-manager.md)]

Ten artykuł zawiera ogólne instrukcje dotyczące używania niestandardowej nazwy domeny z aplikacją [App Service](overview.md) zintegrowaną z [Traffic Manager](../traffic-manager/traffic-manager-overview.md) na potrzeby równoważenia obciążenia.

[!INCLUDE [tmwebsitefooter](../../includes/custom-dns-web-site-traffic-manager-notes.md)]

[!INCLUDE [introfooter](../../includes/custom-dns-web-site-intro-notes.md)]

<a name="understanding-records"></a>

## <a name="understanding-dns-records"></a>Informacje o rekordach DNS
[!INCLUDE [understandingdns](../../includes/custom-dns-web-site-understanding-dns-traffic-manager.md)]

<a name="bkmk_configsharedmode"></a>

## <a name="configure-your-web-apps-for-standard-mode"></a>Skonfiguruj aplikacje sieci Web do trybu standardowego
[!INCLUDE [modes](../../includes/custom-dns-web-site-modes-traffic-manager.md)]

<a name="bkmk_configurecname"></a>

## <a name="add-a-dns-record-for-your-custom-domain"></a>Dodawanie rekordu DNS dla domeny niestandardowej
> [!NOTE]
> Jeśli masz zakupione domeny za pomocą Azure App Service Web Apps następnie Pomiń następujące kroki i zapoznaj się z ostatnim krokiem [kupowania domeny na potrzeby Web Apps](manage-custom-dns-buy-domain.md) artykule.
> 
> 

Aby skojarzyć domenę niestandardową z aplikacją sieci Web w Azure App Service, musisz dodać nowy wpis w tabeli DNS dla domeny niestandardowej. Można to zrobić za pomocą narzędzi do zarządzania od dostawcy domeny.

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

Poszczególne dostawcy domen różnią się w zależności *od* nazwy domeny niestandardowej (na przykład **contoso.com**) *do* nazwy domeny Traffic Manager (**contoso.trafficmanager.NET**) zintegrowanej z aplikacją sieci Web.

> [!NOTE]
> Jeśli rekord jest już używany i musisz zapobiegawczo powiązać z nim aplikacje, możesz utworzyć dodatkowy rekord CNAME. Na przykład, aby zapobiegawczo powiązania **www\.contoso.com** z aplikacją internetową, należy utworzyć rekord CNAME z **awverify. www** w **contoso.trafficmanager.NET**. Następnie możesz dodać "www\.contoso.com" do aplikacji sieci Web bez zmiany rekordu CNAME "www". Aby uzyskać więcej informacji, zobacz [tworzenie rekordów DNS dla aplikacji sieci Web w domenie niestandardowej][CREATEDNS].

Po zakończeniu dodawania lub modyfikowania rekordów DNS u dostawcy domeny Zapisz zmiany.

<a name="enabledomain"></a>

## <a name="enable-traffic-manager"></a>Włącz Traffic Manager
[!INCLUDE [modes](../../includes/custom-dns-web-site-enable-on-traffic-manager.md)]

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji, odwiedź stronę [Centrum deweloperów środowiska Node.js](https://azure.microsoft.com/develop/nodejs/).

<!-- URL List -->

[CREATEDNS]: ../dns/dns-web-sites-custom-domain.md
