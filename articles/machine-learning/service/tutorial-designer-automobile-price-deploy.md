---
title: 'Samouczek: Wdrażanie modelu uczenia maszynowego za pomocą narzędzia Projektant'
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak utworzyć rozwiązanie do analizy predykcyjnej w programie Azure Machine Learning Designer (wersja zapoznawcza). Uczenie, ocenę i wdrożenie modelu uczenia maszynowego przy użyciu modułów przeciągania i upuszczania.
author: peterclu
ms.author: peterlu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: 724a38cb516e5689f817e9ddeaa867b17274971b
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/12/2019
ms.locfileid: "73932046"
---
# <a name="tutorial-deploy-a-machine-learning-model-with-the-designer-preview"></a>Samouczek: Wdrażanie modelu uczenia maszynowego za pomocą narzędzia Projektant (wersja zapoznawcza)
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-enterprise-sku.md)]

Można wdrożyć model predykcyjny opracowany w pierwszej części [samouczka](tutorial-designer-automobile-price-train-score.md) , aby dać innym osobom szansę korzystania z niego. W części pierwszej został przeszkolony model. Teraz można generować nowe prognozy na podstawie danych wejściowych użytkownika. W tej części samouczka zostaną wykonane następujące czynności:

> [!div class="checklist"]
> * Utwórz potok wnioskowania w czasie rzeczywistym.
> * Utwórz klaster inferencing.
> * Wdróż punkt końcowy w czasie rzeczywistym.
> * Przetestuj punkt końcowy w czasie rzeczywistym.

## <a name="prerequisites"></a>Wymagania wstępne

Wykonaj [jedną z części samouczka](tutorial-designer-automobile-price-train-score.md) , aby dowiedzieć się, jak szkolić i oceny model uczenia maszynowego w projektancie.

## <a name="create-a-real-time-inference-pipeline"></a>Tworzenie potoku w czasie rzeczywistym

Aby wdrożyć potok, należy najpierw skonwertować potok szkoleniowy do potoku w czasie rzeczywistym. Ten proces usuwa moduły szkoleniowe i dodaje dane wejściowe i wyjściowe dla żądań inferencing.

### <a name="create-a-real-time-inference-pipeline"></a>Tworzenie potoku w czasie rzeczywistym

1. Nad kanwą potoku wybierz pozycję **Utwórz potok wnioskowania** > **potoku w czasie rzeczywistym**.

    Potok powinien teraz wyglądać następująco: 

   ![Zrzut ekranu przedstawiający oczekiwaną konfigurację potoku po przygotowaniu go do wdrożenia](./media/ui-tutorial-automobile-price-deploy/real-time-inference-pipeline.png)

    Po wybraniu opcji **Utwórz potok wnioskowania**kilka rzeczy zostanie wykonanych:
    
    * Szkolony model jest przechowywany jako moduł **DataSet** w palecie modułów. Można go znaleźć w obszarze **Moje zestawy danych**.
    * Moduły szkoleniowe, takie jak **model uczenia** i **dane podzielone** , są usuwane.
    * Zapisany model przeszkolony zostanie dodany z powrotem do potoku.
    * Dodawane są moduły danych **wejściowych** i **usług** sieci Web. Te moduły pokazują, gdzie dane użytkownika są wprowadzane do modelu i gdzie są zwracane dane.

    > [!NOTE]
    > *Potok szkoleń* jest zapisywany na nowej karcie w górnej części kanwy potoku. Można go również znaleźć jako opublikowany potok w projektancie.
    >

1. Wybierz pozycję **Uruchom**, a następnie użyj tego samego elementu docelowego obliczeń i eksperymentu, który został użyty w części pierwszej.

1. Wybierz moduł **model oceny** .

1. W okienku właściwości wybierz pozycję **wyjściowe** > **Wizualizacja** , aby sprawdzić, czy model nadal działa. Można zobaczyć oryginalne dane wraz z przewidywaną ceną ("etykiety oceny").

1. Wybierz pozycję **Wdróż**.

## <a name="create-an-inferencing-cluster"></a>Tworzenie klastra inferencing

W wyświetlonym oknie dialogowym możesz wybrać dowolny z istniejących klastrów usługi Azure Kubernetes Service (AKS), aby wdrożyć model. Jeśli nie masz klastra AKS, wykonaj następujące kroki, aby go utworzyć.

1. Wybierz pozycję **obliczenia** w wyświetlonym oknie dialogowym, aby przejść do strony **obliczenia** .

1. Na Wstążce Nawigacja wybierz pozycję **klastry wnioskowania** >  **+ Nowy**.

    ![Zrzut ekranu przedstawiający sposób uzyskiwania do okienka nowy klaster wnioskowania](./media/ui-tutorial-automobile-price-deploy/new-inference-cluster.png)

1. W okienku klaster wnioskowania Skonfiguruj nową usługę Kubernetes.

1. Wprowadź *AKS-COMPUTE* dla **nazwy obliczeniowej**.
    
1. Wybierz region znajdujący się w pobliżu, który jest dostępny dla **regionu**.

1. Wybierz pozycję **Utwórz**.

    > [!NOTE]
    > Utworzenie nowej usługi AKS trwa około 15 minut. Stan aprowizacji można sprawdzić na stronie **klastry wnioskowania** .
    >

## <a name="deploy-the-real-time-endpoint"></a>Wdrażanie punktu końcowego w czasie rzeczywistym

Po zakończeniu aprowizacji usługi AKS Wróć do potoku inferencing w czasie rzeczywistym, aby zakończyć wdrażanie.

1. Wybierz pozycję **Wdróż** powyżej kanwy.

1. Wybierz pozycję **wdróż nowy punkt końcowy**w czasie rzeczywistym. 

1. Wybierz utworzony klaster AKS.

1. Wybierz pozycję **Wdróż**.

    ![Zrzut ekranu przedstawiający sposób konfigurowania nowego punktu końcowego w czasie rzeczywistym](./media/ui-tutorial-automobile-price-deploy/setup-endpoint.png)

    Powiadomienie o powodzeniu powyżej kanwy pojawia się po zakończeniu wdrażania. Może to potrwać kilka minut.

## <a name="test-the-real-time-endpoint"></a>Testowanie punktu końcowego w czasie rzeczywistym

Po zakończeniu wdrażania można testować punkt końcowy w czasie rzeczywistym, przechodząc do strony **punkty końcowe** .

1. Na stronie **punkty końcowe** Wybierz wdrożony punkt końcowy.

    ![Zrzut ekranu przedstawiający kartę punkty końcowe czasu rzeczywistego z wyróżnionym ostatnio utworzonym punktem końcowym](./media/ui-tutorial-automobile-price-deploy/endpoints.png)

1. Wybierz pozycję **Testuj**.

1. Możesz ręcznie wprowadzić dane testowe lub użyć autowypełnianych danych przykładowych, a następnie wybrać opcję **Testuj**.

    Portal przesyła żądanie testowe do punktu końcowego i wyświetla wyniki. Mimo że dla danych wejściowych jest generowana wartość ceny, nie jest ona używana do generowania wartości przewidywania.

    ![Zrzut ekranu przedstawiający sposób testowania punktu końcowego w czasie rzeczywistym z etykietą z oceną dla wyróżnionej ceny](./media/ui-tutorial-automobile-price-deploy/test-endpoint.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono najważniejsze kroki w temacie Tworzenie, wdrażanie i Używanie modelu uczenia maszynowego w projektancie. Aby dowiedzieć się więcej o sposobach rozwiązywania innych rodzajów problemów za pomocą projektanta, zobacz nasze inne przykładowe potoki.

> [!div class="nextstepaction"]
> [Przykład klasyfikacji ryzyka kredytowego](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
