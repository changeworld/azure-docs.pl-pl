---
title: Tworzenie klasyfikatora — Custom Vision Service
titlesuffix: Azure Cognitive Services
description: Dowiedz się, jak używać usługi Custom Vision Service do tworzenia klasyfikatorem, który można rozróżnia obiekty w fotografie.
services: cognitive-services
author: anrothMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: conceptual
ms.date: 05/02/2018
ms.author: anroth
ms.openlocfilehash: 998900e72511a95336e4a94289c794e2a8e59feb
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/19/2018
ms.locfileid: "46364250"
---
# <a name="how-to-build-a-classifier-with-custom-vision"></a>Jak tworzyć klasyfikatora z Custom Vision

Aby korzystać z usługi Custom Vision Service, możesz tworzyć klasyfikatora. W tym dokumencie Dowiedz się, jak tworzenie klasyfikatora za pośrednictwem przeglądarki sieci web.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skompilować klasyfikatora, musisz mieć:

- Nieprawidłowy [konta Microsoft](https://account.microsoft.com/account) lub Azure Active Directory identyfikatorem organizacji ("konto służbowe"), dzięki czemu może zalogować się do customvision.ai i rozpocząć pracę.

    > [!IMPORTANT] 
    > Zaloguj się identyfikatorem organizacji dla użytkowników usługi Azure Active Directory (Azure AD) z [chmur krajowych](https://www.microsoft.com/en-us/trustcenter/cloudservices/nationalcloud) nie jest obecnie obsługiwane.

- Seria obrazów, aby uczyć klasyfikatory (z co najmniej 30 obrazy na tag).

- Kilka obrazów do testowania klasyfikatora po skonfigurowanych pod kątem klasyfikatora.

- Opcjonalnie: Subskrypcji platformy Azure skojarzone z Account Microsoft lub identyfikatorem organizacji. Jeśli nie masz subskrypcji platformy Azure, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed przystąpieniem do wykonywania.

    > [!IMPORTANT]
    > Bez subskrypcji platformy Azure, tylko będzie utworzyć __ograniczoną wersję próbną__ projektów. Jeśli masz subskrypcję platformy Azure, możesz zostanie wyświetlony monit o utworzenie Custom Vision Service uczenia i przewidywania zasobów w [witryny Azure portal](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision) podczas tworzenia projektu.   

## <a name="create-a-new-project"></a>Tworzenie nowego projektu

Aby utworzyć nowy projekt, użyj następujących kroków:

1. W przeglądarce internetowej przejdź do [strony sieci web Custom Vision](https://customvision.ai). Wybierz __Zaloguj__ aby rozpocząć korzystanie z usługi.

    ![Obraz strony logowania](./media/getting-started-build-a-classifier/custom-vision-web-ui.png)

    > [!NOTE]
    > Po zalogowaniu się do usługi Custom Vision Service, zostanie wyświetlona lista projektów. Poza dwa "ograniczony okres próbny" projektów testowych projekty są skojarzone z zasobu platformy Azure. Jeśli jesteś użytkownikiem platformy Azure, zobaczysz wszystkie projekty, które są skojarzone z [zasobów platformy Azure](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#grant-access-to-resources) do których masz dostęp. 

2. Aby utworzyć swój pierwszy projekt, wybierz **nowy projekt**. Twój pierwszy projekt prośba o zgodę na warunki użytkowania usługi. Zaznacz pole wyboru, a następnie wybierz **zgodę** przycisku. **Nowy projekt** pojawi się okno dialogowe.

    ![Okno dialogowe Nowy projekt ma pola na nazwę, opis i domen.](./media/getting-started-build-a-classifier/new-project.png)

3. Wprowadź nazwę i opis dla projektu. Następnie wybierz jedną z dostępnych domen. Każda domena optymalizuje klasyfikatora dla określonych typów obrazów, zgodnie z opisem w poniższej tabeli:

    |Domain|Przeznaczenie|
    |---|---|
    |__Ogólny__| Zoptymalizowane pod kątem szerokiego zakresu zadań klasyfikacji obrazów. Jeśli żaden z innych domen są odpowiednie lub wiesz, która domena, do wyboru, wybierz domenę ogólnego. |
    |__Żywności__|Zoptymalizowane pod kątem fotografie płytki, jak będą widoczne w menu restauracji. Do klasyfikowania fotografie poszczególne owoce lub warzyw, należy użyć domeny żywności.|
    |__Charakterystycznych elementów krajobrazu__|Zoptymalizowane pod kątem rozpoznawalnych charakterystycznych elementów krajobrazu, zarówno naturalnych, jak i sztucznych. Ta domena działa najlepiej, gdy charakterystycznych elementów krajobrazu jest widoczny w zdjęcia. Ta domena działa, nawet wtedy, gdy charakterystycznych elementów krajobrazu to nieco zakłócane przez osoby przed nim.|
    |__Handlu detalicznego__|Zoptymalizowane pod kątem obrazów, które znajdują się w katalogu zakupów lub zakupów witryna sieci Web. Jeśli chcesz się bardzo precyzyjnej klasyfikowania między sukienki, spodnie i koszule, należy użyć tej domeny.|
    |__Zawartość dla dorosłych__|Zoptymalizowana, aby dokładniej zdefiniować treści dla dorosłych i zawartości — zawartość dla dorosłych. Na przykład jeśli chcesz zablokować obrazów osób w kąpieli kolory tej domeny umożliwia tworzenie niestandardowych klasyfikatora, aby to zrobić.|
    |__Compact domen__| Zoptymalizowane pod kątem ograniczenia klasyfikacji w czasie rzeczywistym na urządzeniach przenośnych. Modele generowane przez compact domen można wyeksportować do uruchomienia lokalnie.|

    Domeny można zmienić później, jeśli chcesz.

4. Wybierz grupę zasobów. Lista rozwijana grupy zasobów są pokazywane wszystkie swoje grup zasobów platformy Azure, który zawiera zasób usługi Custom Vision. Można również utworzyć wybierz __ograniczoną wersję próbną__. Ograniczona wersja próbna wpis jest grupy tylko zasób, którą użytkownik spoza platformy Azure będą mogli wybierać.

    Aby utworzyć projekt, wybierz __Tworzenie projektu__.

## <a name="upload-and-tag-images"></a>Przekazywanie i tagować obrazy

1. Aby dodać obrazy do klasyfikatora, należy użyć __Dodawanie obrazów__ przycisk, a następnie wybierz pozycję __Przeglądaj pliki lokalne__. Wybierz __Otwórz__ można przenieść do znakowania.

    > [!TIP]
    > Po wybraniu obrazów, można oznaczyć je. Tag jest stosowany do grupy obrazów, które zostały wybrane do przekazywania, dlatego może być łatwiejsze do przekazywania obrazów według tagów, której planujesz używać. Można również zmienić tag dla wybranych obrazów po zostały oznaczone i przekazywane.

    > [!TIP]
    > Przekaż obrazy z kątów kamery różnych oświetlenia, tło, typów, style, grup, rozmiary, itp. Umożliwia upewnij się, że klasyfikatora nie jest obciążona również uogólnić różnych typów.

    Usługa Custom Vision Service akceptuje uczone obrazy w pliku jpg, PNG i formacie bmp do 6 MB na obrazie. (Prognozowania obrazów może być maksymalnie 4 MB na obrazie). Zaleca się obrazy były 256 pikseli na krawędzi najkrótszy. Wszystkie obrazy, które są mniej niż 256 pikseli na krawędzi najkrótszej są skalowane w górę usługi Custom Vision Service.

    ![Dodaj formant obrazów jest wyświetlany w lewym górnym rogu, a także jako przycisk na dole na środku.](./media/getting-started-build-a-classifier/add-images01.png)

    >[!NOTE] 
    > Interfejs API REST może służyć do załadowania uczone obrazy z adresów URL.

2. Aby ustawić tag, wpisz tekst w __Moje znaczniki__ pola, a następnie użyj __+__ przycisku. Aby przekazać obrazy i oznaczyć je, użyj __przekazywanie plików [liczba]__ przycisku. Możesz dodać więcej niż jeden tag do obrazów. 

    > [!NOTE]
    > Czas przekazywania jest zależna od liczby i rozmiaru obrazów, który wybrano.

    ![Obraz przedstawiający stronę tagów i przekazywania](./media/getting-started-build-a-classifier/add-images03.png)

3. Wybierz __gotowe__ po przekazaniu plików obrazów.

    ![Pasek postępu pokazuje wszystkie zadania zostały zakończone.](./media/getting-started-build-a-classifier/add-images04.png)

4. Aby przekazać inny zestaw obrazów, wróć do kroku 1. Na przykład chcąc rozróżnienie między psów i kucyków, przekazywanie i tagować obrazy kuców.

## <a name="train-and-evaluate-the-classifier"></a>Nauczanie i ocena klasyfikatora

To w opracowywaniu klasyfikatora, wybierz **szkolenie** przycisku.

![Przycisk train znajduje się w prawej górnej części okna przeglądarki.](./media/getting-started-build-a-classifier/train01.png)

Trwa tylko kilka minut, to w opracowywaniu klasyfikatora. W tym czasie zostaną wyświetlone informacje o procesie szkolenia.

![Przycisk train znajduje się w prawej górnej części okna przeglądarki.](./media/getting-started-build-a-classifier/train02.png)

Po szkoleniu __wydajności__ jest wyświetlana. Wskaźniki dokładności i wycofaniu zorientować się, jak dobra klasyfikatora na którym opiera testowanie automatyczne. Usługa Custom Vision Service przy użyciu obrazów, które przesłane do szkolenia obliczyć te liczby przy użyciu procesu o nazwie [składanie k krzyżowego sprawdzania poprawności](https://en.wikipedia.org/wiki/Cross-validation_(statistics)).

![Wyniki szkolenia pokazują ogólną dokładności i odwołania, a dokładność i odwołania dla każdego znacznika w klasyfikatora.](./media/getting-started-build-a-classifier/train03.png)

> [!NOTE] 
> Podczas wybierania **Train** przycisku, utworzysz nową iterację klasyfikatora. Możesz wyświetlić wszystkie iteracje stare w **wydajności** kartę, a można usuwać wszystkie, które mogą być nieaktualne. Po usunięciu iteracji na końcu usunięcie wszelkich obrazów, które jednoznacznie powiązanych z nim.

Klasyfikator używa wszystkich obrazów w celu utworzenia modelu, który identyfikuje każdego znacznika. Aby przetestować jakość modelu, klasyfikatora próbuje każdego obrazu na podstawie modelu, aby zobaczyć, co znajdzie modelu.

Jakość wyników klasyfikatora są wyświetlane.

|Termin|Definicja|
|---|---|
|__Precyzja__|Podczas klasyfikowania obrazu, jak prawdopodobnie jest klasyfikatory poprawnie klasyfikowania obrazów? Poza wszystkie obrazy, które są używane do trenowania klasyfikatora (psów i kuców) jaki procent czy modelu wyświetlony poprawne? 99 prawidłowe tagi poza 100 obrazów zapewnia dokładność 99%.|
|__Odwołania__|Poza wszystkie obrazy, które powinny mieć został poprawnie klasyfikowane ile klasyfikatora zidentyfikować prawidłowo? Odwoływanie 100% oznacza, że w przypadku 38 pies obrazów w obrazach, które były używane do trenowania klasyfikatora, klasyfikatora znaleźć psy 38.|

## <a name="next-steps"></a>Kolejne kroki

[Testowanie i ponowne trenowanie modelu](test-your-model.md)

