---
title: 'Samouczek: Wdrażanie modelu uczenia maszynowego z projektantem'
titleSuffix: Azure Machine Learning
description: W tym samouczku pokazano, jak utworzyć rozwiązanie analizy predykcyjnej w projektancie usługi Azure Machine Learning (wersja zapoznawcza). Trenuj, oceniaj i wdrażaj model uczenia maszynowego przy użyciu modułów przeciągania i upuszczania.
author: peterclu
ms.author: peterlu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: c3ca37fd47b6551a95f9a491053ec7863acd1eeb
ms.sourcegitcommit: 0553a8b2f255184d544ab231b231f45caf7bbbb0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2020
ms.locfileid: "80389396"
---
# <a name="tutorial-deploy-a-machine-learning-model-with-the-designer-preview"></a>Samouczek: Wdrażanie modelu uczenia maszynowego z projektantem (wersja zapoznawcza)
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

Można wdrożyć model predykcyjny opracowany w [części pierwsza samouczka,](tutorial-designer-automobile-price-train-score.md) aby dać innym szansę na jego użycie. W części pierwsze, trenowałeś swój model. Teraz nadszedł czas, aby wygenerować nowe prognozy na podstawie danych wejściowych użytkownika. W tej części samouczka zostaną wykonane następujące czynności:

> [!div class="checklist"]
> * Tworzenie potoku wnioskowania w czasie rzeczywistym.
> * Tworzenie klastra wnioskowania.
> * Wdrażanie punktu końcowego w czasie rzeczywistym.
> * Przetestuj punkt końcowy w czasie rzeczywistym.

## <a name="prerequisites"></a>Wymagania wstępne

Ukończ [część pierwsza samouczka,](tutorial-designer-automobile-price-train-score.md) aby dowiedzieć się, jak trenować i oceniać model uczenia maszynowego w projektancie.

## <a name="create-a-real-time-inference-pipeline"></a>Tworzenie potoku wnioskowania w czasie rzeczywistym

Aby wdrożyć potok, należy najpierw przekonwertować potok szkolenia w potoku wnioskowania w czasie rzeczywistym. Ten proces usuwa moduły szkoleniowe i dodaje dane wejściowe i wyjściowe usługi sieci web do obsługi żądań.

### <a name="create-a-real-time-inference-pipeline"></a>Tworzenie potoku wnioskowania w czasie rzeczywistym

1. Nad kanwą potoku wybierz pozycję **Utwórz potok** > wnioskowania w**czasie rzeczywistym.**

    ![Zrzut ekranu przedstawiający, gdzie można znaleźć przycisk utwórz potok](./media/tutorial-designer-automobile-price-deploy/tutorial2-create-inference-pipeline.png)

    Potok powinien teraz wyglądać następująco: 

   ![Zrzut ekranu przedstawiający oczekiwaną konfigurację potoku po przygotowaniu go do wdrożenia](./media/tutorial-designer-automobile-price-deploy/real-time-inference-pipeline.png)

    Po wybraniu opcji **Utwórz potok wnioskowania**dzieje się kilka rzeczy:
    
    * Przeszkolony model jest przechowywany jako moduł **zestawu danych** w palecie modułów. Można go znaleźć w obszarze **Moje zestawy danych**.
    * Moduły szkoleniowe, takie jak **model pociągu** i **dane podzielone,** są usuwane.
    * Zapisany model przeszkolony jest dodawany z powrotem do potoku.
    * **Dodano moduły wprowadzania i** **wyjścia usługi sieci Web.** Te moduły pokazują, gdzie dane użytkownika wprowadza potok i gdzie dane są zwracane.

    > [!NOTE]
    > Domyślnie **dane wejściowe usługi sieci Web** będą oczekiwać tego samego schematu danych, co dane szkoleniowe używane do tworzenia potoku predykcyjnego. W tym scenariuszu cena jest uwzględniona w schemacie. Jednak cena nie jest używany jako czynnik podczas przewidywania.
    >

1. Wybierz **opcję Prześlij**i użyj tego samego celu obliczeniowego i eksperymentu, który został użyty w części 1.

    Jeśli jest to pierwsze uruchomienie, może upłynąć do 20 minut, aby zakończyć uruchamianie potoku. Domyślne ustawienia obliczeniowe mają minimalny rozmiar węzła 0, co oznacza, że projektant musi przydzielić zasoby po bezczynności. Powtarzające się przebiegi potoku zajmie mniej czasu, ponieważ zasoby obliczeniowe są już przydzielone. Ponadto projektant używa buforowanych wyników dla każdego modułu, aby jeszcze bardziej zwiększyć wydajność.

1. Wybierz pozycję **Wdróż**.

## <a name="create-an-inferencing-cluster"></a>Tworzenie klastra wnioskowania

W wyświetlonym oknie dialogowym można wybrać z dowolnego istniejącego klastra usługi Azure Kubernetes Service (AKS), aby wdrożyć model. Jeśli nie masz klastra AKS, użyj następujących kroków, aby go utworzyć.

1. Wybierz **pozycję Oblicz** w oknie dialogowym, które wydaje się przechodzić do strony **Obliczenia.**

1. Na wstążce nawigacji wybierz pozycję **Klastry wnioskowania** > **+ Nowy**.

    ![Zrzut ekranu przedstawiający sposób dostania się do nowego okienka klastra wnioskowania](./media/tutorial-designer-automobile-price-deploy/new-inference-cluster.png)

1. W okienku klastra wnioskowania skonfiguruj nową usługę Kubernetes.

1. Wprowadź *aks-compute* dla **nazwy Obliczeń**.
    
1. Wybierz pobliski region, który jest dostępny dla **regionu**.

1. Wybierz **pozycję Utwórz**.

    > [!NOTE]
    > Utworzenie nowej usługi AKS zajmuje około 15 minut. Stan inicjowania obsługi administracyjnej można sprawdzić na stronie **Klastry wnioskowania.**
    >

## <a name="deploy-the-real-time-endpoint"></a>Wdrażanie punktu końcowego w czasie rzeczywistym

Po zakończeniu inicjowania obsługi administracyjnej usługi AKS wróć do potoku wnioskowania w czasie rzeczywistym, aby zakończyć wdrażanie.

1. Wybierz **pozycję Wdrażanie** nad kanwą.

1. Wybierz **pozycję Wdrażanie nowego punktu końcowego w czasie rzeczywistym**. 

1. Wybierz utworzony klaster AKS.

1. Wybierz pozycję **Wdróż**.

    ![Zrzut ekranu przedstawiający sposób konfigurowania nowego punktu końcowego w czasie rzeczywistym](./media/tutorial-designer-automobile-price-deploy/setup-endpoint.png)

    Powiadomienie o powodzenie nad kanwą pojawia się po zakończeniu wdrażania. Może to potrwać kilka minut.

## <a name="test-the-real-time-endpoint"></a>Testowanie punktu końcowego w czasie rzeczywistym

Po zakończeniu wdrażania można przetestować punkt końcowy w czasie rzeczywistym, przechodząc do punktu **końcowego** strony.

1. Na stronie **Punkty końcowe** wybierz wdrożony punkt końcowy.

    ![Zrzut ekranu przedstawiający kartę punktów końcowych w czasie rzeczywistym z wyróżnionym ostatnio utworzonym punktem końcowym](./media/tutorial-designer-automobile-price-deploy/endpoints.png)

1. Kliknij przycisk **Testuj**.

1. Można ręcznie wprowadzić dane testowe lub użyć automatycznie wypełnionych przykładowych danych i wybrać **opcję Testuj**.

    Portal przesyła żądanie testu do punktu końcowego i pokazuje wyniki. Mimo że wartość ceny jest generowana dla danych wejściowych, nie jest używana do generowania wartości przewidywania.

    ![Zrzut ekranu przedstawiający sposób testowania punktu końcowego w czasie rzeczywistym z wyróżnioną etykietą punktową pod kątem ceny](./media/tutorial-designer-automobile-price-deploy/test-endpoint.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Następne kroki

W tym samouczku poznaliście kluczowe kroki dotyczące tworzenia, wdrażania i korzystania z modelu uczenia maszynowego w projektancie. Aby dowiedzieć się więcej o tym, jak można użyć projektanta do rozwiązywania innych typów problemów, zobacz nasze inne przykładowe potoki.

> [!div class="nextstepaction"]
> [Przykłady projektanta](samples-designer.md)
