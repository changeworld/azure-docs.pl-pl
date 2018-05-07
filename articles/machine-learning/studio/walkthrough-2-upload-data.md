---
title: 'Krok 2: Prześlij dane do eksperymentu uczenia maszynowego | Dokumentacja firmy Microsoft'
description: 'Krok 2 opracowanie wskazówki rozwiązanie predykcyjne: przekazywanie przechowywanych danych publicznych do usługi Azure Machine Learning Studio.'
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cgronlun
ms.assetid: 9f4bc52e-9919-4dea-90ea-5cf7cc506d85
ms.service: machine-learning
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2017
ms.openlocfilehash: 6fb83fbd3106eef9885c50e140a4344e03d901e7
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/03/2018
---
# <a name="walkthrough-step-2-upload-existing-data-into-an-azure-machine-learning-experiment"></a>Przewodnik, krok 2. Przekazywanie istniejących danych do eksperymentu usługi Azure Machine Learning
Jest to drugi etap wskazówki, [tworzenie rozwiązania analizy predykcyjnej w usłudze Azure Machine Learning](walkthrough-develop-predictive-solution.md)

1. [Tworzenie obszaru roboczego usługi Machine Learning](walkthrough-1-create-ml-workspace.md)
2. **Przekazywanie istniejących danych**
3. [Tworzenie nowego eksperymentu](walkthrough-3-create-new-experiment.md)
4. [Nauczanie i ocena modeli](walkthrough-4-train-and-evaluate-models.md)
5. [Wdrażanie usługi sieci Web](walkthrough-5-publish-web-service.md)
6. [Dostęp do usługi sieci Web](walkthrough-6-access-web-service.md)

- - -
Aby opracować model predykcyjny dla oceny ryzyka kredytowego, potrzebujemy możemy użyć do nauczenia i przetestowania następnie modelu danych. W ramach tego przewodnika użyjemy "UCI Statlog (niemieckim dane środki) zestawu danych" z repozytorium UC Irvine Machine Learning. Możesz go znaleźć tutaj:  
<a href="http://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)">http://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)</a>

Użyjemy plik o nazwie **german.data**. Pobierz ten plik na lokalnym dysku twardym.  

**German.data** dataset zawiera wiersze 20 zmiennych dla 1000 ostatnich kandydatów do uznania. Te zmienne 20 reprezentują zestaw funkcji zestawu danych ( *wektor funkcji*), zapewniające cechy identyfikacyjne dla każdego zgłaszającego kredytowej. Dodatkowe kolumny w każdym wierszu reprezentuje kandydata ryzyka kredytowego obliczeniowej, z 700 kandydatami zidentyfikowane jako niskie ryzyko kredytowe i 300 jako wysokiego ryzyka.

UCI witryny sieci Web zawiera opis atrybutów wektor funkcji dla tych danych. W tym informacji finansowych, historii kredytów status zatrudnienia i informacje osobiste. Dla każdego zgłaszającego klasyfikacji binarnej został podany, wskazującą, czy są one niski lub wysoki ryzyka karty kredytowej. 

Użyjemy tych danych do uczenia modelu analizy predykcyjnej. Gdy skończymy, naszego modelu powinno być możliwe do akceptowania wektor funkcji dla nowej osoby i prognozowania, czy użytkownik jest ryzyko kredytowe niski i wysoki.  

Oto interesujące dołączony. Opis zestawu danych w witrynie sieci Web UCI wymienia co kosztuje, jeśli firma Microsoft misclassify ryzyko kredytowe osoby.
Jeśli model przewiduje ryzyko kredytowe wysokiej osobie, która jest rzeczywiście niskie ryzyko kredytowe modelu wprowadził błędną klasyfikację.
Ale odwrotnej błędną klasyfikację jest pięć razy droższy instytucji finansowych: Jeśli model przewiduje niskie ryzyko kredytowe osoby, która jest rzeczywiście ryzyko kredytowe wysokiej.

Dlatego chcemy nauczenia modelu, aby koszt ten ostatni typ błędu klasyfikacji jest pięć razy wyższa niż misclassifying inny sposób.
Prostym sposobem to zrobić podczas uczenia modelu w naszym doświadczeniu jest duplikując (pięć razy) zapisów reprezentujących ktoś z ryzyko kredytowe wysoki. Następnie jeśli model klasyfikuje ktoś jako niskie ryzyko kredytowe, gdy są faktycznie wysokiego ryzyka, model nie tego samego błędu klasyfikacji pięć razy raz dla każdego duplikatu. To spowoduje zwiększenie kosztów tego błędu w wynikach szkolenia.


## <a name="convert-the-dataset-format"></a>Konwertuj w formacie zestawu danych
Oryginalnego zestawu danych w formacie oddzielone puste. Usługa Machine Learning Studio działa lepiej z pliku wartości rozdzielanych przecinkami (CSV), więc będzie nie możemy przekonwertować zestawu danych, zastępując spacje przecinkami.  

Istnieje wiele sposobów, aby przekonwertować te dane. Jednym ze sposobów jest za pomocą następującego polecenia programu Windows PowerShell:   

    cat german.data | %{$_ -replace " ",","} | sc german.csv  

Innym sposobem jest za pomocą polecenia mniejszyć Unix:  

    sed 's/ /,/g' german.data > german.csv  

W obu przypadkach firma Microsoft opracowała przecinkami wersja danych w pliku o nazwie **german.csv** który możemy użyć w naszym doświadczeniu.

## <a name="upload-the-dataset-to-machine-learning-studio"></a>Przekaż zestawu danych do usługi Machine Learning Studio
Po danych został przekonwertowany do formatu CSV, należy przekazać go do usługi Machine Learning Studio. 

1. Otwórz stronę główną Machine Learning Studio ([https://studio.azureml.net](https://studio.azureml.net)). 

2. Kliknij menu ![Menu][1] w lewym górnym rogu okna kliknij **usługi Azure Machine Learning**, wybierz pozycję **Studio**i zaloguj się.

3. Kliknij przycisk **+ nowy** w dolnej części okna.

4. Wybierz **zestawu danych**.

5. Wybierz **z pliku lokalnego**.

    ![Dodaj zestaw danych z pliku lokalnego][2]

6. W **przekazać nowy zestaw danych** okna dialogowego, kliknij przycisk **Przeglądaj** i Znajdź **german.csv** utworzony plik.

7. Wprowadź nazwę dla zestawu danych. W ramach tego przewodnika wywołać ją "Dane karty kredytowej niemiecki UCI".

8. Dla typu danych, wybierz **ogólnego pliku CSV bez nagłówka (. nh.csv)**.

9. Dodaj opis, jeśli chcesz.

10. Kliknij przycisk **OK** znacznik wyboru.  

    ![Przekaż zestawu danych][3]

To przekazywanie danych do modułu zestawu danych, które firma Microsoft może używać w eksperymencie.

Możesz zarządzać zestawów danych, które przekazanego do Studio, klikając **zestawów danych** kartę z lewej strony okna Studio.

![Zarządzanie zbiory danych][4]

Aby uzyskać więcej informacji o importowaniu inne typy danych do eksperymentu, zobacz [importowanie danych szkoleniowych w usłudze Azure Machine Learning Studio](import-data.md).

**Następnie: [Tworzenie nowego eksperymentu](walkthrough-3-create-new-experiment.md)**

[1]: media/walkthrough-2-upload-data/menu.png
[2]: media/walkthrough-2-upload-data/add-dataset.png
[3]: media/walkthrough-2-upload-data/upload-dataset.png
[4]: media/walkthrough-2-upload-data/dataset-list.png
