---
title: Kontroluj ruch za pomocą Menedżera ruchu
description: Znajdź najlepsze rozwiązania dotyczące konfigurowania usługi Azure Traffic Manager podczas integrowania go z usługą Azure App Service.
ms.assetid: dabda633-e72f-4dd4-bf1c-6e945da456fd
ms.topic: article
ms.date: 02/25/2016
ms.custom: seodec18
ms.openlocfilehash: 040f84288c66f4506919e775b9ea41324b617cfa
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437893"
---
# <a name="controlling-azure-app-service-traffic-with-azure-traffic-manager"></a>Kontrolowanie ruchu usługi Azure App Service za pomocą usługi Azure Traffic Manager
> [!NOTE]
> Ten artykuł zawiera informacje podsumowujące dla usługi Microsoft Azure Traffic Manager w odniesieniu do usługi Azure App Service. Więcej informacji na temat usługi Azure Traffic Manager można znaleźć, odwiedzając łącza na końcu tego artykułu.
> 
> 

## <a name="introduction"></a>Wprowadzenie
Usługi Azure Traffic Manager możesz użyć do kontrolowania dystrybucji żądań klientów internetowych kierowanych do aplikacji w usłudze Azure App Service. Gdy punkty końcowe usługi App Service są dodawane do profilu usługi Azure Traffic Manager, usługa Azure Traffic Manager śledzi stan aplikacji usługi App Service (uruchomiona, zatrzymana lub usunięta), dzięki czemu można zdecydować, który z tych punktów końcowych powinien odbierać ruch.

## <a name="routing-methods"></a>Metody routingu
Usługa Azure Traffic Manager używa czterech różnych metod routingu. Te metody są opisane na poniższej liście, ponieważ odnoszą się do usługi Azure App Service.

* ** [Priorytet:](../traffic-manager/traffic-manager-routing-methods.md#priority-traffic-routing-method)** użyj aplikacji podstawowej dla całego ruchu i udostępnij kopie zapasowe w przypadku, gdy aplikacje podstawowe lub zapasowe są niedostępne.
* ** [Ważona](../traffic-manager/traffic-manager-routing-methods.md#weighted):** rozdzielaj ruch na zestaw aplikacji, równomiernie lub według zdefiniowanych wag.
* ** [Wydajność:](../traffic-manager/traffic-manager-routing-methods.md#performance)** jeśli masz aplikacje w różnych lokalizacjach geograficznych, użyj "najbliższej" aplikacji pod względem najniższego opóźnienia sieci.
* ** [Geograficzne:](../traffic-manager/traffic-manager-routing-methods.md#geographic)** kieruj użytkowników do określonych aplikacji na podstawie lokalizacji geograficznej, z której pochodzi ich zapytanie DNS. 

Aby uzyskać więcej informacji, zobacz [Metody routingu usługi Traffic Manager](../traffic-manager/traffic-manager-routing-methods.md).

## <a name="app-service-and-traffic-manager-profiles"></a>Profile usługi app service i usługi Traffic Manager
Aby skonfigurować kontrolę ruchu aplikacji usługi App Service, należy utworzyć profil w usłudze Azure Traffic Manager, który używa jednej z czterech metod równoważenia obciążenia opisane wcześniej, a następnie dodać punkty końcowe (w tym przypadku Usługi app service), dla których chcesz kontrolować ruch do profilu. Stan aplikacji (uruchomiony, zatrzymany lub usunięty) jest regularnie przekazywany do profilu, dzięki czemu usługa Azure Traffic Manager może odpowiednio kierować ruchem.

Korzystając z usługi Azure Traffic Manager na platformie Azure, należy pamiętać o następujących kwestiach:

* W przypadku wdrożeń tylko aplikacji w tym samym regionie usługa App Service udostępnia już funkcje trybu failover i round-robin bez względu na tryb aplikacji.
* W przypadku wdrożeń w tym samym regionie, które korzystają z usługi App Service w połączeniu z inną usługą w chmurze platformy Azure, można połączyć oba typy punktów końcowych, aby włączyć scenariusze hybrydowe.
* W profilu można określić tylko jeden punkt końcowy usługi app service na region. Po wybraniu aplikacji jako punktu końcowego dla jednego regionu, pozostałe aplikacje w tym regionie stają się niedostępne do wyboru dla tego profilu.
* Punkty końcowe usługi App Service określone w profilu usługi Azure Traffic Manager są wyświetlane w sekcji **Domain Names** na stronie Konfigurowanie aplikacji w profilu, ale nie można go tam skonfigurować.
* Po dodaniu aplikacji do profilu **adres URL witryny** na pulpicie nawigacyjnym strony portalu aplikacji wyświetla niestandardowy adres URL domeny aplikacji, jeśli go skonfigurowałeś. W przeciwnym razie zostanie wyświetlony adres URL `contoso.trafficmanager.net`profilu usługi Traffic Manager (na przykład ). Zarówno bezpośrednia nazwa domeny aplikacji, jak i adres URL usługi Traffic Manager są widoczne na stronie Konfigurowanie aplikacji w sekcji **Domain Names (Nazwy domen).**
* Niestandardowe nazwy domen działają zgodnie z oczekiwaniami, ale oprócz dodawania ich do aplikacji należy również skonfigurować mapę DNS tak, aby wskazywała adres URL Usługi Traffic Manager. Aby uzyskać informacje dotyczące konfigurowania domeny niestandardowej dla aplikacji Usługi App Service, zobacz [Konfigurowanie niestandardowej nazwy domeny w usłudze Azure App Service z integracją usługi Traffic Manager](configure-domain-traffic-manager.md).
* Do profilu usługi Azure Traffic Manager można dodawać tylko aplikacje w trybie standardowym lub w trybie premium.
* Dodanie aplikacji do profilu usługi Traffic Manager powoduje ponowne uruchomienie aplikacji.

## <a name="next-steps"></a>Następne kroki
Aby uzyskać koncepcyjne i techniczne omówienie usługi Azure Traffic Manager, zobacz [Omówienie usługi Traffic Manager](../traffic-manager/traffic-manager-overview.md).


