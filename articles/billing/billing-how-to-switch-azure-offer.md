---
title: Zmiana oferty subskrypcji platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o tym, jak zmienić subskrypcję platformy Azure i przełączanie na inną ofertę za pomocą Centrum kont Azure
services: ''
documentationcenter: ''
author: genlin
manager: adpick
editor: ''
tags: billing,top-support-issue
ms.assetid: aae227b3-6d64-4550-a5b6-d359f53f0a59
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/11/2018
ms.author: banders
ms.openlocfilehash: bbdcbdc7ef288eeeb279c7e5e59baee492e1f292
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60918795"
---
# <a name="change-your-azure-pay-as-you-go-subscription-to-a-different-offer"></a>Zmień subskrypcję Azure rozliczana według bieżącego użycia na inną ofertę

Jako [płatność za rzeczywiste użycie](https://azure.microsoft.com/offers/ms-azr-0003p/) klienta, Twoja subskrypcja platformy Azure można przełączyć na inną ofertę w [Centrum kont](https://account.windowsazure.com/Subscriptions). Na przykład, można użyć tej funkcji, aby móc korzystać z [miesięcznych środków dla subskrybentów programu Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/). 

**Po prostu chcesz uaktualnić z bezpłatnej wersji próbnej?** Zobacz [Przeprowadź uaktualnienie do płatności](billing-upgrade-azure-subscription.md).

## <a name="whats-supported"></a>Jakie operacje są obsługiwane:

| Od | Do |
| --- | --- |
| Płatność zgodnie z rzeczywistym użyciem |[Płatność za rzeczywiste użycie, tworzenie i testowanie](https://azure.microsoft.com/offers/ms-azr-0023p/) |
| Płatność zgodnie z rzeczywistym użyciem |[Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/) |
| Płatność zgodnie z rzeczywistym użyciem |[Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/) |
| Płatność zgodnie z rzeczywistym użyciem |[Platformy MSDN](https://azure.microsoft.com/offers/ms-azr-0062p/) |
| Płatność zgodnie z rzeczywistym użyciem |[Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/) |
| Płatność zgodnie z rzeczywistym użyciem |[Visual Studio Enterprise (Bizspark)](https://azure.microsoft.com/offers/ms-azr-0064p/) |

> [!NOTE]
> Inne zmiany oferty [się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
>
>

## <a name="switch-subscription-offer"></a>Przełączanie oferty subskrypcji

> [!VIDEO https://channel9.msdn.com/Series/Microsoft-Azure-Tutorials/Switch-to-a-different-Azure-offer/player]
>
>

1. Zaloguj się na [Centrum konta platformy Azure](https://account.windowsazure.com/Subscriptions).
1. Wybierz swoją subskrypcję rozliczaną zgodnie z rzeczywistym użyciem.
1. Kliknij pozycję **Przełącz na inną ofertę**. Ten przycisk jest dostępny tylko po wybraniu modelu płatności zgodnie z rzeczywistym użyciem i zakończeniu pierwszego okresu rozliczeniowego.

   ![Zwróć uwagę, przycisk przełącznika oferty w prawej części strony](./media/billing-how-to-switch-azure-offer/switchbutton.png)
1. **Wybierz ofertę ma** z listy oferty można dostosować do Twojej subskrypcji. Ta lista różni się w zależności od członkostwa, z którym jest skojarzone Twoje konto. Jeśli nic nie jest dostępny, sprawdź [listę dostępnych ofert, możesz przełączyć się do](#whats-supported) i upewnij się, że masz prawo członkostwa. 

   ![Wybierz oferty, którą chcesz przełączyć się do](./media/billing-how-to-switch-azure-offer/selectoffer.png)
1. W zależności od oferty, w której na mogą pojawić się Uwaga dotycząca wpływ przełączania. Przejdź do tej listy dokładnie i postępuj zgodnie z instrukcjami, przed kontynuowaniem.

   ![Przejrzyj informacje](./media/billing-how-to-switch-azure-offer/thingstonote.png)
1. Można zmienić nazwę subskrypcji. Domyślnie możemy ustawić ją na nową nazwę oferty. Kliknij przycisk **oferują przełącznika** aby ukończyć proces.

   ![Kliknij zielony przycisk](./media/billing-how-to-switch-azure-offer/confirmpage.png)
1. To wszystko! Twoja subskrypcja jest teraz włączane na nową ofertę.

## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="what-is-an-azure-offer"></a>Co to jest oferta platformy Azure?

Oferta Azure *typu* masz subskrypcji platformy Azure. Na przykład [płatność za rzeczywiste użycie](https://azure.microsoft.com/offers/ms-azr-0003p/), [Azure in Open](https://azure.microsoft.com/offers/ms-azr-0111p/), i [programu Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/) są wszystkie oferty platformy Azure. Każda oferta ma inne [warunki](https://azure.microsoft.com/support/legal/offer-details/) i niektóre z nich mają specjalne korzyści. Oferta subskrypcji można znaleźć na stronie subskrypcji Centrum konta. Kliknij nazwę oferty, aby uzyskać więcej szczegółów.

   ![Kliknij link oferty w Centrum konta, aby uzyskać więcej szczegółów](./media/billing-how-to-switch-azure-offer/offerlink.png)

### <a name="why-dont-i-see-the-button"></a>Dlaczego nie widać przycisku?

Może nie być wyświetlana **Przełącz na inną ofertę** przycisk, jeśli:

* Nie masz na [płatność za rzeczywiste użycie](https://azure.microsoft.com/offers/ms-azr-0003p/). Obecnie tylko subskrypcje rozliczane zgodnie z rzeczywistym użyciem mogą być konwertowane na inną ofertę.
  * Jeśli używasz [bezpłatnej wersji próbnej](https://azure.microsoft.com/free/), Dowiedz się, jak [Przeprowadź uaktualnienie do płatności](billing-upgrade-azure-subscription.md).
  * Aby przełączyć oferty z innej subskrypcji, [się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
* Możesz teraz nadal pierwszego okresu rozliczeniowego; trzeba poczekać, aż do końca, aby można było przełączyć oferty pierwszego okresu rozliczeniowego.

### <a name="why-do-i-see-there-are-no-offers-available-in-your-region-or-country-at-this-time"></a>Dlaczego widzę "Nie są dostępne żadne oferty w kraj lub region w tej chwili"?

* Nie może być uprawnieni do skorzystania z oferty przełączników. Sprawdź [listę dostępnych ofert, możesz przełączyć się do](#whats-supported) i upewnij się, czy aktywowano prawo korzyści z programu Visual Studio lub Bizspark.
* Niektóre oferty nie mogą być dostępne we wszystkich krajach.

### <a name="what-does-switching-azure-offers-do-to-my-service-and-billing"></a>Ile kosztuje przełączanie oferty platformy Azure, czy do mojej usługi i rozliczeń?

Poniżej przedstawiono szczegółowe informacje o co się stanie po przełączeniu się z oferty w Centrum konta platformy Azure.

#### <a name="no-service-downtime"></a>Nie przestojów

Nie ma żadnych przestojów dla wszystkich użytkowników, skojarzone z tą subskrypcją. Jednak przełączasz się do oferty mogą mieć ograniczenia. Na przykład niektóre oferty Stanów Zjednoczonych zabraniają użycia w środowisku produkcyjnym, dlatego należy przenieść zasoby produkcyjne do innej subskrypcji.

#### <a name="quota-increases-are-reset"></a>Zwiększenie przydziału zostaną zresetowane.

Po przełączeniu ofert wszelkie [limit lub limitu przydziału przekroczy domyślny limit](../azure-supportability/resource-manager-core-quotas-request.md) zostaną zresetowane. Brak nie przestojów, nawet jeśli masz więcej zasobów, po przekroczeniu limitu domyślnego. Na przykład używasz 200 rdzeni w ramach subskrypcji, a następnie przełączanie oferty resetuje limit przydziału rdzeni do wartości domyślnych 20 rdzeni. Maszyny wirtualne, korzystające z 200 rdzeni nie ma wpływu i będzie nadal działał. Jeśli nie dokonasz inny limit przydziału, zwiększyć żądania, jednak nie można aprowizować żadnych więcej rdzeni.

#### <a name="billing"></a>Rozliczenia

W dniu, migracja faktury są generowane dla wszystkich zaległych opłat. Następnie Twoja subskrypcja jest rozliczane na podstawie nowej oferty warunki cenowe. Rocznicowa rozliczeń Twojej subskrypcji zmienia Data zmiany oferty. Rozliczeń i zużycia danych przed zmiany oferty nie są zachowywane, tak więc zaleca się pobrać kopię przed zmianą.

### <a name="can-i-migrate-from-pay-as-you-go-to-cloud-solution-providerhttpspartnermicrosoftcomsolutionscloud-reseller-overview-csp-or-enterprise-agreementhttpsazuremicrosoftcompricingenterprise-agreement-ea"></a>Można migrować z rzeczywistym użyciem, aby [Cloud Solution Provider](https://partner.microsoft.com/Solutions/cloud-reseller-overview) (CSP) lub [umowy Enterprise Agreement](https://azure.microsoft.com/pricing/enterprise-agreement/) (EA)?

* Aby przeprowadzić migrację do dostawcy usług Kryptograficznych, zobacz [Azure rozliczana według bieżącego użycia migracji do subskrypcji do dostawcy usług Kryptograficznych](https://docs.microsoft.com/azure/cloud-solution-provider/migration/migration-from-payg-to-csp).
* Aby przeprowadzić migrację do Umowy EA, ma dodać swoje konto do Umowy EA administrator rejestracji. Postępuj zgodnie z instrukcjami w wiadomości e-mail z zaproszeniem, aby mieć subskrypcji została przeniesiona do obszaru rejestracji umowy EA. Aby dowiedzieć się więcej, zobacz [skojarzyć konto](https://ea.azure.com/helpdocs/associateExistingAccount) w witrynie EA portal.

### <a name="can-i-migrate-data-and-services-to-a-new-subscription"></a>Czy można migrować dane i usługi do nowej subskrypcji?

* Można przeprowadzić migrację zasobów bezpośrednio do nowej subskrypcji, zobacz [przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](../azure-resource-manager/resource-group-move-resources.md).
* Aby przenieść własność subskrypcji platformy Azure oraz wszystkie elementy w nim innej osobie, zobacz [przekazywanie własności subskrypcji platformy Azure](billing-subscription-transfer.md)

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [Utwórz żądanie obsługi](https://go.microsoft.com/fwlink/?linkid=2083458).
