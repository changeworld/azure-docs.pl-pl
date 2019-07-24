---
title: Przewodnik Szybki Start dotyczący tworzenia klasyfikatora Custom Vision Service
titlesuffix: Azure Cognitive Services
description: W tym przewodniku szybki start dowiesz się, jak utworzyć model klasyfikacji obrazów przy użyciu witryny sieci Web Custom Vision.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 07/12/2019
ms.author: anroth
ms.openlocfilehash: 748336dcea580cefaf7638c86c1466bf0c16a472
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2019
ms.locfileid: "68423563"
---
# <a name="quickstart-how-to-build-a-classifier-with-custom-vision"></a>Szybki start: Jak utworzyć klasyfikator z Custom Vision

W tym przewodniku szybki start dowiesz się, jak utworzyć klasyfikator za pomocą witryny sieci Web Custom Vision. Po utworzeniu modelu klasyfikatora można użyć usługi Custom Vision do klasyfikacji obrazów.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

- Zestaw obrazów, za pomocą których można szkolić klasyfikatora. Poniżej znajdują się porady dotyczące wybierania obrazów.

## <a name="create-custom-vision-resources-in-the-azure-portal"></a>Utwórz zasoby Custom Vision w Azure Portal

Aby korzystać z Custom Vision Service, należy utworzyć Custom Vision szkolenia i zasoby przewidywania w Azure Portal. Wypełnij okno dialogowe na stronie [tworzenie Custom Vision](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision) , aby utworzyć zasób szkoleniowy i predykcyjny. 

## <a name="create-a-new-project"></a>Tworzenie nowego projektu

W przeglądarce internetowej przejdź do [strony sieci web Custom Vision](https://customvision.ai) i wybierz pozycję __Zaloguj__. Zaloguj się przy użyciu tego samego konta, które zostało użyte do zalogowania się do Azure Portal.

![Obraz strony logowania](./media/browser-home.png)


1. Aby utworzyć swój pierwszy projekt, wybierz pozycję **Nowy projekt**. Zostanie wyświetlone okno dialogowe **Utwórz nowy projekt** .

    ![Okno dialogowe Nowy projekt zawiera pola nazw, opisów i domen.](./media/getting-started-build-a-classifier/new-project.png)

1. Wprowadź nazwę i opis projektu. Następnie wybierz grupę zasobów. Jeśli Twoje konto zalogowane jest skojarzone z kontem platformy Azure, na liście rozwijanej Grupa zasobów zostaną wyświetlone wszystkie grupy zasobów platformy Azure zawierające zasób Custom Vision Service. 

   > [!NOTE]
   > Jeśli grupa zasobów nie jest dostępna, upewnij się, że zalogowano się do [customvision.AI](https://customvision.ai) przy użyciu tego samego konta, które zostało użyte do zalogowania się do [Azure Portal](https://portal.azure.com/). Upewnij się również, że wybrano taki sam katalog w portalu Custom Vision jak katalog w Azure Portal, w którym znajdują się zasoby Custom Vision. W obu lokacjach możesz wybrać katalog z menu rozwijanego konto w prawym górnym rogu ekranu. 

1. Wybierz pozycję __Klasyfikacja__ w obszarze __typy projektów__. Następnie w obszarze __typy klasyfikacji__wybierz pozycję wieloetykietowe lub wieloklasowe, w zależności od przypadku użycia. Klasyfikacja wieloetykietowa stosuje dowolną liczbę tagów do obrazu (zero lub więcej), podczas gdy klasyfikacja wieloklasowa sortuje obrazy w pojedynczej kategorii (Każdy przesłany przez Ciebie obraz zostanie posortowany do najbardziej dopasowanego tagu). Jeśli chcesz, będziesz mieć możliwość późniejszego zmiany typu klasyfikacji.

1. Następnie wybierz jedną z dostępnych domen. Każda domena optymalizuje klasyfikatora dla określonych typów obrazów, zgodnie z opisem w poniższej tabeli. Jeśli chcesz, będziesz mieć możliwość późniejszej zmiany domeny.

    |Domain|Cel|
    |---|---|
    |__Ogólnego__| Optymalizacja pod kątem szerokiego zakresu zadań klasyfikacji obrazów. Jeśli żadna z pozostałych domen nie jest odpowiednia lub nie masz pewności, którą domenę wybrać, wybierz domenę generyczną. |
    |__Żywności__|Optymalizacja pod kątem zdjęć naczyń w postaci widocznej w menu restauracji. Jeśli chcesz sklasyfikować fotografie poszczególnych owoców lub warzyw, użyj domeny żywności.|
    |__Charakterystycznych elementów krajobrazu__|Optymalizacja pod kątem rozpoznawalnych terenów, zarówno naturalnych, jak i sztucznej. Ta domena działa najlepiej, gdy punkt orientacyjny jest jasno widoczny na zdjęciu. Ta domena działa nawet wtedy, gdy punkt orientacyjny jest nieco przesunięty przez osoby przed nim.|
    |__Stępują__|Optymalizacja pod kątem obrazów znajdujących się w katalogu zakupów lub witrynie internetowej do kupowania. Jeśli potrzebujesz wysokiej dokładności klasyfikowania między Dresses, Pants i koszulami, Użyj tej domeny.|
    |__Domeny kompaktowe__| Optymalizacja pod kątem ograniczeń klasyfikacji w czasie rzeczywistym na urządzeniach przenośnych. Modele generowane przez domeny kompaktowe mogą być eksportowane do lokalnego uruchamiania.|

1. Na koniec wybierz pozycję __Utwórz projekt__.

## <a name="choose-training-images"></a>Wybierz obrazy szkoleniowe

[!INCLUDE [choose training images](includes/choose-training-images.md)]

## <a name="upload-and-tag-images"></a>Przekazywanie i tagowanie obrazów

W tej sekcji zostaną przesłane i ręcznie oznakowane obrazy, aby ułatwić uczenie klasyfikatora. 

1. Aby dodać obrazy, kliknij przycisk __Dodaj obrazy__ , a następnie wybierz __Przeglądaj pliki lokalne__. Wybierz pozycję __Otwórz__ , aby przejść do tagowania. Wybór tagu zostanie zastosowany do całej grupy obrazów wybranych do przekazania, dzięki czemu można łatwiej przekazywać obrazy w oddzielnych grupach zgodnie z odpowiednimi tagami. Możesz również zmienić Tagi dla poszczególnych obrazów po ich przekazaniu.

    ![Kontrolka Dodaj obrazy jest pokazywana w lewym górnym rogu i jako przycisk w dolnej części.](./media/getting-started-build-a-classifier/add-images01.png)


1. Aby utworzyć tag, wprowadź tekst w polu __Moje Tagi__ i naciśnij klawisz ENTER. Jeśli tag już istnieje, pojawi się w menu rozwijanym. W projekcie z wieloma etykietami można dodać więcej niż jeden tag do obrazów, ale w projekcie wieloklasowym można dodać tylko jedną. Aby zakończyć przekazywanie obrazów, użyj przycisku __Przekaż pliki [Number]__ . 

    ![Obraz przedstawiający tag i stronę przekazywania](./media/getting-started-build-a-classifier/add-images03.png)

1. Po przekazaniu obrazów wybierz opcję __gotowe__ .

    ![Pasek postępu pokazuje wszystkie zadania ukończone.](./media/getting-started-build-a-classifier/add-images04.png)

Aby przekazać inny zestaw obrazów, Wróć do początku tej sekcji i powtórz te kroki.

## <a name="train-the-classifier"></a>Szkolenie klasyfikatora

Aby nauczyć klasyfikatora, wybierz przycisk **poszkol** . Klasyfikator korzysta ze wszystkich bieżących obrazów, aby utworzyć model, który identyfikuje cechy wizualne poszczególnych tagów.

![Przycisk uczenia w prawym górnym rogu paska narzędzi nagłówka strony sieci Web](./media/getting-started-build-a-classifier/train01.png)

Proces szkolenia powinien trwać tylko kilka minut. W tym czasie informacje o procesie szkolenia są wyświetlane na karcie **wydajność** .

![Okno przeglądarki z oknem dialogowym szkoleń w sekcji głównej](./media/getting-started-build-a-classifier/train02.png)

## <a name="evaluate-the-classifier"></a>Oceń klasyfikator

Po zakończeniu szkolenia jest szacowany i wyświetlany jego wydajność. W Custom Vision Service są używane obrazy przesłane do szkolenia w celu obliczenia precyzji i odwołania przy użyciu procesu o nazwie [k-złóż krzyżowego sprawdzania poprawności](https://en.wikipedia.org/wiki/Cross-validation_(statistics)). Precyzja i odwoływanie to dwa różne pomiary skuteczności klasyfikatora:

- **Precyzja** wskazuje ułamek zidentyfikowanych klasyfikacji, które były poprawne. Na przykład jeśli model zidentyfikował 100 obrazów jako psy, a 99 z nich rzeczywiście miało psy, dokładność będzie 99%.
- **Odwołanie** wskazuje ułamek rzeczywistych klasyfikacji, które zostały prawidłowo zidentyfikowane. Na przykład jeśli w rzeczywistości istniało 100 obrazów z jabłek, a model zidentyfikowano 80 jako jabłka, przywoływanie będzie 80%.

![Wyniki szkolenia przedstawiają ogólną precyzję i odwołanie oraz precyzję i odwołania dla każdego znacznika w klasyfikatorze.](./media/getting-started-build-a-classifier/train03.png)

### <a name="probability-threshold"></a>Próg prawdopodobieństwa

[!INCLUDE [probability threshold](includes/probability-threshold.md)]

## <a name="manage-training-iterations"></a>Zarządzanie iteracjami szkoleń

Za każdym razem, gdy nauczysz klasyfikator, tworzysz nową _iterację_ ze swoimi zaktualizowanymi metrykami wydajności. Wszystkie iteracje można wyświetlić w lewym okienku na karcie **wydajność** . W lewym okienku znajdziesz również przycisk **Usuń** , którego można użyć, aby usunąć iterację, jeśli jest ona przestarzała. Po usunięciu iteracji usuwane są wszystkie obrazy, które są w unikatowy sposób skojarzone.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start przedstawiono sposób tworzenia i uczenia modelu klasyfikacji obrazów przy użyciu witryny sieci Web Custom Vision. Następnie uzyskaj więcej informacji na temat procesu iteracyjnego ulepszania modelu.

> [!div class="nextstepaction"]
> [Testowanie i ponowne szkolenie modelu](test-your-model.md)

