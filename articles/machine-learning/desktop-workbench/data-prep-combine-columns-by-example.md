---
title: "Łączenie kolumn przez przekształcania przykład za pomocą usługi Azure Machine Learning Workbench"
description: "W dokumencie referencyjnym przekształcenia łączenie kolumn w przykładzie"
services: machine-learning
author: ranvijaykumar
ms.author: ranku
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc, reference
ms.topic: article
ms.date: 09/14/2017
ms.openlocfilehash: 469fefa48ea795a56b0e7525f99634c5149a780f
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2018
---
# <a name="combine-columns-by-example-transformation"></a>Łączenie kolumn w przykładzie transformacja
Ta transformacja umożliwia użytkownikowi Dodaj nową kolumnę przez połączenie wartości z wielu kolumn. Użytkownika można określić separatora lub zawierają przykłady połączonych wartości do wykonania tej transformacji. Podczas użytkownika zawiera przykłady kombinacja, transformacja jest obsługiwana przez ten sam **przez przykład** aparatu, który jest używany w **kolumny pochodzi przykładzie** transformacji.

## <a name="how-to-perform-this-transformation"></a>Jak wykonać tego przekształcenia

Do wykonania tej transformacji, wykonaj następujące kroki:
1. Wybierz co najmniej dwie kolumny, które mają zostać połączone w jedną kolumnę. 
2. Wybierz **łączenie kolumn, na przykład** z **przekształca** menu. Lub kliknij prawym przyciskiem myszy nagłówek dowolnej wybranej kolumny i wybierz **łączenie kolumn, na przykład** z menu kontekstowego. Zostanie otwarty Edytor przekształcenie, a nowej kolumny są dodawane obok prawo najbardziej zaznaczonej kolumny. Nowa kolumna zawiera połączone wartości oddzielonych separatora domyślne. Wybranych kolumn może zostać zidentyfikowane na podstawie pól wyboru w nagłówkach kolumn. Dodawanie i usuwanie kolumn z zaznaczenia może odbywać się przy użyciu pól wyboru.
3. Można aktualizować łączna wartość w kolumnie nowo utworzony. Zaktualizowanej wartości jest używany na przykład aby dowiedzieć się więcej transformacji.
4. Kliknij przycisk **OK** do akceptowania transformacji.

### <a name="transform-editor-advanced-mode"></a>Przekształć edytora: Tryb zaawansowany

Tryb zaawansowany zawiera rozbudowanej łączenie kolumn. 

Wybieranie **separatora** w obszarze **połączyć kolumny według** umożliwia użytkownikowi określenie ciągów w **separatora** pola tekstowego. Karta limit z **separatora** pole tekstowe, aby wyświetlić podgląd wyników w gird danych. Naciśnij klawisz **OK** można przekazać transformacji.

Wybieranie **przykłady** w obszarze **połączyć kolumny według** umożliwia użytkownikowi zawierają przykłady wartości połączone. Aby promować wierszy, na przykład, kliknij dwukrotnie wierszy w siatce. Wpisz oczekiwane dane wyjściowe w polu tekstowym przed awansowana wiersza. Karta limit z **separatora** pole tekstowe, aby wyświetlić podgląd wyników w gird danych. Naciśnij klawisz **OK** można przekazać transformacji. 

Użytkownik może przełączać się między **trybie podstawowym** i **trybu zaawansowanego** po kliknięciu łączy w edytorze transformacji.

### <a name="transform-editor-send-feedback"></a>Przekształć edytora: wysyłanie opinii

Kliknięcie **wysłać opinię** połączyć otwartym **opinii** udostępnił okna dialogowego z polem komentarze wstępnie wypełnione użytkownika przykłady. Użytkownik powinien przejrzeć zawartość pola komentarze i zawierają więcej szczegółowych informacji, aby pomóc nam zrozumieć problem. Jeśli użytkownik nie chce udostępniać dane firmy Microsoft, użytkownik powinien usunąć wstępnie przykładowe dane przed kliknięciem przycisku **Prześlij opinię** przycisku. 

### <a name="editing-existing-transformation"></a>Edytowanie istniejącego przekształcania

Użytkownik może edytować istniejące **Połącz kolumnę według przykładu** przekształcenie wybierając **Edytuj** opcji krok transformacji. Kliknięcie **Edytuj** otwiera edytor Przekształć w **trybie podstawowym**. Użytkownik może wprowadzić **trybu zaawansowanego** , klikając łącze w nagłówku. Wszystkie przykłady, które zostały podane podczas tworzenia transformacji zostaną w nim wyświetlone.

## <a name="example-using-separators"></a>Przykład użycia separatorów

Przecinek następuje spacja jest używany jako separator w tym przykładzie połączyć *ulicy*, *miasta*, *stanu*, i *ZIP* kolumn.

|Ulica|Miasto|Stan|ZIP|Kolumna|
|:----|:----|:----|:----|:----|
|16011 N.E. sposób 36th|REDMOND|WA|98052|16011 N.E. 36th way, REDMOND, WA, 98052|
|16021 N.E. sposób 36th|REDMOND|WA|98052|16021 N.E. 36th way, REDMOND, WA, 98052|
|16031 N.E. sposób 36th|REDMOND|WA|98052|16031 N.E. 36th way, REDMOND, WA, 98052|
|16041 N.E. sposób 36th|REDMOND|WA|98052|16041 N.E. 36th way, REDMOND, WA, 98052|
|16051 N.E. sposób 36th|REDMOND|WA|98052|16051 N.E. 36th way, REDMOND, WA, 98052|
|16061 N.E. sposób 36th|REDMOND|WA|98052|16061 N.E. 36th way, REDMOND, WA, 98052|
|3460 157th Avenue NE|REDMOND|WA|98052|NE ścieżek 157th 3460, REDMOND, WA, 98052|
|3350 157th Ave. N.E.|REDMOND|WA|98052|N.E. Ave. 157th 3350, REDMOND, WA, 98052|
|3240 157th ścieżek N.E.|REDMOND|WA|98052|N.E. ścieżek 157th 3240, REDMOND, WA, 98052|

## <a name="example-using-by-example"></a>Przykład użycia według przykładu

Wartość w **bold** podano w przykładzie.

|Date|Miesiąc|Rok|Godzina|Minuta|Sekunda|Połączona kolumna|
|:----|:----|:----|:----|:----|:----|:----|
|13|Paź|2016|15|01|23|**13-Oct — 2016 15:01:23 PDT**|
|16|Paź|2016|16|22|33|16-Oct — 2016 15:01:33 PDT|
|17|Paź|2016|12|43|12|17-Oct — 2016 15:01:12 PDT|
|12|Lis|2016|14|22|44|12-lis-2016 15:01:44 PDT|
|23|Lis|2016|01|52|45|23-lis-2016 15:01:45 PDT|
|16|Sty|2017|22|34|56|16-Jan-2016 15:01:56 PDT|
|23|Mar|2017|01|55|25|23-marca-2016 15:01:25 PDT|
|16|Kwi|2017|11|34|36|16-kwietnia-2016 15:01:36 PDT|

