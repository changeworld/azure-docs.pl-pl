---
title: Opis zawartości rachunku na platformie Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak czytać i zrozumieć swoje użycia oraz rachunek dotyczące subskrypcji platformy Azure
services: ''
documentationcenter: ''
author: tonguyen10
manager: alherz
editor: ''
tags: billing
ms.assetid: 32eea268-161c-4b93-8774-bc435d78a8c9
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2018
ms.author: cwatson
ms.openlocfilehash: b9b1496c71e61fce815d225310c8beb57e8f9b19
ms.sourcegitcommit: 56d20d444e814800407a955d318a58917e87fe94
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/29/2018
ms.locfileid: "52584574"
---
# <a name="understand-your-bill-for-microsoft-azure"></a>Opis zawartości rachunku za korzystanie z platformy Microsoft Azure
Aby poznać rachunku dotyczącym platformy Azure, porównaj fakturze za pomocą szczegółowych codzienne pliku użycia i raportów rozwiązania cost management w witrynie Azure portal.

Ten artykuł nie dotyczy klientów platformy Azure z umową Enterprise Agreement (EA klienci). Jeśli jesteś klientem z umową EA, zobacz [opis zawartości rachunku dla klientów platformy Azure z umową Enterprise Agreement](billing-understand-your-bill-ea.md).  

## <a name="charges"></a>Przejrzyj opłat

>[!VIDEO https://www.youtube.com/embed/3YegFD769Pk]

Twoja faktura, który chcesz uzyskać więcej informacji o jest opłaty, można porównać użycia i kosztów, przy użyciu pliku użycia lub za pomocą witryny Azure portal.

### <a name="option-1-compare-usage-and-costs-with-usage-file"></a>Opcja 1: Porównania kosztów i użycia za pomocą pliku użycia

Szczegóły użycia pliku CSV zawiera opłat, okres rozliczeniowy i dziennego użycia. Aby uzyskać plik, zobacz [pobrać faktury i dane dziennego użycia rozliczeniową za platformę Azure](billing-download-azure-invoice-daily-usage-date.md).

Twoje opłaty za użycie są wyświetlane na poziomie miernika. Poniższe terminy oznaczają to samo zarówno faktury, jak i plik szczegółowym zestawieniem użycia. Na przykład okresu rozliczeniowego na fakturze jest taka sama, jak pokazano w pliku szczegółowym zestawieniem użycia okresu rozliczeniowego.

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

Aby wyświetlić rozbiciem na poszczególne dni dodatkowego, przejdź do **dzienne użycie** sekcji CSV. Filtruj "Harmonogram" w obszarze *kategoria licznika*. Widać, które dni użyto licznika i ile Zużyto. *Zasobów* i *grupy zasobów* informacji znajduje się również do porównania. *Zużyto* wartości należy dodać w do przedstawionego na fakturze.

![Codzienne sekcji w woluminie CSV](./media/billing-understand-your-bill/3.png)

Aby uzyskać koszt za każdy dzień, należy pomnożyć *zużyto* kwoty z *współczynnik* wartość z **instrukcji** sekcji.

Aby dowiedzieć się więcej, zobacz:

- [Omówienie usługi Azure faktury](billing-understand-your-invoice.md)
- [Zrozumienie ze szczegółowym zestawieniem użycia platformy Azure](billing-understand-your-invoice.md)

### <a name="option-2-compare-the-usage-and-costs-with-the-azure-portal"></a>Opcja 2: Porównanie użycia i kosztów za pomocą witryny Azure portal

Witryna Azure portal może również pomóc, możesz sprawdzić opłat. Aby uzyskać szybki przegląd invoiced użycia i opłat, wyświetlać wykresy zarządzania kosztami.

1. W witrynie Azure portal przejdź do [subskrypcje](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Wybierz swoją subskrypcję > **analiza kosztów**. 
1. Filtruj według **Timespan**.
1. Kontynuując poprzedni przykład, zobaczysz opłata za użycie usługi Azure Scheduler.

   ![Analiza kosztów widok w witrynie Azure portal](./media/billing-understand-your-bill/4.png)

1. Wybierz ten wiersz, aby wyświetlić koszt rozbiciem na poszczególne dni.

   ![Historia kosztu widok w witrynie Azure portal](./media/billing-understand-your-bill/5.png)

Aby dowiedzieć się więcej, zobacz [zapobieganie powstawaniu nieoczekiwanych kosztów za pomocą rozliczeń platformy Azure i zarządzania kosztami](billing-getting-started.md#costs).

## <a name="external"></a>Usługi zewnętrzne są rozliczane oddzielnie

Usługi zewnętrzne lub opłatach w portalu marketplace są dla zasobów, które zostały utworzone przez producentów oprogramowania innych firm. Te zasoby są dostępne do użycia w portalu Azure Marketplace. Na przykład zapora Barracuda jest zasobem portalu Azure marketplace oferowane przez inną firmę. Wszystkie opłaty za zaporą i jego odpowiednich liczników są traktowane jako opłat za usługi zewnętrzne.

Opłat za usługi zewnętrzne są rozliczane osobno. Opłaty nie ujęte na fakturze platformy Azure. Aby dowiedzieć się więcej, zobacz [o opłatach za usługi platformy Azure zewnętrznej usługi](billing-understand-your-azure-marketplace-charges.md).

## <a name="resources-billed-by-usage-meters"></a>Opłata jest naliczana według mierników użycia zasobów

Azure nie są bezpośrednio rachunek oparte na kosztów zasobów. Opłaty za zasób są obliczane przy użyciu jednego lub więcej liczników. Te liczniki są używane do śledzenia użycia zasobów, w okresie swojego istnienia. Te liczniki są następnie używane do obliczenia rachunku.

Na przykład po utworzeniu pojedynczej zasobów platformy Azure, takich jak maszyny wirtualne, ma co najmniej jedno wystąpienie licznika utworzone. Te liczniki są używane do śledzenia użycia zasobu wraz z upływem czasu. Każdy licznik emituje rekordów użycia, które są używane przez platformę Azure do obliczenia rachunku.

Na przykład pojedynczej maszyny wirtualnej (VM) utworzonej na platformie Azure może mieć następujących liczników, utworzony w celu śledzenia jej użycie:

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

Po utworzeniu maszyny Wirtualnej w każdej z nich te liczniki rozpoczyna się emitowanie rekordów użycia. To użycie i opłaty wartość licznika jest śledzona w systemie Azure systemu zbierania danych.

## <a name="payment"></a>Zapłacić rachunku

Jeśli konfigurujesz kartę kredytową lub debetową jako metody płatności, płatności są naliczane opłaty, automatycznie w ciągu 10 dni, po upływie okresu rozliczeniowego. Na swoim wyciągu karty kredytowej, że element wiersza **MSFT Azure**.

Aby zmienić karty kredytowej lub debetowej, która jest rozliczana, zobacz [Add, update lub usuwanie karty kredytowej lub debetowej dla platformy Azure](billing-how-to-change-credit-card.md).

Jeśli użytkownik [płacić za pomocą faktury](billing-how-to-pay-by-invoice.md), Wyślij płatność do lokalizacji wymienionych na dole faktury.

Aby sprawdzić stan płatności, [Utwórz bilet pomocy technicznej](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).


## <a name="tips-for-cost-management"></a>Wskazówki dotyczące usługi cost management

- Oszacuj koszty przy użyciu:
  - [Kalkulator cen platformy Azure](https://azure.microsoft.com/pricing/calculator/)
  - [Całkowity koszt posiadania Kalkulator](https://aka.ms/azure-tco-calculator)
  - [Szczegółowe informacje o cenach informacji dla każdej usługi](https://azure.microsoft.com/pricing/)
- [Przeglądanie użycia i kosztów regularnie w witrynie Azure portal](billing-getting-started.md#costs).

## <a name="learn-more"></a>Dowiedz się więcej

- [Usługi platformy Azure faktury i dane dotyczące dziennego wykorzystania](billing-download-azure-invoice-daily-usage-date.md)
- [Zrozumienie warunki na fakturze Microsoft Azure](billing-understand-your-invoice.md)
- [Zrozumienie warunki na usługi Microsoft Azure szczegółowe użycia](billing-understand-your-usage.md)
- [Zarządzanie kosztami portalu Azure](https://docs.microsoft.com/azure/billing/billing-getting-started)
- [Zapobieganie powstawaniu nieoczekiwanych kosztów za pomocą rozliczeń platformy Azure i zarządzania kosztami](billing-getting-started.md#costs)

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [Utwórz żądanie obsługi](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
