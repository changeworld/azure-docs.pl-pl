---
title: Jak dokumentować źródła danych w usłudze Azure Data Catalog
description: Artykuł porad wyróżnianie jak dokumentować zasoby danych w usłudze Azure Data Catalog.
services: data-catalog
author: JasonWHowell
ms.author: jasonh
ms.assetid: 053b1701-b848-4ada-b726-6f485caa9961
ms.service: data-catalog
ms.topic: conceptual
ms.date: 01/18/2018
ms.openlocfilehash: 9049e0b25db37f296e27476fbb93418d13c4dcc1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61001568"
---
# <a name="document-data-sources"></a>Dokumentowanie źródeł danych
## <a name="introduction"></a>Wprowadzenie
**Microsoft Azure Data Catalog** to usługa w pełni zarządzana usługa w chmurze, która służy jako system rejestracji i system odnajdowania firmowych źródeł danych. Innymi słowy **usługi Azure Data Catalog** to przede wszystkim pomagając ludziom, odnajdywanie, *zrozumieć*i używanie źródeł danych oraz pomaga organizacjom wydobyć więcej ze swoich istniejących danych.

Jeśli źródło danych jest zarejestrowany w **usługi Azure Data Catalog**, jego metadanych jest kopiowany i indeksowane przez tę usługę, ale wątek nie kończy się tam. **Usługa Azure Data Catalog** umożliwia również użytkowników w celu zapewnienia ich własnych wyczerpujące informacje opisujące użycia i typowe scenariusze dla źródła danych.

W [jak dodawać adnotacje do źródeł danych](data-catalog-how-to-annotate.md), dowiesz się, że ekspertów, którzy nie znają źródła danych może dodawać adnotacje go za pomocą tagów i opis. **Usługi Azure Data Catalog** portal zawiera edytor tekstu sformatowanego, dzięki czemu użytkownicy mogą w pełni dokumentów, zasobów danych i kontenerów. Edytor obejmuje formatowanie akapitów, takie jak nagłówki, formatowania tekstu, list punktowanych, listami numerowanymi i tabel.

Znaczniki i opisy są idealne narzędzie do adnotacji proste. Aby ułatwić konsumenci danych lepiej zrozumieć użycie źródła danych i scenariuszy biznesowych dla źródła danych, eksperta może jednak zapewniać pełne, szczegółowe dokumentacji. To proste dokumentować źródła danych. Wybierz zasób danych lub kontenera, a następnie wybierz **dokumentacji**.

![](media/data-catalog-documentation/data-catalog-documentation.png)

## <a name="documenting-data-assets"></a>Dokumentowanie zasobów danych
Zaletą **usługi Azure Data Catalog** dokumentacji pozwala użyć wykazu danych jako repozytorium zawartości, aby stworzyć kompletny opis zasobów danych. Możesz eksplorować szczegółowa zawartość, która opisuje kontenerów i tabel. Jeśli masz już zawartość w innym repozytorium zawartości, takich jak SharePoint lub udział plików, można dodać do łącza dokumentacji zasobów, aby odwoływać się do tego istniejącej zawartości. Ta funkcja sprawia, że istniejące dokumenty mogą szybciej odnajdywać.

> [!NOTE]
> Dokumentacja usługi nie jest uwzględniony w indeksie wyszukiwania.
>
>

![](media/data-catalog-documentation/data-catalog-documentation2.png)

Poziom dokumentacji może wynosić od opisywania właściwości i wartość kontenera zasobów danych, aby uzyskać szczegółowy opis schematu tabeli w kontenerze. Poziom dokumentacją dostarczoną powinny być prowadzone przez Twoje potrzeby biznesowe. Ale ogólnie rzecz biorąc, poniżej przedstawiono kilka zalet i wad dokumentowanie zasobów danych:

* Dokument po prostu kontener: Cała zawartość znajduje się w jednym miejscu, ale mogą wykazywać Brak odpowiednie szczegóły dla użytkowników podjąć świadomą decyzję.
* Dokumentowanie tylko tabele: Zawartość jest specyficzne dla tego obiektu, ale użytkownicy mają wiele miejsc dla dokumentów.
* Kontenery dokumentów i tabele: Najbardziej kompleksowe podejście, ale może powodować konserwacji więcej dokumentów.

## <a name="summary"></a>Podsumowanie
Dokumentowanie źródeł danych za pomocą **usługi Azure Data Catalog** w możliwie szczegółowo należy utworzyć narracja o Twoje zasoby danych.  Za pomocą łącza, można połączyć zawartości przechowywanej w istniejącym repozytorium zawartości, grupujący swoje istniejące dokumenty i zasobów danych. Gdy użytkownicy odnajdywanie zasobów danych, mogą mieć kompletny zestaw dokumentacji.
