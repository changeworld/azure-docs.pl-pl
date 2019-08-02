---
title: Informacje o rachunku Azure
description: Dowiedz się, jak czytać i zrozumieć użycie i rozliczanie za subskrypcję platformy Azure.
author: bandersmsft
manager: dougeby
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/28/2019
ms.author: banders
ms.openlocfilehash: 51143644a62a77a61c4540d9f2ad3dce401c496b
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/29/2019
ms.locfileid: "68610140"
---
# <a name="understand-your-microsoft-azure-bill"></a>Zapoznaj się z Microsoft Azure rachunku
Aby zrozumieć swój rachunek za korzystanie z platformy Azure, porównaj swoją fakturę ze szczegółowym plikiem dziennego użycia i raportami dotyczącymi zarządzania kosztami w Azure Portal.

Ten artykuł nie dotyczy następujących klientów:
- Klienci platformy Azure z Umowa Enterprise (klienci z umowami EA). Jeśli jesteś klientem z umową EA, zobacz [Opis rachunku dla klientów platformy Azure z Umowa Enterprise](billing-understand-your-bill-ea.md).
- Klienci platformy Azure z [umową klienta firmy Microsoft](#check-access-to-a-microsoft-customer-agreement). Jeśli masz umowę klienta firmy Microsoft, zobacz temat informacje o opłatach za [Korzystanie z platformy Azure na fakturze umowy dla klientów firmy Microsoft](billing-mca-understand-your-bill.md).

Aby dowiedzieć się, jak działa rozliczenia w programie Azure Cloud Solution Provider (CSP), w tym cykl rozliczeń, cennik i użycie, zobacz [Omówienie rozliczeń CSP platformy Azure](/azure/cloud-solution-provider/billing/azure-csp-billing-overview/).

## <a name="charges"></a>Przejrzyj opłaty

>[!VIDEO https://www.youtube.com/embed/3YegFD769Pk]

Jeśli na fakturze jest naliczana opłata za więcej informacji, można porównać użycie i koszty za pomocą pliku użycia lub Azure Portal.

### <a name="option-1-compare-usage-and-costs-with-usage-file"></a>Option 1: Porównanie użycia i kosztów z plikiem użycia

Szczegółowy plik CSV użycia przedstawia opłaty według okresu rozliczeniowego i dziennego użycia. Aby pobrać lub wyświetlić plik, zobacz artykuł [pobieranie faktury dotyczącej rozliczeń i dziennego użycia danych na platformie Azure](billing-download-azure-invoice-daily-usage-date.md).

Opłaty za użycie są wyświetlane na poziomie licznika. Poniższe warunki oznaczają to samo zarówno w przypadku faktury, jak i pliku użycia szczegółowego. Na przykład cykl rozliczeniowy na fakturze jest taki sam jak okres rozliczeniowy wyświetlany w szczegółowym pliku użycia.

 | Faktura (PDF) | Szczegółowe użycie (CSV)|
 | --- | --- |
|Cykl rozliczeń | Okres rozliczeniowy |
 |Name (Nazwa) |Kategoria miernika |
 |Type |Podkategoria miernika |
 |Resource |Nazwa miernika |
 |Region |Region miernika |
 |Użyto |Zużyta ilość |
 |Dołączono |Uwzględniona ilość |
 |Płatne |Ilość nadwyżkowego użycia |

Sekcja **opłaty za użycie** w ramach faktury pokazuje łączną wartość dla każdego licznika, który został zużyty w okresie rozliczeniowym. Na przykład na poniższej ilustracji przedstawiono opłaty za użycie usługi Azure Scheduler.

![Opłaty za użycie faktury](./media/billing-understand-your-bill/1.png)

Sekcja **instrukcji** w SZCZEGÓŁOWYm woluminie CSV zawiera ten sam koszt. Kwota *zużyta* i *wartość* są zgodne z fakturą.

![Opłaty za użycie woluminu CSV](./media/billing-understand-your-bill/2.png)

Aby wyświetlić dzienny podział opłaty, przejdź do sekcji **codzienne użycie** pliku CSV. Filtr *harmonogramu* w obszarze *Kategoria miernika*. Można sprawdzić, które dni użycia licznika i ile było zużyte. Informacje o zasobach i *grupach zasobów* są również widoczne do porównania. *Wykorzystane* wartości powinny być dodawane do i zgodne z informacjami wyświetlanymi na fakturze.

![Sekcja codziennego użycia w woluminie CSV](./media/billing-understand-your-bill/3.png)

Aby uzyskać koszt dziennie, pomnóż zużyte kwoty z wartością *stawki* z sekcji **instrukcji** .

Aby dowiedzieć się więcej, zobacz:

- [Zapoznaj się z fakturą platformy Azure](billing-understand-your-invoice.md)
- [Poznaj szczegółowe informacje na temat użycia platformy Azure](billing-understand-your-invoice.md)

### <a name="option-2-compare-the-usage-and-costs-in-the-azure-portal"></a>Opcja 2: Porównaj użycie i koszty w Azure Portal

Azure Portal może również pomóc w sprawdzeniu opłat. Aby zapoznać się z krótkim omówieniem użycia i opłatami, zapoznaj się z wykresami zarządzania kosztami.

1. W Azure Portal przejdź do pozycji [subskrypcje](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Wybierz swoją subskrypcję > **Analiza kosztów**.
1. Filtruj wedługprzedziału czasu.
1. Aby kontynuować poprzedni przykład, zostanie wyświetlona opłata za użycie usługi Azure Scheduler.

   ![Widok analizy kosztów w Azure Portal](./media/billing-understand-your-bill/4.png)

1. Wybierz wiersz, w którym jest wyświetlana opłata za dzienny podział kosztów.

   ![Widok historii kosztów w Azure Portal](./media/billing-understand-your-bill/5.png)

Aby dowiedzieć się więcej, zobacz [zapobieganie nieoczekiwanym kosztom w rozliczeniach i zarządzaniu kosztami platformy Azure](billing-getting-started.md#costs).

## <a name="external"></a>Usługi zewnętrzne rozliczane oddzielnie

Usługi zewnętrzne lub opłaty za witrynę Marketplace dotyczą zasobów utworzonych przez dostawców oprogramowania innych firm. Te zasoby są dostępne do użycia w portalu Azure Marketplace. Na przykład Zapora Barracuda to zasób portalu Azure Marketplace oferowany przez inną firmę. Wszystkie opłaty za zaporę i odpowiadające im liczniki są wyświetlane jako opłaty za usługi zewnętrzne.

Opłaty za usługi zewnętrzne są rozliczane osobno. Opłaty nie są wyświetlane na fakturze platformy Azure. Aby dowiedzieć się więcej, zobacz [Omówienie opłat za usługi zewnętrzne platformy Azure](billing-understand-your-azure-marketplace-charges.md).

## <a name="resources-billed-by-usage-meters"></a>Zasoby rozliczane według liczników użycia

Na platformie Azure nie są naliczane opłaty bezpośrednio na podstawie kosztu zasobu. Opłaty za zasób są obliczane przy użyciu co najmniej jednego miernika. Liczniki są używane do śledzenia użycia zasobów przez cały okres istnienia. Te liczniki są następnie używane do obliczania rachunku.

Na przykład podczas tworzenia pojedynczego zasobu platformy Azure, takiego jak maszyna wirtualna, zostanie utworzonych co najmniej jedno wystąpienie miernika. Liczniki są używane do śledzenia użycia zasobu w czasie. Każdy licznik emituje rekordy użycia, które są używane przez platformę Azure do obliczania rachunku.

Na przykład Pojedyncza maszyna wirtualna utworzona na platformie Azure może mieć następujące liczniki do śledzenia użycia:

- Godziny obliczeniowe
- Godziny korzystania z adresu IP
- Transfer danych przychodzących
- Transfer danych wychodzących
- Dysk zarządzany w warstwie Standardowa
- Operacje na dyskach zarządzanych w warstwie Standardowa
- Standardowe we/wy — dysk
- Standardowe we/wy — odczyt blokowych obiektów BLOB
- Standardowe we/wy — zapisywanie blokowych obiektów BLOB
- Standardowe we/wy — usuwanie blokowych obiektów BLOB

Po utworzeniu maszyny wirtualnej każdy licznik rozpoczyna emitowanie rekordów użycia. To użycie i cena miernika są śledzone w systemie pomiarów platformy Azure.

## <a name="payment"></a>Płatność za rachunek

W przypadku skonfigurowania karty kredytowej jako formy płatności opłata zostanie naliczona automatycznie w ciągu 10 dni od zakończenia okresu rozliczeniowego. W zestawie danych na karcie kredytowej element Line będzie miał wartość **MSFT platformy Azure**.

Aby zmienić kartę kredytową, która jest naliczana, zobacz [Dodawanie, aktualizowanie lub usuwanie karty kredytowej na platformie Azure](billing-how-to-change-credit-card.md).

Jeśli [płacisz według faktury](billing-how-to-pay-by-invoice.md), Wyślij płatność do lokalizacji wymienionej w dolnej części faktury.

Aby sprawdzić stan płatności, [Utwórz bilet pomocy technicznej](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).


## <a name="tips-for-cost-management"></a>Porady dotyczące zarządzania kosztami

- Oszacuj koszty przy użyciu:
  - [Kalkulator cen platformy Azure](https://azure.microsoft.com/pricing/calculator/)
  - [Kalkulator łącznego kosztu posiadania](https://aka.ms/azure-tco-calculator)
  - [Szczegółowe informacje o cenach dla każdej usługi](https://azure.microsoft.com/pricing/)
- [Regularnie Przeglądaj swoje użycie i koszty w Azure Portal](billing-getting-started.md#costs)

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Sprawdzanie dostępu do umowy klienta firmy Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [Utwórz żądanie pomocy technicznej](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="learn-more"></a>Dowiedz się więcej

- [Pobierz fakturę rozliczeń na platformę Azure i dane dziennego użycia](billing-download-azure-invoice-daily-usage-date.md)
- [Zrozumienie warunków dotyczących faktury Microsoft Azure](billing-understand-your-invoice.md)
- [Poznanie terminów na Microsoft Azure szczegółowym użyciu](billing-understand-your-usage.md)
- [Azure Portal zarządzanie kosztami](https://docs.microsoft.com/azure/billing/billing-getting-started)
- [Zapobiegaj nieoczekiwanym kosztom rozliczeń i zarządzania kosztami platformy Azure](billing-getting-started.md#costs)
