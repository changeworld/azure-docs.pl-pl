---
title: Przekazywanie własności subskrypcji platformy Azure do innego konta | Dokumentacja firmy Microsoft
description: Opisuje, jak przenieść subskrypcję platformy Azure do innego użytkownika, a niektóre często zadawane pytania (FAQ) dotyczących procesu
keywords: przenieść subskrypcję do przeniesienia subskrypcji platformy azure, azure, przenoszenie subskrypcji platformy azure do innego właściciela subskrypcji Zmiana konta platformy azure, Przenieś subskrypcję platformy azure do innego konta
services: ''
documentationcenter: ''
author: genlin
manager: adpick
editor: ''
tags: billing,top-support-issue
ms.assetid: c8ecdc1e-c9c5-468c-a024-94ae41e64702
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: banders
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2ffebdd5b88603113dd7624e97dcf7353ab5d1ba
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2019
ms.locfileid: "65605571"
---
# <a name="transfer-ownership-of-an-azure-subscription-to-another-account"></a>Przenieść własność subskrypcji platformy Azure do innego konta

Przeniesienie subskrypcji do innego użytkownika w Centrum konta, aby zmienić administratora konta i przekazać własność rozliczeń subskrypcji. Aby zmienić subskrypcję na inną ofertę, zobacz [Przełącz subskrypcji platformy Azure na inną ofertę](billing-how-to-switch-azure-offer.md).

> [!IMPORTANT]
> 
> Jeśli przeniesiesz subskrypcję do nowej usługi Azure AD dzierżawy, wszystkie przypisania roli w [kontroli dostępu opartej na rolach (RBAC)](../role-based-access-control/overview.md) zostaną trwale usunięte z dzierżawy źródła i nie są migrowane do dzierżawy docelowej. Musisz również ręcznie odtworzyć utracone zarządzanych tożsamości dla zasobów platformy Azure. Aby uzyskać więcej informacji, zobacz [— często zadawane pytania i znane problemy związane z zarządzanych tożsamości](../active-directory/managed-identities-azure-resources/known-issues.md).

## <a name="transfer-ownership-of-an-azure-subscription"></a>Przekazywanie własności subskrypcji platformy Azure

> [!VIDEO https://channel9.msdn.com/Series/Microsoft-Azure-Tutorials/Transfer-an-Azure-subscription/player]
>
>

1. Zaloguj się na [Centrum konta platformy Azure](https://account.windowsazure.com/Subscriptions) jako administrator konta. Aby dowiedzieć się, kim jest administrator konta, subskrypcji, zobacz [— często zadawane pytania](#faq).

1. Wybierz subskrypcję, której chcesz przenieść.

1. Sprawdź, czy Twoja subskrypcja jest kwalifikuje się do przeniesienia Samoobsługa, sprawdzając **oferują** i **identyfikatora oferty Offer ID** z [listy obsługiwanych oferty](#supported).

   ![Sprawdź identyfikator oferty subskrypcji w Centrum konta](./media/billing-subscription-transfer/image0.png)
1. Kliknij pozycję **Przenieś subskrypcję**.

   ![Karty subskrypcje konta platformy Azure](./media/billing-subscription-transfer/image1.png)
1. Określ adresata.

   > [!IMPORTANT]
   > 
   > Jeśli przeniesiesz subskrypcję do nowej usługi Azure AD dzierżawy, wszystkie przypisania roli w [kontroli dostępu opartej na rolach (RBAC)](../role-based-access-control/overview.md) zostaną trwale usunięte z dzierżawy źródła i nie są migrowane do dzierżawy docelowej. Musisz również ręcznie odtworzyć utracone zarządzanych tożsamości dla zasobów platformy Azure. Aby uzyskać więcej informacji, zobacz [— często zadawane pytania i znane problemy związane z zarządzanych tożsamości](../active-directory/managed-identities-azure-resources/known-issues.md).

   ![Transfer subskrypcji, okno dialogowe](./media/billing-subscription-transfer/image2.PNG)

1. Adresat automatycznie otrzymuje wiadomość e-mail z linkiem umożliwiającym akceptację.

   ![Wiadomość e-mail do przeniesienia subskrypcji do adresata](./media/billing-subscription-transfer/image3.png)
1. Adresat klika link i postępuje zgodnie z instrukcjami, co obejmuje również wprowadzenie informacji dotyczących płatności.

   ![Pierwsza strona sieci web przeniesienia subskrypcji](./media/billing-subscription-transfer/image4.png)

   ![Drugiej strony sieci web przeniesienia subskrypcji](./media/billing-subscription-transfer/image5.png)
1. Powodzenie Subskrypcja jest obecnie przenoszona.

<a id="EA"></a>

## <a name="transfer-subscription-ownership-for-enterprise-agreement-ea-customers"></a>Przekazywanie własności subskrypcji dla klientów z umową Enterprise Agreement (EA)

Administrator przedsiębiorstwa może przenieść własność subskrypcji w ramach rejestracji. Aby rozpocząć pracę, zobacz [przeniesienie własności konta](https://ea.azure.com/helpdocs/changeAccountOwnerForASubscription) w witrynie EA portal.

## <a name="next-steps-after-accepting-ownership-of-a-subscription"></a>Następne kroki po zaakceptowaniu własności subskrypcji

1. Jesteś teraz administratorem konta. Przejrzyj i zaktualizuj administratora usługi Współadministratorów i innych ról RBAC. Aby dowiedzieć się więcej, zobacz [Administratorzy subskrypcji platformy Azure Dodaj lub zmień](billing-add-change-azure-subscription-administrator.md) i [zarządzanie dostępem przy użyciu RBAC i witryny Azure portal](../role-based-access-control/role-assignments-portal.md).
1. Aktualizuj poświadczenia skojarzone z usługami tej subskrypcji, w tym:
   1. Certyfikaty zarządzania, które Przyznaj użytkownikowi prawa administratora do zasobów subskrypcji. Aby uzyskać więcej informacji, zobacz [tworzenie i przekazywanie certyfikatów zarządzania dla platformy Azure](../cloud-services/cloud-services-certs-create.md)
   1. Klucze dostępu dla usług, takich jak magazyn. Aby uzyskać więcej informacji, zobacz [kontach magazynu Azure — informacje](../storage/common/storage-create-storage-account.md)
   1. Poświadczenia dostępu zdalnego dla usług, takich jak Azure Virtual Machines. 
1. Jeśli pracujesz z partnerem, należy wziąć pod uwagę zaktualizowaniu Identyfikatora partnera w ramach tej subskrypcji. Można zaktualizować Identyfikatora partnera w [witryny Azure portal](https://portal.azure.com).

<a id="supported"></a>

## <a name="whats-supported"></a>Jakie operacje są obsługiwane:

Przeniesienie subskrypcji Samoobsługa jest dostępny dla ofert lub typów subskrypcji wymienione w poniższej tabeli. Obecnie nie można przenieść bezpłatnej wersji próbnej lub [platformy Azure w otwartych (AIO)](https://azure.microsoft.com/offers/ms-azr-0111p/) subskrypcji. Aby uzyskać obejście tego problemu, zobacz [przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](../azure-resource-manager/resource-group-move-resources.md). Aby przesłać inne subskrypcje, takie jak [sponsorowania](https://azure.microsoft.com/offers/ms-azr-0036p/) lub plany pomocy technicznej, [się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

| Nazwa oferty                                                                             | Numer oferty |
|----------------------------------------------------------------------------------------|--------------|
| [Umowy Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)\*|MS-AZR-0017P        |
| [Microsoft Partner Network](https://azure.microsoft.com/offers/ms-azr-0025p/)          | MS-AZR-0025P        |
| [Platformy MSDN](https://azure.microsoft.com/offers/ms-azr-0062p/)                     | MS-AZR-0062P        |
| [Pay-As-You-Go](https://azure.microsoft.com/offers/ms-azr-0003p/)                      | MS-AZR-0003P        |
| [Płatność za rzeczywiste użycie, tworzenie i testowanie](https://azure.microsoft.com/offers/ms-azr-0023p/)             | MS-AZR-0023P        |
| [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/)           | MS-AZR-0063P        |
| [Visual Studio Enterprise: BizSpark](https://azure.microsoft.com/offers/ms-azr-0064p/) | MS-AZR-0064P        |
| [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/)         | MS-AZR-0059P        |
| [Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/)    | MS-AZR-0060P        |

\* [W witrynie EA portal](#EA)

<a id="faq"></a>

## <a name="frequently-asked-questions-faq"></a>Często zadawane pytania

### <a name="whoisaa"></a> Kto jest administratorem konta subskrypcji?

Administrator konta to osoba, która w konkursie lub zakupiono subskrypcję platformy Azure. Otrzymali oni autoryzację pozwalającą, aby uzyskać dostęp do [Centrum kont](https://account.azure.com/Subscriptions) i wykonywać różne zadania zarządzania, takie jak tworzenie subskrypcji, anulowania subskrypcji, rozliczeń dla subskrypcji zmienić lub zmienić administratora usługi. Jeśli nie wiesz, kto jest administratorem konta dla subskrypcji, wykonaj poniższe kroki, aby to sprawdzić.

1. Odwiedź [stronę Subskrypcje w witrynie Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Wybierz subskrypcję, aby sprawdzić, a następnie sprawdź w obszarze **ustawienia**.
1. Wybierz **właściwości**. Administrator konta subskrypcji są wyświetlane w **administrator konta** pole.

### <a name="does-everything-transfer-including-resource-groups-vms-disks-and-other-running-services"></a>Wszystko, co przenieść? W tym grup zasobów, maszyny wirtualne, dyski i innych usług uruchomionych?

Wszystkie zasoby, takie jak maszyny wirtualne, dyski i witryn sieci Web przesyłanie danych do nowego właściciela. Jednak wszelkie [ról administratora](billing-add-change-azure-subscription-administrator.md) i [kontroli dostępu opartej na rolach (RBAC)](../role-based-access-control/role-assignments-portal.md) po skonfigurowaniu zasad nie są przenoszone w różnych katalogach. Ponadto [rejestracje aplikacji](../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md) i innymi usługami specyficznym dla dzierżawy nie są przenoszone wraz.

### <a id="no-button"></a> Dlaczego nie widać przycisku "Przeniesienia subskrypcji"?

Niestety przenoszenie Samoobsługa subskrypcji nie jest dostępna dla oferty lub kraj/region. Aby przenieść swoją subskrypcję [się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="does-a-subscription-transfer-result-in-any-service-downtime"></a>Przeniesienie subskrypcji powoduje żadnych przestojów?

Nie ma to wpływu na usługi. Przeniesienie subskrypcji spowoduje anulowanie subskrypcji w ramach bieżącego konta administratora i tworzy subskrypcję w ramach konta adresata. Nowa subskrypcja jest skojarzona z podstawowych usług platformy Azure. Identyfikator subskrypcji pozostaje taki sam.

### <a name="how-do-i-use-this-process-to-change-the-directory-for-subscription"></a>Jak używać tego procesu, aby zmienić katalog subskrypcji?

Subskrypcja platformy Azure jest tworzony w katalogu, w której należy Administrator konta. Aby zmienić katalog, przenieść subskrypcję do konta użytkownika w katalogu docelowym. Po ukończeniu tego użytkownika kroki, aby zaakceptować transfer subskrypcji jest automatycznie przeniesiona na katalog docelowy.

### <a name="if-i-take-over-billing-ownership-of-a-subscription-from-another-organization-do-they-continue-to-have-access-to-my-resources"></a>Jeśli przejąć własności rozliczeń subskrypcji z innej organizacji, są one nadal mają dostęp do moich zasobów?

Jeśli subskrypcja jest przenoszona do innej dzierżawy, użytkownicy skojarzonych dzierżawę poprzedniego utratę dostępu do subskrypcji. Nawet jeśli użytkownik nie jest kontem administratora usługi lub współadministratora, mogą nadal mają dostęp do subskrypcji za pośrednictwem innych mechanizmów zabezpieczeń, w tym:

* Certyfikaty zarządzania, które Przyznaj użytkownikowi prawa administratora do zasobów subskrypcji. Aby uzyskać więcej informacji, zobacz [tworzenie i przekazywanie certyfikatu zarządzania dla platformy Azure](../cloud-services/cloud-services-certs-create.md).
* Klucze dostępu dla usług, takich jak magazyn. Aby uzyskać więcej informacji, zobacz [Informacje o kontach usługi Azure Storage](../storage/common/storage-create-storage-account.md).
* Poświadczenia dostępu zdalnego dla usług, takich jak Azure Virtual Machines.

Jeśli odbiorca musi ograniczyć dostęp do swoich zasobów, powinni rozważyć aktualizowanie jakichkolwiek kluczy tajnych skojarzonych z usługą. Większość zasobów można zaktualizować wykonując następujące kroki:

  1. Przejdź do witryny [Azure Portal](https://portal.azure.com).
  2. W menu Centrum wybierz **wszystkie zasoby**.
  3. Wybierz zasób.
  4. W bloku zasobów, kliknij przycisk **ustawienia**. W tym miejscu można wyświetlać i aktualizować istniejących kluczy tajnych.

### <a name="if-i-transfer-the-subscription-in-the-middle-of-the-billing-cycle-does-the-recipient-pay-for-the-entire-billing-cycle"></a>Jeśli czy mogę przenieść subskrypcję w trakcie okresu rozliczeniowego, cyklu odbiorcy płacisz całego rozliczeń?

Nadawca jest odpowiedzialny za zapłaty za dotychczasowe użycie, który został zgłoszony aż do momentu zakończeniu transferu. Adresat jest odpowiedzialny za użycie zgłoszone od momentu przeniesienia i nowszych wersjach. Może to być niektóre użycia, które miały miejsce przed przeniesieniem, ale zostało zgłoszone po tym dniu. Użycie jest uwzględniona w rachunku adresata.

### <a name="does-the-recipient-have-access-to-usage-and-billing-history"></a>Odbiorca ma dostęp do użycia i Historia rozliczeń?

  Wszystkie informacje dostępne do adresata jest kwota ostatniego rachunku lub jeśli subskrypcja została przeniesiona przed pierwszym rachunku generowane, bieżące saldo. Pozostała część użycia i Historia rozliczeń nie są przekazywane z tą subskrypcją.

### <a name="can-the-offer-be-changed-during-a-transfer"></a>Podczas transferu można zmienić tej oferty?

Oferta musi być taka sama. Aby zmienić ofertę, zobacz [Przełącz subskrypcji platformy Azure na inną ofertę](billing-how-to-switch-azure-offer.md).

### <a name="can-i-transfer-a-subscription-to-a-user-account-in-another-countryregion"></a>Czy mogę przenieść subskrypcję do konta użytkownika w innym kraju/regionie?

Nie, przeniesienie subskrypcji do konta użytkownika w innym kraju/regionie nie jest obsługiwana. Adresata, konto użytkownika musi być w tym samym kraju/regionu.

### <a name="can-the-recipient-use-a-different-payment-method"></a>Odbiorca, można użyć innej metody płatności?

Tak. Ale Historia rozliczeń subskrypcji jest podzielone między dwa konta.  

### <a name="is-the-payment-method-impacted-after-i-transferred-an-azure-subscription"></a>Sposób zapłaty jest wpływ, po przeniesieniu subskrypcji platformy Azure?

Aby zaakceptować transfer subskrypcji, karta kredytowa lub podobnej metody płatności musi być podana na zapłacenie za subskrypcję. Na przykład jeśli Bob przesyła subskrypcji do Magdalena i Magdalena akceptuje transferu, Magdalena podać formę płatności na zapłacenie za subskrypcję. Po zakończeniu przenoszenia Magdalena jest naliczana dla subskrypcji nie Roberta.

### <a name="how-do-i-migrate-data-and-services-for-my-azure-subscription-to-new-subscription"></a>Jak migrować dane i usługi dla subskrypcji platformy Azure do nowej subskrypcji?

Nie można przenieść własność subskrypcji, możesz ręcznie migrować swoje zasoby. Zobacz [przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](../azure-resource-manager/resource-group-move-resources.md).

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [Utwórz żądanie obsługi](https://go.microsoft.com/fwlink/?linkid=2083458).