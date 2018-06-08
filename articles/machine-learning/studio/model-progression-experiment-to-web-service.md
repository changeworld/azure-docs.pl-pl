---
title: Jak modelu uczenia maszynowego Azure staje się usługi sieci web | Dokumentacja firmy Microsoft
description: Przegląd sposobu działania sposób realizowany modelu z usługi Azure Machine Learning z rozwinięcie eksperymentu operationalized usługi sieci Web.
services: machine-learning
documentationcenter: ''
author: YasinMSFT
ms.author: yahajiza
manager: hjerez
editor: cgronlun
ms.assetid: 25e0c025-f8b0-44ab-beaf-d0f2d485eb91
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.openlocfilehash: 7b9aec2815f836b3b220de37fe6428c54d39c3e5
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "34835576"
---
# <a name="how-a-machine-learning-model-progresses-from-an-experiment-to-an-operationalized-web-service"></a>Jak modelu uczenia maszynowego kontynuowana w eksperymencie usługi sieci Web operationalized
Azure Machine Learning Studio udostępnia interaktywny kanwy, która pozwala na tworzenie, uruchom test i iteracji ***eksperymentu*** modelu analizy predykcyjnej. Istnieje szereg dostępne moduły, które można:

* Dane wejściowe do eksperymentu
* Manipulowanie danymi
* Uczenie modelu przy użyciu przy użyciu algorytmów uczenia maszynowego
* Ocena modelu
* Ocena wyników
* Wartości ostateczne dane wyjściowe

Po zakończeniu eksperymentu można wdrożyć go jako ***usługi sieci Web klasycznego Azure Machine Learning*** lub ***usługi sieci Web nowego Azure Machine Learning*** tak, aby użytkownicy mogli przesyła nowych danych i odbierania Wstecz wyniki.

W tym artykule zapewniamy im omówienie sposobu działania jak eksperymentu Twojej realizowany modelu uczenia maszynowego od projektowania do operationalized usługi sieci Web.

> [!NOTE]
> Istnieją inne sposoby tworzenia i wdrażania modeli uczenia maszyny, ale ten artykuł koncentruje się na sposobie korzystania z usługi Machine Learning Studio. Na przykład, aby uzyskać opis sposobu tworzenia klasycznego predykcyjnej usługi sieci Web z R, zobacz wpis w blogu [kompilacji i wdrażania predykcyjnej sieci Web aplikacji przy użyciu programu RStudio i uczenie Maszynowe Azure](http://blogs.technet.com/b/machinelearning/archive/2015/09/25/build-and-deploy-a-predictive-web-app-using-rstudio-and-azure-ml.aspx).
> 
> 

Gdy Azure Machine Learning Studio zaprojektowano w celu ułatwienia tworzenia i wdrażania *modelu analizy predykcyjnej*, można użyć Studio umożliwiające tworzenie eksperymentu, która nie zawiera modelu analizy predykcyjnej. Na przykład eksperymentu może tylko dane wejściowe, zmian, a następnie zapisuje wyniki. Podobnie jak eksperymentu analizy predykcyjnej można wdrożyć tego eksperymentu predykcyjny jako usługę sieci Web, ale jest prostsze procesu, ponieważ eksperyment nie jest szkolenia lub oceniania modelu uczenia maszynowego. Mimo że nie jest typowe do użycia w ten sposób Studio, firma Microsoft będzie dołączyć dyskusji tak, aby firma Microsoft zapewnia pełne wyjaśnienie działania Studio.

## <a name="developing-and-deploying-a-predictive-web-service"></a>Tworzenie i wdrażanie predykcyjnej usługi sieci Web
Oto etapy, które są typowe rozwiązania postępuje zgodnie z tworzenia i wdrażania go za pomocą usługi Machine Learning Studio:

![Przepływ wdrożenia](./media/model-progression-experiment-to-web-service/model-stages-from-experiment-to-web-service.png)

*Rysunek 1 — etapy modelu analizy predykcyjnej typowe*

### <a name="the-training-experiment"></a>Eksperyment uczenia
***Eksperyment uczenia*** jest początkowy etap tworzenia usługi sieci Web w usłudze Machine Learning Studio. Eksperyment uczenia ma na celu zapewniają miejsce, aby tworzyć, testować, iteracji i ostatecznie uczenia modelu uczenia maszynowego. Możesz to zrobić nawet później wielu modeli jednocześnie Wyszukaj najlepszego rozwiązania, ale po zakończeniu eksperymentowanie możesz wybrać jeden uczenia modelu i wyeliminować rest z eksperymentu. Na przykład opracowania eksperymentu analizy predykcyjnej, zobacz [tworzenie rozwiązania analizy predykcyjnej w celu oceny ryzyka kredytowego w usłudze Azure Machine Learning](walkthrough-develop-predictive-solution.md).

### <a name="the-predictive-experiment"></a>Eksperyment predykcyjny
Po uzyskaniu nauczonego modelu w eksperymencie szkolenia, kliknij przycisk **ustawić usługę sieci Web** i wybierz **predykcyjnej usługi sieci Web** w Machine Learning Studio, aby zainicjować proces konwersji do szkolenia eksperymentu do ***eksperyment predykcyjny***. Eksperyment predykcyjny ma na celu użyj uczonego modelu do oceniać nowe dane, w celu ostatecznie staje się operationalized jako usługi sieci Web platformy Azure.

Ta konwersja odbywa się automatycznie następujące czynności:

* Konwertuj zbiór moduły używane do trenowania w jednym module i zapisz go jako uczonego modelu
* Eliminowanie nadmiarowe moduły nie dotyczą oceniania
* Dodać porty wejściowe i wyjściowe, używające ostatecznego usługi sieci Web

Może to być dalszych zmian, które mają być w celu przygotowania predykcyjnej eksperymentu można wdrożyć jako usługę sieci Web. Na przykład jeśli chcesz, aby usługi sieci Web do wyjściowego tylko podzestaw wyników, można dodać moduł filtrowania przed port wyjściowy.

W tym procesie konwersji eksperyment uczenia nie zostaną odrzucone. Po zakończeniu procesu ma dwie karty w Studio: jeden dla eksperyment uczenia i jeden dla eksperyment predykcyjny. W ten sposób można wprowadzić zmiany do eksperyment uczenia przed wdrożeniem usługi sieci Web i Odbuduj eksperyment predykcyjny. Lub możesz zapisać kopię eksperyment uczenia zacząć od innego wiersza eksperymenty.

> [!NOTE]
> Po kliknięciu **predykcyjnej usługi sieci Web** uruchomić proces automatycznego, aby przekonwertować eksperymentu uczenia eksperyment predykcyjny i to działa dobrze w większości przypadków. Jeśli eksperymentu uczenia jest złożony (na przykład masz wiele ścieżek szkolenia, do której należy dołączyć razem), może wolisz ręcznie wykonaj tę konwersję. Aby uzyskać więcej informacji, zobacz [jak przygotować modelu do wdrożenia w usłudze Azure Machine Learning Studio](convert-training-experiment-to-scoring-experiment.md).
> 
> 

### <a name="the-web-service"></a>Usługi sieci Web
Po zakończeniu eksperymentu predykcyjnej jest gotowy, można wdrożyć usługi jako usługi sieci Web klasycznego czy usługi sieci Web nowego oparte na usłudze Azure Resource Manager. Aby operacjonalizować model przez wdrożenie jej jako *klasycznego Machine Learning w sieci Web usługi*, kliknij przycisk **wdrażanie usługi sieci Web** i wybierz **wdrażanie usługi sieci Web [klasyczny]**. Aby wdrożyć jako *usługi sieci Web uczenie nowe maszyny*, kliknij przycisk **wdrażanie usługi sieci Web** i wybierz **wdrażanie usługi sieci Web [New]**. Użytkownicy mogą teraz wysyłać dane do modelu za pomocą interfejsu API REST usługi sieci Web i ponownie odbierania wyników. Aby uzyskać więcej informacji, zobacz [How to consume an Azure Machine Learning Web service](consume-web-services.md) (Jak korzystać z usługi internetowej Azure Machine Learning).

## <a name="the-non-typical-case-creating-a-non-predictive-web-service"></a>Typowy przypadek: tworzenie-predykcyjnej usługi sieci Web
Jeśli eksperymentu nie uczenia modelu analizy predykcyjnej, możesz nie trzeba tworzyć eksperyment uczenia oraz oceniania eksperymentu — istnieje tylko jeden eksperymentu i można go wdrożyć jako usługę sieci Web. Usługa Machine Learning Studio wykrywa, czy eksperymentu zawiera model predykcyjny analizując moduły, w których używano.

Po zostały iterowane w eksperymencie i uzyskaniu go:

1. Kliknij przycisk **ustawić usługę sieci Web** i wybierz **ponownego trenowania usługi sieci Web** — wejściowa i wyjściowa węzły są dodawane automatycznie
2. Kliknij przycisk **Uruchom**
3. Kliknij przycisk **wdrażanie usługi sieci Web** i wybierz **wdrażanie usługi sieci Web [klasyczny]** lub **wdrażanie usługi sieci Web [New]** w zależności od środowiska, do której chcesz wdrożyć.

Usługi sieci Web został wdrożony i można uzyskać dostęp i zarządzać nią tak samo jak predykcyjnej usługi sieci Web.

## <a name="updating-your-web-service"></a>Aktualizowanie usługi sieci Web
Teraz, gdy eksperymentu została wdrożona jako usługę sieci Web, co zrobić, jeśli musisz zaktualizować go?

To zależy od należy zaktualizować:

**Aby zmienić danych wejściowych lub wyjściowych lub chcesz zmodyfikować, jak usługa sieci Web manipulowanie danymi**

Jeśli nie chcesz zmienić model, ale tylko zmiany sposobu obsługi danych przez usługę sieci Web, można edytować eksperyment predykcyjny, a następnie kliknij przycisk **wdrażanie usługi sieci Web** i wybierz **wdrażanie usługi sieci Web [klasyczny]** lub **wdrażanie usługi sieci Web [New]** ponownie. Usługa sieci Web została zatrzymana, zaktualizowane eksperyment predykcyjny jest wdrożony i ponownego uruchomienia usługi sieci Web.

Oto przykład: Załóżmy, że cały wiersz danych wejściowych z wynikiem przewidywane zwraca predykcyjnej eksperymentu. Użytkownik może określić, że usługi sieci Web do właśnie zwrócić wyników. Dzięki czemu można dodać **kolumny projektu** modułu w eksperymencie predykcyjnych, bezpośrednio przed port wyjściowy, aby wykluczyć kolumny innego niż wynik. Po kliknięciu **wdrażanie usługi sieci Web** i wybierz **wdrażanie usługi sieci Web [klasyczny]** lub **wdrażanie usługi sieci Web [New]** ponownie, usługa sieci Web jest aktualizowana.

**Aby ponownie ucz modelu nowymi danymi**

Jeśli chcesz zachować komputerze uczenie modelu, ale ma zostać ponownie ucz go z nowymi danymi, dostępne są dwie opcje:

1. **Ponownie ucz modelu jest uruchomiona usługa sieci Web** — Jeśli chcesz ponownie ucz model predykcyjny usługi sieci Web jest uruchomiona, można to zrobić przez kilka eksperyment uczenia, aby była ***ponownego trenowania eksperymentu***, a następnie wdrożyć go jako  ***web ponownego trenowania* usługi**. Aby uzyskać instrukcje, jak to zrobić, zobacz [Retrain Machine Learning programowo modele](retrain-models-programmatically.md).
2. **Przejdź wstecz do oryginalnego eksperyment uczenia i umożliwia tworzenie modelu danych szkoleniowych różnych** — predykcyjnej eksperymentu jest połączona z usługą sieci Web, ale eksperyment uczenia nie jest bezpośrednio połączony w ten sposób. Jeśli zmodyfikujesz oryginalnego eksperyment uczenia i kliknij przycisk **ustawić usługę sieci Web**, utworzy on *nowe* predykcyjnej eksperymentu, utworzy po wdrożeniu *nowe* sieci Web Usługa. Jednak nie tylko aktualizacji oryginalnego usługi sieci Web.
   
   Jeśli trzeba zmodyfikować eksperyment uczenia, otwórz go i kliknij przycisk **Zapisz jako** do skopiowania. Spowoduje to pozostawić bez zmian oryginalnego eksperyment uczenia eksperyment predykcyjny i usługi sieci Web. Można teraz utworzyć nową usługę sieci Web ze zmianami. Po wdrożeniu nową usługę sieci Web można następnie zdecydować, czy zatrzymanie poprzedniej usługi sieci Web lub działać równolegle z nowym.

**Aby uczenie modelu przy użyciu różnych**

Jeśli chcesz wprowadzić zmiany w oryginalnym eksperyment predykcyjny, takie jak wybranie innej maszyny Algorytm uczenia próby szkolenia innej metody, itp., a następnie należy wykonać w drugiej procedurze opisane powyżej, aby ponownego trenowania modelu: Otwórz uczenie eksperyment, kliknij przycisk **Zapisz jako** do skopiowania, a następnie uruchom nową ścieżkę tworzenia modelu, eksperyment predykcyjny tworzenie i wdrażanie usługi sieci web w dół. Spowoduje to utworzenie nowej sieci Web, usługi niezwiązanych ze sobą do oryginalnego — można zdecydować, które z nich i/lub do nadal działać.

## <a name="next-steps"></a>Następne kroki
Więcej szczegółów na proces projektowania i doświadczenia zobacz następujące artykuły:

* Konwertowanie eksperymentu - [jak przygotować modelu do wdrożenia w usłudze Azure Machine Learning Studio](convert-training-experiment-to-scoring-experiment.md)
* Wdrażanie usługi sieci Web - [wdrażanie usługi sieci web Azure Machine Learning](publish-a-machine-learning-web-service.md)
* ponownego trenowania model — [Retrain Machine Learning modeli](retrain-models-programmatically.md)

Aby uzyskać przykłady całego procesu zobacz:

* [Samouczek dotyczący uczenia maszynowego: Tworzenie pierwszego eksperymentu w usłudze Azure Machine Learning Studio](create-experiment.md)
* [Przewodnik: Tworzenie rozwiązania analizy predykcyjnej w celu oceny ryzyka kredytowego w usłudze Azure Machine Learning](walkthrough-develop-predictive-solution.md)

