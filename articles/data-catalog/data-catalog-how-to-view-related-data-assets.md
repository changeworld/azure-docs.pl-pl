---
title: Jak wyświetlać dane dotyczące zasobów w usłudze Azure Data Catalog
description: W tym artykule wyjaśniono, jak wyświetlać dane dotyczące zasobów wybranego zasobu danych w usłudze Azure Data Catalog.
services: data-catalog
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 01/18/2018
ms.openlocfilehash: b01c328812113ad721b7632978ad28e54a6a3ef1
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2019
ms.locfileid: "59996479"
---
# <a name="how-to-view-related-data-assets-in-azure-data-catalog"></a>Jak wyświetlać dane dotyczące zasobów w usłudze Azure Data Catalog?
Usługa Azure Data Catalog służy do wyświetlania zasobów danych związanych z wybranych danych zasobów i w widoku relacji między nimi. 

## <a name="supported-data-sources"></a>Obsługiwane źródła danych 
Po zarejestrowaniu zasoby danych z następujących źródeł danych, usługi Azure Data Catalog powoduje automatyczne zarejestrowanie metadane dotyczące relacji sprzężenia między wybranych zasobów danych. 

- Oprogramowanie SQL Server
- Azure SQL Database
- MySQL
- Oracle

> [!NOTE]
> Usługi Data Catalog do zaimportowania relacji między zasobami dwóch danych należy zarejestrować zarówno zasoby w tym samym czasie. Jeśli jeden z nich ma dodany osobno, dodaj go ponownie i innych zasobów danych można zaimportować relacji między nimi.

## <a name="view-related-data-assets"></a>Wyświetlanie powiązanych zasobów danych
Aby wyświetlić zasoby danych, które są powiązane z wybranego zestawu danych, użyj **relacje** karty, jak pokazano na poniższej ilustracji: 

![Usługa Azure Data Catalog — Wyświetl powiązane zasoby danych](media/data-catalog-how-to-view-related-data-assets/relationships-tab.png)

W tym przykładzie istnieją dwie relacje dla wybranego **ProductSubcategory** zasobu danych: 

- Kolumna ProductSubcategoryID tabeli produktu ma relacji klucza obcego z kolumną ProductSubcategoryID wybranej tabeli ProductSubcategory. 
- Kolumna ProductCategoryID tabeli ProductSubCategory ma relacji klucza obcego z kolumną ProductCategoryID wybranej tabeli ProductCategory.

> [!NOTE]
> Zwróć uwagę, kierunek strzałki w widoku drzewa relacji.  

Aby zobaczyć więcej szczegółów, takich jak w pełni kwalifikowana nazwa kolumny, przenieś wskaźnik myszy, a zobaczysz okno podręczne podobny do poniższej ilustracji: 

![Usługa Azure Data Catalog — okno podręczne relacji](media/data-catalog-how-to-view-related-data-assets/relationship-popup.png)

Aby uwzględnić relacje między zasobami, które zostały już zarejestrowane, Zarejestruj ponownie aplikację tych zasobów.

## <a name="next-steps"></a>Kolejne kroki
- [Jak zarządzać zasobami danych](data-catalog-how-to-manage.md)