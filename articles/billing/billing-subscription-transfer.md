---
title: Transfer własności subskrypcji platformy Azure na inne konto | Dokumentacja firmy Microsoft
description: Opisuje sposób transferu subskrypcji platformy Azure do innego użytkownika, a niektóre często zadawane pytania (FAQ) dotyczące procesu
keywords: przenieść subskrypcję transferu subskrypcji platformy azure, azure, Przenieś subskrypcji platformy azure do innego konta azure Zmień właściciela subskrypcji, transfer subskrypcji platformy azure na inne konto
services: ''
documentationcenter: ''
author: genlin
manager: jlian
editor: ''
tags: billing,top-support-issue
ms.assetid: c8ecdc1e-c9c5-468c-a024-94ae41e64702
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/11/2018
ms.author: genli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 110e2f611ba8bfc42fe17de6aa4487683db4a414
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/11/2018
ms.locfileid: "34069880"
---
# <a name="transfer-ownership-of-an-azure-subscription-to-another-account"></a>Transfer własności subskrypcji platformy Azure do innego konta

Przenieść subskrypcję do innego użytkownika w Centrum konta, aby zmienić konto administratora i przekazać własność rozliczeń subskrypcji. Aby zmienić subskrypcję do innej oferty, zobacz [Przełącz subskrypcji platformy Azure do innej oferty](billing-how-to-switch-azure-offer.md).

> [!IMPORTANT]
> 
> W przypadku przeniesienia subskrypcji do nowej usługi Azure AD dzierżawy, wszystkie przypisania roli w [kontroli dostępu opartej na rolach (RBAC)](../role-based-access-control/overview.md) są trwale usuwane z dzierżawy źródła i nie są migrowane do dzierżawy docelowej.

## <a name="transfer-ownership-of-an-azure-subscription"></a>Transfer własności subskrypcji platformy Azure

> [!VIDEO https://channel9.msdn.com/Series/Microsoft-Azure-Tutorials/Transfer-an-Azure-subscription/player]
>
>

1. Zaloguj się na stronie [Centrum konta platformy Azure](https://account.windowsazure.com/Subscriptions) jako konto administratora. Aby dowiedzieć się, kto jest administratora konta subskrypcji, zobacz [— często zadawane pytania](#faq).

1. Wybierz subskrypcję do transferu.

1. Sprawdź, czy subskrypcja jest kwalifikuje się do transferu Samoobsługa sprawdzając **oferują** i **identyfikator oferty** z [listy obsługiwanych oferty](#supported).

   ![Sprawdź identyfikator oferty subskrypcji w Centrum konta](./media/billing-subscription-transfer/image0.png)
1. Kliknij pozycję **Przenieś subskrypcję**.

   ![Karta subskrypcje konto platformy Azure](./media/billing-subscription-transfer/image1.png)
1. Określ adresata.

   > [!IMPORTANT]
   > 
   > W przypadku przeniesienia subskrypcji do nowej usługi Azure AD dzierżawy, wszystkie przypisania roli w [kontroli dostępu opartej na rolach (RBAC)](../role-based-access-control/overview.md) są trwale usuwane z dzierżawy źródła i nie są migrowane do dzierżawy docelowej.

   ![Okno dialogowe transfer subskrypcji](./media/billing-subscription-transfer/image2.PNG)

1. Adresat automatycznie otrzymuje wiadomość e-mail z linkiem umożliwiającym akceptację.

   ![E-mail transfer subskrypcji do adresata](./media/billing-subscription-transfer/image3.png)
1. Adresat klika link i postępuje zgodnie z instrukcjami, co obejmuje również wprowadzenie informacji dotyczących płatności.

   ![Pierwszej strony sieci web transfer subskrypcji](./media/billing-subscription-transfer/image4.png)

   ![Drugi strony sieci web transfer subskrypcji](./media/billing-subscription-transfer/image5.png)
1. To wszystko! Subskrypcja jest obecnie przenoszona.

<a id="EA"></a>

## <a name="transfer-subscription-ownership-for-enterprise-agreement-ea-customers"></a>Transfer własności subskrypcji dla klientów Enterprise Agreement (EA)

Administrator przedsiębiorstwa może przenieść własność subskrypcji w ramach rejestracji. Aby rozpocząć, zobacz [Transfer własności kont](https://ea.azure.com/helpdocs/changeAccountOwnerForASubscription) w portalu EA.

## <a name="next-steps-after-accepting-ownership-of-a-subscription"></a>Następne kroki po zaakceptowaniu własności subskrypcji

1. Jesteś teraz administratorem konta. Przejrzeć i zaktualizować administratora usługi, Co Administratorzy i inne role RBAC. Aby dowiedzieć się więcej, zobacz [Dodawanie lub zmienianie ról administrator usługi Azure, które zarządzają subskrypcji lub usługi](billing-add-change-azure-subscription-administrator.md).
1. Aktualizuj poświadczenia skojarzone z usługami tej subskrypcji w tym:
   1. Certyfikaty zarządzania, które Przyznaj użytkownikowi uprawnień administratora do zasobów subskrypcji. Aby uzyskać więcej informacji, zobacz [tworzenie i przekazywanie certyfikatu zarządzania platformy Azure](../cloud-services/cloud-services-certs-create.md)
   1. Klawisze dostępu dla usług, takich jak magazynu. Aby uzyskać więcej informacji, zobacz [konta magazynu Azure — informacje](../storage/common/storage-create-storage-account.md)
   1. Poświadczenia dostępu zdalnego dla usług, takich jak maszyny wirtualne platformy Azure. 
1. [Aktualizuj alerty dotyczące rozliczeń dla tej subskrypcji](billing-set-up-alerts.md) w [Centrum konta platformy Azure](https://account.windowsazure.com/Subscriptions). 
1. Jeśli współpracujesz z partnerem, należy rozważyć aktualizowanie Identyfikatora partnera w tej subskrypcji. Można zaktualizować Identyfikatora partnera w [portalu Azure](https://portal.azure.com).

<a id="supported"></a>

## <a name="whats-supported"></a>Co to jest obsługiwana:

Przenoszenie Samoobsługa subskrypcji jest dostępna dla oferty lub typów subskrypcji wymienionych w poniższej tabeli. Obecnie nie można przenieść bezpłatnej wersji próbnej lub [Azure w otwartych (AIO)](https://azure.microsoft.com/offers/ms-azr-0111p/) subskrypcji. Obejście tego problemu, zobacz [przenoszenia zasobów do nowej grupy zasobów lub subskrypcji](../azure-resource-manager/resource-group-move-resources.md). Aby przenieść inne subskrypcje, takich jak [dostęp sponsorowany](https://azure.microsoft.com/offers/ms-azr-0036p/) lub plany, pomocy technicznej [się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

| Nazwa oferty                                                                             | Numer oferty |
|----------------------------------------------------------------------------------------|--------------|
| [Umowa Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)\*|MS-AZR-0017P        |
| [Sieci Microsoft Partner Network](https://azure.microsoft.com/offers/ms-azr-0025p/)          | MS-AZR-0025P        |
| [Platformy MSDN](https://azure.microsoft.com/offers/ms-azr-0062p/)                     | MS-AZR-0062P        |
| [Pay-As-You-Go](https://azure.microsoft.com/offers/ms-azr-0003p/)                      | MS-AZR-0003P        |
| [Płatność za rzeczywiste użycie: tworzenie i testowanie](https://azure.microsoft.com/offers/ms-azr-0023p/)             | MS-AZR-0023P        |
| [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/)           | MS-AZR-0063P        |
| [Visual Studio Enterprise: BizSpark](https://azure.microsoft.com/offers/ms-azr-0064p/) | MS-AZR-0064P        |
| [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/)         | MS-AZR-0059P        |
| [Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/)    | MS-AZR-0060P        |

\* [W portalu EA](#EA)

<a id="faq"></a>

## <a name="frequently-asked-questions-faq"></a>Często zadawane pytania

### <a name="whoisaa"></a> Kto jest administratora konta subskrypcji?

Konto administratora jest osobą, która utworzył konto na lub zakupiono subskrypcję platformy Azure. Użytkownik jest uprawniony do dostępu [Centrum konta](https://account.azure.com/Subscriptions) i wykonywać różne zadania zarządzania, jak tworzyć subskrypcje, anulowanie subskrypcji, zmienić rozliczeń w ramach subskrypcji, zmienić administratora usługi. Jeśli nie masz pewności, który jest administratorem konta subskrypcji, aby dowiedzieć się, należy użyć następujące kroki.

1. Odwiedź stronę [w portalu Azure na stronie subskrypcji](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Wybierz subskrypcję, którą chcesz sprawdzić, a następnie sprawdź w obszarze **ustawienia**.
1. Wybierz **właściwości**. Administratora konta subskrypcji jest wyświetlany w **administrator konta** pole.

### <a name="does-everything-transfer-including-resource-groups-vms-disks-and-other-running-services"></a>Wszystko transferu? Łącznie z grupami zasobów, maszyn wirtualnych, dysków i inne uruchomione usługi?

Wszystkie zasoby, takie jak maszyn wirtualnych, dysków i transfer witryn sieci Web dla nowego właściciela. Jednak wszystkie [ról administratora](billing-add-change-azure-subscription-administrator.md) i [kontroli dostępu opartej na rolach (RBAC)](../role-based-access-control/role-assignments-portal.md) po skonfigurowaniu zasad nie są przenoszone w różnych katalogach. Ponadto [rejestracji aplikacji](../active-directory//develop/active-directory-integrating-applications.md) i innych usług specyficznego dla dzierżawy nie transfer wzdłuż.

### <a id="no-button"></a> Dlaczego nie widzę przycisku "Przenieść subskrypcję"?

Niestety przenoszenie Samoobsługa subskrypcji nie jest dostępna dla Twojej oferty lub kraju. Do przeniesienia subskrypcji, [się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="does-a-subscription-transfer-result-in-any-service-downtime"></a>Przenoszenie subskrypcji skutkuje przestój usługi?

Nie ma to wpływu na usługę. Przekazanie subskrypcji spowoduje anulowanie subskrypcji w obszarze bieżącego konta administratora i tworzy subskrypcję w ramach konta adresata. Nowa subskrypcja jest skojarzona z podstawowej usług Azure. Identyfikator subskrypcji jest taka sama.

### <a name="how-do-i-use-this-process-to-change-the-directory-for-subscription"></a>Jak używać tego procesu można zmienić katalogu dla subskrypcji?

Subskrypcja platformy Azure jest tworzony w katalogu, do którego należy administratora konta. Aby zmienić katalog, należy przenieść subskrypcję do konta użytkownika w katalogu docelowym. Po ukończeniu tego użytkownika kroki, aby zaakceptować transfer subskrypcji jest automatycznie przeniesiony do katalogu docelowego.

### <a name="if-i-take-over-billing-ownership-of-a-subscription-from-another-organization-do-they-continue-to-have-access-to-my-resources"></a>Jeśli przejąć własność rozliczeń subskrypcji z innej organizacji są one nadal mieć dostęp do moich zasobów?

Jeśli subskrypcja jest przenoszona do innego dzierżawcę, użytkownicy skojarzone z dzierżawcą poprzedniej utratę dostępu do subskrypcji. Nawet jeśli użytkownik nie jest administratorem usługi lub współadministratora, może nadal mają dostęp do subskrypcji za pośrednictwem innych mechanizmów zabezpieczeń, w tym:

* Certyfikaty zarządzania, które Przyznaj użytkownikowi uprawnień administratora do zasobów subskrypcji. Aby uzyskać więcej informacji, zobacz [tworzenie i przekazywanie certyfikatu zarządzania platformy Azure](../cloud-services/cloud-services-certs-create.md).
* Klawisze dostępu dla usług, takich jak magazynu. Aby uzyskać więcej informacji, zobacz [kont magazynu Azure o](../storage/common/storage-create-storage-account.md).
* Poświadczenia dostępu zdalnego dla usług, takich jak maszyny wirtualne platformy Azure.

Jeśli odbiorca musi ograniczyć dostęp do zasobów, należy rozważyć, aktualizowanie żadnych kluczy tajnych skojarzony z usługą. Najwięcej zasobów można aktualizować za pomocą następujących kroków:

  1. Przejdź do witryny [Azure Portal](https://portal.azure.com).
  2. W menu Centrum wybierz **wszystkie zasoby**.
  3. Wybierz zasób.
  4. W bloku zasobów, kliknij przycisk **ustawienia**. W tym miejscu można wyświetlić i zaktualizować istniejące kluczy tajnych.

### <a name="if-i-transfer-the-subscription-in-the-middle-of-the-billing-cycle-does-the-recipient-pay-for-the-entire-billing-cycle"></a>Czy mogę przenieść subskrypcję w trakcie cyklu rozliczeniowego, cyklu odbiorcy płatności dla całego rozliczeń?

Nadawca jest odpowiedzialny za płatności za bez użycia, który został zgłoszony do punktu zakończeniu transferu. Odbiorca jest odpowiedzialny za użycie zgłoszone od chwili transferu i jego nowszych wersjach. Może to być niektóre zastosowania, które miały miejsce przed przeniesieniem, ale został zgłoszony później. Użycie jest dołączony do rachunku adresata.

### <a name="does-the-recipient-have-access-to-usage-and-billing-history"></a>Odbiorca ma dostęp do użycia i rozliczeń historii?

  Tylko adresat będzie mógł odczytać informacji jest kwota rachunku ostatniego lub jeśli subskrypcja przeniesiono przed pierwszym rachunku został wygenerowany, bieżące saldo. Pozostała część użycia i Historia rozliczeń nie są przekazywane z subskrypcją.

### <a name="can-the-offer-be-changed-during-a-transfer"></a>Oferta, dla której można zmienić podczas transferu?

Oferta, dla której musi być taka sama. Aby zmienić ofertę, zobacz [Przełącz subskrypcji platformy Azure do innej oferty](billing-how-to-switch-azure-offer.md).

### <a name="can-i-transfer-a-subscription-to-a-user-account-in-another-country"></a>Czy można przenieść subskrypcję, do konta użytkownika w innym kraju?

Nie, transfer subskrypcji z kontem użytkownika w innym kraju nie jest obsługiwany. Konto użytkownika adresata musi być w tym samym kraju.

### <a name="can-the-recipient-use-a-different-payment-method"></a>Odbiorca może użyć innej metody płatności?

Tak. Ale Historia rozliczeń subskrypcji jest podzielić na dwa konta.  

### <a name="is-the-payment-method-impacted-after-i-transferred-an-azure-subscription"></a>Metodę płatności pogarsza po przeniesieniu subskrypcji platformy Azure?

Aby zaakceptować transfer subskrypcji, należy podać karty kredytowej lub podobnej metody płatności płatności za subskrypcję. Na przykład jeśli Bob przekaże Magdalena subskrypcji, Magdalena akceptuje transferu Magdalena podać formę płatności, aby zwrócić dla subskrypcji. Po zakończeniu przenoszenia Magdalena jest on rozliczany subskrypcji nie Roberta.

### <a name="how-do-i-migrate-data-and-services-for-my-azure-subscription-to-new-subscription"></a>Jak migrację danych i usługi dla subskrypcji platformy Azure do nowej subskrypcji?

Jeśli nie można przetransferować własność subskrypcji, można migrować ręcznie zasobów. Zobacz [przenoszenia zasobów do nowej grupy zasobów lub subskrypcji](../azure-resource-manager/resource-group-move-resources.md).

## <a name="need-help-contact-support"></a>Potrzebujesz pomocy? Skontaktuj się z pomocą techniczną.

Jeśli nadal potrzebujesz pomocy, [się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) uzyskać szybkie rozwiązanie problemu.