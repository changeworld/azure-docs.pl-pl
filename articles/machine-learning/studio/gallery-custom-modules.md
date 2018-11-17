---
title: Moduły niestandardowe Galeria sztucznej Inteligencji platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, modułów usługi niestandardowe machine learning w galerii sztucznej Inteligencji platformy Azure.
services: machine-learning
documentationcenter: ''
author: YasinMSFT
ms.custom: (previous ms.author yahajiza)
ms.author: amlstudiodocs
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
ms.openlocfilehash: 6f92f3d81889bf79db21b5f24cb98963e4eafdb4
ms.sourcegitcommit: 8899e76afb51f0d507c4f786f28eb46ada060b8d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/16/2018
ms.locfileid: "51821178"
---
# <a name="discover-custom-machine-learning-modules-in-azure-ai-gallery"></a>Odnajdywanie modułów usługi niestandardowe machine learning w galerii Azure AI

[Galeria sztucznej Inteligencji platformy Azure](http://gallery.cortanaintelligence.com) oferuje kilka [moduły niestandardowe](https://gallery.cortanaintelligence.com/customModules) , rozszerzyć możliwości usługi Azure Machine Learning Studio. Można również importować moduły, do użycia w eksperymentów, dzięki czemu możesz tworzyć bardziej zaawansowane rozwiązania do analizy predykcyjnej.

Obecnie galerii oferuje modułów *czas analizy w postaci szeregów*, *reguł kojarzenia*, *klastrowania algorytmy* (poza k średnich) i  *wizualizacje*oraz inne moduły narzędzie najważniejszą metodą roboczą.


## <a name="discover"></a>Wykrywanie
Aby przeglądać moduły niestandardowe [w galerii](http://gallery.cortanaintelligence.com)w obszarze **więcej**, wybierz opcję **moduły niestandardowe**.

![Wybierz niestandardowe moduły na stronę główną galerii](./media/gallery-custom-modules/select-custom-modules-in-gallery.png)

**[Moduły niestandardowe](https://gallery.cortanaintelligence.com/customModules)** stronie wyświetlana jest lista ostatnio dodanych i popularnych modułów. Zaznacz, aby wyświetlić wszystkie moduły niestandardowe **holograficznych** przycisku. Aby wyszukać określonego niestandardowego modułu, wybierz **holograficznych**i kryteria filtrowania a następnie wybierz pozycję. Można również wprowadzić terminy wyszukiwania w **wyszukiwania** polu w górnej części strony galerii.

![Wybierz pozycję "Zobacz wszystkie" Aby przeglądać wszystkie moduły niestandardowe](./media/gallery-custom-modules/click-see-all-for-all-custom-modules.png)

### <a name="understand"></a>Omówienie

Aby zrozumieć, jak działa opublikowanych niestandardowego modułu, wybierz niestandardowego modułu, aby otworzyć stronę szczegółów modułu. Na stronie szczegółów zapewnia środowisko nauki spójne i zawierającego wiele użytecznych informacji. Na przykład na stronie szczegółów wyróżnia celem modułu, a następnie wyświetla listę oczekiwanych danych wejściowych, dane wyjściowe i parametry. Na stronie szczegółów zawiera również link do podstawowego kodu źródłowego, który można sprawdzić i dostosować.

### <a name="comment-and-share"></a>Komentarz i udział
W module niestandardowy strona szczegółów, **komentarze** sekcji można komentarz, przekazać opinię lub zadać pytania dotyczące modułu. Moduł można nawet udostępniać znajomym lub współpracownicy w serwisie Twitter i LinkedIn. Możesz również może wysłać wiadomość e-mail łącze do strony szczegółów modułu, aby zaprosić innych użytkowników, aby wyświetlić stronę.

![Udostępnij ten element ze znajomymi](./media/gallery-how-to-use-contribute-publish/share-links.png)

![Dodać swoje własne komentarze](./media/gallery-how-to-use-contribute-publish/comments.png)

## <a name="import"></a>Import
Każdy moduł niestandardowych za pomocą galerii można zaimportować do własnych eksperymentów.

Galeria sztucznej Inteligencji platformy Azure oferuje dwa sposoby importowania kopii modułu:

* **Za pomocą galerii**. Po zaimportowaniu niestandardowego modułu z galerii, możesz także uzyskać przykładowego eksperymentu, zapewniająca przykładem sposobu korzystania z modułu.
* **Z poziomu usługi Machine Learning Studio**. Podczas pracy w usłudze Machine Learning Studio, można zaimportować każdy niestandardowy moduł (w tym przypadku nie uzyskujesz przykładowego eksperymentu).

### <a name="from-the-gallery"></a>Za pomocą galerii

1. W galerii Otwórz stronę szczegółów modułu. 
2. Wybierz **Otwórz w programie Studio**.
   
    ![Otwórz niestandardowego modułu z galerii](./media/gallery-custom-modules/open-custom-module-from-gallery.png)
   
Każdy niestandardowy moduł zawiera przykładowego eksperymentu, który demonstruje sposób korzystania z modułu. Po wybraniu **Otwórz w programie Studio**, przykładowego eksperymentu, który zostanie otwarty w obszarze roboczym usługi Machine Learning Studio. (Jeśli jeszcze nie zostało to zrobione Studio, monit o pierwszym zalogowaniu za pomocą konta Microsoft.)

Oprócz przykładowego eksperymentu niestandardowego modułu jest kopiowana do swojego obszaru roboczego. Jest również umieszczona w palecie modułów przy użyciu wszystkich wbudowanych lub niestandardowych usługi Machine Learning Studio moduły. Można teraz używać do własnych doświadczeń, podobnie jak każdy inny moduł, w obszarze roboczym.

### <a name="from-within-machine-learning-studio"></a>Z poziomu usługi Machine Learning Studio

1. W usłudze Machine Learning Studio, wybierz **NEW**.
2. Wybierz **modułu**. Wybierz z listy moduły z galerii lub znaleźć określonego modułu przy użyciu **wyszukiwania** pole.
3. Wskaż myszą modułu, a następnie wybierz **importu modułu**. (Aby uzyskać informacje o module, wybierz **widoku w galerii**. Spowoduje to przejście do strony szczegółów modułu w galerii.)
   
    ![Importowanie niestandardowego modułu w usłudze Machine Learning Studio](./media/gallery-custom-modules/add-custom-module-in-studio.png)

Niestandardowego modułu jest kopiowany do swojego obszaru roboczego i umieszczane w Twojej palety modułów, z wbudowanych lub niestandardowych modułów usługi Machine Learning Studio. Można teraz używać do własnych doświadczeń, podobnie jak każdy inny moduł, w obszarze roboczym.

## <a name="use"></a>Użycie

Niezależnie od tego, która metoda decydujesz się zaimportować niestandardowego modułu podczas importowania modułu, moduł zostanie umieszczony w palecie modułu w usłudze Machine Learning Studio. Z Twojego palety modułów można użyć niestandardowego modułu w eksperymencie żadnych w obszarze roboczym, podobnie jak każdy inny moduł.

Aby użyć modułu importowanego:

1. Tworzenie eksperymentu, lub otworzyć istniejącego eksperymentu.
2. Aby rozwinąć listę moduły niestandardowe w obszarze roboczym, w palecie modułów wybierz **niestandardowe**. Palety modułów jest z lewej strony obszaru roboczego eksperymentu.
   
    ![Lista modułów niestandardowych w palecie Studio](./media/gallery-custom-modules/custom-module-in-studio-palette.png)
3. Wybierz moduł, który można zaimportować, a następnie przeciągnij go do swojego eksperymentu.


**[Przejdź do galerii](http://gallery.cortanaintelligence.com)**

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

