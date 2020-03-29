---
title: Jak dokumentować źródła danych w wykazie danych platformy Azure
description: Artykuł in jakywanie przedstawiający sposób dokumentowania zasobów danych w usłudze Azure Data Catalog.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: e9e9013d354585d04f205feb93a84d94c0f05905
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68950183"
---
# <a name="how-to-document-data-sources-in-azure-data-catalog"></a>Jak dokumentować źródła danych w wykazie danych platformy Azure

## <a name="introduction"></a>Wprowadzenie
**Microsoft Azure Data Catalog** to w pełni zarządzana usługa w chmurze, która służy jako system rejestracji i systemu odnajdowania dla źródeł danych przedsiębiorstwa. Innymi słowy **usługa Azure Data Catalog** ułatwia ludziom odkrywanie, *rozumienie*i używanie źródeł danych oraz pomaga organizacjom w pozyskiwaniu większej wartości z istniejących danych.

Gdy źródło danych jest zarejestrowane w **usłudze Azure Data Catalog,** jego metadane są kopiowane i indeksowane przez usługę, ale historia nie kończy się na tym. **Usługa Azure Data Catalog** umożliwia również użytkownikom dostarczenie własnej pełnej dokumentacji, która może opisywać użycie i typowe scenariusze dla źródła danych.

W [jak dodawać adnotacje do źródeł danych](data-catalog-how-to-annotate.md), dowiesz się, że eksperci, którzy znają źródło danych, mogą dodawać do niego adnotacje za pomocą tagów i opisu. Portal **wykazu danych platformy Azure** zawiera edytor tekstu sformatowego, dzięki czemu użytkownicy mogą w pełni dokumentować zasoby danych i kontenery. Edytor zawiera formatowanie akapitów, takie jak nagłówki, formatowanie tekstu, listy punktowane, listy numerowane i tabele.

Tagi i opisy są idealne do prostych adnotacji. Jednak aby pomóc konsumentom danych lepiej zrozumieć użycie źródła danych i scenariuszy biznesowych dla źródła danych, ekspert może dostarczyć pełną, szczegółową dokumentację. Łatwo jest udokumentować źródło danych. Wybierz zasób danych lub kontener i wybierz pozycję **Dokumentacja**.

![Karta Dokumentacja w wykazie danych](media/data-catalog-documentation/data-catalog-documentation.png)

## <a name="documenting-data-assets"></a>Dokumentowanie zasobów danych
Zaletą dokumentacji **usługi Azure Data Catalog** umożliwia użycie wykazu danych jako repozytorium zawartości w celu utworzenia pełnej narracji zasobów danych. Można eksplorować szczegółowej zawartości, która opisuje kontenery i tabele. Jeśli masz już zawartość w innym repozytorium zawartości, takim jak SharePoint lub udział plików, możesz dodać do łącza dokumentacji zasobu, aby odwołać się do tej istniejącej zawartości. Ta funkcja sprawia, że istniejące dokumenty są bardziej wykrywalne.

> [!NOTE]
> Dokumentacja nie jest uwzględniona w indeksie wyszukiwania.
>

![Karta Dokumentacja i hiperłącze do łącza sieci Web](media/data-catalog-documentation/data-catalog-documentation2.png)

Poziom dokumentacji może wahać się od opisu właściwości i wartości kontenera zasobów danych do szczegółowego opisu schematu tabeli w kontenerze. Poziom dostarczonej dokumentacji powinien być oparty na potrzebach biznesowych. Ale ogólnie rzecz biorąc, oto kilka zalet i wad dokumentowania zasobów danych:

* Dokument tylko kontener: Cała zawartość jest w jednym miejscu, ale może brakować informacji niezbędnych dla użytkowników do podjęcia świadomej decyzji.
* Dokumentuj tylko tabele: Zawartość jest specyficzna dla tego obiektu, ale użytkownicy mają wiele miejsc dla dokumentów.
* Kontenery dokumentów i tabele: najbardziej kompleksowe podejście, ale może wprowadzić więcej konserwacji dokumentów.

## <a name="summary"></a>Podsumowanie
Dokumentowanie źródeł danych za pomocą **usługi Azure Data Catalog** może utworzyć narrację dotyczącą zasobów danych w miarę szczegółów, ile potrzebujesz.  Korzystając z łączy, można połączyć się z zawartością przechowywaną w istniejącym repozytorium zawartości, które łączy istniejące dokumenty i zasoby danych. Gdy użytkownicy odkryją odpowiednie zasoby danych, mogą mieć kompletny zestaw dokumentacji.
