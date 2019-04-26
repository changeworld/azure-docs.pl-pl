---
title: Omówienie usługi Azure opłat za usługi zewnętrzne | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o rozliczeniach za korzystanie z usług zewnętrznych, znana wcześniej jako witryny Marketplace, opłat na platformie Azure.
services: ''
documentationcenter: ''
author: adpick
manager: adpick
editor: ''
tags: billing
ms.assetid: 5e0e2a3c-d111-4054-8508-0c111c1b749b
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/15/2018
ms.author: banders
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ae9c2c975bf49725be1858ad02a1c4b90ef58a7f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60370584"
---
# <a name="understand-your-azure-billing-for-external-service-charges"></a>Zrozumienie rozliczenia platformy Azure dla opłat za usługi zewnętrzne
Usługi zewnętrzne są publikowane przez producentów oprogramowania innych firm w witrynie Azure marketplace. Na przykład SendGrid jest usług zewnętrznych, które można kupić na platformie Azure, ale nie został opublikowany przez firmę Microsoft.

Podczas aprowizacji nowej zewnętrznej usługi lub zasobu, zostanie wyświetlone ostrzeżenie:

![Portal Marketplace zakupu ostrzeżenie](./media/billing-understand-your-azure-marketplace-charges/marketplace-warning.PNG)

> [!NOTE]
> Usługi zewnętrzne są publikowane przez firmy, które nie są firmy Microsoft, ale czasami produktów firmy Microsoft, są również określane jako usług zewnętrznych.
> 
> 

## <a name="how-external-services-are-billed"></a>Jak są rozliczane usług zewnętrznych
- Usługi zewnętrzne są rozliczane osobno. Będą one traktowane jako pojedyncze zamówienia w ramach subskrypcji platformy Azure. Przy zakupie usługi, jest ustawiony okresu rozliczeniowego dla każdej usługi. Nie należy mylić z okresu rozliczeniowego subskrypcji w ramach którego go zakupiono. Otrzymasz również oddzielnych rachunków i karty kredytowej jest rozliczany osobno.
- Każda usługa zewnętrznych ma inny model rozliczeń. Niektóre usługi są rozliczane w sposób zgodnie z rzeczywistym użyciem, a inni korzystają miesięcznych płatności oparte na modelu. Potrzebne dane karty kredytowej zewnętrznej usługi platformy Azure, możesz dokonać zakupu usług zewnętrznych, za pomocą faktury płatności.
- Nie można używać miesięcznych środków na korzystanie z bezpłatnych usług zewnętrznych. Jeśli używasz subskrypcji platformy Azure, która obejmuje [bezpłatne środki na korzystanie z](https://azure.microsoft.com/pricing/spending-limits/), nie można zastosować do usługi zewnętrznej rachunki. Za pomocą karty kredytowej kupić usług zewnętrznych.

## <a name="view-external-service-spending-and-history-in-the-azure-portal"></a>Widok usługi zewnętrznej wydatków i historii w witrynie Azure portal
Można wyświetlić listę usług zewnętrznych, które znajdują się w każdej subskrypcji w ramach [witryny Azure portal](https://portal.azure.com/): 

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com/) jako administratora konta.
2. W menu Centrum wybierz **subskrypcje**.
   
    ![W menu Centrum Wybierz subskrypcje](./media/billing-understand-your-azure-marketplace-charges/sub-button.png) 
3. W **subskrypcje** bloku, wybierz subskrypcję, dla której chcesz wyświetlić, a następnie wybierz **usług zewnętrznych**.
   
    ![Wybierz subskrypcję, w bloku rozliczeń](./media/billing-understand-your-azure-marketplace-charges/select-sub-external-services.png)
4. Powinien zostać wyświetlony, każda zamówienia usługi zewnętrznej, nazwę wydawcy, warstwy usług, których używasz, nazwa nadana zasób i bieżący stan zamówienia. Aby wyświetlić rachunki, wybierz usługi zewnętrznej.
   
    ![Wybierz usługi zewnętrznej](./media/billing-understand-your-azure-marketplace-charges/external-service-blade2.png)
5. W tym miejscu można wyświetlić ostatnie kwoty rachunek tym podział podatku.
   
    ![Usługi zewnętrzne widoku Historia rozliczeń](./media/billing-understand-your-azure-marketplace-charges/billing-overview-blade.png)

## <a name="view-external-service-spending-for-enterprise-agreement-ea-customers"></a>Zewnętrzna usługa widoku wydatków dla klientów z umową Enterprise Agreement (EA)
Klienci z umowami EA można wyświetlić, wydatków usługi zewnętrznej i pobieranie raportów w witrynie EA portal. Zobacz [portalu Azure Marketplace klienci z umowami EA](https://ea.azure.com/helpdocs/azureMarketplace) na rozpoczęcie pracy.

## <a name="manage-payment-methods-for-external-service-orders"></a>Zarządzaj metodami płatności dla usługi zewnętrznej zamówień
Aktualizuj metodami płatności dla usługi zewnętrznej zamówień z [Centrum kont](https://account.windowsazure.com/).

> [!NOTE]
> W przypadku zakupu subskrypcji przy użyciu konta firmowego lub szkolnego [się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) do zmiany metody płatności.
> 
> 

1. Zaloguj się do [Centrum kont](https://account.windowsazure.com/) i [przejdź do **marketplace** kartę](https://account.windowsazure.com/Store)
   
    ![Wybierz pozycję marketplace w Centrum konta](./media/billing-understand-your-azure-marketplace-charges/select-marketplace.png)
2. Wybierz usługę zewnętrznego, którą chcesz zarządzać
   
    ![Wybierz usługę zewnętrznego, którą chcesz zarządzać](./media/billing-understand-your-azure-marketplace-charges/select-ext-service.png)
3. Kliknij przycisk **Zmień formę płatności** w prawej części strony. Ten link łączy do innego portalu, aby zarządzać swoją formę płatności.
   
    ![Podsumowanie zamówień](./media/billing-understand-your-azure-marketplace-charges/change-payment.PNG)
4. Kliknij przycisk **Edytuj informacje o** i postępuj zgodnie z instrukcjami, aby zaktualizować dane o płatności.
   
    ![Wybierz pozycję Edytuj informacje](./media/billing-understand-your-azure-marketplace-charges/edit-info.png)

## <a name="cancel-an-external-service-order"></a>Anulowanie zamówienia zewnętrznej usługi
Jeśli chcesz anulować zamówienie usługi zewnętrznej, Usuń zasób w [witryny Azure portal](https://portal.azure.com).

![Usuń zasób](./media/billing-understand-your-azure-marketplace-charges/deleteMarketplaceOrder.PNG)

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [Utwórz żądanie obsługi](https://go.microsoft.com/fwlink/?linkid=2083458).

