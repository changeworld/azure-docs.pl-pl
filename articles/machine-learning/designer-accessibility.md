---
title: Korzystanie z funkcji ułatwień dostępu w projektancie
titleSuffix: Azure Machine Learning
description: Dowiedz się więcej na temat skrótów klawiaturowych i funkcji ułatwień dostępu czytnika ekranu dostępnych w projektancie.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.author: peterlu
author: peterclu
ms.date: 01/09/2020
ms.openlocfilehash: 7d5c8047fea8e9081de08b582c537711714ea463
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2020
ms.locfileid: "76314236"
---
# <a name="use-a-keyboard-to-use-azure-machine-learning-designer"></a>Używanie narzędzia Azure Machine Learning Designer przy użyciu klawiatury

Dowiedz się, jak używać programu Azure Machine Learning Designer przy użyciu klawiatury i czytnika ekranu. Aby zapoznać się z listą skrótów klawiaturowych, które działają wszędzie na Azure Portal, zobacz [skróty klawiaturowe w Azure Portal](../azure-portal/azure-portal-keyboard-shortcuts.md)

Ten przepływ pracy został przetestowany przy użyciu programu [narrator](https://support.microsoft.com/help/22798/windows-10-complete-guide-to-narrator) i [szczęk](https://www.freedomscientific.com/products/software/jaws/), ale powinien współpracować z innymi czytnikami ekranu standardowego.

## <a name="navigate-the-pipeline-graph"></a>Nawigowanie po grafie potoku

Wykres potoku jest zorganizowany jako lista list. Lista modułów najwyższego poziomu zawiera wszystkie moduły w potoku. Każdy element na liście modułów zawiera listę połączeń, która opisuje wszystkie połączenia. 

1. Na liście modułów Użyj klawisza Strzałka, aby przełączyć moduły.
1. Użyj klawisza Tab, aby otworzyć listę połączeń dla modułu docelowego.
1. Użyj klawisza Strzałka, aby przełączać się między portami połączenia dla modułu.
1. Użyj "G", aby przejść do modułu docelowego.

## <a name="edit-the-pipeline-graph"></a>Edytowanie wykresu potoku

### <a name="add-a-module-to-the-graph"></a>Dodawanie modułu do wykresu

1. Użyj kombinacji klawiszy CTRL + F6, aby przełączyć fokus z kanwy do drzewa modułów.
1. Znajdź żądany moduł w drzewie modułów przy użyciu standardowej kontrolki TreeView.

### <a name="edit-a-module"></a>Edytowanie modułu

Aby połączyć moduł z innym modułem:

1. Naciśnij klawisze Ctrl + Shift + H w przypadku kierowania do modułu na liście modułów, aby otworzyć pomocnika połączenia.
1. Edytuj porty połączenia dla modułu.

Aby dostosować właściwości modułu:

1. Naciśnij klawisze Ctrl + Shift + E, gdy obiektem docelowym jest moduł, aby otworzyć właściwości modułu.
1. Edytuj właściwości modułu.

## <a name="navigation-shortcuts"></a>Skróty nawigacji

| Keystroke | Opis |
|-|-|
| Ctrl + F6 | Przełącz fokus między kanwą a drzewem modułu |
| Ctrl + F1   | Otwórz kartę informacji, gdy koncentruje się na węźle w drzewie modułów |
| Ctrl + Shift + H | Otwórz pomocnika połączenia, gdy fokus znajduje się w węźle |
| Ctrl + Shift + E | Otwórz właściwości modułu, gdy fokus znajduje się w węźle |
| Ctrl + G | Przenieś fokus na pierwszy węzeł zakończony niepowodzeniem, Jeśli uruchomienie potoku nie powiodło się |

## <a name="action-shortcuts"></a>Skróty akcji

Użyj następujących skrótów z kluczem dostępu. Aby uzyskać więcej informacji o kluczach dostępu, zobacz https://en.wikipedia.org/wiki/Access_key.

| Keystroke | Działanie |
|-|-|
| Klucz dostępu + R | Uruchamianie |
| Klucz dostępu + P | Publikuj |
| Klucz dostępu + C | Klonuj |
| Klucz dostępu + D | Implementacja |
| Klucz dostępu + I | Utwórz/zaktualizuj potok wnioskowania |
| Klucz dostępu + B | Utwórz/zaktualizuj potok wnioskowania dotyczącego partii |
| Klucz dostępu + K | Otwórz listę rozwijaną "Utwórz potok wnioskowania" |
| Klawisz dostępu + U | Otwórz listę rozwijaną "potoku aktualizacji wnioskowania" |
| Klucz dostępu + M | Otwórz listę rozwijaną (...) |

## <a name="next-steps"></a>Następne kroki

- [Włączanie dużego kontrastu lub zmienianie motywu](../azure-portal/azure-portal-change-theme-high-contrast.md)
- [Narzędzia dotyczące ułatwień dostępu w firmie Microsoft](https://www.microsoft.com/accessibility)
