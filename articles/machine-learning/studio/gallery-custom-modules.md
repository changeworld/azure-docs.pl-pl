---
title: Azure galerii AI niestandardowe moduły | Dokumentacja firmy Microsoft
description: Odkryj maszyny niestandardowych modułów uczenia w galerii Azure AI.
services: machine-learning
documentationcenter: ''
author: YasinMSFT
ms.author: yahajiza
manager: hjerez
editor: cgronlun
ms.assetid: 16037a84-dad0-4a8c-9874-a1d3bd551cf0
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/14/2017
ms.openlocfilehash: c53bab2e838425dfdd124e64c3d7d3114fa30429
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "34834434"
---
# <a name="discover-custom-machine-learning-modules-in-azure-ai-gallery"></a>Odkryj maszyny niestandardowych modułów uczenia w galerii Azure AI
[!INCLUDE [machine-learning-gallery-item-selector](../../../includes/machine-learning-gallery-item-selector.md)]

## <a name="custom-modules-for-machine-learning-studio"></a>Niestandardowych modułów usługi Machine Learning Studio
Galeria AI Azure oferuje kilka [niestandardowe moduły](https://gallery.cortanaintelligence.com/customModules) który rozszerzyć możliwości usługi Azure Machine Learning Studio. Możesz zaimportować moduły, aby używać w eksperymentów, dlatego można tworzyć bardziej zaawansowane rozwiązania analizy predykcyjnej.

Obecnie galerii oferuje modułów na *czasu analytics serii*, *reguł kojarzenia*, *klastrowanie algorytmów* (poza k średnich) i  *wizualizacje*oraz inne moduły narzędzie najważniejszą metodą roboczą.


## <a name="discover"></a>Wykrywanie
Aby przeglądać niestandardowe moduły [w galerii](http://gallery.cortanaintelligence.com)w obszarze **więcej**, wybierz pozycję **niestandardowe moduły**.

![Wybierz niestandardowe moduły na stronie głównej galerii](./media/gallery-custom-modules/select-custom-modules-in-gallery.png)

**[Niestandardowe moduły](https://gallery.cortanaintelligence.com/customModules)** zostanie wyświetlona lista ostatnio dodany i popularnych modułów. Zaznacz, aby wyświetlić wszystkie niestandardowe moduły **zobaczyć wszystkie** przycisku. Aby wyszukać określony moduł niestandardowe, wybierz **zobaczyć wszystkie**, a następnie wybierz kryteria. Można również wprowadzić terminy wyszukiwania w **wyszukiwania** u góry strony galerii.

![Wybierz opcję "Zobacz wszystkie" Aby przeglądać wszystkie niestandardowe moduły](./media/gallery-custom-modules/click-see-all-for-all-custom-modules.png)

### <a name="understand"></a>Zrozumienie

Aby zrozumieć, jak działa opublikowanych niestandardowego modułu, wybierz niestandardowego modułu, aby otworzyć stronę Szczegóły modułu. Na stronie Szczegóły zapewnia spójne i informacyjny szkoleniowej. Na przykład na stronie Szczegóły wyróżnia cel modułu i wyświetlane są oczekiwanego wejść, wyjść i parametry. Na stronie Szczegóły zawiera również link do podstawowej kod źródłowy, który można sprawdzić i dostosować.

### <a name="comment-and-share"></a>Komentarz i udziału
W module niestandardowe strony szczegółów, **komentarze** sekcji, można dodać komentarz, wyrazić swoją opinię lub zadać pytania dotyczące modułu. Możesz nawet udostępniać modułu znajomych lub współpracowników w serwisie Twitter i LinkedIn. Można również e-mail łącze do strony szczegółów modułu, z zaproszeniem dla innych użytkowników, aby wyświetlić stronę.

![Udostępnij ten element znajomych](./media/gallery-how-to-use-contribute-publish/share-links.png)

![Dodaj komentarz własnych](./media/gallery-how-to-use-contribute-publish/comments.png)

## <a name="import"></a>Import
Każdy moduł niestandardowych z galerii można importować do własnych eksperymentów.

Galeria AI Azure oferuje dwa sposoby importowania kopii modułu:

* **Z poziomu galerii**. Po zaimportowaniu niestandardowego modułu z poziomu galerii, możesz również uzyskać eksperymentu przykładowego, który zapewnia przykładem użyć modułu programu.
* **Za pomocą uczenia maszynowego Studio**. Podczas pracy w usłudze Machine Learning Studio, można zaimportować każdy moduł niestandardowych (w tym przypadku nie otrzymasz eksperymentu przykładowego).

### <a name="from-the-gallery"></a>Z galerii

1. W galerii Otwórz stronę Szczegóły modułu. 
2. Wybierz **Otwórz w Studio**.
   
    ![Otwórz niestandardowego modułu z galerii](./media/gallery-custom-modules/open-custom-module-from-gallery.png)
   
Każdy niestandardowy moduł zawiera eksperymentu przykładowego, który demonstruje sposób korzystania z modułu. Po wybraniu **Otwórz w Studio**, eksperymentu przykładowego otwiera się w obszarze roboczym Machine Learning Studio. (Jeśli jeszcze nie zalogowano Cię Studio, zostanie wyświetlony monit pierwsze logowanie przy użyciu konta Microsoft.)

Oprócz eksperymentu przykładowego niestandardowego modułu jest kopiowany do swojego obszaru roboczego. Znajduje się również w palecie modułu z wszystkich wbudowanych lub niestandardowych Machine Learning Studio moduły. Można teraz używać do własnych doświadczeń, takich jak inny moduł, w obszarze roboczym.

### <a name="from-within-machine-learning-studio"></a>Za pomocą uczenia maszynowego Studio

1. W usłudze Machine Learning Studio, wybierz **nowy**.
2. Wybierz **modułu**. Wybierz z listy modułów galerii lub znaleźć określony moduł za pomocą **wyszukiwania** pole.
3. Punkt myszy w module, a następnie wybierz **Import Module**. (Aby uzyskać informacje o module, wybierz **Wyświetl w galerii**. Powoduje to przejście do strony szczegółów modułu w galerii.)
   
    ![Zaimportuj niestandardowy moduł do usługi Machine Learning Studio](./media/gallery-custom-modules/add-custom-module-in-studio.png)

Niestandardowego modułu jest kopiowany do swojego obszaru roboczego i umieszczana w palecie modułu z własnych wbudowanych lub niestandardowych modułów usługi Machine Learning Studio. Można teraz używać do własnych doświadczeń, takich jak inny moduł, w obszarze roboczym.

## <a name="use"></a>Użycie

Niezależnie od tego, które metody chcesz zaimportować niestandardowego modułu, gdy należy zaimportować moduł, moduł znajduje się w palecie modułu w usłudze Machine Learning Studio. Z paletę moduł możesz użyć niestandardowego modułu w dowolnym eksperymentu w obszarze roboczym, podobnie jak inne modułu.

Aby użyć modułu zaimportowane:

1. Tworzenie eksperymentu lub Otwórz istniejący eksperymentu.
2. Aby rozwinąć listę niestandardowych modułów w obszarze roboczym, na palecie modułów, wybierz **niestandardowych**. Palety modułów jest z lewej strony obszaru roboczego eksperymentu.
   
    ![Lista niestandardowego modułu w Studio palety](./media/gallery-custom-modules/custom-module-in-studio-palette.png)
3. Wybierz moduł, który można zaimportować, a następnie przeciągnij go do eksperymentu.


**[Przejdź do galerii](http://gallery.cortanaintelligence.com)**

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

