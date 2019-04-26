---
title: Zmiany cennika usługi Notification Hubs w przestrzeni nazw w warstwie | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zmienić warstwę cenową przestrzeni nazw usługi Azure Notification Hubs.
services: notification-hubs
author: jwargo
manager: patniko
editor: spelluru
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 01/28/2019
ms.author: jowargo
ms.openlocfilehash: 99ea21b3eb01a674a89c70a1b923f02e600cc3c5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60234457"
---
# <a name="change-pricing-tier-of-an-azure-notification-hubs-namespace"></a>Zmień warstwę cenową przestrzeni nazw usługi Azure notification hubs
Usługa Notification Hubs jest oferowana w trzech warstwach: **bezpłatne**, **podstawowe**, i **standardowa**. W tym artykule pokazano, jak zmienić warstwę cenową dla przestrzeni nazw usługi Azure Notification Hubs. 

## <a name="overview"></a>Omówienie
W usłudze Azure Notification Hubs **Centrum** jest najmniejszą zasobu na jednostkę. Go ogólnie mapuje do jednej aplikacji i może zawierać jeden certyfikat dla każdego systemu powiadomień platformy, firma Microsoft obsługuje dla aplikacji. Aplikacja może być hybrydowego lub natywny i aplikacji dla wielu platform.

A **przestrzeni nazw** to kolekcja usługi notification hubs. Każda przestrzeń nazw zwykle składa się z koncentratorów, które są powiązane i używane do określonego celu. Na przykład można mieć trzech różnych obszarach nazw do celów programowania, testowania i produkcji odpowiednio. 

Możesz skojarzyć warstwy cenowej na poziomie przestrzeni nazw. Notification Hubs obsługuje też trzech warstw: **bezpłatne**, **podstawowe**, i **standardowa**. Możesz korzystać z warstwy dla przestrzeni nazw, który odpowiada Twoim wymaganiom. Poniższe sekcje pokazują, jak zmienić warstwę cenową w przestrzeni nazw usługi Notification Hubs. 

## <a name="use-azure-portal"></a>Korzystanie z witryny Azure Portal 
Korzystając z witryny Azure portal, możesz zmienić warstwę cenową dla przestrzeni nazw na stronie przestrzeń nazw lub strona Centrum.  Po zmianie na stronie Centrum, możesz faktycznie go zmienić na poziomie przestrzeni nazw. Zmienia warstwę cenową dla przestrzeni nazw i wszystkich centrów w przestrzeni nazw. 

### <a name="change-tier-on-the-namespace-page"></a>Zmiana warstwy na stronie przestrzeń nazw
Poniższa procedura zawiera kroki, aby zmienić warstwę cenową dla przestrzeni nazw na stronie przestrzeni nazw. Po zmianie warstwy w przestrzeni nazw ma zastosowanie do wszystkich centrów w przestrzeni nazw.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz **wszystkich usług** w menu po lewej stronie. 
3. Wybierz **przestrzenie nazw Centrum powiadomień** w **Internet of Things** sekcji. Jeśli wybierzesz gwiazdkę (`*`) obok tekstu, pobiera ono dodane do paska nawigacji po lewej stronie w obszarze **ulubione**. Ułatwia on z uzyskiwaniem dostępu do strony przestrzenie nazw szybciej następnym razem i nowszych wersjach. Po dodaniu go do ulubionych, wybierz **przestrzenie nazw Centrum powiadomień**. 

    ![Wszystkie usługi -> przestrzenie nazw Centrum powiadomień](./media/change-pricing-tier/all-services-nhub.png)
1. Na **przestrzenie nazw Centrum powiadomień** wybierz przestrzeń nazw, dla którego chcesz zmienić warstwę cenową. 
2. Na **Notification Hub Namespace** strony dla przestrzeni nazw, można wyświetlić bieżąca warstwa cenowa dla przestrzeni nazw w **Essentials** sekcji. Na poniższej ilustracji widać, że warstwa cenowa obszaru nazw jest **bezpłatna**. 

    ![Bieżąca warstwa cenowa na stronie przestrzeń nazw](./media/change-pricing-tier/pricing-tier-before.png)
1. Na **Notification Hub Namespace** strony dla swojego obszaru nazw, wybierz opcję **warstwy cenowej** w obszarze **Zarządzaj** sekcji. 

    ![Wybierz warstwę cenową na stronie przestrzeń nazw](./media/change-pricing-tier/namespace-select-pricing-menu.png)
6. Zmień warstwę cenową, a następnie kliknij przycisk **wybierz** przycisku.    
7. Zostanie wyświetlony stan warstwy zmienić akcję w **alerty**. 
8. Przełącz się do **Przegląd** strony. Upewnij się, że nowa warstwa jest wyświetlana dla **warstwy cenowej** pole **Essentials** sekcji.     
1. Ten krok jest opcjonalny. Wybierz dowolne Centrum w przestrzeni nazw. Upewnij się, że te same ceny warstwy **Essentials** sekcji. Powinien zostać wyświetlony tej samej warstwie cenowej do wszystkich centrów w przestrzeni nazw. 

### <a name="change-tier-on-the-hub-page"></a>Zmiana warstwy na stronie
Poniższa procedura zawiera kroki, aby zmienić warstwę cenową dla przestrzeni nazw na stronie. Mimo że wykonaniem tych kroków, zaczynając od strony Centrum faktycznie Zmień warstwę cenową dla przestrzeni nazw i wszystkich centrów w przestrzeni nazw. 

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz **wszystkich usług** w menu po lewej stronie.
3. Wybierz **usługi Notification Hubs** w **Internet of Things** sekcji. 
4. Wybierz powiadomienie, **Centrum**. 
5. Wybierz **warstwy cenowej** w menu po lewej stronie. 
6. Zmień warstwę cenową, a następnie kliknij przycisk **wybierz** przycisku. Ta akcja zmian cen ustawienia warstwy dla przestrzeni nazw, który zawiera koncentratora. Tak Zobacz nowej warstwy cenowej na stronie przestrzeń nazw, a wszystkie strony Centrum. 

## <a name="use-rest-api"></a>Korzystanie z interfejsu API REST
Następujące interfejsy API REST dostawcy zasobów umożliwia pobieranie bieżąca warstwa cenowa i zaktualizować go. 

### <a name="get-current-pricing-tier-for-a-namespace"></a>Pobierz bieżąca warstwa cenowa dla przestrzeni nazw
Aby uzyskać **bieżącej przestrzeni nazw warstwy**, wysłać polecenie GET, jak pokazano w poniższym przykładzie: 

```REST
GET: https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/notificationhubplan
```

### <a name="update-pricing-tier-for-a-namespace"></a>Aktualizowanie warstwy cenowej dla przestrzeni nazw
Aby **zaktualizuj warstwę przestrzeni nazw**, wysłać polecenie PUT, jak pokazano w poniższym przykładzie: 

```REST
PUT: https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/notificationhubplan
Body: <NotificationHubPlan xmlns:i="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/netservices/2010/10/servicebus/connect"><SKU>Standard</SKU></NotificationHubPlan>
```



## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji na temat tych warstw i cenach, zobacz [cennik usługi Notification Hubs](https://azure.microsoft.com/pricing/details/notification-hubs/).
