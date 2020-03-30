---
title: Jak wyświetlić powiązane zasoby danych w wykazie danych platformy Azure
description: W tym artykule wyjaśniono, jak wyświetlić powiązane zasoby danych wybranego zasobu danych w usłudze Azure Data Catalog.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 212ba647e6eb44e800a589928620f56fba65107c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68737014"
---
# <a name="how-to-view-related-data-assets-in-azure-data-catalog"></a>Jak wyświetlać powiązane zasoby danych w usłudze Azure Data Catalog?
Usługa Azure Data Catalog umożliwia wyświetlanie zasobów danych związanych z wybranym zasobem danych i wyświetlanie relacji między nimi. 

## <a name="supported-data-sources"></a>Obsługiwane źródła danych 
Podczas rejestrowania zasobów danych z następujących źródeł danych usługa Azure Data Catalog automatycznie rejestruje metadane dotyczące relacji sprzężenia między wybranymi zasobami danych. 

- SQL Server
- Azure SQL Database
- MySQL
- Oracle

> [!NOTE]
> Aby wykaz danych zaimportować relację między dwoma zasobami danych, należy zarejestrować oba zasoby w tym samym czasie. Jeśli jeden z nich został dodany oddzielnie, dodaj go ponownie, a drugi zasób danych, aby zaimportować relację między nimi.

## <a name="view-related-data-assets"></a>Wyświetlanie powiązanych zasobów danych
Aby wyświetlić zasoby danych związane z wybranym zestawem danych, użyj karty **Relacje,** jak pokazano na poniższej ilustracji: 

![Wykaz danych platformy Azure — wyświetlanie powiązanych zasobów danych](media/data-catalog-how-to-view-related-data-assets/relationships-tab.png)

W tym przykładzie istnieją dwie relacje dla wybranego zasobu danych **ProductSubcategory:** 

- Kolumna ProductSubcategoryID tabeli Produkt ma relację klucza obcego z kolumną ProductSubcategoryID wybranej tabeli ProductSubcategory. 
- Kolumna ProductCategoryID tabeli ProductSubCategory zawiera relację klucza obcego z kolumną ProductCategoryID w wybranej tabeli Kategoria produktu.

> [!NOTE]
> Zwróć uwagę na kierunek strzałki w widoku drzewa relacji.  

Aby wyświetlić więcej szczegółów, takich jak w pełni kwalifikowana nazwa kolumny, przesuń wskaźnik myszy i zobaczysz okno podręczne podobne do następującego obrazu: 

![Usługa Azure Data Catalog — wyskakujące okienko relacji](media/data-catalog-how-to-view-related-data-assets/relationship-popup.png)

Aby uwzględnić relacje między aktywami, które zostały już zarejestrowane, należy ponownie zarejestrować te zasoby.

## <a name="next-steps"></a>Następne kroki
- [Jak zarządzać zasobami danych](data-catalog-how-to-manage.md)