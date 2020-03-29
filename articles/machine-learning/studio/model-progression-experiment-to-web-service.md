---
title: Jak model staje się usługą sieci web
titleSuffix: ML Studio (classic) - Azure
description: Omówienie mechaniki postępów modelu usługi Azure Machine Learning Studio (klasycznego) z eksperymentu deweloperskiego do usługi sieci Web.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: previous-ms.author=yahajiza, previous-author=YasinMSFT
ms.date: 03/20/2017
ms.openlocfilehash: e6a8fe7771776a92b3c28a188bc352e3bb105313
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79217916"
---
# <a name="how-a-machine-learning-studio-classic-model-progresses-from-an-experiment-to-a-web-service"></a>Jak model usługi Machine Learning Studio (klasyczny) przechodzi z eksperymentu do usługi sieci Web

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]
Usługa Azure Machine Learning Studio (klasyczna) udostępnia interaktywną kanwę, która umożliwia tworzenie, uruchamianie, testowanie i iterowanie ***eksperymentu*** reprezentującego model analizy predykcyjnej. Dostępnych jest wiele różnych modułów, które mogą:

* Dane wejściowe do eksperymentu
* Manipulowanie danymi
* Szkolenie modelu przy użyciu algorytmów uczenia maszynowego
* Ocena modelu
* Ocena wyników
* Ostateczne wartości wyjściowe

Po zakończeniu eksperymentu można go wdrożyć jako ***klasyczną usługę azure machine learning web*** lub nową usługę azure machine learning sieci ***Web,*** dzięki czemu użytkownicy mogą wysyłać nowe dane i odbierać wyniki.

W tym artykule przedstawiamy omówienie mechaniki postępów modelu usługi Machine Learning z eksperymentu dewelopera do zdekcjonowanej usługi sieci Web.

> [!NOTE]
> Istnieją inne sposoby tworzenia i wdrażania modeli uczenia maszynowego, ale w tym artykule koncentruje się na tym, jak używać programu Machine Learning Studio (klasyczny). Aby na przykład przeczytać opis tworzenia klasycznej predykcyjnej usługi sieci Web za pomocą języka R, zobacz wpis w blogu [Kompilacja & wdrażanie predykcyjnych aplikacji sieci Web przy użyciu programu RStudio i studia Azure Machine Learning](https://blogs.technet.com/b/machinelearning/archive/2015/09/25/build-and-deploy-a-predictive-web-app-using-rstudio-and-azure-ml.aspx).
>
>

Usługa Azure Machine Learning Studio (classic) została zaprojektowana, aby ułatwić opracowywanie i *wdrażanie modelu analizy predykcyjnej,* możliwe jest użycie studio (klasycznego) do opracowania eksperymentu, który nie zawiera modelu analizy predykcyjnej. Na przykład eksperyment może po prostu wprowadzić dane, manipulować nimi, a następnie wyprowadzić wyniki. Podobnie jak w przypadku eksperymentu analizy predykcyjnej, można wdrożyć ten eksperyment nie predykcyjny jako usługę sieci Web, ale jest to prostszy proces, ponieważ eksperyment nie jest szkolenie lub oceniania modelu uczenia maszynowego. Chociaż nie jest to typowe użycie Studio (classic) w ten sposób, będziemy go uwzględnić w dyskusji, tak abyśmy mogli dać pełne wyjaśnienie, jak Studio (klasyczny) działa.

## <a name="developing-and-deploying-a-predictive-web-service"></a>Tworzenie i wdrażanie predykcyjnej usługi sieci Web
Oto etapy, które typowe rozwiązanie następuje podczas opracowywania i wdrażania go przy użyciu machine learning Studio (klasyczny):

![Przepływ wdrażania](./media/model-progression-experiment-to-web-service/model-stages-from-experiment-to-web-service.png)

*Rysunek 1 - Etapy typowego modelu analizy predykcyjnej*

### <a name="the-training-experiment"></a>Eksperyment szkoleniowy
Eksperyment ***szkoleniowy*** jest początkową fazą tworzenia usługi sieci Web w usłudze Machine Learning Studio (klasyczny). Celem eksperymentu szkoleniowego jest zapewnienie miejsca do opracowania, przetestowania, iteracji i ostatecznie uczenia modelu uczenia maszynowego. Możesz nawet trenować wiele modeli jednocześnie, szukając najlepszego rozwiązania, ale po zakończeniu eksperymentowania wybierzesz jeden przeszkolony model i wyeliminujesz resztę z eksperymentu. Na przykład opracowania eksperymentu analizy predykcyjnej zobacz [Opracowywanie rozwiązania analizy predykcyjnej do oceny ryzyka kredytowego w usłudze Azure Machine Learning Studio (klasycznym).](tutorial-part1-credit-risk.md)

### <a name="the-predictive-experiment"></a>Eksperyment predykcyjny
Po przeszkoleniu modelu w eksperymencie szkoleniowym kliknij pozycję **Skonfiguruj usługę sieci Web** i wybierz **pozycję Predictive Web Service** w machine learning studio (klasyczny), aby zainicjować proces konwersji eksperymentu szkoleniowego na eksperyment ***predykcyjny.*** Celem eksperymentu predykcyjnego jest użycie wyszkolonego modelu do uzyskania nowych danych, z celem, aby ostatecznie stać się operationalized jako usługa sieci Web platformy Azure.

Ta konwersja odbywa się za Ciebie w następujących krokach:

* Przekształć zestaw modułów używanych do szkolenia w jeden moduł i zapisz go jako przeszkolony model
* Wyeliminuj wszelkie moduły obce niezwiązane z punktacją
* Dodawanie portów wejściowych i wyjściowych, z których będzie korzystać ewentualna usługa sieci Web

Może być więcej zmian, które chcesz wprowadzić, aby przygotować eksperyment predykcyjny do wdrożenia jako usługa sieci Web. Na przykład jeśli chcesz, aby usługa sieci Web wysuń tylko podzbiór wyników, można dodać moduł filtrowania przed portem wyjściowym.

W tym procesie konwersji eksperyment szkoleniowy nie jest odrzucany. Po zakończeniu procesu masz dwie karty w Studio (klasyczny): jeden dla eksperymentu szkoleniowego i jeden dla eksperymentu predykcyjnego. W ten sposób można wprowadzić zmiany w eksperymencie szkoleniowym przed wdrożeniem usługi sieci Web i odbudować eksperyment predykcyjny. Możesz też zapisać kopię eksperymentu szkoleniowego, aby rozpocząć kolejną linię eksperymentów.

> [!NOTE]
> Po **kliknięciu przycisku Predictive Web Service** rozpoczynasz automatyczny proces konwersji eksperymentu szkoleniowego na eksperyment predykcyjny, co w większości przypadków sprawdza się dobrze. Jeśli eksperyment szkoleniowy jest złożony (na przykład masz wiele ścieżek do szkolenia, które łączysz), możesz chcieć wykonać tę konwersję ręcznie. Aby uzyskać więcej informacji, zobacz [Jak przygotować model do wdrożenia w usłudze Azure Machine Learning Studio (klasyczny).](convert-training-experiment-to-scoring-experiment.md)
>
>

### <a name="the-web-service"></a>Usługa internetowa
Gdy upewnisz się, że eksperyment predykcyjny jest gotowy, możesz wdrożyć usługę jako klasyczną usługę sieci Web lub nową usługę sieci Web opartą na usłudze Azure Resource Manager. Aby operacjonalizacji modelu, wdrażając go jako *klasyczną usługę uczenia maszynowego sieci Web,* kliknij pozycję **Wdrażanie usługi sieci Web** i wybierz pozycję **Wdrażanie usługi sieci Web [Classic]**. Aby wdrożyć jako *nową usługę sieci Web uczenia maszynowego,* kliknij pozycję **Wdrażanie usługi sieci Web** i wybierz pozycję **Wdrażanie usługi sieci Web [Nowy]**. Użytkownicy mogą teraz wysyłać dane do modelu przy użyciu interfejsu API REST usługi sieci Web i odbierać wyniki. Aby uzyskać więcej informacji, zobacz [How to consume an Azure Machine Learning Web service](consume-web-services.md) (Jak korzystać z usługi internetowej Azure Machine Learning).

## <a name="the-non-typical-case-creating-a-non-predictive-web-service"></a>Nietypowy przypadek: tworzenie nieprzewidziącej usługi sieci Web
Jeśli eksperyment nie szkoli modelu analizy predykcyjnej, nie trzeba tworzyć zarówno eksperymentu szkoleniowego, jak i eksperymentu oceniania — istnieje tylko jeden eksperyment i można go wdrożyć jako usługę sieci Web. Machine Learning Studio (klasyczny) wykrywa, czy eksperyment zawiera model predykcyjny, analizując używane moduły.

Po iterowanym w eksperymencie i zadowoleniu z niego:

1. Kliknij **pozycję Konfigurowanie usługi sieci Web** i wybierz pozycję Ponowne szkolenie usługi sieci **Web** — węzły wejściowe i wyjściowe są dodawane automatycznie
2. Kliknij **przycisk Uruchom**
3. Kliknij **pozycję Wdrażanie usługi sieci Web** i wybierz pozycję **Wdrażanie usługi sieci Web [Klasyczny]** lub **Wdrażanie usługi sieci Web [Nowy]** w zależności od środowiska, w którym chcesz wdrożyć.

Usługa sieci Web jest teraz wdrożona i można uzyskać do niej dostęp i zarządzać nią tak samo jak predykcyjną usługę sieci Web.

## <a name="updating-your-web-service"></a>Aktualizowanie usługi sieci Web
Teraz, gdy eksperyment został wdrożony jako usługa sieci Web, co zrobić, jeśli musisz go zaktualizować?

To zależy od tego, co trzeba zaktualizować:

**Chcesz zmienić dane wejściowe lub wyjściowe lub chcesz zmodyfikować sposób manipulowania danymi przez usługę sieci Web**

Jeśli nie zmieniasz modelu, ale po prostu zmieniasz sposób obsługi danych przez usługę sieci Web, możesz edytować eksperyment predykcyjny, a następnie kliknąć pozycję **Deploy Web Service** i ponownie wybierz pozycję Deploy Web Service **[Classic]** lub **Deploy Web Service [New].** Usługa sieci Web jest zatrzymana, zaktualizowany eksperyment predykcyjny jest wdrażany, a usługa sieci Web jest ponownie uruchamiana.

Oto przykład: Załóżmy, że eksperyment predykcyjny zwraca cały wiersz danych wejściowych z przewidywanym wynikiem. Możesz zdecydować, że chcesz, aby usługa sieci Web po prostu zwróciła wynik. Dlatego można dodać moduł **Kolumny projektu** w eksperymencie predykcyjnym, tuż przed portem wyjściowym, aby wykluczyć kolumny inne niż wynik. Po **kliknięciu przycisku Wdrażanie usługi sieci Web** i wybraniu opcji **Wdrażanie usługi sieci Web [Klasyczny]** lub **Wdrożenie usługi sieci Web [Nowy]** usługa sieci Web zostanie zaktualizowana.

**Chcesz przekwalifikować model z nowymi danymi**

Jeśli chcesz zachować model uczenia maszynowego, ale chcesz przekwalifikować go z nowymi danymi, masz dwie możliwości:

1. **Przekwalifikowanie modelu, gdy usługa sieci Web jest uruchomiona** — jeśli chcesz przeszkolić model, gdy jest uruchomiona predykcyjna usługa sieci Web, możesz to zrobić, wprowadzając kilka modyfikacji eksperymentu szkoleniowego, aby uczynić go ***eksperymentem przekwalifikowania,*** a następnie można go wdrożyć jako ** *usługę sieci web przekwalifikowania* **. Aby uzyskać instrukcje dotyczące programowania, zobacz [Programowo przekwalifikowanie modeli uczenia maszynowego.](/azure/machine-learning/studio/retrain-machine-learning-model)
2. **Wróć do oryginalnego eksperymentu szkoleniowego i użyj różnych danych szkoleniowych do opracowania modelu** — eksperyment predykcyjny jest połączony z usługą sieci Web, ale eksperyment szkoleniowy nie jest bezpośrednio połączony w ten sposób. Jeśli zmodyfikujesz oryginalny eksperyment szkoleniowy i klikniesz pozycję **Skonfiguruj usługę sieci Web,** utworzy *on nowy* eksperyment predykcyjny, który po wdrożeniu utworzy *nową* usługę sieci Web. Nie tylko aktualizuje oryginalną usługę sieci Web.

   Jeśli chcesz zmodyfikować eksperyment szkoleniowy, otwórz go i kliknij przycisk **Zapisz,** aby wykonać kopię. Spowoduje to pozostawienie nienaruszone oryginalnego eksperymentu szkoleniowego, eksperymentu predykcyjnego i usługi sieci Web. Teraz można utworzyć nową usługę sieci Web ze zmianami. Po wdrożeniu nowej usługi sieci Web można zdecydować, czy zatrzymać poprzednią usługę sieci Web, czy zachować ją w działaniu obok nowej.

**Chcesz wyszkolić inny model**

Jeśli chcesz wprowadzić zmiany w oryginalnym eksperymencie predykcyjnym, takie jak wybranie innego algorytmu uczenia maszynowego, wypróbowanie innej metody szkolenia itp., należy wykonać drugą procedurę opisaną powyżej w celu przekwalifikowania modelu: otwórz eksperyment szkoleniowy, kliknij przycisk **Zapisz jako,** aby utworzyć kopię, a następnie rozpocznij nową ścieżkę tworzenia modelu, tworzenia eksperymentu predykcyjnego i wdrażania usługi sieci web. Spowoduje to utworzenie nowej usługi sieci Web niezwiązanej z oryginalną — można zdecydować, która lub obie usługa będzie działać.

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat procesu tworzenia i eksperymentowania, zobacz następujące artykuły:

* konwertowanie eksperymentu — [jak przygotować model do wdrożenia w usłudze Azure Machine Learning Studio (klasyczny)](convert-training-experiment-to-scoring-experiment.md)
* wdrażanie usługi sieci Web — [wdrażanie usługi sieci Web usługi Azure Machine Learning](deploy-a-machine-learning-web-service.md)
* przekwalifikowanie modelu — [programowo przekwalifikowanie modeli uczenia maszynowego](/azure/machine-learning/studio/retrain-machine-learning-model)

Przykłady całego procesu można znaleźć w części:

* [Samouczek uczenia maszynowego: tworzenie pierwszego eksperymentu w usłudze Azure Machine Learning Studio (klasyczny)](create-experiment.md)
* [Przewodnik: Opracowanie rozwiązania do analizy predykcyjnej do oceny ryzyka kredytowego w usłudze Azure Machine Learning](tutorial-part1-credit-risk.md)

