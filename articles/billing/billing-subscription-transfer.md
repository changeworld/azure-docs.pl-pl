---
title: Przenieś własność rozliczeń subskrypcji platformy Azure na inne konto | Microsoft Docs
description: Opisuje sposób transferu własności rozliczeń subskrypcji platformy Azure na inne konto, a kilka często zadawanych pytań dotyczących procesu
keywords: Przenieś subskrypcję platformy Azure, subskrypcję usługi Azure transfer, Przenieś subskrypcję platformy Azure na inne konto, na platformie Azure Zmień właściciela subskrypcji, Przenieś subskrypcję platformy Azure na inne konto, rozliczenia transferu na platformie Azure
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
ms.openlocfilehash: 1e4496e2b5d2b21fd878ef68665b8e5b06fa6cc5
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/23/2019
ms.locfileid: "70012539"
---
# <a name="transfer-billing-ownership-of-an-azure-subscription-to-another-account"></a>Przenoszenie własności rozliczeń subskrypcji platformy Azure na inne konto

Możesz chcieć przenieść własność rozliczeń subskrypcji platformy Azure, jeśli opuszczasz organizację lub chcesz, aby Twoja subskrypcja była rozliczana na inne konto. Przeniesienie własności rozliczeń na inne konto zapewnia administratorom uprawnienia nowe konto do wykonywania zadań rozliczeniowych, takich jak zmiana formy płatności, wyświetlanie opłat i anulowanie subskrypcji.

Jeśli chcesz zachować własność rozliczeń, ale zmienić typ subskrypcji, zobacz [przełączanie subskrypcji platformy Azure na inną ofertę](billing-how-to-switch-azure-offer.md). Jeśli chcesz kontrolować, kto może zarządzać zasobami w ramach subskrypcji, zobacz [wbudowane role dla zasobów platformy Azure](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).

Jeśli jesteś klientem z Umowa Enterprise (EA), Administratorzy przedsiębiorstwa mogą przetransferować własność rozliczeń subskrypcji między kontami. Aby uzyskać więcej informacji, zobacz [Transferowanie własności rozliczeń dla subskrypcji Umowa Enterprise (EA)](#transfer-billing-ownership-of-enterprise-agreement-ea-subscriptions).

## <a name="transfer-billing-ownership-of-an-azure-subscription"></a>Przenoszenie własności rozliczeń subskrypcji platformy Azure

1. Zaloguj się do [Azure Portal](https://portal.azure.com) jako administrator konta rozliczeniowego z subskrypcją, którą chcesz przenieść. Aby dowiedzieć się, czy jesteś administratorem, zapoznaj się z [często](#faq)zadawanymi pytaniami.

1. Wyszukiwanie **Cost Management + rozliczenia**.

   ![Zrzut ekranu pokazujący usługi Azure search w portalu](./media/billing-subscription-transfer/billing-search-cost-management-billing.png)

1. Wybierz pozycję **subskrypcje** w okienku po lewej stronie. W zależności od dostępu może być konieczne wybranie zakresu rozliczeń, a następnie wybranie opcji **subskrypcje** lub **subskrypcje platformy Azure**.

1. Wybierz pozycję **Przenieś własność** rozliczeń dla subskrypcji, którą chcesz przenieść. 

   ![Wybierz subskrypcję do przeniesienia](./media/billing-subscription-transfer/billing-select-subscription-to-transfer.png)

1. Wprowadź adres e-mail użytkownika, który jest administratorem rozliczeń konta, który będzie nowym właścicielem subskrypcji.

1. Jeśli przesyłasz subskrypcję do konta w innej dzierżawie usługi Azure AD, wybierz, czy chcesz przenieść subskrypcję do dzierżawy nowego konta. Aby uzyskać więcej informacji, zobacz [Transferowanie subskrypcji do konta w innej dzierżawie usługi Azure AD](#transferring-subscription-to-an-account-in-another-azure-ad-tenant)

    > [!IMPORTANT]
    >
    > Jeśli zdecydujesz się przenieść subskrypcję do dzierżawy usługi Azure AD dla nowego konta, wszystkie przypisania [kontroli dostępu opartej na rolach (RBAC)](../role-based-access-control/overview.md) do zarządzania zasobami w ramach subskrypcji zostaną trwale usunięte. Tylko użytkownik w nowym koncie akceptującym żądanie transferu będzie miał dostęp do zarządzania zasobami w ramach subskrypcji. Aby uzyskać więcej informacji, zobacz [Transferowanie subskrypcji do użytkownika w innej dzierżawie usługi Azure AD](../active-directory/managed-identities-azure-resources/known-issues.md). Alternatywnie możesz usunąć zaznaczenie pola subskrypcji dzierżawy usługi Azure AD w celu przeniesienia własności rozliczeń bez przeniesienia subskrypcji do dzierżawy nowego konta. W takim przypadku istniejące uprawnienia RBAC do zarządzania zasobami platformy Azure będą obsługiwane.
  
    ![Wyślij stronę transferu](./media/billing-subscription-transfer/billing-send-transfer-request.PNG)

1. Wybierz pozycję **Wyślij żądanie przeniesienia**.

1. Użytkownik otrzymuje wiadomość e-mail z instrukcjami dotyczącymi przeglądania żądania transferu.

   ![Wiadomość e-mail dotycząca przeniesienia subskrypcji wysłana do adresata](./media/billing-subscription-transfer/billing-receiver-email.png)

1. Aby zatwierdzić żądanie transferu, użytkownik wybiera link w wiadomości e-mail i postępuje zgodnie z instrukcjami. Użytkownik musi wybrać metodę płatności, która będzie używana do płacenia za subskrypcję. Ponadto, jeśli użytkownik nie ma konta platformy Azure, może być konieczne zarejestrowanie się w celu uzyskania nowego konta. 

   ![Pierwsza strona sieci Web do przeniesienia subskrypcji](./media/billing-subscription-transfer/billing-accept-ownership-step1.png)

   ![Druga strona sieci Web przenoszenia subskrypcji](./media/billing-subscription-transfer/billing-accept-ownership-step2.png)

   ![Druga strona sieci Web przenoszenia subskrypcji](./media/billing-subscription-transfer/billing-accept-ownership-step3.png)

1. To wszystko! Subskrypcja została przeniesiona.

## <a name="transferring-subscription-to-an-account-in-another-azure-ad-tenant"></a>Transferowanie subskrypcji do konta w innej dzierżawie usługi Azure AD

Dzierżawa usługi Azure Active Directory (AD) jest tworzona podczas tworzenia konta na platformie Azure. Dzierżawca reprezentuje Twoje konto. Dzierżawca służy do zarządzania dostępem do Twoich subskrypcji i zasobów.

Podczas tworzenia nowej subskrypcji jest ona hostowana w dzierżawie usługi Azure AD konta. Jeśli chcesz zapewnić innym użytkownikom dostęp do swojej subskrypcji lub jej zasobów, musisz zaprosić ich do wzięcia udziału w dzierżawie. Ułatwia to kontrolowanie dostępu do subskrypcji i zasobów.

Po przeniesieniu własności rozliczeń subskrypcji na konto w innej dzierżawie usługi Azure AD można przenieść subskrypcję do dzierżawy nowego konta. W takim przypadku wszyscy użytkownicy, grupy lub jednostki usługi, którzy mają [dostęp oparty na rolach (RBAC)](../role-based-access-control/role-assignments-portal.md) do zarządzania subskrypcjami i ich zasobami, utracą dostęp. Tylko użytkownik znajdujący się w nowym koncie akceptującym żądanie transferu będzie miał dostęp do zarządzania zasobami. Aby zapewnić dostęp do użytkowników, którzy pierwotnie mieli dostęp, nowy właściciel będzie musiał [ręcznie dodać tych użytkowników do subskrypcji](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal).


## <a name="transferring-visual-studio-mpn-and-pay-as-you-go-devtest-subscriptions"></a>Przenoszenie programu Visual Studio, MPN i płatność zgodnie z rzeczywistym użyciem subskrypcji

Subskrypcje programu Visual Studio i Microsoft Partner Network zawierają comiesięczne, powiązane z nimi środki na korzystanie z platformy Azure. Po przeniesieniu tych subskrypcji środki nie są dostępne na docelowym koncie rozliczeniowym. Subskrypcja używa kredytu w docelowym koncie rozliczeniowym. Jeśli na przykład Robert przekaże subskrypcję Visual Studio Enterprise do konta Janina w dziewiątej września i Janina zaakceptuje transfer. Po zakończeniu transferu subskrypcja zacznie korzystać z kredytu na koncie w firmie Janina. Środki będą resetowane co miesiąc w dniu 9. 


<a id="EA"></a>

## <a name="transfer-billing-ownership-of-enterprise-agreement-ea-subscriptions"></a>Przenieś własność rozliczeń dla subskrypcji Umowa Enterprise (EA)

Administrator przedsiębiorstwa może przenosić własność subskrypcji między kontami w ramach rejestracji. Aby uzyskać więcej informacji, zobacz [Transferowanie własności konta](https://ea.azure.com/helpdocs/changeAccountOwnerForASubscription) w portalu EA.

## <a name="next-steps-after-accepting-billing-ownership"></a>Następne kroki po przyjęciu własności rozliczeń

Jeśli zaakceptujesz własność rozliczeń subskrypcji platformy Azure, zalecamy zapoznanie się z następującymi krokami:

1. Przejrzyj i zaktualizuj rolę Administrator usługi, współadministratorzy i inne role RBAC. Aby dowiedzieć się więcej, zobacz [Dodawanie lub zmienianie administratorów subskrypcji platformy Azure](billing-add-change-azure-subscription-administrator.md) oraz [Zarządzanie dostępem przy użyciu RBAC i Azure Portal](../role-based-access-control/role-assignments-portal.md).
1. Zaktualizuj poświadczenia skojarzone z usługami tej subskrypcji, w tym:
   1. Certyfikaty zarządzania, które przyznają użytkownikowi uprawnienia administratora do zasobów subskrypcji. Aby uzyskać więcej informacji, zobacz [Tworzenie i przekazywanie certyfikatu zarządzania dla platformy Azure](../cloud-services/cloud-services-certs-create.md)
   1. Klucze dostępu dla usług takich jak Storage. Aby uzyskać więcej informacji, zobacz [Informacje o kontach usługi Azure Storage](../storage/common/storage-create-storage-account.md)
   1. Poświadczenia dostępu zdalnego dla usług takich jak Azure Virtual Machines.
1. Jeśli pracujesz z partnerem, rozważ zaktualizowanie identyfikatora partnera w ramach tej subskrypcji. Identyfikator partnera można zaktualizować w [Azure Portal](https://portal.azure.com). Aby uzyskać więcej informacji, zobacz [łączenie identyfikatora partnera z kontami platformy Azure](billing-partner-admin-link-started.md)

<a id="supported"></a>

## <a name="supported-subscription-types"></a>Obsługiwane typy subskrypcji

Transfer subskrypcji w Azure Portal jest dostępny dla typów subskrypcji wymienionych poniżej. Obecnie transfer nie jest obsługiwany w przypadku [bezpłatnej wersji próbnej](https://azure.microsoft.com/offers/ms-azr-0044p/) ani subskrypcji [platformy Azure w ramach programu Open (AIO)](https://azure.microsoft.com/offers/ms-azr-0111p/) . Obejście tego problemu można znaleźć w temacie [przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](../azure-resource-manager/resource-group-move-resources.md). Aby przenieść inne subskrypcje, takie [](https://azure.microsoft.com/offers/ms-azr-0036p/) jak sponsorowanie lub plany pomocy technicznej, [skontaktuj się z pomocą techniczną platformy Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

- [Umowa Enterprise (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)\*
- [Microsoft Partner Network](https://azure.microsoft.com/offers/ms-azr-0025p/)  
- [Subskrybenci Visual Studio Enterprise (MPN)](https://azure.microsoft.com/offers/ms-azr-0029p/) 
- [Platformy MSDN](https://azure.microsoft.com/offers/ms-azr-0062p/)  
- [Pay-As-You-Go](https://azure.microsoft.com/offers/ms-azr-0003p/) 
- [Płatność zgodnie z rzeczywistym użyciem — tworzenie i testowanie](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/)
- [Visual Studio Enterprise: BizSpark](https://azure.microsoft.com/offers/ms-azr-0064p/)
- [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/)
- [Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/)
- [Plan Microsoft Azure](https://azure.microsoft.com/offers/ms-azr-0017g/)\*\*

\*[Za pośrednictwem portalu EA](#EA).

\*\*Obsługiwane tylko w przypadku kont utworzonych podczas tworzenia konta w witrynie sieci Web systemu Azure. 

<a id="faq"></a>

## <a name="frequently-asked-questions-faq-for-senders"></a>Często zadawane pytania dotyczące nadawców

Te często zadawane pytania dotyczą użytkowników, którzy przesyłają własność rozliczeń subskrypcji platformy Azure na inne konto.

### <a name="whoisaa"></a>Kto jest administratorem rozliczeń konta?

Administrator rozliczeń to osoba, która ma uprawnienia do zarządzania rozliczeniami dla konta. Są one autoryzowane do uzyskiwania dostępu do rozliczeń na [Azure Portal](https://portal.azure.com) i wykonywania różnych zadań rozliczeniowych, takich jak tworzenie subskrypcji, wyświetlanie i płacenie faktur lub aktualizowanie metod płatności.

Aby zidentyfikować konta, dla których jesteś administratorem rozliczeń, wykonaj następujące czynności:

1. Odwiedź [stronę Rozliczanie Cost Management i rozliczenia w Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/Overview).
1. Zaznacz **wszystkie zakresy rozliczeniowe** w okienku po lewej stronie. 
1. Na stronie Subskrypcje są wyświetlane wszystkie subskrypcje, dla których jesteś administratorem rozliczeń.

### <a name="does-everything-transfer-including-resource-groups-vms-disks-and-other-running-services"></a>Czy wszystko jest transferowane? W tym grupy zasobów, maszyny wirtualne, dyski i inne uruchomione usługi?

Wszystkie zasoby, takie jak maszyny wirtualne, dyski i witryny sieci Web, są transferowane do nowego konta. Jednak w przypadku przeniesienia subskrypcji na konto w innej dzierżawie usługi Azure AD [nie są transferowane](#transferring-subscription-to-an-account-in-another-azure-ad-tenant)żadne [role administratora](billing-add-change-azure-subscription-administrator.md) i przypisania [Access Control oparte na rolach (RBAC)](../role-based-access-control/role-assignments-portal.md) w subskrypcji. Ponadto [rejestracje aplikacji](../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md) i inne usługi specyficzne dla dzierżawców nie są transferowane razem z subskrypcją.

### <a name="can-i-transfer-ownership-to-an-account-in-another-country"></a>Czy mogę przenieść własność do konta w innym kraju?
Niestety, nie można wykonywać transferów między krajami w Azure Portal. Aby przenieść swoją subskrypcję między krajami, [skontaktuj się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="i-am-an-administrator-on-two-accounts-can-i-transfer-a-subscription-from-one-of-my-accounts-to-another"></a>Jestem administratorem na dwóch kontach. Czy mogę przenieść subskrypcję z jednego konta do innego?
Tak, możesz przetransferować subskrypcję między kontami. Twoje konta są koncepcyjnie uznawane za konta dwóch różnych użytkowników, dzięki czemu można wykonać powyższe kroki w celu przetransferowania subskrypcji między kontami.

### <a name="does-a-subscription-transfer-result-in-any-service-downtime"></a>Czy transfer subskrypcji skutkuje ewentualnym przestojem usługi?

Jeśli subskrypcja zostanie przetransferowana do konta w tej samej dzierżawie usługi Azure AD, nie ma to wpływu na zasoby działające w ramach subskrypcji. Jeśli jednak przeniesiesz subskrypcję do konta w innej dzierżawie i zdecydujesz się przenieść subskrypcję do dzierżawy, wszyscy użytkownicy, grupy i jednostki usługi, którzy mają [dostęp oparty na rolach (RBAC)](../role-based-access-control/overview.md) do zarządzania zasobami w ramach subskrypcji, tracą dostęp . Może to skutkować przestojem usługi.

### <a name="do-users-in-new-account-have-access-to-usage-and-billing-history"></a>Czy użytkownicy na nowym koncie mają dostęp do historii użytkowania i rozliczeń?

Jedyne dostępne informacje dla użytkowników w nowym koncie to koszt ostatniego miesiąca dla Twojej subskrypcji. Pozostała część historii użytkowania i rozliczeń nie jest przesyłana razem z subskrypcją

### <a name="how-do-i-migrate-data-and-services-for-my-azure-subscription-to-new-subscription"></a>Jak mogę przeprowadzić migrację danych i usług dla mojej subskrypcji platformy Azure do nowej subskrypcji?

Jeśli nie możesz przenieść własności subskrypcji, możesz ręcznie migrować zasoby. Zobacz [przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](../azure-resource-manager/resource-group-move-resources.md).

### <a name="if-i-transfer-a-visual-studio-or-microsoft-partner-network-subscription-does-my-credit-carry-forward-with-the-subscription-in-the-new-account"></a>Czy Jeśli przeniesiesz subskrypcję programu Visual Studio lub Microsoft Partner Network, czy moje środki są przenoszone do przodu w ramach subskrypcji na nowym koncie?

Nie, Twoje środki nie są dostępne na nowym koncie. Użytkownik akceptujący żądanie przeniesienia musi mieć licencję programu Visual Studio, aby zaakceptować żądanie transferu. Subskrypcja korzysta z kredytu programu Visual Studio, który jest dostępny na koncie użytkownika. Aby uzyskać więcej informacji, zobacz [Transferowanie programu Visual Studio, Microsoft Partner Network (MPN) i płatność zgodnie z rzeczywistym](#transferring-visual-studio-mpn-and-pay-as-you-go-devtest-subscriptions)użyciem.


## <a name="frequently-asked-questions-faq-for-recipients"></a>Często zadawane pytania dotyczące adresatów

Te często zadawane pytania dotyczą użytkowników, którzy akceptują własność rozliczeń subskrypcji platformy Azure z innego konta.

### <a name="if-i-take-over-billing-ownership-of-a-subscription-from-another-account-do-users-in-that-account-continue-to-have-access-to-my-resources"></a>Jeśli przejdziesz na własność rozliczeń subskrypcji z innego konta, czy użytkownicy tego konta nadal mają dostęp do moich zasobów?

Tak. Jeśli jednak Twoje konto znajduje się w dzierżawie usługi Azure AD, która różni się od dzierżawy subskrypcji, a użytkownik, który wysłał żądanie transferu, przenosi subskrypcję do dzierżawy konta, wszystkie [role administratorów](billing-add-change-azure-subscription-administrator.md) i [Access Control oparte na rolach (RBAC) ](../role-based-access-control/role-assignments-portal.md)przypisania są usuwane. Aby wyświetlić użytkowników, którzy mają dostęp [oparty na rolach (RBAC)](../role-based-access-control/overview.md) do zarządzania zasobami w ramach subskrypcji, wykonaj następujące czynności:

1. Odwiedź [stronę subskrypcji w Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Wybierz subskrypcję, którą chcesz sprawdzić, a następnie wybierz pozycję **Kontrola dostępu (IAM)** w okienku po lewej stronie.
1. Wybierz pozycję **przypisania ról** w górnej części strony. Na stronie przydziały ról są wyświetlane wszyscy użytkownicy, którzy mają dostęp do usługi RBAC w ramach subskrypcji.

Nawet jeśli przypisania [Access Control oparte na rolach (RBAC)](../role-based-access-control/role-assignments-portal.md) zostaną usunięte podczas transferu, użytkownicy z oryginalnego konta właściciela mogą nadal mieć dostęp do subskrypcji za pośrednictwem niektórych mechanizmów zabezpieczeń, w tym:

* Certyfikaty zarządzania, które przyznają użytkownikowi uprawnienia administratora do zasobów subskrypcji. Aby uzyskać więcej informacji, zobacz [Tworzenie i przekazywanie certyfikatu zarządzania dla platformy Azure](../cloud-services/cloud-services-certs-create.md).
* Klucze dostępu dla usług takich jak Storage. Aby uzyskać więcej informacji, zobacz [Informacje o kontach usługi Azure Storage](../storage/common/storage-create-storage-account.md).
* Poświadczenia dostępu zdalnego dla usług takich jak Azure Virtual Machines.

Jeśli odbiorca musi ograniczyć dostęp do swoich zasobów, powinien rozważyć zaktualizowanie wszelkich wpisów tajnych skojarzonych z usługą. Większość zasobów można zaktualizować, wykonując następujące czynności:

  1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
  2. W menu centrum wybierz pozycję **wszystkie zasoby**.
  3. Wybierz zasób.
  4. Na stronie zasób kliknij pozycję **Ustawienia**. W tym miejscu możesz wyświetlać i aktualizować istniejące wpisy tajne.

### <a name="if-i-take-over-the-billing-ownership-of-a-subscription-in-the-middle-of-the-billing-cycle-do-i-have-to-pay-for-the-entire-billing-cycle"></a>Czy w przypadku przejęcia praw własności do rozliczeń w ramach cyklu rozliczeniowego należy uiścić płatność za cały cykl rozliczeniowy?

Twoje konto jest odpowiedzialne za płatność za użycie zgłoszoną od momentu przeniesienia. Może istnieć pewne użycie, które miało miejsce przed przekazaniem, ale zostało zgłoszone później. Użycie jest uwzględniane na rachunku konta.

### <a name="can-i-use-a-different-payment-method"></a>Czy mogę użyć innej formy płatności?

Tak. Podczas akceptowania żądania przeniesienia możesz wybrać istniejącą metodę płatności, która jest połączona z kontem lub dodać nową metodę płatności.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

### <a id="no-button"></a>Dlaczego nie widzę przycisku "Przenieś subskrypcję"?

Samoobsługowy transfer subskrypcji nie jest dostępny dla Twojego konta rozliczeniowego. Obecnie nie obsługujemy przenoszenia własności rozliczeń subskrypcji w ramach kont Umowa Enterprise (EA) w Azure Portal. Ponadto konta umów dla klientów firmy Microsoft, które są tworzone podczas pracy z przedstawicielem firmy Microsoft, nie obsługują przenoszenia własności rozliczeń. 

### <a id="no-button"></a>Dlaczego mój typ subskrypcji nie obsługuje transferu? 

Nie wszystkie typy subskrypcji obsługują przenoszenie własności rozliczeń. Aby wyświetlić listę typów subskrypcji obsługujących transfery, zobacz [obsługiwane typy subskrypcji](#supported-subscription-types)

### <a id="no-button"></a>Dlaczego otrzymuję komunikat o błędzie odmowy dostępu podczas próby przetransferowania własności rozliczeń subskrypcji? 

Ten błąd zostanie wyświetlony, jeśli próbujesz przenieść subskrypcję planu Microsoft Azure i nie masz wymaganych uprawnień. Aby przetransferować subskrypcję planu Microsoft Azure, musisz być właścicielem lub współautorem sekcji faktury, w której jest rozliczana subskrypcja. Aby uzyskać więcej informacji, zobacz [sekcję Zarządzanie subskrypcjami dla faktury](billing-understand-mca-roles.md#manage-subscriptions-for-invoice-section).


## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [Utwórz żądanie pomocy technicznej](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Następne kroki

- Przejrzyj i zaktualizuj rolę Administrator usługi, współadministratorzy i inne role RBAC. Aby dowiedzieć się więcej, zobacz [Dodawanie lub zmienianie administratorów subskrypcji platformy Azure](billing-add-change-azure-subscription-administrator.md) oraz [Zarządzanie dostępem przy użyciu RBAC i Azure Portal](../role-based-access-control/role-assignments-portal.md).
