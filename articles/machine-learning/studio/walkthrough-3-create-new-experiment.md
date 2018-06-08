---
title: 'Krok 3: Tworzenie nowego eksperymentu uczenia maszynowego | Dokumentacja firmy Microsoft'
description: 'Krok 3 opracowanie wskazówki rozwiązanie predykcyjne: Tworzenie nowego eksperymentu uczenia w usłudze Azure Machine Learning Studio.'
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cgronlun
ms.assetid: 660e3c27-55ef-4c33-a4e9-dff4d1224630
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2017
ms.openlocfilehash: 95000f9fb57b95bf1edcda9abfba3668b5f5b523
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "34835743"
---
# <a name="walkthrough-step-3-create-a-new-azure-machine-learning-experiment"></a>Przewodnik, krok 3. Tworzenie nowego eksperymentu usługi Azure Machine Learning
Jest to trzeci krok wskazówki, [tworzenie rozwiązania analizy predykcyjnej w usłudze Azure Machine Learning](walkthrough-develop-predictive-solution.md)

1. [Tworzenie obszaru roboczego usługi Machine Learning](walkthrough-1-create-ml-workspace.md)
2. [Przekazywanie istniejących danych](walkthrough-2-upload-data.md)
3. **Tworzenie nowego eksperymentu**
4. [Nauczanie i ocena modeli](walkthrough-4-train-and-evaluate-models.md)
5. [Wdrażanie usługi sieci Web](walkthrough-5-publish-web-service.md)
6. [Dostęp do usługi sieci Web](walkthrough-6-access-web-service.md)

- - -
Następnym krokiem w ramach tego przewodnika jest tworzenie eksperymentu w usłudze Machine Learning Studio, która używa zestawu danych, które możemy przekazać.  

1. W Studio, kliknij przycisk **+ nowy** w dolnej części okna.
2. Wybierz **EKSPERYMENTU**, a następnie wybierz pozycję "Pusty eksperyment". 

    ![Tworzenie nowego eksperymentu][0]

2. Wybierz domyślną nazwę eksperymentu w górnej części obszaru roboczego i zmień jego nazwę, wpisując tekst opisowy.

    ![Zmień nazwę eksperymentu][5]
   
   > [!TIP]
   > Dobrym rozwiązaniem, aby wypełnić jest **Podsumowanie** i **opis** do eksperymentu w **właściwości** okienka. Te właściwości zapewniają możliwość dokumentu eksperyment, dzięki czemu każdy, kto analizuje go później będzie zrozumiałe, cele i metodologii.
   > 
   > ![Właściwości eksperymentu][6]
   > 
3. Na palecie modułów z lewej strony obszaru roboczego eksperymentu, rozwiń węzeł **zapisane zestawów danych**.
4. Znaleźć zestaw danych został utworzony w obszarze **Moje zestawów danych** i przeciągnij go do obszaru roboczego. Możesz również znaleźć zestaw danych, wprowadzając nazwę w **wyszukiwania** pole powyżej palety.  

    ![Dodaj zestaw danych do eksperymentu][7]

## <a name="prepare-the-data"></a>Przygotowanie danych
Można wyświetlić pierwszych 100 wierszy danych i niektóre informacje statystyczne dla całego zestawu danych: kliknij port wyjściowy zestaw danych (małe koło u dołu) i wybierz **Visualize**.  

Ponieważ plik danych nie został dostarczony z nagłówków kolumn, Studio udostępnił ogólnego nagłówki (Col1, Col2, *itp.*). Dobrym nagłówki nie są niezbędne do tworzenia modelu, ale to na łatwiejsze do pracy z danymi w eksperymencie. Ponadto możemy po pewnym czasie publikowania tego modelu w usłudze sieci web, nagłówki zidentyfikować kolumny do użytkownika usługi.  

Można dodać nagłówków kolumn za pomocą [edytowanie metadanych] [ edit-metadata] modułu.
Możesz użyć [edytowanie metadanych] [ edit-metadata] modułu, aby zmienić metadane skojarzone z zestawu danych. W takim przypadku stosujemy go udostępnia więcej przyjazne nazwy dla nagłówków kolumn. 

Aby użyć [edytowanie metadanych][edit-metadata], należy najpierw określić, które kolumny do zmodyfikowania (w tym przypadku wszystkie z nich.) Następnie określ akcję do wykonania na podstawie tych kolumn (w tym przypadku zmiana nagłówków kolumn.)

1. Na palecie modułów, wpisz "metadanych" **wyszukiwania** pole. [Edytowanie metadanych] [ edit-metadata] pojawia się na liście modułów.

2. Kliknij i przeciągnij [edytowanie metadanych] [ edit-metadata] modułu kanwę i upuść ją poniżej dodaliśmy wcześniej zestawu danych.

3. Zestaw danych, aby połączyć [edytowanie metadanych][edit-metadata]: kliknij port wyjściowy zestaw danych (małe koło w dolnej części zestawu danych), przeciągnij, aby port wejściowy z [edytowanie metadanych] [ edit-metadata] (małe koło w górnej części modułu) następnie zwolnij przycisk myszy. Zestaw danych i modułu pozostały połączone, nawet w przypadku przenoszenia jednej na kanwie.
   
   Eksperyment powinien teraz wyglądać mniej więcej tak:  
   
   ![Dodawanie metadanych edycji][1]
   
   Czerwony wykrzyknik wskazuje, że firma Microsoft nie zostało to jeszcze ustawiony właściwości dla tego modułu. Robimy tego dalej.
   
   > [!TIP]
   > Aby dodać komentarz do modułu, kliknij dwukrotnie moduł i wpisz tekst. Pozwoli to od razu sprawdzić rolę modułu w eksperymencie. W takim przypadku kliknij dwukrotnie [edytowanie metadanych] [ edit-metadata] moduł i wpisz komentarz "Dodaj nagłówki kolumn". Kliknij gdziekolwiek na kanwę, aby zamknąć okno tekstu. Aby wyświetlić komentarz, kliknij strzałkę w dół w module.
   > 
   > ![Edytuj moduł metadanych z komentarzem dodane][8]
   > 
4. Wybierz [edytowanie metadanych][edit-metadata], a następnie w **właściwości** kliknij w okienku po prawej stronie kanwy **Uruchom selektor kolumn**.

5. W **wybierz kolumny** okno dialogowe, wybierz wszystkie wiersze w **dostępne kolumny** i kliknij przycisk > Aby przenieść je na **wybrane kolumny**.
   Okno dialogowe powinien wyglądać następująco:

   ![Selektor kolumn z wybrano wszystkich kolumn][2]

6. Kliknij przycisk **OK** znacznik wyboru.

7. W **właściwości** okienka, poszukaj **nowych nazw kolumn** parametru. W tym miejscu wprowadź listę nazw 21 kolumn w zestawie danych, oddzielonych przecinkami i kolejność kolumn. Można uzyskać nazwy kolumn w dokumentacji zestawu danych w witrynie sieci Web UCI, lub dla wygody można skopiować i wkleić poniżej:  
   
       Status of checking account, Duration in months, Credit history, Purpose, Credit amount, Savings account/bond, Present employment since, Installment rate in percentage of disposable income, Personal status and sex, Other debtors, Present residence since, Property, Age in years, Other installment plans, Housing, Number of existing credits, Job, Number of people providing maintenance for, Telephone, Foreign worker, Credit risk  
   
   W okienku właściwości wygląda następująco:
   
   ![Właściwości metadanych edycji][3]

> [!TIP]
> Jeśli chcesz sprawdzić nagłówki kolumn, należy uruchomić eksperyment (kliknij **Uruchom** poniżej obszaru roboczego eksperymentu). Po zakończeniu pracy (zielony znacznik wyboru jest wyświetlane na [edytowanie metadanych][edit-metadata]), kliknij port wyjściowy [edytowanie metadanych] [ edit-metadata] modułu, i wybierz **Visualize**. Dane wyjściowe każdy moduł jest widoczny w taki sam sposób, aby wyświetlić postęp danych za pośrednictwem eksperymentu.
> 
> 

## <a name="create-training-and-test-datasets"></a>Tworzenie szkolenia i testowanie zbiory danych
Potrzebujemy niektórych danych do nauczenia modelu, a niektóre testowania go.
Dlatego w następnym kroku eksperyment, możemy podzielić zestawu danych na dwa oddzielne zestawy danych: jeden dla uczenie modelu, a drugi do testowania go.

Aby to zrobić, używamy [podziału danych] [ split] modułu.  

1. Znajdź [podziału danych] [ split] modułu, przeciągnij go do obszaru roboczego i połącz go z [edytowanie metadanych] [ edit-metadata] modułu.

2. Domyślnie współczynnik rozdzielania wynosi 0,5 i **podziału Randomized** ustawiono parametr. Oznacza, że losowe pół danych wyjściowych za pośrednictwem jednego portu [podziału danych] [ split] modułu i połowy przez innych. Te parametry można dostosować, jak również **Random seed** parametr, aby zmienić podziału między celów szkoleniowych i testów danych. Na przykład możemy pozostaw je jako — jest.
   
   > [!TIP]
   > Właściwość **ułamek wierszy w pierwszym wyjściowy zestaw danych** Określa, ile danych jest wysyłany za pośrednictwem *po lewej stronie* output portu. Na przykład jeśli ustawisz stosunek 0,7 70% danych wówczas dane wyjściowe za pośrednictwem lewy port i 30% za pośrednictwem prawy port.  
   > 
   > 

3. Kliknij dwukrotnie [podziału danych] [ split] moduł i wpisz komentarz, "danych szkoleniowych/testowania podzielić 50%". 

Możemy użyć danych wyjściowych z [podziału danych] [ split] modułu jednak firma Microsoft, takich jak, ale ta funkcja pozwala wybrać wykorzystywać dane po lewej stronie, ponieważ dane szkoleniowe i prawa testowania jako dane wyjściowe.  

Jak wspomniano w [poprzedniego kroku](walkthrough-2-upload-data.md), misclassifying ryzyko kredytowe wysokiej jako niski koszt jest pięć razy wyższa niż koszt misclassifying niskie ryzyko kredytowe jako wysoki. Aby to uwzględniać, możemy wygenerować nowy zestaw danych, które odzwierciedla tej funkcji koszt. W nowy zestaw danych każdy przykład wysokiego ryzyka są replikowane pięć razy podczas każdego przykład niskiego ryzyka nie jest replikowany.   

Możemy replikacja za pomocą kodu języka R:  

1. Znajdź i przeciągnij [wykonanie skryptu języka R] [ execute-r-script] modułu na kanwie eksperymentu. 

2. Połącz lewy port wyjściowy [podziału danych] [ split] modułu do pierwszego portu wejściowego ("Dataset1") z [wykonanie skryptu języka R] [ execute-r-script] modułu.

3. Kliknij dwukrotnie [wykonanie skryptu języka R] [ execute-r-script] modułu, a następnie wprowadź komentarz, "Ustaw korekty kosztu".

4. W **właściwości** okienku usunąć domyślny tekst w **skrypt języka R** parametr, a następnie wprowadź ten skrypt:
   
       dataset1 <- maml.mapInputPort(1)
       data.set<-dataset1[dataset1[,21]==1,]
       pos<-dataset1[dataset1[,21]==2,]
       for (i in 1:5) data.set<-rbind(data.set,pos)
       maml.mapOutputPort("data.set")

    ![Skrypt języka R w module wykonanie skryptu języka R][9]

Konieczne tej samej operacji replikacji dla każdego produktu [podziału danych] [ split] modułu, aby dane szkolenia i testowania mieć takiego samego dostosowania kosztów. Najprostszym sposobem, w tym celu jest duplikując [wykonanie skryptu języka R] [ execute-r-script] modułu właśnie wprowadziliśmy i połączenia jej z drugiej output port [podziału danych] [ split] modułu.

1. Kliknij prawym przyciskiem myszy [wykonanie skryptu języka R] [ execute-r-script] moduł i zaznacz **kopiowania**.

2. Kliknij prawym przyciskiem myszy obszaru roboczego eksperymentu, a następnie wybierz **Wklej**.

3. Przeciągnij nowy moduł w określonej pozycji, a następnie połącz portem wyjściowym prawo [podziału danych] [ split] na pierwszy port wejściowy tego nowego modułu [wykonanie skryptu języka R] [ execute-r-script] modułu. 

4. W dolnej części obszaru roboczego, kliknij przycisk **Uruchom**. 

> [!TIP]
> Kopii modułu wykonania skryptu języka R zawiera sam skrypt jako oryginalnego modułu. Podczas kopiowania i wklejania modułu na kanwie kopię zachowuje wszystkie właściwości oryginału.  
> 
> 

Naszym doświadczeniu teraz wygląda następująco:

![Dodanie modułu podziału i skrypty języka R][4]

Aby uzyskać więcej informacji na używanie skryptów języka R w eksperymentów, zobacz [rozszerzanie eksperymentu z R](extend-your-experiment-with-r.md).

**Następnie: [pociągu i Ewaluacja modeli](walkthrough-4-train-and-evaluate-models.md)**

[0]: ./media/walkthrough-3-create-new-experiment/create-new-experiment.png
[5]: ./media/walkthrough-3-create-new-experiment/rename-experiment.png
[6]: ./media/walkthrough-3-create-new-experiment/experiment-properties.png
[7]: ./media/walkthrough-3-create-new-experiment/add-dataset-to-experiment.png
[8]: ./media/walkthrough-3-create-new-experiment/edit-metadata-with-comment.png
[9]: ./media/walkthrough-3-create-new-experiment/execute-r-script.png
[1]: ./media/walkthrough-3-create-new-experiment/experiment-with-edit-metadata-module.png
[2]: ./media/walkthrough-3-create-new-experiment/select-columns.png
[3]: ./media/walkthrough-3-create-new-experiment/edit-metadata-properties.png
[4]: ./media/walkthrough-3-create-new-experiment/experiment.png


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[edit-metadata]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
