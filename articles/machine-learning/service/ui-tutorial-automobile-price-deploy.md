---
title: 'Samouczek: Wdrażanie modelu uczenia maszynowego za pomocą interfejsu wizualnego'
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak utworzyć rozwiązanie do analizy predykcyjnej w interfejsie Visual Azure Machine Learning usługi. Uczenie, ocenę i wdrożenie modelu uczenia maszynowego przy użyciu modułów przeciągania i upuszczania. Ten samouczek jest drugą częścią serii dwóch części na temat przewidywania cen samochodów przy użyciu regresji liniowej.
author: peterclu
ms.author: peterlu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 07/11/2019
ms.openlocfilehash: 64062aeab9a807d2aee7f4bca05d4019a3d9b736
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/10/2019
ms.locfileid: "70858632"
---
# <a name="tutorial-deploy-a-machine-learning-model-with-the-visual-interface"></a>Samouczek: Wdrażanie modelu uczenia maszynowego za pomocą interfejsu wizualnego

Aby umożliwić innym użytkownikom korzystanie z modelu predykcyjnego opracowanego w pierwszej części tego [samouczka](ui-tutorial-automobile-price-train-score.md), możesz go wdrożyć jako usługę sieci Web platformy Azure. Do tej pory udało Ci się eksperymentować z uczeniem modelu. Teraz można generować nowe prognozy na podstawie danych wejściowych użytkownika. W tej części samouczka:

> [!div class="checklist"]
> * Przygotuj model do wdrożenia
> * Wdrażanie usługi internetowej
> * Testowanie usługi sieci Web
> * Zarządzanie usługą sieci Web
> * Korzystanie z usługi sieci Web

## <a name="prerequisites"></a>Wymagania wstępne

Wykonaj [jedną z części samouczka](ui-tutorial-automobile-price-train-score.md) , aby dowiedzieć się, jak szkolić i oceny model uczenia maszynowego w interfejsie wizualnym.

## <a name="prepare-for-deployment"></a>Przygotowanie do wdrożenia

Przed wdrożeniem eksperymentu jako usługi sieci Web należy najpierw skonwertować *eksperyment szkoleniowy* do *eksperymentu predykcyjnego*.

1. Wybierz pozycję **Utwórz eksperyment predykcyjny*** w dolnej części kanwy eksperymentu.

    ![Animowany plik GIF przedstawiający automatyczną konwersję eksperymentu szkoleniowego na eksperyment predykcyjny](./media/ui-tutorial-automobile-price-deploy/deploy-web-service.gif)

    Po wybraniu opcji **Utwórz eksperyment predykcyjny należy wykonać**kilka czynności:
    
    * Szkolony model jest przechowywany jako moduł **przeszkolonych modeli** w palecie modułów. Można go znaleźć w obszarze **modele przeszkolone**.
    * Moduły, które zostały użyte do trenowania zostaną usunięte; w szczególności:
      * Trenowanie modelu
      * Dzielenie danych
      * Ocena modelu
    * Zapisany model przeszkolony zostanie dodany z powrotem do eksperymentu.
    * Dodawane są moduły danych **wejściowych** i **usług** sieci Web. Te moduły identyfikują, gdzie dane użytkownika zostaną wprowadzone do modelu, a dane są zwracane.

    **Eksperyment szkoleniowy** jest nadal zapisywany na nowych kartach w górnej części kanwy eksperymentu.

1. Kliknij przycisk **Run** (Uruchom), aby uruchomić eksperyment

1. Wybierz dane wyjściowe modułu **wynik model** i wybierz pozycję **Wyświetl wyniki** , aby sprawdzić, czy model nadal działa. Wyświetlane są oryginalne dane oraz Przewidywana cena ("etykiety oceny").

Eksperyment powinien teraz wyglądać następująco:  

![Zrzut ekranu przedstawiający oczekiwaną konfigurację eksperymentu po przygotowaniu go do wdrożenia](./media/ui-tutorial-automobile-price-deploy/predictive-graph.png)

## <a name="deploy-the-web-service"></a>Wdrażanie usługi sieci Web

1. Wybierz pozycję **Wdróż usługę sieci Web** poniżej kanwy.

1. Wybierz **obiekt docelowy obliczeń** , dla którego chcesz uruchomić usługę sieci Web.

    Obecnie interfejs wizualny obsługuje tylko wdrażanie w celach obliczeniowych usługi Azure Kubernetes Service (AKS). Możesz wybrać spośród dostępnych obiektów docelowych obliczeniowych AKS w obszarze roboczym usługi Machine Learning lub skonfigurować nowe środowisko AKS przy użyciu kroków w wyświetlonym oknie dialogowym.

    ![Zrzut ekranu przedstawiający możliwą konfigurację nowego elementu docelowego obliczeń](./media/ui-tutorial-automobile-price-deploy/deploy-compute.png)

1. Wybierz pozycję **Wdróż usługę sieci Web**. Po zakończeniu wdrażania zobaczysz następujące powiadomienie. Wdrożenie może potrwać kilka minut.

    ![Zrzut ekranu przedstawiający komunikat potwierdzający pomyślne wdrożenie.](./media/ui-tutorial-automobile-price-deploy/deploy-succeed.png)

## <a name="test-the-web-service"></a>Test usługi sieci web

Możesz przetestować usługi sieci Web interfejsu wizualnego i zarządzać nimi, przechodząc do karty **usługi sieci Web** .

1. Przejdź do sekcji usługa sieci Web. Zostanie wyświetlona usługa sieci Web, którą wdrożono przy użyciu **samouczka z nazwą — przewidywanie ceny samochodów [predykcyjne]** .

     ![Zrzut ekranu przedstawiający kartę usługa sieci Web z ostatnio utworzoną usługą sieci Web](./media/ui-tutorial-automobile-price-deploy/web-services.png)

1. Wybierz nazwę usługi sieci Web, aby wyświetlić dodatkowe szczegóły.

1. Wybierz pozycję **Testuj**.

    [![Zrzut ekranu przedstawiający stronę Testowanie usługi sieci Web](./media/ui-tutorial-automobile-price-deploy/web-service-test.png)](./media/ui-tutorial-automobile-price-deploy/web-service-test.png#lightbox)

1. Dane wejściowe testowania lub użyj autowypełnianych danych przykładowych, a następnie wybierz **test**.

    Żądanie testowe zostanie przesłane do usługi sieci Web, a wyniki są wyświetlane na stronie. Mimo że dla danych wejściowych jest generowana wartość ceny, nie jest ona używana do generowania wartości przewidywania.

## <a name="consume-the-web-service"></a>Korzystanie z usługi sieci Web

Użytkownicy mogą teraz wysyłać żądania interfejsu API do usługi sieci Web platformy Azure i uzyskiwać wyniki, aby przewidzieć cenę nowych samochodów.

**Żądanie/odpowiedź** — użytkownik wysyła do usługi jeden lub więcej wierszy danych typu Automobile przy użyciu protokołu HTTP. Usługa reaguje na co najmniej jeden zestaw wyników.

Przykładowe wywołania REST można znaleźć na karcie **Korzystanie** na stronie Szczegóły usługi sieci Web.

   ![Zrzut ekranu przedstawiający przykładowe wywołanie REST, które użytkownicy mogą znaleźć na karcie korzystanie](./media/ui-tutorial-automobile-price-deploy/web-service-consume.png)

Przejdź do karty **dokumentacji interfejsu API** , aby znaleźć więcej szczegółów interfejsu API.

## <a name="manage-models-and-deployments"></a>Zarządzanie modelami i wdrożeniami

Modele i wdrożenia usługi sieci Web utworzone w interfejsie wizualizacji można także zarządzać za pomocą obszaru roboczego usługi Azure Machine Learning.

1. Otwórz obszar roboczy w [Azure Portal](https://portal.azure.com/).  

1. W obszarze roboczym wybierz pozycję **modele**. Następnie wybierz utworzony eksperyment.

    ![Zrzut ekranu przedstawiający sposób nawigowania do eksperymentów w Azure Portal](./media/ui-tutorial-automobile-price-deploy/portal-models.png)

    Na tej stronie zostaną wyświetlone dodatkowe szczegóły dotyczące modelu.

1. Wybierz pozycję **wdrożenia**, aby wyświetlić listę usług sieci Web, które korzystają z modelu. Wybierz nazwę usługi sieci Web, która zostanie przestawiona na stronę szczegółów usługi sieci Web. Na tej stronie można uzyskać bardziej szczegółowe informacje o usłudze sieci Web.

    [![Raport szczegółowy przebiegu zrzutu ekranu](./media/ui-tutorial-automobile-price-deploy/deployment-details.png)](./media/ui-tutorial-automobile-price-deploy/deployment-details.png#lightbox)

Te modele i wdrożenia można również znaleźć w sekcjach **modele** i **punkty końcowe** na [stronie docelowej obszaru roboczego (wersja zapoznawcza)](https://ml.azure.com).

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono podstawowe kroki tworzenia, wdrażania i zużywania modelu uczenia maszynowego w interfejsie wizualizacji. Aby dowiedzieć się więcej o sposobach rozwiązywania innych rodzajów problemów za pomocą interfejsu wizualnego, zobacz nasze inne Przykładowe eksperymenty.

> [!div class="nextstepaction"]
> [Przykład klasyfikacji ryzyka kredytowego](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
