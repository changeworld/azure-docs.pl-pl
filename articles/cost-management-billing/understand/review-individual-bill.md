---
title: Przeglądanie indywidualnego rachunku za korzystanie z platformy Azure
description: Dowiedz się, jak interpretować informacje o rozliczeniach i użyciu zasobów oraz sprawdzić opłaty za pojedynczą subskrypcję platformy Azure.
author: bandersmsft
ms.reviewer: judupont
tags: billing
ms.service: cost-management-billing
ms.topic: tutorial
ms.date: 02/13/2020
ms.author: banders
ms.openlocfilehash: 8ebda9716df2e9e45050760ee8d0434c12838370
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77199130"
---
# <a name="tutorial-review-your-individual-azure-bill"></a>Samouczek: Przeglądanie indywidualnego rachunku za korzystanie z platformy Azure

Ten artykuł pomaga zinterpretować i przejrzeć rachunek za korzystanie z platformy Azure. Dla każdego okresu rozliczeniowego otrzymujesz zwykle fakturę w wiadomości e-mail. Faktura odzwierciedla naliczone opłaty za platformę Azure. Te same informacje o kosztach na fakturze są dostępne w witrynie Azure Portal. W tym samouczku porównasz fakturę z zawartością pliku szczegółowego dziennego użycia oraz analizą kosztów w witrynie Azure Portal.

Ten samouczek dotyczy tylko klientów platformy Azure mających pojedynczą subskrypcję. Pojedyncze subskrypcje są najczęściej subskrypcjami ze stawkami płatności zgodnie z rzeczywistym użyciem kupowanymi bezpośrednio w witrynie internetowej platformy Azure.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Porównywanie zafakturowanych opłat z plikiem użycia
> * Porównywanie opłat i użycia w analizie kosztów

## <a name="prerequisites"></a>Wymagania wstępne

Musisz mieć płatne konto rozliczeniowe *programu Microsoft Online Services Program*. Konto to jest tworzone podczas rejestrowania się na platformie Azure za pomocą witryny internetowej platformy Azure. Na przykład jeśli masz [konto z płatnością zgodnie z rzeczywistym użyciem](https://azure.microsoft.com/offers/ms-azr-0003p/) lub [subskrypcję programu Visual Studio](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/).

Faktury dla [kont bezpłatnych na platformie Azure](https://azure.microsoft.com/offers/ms-azr-0044p/) są tworzone tylko w przypadku przekroczenia kwoty miesięcznych środków.

Od dnia zasubskrybowania platformy Azure musi upłynąć więcej niż 30 dni. Na platformie Azure opłaty są naliczane na koniec okresu fakturowania.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

- Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).

## <a name="compare-invoiced-charges-with-usage-file"></a>Porównywanie zafakturowanych opłat z plikiem użycia

<a name="charges"></a>

Pierwszym krokiem w celu porównania użycia i kosztów jest pobranie faktury i pliku użycia. Plik CSV ze szczegółami użycia przedstawia opłaty według okresu rozliczeniowego i dziennego użycia. Nie zawiera on żadnych informacji podatkowych. Aby pobrać pliki, musisz być administratorem konta lub mieć rolę właściciela.

W witrynie Azure Portal wpisz *subskrypcje* w polu wyszukiwania, a następnie kliknij pozycję [Subskrypcje](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

![Przechodzenie do subskrypcji](./media/review-individual-bill/navigate-subscriptions.png)

Na liście subskrypcji kliknij daną subskrypcję.

W obszarze **Rozliczenia**kliknij pozycję **Faktury**.

Na liście faktur poszukaj faktury, którą chcesz pobrać, a następnie kliknij symbol pobierania. Może zajść potrzeba zmiany przedziału czasu, aby wyświetlić starsze faktury. Generowanie pliku szczegółów użycia i faktury może potrwać kilka minut.

![Zrzut ekranu pokazujący okresy rozliczeniowe, opcję pobierania i łączne opłaty za każdy okres rozliczeniowy](./media/review-individual-bill/download-invoice.png)

W oknie Pobierz użycie i opłaty kliknij pozycję **Pobierz plik CSV** oraz pozycję **Pobierz fakturę**.

![Zrzut ekranu przedstawiający stronę Pobieranie faktur i danych użycia](./media/review-individual-bill/usageandinvoice.png)

W przypadku wyświetlenia komunikatu **Niedostępne** istnieje kilka powodów, dla których nie można wyświetlić szczegółów użycia lub faktury:

- Od dnia zasubskrybowania platformy Azure upłynęło mniej niż 30 dni.
- Brak zarejestrowanego użycia dla danego okresu rozliczeniowego.
- Faktura nie została jeszcze wygenerowana. Poczekaj na zakończenie okresu rozliczeniowego.
- Nie masz uprawnień do wyświetlania faktur. Stare faktury mogą nie być widoczne, jeśli nie jesteś administratorem konta. Aby dowiedzieć się więcej na temat uzyskiwania dostępu do informacji dotyczących rozliczeń, zobacz [Manage access to Azure billing using roles (Zarządzanie dostępem do rozliczeń platformy Azure przy użyciu ról)](../manage/manage-billing-access.md).
- Jeśli masz bezpłatną wersję próbną lub niewykorzystane miesięczne środki w ramach subskrypcji, nie otrzymasz faktury, chyba że masz umowę klienta firmy Microsoft.

Następnie przejrzyj opłaty. Faktura zawiera wartości podatków i opłaty za użycie.

![Przykładowa faktura platformy Azure](./media/review-individual-bill/invoice-usage-charge.png)

Otwórz pobrany plik CSV użycia. Na końcu pliku zsumuj wartość wszystkich elementów w kolumnie *Cost* (Koszt).

![Przykładowy plik użycia z zsumowanymi kosztami](./media/review-individual-bill/usage-file-usage-charges.png)

 Zsumowana wartość kolumny *Cost* (Koszt) powinna odpowiadać dokładnie kosztom *opłat za użycie* z faktury.

Opłaty za użycie są wyświetlane na poziomie miernika. Poniższe warunki oznaczają to samo zarówno w przypadku faktury, jak i pliku ze szczegółami użycia. Na przykład cykl rozliczeniowy na fakturze jest taki sam jak okres rozliczeniowy wyświetlany w pliku ze szczegółami użycia.

| Faktura (PDF) | Szczegóły użycia (CSV)|
| --- | --- |
|Cykl rozliczeń | BillingPeriodStartDate BillingPeriodEndDate |
|Nazwa |Kategoria miernika |
|Typ |Podkategoria miernika |
|Zasób |MeterName |
|Region |MeterRegion |
|Zużyte | Liczba |
|Dołączono |Uwzględniona ilość |
|Płatne |Ilość nadwyżkowego użycia |
|Stawka | EffectivePrice|
| Wartość | Koszty |

Sekcja **Opłaty za użycie** Twojej faktury zawiera łączną wartość (koszt) poszczególnych mierników zużytą w okresie rozliczeniowym. Na przykład na poniższej ilustracji przedstawiono opłaty za użycie usługi Azure Storage dla zasobu *P10 Disks*.

![Fakturowanie opłat za użycie](./media/review-individual-bill/invoice-usage-charges.png)

W pliku CSV użycia filtruj według wartości *MeterName* dla odpowiedniego zasobu wyświetlanego na fakturze. Następnie zsumuj wartość *Cost* (Koszt) dla elementów w kolumnie. Oto przykład.

![Zsumowana wartość pliku użycia dla MeterName](./media/review-individual-bill/usage-file-usage-charge-resource.png)

Zsumowana wartość kolumny *Cost* (Koszt) powinna odpowiadać dokładnie kosztom *opłat za użycie* dla pojedynczego zasobu naliczonym na fakturze.

Aby uzyskać więcej informacji, zobacz [Informacje dotyczące faktury za użycie platformy Azure](understand-invoice.md) i [Informacje dotyczące szczegółów użycia platformy Azure](understand-usage.md).

## <a name="compare-charges-and-usage-in-cost-analysis"></a>Porównywanie opłat i użycia w analizie kosztów

Analiza kosztów w witrynie Azure Portal może również pomóc w sprawdzeniu opłat. Aby uzyskać dostęp do krótkiego przeglądu zafakturowanego użycia i opłat, wybierz swoją subskrypcję na [stronie Subskrypcje](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) w witrynie Azure Portal. Kliknij pozycję **Analiza kosztów**, a następnie na liście widoków kliknij pozycję **Szczegóły faktury**.

![Przykład pokazujący wybór pozycji Szczegóły faktury](./media/review-individual-bill/cost-analysis-select-invoice-details.png)

Następnie na liście zakresów dat wybierz okres dla faktury. Dodaj filtr dla numeru faktury, a następnie wybierz wartość InvoiceNumber odpowiadającą tej na fakturze. Analiza kosztów pokazuje szczegóły kosztów zafakturowanych elementów.

![Przykład pokazujący szczegóły zafakturowanych kosztów w analizie kosztów](./media/review-individual-bill/cost-analysis-service-usage-charges.png)

Koszty pokazane w analizie kosztów powinny dokładnie odpowiadać kosztom *opłat za użycie* dla pojedynczego zasobu naliczonym na fakturze.

![Fakturowanie opłat za użycie](./media/review-individual-bill/invoice-usage-charges.png)

## <a name="external-services-billed-separately"></a><a name="external"></a>Usługi zewnętrzne rozliczane oddzielnie

Opłaty za usługi zewnętrzne lub korzystanie z portalu Marketplace dotyczą zasobów utworzonych przez dostawców oprogramowania innych firm. Te zasoby są dostępne do użycia w portalu Azure Marketplace. Na przykład zapora Barracuda to zasób portalu Azure Marketplace oferowany przez inną firmę. Wszystkie opłaty za zaporę i odpowiadające jej mierniki są wyświetlane jako opłaty za usługi zewnętrzne.

Opłaty za usługi zewnętrzne są rozliczane osobno. Opłaty nie są wyświetlane na fakturze za korzystanie z platformy Azure. Aby dowiedzieć się więcej, zobacz temat [Understand your Azure external service charges](understand-azure-marketplace-charges.md) (Informacje dotyczące opłat za usługi zewnętrzne platformy Azure).

### <a name="resources-billed-by-usage-meters"></a>Zasoby rozliczane według mierników użycia

Na platformie Azure nie są bezpośrednio naliczane opłaty na podstawie kosztu zasobu. Opłaty za zasób są obliczane przy użyciu co najmniej jednego miernika. Mierniki są używane do śledzenia użycia zasobu przez cały okres istnienia. Te mierniki są następnie używane do obliczania rachunku.

Podczas tworzenia pojedynczego zasobu platformy Azure, takiego jak maszyna wirtualna, zostanie utworzone co najmniej jedno wystąpienie miernika. Mierniki służą do śledzenia użycia zasobu w czasie. Każdy miernik emituje rekordy użycia, które są używane przez platformę Azure do obliczania rachunku.

Na przykład pojedyncza maszyna wirtualna utworzona na platformie Azure może mieć utworzone następujące mierniki do śledzenia jej użycia:

- Godziny obliczeniowe
- Godziny korzystania z adresu IP
- Transfer danych przychodzących
- Transfer danych wychodzących
- Dysk zarządzany w warstwie Standardowa
- Operacje na dyskach zarządzanych w warstwie Standardowa
- Standardowe we/wy — dysk
- Standardowe we/wy — odczyt dla blokowych obiektów blob
- Standardowe we/wy — zapis dla blokowych obiektów blob
- Standardowe we/wy — usuwanie dla blokowych obiektów blob

Po utworzeniu maszyny wirtualnej każdy miernik rozpoczyna emitowanie rekordów użycia. To użycie i cena miernika są śledzone w systemie pomiaru platformy Azure.

Liczniki, które zostały użyte do obliczenia rachunku, są pokazywane w pliku CSV użycia.

## <a name="pay-your-bill"></a><a name="payment"></a>Płatność za rachunek

W przypadku skonfigurowania karty kredytowej jako formy płatności opłata zostanie naliczona automatycznie w ciągu 10 dni od zakończenia okresu rozliczeniowego. Na wyciągu z karty kredytowej będzie to uwzględnione jako **MSFT Azure**.

Aby zmienić kartę kredytową, która będzie obciążana, zobacz temat [Add, update, or remove a credit card for Azure](../manage/change-credit-card.md) (Dodawanie, aktualizowanie lub usuwanie karty kredytowej dla platformy Azure).

W przypadku [płatności przy użyciu faktury](../manage/pay-by-invoice.md) wyślij płatność do lokalizacji wskazanej na dole faktury.

Aby sprawdzić stan płatności, [utwórz bilet pomocy technicznej](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Porównywanie zafakturowanych opłat z plikiem użycia
> * Porównywanie opłat i użycia w analizie kosztów

Ukończ przewodnik Szybki start, aby rozpocząć korzystanie z analizy kosztów.

> [!div class="nextstepaction"]
> [Rozpoczęcie analizy kosztów](../costs/quick-acm-cost-analysis.md)
