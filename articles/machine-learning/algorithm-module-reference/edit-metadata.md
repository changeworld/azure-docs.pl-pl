---
title: 'Edytowanie metadanych: Odwołania do modułu'
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak użyć modułu edytować metadane w usłudze Azure Machine Learning można zmienić metadanych, który jest skojarzony z kolumn w zestawie danych.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: cfee607aca155b6cf68e5bddc40eb9c752df5e34
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028818"
---
# <a name="edit-metadata-module"></a>Edytuj moduł metadanych

W tym artykule opisano moduł interfejs graficzny (wersja zapoznawcza) dla usługi Azure Machine Learning.

Ten moduł służy do zmiany metadanych, który jest skojarzony z kolumn w zestawie danych. Wartość i typ danych zestawu danych zostaną zmienione po użyciu **edytować metadane** modułu. 
 
Typowe metadanych zmiany mogą być następujące:
  
+ Traktowanie kolumn logiczną lub liczbowego jako wartości podzielonych na kategorie  
  
+ Wskazujący, która kolumna zawiera *klasy* etykiety lub wartości, aby kategoryzować lub prognozowania  
  
+ Oznaczanie kolumn jako funkcji
  
+ Zmiana daty/godziny wartości do wartości numerycznej lub na odwrót  
  
+ Zmienianie nazw kolumn
  
 Użyj [Edytuj metadanych w dowolnym momencie należy zmodyfikować definicję kolumny, zazwyczaj pod kątem wymagań dla modułu podrzędnego. Na przykład niektóre moduły mogą pracować tylko z konkretnych typów danych lub wymagają flagi w kolumnach, takich jak `IsFeature` lub `IsCategorical`.  
  
 Po wykonaniu operacji wymagane, możesz zresetować metadanych do stanu pierwotnego. 
  
## <a name="configure-edit-metadata"></a>Konfigurowanie Edytuj metadane
  
1.  W usłudze Azure Machine Learning, należy dodać [edytować metadane](./edit-metadata.md) modułu do eksperymentu i Połącz z zestawu danych ma zostać zaktualizowany. Znajdziesz go w folderze **przekształcania danych**w **manipulowanie** kategorii.
  
2.  Kliknij przycisk **uruchamianie selektora kolumn** i wybierz kolumny lub zestaw kolumn, które chcesz pracować. Wybierz kolumny indywidualnie według nazwy lub indeksu, lub możesz wybrać grupę kolumn według typu.  
  
3.  Wybierz **— typ danych** opcję, jeśli trzeba przypisać różne typy danych w wybranych kolumnach. Zmienianie danych typu mogą być wymagane dla określonych operacji: na przykład, jeśli źródłowy zestaw danych zawiera numery obsługiwane jako tekst, użytkownik musi je zmienić na dane typu liczbowego przed rozpoczęciem korzystania z operacji matematycznych. 

    + Obsługiwane typy danych są `String`, `Integer`, `Double`, `Boolean`, `DateTime`. 

    + Jeśli wybrano wiele kolumn, należy zastosować zmiany metadanych **wszystkich** wybrane kolumny. Załóżmy na przykład, że wybierzesz 2 – 3 kolumn liczbowych. Można je zmienić wszystkie na typie danych ciągu, a następnie zmień je w ramach jednej operacji. Jednak nie możesz zmienić jedną kolumnę na typ danych ciągu, a inna kolumna z liczba zmiennoprzecinkowa na liczbę całkowitą.
  
    + Jeśli nie zostanie nowy typ danych, metadane kolumn nie są bez zmian. 
    
    + Typ kolumny i wartości zostaną zmienione po wykonać [edytować metadane](./edit-metadata.md) operacji. Oryginalny typ danych można odzyskać w dowolnej chwili, używając [edytować metadane](./edit-metadata.md) zresetować typ danych kolumny.  

    > [!NOTE]
    > W przypadku zmiany dowolnego typu liczby do **daty/godziny** wpisz, pozostaw **formacie daty/godziny** pole puste. Obecnie nie jest możliwe określić format danych docelowych.  

      
4.  Wybierz **podzielone** opcję, aby określić, że wartości w zaznaczonych kolumnach powinny być traktowane jako kategorie. 

    Na przykład możesz mieć kolumnę zawierającą liczby 0,1 i 2, ale wiedzieć, że numery faktycznie oznacza "Smoker", "Non-smoker" i "Nieznany". W takiej sytuacji, oznaczając flagą tej kolumny jako podzielonych na kategorie kolumny można zagwarantować, że wartości nie są używane do obliczeń numerycznych, tylko do grupowania danych. 
  
5.  Użyj **pola** opcję, jeśli chcesz zmienić sposób, że usługa Azure Machine Learning korzysta z danych w modelu.

    + **Funkcja**: Użyj tej opcji do kolumny jako funkcja, do użytku z programem moduły, które działają tylko w kolumnach funkcji. Domyślnie wszystkie kolumny początkowo są traktowane jako funkcje.  
  
    + **Etykieta**: Użyj tej opcji, aby oznaczyć etykiety (znany także jako atrybutu wartości prognozowanych lub Zmienna docelowa). Wiele modułów wymagają, że co najmniej jedna (i tylko jeden) kolumna etykiety znajdować się w zestawie danych. 
    
        W wielu przypadkach usługi Azure Machine Learning można wywnioskować, że kolumna zawiera etykietę klasy, ale przez ustawienie metadanych można zapewnić, że kolumna jest prawidłowo zidentyfikowany. Ustawienie tej opcji nie powoduje zmiany wartości danych, wyłącznie na sposób, że niektóre algorytmów uczenia maszynowego obsługi danych.
  

  
    > [!TIP]
    >  Znajdują się dane, który nie mieści się na następujące kategorie?  Na przykład zestaw danych może zawierać wartości, takich jak unikatowe identyfikatory, które nie są przydatne jako zmienne. Czasami identyfikatorów może spowodować problemy, gdy są używane w modelu. 
    >   
    >  Na szczęście "dzieje się w tle" Azure Machine Learning przechowuje wszystkie dane, dzięki czemu nie trzeba Usuń takie kolumny z zestawu danych. Gdy trzeba wykonywać operacje na niektóre specjalnego zestawu kolumn, wystarczy usunąć inne kolumny tymczasowo przy użyciu [Select Columns in Dataset](./select-columns-in-dataset.md) modułu. Później można scalać kolumny do zestawu danych za pomocą [Dodaj kolumny](./add-columns.md) modułu.  
  
6. Aby wyczyścić poprzednich wyborów i przywrócić metadanych do wartości domyślnych, należy użyć następujących opcji.  
  
    + **Wyczyść funkcji**: Opcja ta służy do usuwania flagi funkcji.  
  
         Ponieważ wszystkie kolumny początkowo są traktowane jako funkcji dla modułów, które wykonują operacje matematyczne, może być konieczne użycie tej opcji umożliwia zapobieganie kolumny liczbowe są traktowane jako zmienne.
  
    + **Wyczyść etykiety**: Użyj tej opcji, aby usunąć **etykiety** metadanych z określonej kolumny.  
  
    + **Wyczyść wynik**: Użyj tej opcji, aby usunąć **wynik** metadanych z określonej kolumny.  
  
         Możliwość wyraźnie oznaczyć kolumnę jako wynik nie jest obecnie dostępna w usłudze Azure Machine Learning. Jednak niektóre operacje spowodować kolumny są oznaczone jako wynik wewnętrznie. Ponadto niestandardowego modułu R może być danych wyjściowych wartości wyniku.
  
  
7.  Aby uzyskać **nowych nazw kolumn**, wpisz nową nazwę wybranej kolumny lub kolumn.  
  
    + Nazwy kolumn, można użyć tylko te znaki, które są obsługiwane przez UTF-8 kodowania. Nazwy, składające się wyłącznie z miejsc do magazynowania, wartości null lub puste ciągi nie są dozwolone.  
  
    + Aby zmienić nazwę wiele kolumn, wpisz nazwy jako rozdzielaną przecinkami listę w kolejności indeksów kolumn.  
  
    + Można zmienić nazwy wszystkich zaznaczonych kolumn. Nie można pominąć lub pominąć kolumn.  
  
  
8.  Uruchom eksperyment.  

## <a name="next-steps"></a>Kolejne kroki

Zobacz [zestaw dostępnych modułów](module-reference.md) do usługi Azure Machine Learning. 