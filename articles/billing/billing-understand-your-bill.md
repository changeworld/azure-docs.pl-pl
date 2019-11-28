---
title: Informacje o rachunku Azure
description: Dowiedz się, jak czytać i zrozumieć dane użycia oraz rachunek dotyczące subskrypcji platformy Azure.
author: bandersmsft
manager: dougeby
tags: billing
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: 9486d56a723bb311c05ab7aa776060dfa9561aae
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74223028"
---
# <a name="understand-your-microsoft-azure-bill"></a>Informacje o rachunku Microsoft Azure
Aby zrozumieć zawartość rachunku za korzystanie z platformy Azure, porównaj fakturę z zawartością pliku szczegółowego dziennego użycia i raportami zarządzania kosztami w witrynie Azure Portal.

Ten artykuł nie dotyczy następujących klientów:
- Klienci platformy Azure z umową Enterprise Agreement (klienci z umową EA). Jeśli jesteś klientem z umową EA, zobacz [Opis zawartości rachunku dla klientów platformy Azure z umową Enterprise Agreement](billing-understand-your-bill-ea.md).
- Klienci platformy Azure z [umową klienta firmy Microsoft](#check-access-to-a-microsoft-customer-agreement). Jeśli masz umowę klienta firmy Microsoft, zobacz temat [Understand the Azure charges on your Microsoft Customer Agreement invoice](billing-mca-understand-your-bill.md) (Informacje o opłatach za korzystanie z platformy Azure na fakturze do umowy klienta firmy Microsoft).

Sposób działania rozliczeń w programie Azure Cloud Solution Provider (Azure CSP), z uwzględnieniem cyklu rozliczeniowego, cen i użycia, wyjaśniono w temacie [Przegląd rozliczeń w programie Azure CSP](/azure/cloud-solution-provider/billing/azure-csp-billing-overview/).

## <a name="charges"></a>Przeglądanie opłat

>[!VIDEO https://www.youtube.com/embed/3YegFD769Pk]

Jeśli na fakturze podano opłatę, na temat której chcesz uzyskać więcej informacji, możesz porównać użycie i koszty za pomocą pliku użycia lub witryny Azure Portal.

### <a name="option-1-compare-usage-and-costs-with-usage-file"></a>Opcja 1: Porównanie użycia i kosztów z plikiem użycia

Plik CSV ze szczegółami użycia przedstawia opłaty według okresu rozliczeniowego i dziennego użycia. Aby pobrać i wyświetlić plik, zobacz temat [Get your Azure billing invoice and daily usage data](billing-download-azure-invoice-daily-usage-date.md) (Pobieranie faktury rozliczeniowej i danych dziennego użycia platformy Azure).

Opłaty za użycie są wyświetlane na poziomie miernika. Poniższe warunki oznaczają to samo zarówno w przypadku faktury, jak i pliku ze szczegółami użycia. Na przykład cykl rozliczeniowy na fakturze jest taki sam jak okres rozliczeniowy wyświetlany w pliku ze szczegółami użycia.

 | Faktura (PDF) | Szczegóły użycia (CSV)|
 | --- | --- |
|Cykl rozliczeń | Okres rozliczeniowy |
 |Name (Nazwa) |Kategoria miernika |
 |Typ |Podkategoria miernika |
 |Zasób |Nazwa miernika |
 |Region |Region miernika |
 |Zużyte |Zużyta ilość |
 |Dołączono |Uwzględniona ilość |
 |Płatne |Ilość nadwyżkowego użycia |

Sekcja **Opłaty za użycie** Twojej faktury zawiera łączną wartość poszczególnych mierników zużytą w okresie rozliczeniowym. Na przykład na poniższej ilustracji przedstawiono opłaty za użycie usługi Azure Scheduler.

![Fakturowanie opłat za użycie](./media/billing-understand-your-bill/1.png)

Sekcja **Wyciąg** pliku CSV ze szczegółami użycia zawiera taką samą opłatę. Zarówno kwota *Użyto* jak i *Wartość* są takie same jak na fakturze.

![Opłaty za użycie w pliku CSV](./media/billing-understand-your-bill/2.png)

Aby wyświetlić dzienny podział opłaty, przejdź do sekcji **Dzienne użycie** w pliku CSV. Filtruj pod kątem pozycji *Scheduler* w obszarze *Kategoria miernika*. Można sprawdzić, w które dni miernik był używany oraz ile zostało zużyte. Dla porównania są również wyświetlane informacje dotyczące pozycji *Zasób* i *Grupa zasobów*. Wartości *Zużyto* powinny składać się na to, co wykazano na fakturze, oraz temu odpowiadać.

![Sekcja Dzienne użycie w pliku CSV](./media/billing-understand-your-bill/3.png)

Aby uzyskać dzienny koszt, pomnóż kwoty *Zużyto* z wartością *Stawka* z sekcji **Wyciąg**.

Aby dowiedzieć się więcej, zobacz:

- [Informacje dotyczące faktury za użycie platformy Azure](billing-understand-your-invoice.md)
- [Informacje dotyczące szczegółów użycia platformy Azure](billing-understand-your-invoice.md)

### <a name="option-2-compare-the-usage-and-costs-in-the-azure-portal"></a>Opcja 2: Porównanie użycia i kosztów w witrynie Azure Portal

Witryna Azure Portal może również pomóc w sprawdzeniu opłat. Aby uzyskać dostęp do krótkiego przeglądu zafakturowanego użycia i opłat, wyświetl wykresy dotyczące zarządzania kosztami.

1. W witrynie Azure Portal przejdź do sekcji [Subskrypcje](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Wybierz swoją subskrypcję > **Analiza kosztów**.
1. Filtruj przy użyciu kategorii **Przedział czasu**.
1. Aby kontynuować poprzedni przykład, zostanie wyświetlona opłata za użycie usługi Azure Scheduler.

   ![Widok analizy kosztów w witrynie Azure Portal](./media/billing-understand-your-bill/4.png)

1. Wybierz wiersz, w którym jest wyświetlana opłata, aby zobaczyć podział dziennych kosztów.

   ![Widok historii kosztów w witrynie Azure Portal](./media/billing-understand-your-bill/5.png)

Aby dowiedzieć się więcej, zobacz [sekcję dotyczącą zapobieganiu powstawaniu nieoczekiwanych kosztów w rozliczeniach platformy Azure i zarządzania kosztami](billing-getting-started.md#costs).

## <a name="external"></a>Usługi zewnętrzne rozliczane oddzielnie

Opłaty za usługi zewnętrzne lub korzystanie z portalu Marketplace dotyczą zasobów utworzonych przez dostawców oprogramowania innych firm. Te zasoby są dostępne do użycia w portalu Azure Marketplace. Na przykład zapora Barracuda to zasób portalu Azure Marketplace oferowany przez inną firmę. Wszystkie opłaty za zaporę i odpowiadające jej mierniki są wyświetlane jako opłaty za usługi zewnętrzne.

Opłaty za usługi zewnętrzne są rozliczane osobno. Opłaty nie są wyświetlane na fakturze za korzystanie z platformy Azure. Aby dowiedzieć się więcej, zobacz temat [Understand your Azure external service charges](billing-understand-your-azure-marketplace-charges.md) (Informacje dotyczące opłat za usługi zewnętrzne platformy Azure).

## <a name="resources-billed-by-usage-meters"></a>Zasoby rozliczane według mierników użycia

Na platformie Azure nie są bezpośrednio naliczane opłaty na podstawie kosztu zasobu. Opłaty za zasób są obliczane przy użyciu co najmniej jednego miernika. Mierniki są używane do śledzenia użycia zasobu przez cały okres istnienia. Te mierniki są następnie używane do obliczania rachunku.

Na przykład podczas tworzenia pojedynczego zasobu platformy Azure, takiego jak maszyna wirtualna, zostanie utworzone co najmniej jedno wystąpienie miernika. Mierniki służą do śledzenia użycia zasobu w czasie. Każdy miernik emituje rekordy użycia, które są używane przez platformę Azure do obliczania rachunku.

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

## <a name="payment"></a>Płatność za rachunek

W przypadku skonfigurowania karty kredytowej jako formy płatności opłata zostanie naliczona automatycznie w ciągu 10 dni od zakończenia okresu rozliczeniowego. Na wyciągu z karty kredytowej będzie to uwzględnione jako **MSFT Azure**.

Aby zmienić kartę kredytową, która będzie obciążana, zobacz temat [Add, update, or remove a credit card for Azure](billing-how-to-change-credit-card.md) (Dodawanie, aktualizowanie lub usuwanie karty kredytowej dla platformy Azure).

W przypadku [płatności przy użyciu faktury](billing-how-to-pay-by-invoice.md) wyślij płatność do lokalizacji wskazanej na dole faktury.

Aby sprawdzić stan płatności, [utwórz bilet pomocy technicznej](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).


## <a name="tips-for-cost-management"></a>Porady dotyczące zarządzania kosztami

- Oszacuj koszty, wykorzystując:
  - [Kalkulator cen platformy Azure](https://azure.microsoft.com/pricing/calculator/)
  - [Kalkulator całkowitego kosztu posiadania](https://aka.ms/azure-tco-calculator)
  - [Szczegółowe informacje o cenach poszczególnych usług](https://azure.microsoft.com/pricing/)
- [Regularnie przeglądaj dane użycia i koszty w witrynie Azure Portal](billing-getting-started.md#costs)

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Sprawdzanie dostępu do umowy klienta firmy Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [utwórz wniosek o pomoc techniczną](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="learn-more"></a>Dowiedz się więcej

- [Pobierz fakturę rozliczeniową za platformę Azure i dane dziennego użycia](billing-download-azure-invoice-daily-usage-date.md)
- [Omówienie terminów występujących na fakturze za platformę Microsoft Azure](billing-understand-your-invoice.md)
- [Omówienie terminów dotyczących szczegółów użycia platformy Microsoft Azure](billing-understand-your-usage.md)
- [Zarządzanie kosztami witryny Azure Portal](https://docs.microsoft.com/azure/billing/billing-getting-started)
- [Zapobieganie powstawaniu nieoczekiwanych kosztów w rozliczeniach platformy Azure i zarządzanie kosztami](billing-getting-started.md#costs)
