---
title: Omówienie opłat na fakturze za umowę partnerską firmy Microsoft — Azure
description: Dowiedz się, jak czytać fakturę i co oznaczają poszczególne opłaty.
author: jureid
manager: jureid
tags: billing
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/10/2019
ms.author: banders
ms.openlocfilehash: 9994ef6da379d0063930912a4d198b81623ddbe8
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74223710"
---
# <a name="understand-charges-on-your-microsoft-partner-agreement-invoice"></a>Omówienie opłat na fakturze za umowę partnerską firmy Microsoft

 Na koncie rozliczeniowym umowy partnerskiej firmy Microsoft co miesiąc generowana jest jedna faktura na każdy profil rozliczeniowy. Faktura zawiera wszystkie opłaty dla klienta z poprzedniego miesiąca. Aby zrozumieć opłaty widoczne na fakturze, przeanalizuj poszczególne transakcje w witrynie Azure Portal. Możesz również wyświetlać faktury w witrynie Azure Portal. Aby uzyskać więcej informacji, zobacz [Pobieranie faktur z witryny Azure Portal](billing-download-azure-invoice.md).

Ten artykuł dotyczy konta rozliczeniowego związanego z umową partnerską firmy Microsoft. [Sprawdź, czy masz dostęp do umowy partnerskiej firmy Microsoft](#check-access-to-a-microsoft-partner-agreement).

## <a name="view-transactions-for-an-invoice-in-the-azure-portal"></a>Wyświetlanie transakcji uwzględnionych na fakturze w witrynie Azure Portal

1. Zaloguj się w witrynie [Azure Portal](https://www.azure.com).

2. Wyszukaj pozycję *Zarządzanie kosztami i rozliczenia*.

   <!--  ![Screenshot that shows Azure portal search for cost management + billing](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png) -->

3. Wybierz pozycję **Wszystkie transakcje** po lewej stronie. W zależności od uprawnień dostępu może być konieczne wybranie konta rozliczeniowego, profilu rozliczeniowego lub klienta, a następnie wybranie pozycji **Wszystkie transakcje**.

4. Na stronie Wszystkie transakcje są wyświetlane następujące informacje:

    <!-- ![Screenshot that shows the billed transactions list](./media/billing-mpa-understand-your-bill/mpa-billing-profile-all-transactions.png) -->

    |Kolumna  |Definicja  |
    |---------|---------|
    |Date     | Data transakcji  |
    |Identyfikator faktury     | Identyfikator faktury, na której jest uwzględniona transakcja. Jeśli przesyłasz wniosek o pomoc techniczną, podaj ten identyfikator, aby przyspieszyć obsługę wniosku przez zespół pomocy technicznej platformy Azure |
    |Typ transakcji     |  Typ transakcji, na przykład zakup, anulowanie, opłaty za użycie  |
    |Rodzina produktów     | Kategoria produktu, na przykład obliczenia w przypadku maszyn wirtualnych lub baza danych w przypadku usługi Azure SQL Database|
    |Jednostka SKU produktu     | Unikatowy kod identyfikujący wystąpienie produktu |
    |Kwota     |  Kwota transakcji      |
    |Profil rozliczeniowy     | Profil rozliczeniowy, na którego fakturze jest widoczna ta transakcja |

5. Możesz wyszukać identyfikator faktury, aby odfiltrować transakcje z tej faktury.

## <a name="review-pending-charges-to-estimate-your-next-invoice"></a>Szacowanie kwoty kolejnej faktury na podstawie oczekujących opłat

Opłaty mają charakter szacunkowy i są uznawane za oczekujące, dopóki nie zostaną zafakturowane. Możesz wyświetlić oczekujące opłaty za profil rozliczeniowy umowy partnerskiej firmy Microsoft w witrynie Azure Portal, aby oszacować następną fakturę. Oczekujące opłaty są szacunkowe i nie zawierają podatków. Rzeczywiste opłaty na kolejnej fakturze mogą się różnić od opłat oczekujących.

### <a name="view-pending-transactions"></a>Wyświetlanie oczekujących transakcji

Gdy znajdziesz oczekujące opłaty, możesz je wyjaśnić, analizując poszczególne transakcje, które im odpowiadają. Obecnie oczekujące opłaty za użycie nie są wyświetlane na stronie Wszystkie transakcje. Możesz wyświetlić je na stronie subskrypcji platformy Azure. Aby uzyskać więcej informacji, zobacz [Wyświetlanie oczekujących opłat za użycie](#view-pending-usage-charges).

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. Wyszukaj pozycję *Zarządzanie kosztami i rozliczenia*.

   <!-- ![Screenshot that shows Azure portal search for cost management + billing](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png) -->

3. Wybierz profil rozliczeniowy. W zależności od uprawnień dostępu może być konieczne wybranie konta rozliczeniowego. Z poziomu konta rozliczeniowego wybierz pozycję **Profile rozliczeniowe**, a następnie wybierz profil rozliczeniowy.

4. Wybierz pozycję **Wszystkie transakcje** po lewej stronie.

5. Wyszukaj *oczekujące*. Użyj filtru **Przedział czasu**, aby wyświetlić opłaty za bieżący lub ubiegły miesiąc.

   <!-- ![Screenshot that shows the pending transactions list](./media/billing-mpa-understand-your-bill/mpa-billing-profile-pending-transactions.png) -->

### <a name="view-pending-charges-by-customer"></a>Wyświetlanie oczekujących opłat według klienta

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. Wyszukaj pozycję *Zarządzanie kosztami i rozliczenia*.

3. Wybierz profil rozliczeniowy. W zależności od uprawnień dostępu może być konieczne wybranie konta rozliczeniowego. Z poziomu konta rozliczeniowego wybierz pozycję **Profile rozliczeniowe**, a następnie wybierz profil rozliczeniowy.

4. Wybierz pozycję **Klienci** w lewej części strony.

    <!-- ![screenshot of billing profile customers list](./media/billing-mpa-understand-your-bill/mpa-billing-profile-customers.png) -->

5. Na stronie Klienci są wyświetlane opłaty za bieżący i ubiegły miesiąc, dotyczące poszczególnych klientów skojarzonych z profilem rozliczeniowym. Opłaty za bieżący miesiąc to oczekujące opłaty naliczone w tym miesiącu, które zostaną rozliczone w momencie wygenerowania faktury za ten miesiąc. Jeśli jeszcze nie wygenerowano faktury za poprzedni miesiąc, opłaty z tego miesiąca również będą oznaczone jako oczekujące.

### <a name="view-pending-usage-charges"></a>Wyświetlanie oczekujących opłat za użycie

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. Wyszukaj pozycję *Zarządzanie kosztami i rozliczenia*.

3. Wybierz profil rozliczeniowy. W zależności od uprawnień dostępu może być konieczne wybranie konta rozliczeniowego. Z poziomu konta rozliczeniowego wybierz pozycję **Profile rozliczeniowe**, a następnie wybierz profil rozliczeniowy.

4. Wybierz pozycję **Subskrypcje platformy Azure** w lewej części strony.

5. Na stronie subskrypcji platformy Azure są wyświetlane opłaty za bieżący i ubiegły miesiąc, dotyczące poszczególnych subskrypcji z profilu rozliczeniowego. Opłaty za bieżący miesiąc to oczekujące opłaty naliczone w tym miesiącu, które zostaną rozliczone w momencie wygenerowania faktury za ten miesiąc. Jeśli jeszcze nie wygenerowano faktury za poprzedni miesiąc, opłaty z tego miesiąca również będą oznaczone jako oczekujące.

<!--     ![Screenshot that shows the Azure subscriptions list for MPA billing profile](./media/billing-mpa-understand-your-bill/mpa-billing-profile-subscriptions-list.png) -->

## <a name="analyze-your-azure-usage-charges"></a>Analizowanie opłat za użycie platformy Azure

Możesz przeanalizować opłaty związane z użyciem, korzystając z pliku CSV z danymi o użyciu platformy Azure i opłatach. [Plik CSV użycia i opłat platformy Azure możesz pobrać w witrynie Azure Portal](billing-download-azure-daily-usage.md).

Możesz przefiltrować plik z danymi o użyciu platformy Azure i opłatach, aby uzgodnić opłaty za użycie dla poszczególnych produktów znajdujących się w pliku PDF faktury. Aby wyświetlić szczegółowe opłaty za użycie określonego produktu, przefiltruj kolumnę **produkt** w pliku CSV użycia i opłat platformy Azure w celu uwzględnienia tylko nazwy tego produktu.

Możesz również filtrować kolumnę **customerName** w pliku CSV użycia i opłat platformy Azure, aby wyświetlić opłaty za dzienne użycie dla poszczególnych klientów. Aby wyświetlić opłaty za dzienne użycie według subskrypcji platformy Azure, przefiltruj kolumnę **subscriptionName**.


## <a name="pay-your-bill"></a>Płatność za rachunek

Instrukcje dotyczące płatności znajdują się na końcu faktury. Możesz uiścić płatność za pomocą przelewu lub czeku w ciągu 60 dni od daty faktury.

Jeśli już zapłacono za rachunek, możesz sprawdzić stan płatności na stronie Faktury w witrynie Azure Portal.

## <a name="check-access-to-a-microsoft-partner-agreement"></a>Sprawdzanie dostępu do umowy partnerskiej firmy Microsoft
[!INCLUDE [billing-check-mpa](../../includes/billing-check-mpa.md)]

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [utwórz wniosek o pomoc techniczną](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat faktury i szczegółów użycia, zobacz:
