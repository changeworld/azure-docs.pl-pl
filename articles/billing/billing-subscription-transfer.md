---
title: Przenieś własność rozliczeń subskrypcji platformy Azure do innego konta | Dokumentacja firmy Microsoft
description: Opisuje sposób przenieść własność rozliczeń subskrypcji platformy Azure do innego konta, a niektóre często zadawane pytania (FAQ) dotyczących procesu
keywords: przenieść subskrypcję do przeniesienia subskrypcji platformy azure, azure, przenieść subskrypcję platformy azure do innego właściciela subskrypcji Zmiana konta platformy azure, przeniesienie subskrypcji platformy azure do innego rozliczanie transferu konta platformy azure
author: bandersmsft
manager: amberb
tags: billing,top-support-issue
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: banders
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7d41e32065e3de37eb8f01ab1b836040e7f57b12
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/08/2019
ms.locfileid: "67657848"
---
# <a name="transfer-billing-ownership-of-an-azure-subscription-to-another-account"></a>Przenieś własność rozliczeń subskrypcji platformy Azure do innego konta

Możesz chcieć przenieść własność rozliczeń subskrypcji platformy Azure są opuszczenie organizacji, czy chcesz, aby Twoja subskrypcja jest naliczana na inne konto. Przenoszenia własności rozliczeń na inne konto zapewnia Administratorzy nowych uprawnień konta do wykonywania zadań rozliczeń, takich jak Zmień formę płatności, wyświetlanie opłat i anulować subskrypcję.

Jeśli chcesz zachować własność rozliczeń, ale zmiana typu Twojej subskrypcji, zobacz [Przełącz subskrypcji platformy Azure na inną ofertę](billing-how-to-switch-azure-offer.md). Jeśli użytkownik chce kontrolować, kto może zarządzać zasobami w ramach subskrypcji, zobacz [wbudowane role zasobów platformy Azure](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).

Jeśli jesteś klientem Agreement(EA) przedsiębiorstwa, administratorów przedsiębiorstwa może przenieść własność rozliczeń subskrypcji między kontami. Aby uzyskać więcej informacji, zobacz [przenieść własność rozliczeń subskrypcji Enterprise Agreement (EA)](#transfer-billing-ownership-of-enterprise-agreement-ea-subscriptions).

## <a name="transfer-billing-ownership-of-an-azure-subscription"></a>Przenieść własność rozliczeń subskrypcji platformy Azure

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) jako administrator konta rozliczeniowego, który ma subskrypcję, dla której chcesz przenieść. Aby dowiedzieć się, jeśli jesteś administratorem, zobacz [— często zadawane pytania](#faq).

1. Wyszukiwanie **Cost Management + rozliczenia**.

   ![Zrzut ekranu pokazujący usługi Azure search w portalu](./media/billing-subscription-transfer/billing-search-cost-management-billing.png)

1. Wybierz **subskrypcje** z okienka po lewej stronie. W zależności od dostęp, może być konieczne wybierz zakres rozliczeń, a następnie wybierz pozycję **subskrypcje** lub **subskrypcji platformy Azure**.

1. Wybierz **przenieść własność rozliczeń** dla subskrypcji, który chcesz przenieść. 

   ![Wybierz subskrypcję do transferu](./media/billing-subscription-transfer/billing-select-subscription-to-transfer.png)

1. Wprowadź adres e-mail użytkownika, który jest administratorem rozliczeń konta, który będzie nowego właściciela dla subskrypcji i następnie wybierz pozycję **Wyślij żądanie przeniesienia**.

    > [!IMPORTANT]
    >
    > W przypadku przenoszenia własności rozliczeń subskrypcji na koncie użytkownika w innej usłudze Azure AD dzierżawy, wszystkie [kontroli dostępu opartej na rolach (RBAC)](../role-based-access-control/overview.md) przypisania do zarządzania zasobami w ramach subskrypcji są trwale usuwane. Tylko nowy właściciel będzie mieć dostęp do zarządzania zasobami w ramach subskrypcji. Aby uzyskać więcej informacji, zobacz [przeniesienie subskrypcji do użytkownika w innej dzierżawie usługi Azure AD](../active-directory/managed-identities-azure-resources/known-issues.md).
  
    ![Wyślij stronę transferu](./media/billing-subscription-transfer/billing-send-transfer-request.PNG)

1. Użytkownik otrzymuje wiadomość e-mail z instrukcjami, aby zapoznać się z Twoje żądanie przeniesienia.

   ![Wiadomość e-mail do przeniesienia subskrypcji do adresata](./media/billing-subscription-transfer/billing-receiver-email.png)

1. Aby zatwierdzić żądanie przeniesienia, użytkownik wybierze link w wiadomości e-mail i instrukcje. Użytkownik będzie musiał Wybierz formę płatności, która będzie służyć do zapłacenie za subskrypcję. Ponadto jeśli użytkownik nie ma konta platformy Azure, zostałyby Załóż nowe konto. 

   ![Pierwsza strona sieci web przeniesienia subskrypcji](./media/billing-subscription-transfer/billing-accept-ownership-step1.png)

   ![Drugiej strony sieci web przeniesienia subskrypcji](./media/billing-subscription-transfer/billing-accept-ownership-step2.png)

   ![Drugiej strony sieci web przeniesienia subskrypcji](./media/billing-subscription-transfer/billing-accept-ownership-step3.png)

1. To wszystko! Subskrypcja jest obecnie przenoszona.

## <a name="transferring-subscription-to-an-account-in-another-azure-ad-tenant"></a>Przeniesienie subskrypcji do konta w innej dzierżawie usługi Azure AD

Dzierżawy usługi Azure Active Directory (AD) jest tworzone po zarejestrowaniu się na platformie Azure. Dzierżawca reprezentuje Twoje konto. Dzierżawa umożliwia zarządzanie dostępem do Twojej subskrypcji i zasobów.

Podczas tworzenia nowej subskrypcji, znajduje się w dzierżawie usługi Azure AD, konta usługi. Zapewnienie osobom dostęp do zasobów lub subskrypcji, należy zaprosić ich do swojej dzierżawy. Ta ułatwia kontrolowanie dostępu do Twojej subskrypcji i zasobów.

W przypadku przeniesienia własności rozliczeń subskrypcji do konta w innej dzierżawie usługi Azure AD, subskrypcja jest przenoszona do nowego konta dzierżawy. Użytkownicy, grupy lub jednostki usługi, którzy utworzyli [oparta na rolach (RBAC) dostępu](../role-based-access-control/overview.md) Zarządzanie zasoby w subskrypcji utracą dostęp. Tylko użytkownik nowego konta, który akceptuje Twoje żądanie przeniesienia mają dostęp do zarządzania zasobami. Aby zapewnić dostęp dla użytkowników, którzy pierwotnie miały dostęp, musi nowego właściciela [ręcznie dodać tych użytkowników do subskrypcji](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal).


## <a name="transferring-visual-studio-microsoft-partner-network-mpn-and-pay-as-you-go-devtest-subscriptions"></a>Przenoszenie programu Visual Studio, Microsoft Partner Network (MPN) i subskrypcji płatnej zgodnie z rzeczywistym użyciem — tworzenie i testowanie

Subskrypcje programu Visual Studio i Microsoft Partner Network mają miesięczne środki platformy Azure cyklicznego skojarzonych z nimi. Gdy przeniesiesz te subskrypcje, swoje środki na korzystanie nie jest dostępna w ramach konta rozliczeniowego docelowego. Subskrypcja używa środków w ramach konta rozliczeniowego docelowego. Na przykład jeśli Bob przesyła subskrypcję programu Visual Studio Enterprise do konta na 9 września i Jane Joanny akceptuje transferu. Po zakończeniu transferu subskrypcji rozpoczyna się przy użyciu środków na koncie Joanny. Spowoduje to zresetowanie środków co miesiąc na 9. 


<a id="EA"></a>

## <a name="transfer-billing-ownership-of-enterprise-agreement-ea-subscriptions"></a>Przenieść własność rozliczeń subskrypcji Enterprise Agreement (EA)

Administrator przedsiębiorstwa może przenieść własność subskrypcji między kontami w ramach rejestracji. Aby uzyskać więcej informacji, zobacz [przeniesienie własności konta](https://ea.azure.com/helpdocs/changeAccountOwnerForASubscription) w witrynie EA portal.

<a id="CSP"></a>

## <a name="next-steps-after-accepting-billing-ownership"></a>Następne kroki po zaakceptowaniu własność rozliczeń

Jeśli zaakceptujesz własność rozliczeń subskrypcji platformy Azure, zalecamy przejrzenie podjęcie następujących kroków:

1. Przejrzyj i zaktualizuj administratora usługi Współadministratorów i innych ról RBAC. Aby dowiedzieć się więcej, zobacz [Administratorzy subskrypcji platformy Azure Dodaj lub zmień](billing-add-change-azure-subscription-administrator.md) i [zarządzanie dostępem przy użyciu RBAC i witryny Azure portal](../role-based-access-control/role-assignments-portal.md).
1. Aktualizuj poświadczenia skojarzone z usługami tej subskrypcji, w tym:
   1. Certyfikaty zarządzania, które Przyznaj użytkownikowi prawa administratora do zasobów subskrypcji. Aby uzyskać więcej informacji, zobacz [tworzenie i przekazywanie certyfikatów zarządzania dla platformy Azure](../cloud-services/cloud-services-certs-create.md)
   1. Klucze dostępu dla usług, takich jak magazyn. Aby uzyskać więcej informacji, zobacz [kontach magazynu Azure — informacje](../storage/common/storage-create-storage-account.md)
   1. Poświadczenia dostępu zdalnego dla usług, takich jak Azure Virtual Machines.
1. Jeśli pracujesz z partnerem, należy wziąć pod uwagę zaktualizowaniu Identyfikatora partnera w ramach tej subskrypcji. Można zaktualizować Identyfikatora partnera w [witryny Azure portal](https://portal.azure.com). Aby uzyskać więcej informacji, zobacz [połączyć Identyfikatora partnera kont systemu Azure](billing-partner-admin-link-started.md)

<a id="supported"></a>

## <a name="supported-subscription-types"></a>Typy obsługiwane subskrypcji

Przeniesienie subskrypcji w witrynie Azure portal jest dostępna dla typów subskrypcji wymienione poniżej. Obecnie transfer nie jest obsługiwany dla [bezpłatnej wersji próbnej](https://azure.microsoft.com/offers/ms-azr-0044p/) lub [platformy Azure w otwartych (AIO)](https://azure.microsoft.com/offers/ms-azr-0111p/) subskrypcji. Aby uzyskać obejście tego problemu, zobacz [przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](../azure-resource-manager/resource-group-move-resources.md). Aby przesłać inne subskrypcje, takie jak [sponsorowania](https://azure.microsoft.com/offers/ms-azr-0036p/) lub plany pomocy technicznej, [skontaktuj się z działem pomocy technicznej systemu Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

- [Umowy Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)\*
- [Microsoft Partner Network](https://azure.microsoft.com/offers/ms-azr-0025p/)  
- [Subskrybenci programu Visual Studio Enterprise (MPN)](https://azure.microsoft.com/offers/ms-azr-0029p/) 
- [Platformy MSDN](https://azure.microsoft.com/offers/ms-azr-0062p/)  
- [Pay-As-You-Go](https://azure.microsoft.com/offers/ms-azr-0003p/) 
- [Płatność za rzeczywiste użycie, tworzenie i testowanie](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/)
- [Visual Studio Enterprise: BizSpark](https://azure.microsoft.com/offers/ms-azr-0064p/)
- [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/)
- [Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/)
- [Plan platformy Microsoft Azure](https://azure.microsoft.com/offers/ms-azr-0017g/)\*\*

\* [W portalu EA](#EA).

\*\* Obsługiwane tylko dla kont, które są tworzone podczas logowania się w witrynie internetowej platformy Azure. 

<a id="faq"></a>

## <a name="frequently-asked-questions-faq-for-senders"></a>Często zadawane pytania (FAQ) dotyczące nadawcy

Wśród często zadawanych pytań dotyczy użytkowników, którzy transferowania własność rozliczeń subskrypcji platformy Azure do innego konta.

### <a name="whoisaa"></a> Kto jest administratorem rozliczeń konta?

Administrator rozliczeń jest osoba, która ma uprawnienia do zarządzania rozliczenia dla konta. Otrzymali oni autoryzację pozwalającą dostęp do rozliczeń w [witryny Azure portal](https://portal.azure.com) i wykonywania różnych zadań rozliczeń, jak utworzyć subskrypcje, widok i płatność faktury lub aktualizacji formy płatności.

Aby zidentyfikować subskrypcje, dla których jesteś administratorem rozliczeń, wykonaj następujące kroki:

1. Odwiedź stronę [Zarządzanie kosztami i rozliczenia strony w witrynie Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/Overview).
1. Wybierz **subskrypcje** z okienka po lewej stronie. W zależności od dostęp, może być konieczne wybierz zakres rozliczeń, a następnie wybierz pozycję **subskrypcje** lub **subskrypcji platformy Azure**
1. Na stronie subskrypcje są wymienione wszystkie subskrypcje, dla których użytkownik jest administratorem rozliczeń.

### <a name="does-everything-transfer-including-resource-groups-vms-disks-and-other-running-services"></a>Wszystko, co przenieść? W tym grup zasobów, maszyny wirtualne, dyski i innych usług uruchomionych?

Wszystkie zasoby, takie jak maszyny wirtualne, dyski i witryn sieci Web przesyłanie danych do nowego konta. Jednak jeśli przeniesiesz subskrypcję z kontem w usłudze Azure AD w innej dzierżawy, dowolny [ról administratora](billing-add-change-azure-subscription-administrator.md) i [kontroli dostępu opartej na rolach (RBAC)](../role-based-access-control/role-assignments-portal.md) przypisania w ramach subskrypcji [nie transfer](#transferring-subscription-to-an-account-in-another-azure-ad-tenant). Ponadto [rejestracje aplikacji](../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md) i innymi usługami specyficznym dla dzierżawy nie są przenoszone wraz z subskrypcji.

### <a name="can-i-transfer-ownership-to-an-account-in-another-country"></a>Czy mogę przenieść własności konta w innym kraju?
Niestety między kraju transfery nie można wykonać w witrynie Azure portal. Przeniesienia Twojej subskrypcji w krajach, [się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="i-am-an-administrator-on-two-accounts-can-i-transfer-a-subscription-from-one-of-my-accounts-to-another"></a>Jestem administratorem na dwóch kont. Czy mogę przenieść subskrypcję z jednego z kont do innej?
Tak, można przenieść subskrypcję od konta. Twoje konta pod względem koncepcyjnym są traktowane jako konta dwóch różnych użytkowników, aby można było używać powyższych kroków do transferowania subskrypcji między kontami usługi.

### <a name="does-a-subscription-transfer-result-in-any-service-downtime"></a>Przeniesienie subskrypcji powoduje żadnych przestojów?

Jeśli przeniesiesz subskrypcję użytkownika w tej samej dzierżawie usługi Azure AD nie ma to wpływu na zasoby w subskrypcji.  Jednak jeśli przeniesiesz tę subskrypcję do użytkownika w innej dzierżawy, użytkownicy, grupy i jednostki usługi, którzy utworzyli [oparta na rolach (RBAC) dostępu](../role-based-access-control/overview.md) Zarządzanie zasoby w subskrypcji utracą dostęp. 

### <a name="does-the-recipient-have-access-to-usage-and-billing-history"></a>Odbiorca ma dostęp do użycia i Historia rozliczeń?

Tylko informacje o dostępnych do adresata są kosztu w ciągu ostatniego miesiąca dla Twojej subskrypcji. Pozostała część użycia i Historia rozliczeń nie są przekazywane z subskrypcją

### <a name="how-do-i-migrate-data-and-services-for-my-azure-subscription-to-new-subscription"></a>Jak migrować dane i usługi dla subskrypcji platformy Azure do nowej subskrypcji?

Nie można przenieść własność subskrypcji, możesz ręcznie migrować swoje zasoby. Zobacz [przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](../azure-resource-manager/resource-group-move-resources.md).

### <a name="if-i-transfer-a-visual-studio-or-microsoft-partner-network-subscription-does-my-credit-carry-forward-with-the-subscription-in-the-new-account"></a>Jeśli subskrypcja programu Visual Studio lub Microsoft Partner Network czy mogę przenieść, Mój kredyt przenoszone z subskrypcją w nowe konto?

Nie, środki nie jest dostępna w nowym koncie. Użytkownik, który akceptuje żądanie przeniesienia musi mieć licencję programu Visual Studio, aby zaakceptować żądanie przeniesienia. Subskrypcja używa środki programu Visual Studio, które są dostępne na koncie użytkownika. Aby uzyskać więcej informacji, zobacz [subskrypcje transferu programu Visual Studio, Microsoft Partner Network (MPN) i płatność zgodnie z rzeczywistym użyciem — tworzenie i testowanie](#transferring-visual-studio-microsoft-partner-network-mpn-and-pay-as-you-go-devtest-subscriptions)


## <a name="frequently-asked-questions-faq-for-recipients"></a>Często zadawane pytania (FAQ) dla odbiorców

Wśród często zadawanych pytań dotyczy użytkowników, którzy są przejęcia własności rozliczeń subskrypcji platformy Azure z innego konta.

### <a name="if-i-take-over-billing-ownership-of-a-subscription-from-another-account-do-users-in-that-account-continue-to-have-access-to-my-resources"></a>Jeśli przejąć własności rozliczeń subskrypcji z innego konta zrobić użytkownicy, w tym, że konta nadal mieć dostęp do moich zasobów?

Jeśli subskrypcja została przeniesiona do konta w tej samej dzierżawie usługi Azure AD, wszystkich użytkowników, grupy i jednostki usługi, którzy utworzyli [oparta na rolach (RBAC) dostępu](../role-based-access-control/overview.md) zarządzać zasobów w subskrypcji zachować ich dostęp. Aby wyświetlić użytkowników, którzy mają dostęp RBAC do subskrypcji, wykonaj następujące kroki:

1. Odwiedź stronę [stronę subskrypcji w witrynie Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Wybierz subskrypcję, aby sprawdzić, a następnie wybierz **kontrola dostępu (IAM)** z okienka po lewej stronie.
1. Wybierz **przypisań ról** w górnej części strony. Strona przypisania roli Wyświetla listę wszystkich użytkowników, którzy mają dostęp RBAC w ramach subskrypcji.

Jeśli subskrypcja została przeniesiona do konta w innej dzierżawie usługi Azure AD, wszystkich użytkowników, grupy i jednostki usługi, którzy utworzyli [oparta na rolach (RBAC) dostępu](../role-based-access-control/overview.md) Zarządzanie zasoby w subskrypcji utracą dostęp. Jednak nawet jeśli nie mają już dostęp RBAC, może nadal ma on dostęp do subskrypcji za pomocą niektórych mechanizmów zabezpieczeń, w tym:

* Certyfikaty zarządzania, które Przyznaj użytkownikowi prawa administratora do zasobów subskrypcji. Aby uzyskać więcej informacji, zobacz [tworzenie i przekazywanie certyfikatu zarządzania dla platformy Azure](../cloud-services/cloud-services-certs-create.md).
* Klucze dostępu dla usług, takich jak magazyn. Aby uzyskać więcej informacji, zobacz [Informacje o kontach usługi Azure Storage](../storage/common/storage-create-storage-account.md).
* Poświadczenia dostępu zdalnego dla usług, takich jak Azure Virtual Machines.

Jeśli odbiorcy należy ograniczyć dostęp do swoich zasobów, powinni rozważyć aktualizowanie jakichkolwiek kluczy tajnych skojarzonych z usługą. Większość zasobów można zaktualizować wykonując następujące kroki:

  1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
  2. W menu Centrum wybierz **wszystkie zasoby**.
  3. Wybierz zasób.
  4. Na stronie zasobów kliknij **ustawienia**. W tym miejscu można wyświetlać i aktualizować istniejących kluczy tajnych.

### <a name="if-i-take-over-the-billing-ownership-of-a-subscription-in-the-middle-of-the-billing-cycle-do-i-have-to-pay-for-the-entire-billing-cycle"></a>Jeśli przejąć własność rozliczeń subskrypcji w trakcie okresu rozliczeniowego muszę zapłacić za cały okresu rozliczeniowego?

Twoje konto jest odpowiedzialny za płatności za jakiekolwiek wykorzystanie, które są zgłaszane od momentu przeniesienia i nowszych wersjach. Może to być niektóre użycia, które miały miejsce przed przeniesieniem, ale zostało zgłoszone po tym dniu. Użycie znajduje się Twoje konto rachunku.

### <a name="can-i-use-a-different-payment-method"></a>Można użyć innej metody płatności?

Tak. Podczas akceptowania żądanie przeniesienia, możesz wybrać istniejącą metodę płatności, który jest połączony z Twoim kontem, lub Dodaj nową metodę płatności.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

### <a id="no-button"></a> Dlaczego nie widać przycisku "Przeniesienia subskrypcji"?

Niestety samoobsługowy transfer subskrypcji jest niedostępna dla swojego konta rozliczeniowego. Obecnie nie obsługujemy przeniesienie się własności rozliczeń konto Enterprise Agreement (EA) w witrynie Azure portal. Ponadto kont umowy klienta firmy Microsoft, które są tworzone podczas pracy nad sprzedaży firmy Microsoft nie obsługuje przenoszenia własności rozliczeń subskrypcji. 

### <a id="no-button"></a> Dlaczego Moja subskrypcja typ transferu pomocy technicznej? 

Niestety nie wszystkie typy subskrypcji obsługuje przenoszenie własności rozliczeń. Aby wyświetlić listę typów subskrypcji, które obsługują transferu, zobacz [obsługiwane typy subskrypcji](#supported-subscription-types)

### <a id="no-button"></a> Dlaczego otrzymuję błąd odmowy, gdy próbuję przenieść własność rozliczeń subskrypcji dostępu? 

Zostanie wyświetlony ten błąd, jeśli próbujesz przenieść subskrypcję usługi Microsoft Azure planu, a nie masz niezbędnych uprawnień. Aby przenieść plan subskrypcji Microsoft Azure, musisz być właścicielem lub współautorem sekcji faktury, do którego jest naliczana opłata za subskrypcję. Aby uzyskać więcej informacji, zobacz [Zarządzaj subskrypcjami dla sekcji faktury](billing-understand-mca-roles.md#manage-subscriptions-for-invoice-section).


## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [Utwórz żądanie obsługi](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Następne kroki

- Przejrzyj i zaktualizuj administratora usługi Współadministratorów i innych ról RBAC. Aby dowiedzieć się więcej, zobacz [Administratorzy subskrypcji platformy Azure Dodaj lub zmień](billing-add-change-azure-subscription-administrator.md) i [zarządzanie dostępem przy użyciu RBAC i witryny Azure portal](../role-based-access-control/role-assignments-portal.md).
