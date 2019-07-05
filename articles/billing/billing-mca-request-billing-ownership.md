---
title: Pobierz rozliczeń własności subskrypcji platformy Azure
description: Dowiedz się, jak żądania własność rozliczeń subskrypcji platformy Azure od innych użytkowników.
author: amberbhargava
manager: amberb
editor: banders
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: 4c9a8c866a157757121e6a9dd07a0a8559937c5e
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490807"
---
# <a name="get-billing-ownership-of-azure-subscriptions-from-other-accounts"></a>Pobierz rozliczeń własności subskrypcji platformy Azure z innych kont

Możesz chcieć przejąć własności subskrypcji platformy Azure, jeśli istniejące właściciela rozliczeń opuszcza organizacji lub chcesz zapłacić za subskrypcje za pośrednictwem konta rozliczeniowego. Przejmowanie na własność przesyła obowiązki rozliczeń subskrypcji na koncie.

Ten artykuł dotyczy konta rozliczeniowego dla umowy klienta firmy Microsoft. [Sprawdź, czy dostęp do umowy klienta Microsoft](#check-for-access).

Aby zażądać własność rozliczeń, musi być **właściciel sekcji faktury** lub **Współautor sekcji faktury**. Aby dowiedzieć się więcej, zobacz [faktury sekcji role zadania](billing-understand-mca-roles.md#invoice-section-roles-and-tasks).

## <a name="request-billing-ownership"></a>Żądanie własność rozliczeń

1. Zaloguj się do [witryny Azure portal](http://portal.azure.com) jako faktura sekcji właściciel lub Współautor dla konta rozliczeniowego dla umowy klienta firmy Microsoft.

2. Wyszukaj **Cost Management + rozliczenia**.

   ![Zrzut ekranu pokazujący Azure portal Wyszukaj Zarządzanie kosztami i rozliczenia](./media/billing-mca-request-billing-ownership/billing-search-cost-management-billing.png)

3. Wybierz **faktury sekcje** z po lewej stronie. W zależności od dostęp może być konieczne wybierz konto rozliczeniowe lub profil rozliczeniowy. Z konta fakturowania i profilu, wybierz **faktury sekcje**.
   
   ![Zrzut ekranu przedstawiający Wybieranie faktury sekcje](./media/billing-mca-request-billing-ownership/mca-select-invoice-sections.png)        

4. Wybierz sekcję faktury na liście. Po przejęciu własności subskrypcji będą naliczane w ramach tej sekcji faktury.

5. Wybierz **żądań przeniesienia** z lewej dolnej części, a następnie wybierz **Dodaj**.
 
   ![Zrzut ekranu przedstawia wybierając liczbę żądań transferu](./media/billing-mca-request-billing-ownership/mca-select-transfer-requests.png)

6. Wprowadź adres e-mail użytkownika, którego jesteś żądanie własność rozliczeń z. Użytkownik musi być administratorem konta na konto rozliczeniowe programu usług Microsoft Online Service lub właściciel konta w ramach umowy Enterprise Agreement. Aby uzyskać więcej informacji, zobacz [wyświetlić konta rozliczeniowego w witrynie Azure portal](billing-view-all-accounts.md). Wybierz **Wyślij żądanie przeniesienia**.

   ![Zrzut ekranu pokazujący, wysyłając żądanie transferu](./media/billing-mca-request-billing-ownership/mca-send-transfer-requests.png)

7. Użytkownik otrzymuje wiadomość e-mail z instrukcjami, aby zapoznać się z Twoje żądanie przeniesienia.

   ![Zrzut ekranu tego przedstawia przegląd transferu wiadomość e-mail z żądaniem](./media/billing-mca-request-billing-ownership/mca-review-transfer-request-email.png)

8. Aby zatwierdzić żądanie przeniesienia, użytkownik wybierze link w wiadomości e-mail i instrukcje.

    ![Zrzut ekranu tego przedstawia przegląd transferu wiadomość e-mail z żądaniem](./media/billing-mca-request-billing-ownership/mca-review-transfer-request.png)

## <a name="check-the-transfer-request-status"></a>Sprawdź stan żądania przeniesienia

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. Wyszukaj **Cost Management + rozliczenia**.

   ![Zrzut ekranu pokazujący Azure portal Wyszukaj Zarządzanie kosztami i rozliczenia](./media/billing-mca-request-billing-ownership/billing-search-cost-management-billing.png)


3. Wybierz **faktury sekcje** z po lewej stronie. W zależności od dostęp może być konieczne wybierz konto rozliczeniowe lub profil rozliczeniowy. Z konta fakturowania i profilu, wybierz **faktury sekcje**.
   
   ![Zrzut ekranu przedstawiający Wybieranie faktury sekcje](./media/billing-mca-request-billing-ownership/mca-select-invoice-sections.png)        

4. Wybierz sekcję faktury na liście, dla której wysyłane jest żądanie przeniesienia.

5. Wybierz **żądań przeniesienia** po stronie lewej dolnej. Strona żądań transferu zawiera następujące informacje:

    ![Zrzut ekranu przedstawiający listę żądań transferu](./media/billing-mca-request-billing-ownership/mca-select-transfer-requests-for-status.png)

   |Kolumna|Definicja|
   |---------|---------|
   |Data żądania|Data wysłania żądania przeniesienia|
   |Odbiorcy|Adres e-mail użytkownika, które zostały wysłane żądanie, aby przenieść własność rozliczeń|
   |Data wygaśnięcia|Data wygaśnięcia żądania|
   |Stan|Stan żądania przeniesienia|

    Żądanie przeniesienia może mieć jedną z następujących stanów:

   |Stan|Definicja|
   |---------|---------|
   |W toku|Użytkownik nie zaakceptował żądania przeniesienia|
   |Przetwarzanie|Użytkownik zatwierdził żądanie przeniesienia. Rozliczeń dla subskrypcji, dla których użytkownik wybrał przesyłaną wprowadzenie do sekcji faktury|
   |Ukończone| Naliczania opłat za subskrypcje wybrane przez użytkownika jest przekazywany do sekcji faktury|
   |Zakończone z błędami|Żądanie zostało ukończone, ale rozliczanie dla niektórych subskrypcji, że nie można przenosić wybranego użytkownika|
   |Wygaśnięcie|Użytkownik nie zaakceptował żądania na godzinę i wygasła.|
   |Anulowane|Dostęp do żądanie przeniesienia zostało anulowane żądania|
   |Odrzucone|Użytkownik odrzucił żądanie przeniesienia|

7. Wybierz żądanie przeniesienia, aby wyświetlić szczegóły. Na stronie szczegółów transferu zawiera następujące informacje:
   
   ![Zrzut ekranu przedstawiający listę przeniesienia subskrypcji](./media/billing-mca-request-billing-ownership/mca-transfer-completed.png)
    
   |Kolumna  |Definicja|
   |---------|---------|
   |Identyfikator żądania przeniesienia|Unikatowy identyfikator Twoje żądanie przeniesienia. Przesyłając żądanie pomocy technicznej, udostępniać identyfikator pomocy technicznej platformy Azure w celu przyspieszenia obsługi żądania pomocy technicznej|
   |Przeniesienia zażądano w dniu|Data wysłania żądania przeniesienia|
   |Przeniesienia żądane przez|Adres e-mail użytkownika, który wysłał żądanie transferu|
   |Żądanie przeniesienia wygasa w dniu| Data wygaśnięcia żądania przeniesienia|
   |Adres e-mail adresata|Adres e-mail użytkownika, które zostały wysłane żądanie, aby przenieść własność rozliczeń|
   |Link do przenoszenia wysyłany do adresata|Adres url, do którego została wysłana do użytkownika, aby przejrzeć żądanie transferu|

## <a name="supported-subscription-types"></a>Typy obsługiwane subskrypcji

Możesz poprosić własność rozliczeń subskrypcji typów wymienionych poniżej.

- [Akcja pakietu](https://azure.microsoft.com/offers/ms-azr-0025p/)\* 
- [Platforma Azure w ramach programu licencjonowania Open](https://azure.microsoft.com/offers/ms-azr-0111p/)\*
- [Dostęp sponsorowany Microsoft Azure — dostęp próbny](https://azure.microsoft.com/offers/azure-pass/)\*
- [Enterprise Dev/Test](https://azure.microsoft.com/offers/ms-azr-0148p/)
- [Bezpłatna wersja próbna](https://azure.microsoft.com/offers/ms-azr-0044p/)\*
- [Pay-As-You-Go](https://azure.microsoft.com/offers/ms-azr-0003p/) 
- [Płatność za rzeczywiste użycie, tworzenie i testowanie](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Plan platformy Microsoft Azure](https://azure.microsoft.com/offers/ms-azr-0017g/)\*\*
- [Oferta sponsorowana Microsoft Azure](https://azure.microsoft.com/offers/ms-azr-0036p/)\*
- [Microsoft Enterprise Agreement](https://azure.microsoft.com/pricing/enterprise-agreement/)
- [Microsoft Partner Network](https://azure.microsoft.com/offers/ms-azr-0025p/)\*
- [Platformy MSDN](https://azure.microsoft.com/offers/ms-azr-0062p/)\*
- [Subskrybenci programu Visual Studio Enterprise (BizSpark)](https://azure.microsoft.com/offers/ms-azr-0064p/)\*
- [Subskrybenci programu Visual Studio Enterprise (MPN)](https://azure.microsoft.com/offers/ms-azr-0029p/)\*
- [Subskrybenci programu Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/)\*
- [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/)\*
- [Subskrybenci programu Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/)\*

\* Wszystkie środki, które są dostępne w ramach subskrypcji nie będą dostępne w ramach nowego konta po przeniesieniu.

\*\* Obsługiwane tylko w przypadku subskrypcji w ramach kont, które są tworzone podczas tworzenia konta w witrynie internetowej platformy Azure.


## <a name="additional-information"></a>Dodatkowe informacje

W poniższej sekcji przedstawiono dodatkowe informacje na temat przenoszenia subskrypcji.

### <a name="no-service-downtime"></a>Nie przestojów

Usługi platformy Azure w ramach subskrypcji pozostanie uruchomione bez jakiejkolwiek przerwy. Możemy przejść tylko relacji rozliczeń dla subskrypcji platformy Azure, określone przez użytkownika do transferu.

### <a name="disabled-subscriptions"></a>Wyłączonych subskrypcji

Nie można przenieść wyłączonych subskrypcji. Subskrypcje muszą być w stanie aktywnym, aby przenieść swoje własność rozliczeń.

### <a name="azure-resources-transfer"></a>Przeniesienie zasobów platformy Azure

Wszystkie zasoby z subskrypcji, takie jak maszyny wirtualne, dyski i transfer witryn sieci Web.

### <a name="azure-marketplace-products-transfer"></a>Transfer produktów platformy Azure Marketplace

Produkty platformy Azure Marketplace przesyłać wraz z ich odpowiednich subskrypcji.

### <a name="azure-reservations-transfer"></a>Transfer rezerwacji platformy Azure

Azure rezerwacji nie automatycznego przenoszenia subskrypcji. [Skontaktuj się z działem pomocy technicznej platformy Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) przenieść rezerwacji.

### <a name="access-to-azure-services"></a>Dostęp do usług platformy Azure

Dostęp do istniejących użytkowników, grup lub jednostek usług przypisanego za pomocą (Azure RBAC (kontrola dostępu oparta na rolach)) [... nie dotyczy /role-based-Access-Control/overview.MD] podczas przejścia.

### <a name="azure-support-plan"></a>Plan pomocy technicznej platformy Azure

Pomoc techniczna platformy Azure nie przenosi z subskrypcjami. Jeśli użytkownik przesyła wszystkie subskrypcje systemu Azure, poproś o anulować plan pomocy technicznej, ich.

### <a name="charges-for-transferred-subscription"></a>Opłaty za subskrypcję przeniesione

Pierwotny właściciel rozliczeń subskrypcji jest odpowiedzialny za wszelkie opłaty, które zostały zgłoszone, aż do momentu zakończeniu transferu. Twoja sekcja faktury jest obciążony opłatami zgłoszone od momentu przeniesienia i nowszych wersjach. Może to być niektóre opłaty, które miały miejsce przed przeniesieniem, ale zostało zgłoszone po tym dniu. Te opłaty są wyświetlane w sekcji faktury.

### <a name="cancel-a-transfer-request"></a>Anuluj żądanie transferu

Możesz anulować żądanie transferu do czasu zatwierdzenia lub odrzucenia żądania. Aby anulować żądanie transferu, przejdź do [strony szczegółów transferu](#check-the-transfer-request-status) i wybierz pozycję Anuluj w dolnej części strony.

### <a name="software-as-a-service-saas-transfer"></a>Oprogramowanie jako usługa (SaaS) przeniesienia

Produkty SaaS nie są przenoszone z subskrypcjami. Poproś użytkownika o [Azure skontaktuj się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) Aby przenieść własność rozliczeń produktów SaaS. Wraz z własność rozliczeń użytkownik może również przenieść własność zasobów. Posiadanie zasobów umożliwia wykonywanie operacji zarządzania, takich jak usuwanie oraz wyświetlanie szczegółów produktu. Użytkownik musi być właściciel zasobu nad produktem SaaS, aby przenieść własność zasobów.

## <a name="check-for-access"></a>Sprawdzanie dostępu
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Potrzebujesz pomocy? Skontaktuj się z pomocą techniczną

Jeśli potrzebujesz pomocy, [się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) można szybko rozwiązać swój problem.

## <a name="next-steps"></a>Kolejne kroki

- Własność rozliczeń subskrypcji platformy Azure jest przenoszona do sekcji faktury. Informacje o opłatach za te subskrypcje w [witryny Azure portal](https://portal.azure.com).
- Zezwól na uprawnienia do wyświetlania i zarządzania rozliczeniami za te subskrypcje. Aby uzyskać więcej informacji, zobacz [faktury sekcji role i zadania](billing-understand-mca-roles.md#invoice-section-roles-and-tasks).
