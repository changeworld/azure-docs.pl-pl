---
title: 'Krok 2: Przekazywanie danych do eksperymentu usługi Machine Learning Studio'
titleSuffix: Azure Machine Learning Studio
description: 'Krok 2 programowanie przewodnik rozwiązania do analizy predykcyjnej: Przekaż przechowywanych danych publicznych do usługi Azure Machine Learning Studio.'
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: article
author: garyericson
ms.author: garye
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/23/2017
ms.openlocfilehash: 4b23286a5a86b8d2731a34f39755b4fd18bbd78d
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/31/2019
ms.locfileid: "55507574"
---
# <a name="walkthrough-step-2-upload-existing-data-into-an-azure-machine-learning-studio-experiment"></a>Przewodnik, krok 2 Przekazywanie istniejących danych do eksperymentu usługi Azure Machine Learning Studio
Jest to drugi etap tego przewodnika, [tworzenia rozwiązania analizy predykcyjnej w usłudze Azure Machine Learning](walkthrough-develop-predictive-solution.md)

1. [Tworzenie obszaru roboczego usługi Machine Learning](walkthrough-1-create-ml-workspace.md)
2. **Przekazywanie istniejących danych**
3. [Tworzenie nowego eksperymentu](walkthrough-3-create-new-experiment.md)
4. [Nauczanie i ocena modeli](walkthrough-4-train-and-evaluate-models.md)
5. [Wdrażanie usługi sieci Web](walkthrough-5-publish-web-service.md)
6. [Dostęp do usługi sieci Web](walkthrough-6-access-web-service.md)

- - -
Aby opracować model predykcyjny dla oceny ryzyka kredytowego, potrzebujemy dane, które możemy użyć szkolenie, a następnie przetestować model. W tym przewodniku użyjemy "UCI Statlog (danych środków) Data Set" z repozytorium UC Irvine Machine Learning. Możesz go znaleźć tutaj:  
<a href="http://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)">http://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)</a>

Użyjemy plik o nazwie **german.data**. Pobierz ten plik na lokalnym dysku twardym.  

**German.data** zestaw danych zawiera wiersze 20 zmienne dla 1000 ostatnich kandydatów kredytu. Te zmienne 20 reprezentują zestaw funkcji, zestawu danych ( *wektor funkcji*), zapewniającą cechy identyfikacyjne dla każdego zgłaszającego środków. Dodatkową kolumnę w każdym wierszu reprezentuje ryzyka kredytowego obliczeniowe wnioskodawca, z 700 kandydatami zidentyfikowane jako niskie ryzyko kredytowe i 300 jako wysokiego ryzyka.

Witryny sieci Web UCI zawiera opis atrybutów wektor funkcja dla tych danych. Obejmuje to informacje finansowe, Historia kredytów, status zatrudnienia i dane osobowe. Dla każdego zgłaszającego binarne klasyfikacji została danej, wskazująca, czy są one działania niskiej lub wysokie ryzyko kredytowe. 

Użyjemy tych danych do nauczenia modelu analizy predykcyjnej. Gdy wszystko jest gotowe, nasz model powinien móc zaakceptować wektor funkcji dla poszczególnych nowych i przewidywania, czy użytkownik jest ryzyko kredytowe niskie lub wysokie.  

Oto interesujące akcentem. Opis zestawu danych w witrynie sieci Web UCI uwagi, co jej koszty, jeśli firma Microsoft misclassify ryzyko kredytowe osoby.
Jeśli model przewiduje ryzyko kredytowe wysokiej osobie, która jest faktycznie niskie ryzyko kredytowe, modelu biznesowego uczyniło system błędu klasyfikacji.
Ale odwrotnej błędu klasyfikacji jest pięć razy bardziej kosztowne instytucji finansowej: Jeśli model przewiduje niskie ryzyko kredytowe osobie, która jest faktycznie ryzyko kredytowe wysoka.

Dlatego chcemy szkolenie nasz model, aby koszt tego typu ostatniego błędu klasyfikacji jest pięć razy wyższa niż misclassifying inny sposób.
Prostym sposobem na to zrobić podczas uczenia modelu w naszym doświadczeniu jest duplikując (pięć razy) tych wpisów, które reprezentują inna osoba z ryzyko kredytowe wysoka. Następnie jeśli model klasyfikuje ktoś jako niskie ryzyko kredytowe, gdy są one faktycznie wysokiego ryzyka, model nie tego samego błędu klasyfikacji pięć razy raz dla wszystkich duplikatów. Takie rozwiązanie zwiększy koszty wystąpienia tego błędu w wynikach szkolenia.


## <a name="convert-the-dataset-format"></a>Konwertuj format zestawu danych
Oryginalnego zestawu danych w formacie oddzielone od podstaw. Usługa Machine Learning Studio sprawdzi się najlepiej przy użyciu pliku wartości rozdzielanych przecinkami (CSV), dzięki czemu firma Microsoft będzie przekonwertować zestawu danych, zastępując miejsca do magazynowania za pomocą przecinków.  

Istnieje wiele sposobów, aby przekonwertować te dane. Jednym ze sposobów polega na użyciu następującego polecenia programu Windows PowerShell:   

    cat german.data | %{$_ -replace " ",","} | sc german.csv  

Innym sposobem jest za pomocą polecenia sed Unix:  

    sed 's/ /,/g' german.data > german.csv  

W obu przypadkach utworzyliśmy rozdzielonych przecinkami wersji dane w pliku o nazwie **german.csv** , firma Microsoft można używać w naszym eksperymentu.

## <a name="upload-the-dataset-to-machine-learning-studio"></a>Przekaż zestaw danych w usłudze Machine Learning Studio
Po danych został przekonwertowany do formatu CSV, należy przekazać go do usługi Machine Learning Studio. 

1. Otwórz stronę główną usługi Machine Learning Studio ([https://studio.azureml.net](https://studio.azureml.net)). 

2. Kliknij menu ![Menu][1] w lewym górnym rogu okna kliknij **usługi Azure Machine Learning**, wybierz opcję **Studio**i zaloguj się.

3. Kliknij przycisk **+ nowy** w dolnej części okna.

4. Wybierz **zestawu danych**.

5. Wybierz **z pliku lokalnego**.

    ![Dodaj zestaw danych z pliku lokalnego][2]

6. W **przekazać nowy zestaw danych** okno dialogowe, kliknij przycisk **Przeglądaj** i Znajdź **german.csv** utworzony plik.

7. Wprowadź nazwę dla zestawu danych. W tym przewodniku wywołać ją "Dane karty kredytowej niemiecki UCI".

8. Dla typu danych, wybierz **ogólny plik CSV, bez nagłówka (. nh.csv)**.

9. Dodaj opis, jeśli chcesz.

10. Kliknij przycisk **OK** znacznik wyboru.  

    ![Przekaż zestaw danych][3]

To przekazywanie danych do modułu zestawu danych, której używamy w eksperymencie.

Możesz zarządzać zestawów danych, które zostało przesłane do Studio, klikając **zestawów danych** karty po lewej stronie okna Studio.

![Zarządzanie zestawami danych][4]

Aby uzyskać więcej informacji na temat importowania inne typy danych do eksperymentu, zobacz [importowanie danych szkoleniowych do usługi Azure Machine Learning Studio](import-data.md).

**Dalej: [Tworzenie nowego eksperymentu](walkthrough-3-create-new-experiment.md)**

[1]: media/walkthrough-2-upload-data/menu.png
[2]: media/walkthrough-2-upload-data/add-dataset.png
[3]: media/walkthrough-2-upload-data/upload-dataset.png
[4]: media/walkthrough-2-upload-data/dataset-list.png
