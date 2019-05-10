---
title: 'Samouczek: Wdrażanie modelu za pomocą interfejsu wizualnego uczenia maszynowego'
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak tworzyć rozwiązania do analizy predykcyjnej w interfejsie visual usługi Azure Machine Learning. Szkolenie, wynik, wdrożyć model uczenia maszynowego przy użyciu przeciągania i upuszczania modułów. Niniejszy samouczek jest drugą częścią serii legalną dwuczęściową na Prognozowanie cen samochodów przy użyciu regresji liniowej.
author: peterclu
ms.author: peterclu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 04/06/2019
ms.openlocfilehash: 5f29e3820416686b42167fa278c4b7d0f9a58f1f
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65190915"
---
# <a name="tutorial-deploy-a-machine-learning-model-with-the-visual-interface"></a>Samouczek: Wdrażanie modelu za pomocą interfejsu wizualnego uczenia maszynowego

W tym samouczku możesz Przyjrzyj się rozszerzone tworzenie rozwiązania do analizy predykcyjnej w interfejsie visual usługi Azure Machine Learning. Ten samouczek jest **drugą częścią dwuczęściowej serii samouczków**. W [część pierwsza samouczka](ui-tutorial-automobile-price-train-score.md), skonfigurowanych pod kątem, oceniane i ocenić modelu do prognozowania cen samochodów. W tej części samouczka możesz:

> [!div class="checklist"]
> * Przygotowanie modelu wdrożenia
> * Wdrażanie usługi internetowej
> * Test usługi sieci web
> * Zarządzanie usługą sieci web
> * Korzystanie z usługi sieci web

## <a name="prerequisites"></a>Wymagania wstępne

Wykonanie [pierwszej części samouczka](ui-tutorial-automobile-price-train-score.md).

## <a name="prepare-for-deployment"></a>Przygotowanie do wdrożenia

Udostępniania szansę, aby używać modelu predykcyjnego, opracowane w ramach tego samouczka, możesz go wdrożyć jako usługę sieci web platformy Azure.

Do tej pory były zmieniane za pomocą szkolenia modelu. Teraz nadszedł czas, aby wygenerować nowy prognozy na podstawie danych wejściowych użytkownika.

Przygotowanie do wdrożenia jest procesem dwuetapowym:  

1. Konwertuj *eksperymentu szkolenia* utworzonego w *eksperyment predykcyjny*
1. Wdrożyć eksperyment predykcyjny jako usługę sieci web

Warto najpierw utworzyć kopię eksperymentu, wybierając **Zapisz jako** w dolnej części obszaru roboczego eksperymentu.

### <a name="convert-the-training-experiment-to-a-predictive-experiment"></a>Konwertowanie eksperymentu szkolenia na eksperyment predykcyjny

Aby uzyskać ten model jest gotowe do wdrożenia, należy przekonwertować tego eksperymentu szkolenia na eksperyment predykcyjny. Obejmuje to zwykle trzy kroki:

1. Zapisywanie modelu już uczony i Zastąp moduły szkoleniowe
1. TRIM eksperymentu do usunięcia modułów, które były potrzebne tylko w przypadku szkolenia
1. Zdefiniuj, którym usługa sieci web będzie akceptować dane wejściowe i gdzie generuje dane wyjściowe

Te kroki można wykonać ręcznie lub można wybrać opcję **ustawić usługę sieci Web** w dolnej części obszaru roboczego eksperymentu do nich wykonywane automatycznie.

![Animowany obraz gif przedstawiający automatycznej konwersji eksperymentu szkolenia eksperyment predykcyjny](./media/ui-tutorial-automobile-price-deploy/deploy-web-service.gif)

Po wybraniu **ustawić usługę sieci Web**, ma miejsce kilka rzeczy:

* Uczony model jest konwertowany na pojedynczej **Uczonego modelu** modułu. Jest on przechowywany w palety modułów, z lewej strony obszaru roboczego eksperymentu. Znajdziesz go w folderze **przeszkolone modele**.
* Moduły, które zostały użyte do trenowania zostaną usunięte; w szczególności:
  * Trenowanie modelu
  * Dzielenie danych
  * Ocena modelu
* Zapisane uczony model jest ponownie dodane do eksperymentu
* **Dane wejściowe usługi w sieci Web** i **sieci Web usługi danych wyjściowych** moduły są dodawane. Te moduły zidentyfikować, gdzie przejdzie modelu danych użytkownika i gdzie dane są zwracane.

Widać, że eksperyment został zapisany w dwóch częściach na nowych kartach w górnej części obszaru roboczego eksperymentu. Oryginalny eksperymentu szkolenia znajduje się w karcie **eksperymentu szkolenia**, a nowo utworzony eksperyment predykcyjny podlega **eksperyment predykcyjny**. Eksperyment predykcyjny jest tym, który wdrożysz jako usługę internetową.

Eksperyment powinien teraz wyglądać następująco:  

![Zrzut ekranu przedstawiający oczekiwanej konfiguracji eksperymentu po przygotowaniu go do wdrożenia](./media/ui-tutorial-automobile-price-deploy/predictive-graph.png)

Uruchom eksperyment jeszcze jeden raz (wybierz **Uruchom**). Wybierz cel obliczeniowej, mają eksperyment do uruchomienia na w oknie podręcznym. Aby sprawdzić, model nadal działa, wybierz dane wyjściowe modułu Score Model, a następnie wybierz pozycję **wyświetlanie wyników**. Widać, że oryginalne dane są wyświetlane, oraz przewidywany cen ("Labels oceniane").

## <a name="deploy-the-web-service"></a>Wdrażanie usługi sieci Web

Aby wdrożyć nową usługę sieci web pochodzące z eksperymentu:

1. Wybierz **wdrażanie usługi sieci Web** poniżej obszaru roboczego.
1. Wybierz **obliczenia docelowej** chcesz uruchomić usługę sieci web.

    Obecnie interfejs graficzny obsługuje tylko wdrażanie w usłudze Azure Kubernetes Service (AKS) obliczeniowych elementów docelowych. Można wybierać dostępne AKS obliczeniowych elementów docelowych w obszarze roboczym usługi uczenia maszynowego, lub skonfigurować nowe środowisko usługi AKS przy użyciu okna dialogowego, który pojawia się.

    ![Zrzut ekranu przedstawiający możliwych konfiguracji nowemu celowi obliczenia](./media/ui-tutorial-automobile-price-deploy/deploy-compute.png)

1. Wybierz **wdrażanie usługi sieci Web**. Po zakończeniu wdrażania zostanie wyświetlone następujące powiadomienie. Wdrożenie może potrwać kilka minut.

    ![Zrzut ekranu przedstawiający komunikat potwierdzający pomyślne wdrożenie.](./media/ui-tutorial-automobile-price-deploy/deploy-succeed.png)

## <a name="test-the-web-service"></a>Test usługi sieci web

Dane wejściowe użytkownika wprowadza wdrożonym modelu za pomocą **sieci Web dane wejściowe usługi** modułu. Dane wejściowe są następnie oceniane w **Score Model** modułu. Sposobu po skonfigurowaniu eksperyment predykcyjny model oczekuje danych w tym samym formacie co oryginalnego zestawu danych dotyczących samochodów ceny. Na koniec wyniki są zwracane użytkownikowi za pośrednictwem **sieci Web usługi danych wyjściowych** modułu.

Usługi sieci web można sprawdzić na karcie usług sieci web w interfejs graficzny.

1. Przejdź do sekcji usługi sieci web. Zostaną wyświetlone wdrożone z nazwą usługi sieci web **— samouczek — prognozowania cen samochodów [Exp predykcyjne]**.

     ![Zrzut ekranu przedstawiający kartę usługi sieci web z usługą internetową ostatnio utworzone wyróżniony](./media/ui-tutorial-automobile-price-deploy/web-services.png)

1. Wybierz nazwę usługi sieci web w celu wyświetlenia dodatkowych szczegółów.

     ![Wyświetl zrzut ekranu przedstawiający dodatkowe szczegóły dostępne w usłudze sieci web](./media/ui-tutorial-automobile-price-deploy/web-service-details.png)

1. Wybierz **testu**.

    ![Zrzut ekranu przedstawiający przetestować stronę usługi sieci web](./media/ui-tutorial-automobile-price-deploy/web-service-test.png)

1. Dane wejściowe testowanie danych lub użyj autofilled przykładowych danych i wybierz polecenie **testu** u dołu. Żądanie testu jest przesyłany do usługi sieci web, a wyniki są wyświetlane na stronie.

## <a name="manage-the-web-service"></a>Zarządzanie usługą sieci web

Po wdrożeniu usługi sieci web, można zarządzać nim z **usług sieci Web** karcie interfejsu wizualnego.

Usługa sieci web można usunąć, wybierając **Usuń** na stronie szczegółów usługi sieci web.

   ![Zrzut ekranu przedstawiający lokalizację przycisk Usuń w sieci web usługi w dolnej części okna](./media/ui-tutorial-automobile-price-deploy/web-service-delete.png)

## <a name="consume-the-web-service"></a>Korzystanie z usługi sieci web

W poprzednich krokach w tym samouczku wdrożono model predykcyjny samochodów jako usługi sieci web platformy Azure. Teraz użytkownicy mogą wysyłać dane i otrzymywać wyniki za pośrednictwem interfejsu API REST.

**Żądanie/odpowiedź** — użytkownik wysyła co najmniej jeden wiersz danych samochodów w usłudze przy użyciu protokołu HTTP. Usługa odpowiada za pomocą co najmniej jeden zestaw wyników.

Można znaleźć przykładowy wywołuje REST **zużywania** kartę strony szczegółów usługi sieci web.

   ![Zrzut ekranu przedstawiający przykładowy REST wywołania, że użytkownicy mogą znaleźć na karcie wykorzystania](./media/ui-tutorial-automobile-price-deploy/web-service-consume.png)

Przejdź do **dokumentacji interfejsu API** kartę, aby uzyskać więcej szczegółów interfejsu API.

  ![Zrzut ekranu przedstawiający dodatkowe szczegóły interfejsu API przez użytkowników można znaleźć na karcie dokumentacji interfejsu API](./media/ui-tutorial-automobile-price-deploy/web-service-api.png)

## <a name="manage-models-and-deployments-in-azure-machine-learning-service-workspace"></a>Zarządzanie modelami i wdrożeń w obszarze roboczym usługi Azure Machine Learning

Modele i wdrożeń usług internetowych, utworzonej za pomocą wizualnego interfejsu można zarządzać z obszaru roboczego usługi Azure Machine Learning.

1. Otwieranie obszaru roboczego w [witryny Azure portal](https://portal.azure.com/).  

1. W obszarze roboczym, wybierz **modeli**. Następnie wybierz eksperyment, który został utworzony.

    ![Zrzut ekranu pokazujący sposób przejścia do eksperymentów w witrynie Azure portal](./media/ui-tutorial-automobile-price-deploy/portal-models.png)

    Na tej stronie zostanie wyświetlona dodatkowe szczegóły dotyczące modelu.

    ![Zrzut ekranu przedstawiający Przegląd statystyk eksperymentu w witrynie Azure portal](./media/ui-tutorial-automobile-price-deploy/model-details.png)

1. Wybierz **wdrożeń**, zostanie wyświetlona lista wszelkich usług sieci web korzystające z modelu. Wybierz nazwę usługi sieci web, przejść do strony szczegółów usługi sieci web. Na tej stronie można uzyskać bardziej szczegółowe informacje, które usługi sieci web.

    ![Zrzut ekranu szczegółowe Uruchom raport](./media/ui-tutorial-automobile-price-deploy/deployment-details.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku przedstawiono kluczowe kroki tworzenia, wdrażania i używania usługi machine learning model w interfejs graficzny. Aby dowiedzieć się więcej na temat wykorzystania interfejs graficzny rozwiązać inne rodzaje problemów, zapoznaj się z przykładowych eksperymentów.

> [!div class="nextstepaction"]
> [Przykładowe klasyfikację ryzyka środków](ui-sample-classification-predict-credit-risk-basic.md)
