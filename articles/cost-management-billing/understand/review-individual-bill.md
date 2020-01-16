---
title: Przeglądanie indywidualnego rachunku na korzystanie z platformy Azure
description: Dowiedz się, jak zrozumieć użycie rachunku i zasobów oraz sprawdzić opłaty za poszczególne subskrypcje platformy Azure.
author: bandersmsft
manager: dougeby
tags: billing
ms.service: cost-management-billing
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/20/2019
ms.author: banders
ms.openlocfilehash: 1249758e5dd5b7e823c11890e57549195da22529
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75987348"
---
# <a name="tutorial-review-your-individual-azure-bill"></a>Samouczek: przeglądanie indywidualnego rachunku na platformę Azure

Ten artykuł pomaga zrozumieć i przejrzeć swój rachunek na korzystanie z platformy Azure. Dla każdego okresu rozliczeniowego zwykle otrzymujesz fakturę w wiadomości e-mail. Faktura jest reprezentacją rachunku na korzystanie z platformy Azure. Te same informacje o kosztach na fakturze są dostępne w Azure Portal. W tym samouczku porównasz fakturę ze szczegółowym plikiem dziennego użycia i analizą kosztów w Azure Portal.

Ten samouczek dotyczy tylko klientów platformy Azure z pojedynczą subskrypcją. Często indywidualnymi subskrypcjami są opłaty z opłatami zgodnie z rzeczywistym użyciem kupowane bezpośrednio w witrynie sieci Web systemu Azure.

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Porównaj zafakturowane opłaty z plikiem użycia
> * Porównaj opłaty i użycie w analizie kosztów

## <a name="prerequisites"></a>Wymagania wstępne

Musisz mieć płatne konto do rozliczeń *programu Microsoft Online Services* . Konto zostanie utworzone po zarejestrowaniu się na platformie Azure za pomocą witryny sieci Web systemu Azure. Jeśli na przykład masz [konto z stawką płatność zgodnie z rzeczywistym użyciem](https://azure.microsoft.com/offers/ms-azr-0003p/) lub jesteś [subskrybentem programu Visual Studio](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/).

Faktury dla [bezpłatnego konta platformy Azure](https://azure.microsoft.com/offers/ms-azr-0044p/) są tworzone tylko w przypadku przekroczenia kwoty miesięcznego kredytu.

Musi ona być dłuższa niż 30 dni od dnia, w którym zasubskrybujesz platformę Azure. Na platformie Azure opłaty są naliczane na koniec okresu fakturowania.

## <a name="sign-in-to-azure"></a>Zaloguj się w usłudze Azure

- Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.

## <a name="compare-invoiced-charges-with-usage-file"></a>Porównaj zafakturowane opłaty z plikiem użycia

<a name="charges"></a>

Pierwszym krokiem do porównania użycia i kosztów jest pobranie faktur i plików użycia. Plik CSV ze szczegółami użycia przedstawia opłaty według okresu rozliczeniowego i dziennego użycia. Nie zawierają one żadnych informacji podatkowych. Aby można było pobrać pliki, musisz być administratorem konta lub mieć rolę właściciela.

W Azure Portal wpisz *subskrypcje* w polu wyszukiwania, a następnie kliknij pozycję [subskrypcje](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

![Przejdź do subskrypcji](./media/review-individual-bill/navigate-subscriptions.png)

Na liście subskrypcji kliknij subskrypcję.

W obszarze **rozliczenia**kliknij pozycję **faktury**.

Poszukaj na liście faktur, które chcesz pobrać, a następnie kliknij symbol pobierania. Może zajść potrzeba zmiany wartości TimeSpan, aby wyświetlić starsze faktury. Generowanie pliku szczegółów użycia i faktury może potrwać kilka minut.

![Zrzut ekranu pokazujący okresy rozliczeniowe, opcję pobierania i łączne opłaty za każdy okres rozliczeniowy](./media/review-individual-bill/download-invoice.png)

W oknie Pobierz użycie i opłaty kliknij pozycję **Pobierz plik CSV** i **Pobierz fakturę**.

![Zrzut ekranu przedstawiający stronę Pobieranie faktur i danych użycia](./media/review-individual-bill/usageandinvoice.png)

Jeśli komunikat **nie jest dostępny** , istnieje kilka powodów, dla których nie widzisz szczegółów użycia lub faktury:

- Od dnia zasubskrybowania platformy Azure upłynęło mniej niż 30 dni.
- Nie ma użycia dla okresu rozliczeniowego.
- Nie Wygenerowano jeszcze faktury. Poczekaj na zakończenie okresu rozliczeniowego.
- Nie masz uprawnień do wyświetlania faktur. Stare faktury mogą nie być widoczne, chyba że jesteś administratorem konta. Aby dowiedzieć się więcej na temat uzyskiwania dostępu do informacji dotyczących rozliczeń, zobacz [Manage access to Azure billing using roles (Zarządzanie dostępem do rozliczeń platformy Azure przy użyciu ról)](../manage/manage-billing-access.md).
- Jeśli masz bezpłatną wersję próbną lub niewykorzystane miesięczne środki w ramach subskrypcji, nie otrzymasz faktury, chyba że masz umowę klienta firmy Microsoft.

Następnie przejrzyj opłaty. Faktura zawiera wartości podatków i opłat za użycie.

![Przykład faktury platformy Azure](./media/review-individual-bill/invoice-usage-charge.png)

Otwórz pobrany plik użycia woluminu CSV. Na końcu pliku, należy zsumować wartości dla wszystkich elementów w kolumnie *koszt* .

![Przykładowy plik użycia z zsumowanym kosztem](./media/review-individual-bill/usage-file-usage-charges.png)

 Zsumowana wartość *kosztu* powinna dokładnie pasować do kosztu *opłat za użycie* w ramach faktury.

Opłaty za użycie są wyświetlane na poziomie miernika. Poniższe warunki oznaczają to samo zarówno w przypadku faktury, jak i pliku ze szczegółami użycia. Na przykład cykl rozliczeniowy na fakturze jest taki sam jak okres rozliczeniowy wyświetlany w pliku ze szczegółami użycia.

| Faktura (PDF) | Szczegóły użycia (CSV)|
| --- | --- |
|Cykl rozliczeń | BillingPeriodStartDate BillingPeriodEndDate |
|Nazwa |Kategoria miernika |
|Typ |Podkategoria miernika |
|Zasób |MeterName |
|Region |MeterRegion |
|Zużyte | Ilość |
|Zawarte |Uwzględnione |
|Płatne |Nadwyżkowe |
|Stawka | EffectivePrice|
| Wartość | Koszt |

Sekcja **opłaty za użycie** w ramach faktury zawiera łączną wartość (koszt) dla każdego licznika, który został zużyty w okresie rozliczeniowym. Na przykład na poniższej ilustracji przedstawiono opłaty za użycie usługi Azure Storage dla zasobu *dysków P10* .

![Fakturowanie opłat za użycie](./media/review-individual-bill/invoice-usage-charges.png)

W pliku użycia woluminów CSV odfiltruj według *miernika* dla odpowiedniego zasobu wyświetlanego na fakturze. Następnie należy zsumować wartość *kosztu* dla elementów w kolumnie. Oto przykład.

![Wartość podsumowania pliku użycia dla miernika](./media/review-individual-bill/usage-file-usage-charge-resource.png)

Zsumowana wartość *kosztu* powinna dokładnie pasować do kosztu *opłat za użycie* poszczególnych zasobów naliczanych na podstawie faktury.

Aby uzyskać więcej informacji, zobacz [Omówienie faktury platformy Azure](understand-invoice.md) i [zrozumienie szczegółowego użycia platformy Azure](understand-usage.md).

## <a name="compare-charges-and-usage-in-cost-analysis"></a>Porównaj opłaty i użycie w analizie kosztów

Analiza kosztów w Azure Portal może również pomóc w sprawdzeniu opłat. Aby zapoznać się z krótkim omówieniem użycia i opłatami, wybierz swoją subskrypcję na [stronie Subskrypcje](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) w Azure Portal. Następnie kliknij pozycję **Analiza kosztów** , a następnie na liście widoki kliknij pozycję **szczegóły faktury**.

![Przykład pokazujący wybór szczegółów faktury](./media/review-individual-bill/cost-analysis-select-invoice-details.png)

Następnie na liście zakres dat wybierz okres dla faktury. Dodaj filtr dla numeru faktury, a następnie wybierz InvoiceNumber pasujący do tego, który jest zgodny z tym w fakturze. Analiza kosztów pokazuje szczegóły kosztu zafakturowanych elementów.

![Przykład pokazujący szczegóły kosztu zafakturowanego w analizie kosztów](./media/review-individual-bill/cost-analysis-service-usage-charges.png)

Koszty pokazywane w analizie kosztów powinny dokładnie pasować do kosztów *opłat za użycie* poszczególnych zasobów naliczanych na podstawie faktury.

![Fakturowanie opłat za użycie](./media/review-individual-bill/invoice-usage-charges.png)

## <a name="external"></a>Usługi zewnętrzne rozliczane oddzielnie

Opłaty za usługi zewnętrzne lub korzystanie z portalu Marketplace dotyczą zasobów utworzonych przez dostawców oprogramowania innych firm. Te zasoby są dostępne do użycia w portalu Azure Marketplace. Na przykład zapora Barracuda to zasób portalu Azure Marketplace oferowany przez inną firmę. Wszystkie opłaty za zaporę i odpowiadające jej mierniki są wyświetlane jako opłaty za usługi zewnętrzne.

Opłaty za usługi zewnętrzne są rozliczane osobno. Opłaty nie są wyświetlane na fakturze za korzystanie z platformy Azure. Aby dowiedzieć się więcej, zobacz temat [Understand your Azure external service charges](understand-azure-marketplace-charges.md) (Informacje dotyczące opłat za usługi zewnętrzne platformy Azure).

### <a name="resources-billed-by-usage-meters"></a>Zasoby rozliczane według mierników użycia

Na platformie Azure nie są bezpośrednio naliczane opłaty na podstawie kosztu zasobu. Opłaty za zasób są obliczane przy użyciu co najmniej jednego miernika. Mierniki są używane do śledzenia użycia zasobu przez cały okres istnienia. Te mierniki są następnie używane do obliczania rachunku.

Podczas tworzenia pojedynczego zasobu platformy Azure, takiego jak maszyna wirtualna, zostanie utworzonych co najmniej jedno wystąpienie miernika. Mierniki służą do śledzenia użycia zasobu w czasie. Każdy miernik emituje rekordy użycia, które są używane przez platformę Azure do obliczania rachunku.

Na przykład pojedyncza maszyna wirtualna utworzona na platformie Azure może mieć utworzone następujące mierniki do śledzenia jej użycia:

- Godziny obliczeniowe
- Godziny korzystania z adresu IP
- Transfer danych przychodzących
- Wychodzący transfer danych
- Dysk zarządzany w warstwie Standardowa
- Operacje na dyskach zarządzanych w warstwie Standardowa
- Standardowe we/wy — dysk
- Standardowe we/wy — odczyt dla blokowych obiektów blob
- Standardowe we/wy — zapis dla blokowych obiektów blob
- Standardowe we/wy — usuwanie dla blokowych obiektów blob

Po utworzeniu maszyny wirtualnej każdy miernik rozpoczyna emitowanie rekordów użycia. To użycie i cena miernika są śledzone w systemie pomiaru platformy Azure.

Liczniki, które zostały użyte do obliczenia rachunku, są wyświetlane w pliku CSV użycia.

## <a name="payment"></a>Płatność za rachunek

W przypadku skonfigurowania karty kredytowej jako formy płatności opłata zostanie naliczona automatycznie w ciągu 10 dni od zakończenia okresu rozliczeniowego. Na wyciągu z karty kredytowej będzie to uwzględnione jako **MSFT Azure**.

Aby zmienić kartę kredytową, która będzie obciążana, zobacz temat [Add, update, or remove a credit card for Azure](../manage/change-credit-card.md) (Dodawanie, aktualizowanie lub usuwanie karty kredytowej dla platformy Azure).

W przypadku [płatności przy użyciu faktury](../manage/pay-by-invoice.md) wyślij płatność do lokalizacji wskazanej na dole faktury.

Aby sprawdzić stan płatności, [utwórz bilet pomocy technicznej](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Porównaj zafakturowane opłaty z plikiem użycia
> * Porównaj opłaty i użycie w analizie kosztów

Ukończ Przewodnik Szybki Start, aby rozpocząć korzystanie z analizy kosztów.

> [!div class="nextstepaction"]
> [Rozpoczęcie analizy kosztów](../costs/quick-acm-cost-analysis.md)
