---
title: Zapoznaj się z rozliczeniami w ramach umowy klienta firmy Microsoft — Azure
description: Dowiedz się, jak przejrzeć informacje o rozliczeniach i użyciu zasobów oraz sprawdzić opłaty dla faktury za umowę klienta firmy Microsoft.
author: bandersmsft
manager: jureid
tags: billing
ms.service: cost-management-billing
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/22/2019
ms.author: banders
ms.openlocfilehash: 3cbc4ab4f0e2ad18c7d1d430ed5501e23b5f5f4d
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75987068"
---
# <a name="tutorial-review-your-microsoft-customer-agreement-invoice"></a>Samouczek: przeglądanie faktury dla umowy klienta firmy Microsoft

Opłaty za fakturę można sprawdzić, analizując poszczególne transakcje. Na koncie rozliczeniowym umowy klienta firmy Microsoft co miesiąc generowana jest jedna faktura na każdy profil rozliczeniowy. Faktura zawiera wszystkie opłaty z poprzedniego miesiąca. Możesz wyświetlić faktury w Azure Portal i porównać opłaty z plikiem szczegółów użycia.

Ten samouczek dotyczy tylko klientów platformy Azure z umową klienta firmy Microsoft.

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Przejrzyj zafakturowane transakcje w Azure Portal
> * Szacowanie kwoty kolejnej faktury na podstawie oczekujących opłat
> * Analizowanie opłat za użycie platformy Azure

## <a name="prerequisites"></a>Wymagania wstępne

Musisz mieć konto rozliczeniowe dla umowy klienta firmy Microsoft.

Musisz mieć dostęp do umowy klienta firmy Microsoft. Aby wyświetlić informacje dotyczące rozliczeń i użytkowania, musisz być właścicielem profilu rozliczeń, współautorem, Czytelnikem lub menedżerem faktury. Aby dowiedzieć się więcej o rolach rozliczeniowych na potrzeby umów klienta firmy Microsoft, zobacz [Zadania i role profilu rozliczeniowego](../manage/understand-mca-roles.md#billing-profile-roles-and-tasks).

Musi ona być dłuższa niż 30 dni od dnia, w którym zasubskrybujesz platformę Azure. Na platformie Azure opłaty są naliczane na koniec okresu fakturowania.

## <a name="sign-in-to-azure"></a>Zaloguj się w usłudze Azure

- Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Sprawdzanie dostępu do umowy klienta firmy Microsoft

Sprawdź typ umowy, aby określić, czy masz dostęp do konta rozliczeniowego umowy klienta firmy Microsoft.

W Azure Portal wpisz *Cost Management + rozliczenia* w polu wyszukiwania, a następnie wybierz pozycję **Cost Management + rozliczenia**.

![Zrzut ekranu przedstawiający wyszukiwanie zarządzania kosztami i rozliczeń w witrynie Azure Portal](./media/review-customer-agreement-bill/billing-search-cost-management-billing.png)

Jeśli masz dostęp tylko do jednego zakresu rozliczeniowego, wybierz pozycję **Właściwości** po lewej stronie. Masz dostęp do konta rozliczeniowego umowy klienta firmy Microsoft, jeśli typ konta rozliczeniowego to **Umowa klienta firmy Microsoft**.

![Zrzut ekranu przedstawiający umowę klienta firmy Microsoft na stronie właściwości](./media/review-customer-agreement-bill/billing-mca-property.png)

Jeśli masz dostęp do wielu zakresów rozliczeniowych, sprawdź typ w kolumnie konta rozliczeniowego. Masz dostęp do konta rozliczeniowego umowy klienta firmy Microsoft, jeśli typ konta rozliczeniowego w dowolnym z Twoich zakresów to **Umowa klienta firmy Microsoft**.

![Zrzut ekranu przedstawiający umowę klienta firmy Microsoft na stronie listy kont rozliczeń](./media/review-customer-agreement-bill/billing-mca-in-the-list.png)

## <a name="review-invoiced-transactions-in-the-azure-portal"></a>Przejrzyj zafakturowane transakcje w Azure Portal

W Azure Portal wybierz pozycję **wszystkie transakcje** z lewej strony. W zależności od uprawnień dostępu może być konieczne wybranie konta rozliczeniowego, profilu rozliczeniowego lub sekcji faktury, a następnie wybranie pozycji **Wszystkie transakcje**.

Na stronie Wszystkie transakcje są wyświetlane następujące informacje:

![Zrzut ekranu przedstawiający listę rozliczonych transakcji](./media/review-customer-agreement-bill/mca-billed-transactions-list.png)

|Kolumna  |Definicja  |
|---------|---------|
|Data     | Data transakcji  |
|Identyfikator faktury     | Identyfikator faktury, na której jest uwzględniona transakcja. Jeśli przesyłasz wniosek o pomoc techniczną, podaj ten identyfikator, aby przyspieszyć obsługę wniosku przez zespół pomocy technicznej platformy Azure |
|Typ transakcji     |  Typ transakcji, na przykład zakup, anulowanie, opłaty za użycie  |
|Rodzina produktów     | Kategoria produktu, na przykład obliczenia w przypadku maszyn wirtualnych lub baza danych w przypadku usługi Azure SQL Database|
|Jednostka SKU produktu     | Unikatowy kod identyfikujący wystąpienie produktu |
|Ilość     |  Kwota transakcji      |
|Sekcja faktury     | Sekcja faktury danego profilu rozliczeniowego, w której jest widoczna ta transakcja |
|Profil rozliczeniowy     | Profil rozliczeniowy, na którego fakturze jest widoczna ta transakcja |

Wyszukaj Identyfikator faktury, aby odfiltrować transakcje dla faktury.

### <a name="view-transactions-by-invoice-sections"></a>Wyświetlanie transakcji według sekcji faktury

Sekcje faktur ułatwiają organizowanie kosztów na fakturze danego profilu rozliczeniowego. Aby uzyskać więcej informacji. Po wygenerowaniu faktury są na niej widoczne opłaty z wszystkich sekcji w ramach danego profilu rozliczeniowego.

Na poniżej ilustracji przedstawiono opłaty z sekcji działu księgowości na przykładowej fakturze.

![Przykładowy obraz pokazujący szczegółowe informacje w sekcji faktury](./media/review-customer-agreement-bill/invoicesection-details.png)

Po znalezieniu opłat w odpowiedniej sekcji faktury możesz wyświetlić transakcje w witrynie Azure Portal, aby wyjaśnić opłaty.

Przejdź na stronę Wszystkie transakcje w witrynie Azure Portal, aby wyświetlić transakcje z faktury.

Możesz filtrować według nazwy sekcji faktury, aby wyświetlić odpowiednie transakcje.

## <a name="review-pending-charges-to-estimate-your-next-invoice"></a>Szacowanie kwoty kolejnej faktury na podstawie oczekujących opłat

Na koncie rozliczeń dla umowy klienta firmy Microsoft opłaty są szacowane i uznawane za oczekujące, aż do ich zafakturowania. Możesz wyświetlić oczekujące opłaty w witrynie Azure Portal, aby oszacować kwotę kolejnej faktury. Oczekujące opłaty są szacunkowe i nie zawierają podatków. Rzeczywiste opłaty na kolejnej fakturze mogą się różnić od opłat oczekujących.

### <a name="view-summary-of-pending-charges"></a>Wyświetlanie podsumowania opłat oczekujących

Zaloguj się do [portalu Azure](https://portal.azure.com).

Wybierz profil rozliczeniowy. W zależności od uprawnień dostępu może być konieczne wybranie konta rozliczeniowego. Z poziomu konta rozliczeniowego wybierz pozycję **Profile rozliczeniowe**, a następnie wybierz profil rozliczeniowy.

Wybierz kartę **Podsumowanie** w górnej części ekranu.

W sekcji Opłaty są wyświetlane opłaty za bieżący i poprzedni miesiąc.

![Zrzut ekranu przedstawiający wyszukiwanie zarządzania kosztami i rozliczeń w witrynie Azure Portal](./media/review-customer-agreement-bill/mca-billing-profile-summary.png)

Opłaty za bieżący miesiąc to oczekujące opłaty naliczone w tym miesiącu, które zostaną rozliczone w momencie wygenerowania faktury za ten miesiąc. Jeśli jeszcze nie wygenerowano faktury za poprzedni miesiąc, opłaty z tego miesiąca również będą oznaczone jako oczekujące i pojawią się na najbliższej fakturze.

### <a name="view-pending-transactions"></a>Wyświetlanie oczekujących transakcji

Gdy znajdziesz oczekujące opłaty, możesz je wyjaśnić, analizując poszczególne transakcje, które im odpowiadają. Obecnie oczekujące opłaty za użycie nie są wyświetlane na stronie Wszystkie transakcje. Możesz wyświetlić je na stronie subskrypcji platformy Azure.

Zaloguj się do [portalu Azure](https://portal.azure.com).

W Azure Portal wpisz *Cost Management + rozliczenia* w polu wyszukiwania, a następnie wybierz pozycję **Cost Management + rozliczenia**.

Wybierz profil rozliczeniowy. W zależności od uprawnień dostępu może być konieczne wybranie konta rozliczeniowego. Z poziomu konta rozliczeniowego wybierz pozycję **Profile rozliczeniowe**, a następnie wybierz profil rozliczeniowy.

Wybierz pozycję **Wszystkie transakcje** po lewej stronie.

Wyszukaj *oczekujące*. Użyj filtru **Przedział czasu**, aby wyświetlić opłaty za bieżący lub ubiegły miesiąc.

![Zrzut ekranu przedstawiający listę oczekujących transakcji](./media/review-customer-agreement-bill/mca-pending-transactions-list.png)

### <a name="view-pending-usage-charges"></a>Wyświetlanie oczekujących opłat za użycie

Zaloguj się do [portalu Azure](https://portal.azure.com).

W Azure Portal wpisz *Cost Management + rozliczenia* w polu wyszukiwania, a następnie wybierz pozycję **Cost Management + rozliczenia**.

Wybierz profil rozliczeniowy. W zależności od uprawnień dostępu może być konieczne wybranie konta rozliczeniowego. Z poziomu konta rozliczeniowego wybierz pozycję **Profile rozliczeniowe**, a następnie wybierz profil rozliczeniowy.

Wybierz pozycję **Wszystkie subskrypcje** po lewej stronie.

Na stronie subskrypcji platformy Azure są wyświetlane opłaty za bieżący i ubiegły miesiąc, dotyczące poszczególnych subskrypcji z profilu rozliczeniowego. Opłaty za bieżący miesiąc to oczekujące opłaty naliczone w tym miesiącu, które zostaną rozliczone w momencie wygenerowania faktury za ten miesiąc. Jeśli jeszcze nie wygenerowano faktury za poprzedni miesiąc, opłaty z tego miesiąca również będą oznaczone jako oczekujące.

![Zrzut ekranu przedstawiający listę subskrypcji platformy Azure dla profilu rozliczeniowego](./media/review-customer-agreement-bill/mca-billing-profile-subscriptions-list.png)

## <a name="analyze-your-azure-usage-charges"></a>Analizowanie opłat za użycie platformy Azure

Możesz przeanalizować opłaty związane z użyciem, korzystając z pliku CSV z danymi o użyciu platformy Azure i opłatach. Możesz pobrać taki plik dla faktury lub dla oczekujących opłat.

### <a name="download-your-invoice-and-usage-details"></a>Pobierz fakturę i szczegóły użycia

W zależności od dostępu może być konieczne wybranie konta rozliczenia lub profilu rozliczeń w ramach Cost Management i rozliczeń. W menu po lewej stronie wybierz pozycję **Faktury** w obszarze **Rozliczenia**. W siatce faktur znajdź wiersz faktury, którą chcesz pobrać. Kliknij symbol pobierania lub wielokropek (...) na końcu wiersza. W polu **Pobierz** Pobierz plik szczegółów użycia i fakturę.

### <a name="view-detailed-usage-by-invoice-section"></a>Wyświetlanie szczegółów użycia według sekcji faktury

Możesz przefiltrować plik z danymi o użyciu platformy Azure i opłatach, aby uzgodnić opłaty za użycie w ramach poszczególnych sekcji faktury.

Poniższe informacje przeprowadzą Cię przez uzgadnianie opłat za obliczenia dla sekcji faktura Księgowość:

![Przykładowy obraz pokazujący szczegółowe informacje w sekcji faktury](./media/review-customer-agreement-bill/invoicesection-details.png)

| Faktura (PDF) | Plik CSV użycia platformy Azure i opłata |
| --- | --- |
|Dział księgowości |invoiceSectionName |
|Opłaty za użycie — plan Microsoft Azure |productOrderName |
|Wystąpienia obliczeniowe |serviceFamily |

Przefiltruj kolumnę **invoiceSectionName** w pliku CSV na **Wydział księgowości**. Następnie Przefiltruj kolumnę **productOrderName** w pliku CSV do **planu Microsoft Azure**. Następnie odfiltruj kolumnę **servicefamily** w pliku CSV do **firmy Microsoft. COMPUTE**.

![Zrzut ekranu przedstawiający plik użycia i opłat przefiltrowany według sekcji faktury](./media/review-customer-agreement-bill/billing-usage-file-filtered-by-invoice-section.png)

### <a name="view-detailed-usage-by-subscription"></a>Wyświetlanie szczegółów użycia według subskrypcji

Możesz przefiltrować plik CSV z danymi o użyciu platformy Azure i opłatach, aby uzgodnić opłaty za użycie w ramach poszczególnych subskrypcji. Gdy znajdziesz opłaty w ramach określonej subskrypcji, możesz je przeanalizować, korzystając z pliku CSV z danymi o użyciu platformy Azure i opłatach.

Na poniższej ilustracji przedstawiono listę subskrypcji w witrynie Azure Portal.

![Zrzut ekranu przedstawiający listę subskrypcji platformy Azure dla profilu rozliczeniowego](./media/review-customer-agreement-bill/mca-billing-profile-subscriptions-list-highlighted.png)

Przefiltruj kolumnę **subscriptionName** w pliku CSV użycia platformy Azure i opłat według wartości **WA_Subscription**, aby wyświetlić szczegółowe informacje o opłatach za użycie w ramach tej subskrypcji.

![Zrzut ekranu przedstawiający plik użycia i opłat przefiltrowany według subskrypcji](./media/review-customer-agreement-bill/billing-usage-file-filtered-by-subscription.png)

## <a name="pay-your-bill"></a>Płatność za rachunek

Instrukcje dotyczące płatności znajdują się na końcu faktury. [Dowiedz się, jak zapłacić](mca-understand-your-invoice.md#how-to-pay).

Jeśli już zapłacono za rachunek, możesz sprawdzić stan płatności na stronie Faktury w witrynie Azure Portal.

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Przejrzyj zafakturowane transakcje w Azure Portal
> * Szacowanie kwoty kolejnej faktury na podstawie oczekujących opłat
> * Analizowanie opłat za użycie platformy Azure

Ukończ Przewodnik Szybki Start, aby rozpocząć korzystanie z analizy kosztów.

> [!div class="nextstepaction"]
> [Rozpoczęcie analizy kosztów](../costs/quick-acm-cost-analysis.md)
