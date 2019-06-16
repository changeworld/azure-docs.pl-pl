---
title: 'Edytowanie metadanych: Dokumentacja modułu'
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak użyć modułu programu edytować metadane w usłudze Azure Machine Learning, aby zmienić metadane, która jest skojarzona z kolumn w zestawie danych.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 282652adb917450c262e08bf10c3c6e537b829e7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67072717"
---
# <a name="edit-metadata-module"></a>Edytuj moduł metadanych

W tym artykule opisano moduł interfejs graficzny (wersja zapoznawcza) dla usługi Azure Machine Learning.

Aby zmienić metadane, która jest skojarzona z kolumn w zestawie danych, należy skorzystać z modułu edytowanie danych. Po użyciu modułu edytować metadane zmieni się wartość i typ danych zestawu danych.

Typowe metadanych zmiany mogą być następujące:
  
+ Traktowanie kolumn logiczną lub liczbowego jako wartości podzielonych na kategorie.
  
+ Wskazujący, która kolumna zawiera **klasy** etykiety lub zawiera wartości, aby kategoryzować lub przewidzieć.
  
+ Oznaczanie kolumn jako funkcji.
  
+ Zmiana wartości daty/godziny na wartości liczbowe lub na odwrót.
  
+ Zmienianie nazw kolumn.
  
 Użyj edytować metadane w dowolnym momencie należy zmodyfikować definicję kolumny, zazwyczaj pod kątem wymagań dla modułu podrzędnego. Na przykład niektóre moduły działają tylko z konkretnych typów danych lub wymaga flagi w kolumnach, takich jak `IsFeature` lub `IsCategorical`.  
  
 Po wykonaniu operacji wymagane, możesz zresetować metadanych do stanu pierwotnego.
  
## <a name="configure-edit-metadata"></a>Konfigurowanie Edytuj metadane
  
1. W usłudze Azure Machine Learning Dodaj moduł edytować metadane do eksperymentu i połącz zestaw danych, które chcesz zaktualizować. Można znaleźć zestawu danych w ramach **przekształcania danych** w **manipulowanie** kategorii.
  
1. Wybierz **uruchamianie selektora kolumn** i wybierz kolumny lub zestaw kolumn, które chcesz pracować. Można wybrać kolumny indywidualnie według nazwy lub indeksu, lub możesz wybrać grupę kolumn według typu.  
  
1. Wybierz **— typ danych** opcję, jeśli trzeba przypisać różne typy danych w wybranych kolumnach. Konieczne może być zmiana typu danych dla niektórych operacji. Na przykład jeśli źródłowy zestaw danych zawiera numery obsługiwane jako tekst, użytkownik musi je zmienić na dane typu liczbowego przed rozpoczęciem korzystania z operacji matematycznych.

    + Obsługiwane typy danych są **ciąg**, **całkowitą**, **Double**, **logiczna**, i **daty/godziny**.

    + Jeśli wybierzesz wiele kolumn, należy zastosować zmiany metadanych *wszystkich* wybrane kolumny. Załóżmy na przykład, że możesz wybrać dwie lub trzy kolumny liczbowe. Można je zmienić, wszystko na string, typ danych i zmień je w ramach jednej operacji. Jednak nie możesz zmienić jedną kolumnę na typ danych ciągu, a inna kolumna z liczba zmiennoprzecinkowa na liczbę całkowitą.
  
    + Metadane kolumn nie są bez zmian, jeśli nie określisz nowy typ danych.

    + Typ kolumny i wartości zmieni się po wykonaniu operacji edycji metadanych. Oryginalnego typu danych można odzyskać w dowolnej chwili za pomocą edycji metadanych można zresetować typ danych kolumny.  

    > [!NOTE]
    > W przypadku zmiany dowolnego typu liczby do **daty/godziny** wpisz, pozostaw **formacie daty/godziny** pole puste. Obecnie nie można określić format danych docelowych.  

1. Wybierz **podzielone** opcję, aby określić, że wartości w zaznaczonych kolumnach powinny być traktowane jako kategorie.

    Na przykład możesz mieć kolumnę zawierającą liczby 0, 1 i 2, ale wiedzieć, że numery faktycznie oznacza "Smoker", "Non-smoker" i "Nieznany". W takiej sytuacji, oznaczając flagą tej kolumny jako podzielonych na kategorie kolumny możesz upewnij się, że wartości są używane tylko do grupowania danych, a nie w obliczeniach liczbowych.
  
1. Użyj **pola** opcję, jeśli chcesz zmienić sposób, że usługa Azure Machine Learning korzysta z danych w modelu.

    + **Funkcja**: Użyj tej opcji do kolumny jako funkcję w modułach, które działają tylko w kolumnach funkcji. Domyślnie wszystkie kolumny początkowo są traktowane jako funkcje.  
  
    + **Etykieta**: Użyj tej opcji, aby oznaczyć etykiety, który jest również nazywany przewidywalne zmiennej atrybutu lub elementu docelowego. Wiele modułów wymaga tej kolumny dokładnie jednej etykiety jest obecny w zestawie danych.

        W wielu przypadkach usługi Azure Machine Learning można wywnioskować, że kolumna zawiera etykietę klasy. Ustawiając te metadane, można zagwarantować, że kolumna jest prawidłowo zidentyfikowany. Ustawienie tej opcji nie powoduje zmiany wartości danych. Zmienia sposób, że niektóre algorytmów uczenia maszynowego obsługi danych.
  
    > [!TIP]
    > Czy masz dane, które nie pasuje do tych kategorii? Na przykład zestaw danych może zawierać wartości, takich jak unikatowe identyfikatory, które nie są przydatne jako zmienne. Czasami takie identyfikatory może spowodować problemy, gdy są używane w modelu.
    >
    > Na szczęście usługi Azure Machine Learning przechowuje wszystkie dane, dzięki czemu nie trzeba Usuń takie kolumny z zestawu danych. Gdy trzeba wykonywać operacje na niektóre specjalnego zestawu kolumn, wystarczy usunąć inne kolumny tymczasowo przy użyciu [Select Columns in Dataset](select-columns-in-dataset.md) modułu. Później można scalać kolumny do zestawu danych za pomocą [Dodaj kolumny](add-columns.md) modułu.  
  
1. Aby wyczyścić poprzednich wyborów i przywrócić metadanych do wartości domyślnych, należy użyć następujących opcji.  
  
    + **Wyczyść funkcji**: Opcja ta służy do usuwania flagi funkcji.  
  
         Wszystkie kolumny początkowo są traktowane jako funkcje. Dla modułów, które wykonują operacje matematyczne może być konieczne użycie tę opcję, aby zapobiec kolumny liczbowe są traktowane jako zmienne.
  
    + **Wyczyść etykiety**: Użyj tej opcji, aby usunąć **etykiety** metadanych z określonej kolumny.  
  
    + **Wyczyść wynik**: Użyj tej opcji, aby usunąć **wynik** metadanych z określonej kolumny.  
  
         Obecnie nie można jawnie oznaczyć kolumny jako wynik w usłudze Azure Machine Learning. Jednak niektóre operacje spowodować kolumny są oznaczone jako wynik wewnętrznie. Ponadto niestandardowego modułu R może być danych wyjściowych wartości wyniku.

1. Aby uzyskać **nowych nazw kolumn**, wprowadź nową nazwę wybranej kolumny lub kolumn.  
  
    + Nazwy kolumn, można użyć tylko te znaki, które są obsługiwane przez UTF-8 kodowania. Puste ciągi, wartości null lub nazwy, składać się wyłącznie z miejsc do magazynowania, które nie są dozwolone.  
  
    + Aby zmienić nazwę wiele kolumn, należy wprowadzić nazwy jako rozdzielaną przecinkami listę w kolejności indeksy kolumn.  
  
    + Można zmienić nazwy wszystkich zaznaczonych kolumn. Nie można pominąć lub pominąć kolumn.  
  
1. Uruchom eksperyment.  

## <a name="next-steps"></a>Kolejne kroki

Zobacz [zestaw dostępnych modułów](module-reference.md) do usługi Azure Machine Learning.
