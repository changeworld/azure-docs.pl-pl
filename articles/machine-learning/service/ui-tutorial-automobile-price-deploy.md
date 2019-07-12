---
title: 'Samouczek: Wdrażanie modelu za pomocą interfejsu wizualnego uczenia maszynowego'
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak tworzyć rozwiązania do analizy predykcyjnej w interfejsie visual usługi Azure Machine Learning. Szkolenie, wynik, wdrożyć model uczenia maszynowego przy użyciu przeciągania i upuszczania modułów. Niniejszy samouczek jest drugą częścią serii legalną dwuczęściową na Prognozowanie cen samochodów przy użyciu regresji liniowej.
author: peterclu
ms.author: peterlu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 07/11/2019
ms.openlocfilehash: dd28fb51a4fc3fbf3dfc893f2f5f159ccafdb4b3
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67839302"
---
# <a name="tutorial-deploy-a-machine-learning-model-with-the-visual-interface"></a>Samouczek: Wdrażanie modelu za pomocą interfejsu wizualnego uczenia maszynowego

Udostępniania szansę, aby używać modelu predykcyjnego, opracowane w [część pierwsza samouczka](ui-tutorial-automobile-price-train-score.md), można go wdrożyć jako usługę sieci web platformy Azure. Do tej pory były zmieniane za pomocą szkolenia modelu. Teraz nadszedł czas, aby wygenerować nowy prognozy na podstawie danych wejściowych użytkownika. W tej części samouczka możesz:

> [!div class="checklist"]
> * Przygotowanie modelu wdrożenia
> * Wdrażanie usługi internetowej
> * Test usługi sieci web
> * Zarządzanie usługą sieci web
> * Korzystanie z usługi sieci web

## <a name="prerequisites"></a>Wymagania wstępne

Pełne [część pierwsza samouczka](ui-tutorial-automobile-price-train-score.md) dowiesz się, jak szkolenie i ocenianie modeli uczenia maszynowego w interfejsie visual.

## <a name="prepare-for-deployment"></a>Przygotowanie do wdrożenia

Przed wdrożeniem eksperymentu jako usługę sieci web, najpierw trzeba przekonwertować swoje *eksperymentu szkolenia* do *eksperyment predykcyjny*.

1. Wybierz **tworzenie eksperymentów predykcyjne*** w dolnej części obszaru roboczego eksperymentu.

    ![Animowany obraz gif przedstawiający automatycznej konwersji eksperymentu szkolenia eksperyment predykcyjny](./media/ui-tutorial-automobile-price-deploy/deploy-web-service.gif)

    Po wybraniu **utworzyć eksperyment predykcyjny**, ma miejsce kilka rzeczy:
    
    * Uczony model jest przechowywany jako **Uczonego modelu** modułu w palecie modułów. Znajdziesz go w folderze **przeszkolone modele**.
    * Moduły, które zostały użyte do trenowania zostaną usunięte; w szczególności:
      * Trenowanie modelu
      * Dzielenie danych
      * Ocena modelu
    * Zapisane uczony model jest dodawany do eksperymentu.
    * **Dane wejściowe usługi w sieci Web** i **sieci Web usługi danych wyjściowych** moduły są dodawane. Te moduły zidentyfikować, gdzie danych użytkownik przejdzie modelu i gdzie dane są zwracane.

    **Eksperymentu szkolenia** nadal jest zapisywane w ramach nowej karty w górnej części obszaru roboczego eksperymentu.

1. Kliknij przycisk **Run** (Uruchom), aby uruchomić eksperyment

1. Wybierz dane wyjściowe **Score Model** modułu, a następnie wybierz pozycję **wyświetlanie wyników** Aby sprawdzić, model nadal działa. Widać, że oryginalne dane są wyświetlane, oraz przewidywany cen ("Labels oceniane").

Eksperyment powinien teraz wyglądać następująco:  

![Zrzut ekranu przedstawiający oczekiwanej konfiguracji eksperymentu po przygotowaniu go do wdrożenia](./media/ui-tutorial-automobile-price-deploy/predictive-graph.png)

## <a name="deploy-the-web-service"></a>Wdrażanie usługi sieci Web

1. Wybierz **wdrażanie usługi sieci Web** poniżej obszaru roboczego.

1. Wybierz **obliczenia docelowej** chcesz uruchomić usługę sieci web.

    Obecnie interfejs graficzny obsługuje tylko wdrażanie w usłudze Azure Kubernetes Service (AKS) obliczeniowych elementów docelowych. Można wybierać dostępne AKS obliczeniowych elementów docelowych w obszarze roboczym usługi uczenia maszynowego, lub skonfigurować nowe środowisko usługi AKS przy użyciu okna dialogowego, który pojawia się.

    ![Zrzut ekranu przedstawiający możliwych konfiguracji nowemu celowi obliczenia](./media/ui-tutorial-automobile-price-deploy/deploy-compute.png)

1. Wybierz **wdrażanie usługi sieci Web**. Po zakończeniu wdrażania zostanie wyświetlone następujące powiadomienie. Wdrożenie może potrwać kilka minut.

    ![Zrzut ekranu przedstawiający komunikat potwierdzający pomyślne wdrożenie.](./media/ui-tutorial-automobile-price-deploy/deploy-succeed.png)

## <a name="test-the-web-service"></a>Test usługi sieci web

Można przetestować i zarządzania usługami sieci web interfejs graficzny, przechodząc do **usług sieci Web** kartę.

1. Przejdź do sekcji usługi sieci web. Zostaną wyświetlone wdrożone z nazwą usługi sieci web **— samouczek — prognozowania cen samochodów [Exp predykcyjne]** .

     ![Zrzut ekranu przedstawiający kartę usługi sieci web z usługą internetową ostatnio utworzone wyróżniony](./media/ui-tutorial-automobile-price-deploy/web-services.png)

1. Wybierz nazwę usługi sieci web w celu wyświetlenia dodatkowych szczegółów.

     ![Wyświetl zrzut ekranu przedstawiający dodatkowe szczegóły dostępne w usłudze sieci web](./media/ui-tutorial-automobile-price-deploy/web-service-details.png)

1. Wybierz **testu**.

    ![Zrzut ekranu przedstawiający przetestować stronę usługi sieci web](./media/ui-tutorial-automobile-price-deploy/web-service-test.png)

1. Dane wejściowe testowanie danych lub użyj autofilled przykładowych danych i wybierz polecenie **testu**.

    Żądanie testu jest przesyłany do usługi sieci web, a wyniki są wyświetlane na stronie. Mimo że wartość cena jest generowany dla danych wejściowych, nie jest używany do generowania wartości prognozy.

## <a name="consume-the-web-service"></a>Korzystanie z usługi sieci web

Użytkownicy mogą teraz wysyłać żądań interfejsu API usługi sieci web platformy Azure i otrzymywać wyniki, aby przewidzieć cenę ich nowych samochodów.

**Żądanie/odpowiedź** — użytkownik wysyła co najmniej jeden wiersz danych samochodów w usłudze przy użyciu protokołu HTTP. Usługa odpowiada za pomocą co najmniej jeden zestaw wyników.

Można znaleźć przykładowy wywołuje REST **zużywania** kartę strony szczegółów usługi sieci web.

   ![Zrzut ekranu przedstawiający przykładowy REST wywołania, że użytkownicy mogą znaleźć na karcie wykorzystania](./media/ui-tutorial-automobile-price-deploy/web-service-consume.png)

Przejdź do **dokumentacji interfejsu API** kartę, aby uzyskać więcej szczegółów interfejsu API.

  ![Zrzut ekranu przedstawiający dodatkowe szczegóły interfejsu API przez użytkowników można znaleźć na karcie dokumentacji interfejsu API](./media/ui-tutorial-automobile-price-deploy/web-service-api.png)

## <a name="manage-models-and-deployments"></a>Zarządzanie modelami i wdrożeń

Modele i wdrożeń usług internetowych, utworzonej za pomocą wizualnego interfejsu można też zarządzać w obszarze roboczym usługi Azure Machine Learning.

1. Otwieranie obszaru roboczego w [witryny Azure portal](https://portal.azure.com/).  

1. W obszarze roboczym, wybierz **modeli**. Następnie wybierz eksperyment, który został utworzony.

    ![Zrzut ekranu pokazujący sposób przejścia do eksperymentów w witrynie Azure portal](./media/ui-tutorial-automobile-price-deploy/portal-models.png)

    Na tej stronie zostanie wyświetlona dodatkowe szczegóły dotyczące modelu.

    ![Zrzut ekranu przedstawiający Przegląd statystyk eksperymentu w witrynie Azure portal](./media/ui-tutorial-automobile-price-deploy/model-details.png)

1. Wybierz **wdrożeń**, zostanie wyświetlona lista wszelkich usług sieci web korzystające z modelu. Wybierz nazwę usługi sieci web, przejść do strony szczegółów usługi sieci web. Na tej stronie można uzyskać bardziej szczegółowe informacje, które usługi sieci web.

    ![Zrzut ekranu szczegółowe Uruchom raport](./media/ui-tutorial-automobile-price-deploy/deployment-details.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono kluczowe kroki tworzenia, wdrażania i używania usługi machine learning model w interfejs graficzny. Aby dowiedzieć się więcej na temat wykorzystania interfejs graficzny rozwiązać inne rodzaje problemów, zapoznaj się naszych przykładowych eksperymentów.

> [!div class="nextstepaction"]
> [Przykładowe klasyfikację ryzyka środków](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
