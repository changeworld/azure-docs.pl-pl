---
title: Tworzenie klasyfikatora — Custom Vision Service
titlesuffix: Azure Cognitive Services
description: Dowiedz się, jak utworzyć model klasyfikacji obrazów za pomocą witryny sieci Web Custom Vision.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: anroth
ms.openlocfilehash: d0f0f3b120187a7538989f219876a8c10569a98e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60606356"
---
# <a name="how-to-build-a-classifier-with-custom-vision"></a>Jak tworzyć klasyfikatora z Custom Vision

Aby użyć usługi Custom Vision Service Klasyfikacja obrazów, należy utworzyć model klasyfikatora. W tym przewodniku dowiesz się, jak tworzyć klasyfikatora za pośrednictwem witryny internetowej Custom Vision.

## <a name="prerequisites"></a>Wymagania wstępne

- Ważnej subskrypcji platformy Azure. [Tworzenie konta usługi](https://azure.microsoft.com/free/) za darmo.
- Zestaw obrazów za pomocą którego ma zostać uczyć klasyfikatory. Poniżej znajdują się porady na temat wybierania obrazów.


## <a name="create-custom-vision-resources-in-the-azure-portal"></a>Tworzenie niestandardowego przetwarzania zasobów w witrynie Azure portal
Aby korzystać z usługi Custom Vision Service, konieczne będzie utworzenie Custom Vision uczenia i przewidywania zasobów w w [witryny Azure portal](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision). Spowoduje to utworzenie szkolenia i prognozowania zasobów. 

## <a name="create-a-new-project"></a>Tworzenie nowego projektu

W przeglądarce internetowej przejdź do [strony sieci web Custom Vision](https://customvision.ai) i wybierz __Zaloguj__. Zaloguj się przy użyciu tego samego konta, którego używasz do logowania się do witryny Azure portal.

![Obraz strony logowania](./media/browser-home.png)


1. Aby utworzyć swój pierwszy projekt, wybierz **nowy projekt**. **Tworzenie nowego projektu** zostanie wyświetlone okno dialogowe.

    ![Okno dialogowe Nowy projekt ma pola na nazwę, opis i domen.](./media/getting-started-build-a-classifier/new-project.png)

1. Wprowadź nazwę i opis dla projektu. Następnie wybierz grupę zasobów. Jeśli konto logowania jest skojarzony z kontem platformy Azure, lista rozwijana grupy zasobów spowoduje wyświetlenie wszystkich grup zasobów platformy Azure zawierające zasób usługi Custom Vision. 

   > [!NOTE]
   > Jeśli żadna grupa zasobów jest dostępny, sprawdź, czy po zalogowaniu do [customvision.ai](https://customvision.ai) z tego samego konta, jak używane do logowania się do [witryny Azure portal](https://portal.azure.com/). Ponadto, upewnij się, że wybrano ten sam "Directory" w portalu usługi Custom Vision jako katalog w witrynie Azure portal, gdzie znajdują się Twoje zasoby Custom Vision. W obu lokacjach katalogu można wybrać z rozwijanego menu konta w prawym górnym rogu ekranu. 

1. Wybierz __klasyfikacji__ w obszarze __typów projektów__. Następnie w obszarze __typy klasyfikacji__, wybierają **Multilabel** lub **kontra**, w zależności od danego przypadku użycia. Dowolna liczba tagów w multilabel klasyfikacja ma zastosowanie do obrazu (zero lub więcej), podczas gdy klasyfikacji wieloklasowej sortuje obrazy w jednej kategorii (każdego obrazu, który prześlesz zostaną posortowane w najprawdopodobniej tag). Będzie można później zmienić typ klasyfikacji, w razie potrzeby.

1. Następnie wybierz jedną z dostępnych domen. Każda domena optymalizuje klasyfikatora dla określonych typów obrazów, zgodnie z opisem w poniższej tabeli. Będzie można później zmienić domenę, w razie potrzeby.

    |Domain|Przeznaczenie|
    |---|---|
    |__Ogólny__| Zoptymalizowane pod kątem szerokiego zakresu zadań klasyfikacji obrazów. Jeśli żaden z innych domen są odpowiednie lub wiesz, która domena, do wyboru, wybierz domenę ogólnego. |
    |__Żywności__|Zoptymalizowane pod kątem fotografie płytki, jak będą widoczne w menu restauracji. Do klasyfikowania fotografie poszczególne owoce lub warzyw, należy użyć domeny żywności.|
    |__Charakterystycznych elementów krajobrazu__|Zoptymalizowane pod kątem rozpoznawalnych charakterystycznych elementów krajobrazu, zarówno naturalnych, jak i sztucznych. Ta domena działa najlepiej, gdy charakterystycznych elementów krajobrazu jest widoczny w zdjęcia. Ta domena działa, nawet wtedy, gdy charakterystycznych elementów krajobrazu to nieco zakłócane przez osoby przed nim.|
    |__Handlu detalicznego__|Zoptymalizowane pod kątem obrazów, które znajdują się w katalogu zakupów lub zakupów witryna sieci Web. Jeśli chcesz się bardzo precyzyjnej klasyfikowania między sukienki, spodnie i koszule, należy użyć tej domeny.|
    |__Compact domen__| Zoptymalizowane pod kątem ograniczenia klasyfikacji w czasie rzeczywistym na urządzeniach przenośnych. Modele generowane przez compact domen można wyeksportować do uruchomienia lokalnie.|

1. Na koniec wybierz pozycję __Tworzenie projektu__.

## <a name="choose-training-images"></a>Wybierz uczone obrazy

Co najmniej zaleca się, że używasz co najmniej 30 obrazy na tagu w zestawie wstępne szkolenie. Należy również zbierać kilka dodatkowych obrazów do przetestowania modelu po jego przygotowaniu.

Aby skutecznie nauczenia modelu, należy używać obrazów z różnymi visual. Wybierz obrazów za pomocą którego różnią się zależnie od:
* Kąt kamery
* oświetlenia
* Tło
* Styl wizualny
* osoba/pogrupowane subject(s)
* rozmiar
* type

Dodatkowo należy upewnić się, że wszystkie Twoje uczone obrazy spełniają następujące kryteria:
* format JPG, PNG lub bmp
* nie większy niż rozmiar (4MB dla obrazów prognozowania) 6MB
* nie może być mniejsza niż 256 pikseli na krawędzi najkrótszy. wszystkie obrazy, które są mniej niż to będzie można automatycznie skalowany w górę przez usługi Custom Vision Service

## <a name="upload-and-tag-images"></a>Przekazywanie i tagowanie obrazów

W tej sekcji możesz przekazać i ręcznie tagować obrazy w celu nauczenia klasyfikatora. 

1. Aby dodać obrazy, kliknij __Dodawanie obrazów__ przycisk, a następnie wybierz pozycję __Przeglądaj pliki lokalne__. Wybierz __Otwórz__ można przenieść do znakowania. Znacznik wyboru będą stosowane do całej grupy obrazów, wybrane do przekazania, dzięki czemu łatwiej jest je przekazywać obrazy do oddzielnych grup zgodnie z ich odpowiednie tagi. Można również zmienić tagów dla poszczególnych obrazów po zostały przekazane.

    ![Dodaj formant obrazów jest wyświetlany w lewym górnym rogu, a także jako przycisk na dole na środku.](./media/getting-started-build-a-classifier/add-images01.png)


1. Aby utworzyć tag, wpisz tekst w __Moje znaczniki__ pola, a następnie naciśnij klawisz Enter. Jeśli tag już istnieje, zostanie wyświetlona w menu rozwijanym. W projekcie multilabel możesz dodać więcej niż jeden tag do obrazów, ale w wieloklasowej projektu można dodać tylko jeden. Aby zakończyć przekazywanie obrazów, użyj __przekazywanie plików [liczba]__ przycisku. 

    ![Obraz przedstawiający stronę tagów i przekazywania](./media/getting-started-build-a-classifier/add-images03.png)

1. Wybierz __gotowe__ po przekazaniu plików obrazów.

    ![Pasek postępu pokazuje wszystkie zadania zostały zakończone.](./media/getting-started-build-a-classifier/add-images04.png)

Aby przekazać inny zestaw obrazów, powrót do początku tej sekcji, a następnie powtórz kroki.

## <a name="train-the-classifier"></a>Szkolenie klasyfikatora

To w opracowywaniu klasyfikatora, wybierz **szkolenie** przycisku. Klasyfikator używa wszystkich bieżących obrazów do tworzenia modelu, który identyfikuje visual właściwości każdego znacznika.

![Szkolenie przycisk w prawym górnym rogu paska narzędzi Nagłówek strony sieci web](./media/getting-started-build-a-classifier/train01.png)

Proces szkolenia powinien potrwać kilka minut. W tym czasie zostaną wyświetlone informacje o procesie szkolenia w **wydajności** kartę.

![Okno przeglądarki, za pomocą okna dialogowego szkolenia w sekcji głównej](./media/getting-started-build-a-classifier/train02.png)

## <a name="evaluate-the-classifier"></a>Oceń klasyfikatora

Po zakończeniu szkolenia wydajności modelu szacowania i wyświetlane. Custom Vision Service przy użyciu obrazów, które przesłane dla szkoleń do obliczania dokładności i odwołania, za pomocą procesu o nazwie [składanie k krzyżowego sprawdzania poprawności](https://en.wikipedia.org/wiki/Cross-validation_(statistics)). Dokładność i odwołania są dwa różne pomiary skuteczności klasyfikatora:

- **Dokładność** wskazuje ułamek zidentyfikowanych klasyfikacje, które były prawidłowe. Na przykład jeśli model zidentyfikowane 100 obrazów jako psy, a 99 z nich są faktycznie psy, dokładność będzie 99%.
- **Odwołaj** wskazuje ułamek rzeczywiste klasyfikacje, które zostały poprawnie zidentyfikowane. Na przykład jeśli wystąpiły faktycznie 100 obrazów jabłek i modelu zidentyfikowane 80 jako jabłek, odwołanie będzie 80%.

![Wyniki szkolenia pokazują ogólną dokładności i odwołania, a dokładność i odwołania dla każdego znacznika w klasyfikatora.](./media/getting-started-build-a-classifier/train03.png)

### <a name="probability-threshold"></a>Próg prawdopodobieństwa

Uwaga **próg prawdopodobieństwa** suwaka w okienku po lewej stronie **wydajności** kartę. Wartość progowa dla przewidywane prawdopodobieństwa zostały uznane za prawidłowe podczas obliczania dokładności i odwołania.

Interpretowanie wywołań prognoz o progu wysokie prawdopodobieństwo ma tendencję do zwracania wyników z bardzo precyzyjnej kosztem odwołania (znaleziono klasyfikacji są prawidłowe, ale nie znaleziono wiele); Próg niewielkie prawdopodobieństwo ma odwrotne (znaleziono większości rzeczywistych klasyfikacji, ale istnieją fałszywych alarmów, w tym zestawie). Pamiętając o tym należy ustawić próg prawdopodobieństwa odpowiednio do potrzeb określonego projektu. Później po stronie klienta, należy używać tej samej wartości próg prawdopodobieństwa jako filtru podczas odbierania wyników przewidywań z modelu.

## <a name="manage-training-iterations"></a>Zarządzanie iteracjami szkolenia

Zawsze możesz uczyć klasyfikatory, możesz utworzyć nową _iteracji_ z własną metryki wydajności zaktualizowane. Można wyświetlić wszystkie swoje iteracje w lewym okienku **wydajności** kartę. W okienku po lewej stronie znajdziesz również **Usuń** przycisk, który służy do usuwania iteracji, jeśli jest przestarzały. Po usunięciu iteracji, możesz usunąć wszystkie obrazy, które jednoznacznie powiązanych z nim.

## <a name="next-steps"></a>Kolejne kroki

W tym przewodniku przedstawiono sposób tworzenie i uczenie model klasyfikacji obrazów, przetwarzania niestandardowe witryny sieci Web. Następnie Pobierz więcej informacji na temat proces iteracyjny poprawy modelu.

[Testowanie i ponowne szkolenie modelu](test-your-model.md)

