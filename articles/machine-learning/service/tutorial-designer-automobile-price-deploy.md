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
ms.openlocfilehash: 69fba508eac4b778dcd72371fd1471625ecb8c1a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73501603"
---
# <a name="tutorial-deploy-a-machine-learning-model-with-the-designer-preview"></a>Samouczek: Wdrażanie modelu uczenia maszynowego za pomocą narzędzia Projektant (wersja zapoznawcza)
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-enterprise-sku.md)]

Aby umożliwić innym użytkownikom korzystanie z modelu predykcyjnego opracowanego w pierwszej części tego [samouczka](tutorial-designer-automobile-price-train-score.md), możesz wdrożyć go jako punkt końcowy w czasie rzeczywistym. W części 1 został przeszkolony model. Teraz można generować nowe prognozy na podstawie danych wejściowych użytkownika. W tej części samouczka:

> [!div class="checklist"]
> * Wdrażanie punktu końcowego w czasie rzeczywistym
> * Tworzenie klastra inferencing
> * Testowanie punktu końcowego w czasie rzeczywistym

## <a name="prerequisites"></a>Wymagania wstępne

Wykonaj [jedną z części samouczka](tutorial-designer-automobile-price-train-score.md) , aby dowiedzieć się, jak szkolić i oceny model uczenia maszynowego w projektancie.

## <a name="deploy-a-real-time-endpoint"></a>Wdrażanie punktu końcowego w czasie rzeczywistym

Aby można było wdrożyć potok, należy:

1. Przekonwertuj potok szkoleniowy na potok wnioskowania w czasie rzeczywistym, który usuwa moduły szkoleniowe i dodaje dane wejściowe i wyjściowe dla żądań inferencing.
1. Wdróż potok wnioskowania.

### <a name="create-a-real-time-inference-pipeline"></a>Tworzenie potoku w czasie rzeczywistym

1. W górnej części kanwy potoku wybierz pozycję **Utwórz potok wnioskowania** > **potoku w czasie rzeczywistym**

    Po wybraniu opcji **Utwórz potok wnioskowania**kilka rzeczy zostanie wykonanych:
    
    * Szkolony model jest przechowywany jako moduł **DataSet** w palecie modułów. Można go znaleźć w obszarze **Moje zestawy danych**.
    * Moduły, takie jak **uczenie modelu** i **dane podzielone**, które zostały użyte do szkolenia, są usuwane.
    * Zapisany model przeszkolony zostanie dodany z powrotem do potoku.
    * Dodawane są moduły danych **wejściowych** i **usług** sieci Web. Te moduły identyfikują, gdzie dane użytkownika zostaną wprowadzone do modelu, a dane są zwracane.

    > [!Note]
    > **Potok szkoleń** jest zapisywany na nowej karcie w górnej części kanwy potoku. Można go również znaleźć jako opublikowany potok w projektancie.
    >

    Potok powinien teraz wyglądać następująco:  

   ![Zrzut ekranu przedstawiający oczekiwaną konfigurację potoku po przygotowaniu go do wdrożenia](./media/ui-tutorial-automobile-price-deploy/real-time-inference-pipeline.png)

1. Wybierz pozycję **Uruchom** i Użyj tego samego elementu docelowego obliczeń i eksperymentu użytego w części 1.

1. Wybierz moduł **model oceny** .

1. W okienku właściwości wybierz pozycję **wyjściowe** > **Wizualizacja** , aby sprawdzić, czy model nadal działa. Można zobaczyć oryginalne dane wraz z przewidywaną ceną ("etykiety oceny").

1. Wybierz pozycję **Wdróż**.

### <a name="create-an-inferencing-cluster"></a>Tworzenie klastra inferencing

W wyświetlonym oknie dialogowym możesz wybrać z istniejących klastrów usługi Azure Kubernetes Service (AKS) w obszarze roboczym, aby wdrożyć model. Jeśli nie masz klastra AKS, wykonaj następujące kroki, aby go utworzyć.

1. W oknie dialogowym wybierz pozycję **obliczenia** , aby przejść do strony **obliczenia** .

1. Na Wstążce Nawigacja wybierz pozycję **klastry wnioskowania** >  **+ Nowy**.

    ![Zrzut ekranu przedstawiający sposób przejścia do okienka nowy klaster wnioskowania](./media/ui-tutorial-automobile-price-deploy/new-inference-cluster.png)

1. W okienku klaster wnioskowania Skonfiguruj nową usługę Kubernetes.

1. Wprowadź wartość "AKS-COMPUTE" dla **nazwy obliczeniowej**.
    
1. Wybierz dostępny **region**w pobliżu.

1. Wybierz pozycję **Utwórz**.

    > [!Note]
    > Utworzenie nowej usługi AKS trwa około 15 minut. Stan aprowizacji można sprawdzić na stronie **klastrów wnioskowania**
    >

### <a name="deploy-the-real-time-endpoint"></a>Wdrażanie punktu końcowego w czasie rzeczywistym

Po zakończeniu aprowizacji usługi AKS Wróć do potoku inferencing w czasie rzeczywistym, aby zakończyć wdrażanie.

1. Wybierz pozycję **Wdróż** powyżej kanwy.

1. Wybierz pozycję **wdróż nowy punkt końcowy**w czasie rzeczywistym. 

1. Wybierz utworzony klaster AKS.

1. Wybierz pozycję **Wdróż**.

    ![Zrzut ekranu przedstawiający sposób konfigurowania nowego punktu końcowego w czasie rzeczywistym](./media/ui-tutorial-automobile-price-deploy/setup-endpoint.png)

    Powiadomienie o powodzeniu nad kanwą zostanie wyświetlone po zakończeniu wdrażania. może to potrwać kilka minut.

## <a name="test-the-real-time-endpoint"></a>Testowanie punktu końcowego w czasie rzeczywistym

Punkt końcowy w czasie rzeczywistym można przetestować, przechodząc do strony **punkty końcowe** w okienku nawigacji obszaru roboczego po lewej stronie.

1. Na stronie **punkty końcowe** Wybierz wdrożony punkt końcowy.

    ![Zrzut ekranu przedstawiający kartę punkty końcowe czasu rzeczywistego z wyróżnionym ostatnio utworzonym punktem końcowym](./media/ui-tutorial-automobile-price-deploy/endpoints.png)

1. Wybierz pozycję **Testuj**.

1. Dane wejściowe testowania lub użyj autowypełnianych danych przykładowych, a następnie wybierz **test**.

    Żądanie testowe zostanie przesłane do punktu końcowego, a wyniki są wyświetlane na stronie. Mimo że dla danych wejściowych jest generowana wartość ceny, nie jest ona używana do generowania wartości przewidywania.

    ![Zrzut ekranu przedstawiający sposób testowania punktu końcowego w czasie rzeczywistym z etykietą z oceną dla wyróżnionej ceny](./media/ui-tutorial-automobile-price-deploy/test-endpoint.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono kluczowe kroki tworzenia, wdrażania i zużywania modelu uczenia maszynowego w projektancie. Aby dowiedzieć się więcej o sposobach rozwiązywania innych rodzajów problemów za pomocą projektanta, zobacz nasze inne przykładowe potoki.

> [!div class="nextstepaction"]
> [Przykład klasyfikacji ryzyka kredytowego](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
