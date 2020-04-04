---
title: Zmienianie warstwy cenowej obszaru nazw Centrów powiadomień | Dokumenty firmy Microsoft
description: Dowiedz się, jak zmienić warstwę cenową obszaru nazw usługi Azure Notification Hubs.
services: notification-hubs
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 04/02/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 01/28/2019
ms.openlocfilehash: 855a050afa14144f8963f24398c6b7b3939ef562
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656467"
---
# <a name="change-pricing-tier-of-an-azure-notification-hubs-namespace"></a>Zmienianie warstwy cenowej obszaru nazw centrów powiadomień platformy Azure

Centra powiadomień są oferowane w trzech warstwach: **bezpłatna,** **podstawowa**i **standardowa.** W tym artykule pokazano, jak zmienić warstwę cenową dla obszaru nazw usługi Azure Notification Hubs.

## <a name="overview"></a>Omówienie

W centrum powiadomień platformy Azure centrum jest najmniejszym zasobem/encją. Zazwyczaj mapuje do jednej aplikacji i może posiadać jeden certyfikat dla każdego systemu powiadomień platformy (PNS), który obsługujemy dla aplikacji. Aplikacja może być hybrydowa lub natywna i wieloplatformowa aplikacja.

**Obszar nazw** jest kolekcją centrów powiadomień. Każdy obszar nazw zwykle składa się z koncentratorów, które są powiązane i używane do określonego celu. Na przykład można mieć trzy różne obszary nazw do celów osiągnięć, testowania i produkcji, odpowiednio.

Obszar nazw można skojarzyć z **bezpłatnymi,** **podstawowymi**lub **standardowymi** warstwami cenowymi. Można użyć warstwy dla obszaru nazw, który odpowiada Twoim wymaganiom. W poniższych sekcjach pokazano, jak zmienić warstwę cenową obszaru nazw Centrów powiadomień.

## <a name="use-azure-portal"></a>Korzystanie z witryny Azure Portal

Korzystając z witryny Azure portal, można zmienić warstwę cenową dla obszaru nazw na stronie obszaru nazw lub na stronie centrum. Po zmianie na stronie centrum, faktycznie zmienić go na poziomie obszaru nazw. Zmienia warstwę cenową dla obszaru nazw i wszystkich centrów w obszarze nazw.

### <a name="change-tier-on-the-namespace-page"></a>Zmienianie warstwy na stronie obszaru nazw

Poniższa procedura pokazuje, jak zmienić warstwę cenową dla obszaru nazw na stronie obszaru nazw. Po zmianie warstwy dla obszaru nazw, stosuje się do wszystkich koncentratorów w tej przestrzeni nazw.

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
2. Wybierz **pozycję Wszystkie usługi** w menu po lewej stronie.
3. Wybierz **pozycję Obszary nazw Centrum powiadomień** w sekcji Internet **rzeczy.** Jeśli zaznaczysz gwiazdkę`*`( ) obok tekstu, zostanie on dodany do lewego paska nawigacyjnego w obszarze **ULUBIONE**. Ułatwia szybszy dostęp do strony obszarów nazw w przyszłości. Po dodaniu go do ulubionych wybierz pozycję **Obszary nazw Centrum powiadomień**.

    ![Wszystkie > obszarów nazw Centrum powiadomień >](./media/change-pricing-tier/all-services-nhub.png)

4. Na stronie **Obszary nazw Centrum powiadomień** wybierz obszar nazw, dla którego chcesz zmienić warstwę cenową.
5. Na stronie **Obszar nazw Centrum powiadomień** dla obszaru nazw możesz zobaczyć bieżącą warstwę cenową obszaru nazw w sekcji **Podstawowe informacje.** Na poniższej ilustracji widać, że warstwa cenowa obszaru nazw jest **bezpłatna**.

    ![Bieżąca warstwa cenowa na stronie obszaru nazw](./media/change-pricing-tier/pricing-tier-before.png)

6. Na stronie **Obszar nazw Centrum powiadomień** dla obszaru nazw wybierz **pozycję Warstwa cenowa** w sekcji **Zarządzanie.**

    ![Wybieranie warstwy cenowej na stronie obszaru nazw](./media/change-pricing-tier/namespace-select-pricing-menu.png)

7. Zmień warstwę cenową, a następnie kliknij przycisk **Wybierz.**
8. Stan akcji zmiany warstwy można zobaczyć w **alertach**.
9. Przełącz się do strony **Przegląd.** Upewnij się, że nowa warstwa jest wyświetlana dla pola **Warstwa cenowa** w sekcji **Essentials.**
10. Ten krok jest opcjonalny. Wybierz dowolny koncentrator w obszarze nazw. Upewnij się, że w sekcji Essentials jest widoczna ta sama warstwa **cenowa.** Powinna być widoczna ta sama warstwa cenowa dla wszystkich koncentratorów w obszarze nazw.

### <a name="change-tier-on-the-hub-page"></a>Zmienianie warstwy na stronie centrum

Poniższa procedura pokazuje, jak zmienić warstwę cenową dla obszaru nazw na stronie centrum. Mimo że te kroki można wykonać, zaczynając od strony centrum, faktycznie zmienić warstwę cenową dla obszaru nazw i wszystkich centrów w obszarze nazw.

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
2. Wybierz **pozycję Wszystkie usługi** w menu po lewej stronie.
3. Wybierz **centra powiadomień** w sekcji **Internet rzeczy.**
4. Wybierz **centrum**powiadomień .
5. Wybierz **warstwę cenową** w menu po lewej stronie.
6. Zmień warstwę cenową i kliknij przycisk **Wybierz.** Ta akcja zmienia ustawienie warstwy cenowej dla obszaru nazw zawierającego centrum. Tak, widać nową warstwę cenową na stronie obszaru nazw i wszystkich stron centrum.

> [!NOTE]
> Wszystkie zmiany warstwy cenowej wchodzą w życie natychmiast.

## <a name="use-rest-api"></a>Korzystanie z interfejsu API REST

Aby uzyskać bieżącą warstwę cenową i zaktualizować ją, można użyć następujących interfejsów API rest dostawcy zasobów.

### <a name="get-current-pricing-tier-for-a-namespace"></a>Pobierz bieżącą warstwę cenową dla obszaru nazw

Aby uzyskać bieżącą warstwę obszaru nazw, wyślij polecenie GET, jak pokazano w poniższym przykładzie:

```REST
GET: https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/notificationhubplan
```

### <a name="update-pricing-tier-for-a-namespace"></a>Aktualizowanie warstwy cenowej dla obszaru nazw

Aby zaktualizować warstwę obszaru nazw, wyślij polecenie PUT, jak pokazano w poniższym przykładzie:

```REST
PUT: https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/notificationhubplan
Body: <NotificationHubPlan xmlns:i="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/netservices/2010/10/servicebus/connect"><SKU>Standard</SKU></NotificationHubPlan>
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat tych warstw i cen, zobacz [Usługi powiadomień .](https://azure.microsoft.com/pricing/details/notification-hubs/)
