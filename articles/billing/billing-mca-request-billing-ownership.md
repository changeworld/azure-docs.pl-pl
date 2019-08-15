---
title: Uzyskaj własność rozliczeń subskrypcji platformy Azure
description: Dowiedz się, jak zażądać własności rozliczeń subskrypcji platformy Azure od innych użytkowników.
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
ms.openlocfilehash: 110bd975de1d865c2aa3d3b088c98cdc8b9e45ad
ms.sourcegitcommit: b12a25fc93559820cd9c925f9d0766d6a8963703
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/14/2019
ms.locfileid: "69019555"
---
# <a name="get-billing-ownership-of-azure-subscriptions-from-other-accounts"></a>Uzyskaj własność rozliczeń subskrypcji platformy Azure na podstawie innych kont

Możesz chcieć przejąć własność subskrypcji platformy Azure, jeśli istniejący właściciel rozliczeń opuszcza organizację lub chcesz uregulować subskrypcje za pośrednictwem konta rozliczeniowego. Przejęcie własności przenosi obowiązki dotyczące rozliczeń subskrypcji na konto.

Ten artykuł dotyczy konta rozliczeniowego umowy klienta firmy Microsoft. [Sprawdź, czy masz dostęp do umowy klienta firmy Microsoft](#check-for-access).

Aby zażądać własności rozliczenia, musisz być **właścicielem sekcji faktury** lub współautorem sekcji faktury. Aby dowiedzieć się więcej, zobacz [sekcję faktury zadania ról](billing-understand-mca-roles.md#invoice-section-roles-and-tasks).

## <a name="request-billing-ownership"></a>Żądaj własności rozliczeń

1. Zaloguj się do [Azure Portal](https://portal.azure.com) jako właściciel sekcji faktury lub współautor konta rozliczeniowego dla umowy klienta firmy Microsoft.

2. Wyszukaj **Cost Management i rozliczenia**.

   ![Zrzut ekranu przedstawiający Azure Portal wyszukiwanie kosztów zarządzania i rozliczeń](./media/billing-mca-request-billing-ownership/billing-search-cost-management-billing.png)

3. Wybierz **sekcje faktury** z lewej strony. W zależności od dostępu może być konieczne wybranie konta rozliczenia lub profilu rozliczeń. Na koncie rozliczeń lub profilu wybierz pozycję **faktury sekcje**.
   
   ![Zrzut ekranu pokazujący Wybieranie sekcji faktur](./media/billing-mca-request-billing-ownership/mca-select-invoice-sections.png)        

4. Wybierz z listy sekcję faktury. Po przekroczeniu własności subskrypcji zostanie naliczona Ta sekcja faktury.

5. Wybierz pozycję **Prześlij żądania** w lewej dolnej części, a następnie wybierz pozycję **Dodaj**.
 
   ![Zrzut ekranu pokazujący Wybieranie żądań transferu](./media/billing-mca-request-billing-ownership/mca-select-transfer-requests.png)

6. Wprowadź adres e-mail użytkownika, od którego żądasz własności rozliczeń. Użytkownik musi być administratorem konta w ramach konta rozliczeń programu Microsoft Online Service lub właściciela konta na Umowa Enterprise. Aby uzyskać więcej informacji, zobacz [Wyświetlanie kont rozliczeń w Azure Portal](billing-view-all-accounts.md). Wybierz pozycję **Wyślij żądanie przeniesienia**.

   ![Zrzut ekranu pokazujący wysyłanie żądania transferu](./media/billing-mca-request-billing-ownership/mca-send-transfer-requests.png)

7. Użytkownik otrzymuje wiadomość e-mail z instrukcjami dotyczącymi przeglądania żądania transferu.

   ![Zrzut ekranu przedstawiający wiadomość e-mail dotyczącą przeglądu żądania przeniesienia](./media/billing-mca-request-billing-ownership/mca-review-transfer-request-email.png)

8. Aby zatwierdzić żądanie transferu, użytkownik wybiera link w wiadomości e-mail i postępuje zgodnie z instrukcjami.

    ![Zrzut ekranu przedstawiający wiadomość e-mail dotyczącą przeglądu żądania przeniesienia](./media/billing-mca-request-billing-ownership/mca-review-transfer-request.png)

## <a name="check-the-transfer-request-status"></a>Sprawdź stan żądania transferu

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. Wyszukaj **Cost Management i rozliczenia**.

   ![Zrzut ekranu przedstawiający Azure Portal wyszukiwanie kosztów zarządzania i rozliczeń](./media/billing-mca-request-billing-ownership/billing-search-cost-management-billing.png)


3. Wybierz **sekcje faktury** z lewej strony. W zależności od dostępu może być konieczne wybranie konta rozliczenia lub profilu rozliczeń. Na koncie rozliczeń lub profilu wybierz pozycję **faktury sekcje**.
   
   ![Zrzut ekranu pokazujący Wybieranie sekcji faktur](./media/billing-mca-request-billing-ownership/mca-select-invoice-sections.png)        

4. Wybierz sekcję Invoice z listy, dla której wysłano żądanie transferu.

5. Wybierz pozycję **Prześlij żądania** w lewej dolnej części. Na stronie żądania transferu są wyświetlane następujące informacje:

    ![Zrzut ekranu przedstawiający listę żądań transferu](./media/billing-mca-request-billing-ownership/mca-select-transfer-requests-for-status.png)

   |Kolumna|Definicja|
   |---------|---------|
   |Data żądania|Data wysłania żądania transferu|
   |Adresat|Adres e-mail użytkownika, który wysłał żądanie przetransferowania własności rozliczeń|
   |Data ważności|Data wygaśnięcia żądania|
   |Stan|Stan żądania transferu|

    Żądanie transferu może mieć jeden z następujących stanów:

   |Stan|Definicja|
   |---------|---------|
   |W toku|Użytkownik nie zaakceptował żądania transferu|
   |Przetwarzanie|Użytkownik zatwierdził żądanie przeniesienia. Naliczanie opłat za subskrypcje, które zostały wybrane przez użytkownika, jest przenoszone do sekcji faktury|
   |Ukończone| Opłaty za subskrypcje, które zostały wybrane przez użytkownika, są przenoszone do sekcji faktury|
   |Zakończono z błędami|Żądanie zostało zakończone, ale rozliczenia dla niektórych subskrypcji, które nie zostały wybrane przez użytkownika, nie można przesłać|
   |Wygaśnięcie|Użytkownik nie zaakceptował żądania na czas i wygasł|
   |Anulowane|Ktoś z dostępem do żądania transferu anulował żądanie|
   |Odrzucono|Użytkownik odrzucił żądanie transferu|

7. Wybierz żądanie transferu, aby wyświetlić szczegóły. Na stronie Szczegóły transferu są wyświetlane następujące informacje:
   
   ![Zrzut ekranu przedstawiający listę przesłanych subskrypcji](./media/billing-mca-request-billing-ownership/mca-transfer-completed.png)
    
   |Kolumna  |Definicja|
   |---------|---------|
   |Identyfikator żądania transferu|Unikatowy identyfikator żądania transferu. Jeśli przesyłasz żądanie pomocy technicznej, skontaktuj się z pomocą techniczną platformy Azure, aby przyspieszyć żądanie pomocy technicznej|
   |Zażądano przeniesienia|Data wysłania żądania transferu|
   |Transfer żądany przez|Adres e-mail użytkownika, który wysłał żądanie transferu|
   |Żądanie przeniesienia wygasa| Data wygaśnięcia żądania transferu|
   |Adres e-mail odbiorcy|Adres e-mail użytkownika, który wysłał żądanie przetransferowania własności rozliczeń|
   |Link do transferu został wysłany do adresata|Adres URL, który został wysłany do użytkownika w celu przejrzenia żądania transferu|

## <a name="supported-subscription-types"></a>Obsługiwane typy subskrypcji

Możesz zażądać własności rozliczeń wymienionych poniżej typów subskrypcji.

- [Pakiet akcji](https://azure.microsoft.com/offers/ms-azr-0025p/)\* 
- [Licencjonowanie systemu Azure w ramach programu licencjonowania Open](https://azure.microsoft.com/offers/ms-azr-0111p/)\*
- [Azure — dostęp próbny sponsorowanie](https://azure.microsoft.com/offers/azure-pass/)\*
- [Enterprise Dev/Test](https://azure.microsoft.com/offers/ms-azr-0148p/)
- [Bezpłatna wersja próbna](https://azure.microsoft.com/offers/ms-azr-0044p/)\*
- [Pay-As-You-Go](https://azure.microsoft.com/offers/ms-azr-0003p/) 
- [Płatność zgodnie z rzeczywistym użyciem — tworzenie i testowanie](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Plan Microsoft Azure](https://azure.microsoft.com/offers/ms-azr-0017g/)\*\*
- [Oferta sponsorowana Microsoft Azure](https://azure.microsoft.com/offers/ms-azr-0036p/)\*
- [Microsoft Enterprise Agreement](https://azure.microsoft.com/pricing/enterprise-agreement/)
- [Microsoft Partner Network](https://azure.microsoft.com/offers/ms-azr-0025p/)\*
- [Platformy MSDN](https://azure.microsoft.com/offers/ms-azr-0062p/)\*
- [Subskrybenci Visual Studio Enterprise (BizSpark)](https://azure.microsoft.com/offers/ms-azr-0064p/)\*
- [Subskrybenci Visual Studio Enterprise (MPN)](https://azure.microsoft.com/offers/ms-azr-0029p/)\*
- [Subskrybenci Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/)\*
- [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/)\*
- [Subskrybenci Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/)\*

\*Wszelkie środki dostępne w ramach subskrypcji nie będą dostępne na nowym koncie po przeniesieniu.

\*\*Obsługiwane tylko w przypadku subskrypcji kont utworzonych podczas tworzenia konta w witrynie sieci Web systemu Azure.


## <a name="additional-information"></a>Dodatkowe informacje

W poniższej sekcji znajdują się dodatkowe informacje na temat przenoszenia subskrypcji.

### <a name="no-service-downtime"></a>Brak przestojów usługi

Usługi platformy Azure w ramach subskrypcji działają bez żadnych przerw. W przypadku subskrypcji platformy Azure, które użytkownik wybiera do przeniesienia, przeniesiemy tylko relację rozliczeń.

### <a name="disabled-subscriptions"></a>Wyłączone subskrypcje

Nie można przenieść wyłączonych subskrypcji. Aby przeniesienie własności rozliczeń było możliwe, subskrypcje muszą być w stanie aktywnym.

### <a name="azure-resources-transfer"></a>Transfer zasobów platformy Azure

Wszystkie zasoby z subskrypcji, takie jak maszyny wirtualne, dyski i transfery witryn sieci Web.

### <a name="azure-marketplace-products-transfer"></a>Transfer produktów w portalu Azure Marketplace

Transfer produktów w portalu Azure Marketplace wraz z odpowiednimi subskrypcjami.

### <a name="azure-reservations-transfer"></a>Azure Reservations transfer

Azure Reservations nie są automatycznie przenoszone przy użyciu subskrypcji. [Skontaktuj się z pomocą techniczną platformy Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) , aby przenieść rezerwacje.

### <a name="access-to-azure-services"></a>Dostęp do usług platformy Azure

Dostęp do istniejących użytkowników, grup lub jednostek usługi, które zostały przypisane przy użyciu usługi (RBAC kontroli dostępu opartej na rolach) [... w trakcie przejścia nie ma to żadnego oddziaływania na/Role-based-Access-Control/Overview.MD].

### <a name="azure-support-plan"></a>Plan pomocy technicznej platformy Azure

Pomoc techniczna platformy Azure nie jest przesyłana do subskrypcji. Jeśli Użytkownik przeniesie wszystkie subskrypcje platformy Azure, poproś go o anulowanie planu pomocy technicznej.

### <a name="charges-for-transferred-subscription"></a>Opłaty za przesłaną subskrypcję

Oryginalny właściciel rozliczeń subskrypcji jest odpowiedzialny za wszelkie opłaty, które zostały zgłoszone do momentu ukończenia transferu. Sekcja faktury jest odpowiedzialna za opłaty za okres transferu. Mogą występować pewne opłaty, które miały miejsce przed przekazaniem, ale zostały zgłoszone później. Te opłaty są wyświetlane w sekcji faktury.

### <a name="cancel-a-transfer-request"></a>Anuluj żądanie transferu

Możesz anulować żądanie transferu, dopóki żądanie nie zostanie zatwierdzone lub odrzucone. Aby anulować żądanie transferu, przejdź do [strony Szczegóły transferu](#check-the-transfer-request-status) i wybierz pozycję Anuluj w dolnej części strony.

### <a name="software-as-a-service-saas-transfer"></a>Transfer oprogramowanie jako usługa (SaaS)

SaaS produkty nie są transferowane w ramach subskrypcji. Poproś użytkownika o [skontaktowanie się z pomocą techniczną platformy Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) w celu przetransferowania własności rozliczeniowej produktów SaaS. Oprócz własności rozliczeń użytkownik może również przetransferować własność zasobów. Własność zasobów umożliwia wykonywanie operacji zarządzania, takich jak usuwanie i wyświetlanie szczegółowych informacji o produkcie. Użytkownik musi być właścicielem zasobu na produkcie SaaS, aby przetransferować własność zasobów.

## <a name="check-for-access"></a>Sprawdzanie dostępu
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Potrzebujesz pomocy? Skontaktuj się z pomocą techniczną

Jeśli potrzebujesz pomocy, [skontaktuj się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) , aby szybko rozwiązać problem.

## <a name="next-steps"></a>Następne kroki

- Własność rozliczeń subskrypcji platformy Azure jest przenoszona do sekcji faktury. Śledź opłaty za te subskrypcje w [Azure Portal](https://portal.azure.com).
- Przyznaj innym osobom uprawnienia do wyświetlania rozliczeń i zarządzania nimi w ramach tych subskrypcji. Aby uzyskać więcej informacji, zobacz [sekcję faktury role i zadania](billing-understand-mca-roles.md#invoice-section-roles-and-tasks).
