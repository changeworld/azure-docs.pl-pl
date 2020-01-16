---
title: Uzyskiwanie własności rozliczeń subskrypcji platformy Azure
description: Dowiedz się, jak uzyskiwać własność rozliczeń subskrypcji platformy Azure od innych użytkowników.
author: amberbhargava
manager: amberb
editor: banders
tags: billing
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: f7d6c6cbe5b99cb429b5399df7ba9765c1553901
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75991115"
---
# <a name="get-billing-ownership-of-azure-subscriptions-from-other-accounts"></a>Uzyskiwanie własności rozliczeń subskrypcji platformy Azure z innych kont

Możesz zdecydować się przejąć własność subskrypcji platformy Azure, jeśli istniejący właściciel rozliczeń opuszcza organizację lub jeśli chcesz uregulować płatność za subskrypcje za pośrednictwem konta rozliczeniowego. Przejęcie własności przenosi obowiązki dotyczące rozliczeń subskrypcji na konto.

Ten artykuł dotyczy konta rozliczeniowego związanego z umową klienta firmy Microsoft. [Sprawdź, czy masz dostęp do umowy klienta firmy Microsoft](#check-for-access).

Aby zażądać własności rozliczeń, musisz być **właścicielem sekcji faktury** lub **współautorem sekcji faktury**. Aby dowiedzieć się więcej zobacz sekcję dotyczącą [zadań ról sekcji faktury](understand-mca-roles.md#invoice-section-roles-and-tasks).

## <a name="request-billing-ownership"></a>Żądanie własności rozliczeń

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) jako właściciel lub współautor sekcji faktury dla konta rozliczeniowego w ramach umowy klienta firmy Microsoft.

2. Wyszukaj pozycję **Zarządzanie kosztami i rozliczenia**.

    ![Zrzut ekranu przedstawiający wyszukiwanie zarządzania kosztami i rozliczeń w witrynie Azure Portal](./media/mca-request-billing-ownership/billing-search-cost-management-billing.png)

3. Na stronie zakresy rozliczeń wybierz konto rozliczeniowe, które będzie używane do płacenia za użycie subskrypcji. Konto rozliczeniowe powinno być kontem typu **Umowa klienta firmy Microsoft**.

    ![Zrzut ekranu przedstawiający wyszukiwanie zarządzania kosztami i rozliczeń w portalu](./media/mca-request-billing-ownership/list-of-scopes.png)

    > [!NOTE]
    >
    > Azure Portal zapamiętuje ostatni zakres rozliczeń, do którego uzyskuje się dostęp, i wyświetla zakres przy następnym Cost Managementniu i stronie rozliczeń. Strona zakresy rozliczeń nie zostanie wyświetlona, Jeśli odwiedzono Cost Management i rozliczenia wcześniej. Jeśli tak jest, sprawdź, czy jesteś w [odpowiednim zakresie](#check-for-access). Jeśli nie, [Przejdź do zakresu](view-all-accounts.md#switch-billing-scope-in-the-azure-portal) , aby wybrać konto rozliczeniowe dla umowy klienta firmy Microsoft.

4. Wybierz pozycję **Profile rozliczeń** z lewej strony.

    ![Zrzut ekranu pokazujący Wybieranie profilów rozliczeń](./media/mca-request-billing-ownership/mca-select-profiles.png)     

    > [!Note]
    >
    > Jeśli nie widzisz profilów rozliczeń, nie jesteś w odpowiednim zakresie rozliczeń. Musisz wybrać konto rozliczeniowe dla umowy klienta firmy Microsoft, a następnie wybrać pozycję profile rozliczeń. Aby dowiedzieć się, jak zmienić zakresy, zobacz [Przełączanie zakresów rozliczeń w Azure Portal](view-all-accounts.md#switch-billing-scope-in-the-azure-portal).

5. Wybierz **profil rozliczeń** z listy. Po przeniesieniu własności subskrypcji ich użycie będzie rozliczane w ramach tego profilu rozliczeń.

6. Wybierz pozycję **Sekcje faktury** po lewej stronie.

    ![Zrzut ekranu przedstawiający wybieranie sekcji faktury](./media/mca-request-billing-ownership/mca-select-invoice-sections.png)   

7. Wybierz sekcję faktur z listy. Po przeniesieniu własności subskrypcji ich użycie zostanie przypisane do tej sekcji faktury profilu rozliczenia.

8. Wybierz pozycję **Żądania przeniesienia** w lewym dolnym rogu, a następnie wybierz pozycję **Dodaj nowe żądanie**.

    ![Zrzut ekranu przedstawiający wybieranie żądań przeniesienia](./media/mca-request-billing-ownership/mca-select-transfer-requests.png)

9. Wprowadź adres e-mail użytkownika, od którego żądasz własności rozliczeń. Użytkownik musi być administratorem konta w ramach konta rozliczeń programu Microsoft Online Service lub właścicielem konta w umowie Enterprise Agreement. Aby uzyskać więcej informacji, zobacz [View your billing accounts in Azure portal](view-all-accounts.md) (Wyświetlanie kont rozliczeniowych w witrynie Azure Portal). Wybierz opcję **Wyślij żądanie przeniesienia**.

    ![Zrzut ekranu przedstawiający wysyłanie żądania przeniesienia](./media/mca-request-billing-ownership/mca-send-transfer-requests.png)

10. Użytkownik otrzymuje wiadomość e-mail z instrukcjami dotyczącymi przeglądania żądania transferu.

    ![Zrzut ekranu przedstawiający wiadomość e-mail z prośbą o przejrzenie żądania przeniesienia](./media/mca-request-billing-ownership/mca-review-transfer-request-email.png)

11. Aby zatwierdzić żądanie przeniesienia, użytkownik wybiera link w wiadomości e-mail i postępuje zgodnie z instrukcjami.

    ![Zrzut ekranu przedstawiający wiadomość e-mail z prośbą o przejrzenie żądania przeniesienia](./media/mca-request-billing-ownership/mca-review-transfer-request.png)

## <a name="check-the-transfer-request-status"></a>Sprawdzanie stanu żądania przeniesienia

1. Zaloguj się do [portalu Azure](https://portal.azure.com).

2. Wyszukaj pozycję **Zarządzanie kosztami i rozliczenia**.

    ![Zrzut ekranu przedstawiający wyszukiwanie zarządzania kosztami i rozliczeń w witrynie Azure Portal](./media/mca-request-billing-ownership/billing-search-cost-management-billing.png)

3. Na stronie zakresy rozliczeń wybierz konto rozliczeniowe, dla którego wysłano żądanie transferu.

4. Wybierz pozycję **Profile rozliczeń** z lewej strony.

    ![Zrzut ekranu pokazujący Wybieranie profilów rozliczeń](./media/mca-request-billing-ownership/mca-select-profiles.png)     

5. Wybierz **profil rozliczeń** , dla którego wysłano żądanie transferu.

6. Wybierz pozycję **Sekcje faktury** po lewej stronie.

    ![Zrzut ekranu przedstawiający wybieranie sekcji faktury](./media/mca-request-billing-ownership/mca-select-invoice-sections.png)   

7. Wybierz z listy sekcję fakturę, dla której wysłano żądanie transferu.

8. Wybierz pozycję **Żądania przeniesienia** w lewym dolnym rogu. Strona Żądania przeniesienia zawiera następujące informacje:

    ![Zrzut ekranu przedstawiający listę żądań przeniesienia](./media/mca-request-billing-ownership/mca-select-transfer-requests-for-status.png)

   |Kolumna|Definicja|
   |---------|---------|
   |Data żądania|Data wysłania żądania przeniesienia|
   |Adresat|Adres e-mail użytkownika, do którego wysłano żądanie przeniesienia własności rozliczeń|
   |Data wygaśnięcia|Data wygaśnięcia żądania|
   |Stan|Stan żądania przeniesienia|

    Żądanie przeniesienia może mieć jeden z następujących stanów:

   |Stan|Definicja|
   |---------|---------|
   |W toku|Użytkownik nie zaakceptował żądania przeniesienia|
   |Przetwarzanie|Użytkownik zatwierdził żądanie przeniesienia. Rozliczanie subskrypcji wybranych przez użytkownika jest przenoszone do sekcji faktury|
   |Zakończone| Rozliczanie subskrypcji wybranych przez użytkownika jest przenoszone do sekcji faktury|
   |Zakończone z błędami|Żądanie zostało zakończone, ale nie można było przenieść rozliczeń dla niektórych subskrypcji wybranych przez użytkownika|
   |Wygasłe|Użytkownik nie zaakceptował żądania na czas i żądanie wygasło|
   |Anulowane|Ktoś z dostępem do żądania przeniesienia anulował żądanie|
   |Odrzucone|Użytkownik odrzucił żądanie przeniesienia|

9. Wybierz żądanie przeniesienia, aby wyświetlić szczegóły. Strona szczegółów przeniesienia zawiera następujące informacje:

    ![Zrzut ekranu przedstawiający listę przeniesionych subskrypcji](./media/mca-request-billing-ownership/mca-transfer-completed.png)

   |Kolumna  |Definicja|
   |---------|---------|
   |Identyfikator żądania przeniesienia|Unikatowy identyfikator żądania przeniesienia. Jeśli przesyłasz wniosek o pomoc techniczną, podaj ten identyfikator, aby przyspieszyć obsługę wniosku przez zespół pomocy technicznej platformy Azure|
   |Przeniesienia zażądano w dniu|Data wysłania żądania przeniesienia|
   |Żądanie przeniesienia zgłoszone przez|Adres e-mail użytkownika, który wysłał żądanie przeniesienia|
   |Żądanie przeniesienia wygasa| Data wygaśnięcia żądania przeniesienia|
   |Adres e-mail adresata|Adres e-mail użytkownika, do którego wysłano żądanie przeniesienia własności rozliczeń|
   |Link do przenoszenia został wysłany do adresata|Adres URL, który został wysłany do użytkownika w celu przejrzenia żądania przeniesienia|

## <a name="supported-subscription-types"></a>Obsługiwane typy subskrypcji

Możesz zażądać własności rozliczeń wymienionych poniżej typów subskrypcji.

- [Action Pack](https://azure.microsoft.com/offers/ms-azr-0025p/)\*
- [Azure w ramach programu licencjonowania Open](https://azure.microsoft.com/offers/ms-azr-0111p/)\*
- [Azure — dostęp próbny (sponsorowany)](https://azure.microsoft.com/offers/azure-pass/)\*
- [Enterprise — tworzenie i testowanie](https://azure.microsoft.com/offers/ms-azr-0148p/)
- [Bezpłatna wersja próbna](https://azure.microsoft.com/offers/ms-azr-0044p/)\*
- [Płatność zgodnie z rzeczywistym użyciem](https://azure.microsoft.com/offers/ms-azr-0003p/)
- [Płatność zgodnie z rzeczywistym użyciem — tworzenie i testowanie](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Plan platformy Microsoft Azure](https://azure.microsoft.com/offers/ms-azr-0017g/)\*\*
- [Oferta sponsorowana platformy Microsoft Azure](https://azure.microsoft.com/offers/ms-azr-0036p/)\*
- [Microsoft Enterprise Agreement](https://azure.microsoft.com/pricing/enterprise-agreement/)
- [Microsoft Partner Network](https://azure.microsoft.com/offers/ms-azr-0025p/)\*
- [Platformy MSDN](https://azure.microsoft.com/offers/ms-azr-0062p/)\*
- [Subskrybenci programu Visual Studio Enterprise (BizSpark)](https://azure.microsoft.com/offers/ms-azr-0064p/)\*
- [Subskrybenci programu Visual Studio Enterprise (MPN)](https://azure.microsoft.com/offers/ms-azr-0029p/)\*
- [Subskrybenci programu Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/)\*
- [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/)\*
- [Subskrybenci programu Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/)\*

\* Wszelkie środki dostępne w ramach subskrypcji nie będą dostępne na nowym koncie po przeniesieniu.

\*\* Obsługiwane tylko w przypadku subskrypcji na kontach utworzonych podczas tworzenia konta w witrynie internetowej platformy Azure.


## <a name="additional-information"></a>Dodatkowe informacje

W poniższej sekcji przedstawiono dodatkowe informacje na temat przenoszenia subskrypcji.

### <a name="no-service-downtime"></a>Brak przestojów usługi

Usługi platformy Azure w ramach subskrypcji będą działały bez jakiejkolwiek przerwy. W przypadku subskrypcji platformy Azure, które użytkownik wybierze do przeniesienia, przeniesiemy tylko relację rozliczeń.

### <a name="disabled-subscriptions"></a>Wyłączone subskrypcje

Wyłączonych subskrypcji nie można przenosić. Aby przeniesienie własności rozliczeń było możliwe, subskrypcje muszą być w stanie aktywnym.

### <a name="azure-resources-transfer"></a>Przenoszenie zasobów platformy Azure

Wszystkie zasoby z subskrypcji, takie jak maszyny wirtualne, dyski i witryny internetowe, są przenoszone.

### <a name="azure-marketplace-products-transfer"></a>Przenoszenie produktów z witryny Azure Marketplace

Produkty z witryny Azure Marketplace są przenoszone wraz z odpowiednimi subskrypcjami.

### <a name="azure-reservations-transfer"></a>Przenoszenie rezerwacji platformy Azure

Rezerwacje platformy Azure nie są automatycznie przenoszone wraz z subskrypcjami. [Skontaktuj się z działem pomocy technicznej platformy Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), aby przenieść rezerwacje.

### <a name="access-to-azure-services"></a>Dostęp do usług platformy Azure

Przejście nie wpływa na dostęp do istniejących użytkowników, grup lub jednostek usługi, które zostały przypisane przy użyciu usługi (Azure RBAC (kontroli dostępu opartej na rolach))[../role-based-access-control/overview.md].

### <a name="azure-support-plan"></a>Plan pomocy technicznej platformy Azure

Pomoc techniczna platformy Azure nie jest przenoszona razem z subskrypcjami. Jeśli użytkownik przeniesie wszystkie subskrypcje platformy Azure, poproś go o anulowanie planu pomocy technicznej.

### <a name="charges-for-transferred-subscription"></a>Opłaty za przeniesioną subskrypcję

Oryginalny właściciel rozliczeń subskrypcji jest odpowiedzialny za wszelkie opłaty, które zostały zgłoszone do momentu ukończenia przenoszenia. Sekcja faktury jest odpowiedzialna za opłaty zgłoszone od momentu przeniesienia. Pewne opłaty, które miały miejsce przed przeniesieniem, mogły zostać zgłoszona dopiero po nim. Te opłaty są wyświetlane w sekcji faktury.

### <a name="cancel-a-transfer-request"></a>Anulowanie żądania przeniesienia

Żądanie przeniesienia można anulować, dopóki nie zostanie zatwierdzone lub odrzucone. Aby anulować żądanie przeniesienia, przejdź do [strony szczegółów przeniesienia](#check-the-transfer-request-status) i wybierz pozycję Anuluj w dolnej części strony.

### <a name="software-as-a-service-saas-transfer"></a>Przenoszenie oprogramowania jako usługi (SaaS)

Produkty SaaS nie są przenoszone razem z subskrypcjami. Poproś użytkownika o [skontaktowanie się z pomocą techniczną platformy Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) w celu przeniesienia własności rozliczeń produktów SaaS. Oprócz własności rozliczeń użytkownik może również przenieść własność zasobów. Własność zasobów umożliwia wykonywanie operacji zarządzania, takich jak usuwanie i wyświetlanie szczegółów produktu. Użytkownik musi być właścicielem zasobu w ramach produktu SaaS, aby przenieść własność zasobów.

## <a name="check-for-access"></a>Sprawdzanie dostępu
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Potrzebujesz pomocy? Kontakt z pomocą techniczną

Jeśli potrzebujesz pomocy, [skontaktuj się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), aby szybko rozwiązać problem.

## <a name="next-steps"></a>Następne kroki

- Własność rozliczeń subskrypcji platformy Azure została przeniesiona do sekcji faktury. Śledź opłaty za te subskrypcje w witrynie [Azure Portal](https://portal.azure.com).
- Przyznaj innym osobom uprawnienia do wyświetlania rozliczeń i zarządzania nimi w ramach tych subskrypcji. Aby uzyskać więcej informacji, zobacz sekcję dotyczącą [zadań i ról sekcji faktur](understand-mca-roles.md#invoice-section-roles-and-tasks).
