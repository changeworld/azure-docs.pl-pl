---
title: Zrozumienie sieci Azure koszty usługi zewnętrzne | Dokumentacja firmy Microsoft
description: Informacje o rozliczeniach usług zewnętrznych, wcześniej znana jako Marketplace, opłaty na platformie Azure.
services: ''
documentationcenter: ''
author: adpick
manager: tonguyen
editor: ''
tags: billing
ms.assetid: 5e0e2a3c-d111-4054-8508-0c111c1b749b
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/9/2017
ms.author: adpick
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6f7d8b89806e1f6d59e1a64e589558cd972f4fdc
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2018
---
# <a name="understand-your-azure-billing-for-external-service-charges"></a>Zrozumienie dla zewnętrznych opłaty za usługę Azure rozliczeniowego
Zewnętrznych usług są publikowane przez producentów oprogramowania innych firm w portalu Azure marketplace. Na przykład ClearDB i SendGrid są usług zewnętrznych, które można kupić na platformie Azure, ale nie są publikowane przez firmę Microsoft.

Podczas obsługi administracyjnej nowych zewnętrznej usługi lub zasobu, wyświetlane jest ostrzeżenie:

![Ostrzeżenie zakupu Marketplace](./media/billing-understand-your-azure-marketplace-charges/marketplace-warning.PNG)

> [!NOTE]
> Zewnętrznych usług są publikowane przez firmy, które nie są firmy Microsoft, ale czasami produktów firmy Microsoft są również podzielone na usług zewnętrznych.
> 
> 

## <a name="how-external-services-are-billed"></a>W jaki sposób są rozliczane usług zewnętrznych
- Zewnętrznych usług są rozliczane oddzielnie. Są one traktowane jako pojedyncze zamówienia w ramach Twojej subskrypcji platformy Azure. Okresie rozliczeniowym dla każdej usługi jest ustawiana po zakupie usługi. Nie należy mylić z okresu rozliczeniowego subskrypcji, w ramach którego go zakupiono. Otrzymasz również oddzielne rachunków i karty kredytowej jest naliczane osobno.
- Każda usługa zewnętrzny ma inny model rozliczeń. Niektóre usługi są rozliczane w sposób płatności obejmujące, podczas gdy inne miesięczne płatności na podstawie modelu. Karty kredytowej jest wymagane dla zewnętrznych usług Azure, nie można kupić usług zewnętrznych o płatności faktury.
- Nie można użyć miesięczne bezpłatnych środków dla usług zewnętrznych. Jeśli używasz subskrypcji platformy Azure, która obejmuje [wolne środków](https://azure.microsoft.com/pricing/spending-limits/), nie można zastosować do BOM zewnętrznej usługi. Za pomocą karty kredytowej kupić usług zewnętrznych.

## <a name="view-external-service-spending-and-history-in-the-azure-portal"></a>Widok zewnętrznej usługi wydatków i historię w portalu Azure
Można wyświetlić listę usług zewnętrznych, które znajdują się na każdej subskrypcji w ramach [portalu Azure](https://portal.azure.com/): 

1. Zaloguj się do [portalu Azure](https://portal.azure.com/) jako administratora konta.
2. W menu Centrum wybierz **subskrypcje**.
   
    ![W menu Centrum Wybierz subskrypcje](./media/billing-understand-your-azure-marketplace-charges/sub-button.png) 
3. W **subskrypcje** bloku, wybierz subskrypcję, którą chcesz wyświetlić, a następnie wybierz **usług zewnętrznych**.
   
    ![Wybierz subskrypcję w bloku rozliczeń](./media/billing-understand-your-azure-marketplace-charges/select-sub-external-services.png)
4. Powinna zostać wyświetlona każdego zamówień zewnętrznej usługi, nazwę wydawcy, warstwy usług, których używasz, nazwa nadana zasób i bieżący stan zlecenia. Aby wyświetlić poza rachunków, wybierz zewnętrznej usługi.
   
    ![Wybierz zewnętrznej usługi](./media/billing-understand-your-azure-marketplace-charges/external-service-blade2.png)
5. W tym miejscu można wyświetlić poza tym podział podatku kwoty rachunku.
   
    ![Widok usług zewnętrznych historię rozliczeń](./media/billing-understand-your-azure-marketplace-charges/billing-overview-blade.png)

## <a name="view-external-service-spending-for-enterprise-agreement-ea-customers"></a>Zewnętrzna usługa widoku wydatków dla klientów Enterprise Agreement (EA)
Zobacz wydatków zewnętrznej usługi klientom EA i pobierania raportów w portalu EA. Zobacz [Azure Marketplace w celu EA klienci](https://ea.azure.com/helpdocs/azureMarketplace) rozpocząć pracę.

## <a name="manage-payment-methods-for-external-service-orders"></a>Zarządzanie formy płatności dla zewnętrznych zleceniami
Aktualizowanie metody płatności zamówień zewnętrznej usługi z [Centrum konta](https://account.windowsazure.com/).

> [!NOTE]
> Jeśli zakupiono subskrypcję za pomocą konta służbowego [się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) wprowadzać zmiany do metody płatności.
> 
> 

1. Zaloguj się do [Centrum konta](https://account.windowsazure.com/) i [przejdź do **marketplace** kartę](https://account.windowsazure.com/Store)
   
    ![Wybierz w Centrum konta witryny marketplace](./media/billing-understand-your-azure-marketplace-charges/select-marketplace.png)
2. Wybierz usługę zewnętrznych, którą chcesz zarządzać
   
    ![Wybierz usługę zewnętrznych, którą chcesz zarządzać](./media/billing-understand-your-azure-marketplace-charges/select-ext-service.png)
3. Kliknij przycisk **Zmień formę płatności** w prawej części strony. To łącze powoduje przeniesienie do innego portalu do zarządzania formy płatności.
   
    ![Podsumowanie zlecenia](./media/billing-understand-your-azure-marketplace-charges/change-payment.PNG)
4. Kliknij przycisk **Edytuj informacje o** i postępuj zgodnie z instrukcjami, aby zaktualizować dane o płatności.
   
    ![Wybierz pozycję Edytuj informacje](./media/billing-understand-your-azure-marketplace-charges/edit-info.png)

## <a name="cancel-an-external-service-order"></a>Anulowanie zamówienia zewnętrznej usługi
Jeśli chcesz anulować zamówienia zewnętrznej usługi, Usuń zasób w [portalu Azure](https://portal.azure.com).

![Usuwanie zasobu](./media/billing-understand-your-azure-marketplace-charges/deleteMarketplaceOrder.PNG)

## <a name="need-help-contact-support"></a>Potrzebujesz pomocy? Skontaktuj się z pomocą techniczną.
Jeśli nadal masz pytania, [się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) uzyskać szybkie rozwiązanie problemu.

