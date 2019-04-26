---
title: Pobierz rozliczeń własności subskrypcji platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak żądania własność rozliczeń subskrypcji platformy Azure od innych użytkowników.
services: ''
documentationcenter: ''
author: amberbhargava
manager: amberb
editor: banders
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/28/2019
ms.author: banders
ms.openlocfilehash: be8c7fcebca224196d9eac7d22387989b1bdfd46
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60371963"
---
# <a name="get-billing-ownership-of-azure-subscriptions-from-other-users"></a>Pobierz rozliczeń własności subskrypcji platformy Azure od innych użytkowników

Możesz chcieć przejęcie na własność subskrypcji platformy Azure, jeśli istniejące właściciela rozliczeń opuszcza organizacji lub chcesz zapłacić za subskrypcje za pośrednictwem konta rozliczeniowego.

Możesz wysłać żądanie, aby przejąć prawo własności subskrypcji platformy Azure od istniejących właścicieli w inne konta rozliczeniowego. Przejmowanie na własność przesyła obowiązki rozliczeń subskrypcji do sekcji faktury.

Aby zażądać własność rozliczeń, musi być **właściciel sekcji faktury** lub **Współautor sekcji faktury**. Aby dowiedzieć się więcej, zobacz [faktury sekcji role zadania](billing-understand-mca-roles.md#invoice-section-roles-and-tasks).

Ten artykuł dotyczy konta rozliczeniowego dla umowy klienta firmy Microsoft. [Sprawdź, czy dostęp do umowy klienta Microsoft](#check-access-to-a-microsoft-customer-agreement).

## <a name="request-billing-ownership-in-the-azure-portal"></a>Żądanie własność rozliczeń w witrynie Azure portal

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. Wyszukaj pozycję **Zarządzanie kosztami i rozliczenia**.

   ![Zrzut ekranu pokazujący Azure portal Wyszukaj Zarządzanie kosztami i rozliczenia](./media/billing-mca-request-billing-ownership/billing-search-cost-management-billing.png)

3. Przejdź do sekcji faktury. W zależności od dostęp może być konieczne wybierz konto rozliczeniowe lub profil rozliczeniowy. Z konta fakturowania i profilu, wybierz **faktury sekcje** i następnie sekcji faktury.
   <!-- Todo - Add a screenshot -->

4. Wybierz **żądań przeniesienia** po stronie lewej dolnej.

5. W górnej części strony wybierz pozycję **Dodaj**.

6. Wprowadź adres e-mail użytkownika, którego jesteś żądanie własność rozliczeń z. Użytkownik musi być administratorem konta na konto rozliczeniowe programu usług Microsoft Online Service lub właściciel konta w ramach umowy Enterprise Agreement. Aby uzyskać więcej informacji, zobacz [wyświetlić konta rozliczeniowego w witrynie Azure portal](billing-view-all-accounts.md).

   ![Zrzut ekranu przedstawiający dodawanie nowego żądania przeniesienia](./media/billing-mca-request-billing-ownership/mca-new-transfer-request.png)

7. Wybierz **Wyślij żądanie przeniesienia**.

8. Użytkownik otrzymuje wiadomość e-mail z instrukcjami, aby zapoznać się z Twoje żądanie przeniesienia.

   ![Zrzut ekranu tego przedstawia przegląd transferu wiadomość e-mail z żądaniem](./media/billing-mca-request-billing-ownership/mca-review-transfer-request-email.png)

9. Aby zatwierdzić żądanie przeniesienia, użytkownik wybierze link w wiadomości e-mail i instrukcje.

    ![Zrzut ekranu tego przedstawia przegląd transferu wiadomość e-mail z żądaniem](./media/billing-mca-request-billing-ownership/mca-review-transfer-request.png)

## <a name="check-the-status-of-your-transfer-request-in-the-azure-portal"></a>Sprawdź stan Twoje żądanie przeniesienia w witrynie Azure portal

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. Wyszukaj pozycję **Zarządzanie kosztami i rozliczenia**.

   ![Zrzut ekranu pokazujący Azure portal Wyszukaj Zarządzanie kosztami i rozliczenia](./media/billing-mca-request-billing-ownership/billing-search-cost-management-billing.png)

3. Przejdź do sekcji faktury. W zależności od dostęp może być konieczne wybierz konto rozliczeniowe lub profil rozliczeniowy. Z konta fakturowania i profilu, wybierz **faktury sekcje** i następnie sekcji faktury.
   <!-- Todo - Add a screenshot -->

4. Wybierz **żądań przeniesienia** po stronie lewej dolnej.

5. Strona żądań transferu zawiera następujące informacje:

    ![Zrzut ekranu przedstawiający listę żądań transferu](./media/billing-mca-request-billing-ownership/mca-view-transfer-requests.png)

   |Kolumna|Definicja|
   |---------|---------|
   |Data żądania|Data wysłania żądania przeniesienia|
   |Adresat|Adres e-mail użytkownika, które zostały wysłane żądanie, aby przenieść własność rozliczeń|
   |Data ważności|Data wygaśnięcia żądania|
   |Stan|Stan żądania przeniesienia|

    Żądanie przeniesienia może mieć jedną z następujących stanów:

   |Stan|Definicja|
   |---------|---------|
   |W toku|Użytkownik nie zaakceptował żądania przeniesienia|
   |Przetwarzanie|Użytkownik zatwierdził żądanie przeniesienia. Rozliczeń dla subskrypcji, dla których użytkownik wybrał przesyłaną wprowadzenie do sekcji faktury|
   |Zakończone| Naliczania opłat za subskrypcje wybrane przez użytkownika jest przekazywany do sekcji faktury|
   |Zakończone z błędami|Żądanie zostało ukończone, ale rozliczanie dla niektórych subskrypcji, że nie można przenosić wybranego użytkownika|
   |Wygaśnięcie|Użytkownik nie zaakceptował żądania na godzinę i wygasła.|
   |Anulowane|Dostęp do żądanie przeniesienia zostało anulowane żądania|
   |Odrzucono|Użytkownik odrzucił żądanie przeniesienia|

6. Wybierz żądanie przeniesienia, aby wyświetlić szczegóły. Na stronie szczegółów transferu zawiera następujące informacje:
   <!-- Todo - Add a screenshot -->

   |Kolumna  |Definicja|
   |---------|---------|
   |Identyfikator żądania przeniesienia|Unikatowy identyfikator Twoje żądanie przeniesienia. Przesyłając żądanie pomocy technicznej, udostępniać identyfikator pomocy technicznej platformy Azure w celu przyspieszenia obsługi żądania pomocy technicznej|
   |Przeniesienia zażądano w dniu|Data wysłania żądania przeniesienia|
   |Przeniesienia żądane przez|Adres e-mail użytkownika, który wysłał żądanie transferu|
   |Żądanie przeniesienia wygasa w dniu| Data wygaśnięcia żądania przeniesienia|
   |Adres e-mail odbiorcy|Adres e-mail użytkownika, które zostały wysłane żądanie, aby przenieść własność rozliczeń|
   |Link do przenoszenia wysyłany do adresata|Adres url, do którego została wysłana do użytkownika, aby przejrzeć żądanie transferu|

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

Dostęp do zasobów platformy Azure, która została ustawiona przy użyciu funkcji Azure RBAC (kontrola dostępu oparta na rolach) nie zmienia się podczas przejścia.

### <a name="azure-support-plan"></a>Plan pomocy technicznej platformy Azure

Pomoc techniczna platformy Azure nie przenosi z subskrypcjami. Jeśli użytkownik przesyła wszystkie subskrypcje systemu Azure, poproś o anulować plan pomocy technicznej, ich.

### <a name="charges-for-transferred-subscription"></a>Opłaty za subskrypcję przeniesione

Pierwotny właściciel rozliczeń subskrypcji jest odpowiedzialny za wszelkie opłaty, które zostały zgłoszone, aż do momentu zakończeniu transferu. Twoja sekcja faktury jest obciążony opłatami zgłoszone od momentu przeniesienia i nowszych wersjach. Może to być niektóre opłaty, które miały miejsce przed przeniesieniem, ale zostało zgłoszone po tym dniu. Te opłaty są wyświetlane w sekcji faktury.

### <a name="supported-offers"></a>Obsługiwane oferty

Można dokonać transferu subskrypcji ani typy ofert, z wyjątkiem oferuje dostawcy usług Kryptograficznych.

### <a name="cancel-a-transfer-request"></a>Anuluj żądanie transferu

Możesz anulować żądanie transferu do czasu zatwierdzenia lub odrzucenia żądania. Aby anulować żądanie transferu, przejdź do strony szczegółów transferu, i wybierz pozycję Anuluj w dolnej części strony.

### <a name="software-as-a-service-saas-transfer"></a>Oprogramowanie jako usługa (SaaS) przeniesienia

Produkty SaaS nie są przenoszone z subskrypcjami. Poproś użytkownika o [Azure skontaktuj się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) Aby przenieść własność rozliczeń produktów SaaS. Wraz z własność rozliczeń użytkownik może również przenieść własność zasobów. Posiadanie zasobów umożliwia wykonywanie operacji zarządzania, takich jak usuwanie oraz wyświetlanie szczegółów produktu. Użytkownik musi być właściciel zasobu nad produktem SaaS, aby przenieść własność zasobów.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Sprawdź dostęp do umowy klienta firmy Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Potrzebujesz pomocy? Kontakt z pomocą techniczną

Jeśli potrzebujesz pomocy, [się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) można szybko rozwiązać swój problem.

## <a name="next-steps"></a>Kolejne kroki

- Własność rozliczeń subskrypcji platformy Azure jest przenoszona do sekcji faktury. Informacje o opłatach za te subskrypcje w [witryny Azure portal](https://portal.azure.com).
- Zezwól na uprawnienia do wyświetlania i zarządzania rozliczeniami za te subskrypcje. Aby uzyskać więcej informacji, zobacz [faktury sekcji role i zadania](billing-understand-mca-roles.md#invoice-section-roles-and-tasks).
