---
title: Opis warunków w arkuszu cen dla umowy klienta firmy Microsoft — Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak czytać i zrozumieć swoje użycia oraz rachunek dotyczące subskrypcji platformy Azure
services: ''
documentationcenter: ''
author: jureid
manager: jureid
editor: ''
tags: billing
ms.assetid: 32eea268-161c-4b93-8774-bc435d78a8c9
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/19/2019
ms.author: banders
ms.openlocfilehash: eb6184e10d38cdcfad7070663e36f6610d009cdb
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60371363"
---
# <a name="understand-the-terms-in-your-price-sheet-for-a-microsoft-customer-agreement"></a>Opis warunków w arkuszu cen dla umowy klienta firmy Microsoft

Ten artykuł dotyczy konta rozliczeniowego dla umowy klienta firmy Microsoft. [Sprawdź, czy dostęp do umowy klienta Microsoft](#check-access-to-a-microsoft-customer-agreement).

Jeśli profil rozliczeniowy właścicielem, współautorem, czytnika lub Menedżer faktur możesz pobrać arkusz cen w organizacji w witrynie Azure portal. Zobacz [wyświetlanie i pobieranie cenowych organizacji](billing-ea-pricing.md).

## <a name="detailed-terms-and-descriptions-in-your-microsoft-customer-agreement-price-sheet"></a>Szczegółowy opis warunków i opisy w arkuszu cen umowy klienta firmy Microsoft

W poniższej sekcji opisano ważne terminy wyświetlane w arkuszu cen umowy klienta firmy Microsoft.

| **Nazwa pola**   | **Opis**   |
| --- | --- |
| billingAccountId  | Unikatowy identyfikator konta rozliczeniowego.   |
| billingAccountName  | Nazwa konta rozliczeniowego.  |
| billingProfileId  | Unikatowy identyfikator profil rozliczeniowy.   |
| billingProfileName  | Nazwa profilu rozliczeń, który jest skonfigurowany do otrzymywać faktur. Ceny w arkuszu cen są skojarzone z tym profilem rozliczeń. |
| productOrderName  | Nazwa planu zakupionych produktów. |
| serviceFamily  | Typ usługi platformy Azure. Przykład: Obliczenia, analiza, zabezpieczeń |
| Product (Produkt)  | Nazwa produktu, naliczane opłaty. Przykład: Podstawowe vs SQL DB standardowa bazy danych SQL  |
| productId  | Unikatowy identyfikator produktu, którego licznik jest używany. |
| unitOfMeasure  | Identyfikuje jednostki miary dla rozliczeń dla usługi. Na przykład usługi obliczeniowe są rozliczane godzinowo. |
| meterId  | Unikatowy identyfikator miernika. |
| meterName  | Nazwa licznika. Licznik reprezentuje możliwych do wdrożenia zasobów usługi platformy Azure. |
| MeterCategory  | Nazwa kategorii klasyfikacji dla licznika. Na przykład _usług w chmurze_, _sieć_itp. |
| meterType  |  Nazwa typu miernika. |
| meterSubCategory  | Nazwa kategorii klasyfikacji podrzędnych miernika.  |
| meterRegion  | Nazwa regionu, w których mierniku usługi jest dostępny. Określa lokalizację centrum danych pewnych usług, które są wyceniane na podstawie lokalizacji centrum danych.    |
| tierId  | Określa warstwę cenową, jeśli ma to zastosowanie. To działa w połączeniu z tierMinimumUnits dla ustawienie cenach warstwowych podczas ceny różnią się w oparciu o liczbę wykorzystane jednostki.    |
| tierMinimumUnits  | Określa dolna granica zakresu warstwy, dla którego zdefiniowano ceny. Na przykład jeśli zakres jest 0 do 100, tierMinimumUnits będzie 0.  |
| effectiveStartDate  | Kiedy ceny staje się obowiązująca Data rozpoczęcia. |
| effectiveEndDate  | Data zakończenia Cena efektywna. |
| Cena jednostkowa  | Cena za jednostkę w czasie rozliczeń (nie mieszanego cen) jako specyficzne dla licznika i kolejność nazwy produktu.  Uwaga: Cena jednostki nie jest taka sama jak cena efektywna w szczegółach użycia pliki do pobrania w przypadku usług, które mają różnicowej ceny w warstwach.  W przypadku usług z cenami wielopoziomowe cena efektywna jest wskaźnik wydajności we wszystkie warstwy i cena warstwy określonej jednostki nie są wyświetlane. Cena mieszany lub cena efektywna jest cena netto usługi zużyta ilość obejmujące wiele warstw (gdzie każda warstwa ma określoną cenę jednostkową). |
| basePrice  | Cena na rynku, w tym czasie klient loguje się lub cena na rynku, w tym czasie mierników usługi spowoduje uruchomienie przypadku po logowania jednokrotnego.   |

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Sprawdź dostęp do umowy klienta firmy Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [Utwórz żądanie obsługi](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Kolejne kroki

- [Przeglądanie i pobieranie ceny Twojej organizacji](billing-ea-pricing.md)
