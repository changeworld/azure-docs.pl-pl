---
title: Łączenie kolumn według przykładu przekształcenia przy użyciu usługi Azure Machine Learning Workbench
description: Dokument referencyjny dotyczący przekształcenia łączenie kolumn według przykładu
services: machine-learning
author: ranvijaykumar
ms.author: ranku
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.custom: mvc, reference
ms.topic: article
ms.date: 09/14/2017
ROBOTS: NOINDEX
ms.openlocfilehash: c952882a0a12e288eaf4f5f1074a9a2e876a21d6
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46986238"
---
# <a name="combine-columns-by-example-transformation"></a>Łączenie kolumn według przykładu przekształcania

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 


Ta transformacja umożliwia użytkownikowi Dodaj nową kolumnę, łącząc wartości z wielu kolumn. Użytkownika można określić separatora lub zawierają przykłady wartości połączone do wykonania przekształcenia. Podczas użytkownika zawiera przykłady kombinacji, transformacja jest obsługiwana przez ten sam **według przykładu** aparat, który jest używany w **Utwórz kolumnę pochodną według przykładu** przekształcania.

## <a name="how-to-perform-this-transformation"></a>Jak przeprowadzić takie przekształcenie

Aby wykonać to przekształcenie, wykonaj następujące kroki:
1. Wybierz co najmniej dwóch kolumn, które mają zostać połączone w jedną kolumnę. 
2. Wybierz **łączenie kolumn według przykładu** z **przekształca** menu. Lub kliknij prawym przyciskiem myszy nagłówek dowolnej wybranej kolumny, a następnie wybierz pozycję **łączenie kolumn według przykładu** z menu kontekstowego. Zostanie otwarty Edytor Przekształcanie i nowa kolumna zostanie dodana obok prawej najbardziej zaznaczonej kolumnie. Nowa kolumna zawiera połączone wartości oddzielonych domyślnym separatorem. Wybranych kolumn można zidentyfikować przez pola wyboru w nagłówkach kolumn. Dodawanie i usuwanie kolumn z zaznaczenia może odbywać się przy użyciu pola wyboru.
3. Możesz zaktualizować połączoną wartość w nowo utworzonej kolumny. Zaktualizowaną wartość jest używany jako przykład się transformacji.
4. Kliknij przycisk **OK** aby zaakceptować przekształcenie.

### <a name="transform-editor-advanced-mode"></a>Przekształcanie Edytor: Tryb zaawansowany

Tryb zaawansowany zawiera więcej możliwości łączenia kolumn. 

Wybieranie **Separator** w obszarze **łączenie kolumn według** umożliwia użytkownikowi określenie ciągów w **Separator** pola tekstowego. Z karty się **Separator** pole tekstowe, aby wyświetlić podgląd wyników w gird danych. Naciśnij klawisz **OK** zatwierdzić transformacji.

Wybieranie **przykłady** w obszarze **łączenie kolumn według** umożliwia użytkownikowi zawierają przykłady połączone wartości. Do podwyższenia poziomu wierszy, na przykład, kliknij dwukrotnie wierszy w siatce. Wpisz w oczekiwanych danych wyjściowych w polu tekstowym względem wiersza o podwyższonym poziomie. Z karty się **Separator** pole tekstowe, aby wyświetlić podgląd wyników w gird danych. Naciśnij klawisz **OK** zatwierdzić transformacji. 

Użytkownik może przełączać się między **trybie podstawowym** i **trybu zaawansowanego** , klikając łącza w edytorze przekształcania.

### <a name="transform-editor-send-feedback"></a>Przekształcanie Edytor: wysyłanie opinii

Kliknięcie **Wyślij opinię** link zostanie otwarty **opinii** udostępnił okna dialogowego pole komentarze wstępnie wypełnione przy użyciu użytkownika przykłady. Użytkownik powinien Przejrzyj zawartość pola komentarze i dostarczenia bardziej szczegółowych informacji, która pomoże nam zrozumieć ten problem. Jeśli użytkownik nie chce udostępniać dane z firmą Microsoft, użytkownik powinien usunąć wstępnie wypełnionych przykładowe dane przed kliknięciem przycisku **Wyślij opinię** przycisku. 

### <a name="editing-existing-transformation"></a>Edytowanie istniejącego transformacji

Użytkownik może edytować istniejące **łączenie kolumn według przykładu** przekształcania, wybierając **Edytuj** opcji kroku przekształcania. Kliknięcie **Edytuj** zostanie otwarty Edytor przekształcania w **trybie podstawowym**. Użytkownik może wprowadzić **trybu zaawansowanego** , klikając łącze w nagłówku. Wszystkie przykłady, które zostały podane podczas tworzenia przekształcenia są wyświetlane dostępne.

## <a name="example-using-separators"></a>Przykład korzystający z separatorami

Przecinek następuje spacja jest używany jako separator w tym przykładzie połączyć *ulicy*, *Miasto*, *stanu*, i *ZIP* kolumn.

|Ulica|Miasto|Stan|ZIP|Kolumna|
|:----|:----|:----|:----|:----|
|16011 N.E. sposób 36th|REDMOND|WA|98052|16011 N.E. 36th way, REDMOND, WA, 98052|
|16021 N.E. sposób 36th|REDMOND|WA|98052|16021 N.E. 36th way, REDMOND, WA, 98052|
|16031 N.E. sposób 36th|REDMOND|WA|98052|16031 N.E. 36th way, REDMOND, WA, 98052|
|16041 N.E. sposób 36th|REDMOND|WA|98052|16041 N.E. 36th way, REDMOND, WA, 98052|
|16051 N.E. sposób 36th|REDMOND|WA|98052|16051 N.E. 36th way, REDMOND, WA, 98052|
|16061 N.E. sposób 36th|REDMOND|WA|98052|16061 N.E. 36th way, REDMOND, WA, 98052|
|NE ścieżek 157th 3460|REDMOND|WA|98052|NE ścieżek 157th 3460, REDMOND, WA, 98052|
|3350 157th Ave N.E.|REDMOND|WA|98052|N.E. Ave 157th 3350, REDMOND, WA, 98052|
|3240 157th ścieżek N.E.|REDMOND|WA|98052|N.E. ścieżek 157th 3240, REDMOND, WA, 98052|

## <a name="example-using-by-example"></a>Przykład użycia według przykładu

Wartość w **bold** został podany jako przykład.

|Date|Miesiąc|Rok|Godzina|Minuta|Sekunda|Połączone kolumny|
|:----|:----|:----|:----|:----|:----|:----|
|13|Paź|2016|15|01|23|**13-Oct — 2016 15:01:23 (PDT)**|
|16|Paź|2016|16|22|33|16-Oct — 2016 15:01:33 (PDT)|
|17|Paź|2016|12|43|12|17-Oct — 2016 15:01:12 (PDT)|
|12|Lis|2016|14|22|44|2016-12-lis 15:01:44 (PDT)|
|23|Lis|2016|01|52|45|23-lis-2016 15:01:45 (PDT)|
|16|Sty|2017|22|34|56|16-sty-2016 15:01:56 (PDT)|
|23|Mar|2017|01|55|25|23 marca 2016 r 15:01:25 (PDT)|
|16|Kwi|2017|11|34|36|16-kwi-2016 15:01:36 (PDT)|

