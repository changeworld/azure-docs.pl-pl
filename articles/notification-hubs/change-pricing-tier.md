---
title: Zmień warstwę cenową Notification Hubs przestrzeni nazw | Microsoft Docs
description: Dowiedz się, jak zmienić warstwę cenową przestrzeni nazw platformy Azure Notification Hubs.
services: notification-hubs
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 01/28/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/28/2019
ms.openlocfilehash: 51d0880b7f56d523a01fbc993993b3caf7328134
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/17/2020
ms.locfileid: "76261079"
---
# <a name="change-pricing-tier-of-an-azure-notification-hubs-namespace"></a>Zmień warstwę cenową przestrzeni nazw centrów powiadomień platformy Azure
Notification Hubs jest oferowana w trzech warstwach: **bezpłatna**, **podstawowa**i **standardowa**. W tym artykule opisano sposób zmiany warstwy cenowej dla przestrzeni nazw platformy Azure Notification Hubs. 

## <a name="overview"></a>Przegląd
W usłudze Azure Notification Hubs **centrum** jest najmniejszym zasobem/jednostką. Zwykle jest ona mapowana na jedną aplikację i może przechowywać jeden certyfikat dla każdej system powiadomień platformy, która obsługuje aplikację. Aplikacja może być hybrydową lub natywną i międzyplatformową aplikacją.

**Przestrzeń nazw** jest kolekcją centrów powiadomień. Każda przestrzeń nazw składa się zazwyczaj z centrów, które są powiązane i używane do określonego celu. Na przykład mogą istnieć trzy różne przestrzenie nazw na potrzeby programowania, testowania i produkcji. 

Warstwę cenową można skojarzyć na poziomie przestrzeni nazw. Notification Hubs obsługuje trzy warstwy: **bezpłatna**, **podstawowa**i **standardowa**. Możesz użyć warstwy dla przestrzeni nazw, która odpowiada Twoim wymaganiom. W poniższych sekcjach pokazano, jak zmienić warstwę cenową Notification Hubs przestrzeni nazw. 

## <a name="use-azure-portal"></a>Korzystanie z witryny Azure Portal 
W przypadku korzystania z Azure Portal można zmienić warstwę cenową dla przestrzeni nazw na stronie przestrzeni nazw lub na stronie centrum.  Gdy zmienisz ją na stronie centrum, rzeczywiście zmienisz ją na poziomie przestrzeni nazw. Zmiana warstwy cenowej dla przestrzeni nazw i wszystkich centrów w przestrzeni nazw. 

### <a name="change-tier-on-the-namespace-page"></a>Zmień warstwę na stronie przestrzeni nazw
Poniższa procedura zawiera kroki umożliwiające zmianę warstwy cenowej dla przestrzeni nazw na stronie przestrzeni nazw. Zmiana warstwy dla przestrzeni nazw ma zastosowanie do wszystkich centrów w przestrzeni nazw.

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
2. W menu po lewej stronie wybierz pozycję **wszystkie usługi** . 
3. W sekcji **Internet rzeczy** wybierz pozycję **przestrzenie nazw centrum powiadomień** . Jeśli wybierzesz gwiazdkę (`*`) obok tekstu, zostanie ona dodana na lewym pasku nawigacyjnym w obszarze **Ulubione**. Ułatwia ona szybkie uzyskiwanie dostępu do strony przestrzeni nazw. Po dodaniu go do ulubionych wybierz pozycję **przestrzenie nazw centrum powiadomień**. 

    ![Wszystkie usługi — > przestrzenie nazw centrum powiadomień](./media/change-pricing-tier/all-services-nhub.png)
1. Na stronie **przestrzenie nazw centrum powiadomień** wybierz przestrzeń nazw, dla której chcesz zmienić warstwę cenową. 
2. Na stronie **obszar nazw centrum powiadomień** dla obszaru nazw można wyświetlić bieżącą warstwę cenową dla przestrzeni nazw w sekcji **podstawy** . Na poniższej ilustracji widać, że warstwa cenowa przestrzeni nazw jest **bezpłatna**. 

    ![Bieżąca warstwa cenowa na stronie przestrzeni nazw](./media/change-pricing-tier/pricing-tier-before.png)
1. Na stronie **obszar nazw centrum powiadomień** dla obszaru nazw wybierz pozycję **warstwa cenowa** w obszarze **Zarządzaj** . 

    ![Wybierz warstwę cenową na stronie przestrzeni nazw](./media/change-pricing-tier/namespace-select-pricing-menu.png)
6. Zmień warstwę cenową, a następnie kliknij przycisk **Wybierz** .    
7. Zobaczysz stan akcji zmiany warstwy w **alertach**. 
8. Przejdź do strony **Przegląd** . Upewnij się, że nowa warstwa jest wyświetlana w polu **warstwa cenowa** w sekcji **podstawowe** informacje.     
1. Ten krok jest opcjonalny. Wybierz dowolne centrum w przestrzeni nazw. Upewnij się, że w sekcji **podstawowe** informacje są wyświetlane te same warstwy cenowe. Powinna zostać wyświetlona ta sama warstwa cenowa dla wszystkich centrów w przestrzeni nazw. 

### <a name="change-tier-on-the-hub-page"></a>Zmień warstwę na stronie centrum
Poniższa procedura zawiera kroki umożliwiające zmianę warstwy cenowej dla przestrzeni nazw na stronie centrum. Mimo że te kroki są wykonywane począwszy od strony centrum, w rzeczywistości zmienisz warstwę cenową dla przestrzeni nazw i wszystkich centrów w przestrzeni nazw. 

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
2. W menu po lewej stronie wybierz pozycję **wszystkie usługi** .
3. Wybierz **Notification Hubs** w sekcji **Internet rzeczy** . 
4. Wybierz **centrum**powiadomień. 
5. W menu po lewej stronie wybierz pozycję **warstwa cenowa** . 
6. Zmień warstwę cenową, a następnie kliknij przycisk **Wybierz** . Ta akcja powoduje zmianę ustawienia warstwy cenowej dla przestrzeni nazw zawierającej centrum. Na stronie przestrzeń nazw zostanie wyświetlona nowa warstwa cenowa i wszystkie strony centrów. 

## <a name="use-rest-api"></a>Używanie interfejsu API REST
Aby uzyskać bieżącą warstwę cenową i zaktualizować ją, można użyć następujących interfejsów API REST dostawcy zasobów. 

### <a name="get-current-pricing-tier-for-a-namespace"></a>Pobierz bieżącą warstwę cenową dla przestrzeni nazw
Aby uzyskać **bieżącą warstwę przestrzeni nazw**, Wyślij polecenie Get, jak pokazano w następującym przykładzie: 

```REST
GET: https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/notificationhubplan
```

### <a name="update-pricing-tier-for-a-namespace"></a>Aktualizowanie warstwy cenowej dla przestrzeni nazw
Aby **zaktualizować warstwę przestrzeni nazw**, Wyślij polecenie Put, jak pokazano w następującym przykładzie: 

```REST
PUT: https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/notificationhubplan
Body: <NotificationHubPlan xmlns:i="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/netservices/2010/10/servicebus/connect"><SKU>Standard</SKU></NotificationHubPlan>
```



## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji o tych warstwach i cenach, zobacz [Notification Hubs Cennik](https://azure.microsoft.com/pricing/details/notification-hubs/).
