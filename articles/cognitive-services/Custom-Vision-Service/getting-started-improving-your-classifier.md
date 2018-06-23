---
title: Ulepszyć Twoje klasyfikatora przy użyciu usługi wizji niestandardowe - kognitywnych usług Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak poprawić jakość klasyfikatora z usługi wizji niestandardowe.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: article
ms.date: 05/03/2018
ms.author: anroth
ms.openlocfilehash: 65b1424f259066c7d5bd6b2b508d2a4052ff0527
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347865"
---
# <a name="how-to-improve-your-classifier"></a>Ulepszenia klasyfikatora programu

Dowiedz się, jak poprawić jakość klasyfikatora z usługi wizji niestandardowe. Jakość Twojej klasyfikatora jest zależna od jakości danych oznaczonych zapewnienia do niego. 

## <a name="train-more-varied-images"></a>Szkolenie bardziej różnorodnych obrazów

Dzięki oznakowanych obrazów z różnymi kątami, tła, rozmiar obiektu, grupy zdjęć i inne wariantów poprawia klasyfikatora. Zdjęć w kontekście są lepsze niż zdjęć przed neutralne tła. Obejmują obrazy, które są przedstawiciela co będzie można przesłać do klasyfikatora podczas normalnego użytkowania.

Aby uzyskać więcej informacji o dodawaniu obrazów, zobacz [kompilacji klasyfikatora](getting-started-build-a-classifier.md) dokumentu.

> [!IMPORTANT]
> Pamiętaj, aby uczenia Klasyfikator po dodaniu obrazów.

## <a name="use-images-submitted-for-prediction"></a>Obrazy do prognozowania

Usługi wizji niestandardowe są przechowywane obrazy przesłane do punktu końcowego prognozowania. Aby użyć tych obrazów do poprawy klasyfikatora, wykonaj następujące kroki:

1. Aby wyświetlić obrazy przesłane do klasyfikatora, otwórz [wizji niestandardowe strony sieci web](https://customvision.ai) i wybierz __prognoz__ kartę.

    ![Obraz na karcie prognoz](./media/getting-started-improving-your-classifier/predictions-tab.png)

    > [!TIP]
    > Widok domyślny pokazuje obrazów z bieżącej iteracji. Można użyć __iteracji__ listy rozwijanej pola, aby wyświetlić obrazy przesłane podczas poprzedniej iteracji.

2. Umieść kursor nad obraz, aby zobaczyć tagi, które zostały przewidzieć klasyfikatora.

    > [!TIP]
    > Obrazy są klasyfikowane, tak aby obrazów, które można przełączyć większości korzyści klasyfikatora znajdowały się u góry. Aby wybrać inną sortowanie, użyj __sortowania__ sekcji.

    Aby dodać obraz do danych szkoleniowych, wybierz obraz, zaznacz tag, a następnie wybierz __Zapisz i Zamknij__. Obraz jest usuwany z __prognoz__ i dodać do obrazów szkolenia. Możesz je wyświetlić, wybierając __obrazów szkolenia__ kartę.

    ![Obraz strony znaczników](./media/getting-started-improving-your-classifier/tag-image.png)

3. Użyj __pociągu__ przycisk, aby ponownie ucz klasyfikatora.

## <a name="visually-inspect-predictions"></a>Wizualne sprawdzić prognoz

Aby sprawdzić prognoz obrazu, wybierz __obrazów szkolenia__ karcie, a następnie wybierz __historii iteracji__. Obrazy z czerwonym prostokątem zostały niepoprawnie przewidzieć.

![Obraz historii iteracji](./media/getting-started-improving-your-classifier/iteration-history.png)

Czasami visual inspekcji można zidentyfikować wzorce, które następnie można usunąć, dodając szkolenia dodatkowych danych. Na przykład klasyfikatora róże a daises mogą niepoprawnie etykietą wszystkie białe róże daises. Można rozwiązać ten problem, dodając i dostarczania danych szkoleniowych zawiera obrazy oznakowanych róż białe.

## <a name="unexpected-classification"></a>Nieoczekiwany klasyfikacji

Czasami Klasyfikator uzyskuje informacje o właściwości, które są wspólne dla obrazów. Na przykład chcesz utworzyć klasyfikatora róże a tulipanów. Należy podać obrazy tulipanów pól i róże w czerwonym wazon przed niebieski tablicy. Biorąc pod uwagę te dane, klasyfikator może uczenia dla pola a wall + wazon zamiast róże a tulipanów.

Aby rozwiązać ten problem, należy użyć wskazówki na szkolenia więcej zróżnicowanych obrazów: Podaj obrazów z różnymi kątami, tło rozmiar obiektu, grup i innych elementach Variant.

## <a name="negative-image-handling"></a>Obsługa ujemna obrazu

Usługa wizji niestandardowe obsługuje niektóre obsługi automatycznego ujemna obrazów. Jeśli tworzysz cat a dog klasyfikatora, przesyłanie obrazu butów do przewidywania klasyfikatora powinien wynik tego obrazu jako 0% dla cat i dog. 

> [!WARNING]
> Automatyczne rozwiązanie działa w przypadku obrazów wyraźnie ujemna. Nie może działać również w przypadku obrazów ujemna odmianą obrazy używane w szkolenia. 
>
> Na przykład jeśli masz husky a klasyfikatora corgi i źródła w obrazie Pomeranian, jego może wynik Pomeranian jako Husky. W przypadku obrazów ujemna tego rodzaju, utworzenia nowego tagu (na przykład "inne") i zastosować je do obrazów ujemna szkolenia.

## <a name="next-steps"></a>Kolejne kroki

[Użyj prognozowania interfejsu API](use-prediction-api.md)