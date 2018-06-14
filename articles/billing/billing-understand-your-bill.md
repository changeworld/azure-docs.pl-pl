---
title: Zrozumienie rachunku dla platformy Azure
description: Dowiedz się, jak i dokładnie zapoznać się z użycia i Kwota rachunku dla subskrypcji platformy Azure
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
ms.date: 08/31/2017
ms.author: tonguyen
ms.openlocfilehash: f3e0e3eeab88ad8ad0c4a21eb69a6340dbbe0441
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2018
ms.locfileid: "33204895"
---
# <a name="understand-your-bill-for-microsoft-azure"></a>Opis zawartości rachunku za korzystanie z platformy Microsoft Azure
Aby zrozumieć rachunku Azure, porównaj faktury z szczegółowe codzienne pliku użycia i kosztów zarządzania raportów w portalu Azure.

>[!NOTE]
>W tym artykule nie dotyczą klientów Enterprise Agreement (EA). Jeśli klient EA [faktury dokumentację można znaleźć w witrynie Enterprise Portal.](https://ea.azure.com/helpdocs/understandingYourInvoice)  

Aby uzyskać plik PDF faktury i kopię szczegółowe codziennego użycia pliku CSV pobieranie, zobacz [uzyskać Azure rozliczeń faktury i dziennego użycia danych](billing-download-azure-invoice-daily-usage-date.md). 

Szczegółowe warunki i opisy faktury i szczegółowe pliku codziennego użycia, zobacz [zrozumieć warunki na fakturę Microsoft Azure](billing-understand-your-invoice.md) i [użycia szczegółowe omówienie warunki na platformy Microsoft Azure](billing-understand-your-usage.md). 

Aby uzyskać więcej informacji dotyczących raportów zarządzania koszt, zobacz [portalu Azure koszt zarządzania](https://docs.microsoft.com/azure/billing/billing-getting-started).

## <a name="charges"></a>Jak utworzyć się, że opłaty w fakturą są poprawne?

>[!VIDEO https://www.youtube.com/embed/3YegFD769Pk]

Jeśli Twoja faktura, który ma więcej szczegółów na jest opłat, istnieje kilka opcji.

### <a name="option-1-review-your-invoice-and-compare-the-usage-and-costs-with-the-detailed-usage-csv-file"></a>Opcja 1: Fakturze i porównania użycia i kosztów z pliku CSV szczegółowe dane użycia

Szczegółowe dane użycia pliku CSV zawiera Twoje opłat okresie rozliczeniowym i dziennego użycia. Aby uzyskać szczegółowe dane użycia pliku CSV, zobacz [uzyskać Azure rozliczeń faktury i dziennego użycia danych](https://docs.microsoft.com/azure/billing/billing-download-azure-invoice-daily-usage-date).

Twoje opłaty za użycie są wyświetlane na poziomie miernika. Poniższe terminy oznaczają to samo w faktury i szczegółowe dane użycia pliku. Na przykład cyklu rozliczeniowego na fakturze jest odpowiednikiem okresie rozliczeniowym wyświetlane szczegółowe dane użycia pliku.

 | Faktura (PDF) | Szczegółowe dane użycia (CSV)|
 | --- | --- |
|Cykl rozliczeń | Okres rozliczeniowy |
 |Name (Nazwa) |Kategoria miernika |
 |Typ |Licznik podkategorii |
 |Zasób |Nazwa miernika |
 |Region |Region miernika |
 |Zużyte |Zużyta ilość |
 |Dołączono |Uwzględniona ilość |
 |Płatne |Ilość nadwyżkowego użycia |

**Opłaty za użycie** części faktury ma wartość dla każdego licznika, który został wykorzystany okresie rozliczeniowym. Na przykład poniższy zrzut ekranu przedstawia opłat użycia usługi Azure harmonogramu.

![Opłaty za użycie faktury](./media/billing-understand-your-bill/1.png)

**Instrukcji** sekcji szczegółowe użycie CSV zawiera samej opłat. Zarówno *zużyto* kwota i *wartość* odpowiada faktury.

![Opłaty za użycie CSV](./media/billing-understand-your-bill/2.png)

Aby zobaczyć podział dodatkowego codziennie, przejdź do **dzienne dane dotyczące użycia** sekcji CSV. Filtruj "Harmonogramu" w obszarze *kategorii licznika* i sprawdzić, które dni użyto licznik i ile został wykorzystany. *Zasobów* i *grupy zasobów* informacji znajduje się także do porównania. *Zużyto* wartości powinny sumują się do co przedstawiono na fakturze.

![Dzienne użycie części CSV](./media/billing-understand-your-bill/3.png)

Aby uzyskać koszt na dzień, należy pomnożyć *zużyto* kwoty z *szybkość* wartość z **instrukcji** sekcji.

Aby dowiedzieć się więcej na temat faktury, zobacz [zrozumieć faktura Azure](billing-understand-your-invoice.md).

Aby dowiedzieć się więcej na temat każdej kolumny w woluminie CSV, zobacz [określić sposób użycia szczegółowe Azure](billing-understand-your-invoice.md).

### <a name="option-2-review-your-invoice-and-compare-with-the-usage-and-costs-in-the-azure-portal"></a>Opcja 2: Przejrzyj faktury i porównaj z użycia i kosztów w portalu Azure

Portalu Azure ułatwia również zweryfikować Twojego opłat. Portalu Azure zapewnia szybki przegląd z użycia i opłat na fakturę kosztów zarządzania wykresy.

Aby kontynuować z powyższego przykładu, odwiedź stronę [subskrypcji](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade), wybierz subskrypcję, a następnie wybierz pozycję **analizy kosztów**. Z tego miejsca można określić przedział czasu i widoczne użycia usługi Azure harmonogramu.

![Widok analizy kosztów w portalu Azure](./media/billing-understand-your-bill/4.png)

Aby wyświetlić zestawienie kosztów w **historii kosztów**, kliknij wiersz.

![Wyświetl historię kosztów w portalu Azure](./media/billing-understand-your-bill/5.png)

Aby dowiedzieć się więcej, zobacz [uniknąć kosztów nieoczekiwany rozliczenia Azure i kosztów zarządzania](billing-getting-started.md#costs).

## <a name="external"></a>Informacje o zewnętrznych opłaty za usługę?
Usług zewnętrznych (znanej także jako portalu Azure Marketplace zamówienia) są dostarczane przez dostawców usługi niezależne i są rozliczane oddzielnie. Opłaty nie wyświetlone na faktura platformy Azure. Aby dowiedzieć się więcej, zobacz [zrozumieć sieci Azure koszty usługi zewnętrzne](billing-understand-your-azure-marketplace-charges.md).

## <a name="payment"></a>Jak dokonać płatności?

Jeśli konfigurujesz kartą kredytową lub debetową jako metody płatności, płatność jest pobierana automatycznie w ciągu 10 dni, po zakończeniu okresu rozliczeniowego. W instrukcji karty kredytowej powiedzieć czy pozycja **MSFT Azure**.

Jeśli użytkownik [płać za fakturowania](billing-how-to-pay-by-invoice.md), Wyślij płatność do lokalizacji podanej w dolnej części faktury. Aby uzyskać Pomoc [się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="how-do-i-check-the-status-of-a-payment-made-by-credit-card"></a>Jak sprawdzić stan płatności kartą kredytową?

[Tworzenie biletu pomocy technicznej](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) poprosić o stan płatności. 

## <a name="tips-for-cost-management"></a>Wskazówki dotyczące zarządzania koszt
- Szacowanie kosztów za pomocą [Kalkulator cen](https://azure.microsoft.com/pricing/calculator/) i [całkowity koszt posiadania kalkulatora](https://aka.ms/azure-tco-calculator)i uzyskać [szczegółowych informacji o cenach dla każdej usługi](https://azure.microsoft.com/pricing/).
- [Konfigurowanie alertów rozliczeń](billing-set-up-alerts.md).
- [Przejrzyj użycia i kosztów regularnie w portalu Azure](billing-getting-started.md#costs).

## <a name="need-help-contact-support"></a>Potrzebujesz pomocy? Skontaktuj się z pomocą techniczną.

Jeśli nadal potrzebujesz pomocy, [się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) uzyskać szybkie rozwiązanie problemu.
