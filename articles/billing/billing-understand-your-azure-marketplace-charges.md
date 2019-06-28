---
title: Omówienie usługi Azure opłat za usługi zewnętrzne | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o rozliczeniach za korzystanie z usług zewnętrznych, znana wcześniej jako witryny Marketplace, opłat na platformie Azure.
services: ''
documentationcenter: ''
author: jureid
manager: jureid
editor: ''
tags: billing
ms.assetid: 5e0e2a3c-d111-4054-8508-0c111c1b749b
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: banders
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 90753c6046425b60fda04fa99b2952e706d9c0e5
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/21/2019
ms.locfileid: "67311938"
---
# <a name="understand-your-azure-external-services-charges"></a>O opłatach za usługi platformy Azure usługi zewnętrzne
Usługi zewnętrzne są publikowane przez producentów oprogramowania innych firm w witrynie Azure marketplace. Na przykład SendGrid to usługa zewnętrznych można zakupić w systemie Azure, ale nie został opublikowany przez firmę Microsoft. Niektóre produkty firmy Microsoft są sprzedawane za pośrednictwem witryny Azure marketplace, zbyt.

## <a name="how-external-services-are-billed"></a>Jak są rozliczane usług zewnętrznych

- Jeśli masz [umowy klienta Microsoft](#check-access-to-a-microsoft-customer-agreement), opłaty za usługi innych firm są naliczane z pozostałą częścią usługi platformy Azure.
- Jeśli nie masz umowy klienta firmy Microsoft, usługi zewnętrzne są rozliczane oddzielnie od usług platformy Azure.
- Każda usługa zewnętrznych ma inny model rozliczeń. Niektóre usługi są rozliczane w sposób zgodnie z rzeczywistym użyciem, natomiast inne mają stałe opłaty miesięczne.
- Nie można używać miesięcznych środków na korzystanie z bezpłatnych usług zewnętrznych. Jeśli używasz subskrypcji platformy Azure, która obejmuje [bezpłatne środki na korzystanie z](https://azure.microsoft.com/pricing/spending-limits/), nie można zastosować do opłat z usług zewnętrznych. Podczas aprowizacji nowej zewnętrznej usługi lub zasobu, zostanie wyświetlone ostrzeżenie:

    ![Portal Marketplace zakupu ostrzeżenie](./media/billing-understand-your-azure-marketplace-charges/credit-warning.png)

<!-- ## View external service spending and history in the Azure portal
You can view a list of the external services that are on each subscription within the [Azure portal](https://portal.azure.com/): 

1. Sign in to the [Azure portal](https://portal.azure.com/) as the account administrator.
2. In the Hub menu, select **Subscriptions**.
   
    ![Select Subscriptions in the Hub menu](./media/billing-understand-your-azure-marketplace-charges/sub-button.png) 
3. In the **Subscriptions** blade, select the subscription that you want to view, and then select **External services**.
   
    ![Select a subscription in the billing blade](./media/billing-understand-your-azure-marketplace-charges/select-sub-external-services.png)
4. You should see each of your external service orders, the publisher name, service tier you bought, name you gave the resource, and the current order status. To see past bills, select an external service.
   
    ![Select an external service](./media/billing-understand-your-azure-marketplace-charges/external-service-blade2.png)
5. From here, you can view past bill amounts including the tax breakdown.
   
    ![View external services billing history](./media/billing-understand-your-azure-marketplace-charges/billing-overview-blade.png) -->

## <a name="view-and-download-azure-marketplace-invoices"></a>Wyświetlanie i pobieranie faktur w portalu Azure marketplace

Jeśli masz [umowy klienta Microsoft](#check-access-to-a-microsoft-customer-agreement), opłaty za usługi innych firm znajdują się na tym samym faktur jako platformy Azure opłaty. Dowiedz się, jak [wyświetlić i pobrać fakturę Azure](billing-download-azure-invoice.md) z witryny Azure portal, aby wyświetlić opłat innych firm.

Jeśli nie masz umowy klienta firmy Microsoft, masz faktur oddzielne opłaty innych firm. Można wyświetlić i pobrać fakturach witryny Azure Marketplace w witrynie Azure portal, wykonując następujące czynności:

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com).
1. Wyszukiwanie **Cost Management + rozliczenia**.
1. W menu po lewej stronie wybierz **faktur**.
1. Kliknij pozycję **portalu Azure Marketplace i zastrzeżenia** kartę.  ![Obraz Azure marketplace i zastrzeżenia karty](./media/billing-understand-your-azure-marketplace-charges/invoice-tabs.png)
1. W subskrypcji listy rozwijanej wybierz subskrypcję, która zawiera usług zewnętrznych, aby wyświetlić faktury dla.

## <a name="view-external-service-spending-for-enterprise-agreement-ea-customers"></a>Zewnętrzna usługa widoku wydatków dla klientów z umową Enterprise Agreement (EA)

Klienci z umowami EA można wyświetlić, wydatków usługi zewnętrznej i pobieranie raportów w witrynie EA portal. Zobacz [portalu Azure Marketplace klienci z umowami EA](https://ea.azure.com/helpdocs/azureMarketplace) na rozpoczęcie pracy.

## <a name="manage-payment-methods-for-external-service-orders"></a>Zarządzaj metodami płatności dla usługi zewnętrznej zamówień

Przy zakupie usługi zewnętrznej, możesz wybrać subskrypcję platformy Azure dla zasobu. Formy płatności dla wybranej subskrypcji platformy Azure staje się metodę płatności dla usługi zewnętrznej. Aby zmienić metodę płatności dla usługi zewnętrznej, należy najpierw [Zmień formę płatności subskrypcji platformy Azure](billing-how-to-change-credit-card.md) związany z zewnętrznej usługi. Można określić subskrypcję, która jest powiązane zamówienia zewnętrznej usługi wykonaj następujące czynności:

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com).
1. Kliknij pozycję **wszystkie zasoby** w menu nawigacji po lewej stronie.
     ![Zrzut ekranu przedstawiający wszystkie elementy menu zasobów](./media/billing-understand-your-azure-marketplace-charges/all-resources.png)
1. Wyszukiwanie usługi zewnętrznej.
1. Wyszukaj nazwę subskrypcji w **subskrypcji** kolumny.
    ![Zrzut ekranu przedstawiający nazwę subskrypcji dla zasobów](./media/billing-understand-your-azure-marketplace-charges/sub-selected.png)
1. Kliknij nazwę subskrypcji i [zaktualizować metodę płatności active](billing-how-to-change-credit-card.md).
 
<!-- Update your payment methods for external service orders from the [Account Center](https://account.windowsazure.com/).

> [!NOTE]
> If you purchased your subscription with a Work or School account, [contact support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) to make changes to your payment method.

1. Sign in to the [Account Center](https://account.windowsazure.com/) and [navigate to the **marketplace** tab](https://account.windowsazure.com/Store)
   
    ![Select marketplace in the account center](./media/billing-understand-your-azure-marketplace-charges/select-marketplace.png)
2. Select the external service you want to manage
   
    ![Select the external service you want to manage](./media/billing-understand-your-azure-marketplace-charges/select-ext-service.png)
3. Click **Change payment method** on the right side of the page. This link brings you to a different portal to manage your payment method.
   
    ![Order summary](./media/billing-understand-your-azure-marketplace-charges/change-payment.PNG)
4. Click **Edit info** and follow instructions to update your payment information.
   
    ![Select edit info](./media/billing-understand-your-azure-marketplace-charges/edit-info.png) -->

## <a name="cancel-an-external-service-order"></a>Anulowanie zamówienia zewnętrznej usługi
Jeśli chcesz anulować zamówienie usługi zewnętrznej, Usuń zasób w [witryny Azure portal](https://portal.azure.com).

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com).
1. Kliknij pozycję **wszystkie zasoby** w menu nawigacji po lewej stronie.
    ![Zrzut ekranu przedstawiający wszystkie elementy menu zasobów](./media/billing-understand-your-azure-marketplace-charges/all-resources.png)
1. Wyszukiwanie usługi zewnętrznej.
1. Zaznacz pole obok zasobów, które chcesz usunąć.
1. Wybierz **Usuń** na pasku poleceń.
    ![Zrzut ekranu przedstawiający przycisk Usuń](./media/billing-understand-your-azure-marketplace-charges/delete-button.png)
1. Typ *"Yes"* w bloku potwierdzenia.
    ![Usuń zasób](./media/billing-understand-your-azure-marketplace-charges/delete-resource.PNG)
1. Kliknij przycisk **Usuń**.



## <a name="check-access-to-a-microsoft-customer-agreement"></a>Sprawdź dostęp do umowy klienta firmy Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [Utwórz żądanie obsługi](https://go.microsoft.com/fwlink/?linkid=2083458).

