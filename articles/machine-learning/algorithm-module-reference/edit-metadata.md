---
title: 'Edytuj metadane: odwołanie do modułu'
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak zmienić metadane skojarzone z kolumnami w zestawie danych za pomocą modułu Edytuj metadane w usłudze Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/11/2020
ms.openlocfilehash: 9853a3decc8d145fee58d1da526926e224ee2030
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064242"
---
# <a name="edit-metadata-module"></a>Edytuj moduł metadanych

W tym artykule opisano moduł uwzględniony w projektancie usługi Azure Machine Learning (wersja zapoznawcza).

Użyj modułu Edytuj metadane, aby zmienić metadane skojarzone z kolumnami w zestawie danych. Wartość i typ danych zestawu danych zmienią się po użyciu modułu Edytuj metadane.

Typowe zmiany metadanych mogą obejmować:
  
+ Traktowanie kolumn logicznych lub liczbowych jako wartości kategorii.
  
+ Wskazując, która kolumna zawiera etykietę **klasy** lub zawiera wartości, które chcesz skategoryzować lub przewidzieć.
  
+ Oznaczanie kolumn jako operacji.
  
+ Zmiana wartości daty/godziny na wartości liczbowe lub odwrotnie.
  
+ Zmiana nazwy kolumn.
  
 Użyj edytować metadane w dowolnym momencie trzeba zmodyfikować definicję kolumny, zazwyczaj w celu spełnienia wymagań dla modułu podrzędnego. Na przykład niektóre moduły działają tylko z określonymi typami danych `IsFeature` `IsCategorical`lub wymagają flag na kolumnach, takich jak lub .  
  
 Po wykonaniu wymaganej operacji można zresetować metadane do stanu pierwotnego.
  
## <a name="configure-edit-metadata"></a>Konfigurowanie metadanych edycji
  
1. W projektancie usługi Azure Machine Learning dodaj moduł Edytuj metadane do potoku i połącz zestaw danych, który chcesz zaktualizować. Moduł można znaleźć w kategorii **Transformacja danych.**
  
1. Kliknij **pozycję Edytuj kolumnę** w prawym panelu modułu i wybierz kolumnę lub zestaw kolumn do pracy. Kolumny można wybierać indywidualnie według nazwy lub indeksu lub można wybrać grupę kolumn według typu.  
  
1. Wybierz opcję **Typ danych,** jeśli chcesz przypisać inny typ danych do wybranych kolumn. Może być konieczna zmiana typu danych dla niektórych operacji. Na przykład jeśli źródłowy zestaw danych ma liczby obsługiwane jako tekst, należy zmienić je na typ danych liczbowych przed użyciem operacji matematycznych.

    + Obsługiwane typy danych to **Ciąg,** **Liczba całkowita,** **Podwójna,** **Logiczna**i **DateTime**.

    + Jeśli wybierzesz wiele kolumn, musisz zastosować zmiany metadanych do *wszystkich* wybranych kolumn. Załóżmy na przykład, że wybierasz dwie lub trzy kolumny liczbowe. Można zmienić je wszystkie na typ danych ciągu i zmienić ich nazwę w jednej operacji. Jednak nie można zmienić jedną kolumnę na typ danych ciągu i inną kolumnę z float do liczby całkowitej.
  
    + Jeśli nie określisz nowego typu danych, metadane kolumny pozostaną niezmienione.

    + Typ kolumny i wartości zmienią się po wykonaniu operacji Edytuj metadane. Oryginalny typ danych można odzyskać w dowolnym momencie, używając polecenia Edytuj metadane, aby zresetować typ danych kolumny.  

    > [!NOTE]
    > Jeśli zmienisz dowolny typ liczby na typ **DateTime,** pozostaw pole **Format DateTime** puste. Obecnie nie można określić formatu danych docelowych.  

1. Wybierz opcję **Kategoryczna,** aby określić, że wartości w wybranych kolumnach powinny być traktowane jako kategorie.

    Na przykład może być kolumna, która zawiera liczby 0, 1 i 2, ale wiem, że liczby faktycznie oznaczają "Palacz", "Niepalący" i "Nieznany". W takim przypadku, oflagując kolumnę jako kategoryczną, należy upewnić się, że wartości są używane tylko do grupowania danych, a nie do obliczeń liczbowych.
  
1. Użyj **pola** opcji, jeśli chcesz zmienić sposób, w jaki usługa Azure Machine Learning używa danych w modelu.

    + **Funkcja**: Użyj tej opcji, aby oznaczyć kolumnę jako operację w modułach, które działają tylko na kolumnach elementów. Domyślnie wszystkie kolumny są początkowo traktowane jako operacje.  
  
    + **Etykieta:** Użyj tej opcji, aby oznaczyć etykietę, która jest również nazywana przewidywalnym atrybutem lub zmienną docelową. Wiele modułów wymaga, aby dokładnie jedna kolumna etykiety była obecna w zestawie danych.

        W wielu przypadkach usługi Azure Machine Learning można wywnioskować, że kolumna zawiera etykietę klasy. Ustawiając te metadane, można upewnić się, że kolumna jest poprawnie identyfikowana. Ustawienie tej opcji nie powoduje zmiany wartości danych. Zmienia tylko sposób, w jaki niektóre algorytmy uczenia maszynowego obsługują dane.
  
    > [!TIP]
    > Czy masz dane, które nie pasują do tych kategorii? Na przykład zestaw danych może zawierać wartości, takie jak unikatowe identyfikatory, które nie są przydatne jako zmienne. Czasami takie identyfikatory mogą powodować problemy, gdy są używane w modelu.
    >
    > Na szczęście usługa Azure Machine Learning przechowuje wszystkie dane, dzięki czemu nie trzeba usuwać takich kolumn z zestawu danych. Jeśli trzeba wykonać operacje na jakimś specjalnym zestawie kolumn, po prostu usuń wszystkie inne kolumny tymczasowo za pomocą [modułu Wybierz kolumny w zestawie danych.](select-columns-in-dataset.md) Później można scalić kolumny z powrotem do zestawu danych przy użyciu modułu [Dodaj kolumny.](add-columns.md)  
  
1. Użyj następujących opcji, aby wyczyścić poprzednie wybory i przywrócić metadane do wartości domyślnych.  
  
    + **Wyczyść funkcję**: Użyj tej opcji, aby usunąć flagę operacji.  
  
         Wszystkie kolumny są początkowo traktowane jako operacje. W przypadku modułów wykonujących operacje matematyczne może być konieczne użycie tej opcji, aby zapobiec traktowaniu kolumn liczbowych jako zmiennych.
  
    + **Wyczyść etykietę:** Użyj tej opcji, aby usunąć metadane **etykiety** z określonej kolumny.  
  
    + **Wyczyść wynik:** Użyj tej opcji, aby usunąć metadane **wynik** z określonej kolumny.  
  
         Obecnie nie można jawnie oznaczyć kolumnę jako wynik w usłudze Azure Machine Learning. Jednak niektóre operacje powodują kolumnę oflagowane jako wynik wewnętrznie. Ponadto niestandardowy moduł R może wyprowadzać wartości wyników.

1. W polu **Nowe nazwy kolumn**wprowadź nową nazwę zaznaczonej kolumny lub kolumn.  
  
    + Nazwy kolumn mogą używać tylko znaków obsługiwanych przez kodowanie UTF-8. Puste ciągi, wartości null lub nazwy, które składają się wyłącznie ze spacji, nie są dozwolone.  
  
    + Aby zmienić nazwę wielu kolumn, wprowadź nazwy jako listę oddzieloną przecinkami w kolejności indeksów kolumn.  
  
    + Należy zmienić nazwę wszystkich zaznaczonych kolumn. Nie można pominąć ani pominąć kolumn.  
  
1. Prześlij potok.  

## <a name="next-steps"></a>Następne kroki

Zobacz [zestaw modułów dostępnych dla](module-reference.md) usługi Azure Machine Learning.
