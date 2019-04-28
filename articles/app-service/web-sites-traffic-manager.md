---
title: Sterowanie ruchem za pomocą usługi Traffic Manager — usługi Azure App Service
description: Ten artykuł zawiera podsumowanie informacji o usłudze Azure Traffic Manager, w odniesieniu do usługi Azure App Service.
services: app-service\web
documentationcenter: ''
author: cephalin
writer: cephalin
manager: erikre
editor: mollybos
ms.assetid: dabda633-e72f-4dd4-bf1c-6e945da456fd
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/25/2016
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 79fe3bce558a8315f5fbf7dbc82a4979e8e24238
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60831983"
---
# <a name="controlling-azure-app-service-traffic-with-azure-traffic-manager"></a>Kontrolowanie ruchu w usłudze Azure App Service z usługą Azure Traffic Manager
> [!NOTE]
> Ten artykuł zawiera podsumowanie informacji Microsoft Azure Traffic Manager, w odniesieniu do usługi Azure App Service. Więcej informacji na temat usługi Azure Traffic Manager sam można znaleźć, odwiedzając linki na końcu tego artykułu.
> 
> 

## <a name="introduction"></a>Wprowadzenie
Usługa Azure Traffic Manager służy do kontrolowania, jak żądania od klientów sieci web są dystrybuowane do aplikacji w usłudze Azure App Service. Gdy punkty końcowe usługi App Service są dodawane do profilu usługi Azure Traffic Manager, usługa Azure Traffic Manager śledzi stan aplikacji usługi App Service (uruchomiona, zatrzymana lub usunięta), dzięki czemu można zdecydować, który z tych punktów końcowych powinien odbierać ruch.

## <a name="routing-methods"></a>Metody routingu
Usługa Azure Traffic Manager korzysta z czterech różnych metod routingu. Te metody są opisane na poniższej liście odnoszą się do usługi Azure App Service.

* **[Priorytet](../traffic-manager/traffic-manager-routing-methods.md#priority):** korzystanie z aplikacji głównej dla całego ruchu, a wykonywanie kopii zapasowych w przypadku, gdy podstawowej maszyny wirtualnej lub aplikacji kopii zapasowej są niedostępne.
* **[Ważona średnia](../traffic-manager/traffic-manager-routing-methods.md#weighted):** rozdziela ruch między zbiór aplikacji, równomiernie lub według wagi, które należy zdefiniować.
* **[Wydajność](../traffic-manager/traffic-manager-routing-methods.md#performance):** po aplikacje znajdują się w różnych lokalizacjach geograficznych, używać "najbliższy" aplikacji pod kątem najniższych opóźnieniem sieci.
* **[Geograficzne](../traffic-manager/traffic-manager-routing-methods.md#geographic):** bezpośrednie użytkowników do określonych aplikacji oparte na lokalizacji geograficznej, do której pochodzi swoje zapytanie DNS. 

Aby uzyskać więcej informacji, zobacz [metody routingu w usłudze Traffic Manager](../traffic-manager/traffic-manager-routing-methods.md).

## <a name="app-service-and-traffic-manager-profiles"></a>Usługa App Service i profile usługi Traffic Manager
Aby skonfigurować kontrolę nad ruchem aplikacji usługi App Service, Utwórz profil w usłudze Azure Traffic Manager, używa jednego z czterech obciążenia opisanych powyżej metod równoważenia, a następnie dodaj punkty końcowe (w tym przypadku usługi App Service) dla których chcesz kontrolować ruch profil. Stan aplikacji (uruchomiona, zatrzymana lub usunięta) regularnie jest przekazywany do profilu, tak aby usługi Azure Traffic Manager może skierować ruch odpowiednio.

Podczas przy użyciu usługi Azure Traffic Manager za pomocą platformy Azure, należy uwzględnić następujące kwestie:

* Wdrożeń aplikacji tylko w tym samym regionie usługa App Service udostępnia już trybu failover i działanie okrężne funkcjonalności, bez względu na tryb aplikacji.
* W przypadku wdrożeń w tym samym regionie, korzystających z usługi App Service w połączeniu z innej usługi w chmurze platformy Azure możesz połączyć oba typy punktów końcowych, która umożliwia realizację scenariuszy hybrydowych.
* Można określić tylko jeden punkt końcowy usługi aplikacji, na region w profilu. Po wybraniu aplikacji jako punkt końcowy do jednego regionu, pozostałe aplikacje w danym regionie staną się niedostępne do wyboru dla tego profilu.
* Punkty końcowe usługi App Service, które są określone w profilu usługi Azure Traffic Manager jest wyświetlany w obszarze **nazw domen** sekcji na stronie Konfiguracja aplikacji w profilu, ale nie ma można konfigurować.
* Po dodaniu aplikacji do profilu, **adres URL witryny** na pulpicie nawigacyjnym portalu strony aplikacji Wyświetla adres URL domeny niestandardowej w aplikacji, jeśli skonfigurowano jedną. W przeciwnym razie wyświetla adres URL profilu usługi Traffic Manager (na przykład `contoso.trafficmanager.net`). Zarówno bezpośrednich nazwę domeny aplikacji i adres URL usługi Traffic Manager są widoczne na stronie konfigurowania aplikacji w ramach **nazw domen** sekcji.
* Niestandardowa nazwa domeny działa zgodnie z oczekiwaniami, ale oprócz dodania ich do aplikacji, należy również skonfigurować na mapie DNS, aby wskazywały na adres URL usługi Traffic Manager. Aby uzyskać informacje na temat sposobu konfigurowania domeny niestandardowej na potrzeby aplikacji usługi App Service, zobacz [mapowanie istniejącej niestandardowej nazwy DNS w usłudze Azure App Service](app-service-web-tutorial-custom-domain.md).
* Można dodawać tylko te aplikacje, które są w trybie standardowa lub premium do profilu usługi Azure Traffic Manager.

## <a name="next-steps"></a>Następne kroki
Aby uzyskać koncepcyjne i techniczne omówienie usługi Azure Traffic Manager, zobacz [Traffic Manager — omówienie](../traffic-manager/traffic-manager-overview.md).

Aby uzyskać więcej informacji o korzystaniu z usługi Traffic Manager z usługą App Service, zobacz wpisy w blogu [przy użyciu usługi Azure Traffic Manager za pomocą usługi Azure Web Sites](https://blogs.msdn.com/b/waws/archive/2014/03/18/using-windows-azure-traffic-manager-with-waws.aspx) i [teraz integrację z usługi Azure Traffic Manager za pomocą usługi Azure Web Sites](https://azure.microsoft.com/blog/2014/03/27/azure-traffic-manager-can-now-integrate-with-azure-web-sites/).

