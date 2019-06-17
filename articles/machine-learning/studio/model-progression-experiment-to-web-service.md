---
title: Jak usługa sieci web staje się na modelu
titleSuffix: Azure Machine Learning Studio
description: Omówienie sposobu działania jak usługi Azure Machine Learning Studio w miarę modelu od etapu programowania eksperymentować z usługą sieci Web.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: previous-ms.author=yahajiza, previous-author=YasinMSFT
ms.date: 03/20/2017
ms.openlocfilehash: 28bb96099acb800d9095325b8c7b46a6b5124b4e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61066052"
---
# <a name="how-a-machine-learning-studio-model-progresses-from-an-experiment-to-a-web-service"></a>Jak modelu usługi Machine Learning Studio w miarę z eksperymentu usługi sieci Web
Usługa Azure Machine Learning Studio udostępnia interaktywne kanwy, która pozwala na tworzenie, uruchamianie, testów i modyfikować ***eksperymentować*** reprezentujący modelu analizy predykcyjnej. Istnieją szerokiej gamy dostępnych modułów, które można:

* Dane wejściowe do eksperymentu
* Manipulowanie danymi
* Uczenie modelu przy użyciu algorytmów uczenia maszynowego
* Ocena modelu
* Ocena wyników
* Końcowe wartości danych wyjściowych

Po zakończeniu eksperymentu można wdrożyć go jako ***klasycznej Azure Machine Learning w sieci Web usługi*** lub ***nowe usługi Azure Machine Learning w sieci Web usługi*** tak, aby użytkownicy mogli nowych danych i odbierania Wstecz wyniki.

W tym artykule udostępniamy omówienie sposobu działania jak eksperymentować z postępów modelu uczenia maszynowego od etapu programowania do zoperacjonalizowanej usługi sieci Web.

> [!NOTE]
> Istnieją inne sposoby tworzenia i wdrażania modeli uczenia maszynowego, ale w tym artykule koncentruje się na jak używać usługi Machine Learning Studio. Na przykład, aby uzyskać opis sposobu tworzenia klasycznego predykcyjne usługi sieci Web przy użyciu języka R, zobacz wpis w blogu [kompilacji i wdrażania predykcyjne sieci Web Apps przy użyciu programu RStudio i Azure Machine Learning studio](https://blogs.technet.com/b/machinelearning/archive/2015/09/25/build-and-deploy-a-predictive-web-app-using-rstudio-and-azure-ml.aspx).
>
>

Gdy usługi Azure Machine Learning Studio jest ułatwia tworzenie i wdrażanie *modelu analizy predykcyjnej*, można używać programu Studio do tworzenia eksperymentu, który nie zawiera modelu analizy predykcyjnej. Na przykład eksperymentu może tylko dane wejściowe, manipulowania go, a następnie wyprowadzi wyniki. Podobnie jak eksperymentu analizy predykcyjnej można wdrożyć tego eksperymentu predykcyjny jako usługę sieci Web, ale jest prostszy proces, ponieważ eksperymentu nie jest szkolenia i oceniania modelu uczenia maszynowego. Mimo że nie jest to typowy używać programu Studio w ten sposób, będzie uwzględniamy je w dyskusji, dzięki czemu możemy udostępnić pełne wyjaśnienie sposobu działania Studio.

## <a name="developing-and-deploying-a-predictive-web-service"></a>Tworzenie i wdrażanie predykcyjne usługi sieci Web
Oto etapy, które następują typowe rozwiązanie, podczas tworzenia i wdrażania go za pomocą usługi Machine Learning Studio:

![Przepływ wdrożenia](./media/model-progression-experiment-to-web-service/model-stages-from-experiment-to-web-service.png)

*Rysunek 1 — etapy modelu Typowa analizy predykcyjnej*

### <a name="the-training-experiment"></a>Eksperymentu szkolenia
***Eksperymentu szkolenia*** jest początkowa faza związanych z tworzeniem usługi sieci Web w usłudze Machine Learning Studio. Eksperymentu szkolenia ma na celu zapewniają miejsce opracowywanie, testowanie, iteracji i ostatecznie szkolenie modelu uczenia maszynowego. Możesz nawet uczyć wielu modeli jednocześnie poszukaj najlepszym rozwiązaniem, ale po zakończeniu eksperymentowanie, możesz wybrać jeden uczony model obsługi i eliminowanie rest z eksperymentu. Aby uzyskać przykład tworzenia eksperymentu analizy predykcyjnej, zobacz [tworzenia rozwiązania analizy predykcyjnej w celu oceny ryzyka kredytowego w usłudze Azure Machine Learning Studio](tutorial-part1-credit-risk.md).

### <a name="the-predictive-experiment"></a>Eksperyment predykcyjny
Po utworzeniu uczonego modelu w eksperymentu szkolenia, kliknij przycisk **ustawić usługę sieci Web** i wybierz **predykcyjne usługi sieci Web** w celu zainicjowania procesu konwersji szkolenia usługi Machine Learning Studio eksperymentowanie do ***eksperyment predykcyjny***. Eksperyment predykcyjny ma na celu użyj uczonego modelu, aby oceniać nowe dane, z celem ostatecznie staje się przygotowany do działania jako usługa sieci Web platformy Azure.

Ta konwersja jest wykonywane przez następujące kroki:

* Konwertowanie zestawu moduły używane na potrzeby szkolenia w pojedynczym module i zapisz go jako uczonego modelu
* Wyeliminowanie wszelkich nadmiarowe modułów, które nie dotyczą oceniania
* Dodaj porty wejściowe i wyjściowe, używających ostatecznej usługi sieci Web

Może istnieć więcej zmian, które mają być w celu przygotowania eksperymentu predykcyjnego można wdrożyć jako usługę sieci Web. Na przykład jeśli chcesz, aby usługi sieci Web służący do wypełniania wyjściowego tylko podzbiór wyników, można dodać modułu filtrowania przed port wyjściowy.

W ramach tego procesu konwersji eksperymentu szkolenia nie zostanie usunięty. Po zakończeniu procesu ma dwie karty w Studio: jeden dla eksperymentu szkolenia i jeden dla eksperyment predykcyjny. W ten sposób można wprowadzić zmiany do eksperymentu szkolenia, przed wdrożeniem usługi sieci Web i ponownie utworzyć eksperyment predykcyjny. Można też zapisać kopię eksperymentu szkolenia, aby uruchomić kolejny wiersz eksperymentowanie w usłudze.

> [!NOTE]
> Po kliknięciu **predykcyjne usługi sieci Web** uruchamiania procesu automatycznego, aby przekonwertować eksperymentu szkolenia na eksperyment predykcyjny i to działa dobrze w większości przypadków. Jeśli eksperymentu szkolenia jest złożony (na przykład masz wiele ścieżek do trenowania, który można dołączyć razem), możesz preferować ręcznego wykonywania konwersji. Aby uzyskać więcej informacji, zobacz [jak przygotować modelu wdrożenia w usłudze Azure Machine Learning Studio](convert-training-experiment-to-scoring-experiment.md).
>
>

### <a name="the-web-service"></a>Usługa sieci Web
Po zakończeniu czy eksperymentu predykcyjnej jest gotowy, można wdrożyć jako usługę sieci Web usługi lub usługi sieci Web nowy oparty na usłudze Resource Manager. Do obsługi operacji modelu, wdrażając go jako *usługi sieci Web Machine Learning*, kliknij przycisk **wdrażanie usługi sieci Web** i wybierz **wdrażanie usługi sieci Web [klasyczny]** . Do wdrożenia jako *nowe Machine Learning w sieci Web usługi*, kliknij przycisk **wdrażanie usługi sieci Web** i wybierz **wdrażanie usługi sieci Web [New]** . Użytkownicy mogą teraz wysyłać dane do modelu przy użyciu usługi sieci Web interfejsu API REST i ponownie wyświetlone wyniki. Aby uzyskać więcej informacji, zobacz [How to consume an Azure Machine Learning Web service](consume-web-services.md) (Jak korzystać z usługi internetowej Azure Machine Learning).

## <a name="the-non-typical-case-creating-a-non-predictive-web-service"></a>Typowy przypadek: tworzenie-predykcyjne usługi sieci Web
Jeśli eksperymentu nie uczenie modelu analizy predykcyjnej, a następnie użytkownik nie ma potrzeby tworzenia eksperymentu szkolenia i oceniania eksperymentu — istnieje tylko jeden eksperyment i można go wdrożyć jako usługę sieci Web. Usługa Machine Learning Studio wykrywa, czy eksperymentu zawiera model predykcyjny, analizując modułów, które zostały użyte.

Po został postanowiliśmy eksperymentu i uzyskaniu go:

1. Kliknij przycisk **ustawić usługę sieci Web** i wybierz **ponownego trenowania usługi sieci Web** — danych wejściowych i wyjściowych węzły są dodawane automatycznie
2. Kliknij przycisk **uruchamiania**
3. Kliknij przycisk **wdrażanie usługi sieci Web** i wybierz **wdrażanie usługi sieci Web [klasyczny]** lub **wdrażanie usługi sieci Web [New]** w zależności od środowiska, do której chcesz wdrożyć.

Teraz jest wdrożona usługa sieci Web i można uzyskać dostęp i zarządzać nią tak samo jak predykcyjne usługi sieci Web.

## <a name="updating-your-web-service"></a>Aktualizowanie usługi sieci Web
Teraz, że udało Ci się wdrożyć eksperymentu jako usługę sieci Web, co zrobić, jeśli trzeba ją zaktualizować?

To zależy od wymaga zaktualizowania:

**Aby zmienić dane wejściowe lub wyjściowe lub chcesz zmodyfikować, jak manipuluje danymi przez usługę sieci Web**

Jeśli nie jesteś zmiana modelu, ale wystarczy zmienić sposób obsługi danych przez usługę sieci Web, można edytować eksperyment predykcyjny, a następnie kliknij przycisk **wdrażanie usługi sieci Web** i wybierz **wdrażanie usługi sieci Web [klasyczny]** lub **wdrażanie usługi sieci Web [New]** ponownie. Usługa sieci Web jest zatrzymana, wdrożono zaktualizowaną eksperyment predykcyjny i ponownym uruchomieniu usługi sieci Web.

Oto przykład: Załóżmy, że Twoje eksperyment predykcyjny zwraca cały wiersz danych wejściowych z przewidywany wynik. Użytkownik może zdecydować, czy chcesz, aby usługi sieci Web, można po prostu zwraca wynik. Więc można dodać **kolumny projektu** modułu w eksperyment predykcyjny, tuż przedtem port wyjściowy, które mają zostać wykluczone z kolumn innych niż wynik. Po kliknięciu **wdrażanie usługi sieci Web** i wybierz **wdrażanie usługi sieci Web [klasyczny]** lub **wdrażanie usługi sieci Web [New]** ponownie, usługa sieci Web jest aktualizowana.

**Aby ponowne szkolenie modelu za pomocą nowych danych**

Jeśli chcesz zachować modelu uczenia maszynowego, ale chcesz ponownie ucz je za pomocą nowych danych, dostępne są dwie opcje:

1. **Ponowne szkolenie modelu, gdy jest uruchomiona usługa sieci Web** — Jeśli chcesz ponowne szkolenie modelu predykcyjnego usługi sieci Web jest uruchomiona, można to zrobić, wprowadzając kilka zmian eksperymentu szkolenia, aby stał się ***ponownego trenowania eksperymentowanie***, a następnie wdrożyć go jako  ***ponownego trenowania w sieci web* usługi**. Aby uzyskać instrukcje, jak to zrobić, zobacz [Retrain Machine Learning models programowo](/azure/machine-learning/studio/retrain-machine-learning-model).
2. **Wróć do oryginalnego eksperymentu szkolenia i umożliwia tworzenie modelu danych szkoleniowych różnych** — eksperyment predykcyjny jest połączona z usługą sieci Web, ale eksperymentu szkolenia nie są bezpośrednio powiązane w ten sposób. Jeśli zmodyfikujesz oryginalny eksperymentu szkolenia, a kliknij **ustawić usługę sieci Web**, zostanie utworzony *nowe* predykcyjne eksperymentować, utworzy po wdrożeniu *nowe* sieci Web Usługa. Jednak nie powoduje aktualizacji tylko oryginalnej usługi sieci Web.

   Jeśli trzeba zmodyfikować eksperymentu szkolenia, otwórz go i kliknij przycisk **Zapisz jako** do skopiowania. Spowoduje to pozostawić bez zmian, oryginalnym eksperymentu szkolenia eksperyment predykcyjny i usługi sieci Web. Teraz można utworzyć nowej usługi sieci Web, z uwzględnieniem zmienionych uprawnień. Po wdrożeniu nowej usługi sieci Web, możesz następnie zdecydować, czy zatrzymanie poprzedniej usługi sieci Web lub zapewnienia jego działania wraz z nowym.

**Ma to w opracowywaniu innego modelu**

Jeśli chcesz wprowadzić zmiany w oryginalnym eksperyment predykcyjny, np. wybranie innego algorytmu, uczenia maszynowego podjęcie próby szkolenia różne metody, itp., a następnie należy wykonać drugiej procedury opisane powyżej, aby ponowne trenowanie modelu: Otwórz szkolenie eksperymentu, kliknij przycisk **Zapisz jako** do skopiowania, a następnie uruchom nową ścieżkę opracowanie modelu, tworzenie eksperyment predykcyjny i wdrażanie usługi sieci web w dół. Spowoduje to utworzenie nowej sieci Web usługi niezwiązanych ze sobą do oryginalnego — można określić, który z nich lub obie umożliwia kontynuowanie działania.

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać szczegółowe informacje na temat procesu opracowywaniem i eksperymentu zobacz następujące artykuły:

* Konwertowanie eksperymentu - [jak przygotować modelu wdrożenia w usłudze Azure Machine Learning Studio](convert-training-experiment-to-scoring-experiment.md)
* Wdrażanie usługi sieci Web - [wdrażanie usługi sieci web Azure Machine Learning](publish-a-machine-learning-web-service.md)
* Ponowne szkolenie modelu — [modeli Retrain Machine Learning](/azure/machine-learning/studio/retrain-machine-learning-model)

Aby uzyskać przykłady całego procesu zobacz:

* [Samouczek dotyczący uczenia maszynowego: Tworzenie pierwszego eksperymentu w usłudze Azure Machine Learning Studio](create-experiment.md)
* [Wskazówki: Tworzenie rozwiązania analizy predykcyjnej w celu oceny ryzyka kredytowego w usłudze Azure Machine Learning](tutorial-part1-credit-risk.md)

