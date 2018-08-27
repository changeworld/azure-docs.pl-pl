---
title: Opis zawartości rachunku na platformie Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak czytać i zrozumieć swoje użycia oraz rachunek dotyczące subskrypcji platformy Azure
services: ''
documentationcenter: ''
author: tonguyen10
manager: tonguyen
editor: ''
tags: billing
ms.assetid: 32eea268-161c-4b93-8774-bc435d78a8c9
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/14/2018
ms.author: tonguyen
ms.openlocfilehash: c782cadadb0250e6c3ca4912dbf8f81e19cb88c5
ms.sourcegitcommit: ebb460ed4f1331feb56052ea84509c2d5e9bd65c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/24/2018
ms.locfileid: "42919058"
---
# <a name="understand-your-bill-for-microsoft-azure"></a>Opis zawartości rachunku za korzystanie z platformy Microsoft Azure
Aby poznać rachunku dotyczącym platformy Azure, porównaj fakturze za pomocą szczegółowych codzienne pliku użycia i raportów rozwiązania cost management w witrynie Azure portal.

>[!NOTE]
>W tym artykule nie ma zastosowania do klientów z umową Enterprise Agreement (EA). Jeśli jesteś klientem z umową EA [faktury dokumentację można znaleźć w witrynie Enterprise Portal.](https://ea.azure.com/helpdocs/understandingYourInvoice)  

Aby uzyskać plik PDF fakturze i kopiowania szczegółowe dzienne użycie pliku CSV pobranego pliku, zobacz [pobrać faktury i dane dziennego użycia rozliczeniową za platformę Azure](billing-download-azure-invoice-daily-usage-date.md). 

Szczegółowy opis warunków i opisy faktury i szczegółowe codzienne plik użycia, zobacz [zrozumieć warunki na fakturze Microsoft Azure](billing-understand-your-invoice.md) i [warunki poznawanie usługi Microsoft Azure szczegółowe użycie](billing-understand-your-usage.md). 

Aby uzyskać szczegółowe informacje dotyczące raportów rozwiązania cost management, zobacz [portalu Azure cost management](https://docs.microsoft.com/azure/billing/billing-getting-started).

> [!div class="nextstepaction"]
> [Pomóż nam w usprawnianiu dokumentacja rozliczeń platformy Azure](https://go.microsoft.com/fwlink/p/?linkid=2010091)

## <a name="charges"></a>Jak upewnić się, że opłaty w mojej fakturze są poprawne?

>[!VIDEO https://www.youtube.com/embed/3YegFD769Pk]

W przypadku opłaty na fakturze, które chcesz uzyskać więcej informacji, istnieje kilka opcji.

### <a name="option-1-review-your-invoice-and-compare-the-usage-and-costs-with-the-detailed-usage-csv-file"></a>Opcja 1: Na fakturze i porównywanie kosztów i użycia ze szczegółowym zestawieniem użycia pliku CSV

Szczegóły użycia pliku CSV zawiera opłat, okres rozliczeniowy i dziennego użycia. Aby uzyskać szczegóły użycia pliku CSV, zobacz [pobrać faktury i dane dziennego użycia rozliczeniową za platformę Azure](https://docs.microsoft.com/azure/billing/billing-download-azure-invoice-daily-usage-date).

Twoje opłaty za użycie są wyświetlane na poziomie miernika. Poniższe terminy oznaczają to samo zarówno faktury, jak i plik szczegółowym zestawieniem użycia. Na przykład okresu rozliczeniowego na fakturze jest odpowiednikiem pokazano w pliku szczegółowym zestawieniem użycia okresu rozliczeniowego.

 | Faktury (PDF) | Szczegóły użycia (CSV)|
 | --- | --- |
|Cykl rozliczeń | Okres rozliczeniowy |
 |Name (Nazwa) |Kategoria miernika |
 |Typ |Podkategoria miernika |
 |Zasób |Nazwa miernika |
 |Region |Region miernika |
 |Zużyte |Zużyta ilość |
 |Dołączono |Uwzględniona ilość |
 |Płatne |Ilość nadwyżkowego użycia |

**Opłaty za użycie** sekcji fakturze z wartością całkowitą w ramach każdego miernika, która została wykorzystana w trakcie okresu rozliczeniowego. Na przykład poniższy zrzut ekranu przedstawia opłata za użycie usługi Azure Scheduler.

![Opłaty za użycie faktury](./media/billing-understand-your-bill/1.png)

**Instrukcji** sekcji ze szczegółowym zestawieniem użycia woluminów CSV zawiera tych samych opłaty. Zarówno *zużyto* kwota i *wartość* dopasowania faktury.

![Opłaty za użycie pliku CSV](./media/billing-understand-your-bill/2.png)

Aby zobaczyć podział tej opłaty, codziennie, przejdź do **dzienne użycie** sekcji CSV. Filtruj "Harmonogram" w obszarze *kategoria licznika* i zobacz, które dni użyto licznika i ile Zużyto. *Zasobów* i *grupy zasobów* informacji znajduje się również do porównania. *Zużyto* wartości należy dodać w do przedstawionego na fakturze.

![Codzienne sekcji w woluminie CSV](./media/billing-understand-your-bill/3.png)

Aby uzyskać koszt za każdy dzień, należy pomnożyć *zużyto* kwoty z *współczynnik* wartość z **instrukcji** sekcji.

Aby dowiedzieć się więcej na temat faktury, zobacz [zrozumieć Azure fakturze](billing-understand-your-invoice.md).

Aby uzyskać informacje dotyczące każdej z kolumn w woluminie CSV, zobacz [zrozumieć ze szczegółowym zestawieniem użycia platformy Azure](billing-understand-your-invoice.md).

### <a name="option-2-review-your-invoice-and-compare-with-the-usage-and-costs-in-the-azure-portal"></a>Opcja 2: Na fakturze i porównaj z użycia i kosztów w witrynie Azure portal

Witryna Azure portal może również pomóc, możesz sprawdzić opłat. Azure portal udostępnia wykresy zarządzania kosztami, aby uzyskać szybki przegląd użycia i opłat na fakturze.

Aby kontynuować pracę z powyższego przykładu, odwiedź stronę [strony subskrypcje](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade), wybierz swoją subskrypcję, a następnie wybierz **analiza kosztów**. Z tego miejsca możesz określić zakres czasu i zobacz za użycie usługi Azure Scheduler.

![Analiza kosztów widok w witrynie Azure portal](./media/billing-understand-your-bill/4.png)

Aby zobaczyć codzienne podział kosztów w **historii**, kliknij wiersz.

![Historia kosztu widok w witrynie Azure portal](./media/billing-understand-your-bill/5.png)

Aby dowiedzieć się więcej, zobacz [zapobieganie powstawaniu nieoczekiwanych kosztów za pomocą rozliczeń platformy Azure i zarządzania kosztami](billing-getting-started.md#costs).

## <a name="external"></a>Jak wygląda opłat za usługi zewnętrzne?
Zewnętrznych usług (znany także jako portalu Azure Marketplace zamówienia) są dostarczane przez dostawców niezależną usługę i są rozliczane osobno. Opłaty nie ujęte na fakturze platformy Azure. Aby dowiedzieć się więcej, zobacz [o opłatach za usługi platformy Azure zewnętrznej usługi](billing-understand-your-azure-marketplace-charges.md).

## <a name="payment"></a>Jak dokonać płatności?

Jeśli konfigurujesz kartę kredytową lub debetową jako metody płatności, płatności są naliczane opłaty, automatycznie w ciągu 10 dni, po upływie okresu rozliczeniowego. Na swoim wyciągu karty kredytowej, że element wiersza **MSFT Azure**.

Jeśli użytkownik [płatność na fakturowania](billing-how-to-pay-by-invoice.md), Wyślij płatność do lokalizacji wymienionych na dole faktury. Aby uzyskać dodatkową Pomoc [się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="how-do-i-check-the-status-of-a-payment-made-by-credit-card"></a>Jak sprawdzić stan płatności przy użyciu karty kredytowej?

[Utwórz bilet pomocy technicznej](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) poproszenie stanu płatności. 

## <a name="are-there-different-azure-customer-types-how-do-i-know-what-customer-type-i-am"></a>Istnieją typy różnych klientów platformy Azure? Jak sprawdzić, jakiego klienta jestem?
Istnieją różne typy klientów platformy Azure. Aby lepiej zrozumieć swoje ceny i zestawienie, zobacz poniższe opisy typ klienta.

- **Enterprise**: klienci korporacyjni podpisały umowę Enterprise z platformą Azure na wynegocjowanym zobowiązań pieniężnych i uzyskać dostęp do ceny niestandardowe dla zasobów platformy Azure.
- **Sieci Web bezpośrednio**: bezpośrednich klientów internetowych nie zarejestrował żadnych niestandardowych umowy z platformą Azure. Ci klienci utworzyli konto platformy Azure za pośrednictwem witryny azure.com i otrzymywać publicznych ceny umożliwiający dostęp do Internetu dla wszystkich zasobów platformy Azure.
- **Dostawca usług w chmurze**: dostawców usług w chmurze są zazwyczaj firm, które zostały wynajęte przez firmę przez klienta końcowego do tworzenia rozwiązań na platformie Azure.

## <a name="why-dont-i-see-the-cost-the-resource-i-have-created-in-my-bill"></a>Dlaczego nie widzę koszt zasobów, utworzony na rachunku?
System Azure nie, opłata nie jest bezpośrednio oparty na kosztów zasobów. Rozliczanie odbywa się na podstawie wyłączyć jeden lub więcej liczników, które są używane do śledzenia użycia zasobów, w okresie swojego istnienia. Te liczniki są następnie używane do obliczenia rachunku. Zobacz więcej informacji na temat platformy Azure pomiaru poniżej.

## <a name="how-does-azure-charge-metering-work"></a>Jak Azure opłaty pomiaru pracy?
Po paru pojedynczego zasobu platformy Azure, np. maszyna wirtualna będzie mieć jeden lub wiele wystąpień licznika również utworzyć. Te liczniki są używane do śledzenia użycia zasobu wraz z upływem czasu. Każdy licznik emituje rekordów użycia, które są następnie używane przez platformę Azure nasz koszt systemu zbierania danych do obliczenia rachunku. 

Na przykład jedna maszyna wirtualna utworzona na platformie Azure może mieć następujących liczników, utworzony w celu śledzenia jej użycie:

- Godziny obliczeniowe
- Godziny korzystania z adresu IP
- Transfer danych przychodzących
- Wychodzący transfer danych
- Dysk zarządzany w warstwie standardowa
- Operacje dysków zarządzanych w warstwie standardowa
- Standardowe We/Wy dysku
- Standardowe we/wy — blok Blob odczytu
- Standardowe we/wy Block Blob zapisu
- Usunięcie standardowe we/wy — blokowych obiektów Blob

Po utworzeniu maszyny Wirtualnej każdej z nich liczniki powyżej rozpocznie się emitowanie rekordów użycia. Użycie tych zostanie użyte w systemie zliczania platformy Azure oraz przez cenę Aby ustalić, ile klienta jest naliczana.

> [!Note]
> Liczniki przykład powyżej może być tylko podzbiór liczniki utworzono maszynę Wirtualną, który jest tworzony.

## <a name="what-is-the-difference-between-azure-1st-party-charges-and-azure-marketplace-charges"></a>Jaka jest różnica między platformą Azure dnia 1. strona opłaty za magazyn i portalu Azure Marketplace?
Azure 1. strona opłaty są naliczane za zasoby, których bezpośrednio opracowany i jest oferowane przez platformę Azure. Azure Marketplace opłaty są naliczane dla zasobów, które zostały utworzone przez producentów oprogramowania innych firm, które są dostępne za pośrednictwem portalu Azure marketplace. Na przykład zapora Barracuda jest zasobem portalu Azure marketplace oferowane przez inną firmę. Wszystkie opłaty za zaporą i jego odpowiednich liczników będzie wyświetlany jako opłatach w portalu marketplace. 

## <a name="tips-for-cost-management"></a>Wskazówki dotyczące usługi cost management
- Oszacuj koszty przy użyciu [Kalkulator cen](https://azure.microsoft.com/pricing/calculator/) i [całkowity koszt posiadania Kalkulator](https://aka.ms/azure-tco-calculator)i Uzyskaj [szczegółowe informacje o cenach informacji dla każdej usługi](https://azure.microsoft.com/pricing/).
- [Ustawianie alertów dotyczących rozliczeń](billing-set-up-alerts.md).
- [Przeglądanie użycia i kosztów regularnie w witrynie Azure portal](billing-getting-started.md#costs).

## <a name="need-help-contact-support"></a>Potrzebujesz pomocy? Skontaktuj się z pomocą techniczną.

Jeśli nadal potrzebujesz pomocy, [się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) można szybko rozwiązać swój problem.
