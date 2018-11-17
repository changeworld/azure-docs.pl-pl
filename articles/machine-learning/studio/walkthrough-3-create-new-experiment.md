---
title: 'Krok 3: Tworzenie nowego eksperymentu usługi Machine Learning | Dokumentacja firmy Microsoft'
description: 'Krok 3 programowanie przewodnik rozwiązania do analizy predykcyjnej: Tworzenie nowego eksperymentu szkolenia w usłudze Azure Machine Learning Studio.'
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.custom: (previous ms.author hshapiro)
ms.author: amlstudiodocs
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
ms.openlocfilehash: 2fdeab83d1e668fbbb68155c1695ffb40d71c15b
ms.sourcegitcommit: 8899e76afb51f0d507c4f786f28eb46ada060b8d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/16/2018
ms.locfileid: "51824442"
---
# <a name="walkthrough-step-3-create-a-new-azure-machine-learning-experiment"></a>Przewodnik, krok 3. Tworzenie nowego eksperymentu usługi Azure Machine Learning
Jest to trzeci krok tego przewodnika, [tworzenia rozwiązania analizy predykcyjnej w usłudze Azure Machine Learning](walkthrough-develop-predictive-solution.md)

1. [Tworzenie obszaru roboczego usługi Machine Learning](walkthrough-1-create-ml-workspace.md)
2. [Przekazywanie istniejących danych](walkthrough-2-upload-data.md)
3. **Tworzenie nowego eksperymentu**
4. [Nauczanie i ocena modeli](walkthrough-4-train-and-evaluate-models.md)
5. [Wdrażanie usługi sieci Web](walkthrough-5-publish-web-service.md)
6. [Dostęp do usługi sieci Web](walkthrough-6-access-web-service.md)

- - -
Następnym krokiem w tym instruktażu jest tworzenie eksperymentu w usłudze Machine Learning Studio, który używa zestawu danych, które możemy przekazać.  

1. W programie Studio, kliknij **+ nowy** w dolnej części okna.
2. Wybierz **EKSPERYMENTU**, a następnie wybierz pozycję "Pusty eksperyment". 

    ![Tworzenie nowego eksperymentu][0]

2. Wybierz domyślną nazwę eksperymentu w górnej części kanwy, a następnie zmień jego nazwę na opisową nazwę.

    ![Zmienianie nazwy eksperymentu][5]
   
   > [!TIP]
   > Jest dobrą praktyką, aby wypełnić **Podsumowanie** i **opis** eksperymentu w **właściwości** okienka. Te właściwości zapewniają możliwość dokumentu eksperymentu, tak aby każdy, kto przegląda go później będzie zrozumiałe, cele i metodologii.
   > 
   > ![Właściwości eksperymentu][6]
   > 
3. W palety modułów, z lewej strony obszaru roboczego eksperymentu, rozwiń **zapisane zestawów danych**.
4. Znajdowanie zestawu danych utworzonego w ramach **Moje zestawy danych** i przeciągnij go na kanwie. Możesz również znaleźć zestaw danych, wprowadzając nazwę w **wyszukiwania** polu nad palety.  

    ![Dodaj zestaw danych do eksperymentu][7]

## <a name="prepare-the-data"></a>Przygotowywanie danych
Możesz wyświetlić pierwszych 100 wierszy danych i niektóre informacje statystyczne dla całego zestawu danych: kliknij port wyjściowy zestawu danych (mały okrąg w dolnej części), a następnie wybierz pozycję **Visualize**.  

Ponieważ plik danych nie został dostarczony z nagłówkami kolumn, Studio udostępnił nagłówki ogólnego (Col1, Col2, *itp.*). Dobre nagłówki nie są niezbędne do tworzenia modelu, ale mogą ułatwić pracę z danymi w eksperymencie. Ponadto po opublikowaniu tego modelu po pewnym czasie w usłudze sieci web, nagłówki pomagać w identyfikacji kolumn tak, aby użytkownik usługi.  

Możemy dodać nagłówków kolumn za pomocą [edytować metadane] [ edit-metadata] modułu.
Możesz użyć [edytować metadane] [ edit-metadata] modułu, aby zmienić metadane skojarzone z zestawu danych. W tym przypadku używamy go udostępnia więcej przyjazne nazwy dla nagłówków kolumn. 

Aby użyć [edytować metadane][edit-metadata], należy najpierw określić kolumny, które można modyfikować (w tym przypadku wszystkie z nich.) Następnie określ akcję do wykonania na podstawie tych kolumn (w tym przypadku zmiana nagłówków kolumn.)

1. W palety modułów, wpisz "metadane" **wyszukiwania** pole. [Edytować metadane] [ edit-metadata] pojawia się na liście modułów.

2. Kliknij i przeciągnij [edytować metadane] [ edit-metadata] modułu na kanwę i upuść je poniżej zestawu danych, dodaliśmy wcześniej.

3. Łączenie zestawu danych na [edytować metadane][edit-metadata]: kliknij port wyjściowy zestawu danych (mały okrąg w dolnej części zestawu danych), przeciągnij portem wejściowym [edytować metadane] [ edit-metadata] (mały okrąg w górnej części modułu) zwolnij przycisk myszy. Zestaw danych i modułu pozostały połączone, nawet wtedy, gdy albo zmieniają położenie w obszarze roboczym.
   
   Eksperyment powinien teraz wyglądać mniej więcej tak:  
   
   ![Dodawanie, edytowanie metadanych][1]
   
   Czerwony wykrzyknik wskazuje, że firma Microsoft nie został ustawiony właściwości dla tego modułu. Możemy to zrobić to w następnym kroku.
   
   > [!TIP]
   > Aby dodać komentarz do modułu, kliknij dwukrotnie moduł i wpisz tekst. Pozwoli to od razu sprawdzić rolę modułu w eksperymencie. W tym przypadku kliknij dwukrotnie [edytować metadane] [ edit-metadata] moduł i wpisz komentarz "Dodaj nagłówki kolumn". Kliknij, gdzie indziej na kanwę, aby zamknąć pole tekstowe. Aby wyświetlić komentarz, kliknij strzałkę w dół w module.
   > 
   > ![Edytuj metadane modułu za pomocą dodano komentarz][8]
   > 
4. Wybierz [edytować metadane][edit-metadata], a następnie w **właściwości** kliknij w okienku po prawej stronie kanwy, **uruchamianie selektora kolumn**.

5. W **wybierz kolumny** okno dialogowe, wybierz wszystkie wiersze w **dostępnych kolumn** i kliknij pozycję > Aby przenieść je na **wybrane kolumny**.
   Okno dialogowe powinno wyglądać następująco:

   ![Selektor kolumn ze wszystkimi kolumnami wybrane][2]

6. Kliknij przycisk **OK** znacznik wyboru.

7. Ponownie **właściwości** okienku i wyszukaj **nowych nazw kolumn** parametru. W tym polu wprowadź listę nazw 21 kolumn w zestawie danych, rozdzielonych przecinkami i kolejność kolumn. Nazwy kolumn można uzyskać w dokumentacji zestawu danych w witrynie sieci Web UCI lub dla wygody można skopiować i wkleić następującej listy:  
   
       Status of checking account, Duration in months, Credit history, Purpose, Credit amount, Savings account/bond, Present employment since, Installment rate in percentage of disposable income, Personal status and sex, Other debtors, Present residence since, Property, Age in years, Other installment plans, Housing, Number of existing credits, Job, Number of people providing maintenance for, Telephone, Foreign worker, Credit risk  
   
   Zawartość okienka właściwości wygląda następująco:
   
   ![Właściwości metadanych edycji][3]

> [!TIP]
> Jeśli chcesz sprawdzić nagłówki kolumn, uruchamianie eksperymentu (kliknij **Uruchom** poniżej obszaru roboczego eksperymentu). Po zakończeniu pracy (zielony znacznik wyboru pojawia się na [edytować metadane][edit-metadata]), kliknij port wyjściowy [edytować metadane] [ edit-metadata] modułu i wybierz **Visualize**. Możesz wyświetlić dane wyjściowe każdego modułu w taki sam sposób, aby wyświetlić postęp danych przy użyciu eksperymentu.
> 
> 

## <a name="create-training-and-test-datasets"></a>Tworzenie, szkolenie i testowanie zestawów danych
Potrzebujemy niektórych danych do nauczenia modelu, a niektóre do testowania.
Aby w następnym kroku eksperymentu, możemy podzielić zestawu danych dwa oddzielne zestawy danych: jeden dla szkolenia nasz model, a drugi dla testowania.

Aby to zrobić, użyjemy [podziału danych] [ split] modułu.  

1. Znajdź [podziału danych] [ split] modułu, przeciągnij go na kanwie, a następnie połącz go [edytować metadane] [ edit-metadata] modułu.

2. Domyślnie, współczynnik rozdzielania wynosi 0,5 i **podziału Randomized** zestaw parametrów. Oznacza, że w połowie losowe dane dane wyjściowe za pośrednictwem jednego portu [podziału danych] [ split] modułu, a połowa za pośrednictwem innych. Można dostosować te parametry, jak również **Random seed** parametru, aby zmienić podziału między szkolenie i testowanie danych. W tym przykładzie pozostawimy jako-to.
   
   > [!TIP]
   > Właściwość **ułamek wierszy w pierwszym zestawie danych wyjściowych** Określa, ile danych jest dane wyjściowe za pośrednictwem *po lewej stronie* portu wyjściowego. Na przykład jeśli ustawisz stosunek 0,7 następnie 70% danych jest dane wyjściowe za pośrednictwem portu po lewej stronie i 30% przez prawy port.  
   > 
   > 

3. Kliknij dwukrotnie [podziału danych] [ split] modułu i wprowadź komentarz, "szkolenia i testowania danych podziału 50%". 

Możemy użyć danych wyjściowych z [podziału danych] [ split] modułu jednak firma Microsoft, takich jak, ale Wybierzmy o używaniu lewej wyjście danych szkoleniowych i po prawej stronie testowania jako dane wyjściowe.  

Jak wspomniano w [poprzedniego kroku](walkthrough-2-upload-data.md), misclassifying ryzyko kredytowe wysokiej jako niski koszt wynosi pięć razy wyższy niż koszt misclassifying niskie ryzyko kredytowe jako wysoki. Aby to uwzględniać, firma Microsoft wygenerować nowy zestaw danych, który odzwierciedla tę funkcję, kosztów. W nowym zestawie danych każdy przykład wysokiego ryzyka są replikowane pięć razy podczas każdego przykład niskiego ryzyka nie jest replikowany.   

Robimy to replikacji przy użyciu kodu języka R:  

1. Znajdowanie i przeciąganie [wykonanie skryptu języka R] [ execute-r-script] modułu do obszaru roboczego eksperymentu. 

2. Połącz lewy port wyjściowy [podziału danych] [ split] pierwszy portem wejściowym ("Dataset1") przez moduł [wykonanie skryptu języka R] [ execute-r-script] modułu.

3. Kliknij dwukrotnie [wykonanie skryptu języka R] [ execute-r-script] modułu i wprowadź komentarz, "Zestaw Korekta kosztu".

4. W **właściwości** okienku Usuń domyślny tekst w **skrypt języka R** parametru i wprowadź ten skrypt:
   
       dataset1 <- maml.mapInputPort(1)
       data.set<-dataset1[dataset1[,21]==1,]
       pos<-dataset1[dataset1[,21]==2,]
       for (i in 1:5) data.set<-rbind(data.set,pos)
       maml.mapOutputPort("data.set")

    ![Skrypt języka R w module wykonywania skryptu języka R][9]

Musimy tej samej operacji replikacji dla każdego danych wyjściowych [podziału danych] [ split] modułu, aby dane szkolenia i testowania mają ten sam korekty kosztów. W tym celu najłatwiej duplikując [wykonanie skryptu języka R] [ execute-r-script] modułu, firma Microsoft wykonane i łączy ją do innego danych wyjściowych port [podziału danych] [ split] modułu.

1. Kliknij prawym przyciskiem myszy [wykonanie skryptu języka R] [ execute-r-script] modułu, a następnie wybierz pozycję **kopiowania**.

2. Kliknij prawym przyciskiem myszy obszaru roboczego eksperymentu, a następnie wybierz pozycję **Wklej**.

3. Nowy moduł przeciągnij w odpowiednie miejsce, a następnie połącz port wyjściowy prawo [podziału danych] [ split] pierwszy port wejściowy to nowy moduł [wykonanie skryptu języka R] [ execute-r-script] modułu. 

4. W dolnej części obszaru roboczego kliknij **Uruchom**. 

> [!TIP]
> Kopię modułu wykonywania skryptu języka R zawiera ten sam skrypt jako oryginalnego modułu. Podczas kopiowania i wklejania modułu na kanwie kopii zachowuje wszystkie właściwości oryginału.  
> 
> 

Nasz eksperyment teraz wygląda następująco:

![Dodawanie modułu dzielenia i skrypty języka R][4]

Aby uzyskać więcej informacji na temat używania skryptów języka R w eksperymenty zobacz [rozszerzanie eksperymentu przy użyciu języka R](extend-your-experiment-with-r.md).

**Następnie: [szkolenie i Ewaluacja modeli](walkthrough-4-train-and-evaluate-models.md)**

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
