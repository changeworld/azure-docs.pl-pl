---
title: Zmień ofertę subskrypcji platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o tym, jak zmienić subskrypcji platformy Azure i przełączanie do innej oferty przy użyciu Centrum konta platformy Azure
services: ''
documentationcenter: ''
author: genlin
manager: jlian
editor: ''
tags: billing,top-support-issue
ms.assetid: aae227b3-6d64-4550-a5b6-d359f53f0a59
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/30/2017
ms.author: genli
ms.openlocfilehash: d61daede687da077549e4a3e58bb0e5213c6a69b
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/03/2018
---
# <a name="change-your-azure-pay-as-you-go-subscription-to-a-different-offer"></a>Zmiana subskrypcji Azure płatność za rzeczywiste użycie do innej oferty

Jako [płatność za rzeczywiste użycie](https://azure.microsoft.com/offers/ms-azr-0003p/) klienta, możesz przełączyć subskrypcji platformy Azure do innej oferty w [Centrum konta](https://account.windowsazure.com/Subscriptions). Na przykład funkcja ten mógł korzystać z [miesięcznych środków dla subskrybentów programu Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/). 

**Po prostu chcesz uaktualnić z bezpłatnej wersji próbnej?** Zobacz [uaktualnienia do płatność za rzeczywiste użycie](billing-upgrade-azure-subscription.md).

## <a name="whats-supported"></a>Co to jest obsługiwana:

| Od | Do |
| --- | --- |
| Płatność zgodnie z rzeczywistym użyciem |[Płatność za rzeczywiste użycie: tworzenie i testowanie](https://azure.microsoft.com/offers/ms-azr-0023p/) |
| Płatność zgodnie z rzeczywistym użyciem |[Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/) |
| Płatność zgodnie z rzeczywistym użyciem |[Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/) |
| Płatność zgodnie z rzeczywistym użyciem |[Platformy MSDN](https://azure.microsoft.com/offers/ms-azr-0062p/) |
| Płatność zgodnie z rzeczywistym użyciem |[Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/) |
| Płatność zgodnie z rzeczywistym użyciem |[Visual Studio Enterprise (Bizspark)](https://azure.microsoft.com/offers/ms-azr-0064p/) |

> [!NOTE]
> Inne zmiany oferta [się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
>
>

## <a name="switch-subscription-offer"></a>Oferta subskrypcji przełącznika

> [!VIDEO https://channel9.msdn.com/Series/Microsoft-Azure-Tutorials/Switch-to-a-different-Azure-offer/player]
>
>

1. Zaloguj się na stronie [Centrum konta platformy Azure](https://account.windowsazure.com/Subscriptions).
1. Wybierz swoją subskrypcję rozliczaną zgodnie z rzeczywistym użyciem.
1. Kliknij pozycję **Przełącz na inną ofertę**. Ten przycisk jest dostępny tylko po wybraniu modelu płatności zgodnie z rzeczywistym użyciem i zakończeniu pierwszego okresu rozliczeniowego.

   ![Zwróć uwagę, przycisk przełącznika oferta w prawej części strony](./media/billing-how-to-switch-azure-offer/switchbutton.png)
1. **Wybierz oferty mają** z listy oferty subskrypcji mogą być przełączane do. Ta lista zależy od członkostwa, które Twoje konto jest skojarzone z. Jeśli nic nie jest dostępny, sprawdź [listę dostępnych ofert możesz przełączyć się do](#whats-supported) i upewnij się, że masz prawa członkostwa. 

   ![Wybierz oferty, który chcesz przełączyć się do](./media/billing-how-to-switch-azure-offer/selectoffer.png)
1. W zależności od rozpoczynasz korzystanie z oferty mogą pojawić się Uwaga dotyczących wpływu przełączania. Przejdź do tej listy dokładnie i postępuj zgodnie z instrukcjami, aby przejść dalej.

   ![Przejrzyj uwagi](./media/billing-how-to-switch-azure-offer/thingstonote.png)
1. Można zmienić nazwę subskrypcji. Domyślnie możemy ustawić go na nową nazwę oferty. Kliknij przycisk **oferują przełącznika** aby ukończyć proces.

   ![Kliknij zielony przycisk](./media/billing-how-to-switch-azure-offer/confirmpage.png)
1. To wszystko! Subskrypcję teraz jest włączane do nowej oferty.

## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="what-is-an-azure-offer"></a>Co to jest oferta platformy Azure?

Oferta platformy Azure jest *typu* subskrypcji Azure, masz. Na przykład [płatność za rzeczywiste użycie](https://azure.microsoft.com/offers/ms-azr-0003p/), [Azure otwartym](https://azure.microsoft.com/offers/ms-azr-0111p/), i [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/) są wszystkie oferty Azure. Każda oferta ma inną [warunki](https://azure.microsoft.com/support/legal/offer-details/) i niektóre mają specjalne korzyści. Oferta subskrypcji można znaleźć na stronie Centrum konta subskrypcji. Kliknij nazwę oferty, aby uzyskać więcej szczegółów.

   ![Kliknij łącze oferta w Centrum konta, aby uzyskać więcej informacji](./media/billing-how-to-switch-azure-offer/offerlink.png)

### <a name="why-dont-i-see-the-button"></a>Dlaczego nie widzę przycisku

Może nie być wyświetlana **Przełącz do innej oferty** przycisku, jeśli:

* Nie masz na [płatność za rzeczywiste użycie](https://azure.microsoft.com/offers/ms-azr-0003p/). Obecnie tylko płatność za rzeczywiste użycie subskrypcje mogą być konwertowane do innej oferty.
  * Jeśli używasz [bezpłatnej wersji próbnej](https://azure.microsoft.com/free/), Dowiedz się, jak [uaktualnienia do płatność za rzeczywiste użycie](billing-upgrade-azure-subscription.md).
  * Aby przełączyć oferowane przez inną subskrypcję, [się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
* Jesteś nadal w pierwszym okresie rozliczeniowym; należy poczekać z pierwszego okresu rozliczeniowego do końca, aby można było przełączyć oferty.

### <a name="why-do-i-see-there-are-no-offers-available-in-your-region-or-country-at-this-time"></a>Dlaczego zobacz "Nie są dostępne żadne oferty w kraj lub region w tym momencie"

* Może nie być uprawnia do skorzystania z przełącznikami oferty. Sprawdź [listę dostępnych ofert możesz przełączyć się do](#whats-supported) i upewnij się, że została aktywowana prawo korzyści z programu Visual Studio lub Bizspark.
* Niektóre oferty mogą nie być dostępne w pewnych krajach.

### <a name="what-does-switching-azure-offers-do-to-my-service-and-billing"></a>Jaki jest przełączenie oferty Azure na mój usługi i rozliczeń?

Poniżej przedstawiono szczegóły co się stanie po przełączeniu oferty w Centrum konta platformy Azure.

#### <a name="no-service-downtime"></a>Nie przestojów

Nie ma żadnych przestojów dla wszystkich użytkowników, skojarzone z subskrypcją. Przełącz się do oferty mogą jednak ograniczenia. Na przykład niektóre oferty uniemożliwiają użycia w środowisku produkcyjnym, więc będzie potrzebny do przenoszenia zasobów w środowisku produkcyjnym do innej subskrypcji.

#### <a name="quota-increases-are-reset"></a>Zwiększanie przydziału są resetowane

Po przełączeniu oferty, wszystkie [limitu lub przydziału przekroczy limit domyślny](../azure-supportability/resource-manager-core-quotas-request.md) są resetowane. Brak nie przestojów, nawet jeśli masz więcej zasobów, przekroczenie limitu domyślnego. Na przykład używasz 200 rdzeni w ramach subskrypcji, a następnie przełączenie oferty spowoduje zresetowanie limitu przydziału rdzeni domyślną 20 rdzeni. Maszyny wirtualne, korzystających z 200 rdzenie nie dotyczy to i będzie nadal działał. Jeśli nie wprowadzisz innego przydziału zwiększyć żądanie, jednak nie może obsłużyć wszystkie większej liczby rdzeni.

#### <a name="billing"></a>Rozliczenia

W dniu przełączania faktury jest generowany dla wszystkich zaległych opłat. Następnie w subskrypcji jest on rozliczany na warunki cenowe nowej oferty. Data zmiany oferty zmiany rocznicy rozliczeń Twojej subskrypcji. Rozliczeń i zużycia danych przed oferta zmiany nie zostaną zachowane, tak więc zaleca się pobrać kopię przed zmianą.

### <a name="can-i-migrate-from-pay-as-you-go-to-cloud-solution-providerhttpspartnermicrosoftcomsolutionscloud-reseller-overview-csp-or-enterprise-agreementhttpsazuremicrosoftcompricingenterprise-agreement-ea"></a>Można migrować z płatność za rzeczywiste użycie do [Cloud Solution Provider](https://partner.microsoft.com/Solutions/cloud-reseller-overview) (CSP) lub [umowy Enterprise Agreement](https://azure.microsoft.com/pricing/enterprise-agreement/) (EA)?

* Aby przeprowadzić migrację do dostawcy usług Kryptograficznych, zobacz [Azure płatność za rzeczywiste użycie subskrypcji migracja do dostawcy usług Kryptograficznych](https://docs.microsoft.com/azure/cloud-solution-provider/migration/migration-from-payg-to-csp).
* Aby migrować do EA, mieć administratora rejestracji, Dodaj konto do Umowa EA. Postępuj zgodnie z instrukcjami w wiadomości e-mail z zaproszeniem zostały przeniesione w obszarze EA rejestracji subskrypcji. Aby dowiedzieć się więcej, zobacz [skojarzyć konto](https://ea.azure.com/helpdocs/associateExistingAccount) w portalu EA.

### <a name="can-i-migrate-data-and-services-to-a-new-subscription"></a>Można przeprowadzić migrację danych i usług nową subskrypcję?

* Można przeprowadzić migrację zasobów bezpośrednio do nowej subskrypcji, zobacz [przenoszenia zasobów do nowej grupy zasobów lub subskrypcji](../azure-resource-manager/resource-group-move-resources.md).
* Aby przetransferować własność subskrypcji platformy Azure i wszystkie jego innej osobie, zobacz [przeniesieniem własności subskrypcji platformy Azure](billing-subscription-transfer.md)

## <a name="need-help-contact-support"></a>Potrzebujesz pomocy? Skontaktuj się z pomocą techniczną.

Jeśli masz więcej pytań, [się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) uzyskać szybkie rozwiązanie problemu.
