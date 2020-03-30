---
title: 'Model pociągu: Odwołanie do modułu'
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak użyć modułu **modelu pociągu** w usłudze Azure Machine Learning do uczenia modelu klasyfikacji lub regresji.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/11/2020
ms.openlocfilehash: 0a9728e05aee27e74054a77e2c9be7dc08968207
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79455863"
---
# <a name="train-model-module"></a>Moduł modelu pociągu

W tym artykule opisano moduł w projektancie usługi Azure Machine Learning (wersja zapoznawcza).

Ten moduł służy do trenowania modelu klasyfikacji lub regresji. Szkolenie odbywa się po zdefiniowaniu modelu i ustawieniu jego parametrów i wymaga oznakowanych danych. Można również użyć **modelu pociągu,** aby przeszkolić istniejący model z nowymi danymi. 

## <a name="how-the-training-process-works"></a>Jak działa proces szkolenia

W usłudze Azure Machine Learning tworzenie i używanie modelu uczenia maszynowego jest zazwyczaj procesem trzyetapowym. 

1. Można skonfigurować model, wybierając określony typ algorytmu i definiując jego parametry lub hiperparametry. Wybierz dowolny z następujących typów modeli: 

    + **Modele klasyfikacji,** oparte na sieciach neuronowych, drzewach decyzyjnych i lasach decyzyjnych oraz innych algorytmach.
    + **Modele regresji,** które mogą obejmować standardową regresję liniową lub które używają innych algorytmów, w tym sieci neuronowych i regresji Bayesa.  

2. Podaj zestaw danych, który jest oznaczony etykietą i ma dane zgodne z algorytmem. Połącz dane i model z **modelem pociągu**.

    To, co generuje szkolenie, to określony format binarny, iLearner, który hermetyzuje wzorce statystyczne wyciągnięte z danych. Nie można bezpośrednio modyfikować ani odczytywać tego formatu; jednak inne moduły mogą korzystać z tego przeszkolonego modelu. 
    
    Można również wyświetlić właściwości modelu. Aby uzyskać więcej informacji, zobacz sekcję Wyniki.

3. Po zakończeniu szkolenia użyj przeszkolonego modelu z jednym z [modułów punktacji,](./score-model.md)aby przewidzieć nowe dane.

## <a name="how-to-use-train-model"></a>Jak korzystać z modelu pociągu 
  
1.  W usłudze Azure Machine Learning skonfiguruj model klasyfikacji lub model regresji.
    
2. Dodaj **moduł Modelu pociągu** do potoku.  Ten moduł można znaleźć w kategorii **Uczenie maszynowe.** Rozwiń **węzeł Pociąg**, a następnie przeciągnij moduł **Modelu pociągu** do potoku.
  
3.  Na lewym wejściu podłącz tryb nieprzeszkolony. Dołącz zestaw danych szkoleniowych do po prawej stronie wejścia **Train Model**.

    Zestaw danych szkoleniowych musi zawierać kolumnę etykiety. Wszystkie wiersze bez etykiet są ignorowane.
  
4.  W polu **Kolumna Etykieta**kliknij pozycję **Edytuj kolumnę** w prawym panelu modułu i wybierz pojedynczą kolumnę zawierającą wyniki, których model może używać do szkolenia.
  
    - W przypadku problemów z klasyfikacją kolumna etykiety musi zawierać wartości **podzielone** na kategorii lub wartości **dyskretne.** Niektóre przykłady mogą być tak / nie ocena, kod klasyfikacji choroby lub nazwę, lub grupy dochodów.  Jeśli wybierzesz kolumnę niekategortyczną, moduł zwróci błąd podczas treningu.
  
    -   W przypadku problemów z regresją kolumna etykiety musi zawierać dane **liczbowe** reprezentujące zmienną odpowiedzi. W idealnym przypadku dane liczbowe reprezentują skalę ciągłą. 
    
    Przykładami mogą być ocena ryzyka kredytowego, przewidywany czas awarii dysku twardego lub prognozowana liczba połączeń z centrum obsługi w danym dniu lub godzinie.  Jeśli nie wybierzesz kolumny numerycznej, może pojawić się błąd.
  
    -   Jeśli nie określisz, która kolumna etykiety ma być używana, usługa Azure Machine Learning spróbuje wywnioskować, która kolumna jest odpowiednią etykietą, przy użyciu metadanych zestawu danych. Jeśli wybierze niewłaściwą kolumnę, użyj selektora kolumn, aby ją poprawić.
  
    > [!TIP] 
    > Jeśli masz problemy z używaniem selektora kolumn, zapoznaj się z artykułem [Wybieranie kolumn w zestawie danych,](./select-columns-in-dataset.md) aby uzyskać porady. Opisano w nim niektóre typowe scenariusze i wskazówki dotyczące korzystania **z opcji Z regułami** i **WEDŁUG NAZWY.**
  
5.  Prześlij potok. Jeśli masz dużo danych, może to trochę potrwać.

## <a name="results"></a><a name="bkmk_results"></a>Wyniki

Po przeszkoleniu modelu:


+ Aby użyć modelu w innych potokach, wybierz moduł i wybierz ikonę **Zarejestruj zestaw danych** na karcie **Wyjścia** w prawym panelu. Dostęp do zapisanych modeli można uzyskać w palecie **modułów**w obszarze Zestawy danych .

+ Aby użyć modelu w przewidywaniu nowych wartości, należy połączyć go z [modułem Score Model](./score-model.md) wraz z nowymi danymi wejściowymi.


## <a name="next-steps"></a>Następne kroki

Zobacz [zestaw modułów dostępnych dla](module-reference.md) usługi Azure Machine Learning. 