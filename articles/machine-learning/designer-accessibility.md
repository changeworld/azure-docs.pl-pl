---
title: Używanie funkcji ułatwień dostępu w projektancie (wersja zapoznawcza)
titleSuffix: Azure Machine Learning
description: Dowiedz się więcej o skrótach klawiaturowych i funkcjach ułatwień dostępu czytnika zawartości ekranu dostępnych w projektancie.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.author: peterlu
author: peterclu
ms.date: 01/09/2020
ms.openlocfilehash: 59199291589a81d0a0d96b7867078b8196be086f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77366198"
---
# <a name="use-a-keyboard-to-use-azure-machine-learning-designer-preview"></a>Używanie klawiatury do korzystania z projektanta usługi Azure Machine Learning (wersja zapoznawcza)

Dowiedz się, jak używać klawiatury i czytnika zawartości ekranu do korzystania z projektanta usługi Azure Machine Learning. Aby uzyskać listę skrótów klawiaturowych, które działają wszędzie w witrynie Azure Portal, zobacz [Skróty klawiaturowe w witrynie Azure portal](../azure-portal/azure-portal-keyboard-shortcuts.md)

Ten przepływ pracy został przetestowany z [Narratorem](https://support.microsoft.com/help/22798/windows-10-complete-guide-to-narrator) i [jaws](https://www.freedomscientific.com/products/software/jaws/), ale powinien działać z innymi standardowymi czytnikami ekranu.

## <a name="navigate-the-pipeline-graph"></a>Poruszanie się po wykresie potoku

Wykres potoku jest zorganizowany jako lista zagnieżdżona. Zewnętrzna lista jest listą modułów, która opisuje wszystkie moduły na wykresie potoku. Wewnętrzna lista jest listą połączeń, która opisuje wszystkie połączenia określonego modułu.  

1. Na liście modułów użyj klawisza strzałek, aby przełączyć moduły.
1. Użyj karty, aby otworzyć listę połączeń dla modułu docelowego.
1. Użyj klawisza strzałek, aby przełączać się między portami połączeń dla modułu.
1. Użyj "G", aby przejść do modułu docelowego.

## <a name="edit-the-pipeline-graph"></a>Edytowanie wykresu potoku

### <a name="add-a-module-to-the-graph"></a>Dodawanie modułu do wykresu

1. Użyj klawiszy Ctrl+F6, aby przełączyć fokus z obszaru roboczego na drzewo modułów.
1. Znajdź żądany moduł w drzewie modułów przy użyciu standardowej kontrolki treeview.

### <a name="edit-a-module"></a>Edytowanie modułu

Aby podłączyć moduł do innego modułu:

1. Użyj klawiszy Ctrl + Shift + H podczas kierowania na moduł na liście modułów, aby otworzyć pomocnika połączenia.
1. Edytuj porty połączeń dla modułu.

Aby dostosować właściwości modułu:

1. Użyj klawiszy Ctrl + Shift + E podczas kierowania na moduł, aby otworzyć właściwości modułu.
1. Edytuj właściwości modułu.

## <a name="navigation-shortcuts"></a>Skróty nawigacyjne

| Klawiszy | Opis |
|-|-|
| Ctrl + F6 | Przełączanie fokusu między kanwą a drzewem modułów |
| Ctrl + F1   | Otwieranie karty informacyjnej podczas ustawiania ostrości na węźle w drzewie modułów |
| Ctrl + Shift + H | Otwieranie pomocnika połączenia, gdy fokus znajduje się na węźle |
| Ctrl + Shift + E | Otwieranie właściwości modułu, gdy fokus znajduje się na węźle |
| Ctrl + G | Przenoszenie fokusu do pierwszego węzła, który nie powiódł się, jeśli uruchomienie potoku nie powiodło się |

## <a name="action-shortcuts"></a>Skróty akcji

Użyj następujących skrótów z kluczem dostępu. Aby uzyskać więcej informacji https://en.wikipedia.org/wiki/Access_keyna temat kluczy dostępu, zobacz .

| Klawiszy | Akcja |
|-|-|
| Klucz dostępu + R | Run |
| Klucz dostępu + P | Publikowanie |
| Klucz dostępu + C | Klonowanie |
| Klucz dostępu + D | Wdrożenie |
| Klucz dostępu + I | Potok wnioskowania tworzenia/aktualizowania |
| Klucz dostępu + B | Potok wnioskowania partii tworzenie/aktualizowanie partii |
| Klucz dostępu + K | Otwórz listy rozwijane "Tworzenie potoku wnioskowania" |
| Klucz dostępu + U | Otwórz menu rozwijane "Aktualizuj potok wnioskowania" |
| Klucz dostępu + M | Otwórz więcej(...) listy rozwijanej |

## <a name="next-steps"></a>Następne kroki

- [Włączanie dużego kontrastu lub zmienianie motywu](../azure-portal/azure-portal-change-theme-high-contrast.md)
- [Narzędzia związane z ułatwieniami dostępu w firmie Microsoft](https://www.microsoft.com/accessibility)
