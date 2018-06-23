---
title: Tworzenie klasyfikatora z usługą wizji niestandardowe - kognitywnych usług Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać usługi wizji niestandardowe utworzenie klasyfikatora, który można wykrycia obiektów w fotografie.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: article
ms.date: 05/02/2018
ms.author: anroth
ms.openlocfilehash: 6dc271c13f53a445c7d1101f5264d890208bd03c
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347889"
---
# <a name="how-to-build-a-classifier-with-custom-vision"></a>Jak tworzyć klasyfikatora z wizji niestandardowe

Do korzystania z usługi wizji niestandardowe, należy najpierw utworzyć klasyfikatora. W tym dokumencie informacje o sposobie tworzenia klasyfikatora za pośrednictwem przeglądarki sieci web.

## <a name="prerequisites"></a>Wymagania wstępne

Aby utworzyć klasyfikatora, musisz najpierw mieć:

- Prawidłowy [konta Microsoft](https://account.microsoft.com/account) lub Azure Active Directory OrgID ("konto służbowe"), dzięki czemu można zalogować się do customvision.ai i rozpocząć pracę.

    > [!IMPORTANT] 
    > Logowanie OrgID dla użytkowników usługi Azure Active Directory (Azure AD) z [national chmur](https://www.microsoft.com/en-us/trustcenter/cloudservices/nationalcloud) nie jest obecnie obsługiwany.

- Serie obrazów w celu przeszkolenia Twojej klasyfikatora (z co najmniej 30 obrazów na tag).

- Kilka obrazów do testowania programu klasyfikatora, po przygotowaniu klasyfikatora.

- Opcjonalnie: Subskrypcji platformy Azure skojarzone z Account Microsoft lub OrgID. Jeśli nie masz subskrypcji platformy Azure, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

    > [!IMPORTANT]
    > Bez subskrypcji platformy Azure, tylko będzie mogła tworzyć __ograniczona wersja próbna__ projektów. Jeśli masz subskrypcję platformy Azure, pojawi się monit utworzyć niestandardowe wizji usługi szkolenia i prognozowania zasobów w [portalu Azure](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision) podczas tworzenia projektu.   

## <a name="create-a-new-project"></a>Tworzenie nowego projektu

Aby utworzyć nowy projekt, użyj następujących kroków:

1. W przeglądarce sieci web, przejdź do [wizji niestandardowe strony sieci web](https://customvision.ai). Wybierz __Zaloguj__ aby rozpocząć korzystanie z usługi.

    ![Obraz strony logowania](./media/getting-started-build-a-classifier/custom-vision-web-ui.png)

    > [!NOTE]
    > Po zalogowaniu w usłudze wizji niestandardowe są prezentowane z listy projektów. Poza dwóch "ograniczona wersja próbna" projekty do testowania projekty są skojarzone z zasobów Azure. Jeśli jesteś użytkownikiem usługi Azure, zobaczysz wszystkie projekty, które są skojarzone z [zasobów Azure](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#grant-access-to-resources) do których masz dostęp. 

2. Aby utworzyć pierwszy projekt, wybierz **nowy projekt**. W przypadku pierwszego projektu zostanie wyświetlona prośba o zgodę na warunki użytkowania. Zaznacz pole wyboru, a następnie wybierz **zgadzam się** przycisku. **Nowy projekt** zostanie wyświetlone okno dialogowe.

    ![Okno dialogowe Nowy projekt ma pól na nazwę, opis i domen.](./media/getting-started-build-a-classifier/new-project.png)

3. Wprowadź nazwę i opis dla projektu. Następnie wybierz jedną z dostępnych domen. Każda domena optymalizuje Klasyfikator dla określonych typów obrazów, zgodnie z opisem w poniższej tabeli:

    |Domena|Przeznaczenie|
    |---|---|
    |__Ogólny__| Zoptymalizowana pod kątem szeroką gamę zadań klasyfikacji obrazu. Jeśli żaden z innych domen nie jest odpowiedni, lub nie wiesz, która domena, aby wybrać, wybierz domenę ogólnego. |
    |__Żywności__|Zoptymalizowana pod kątem fotografie stosie będzie wyświetlanych w menu w restauracji. Do klasyfikowania fotografie poszczególnych owoców lub warzyw, należy użyć domeny żywności.|
    |__Punkty orientacyjne__|Zoptymalizowana pod kątem rozpoznawalnych punkty orientacyjne, fizyczne i sztucznych. Ta domena działa najlepiej, gdy punkty jest widoczny w zdjęcie. Ta domena działa nawet wtedy, gdy punkty nieco jest zablokowane przez osoby przed nim.|
    |__Handlowe__|Zoptymalizowana pod kątem obrazów, które znajdują się w katalogu zakupów lub zakupów witryna sieci Web. Klasyfikowanie wysokiej precyzji między sukienki, spodnie i koszule, należy użyć tej domeny.|
    |__Dla dorosłych__|Zoptymalizowane, aby dokładniej zdefiniować zawartość dla dorosłych i zawartości z systemem innym niż osoba dorosła. Na przykład jeśli chcesz zablokować obrazów osób w kąpieliskach kolory tej domeny umożliwia tworzenie niestandardowych klasyfikatora w tym.|
    |__Compact domen__| Zoptymalizowana pod kątem ograniczenia klasyfikacji w czasie rzeczywistym na urządzeniach przenośnych. Modele generowane przez compact domeny mogą być eksportowane do uruchomienia lokalnie.|

    Domeny można zmienić później, jeśli chcesz.

4. Wybierz grupę zasobów. Lista rozwijana grupy zasobów pokazuje wszystkie grupy zasobów Azure obejmujących zasób niestandardowy wizji usługi. Można również utworzyć wybierz __ograniczona wersja próbna__. Ograniczone wpis wersji próbnej jest grupa zasobów tylko użytkowników innych niż Azure będzie można wybrać.

    Aby utworzyć projekt, wybierz __Tworzenie projektu__.

## <a name="upload-and-tag-images"></a>Przekazywanie i tagu obrazów

1. Aby dodać obrazy do klasyfikatora, użyj __dodać obrazy__ przycisk, a następnie wybierz __Przeglądaj lokalne pliki__. Wybierz __Otwórz__ można przenieść do znakowania.

    > [!TIP]
    > Po wybraniu obrazów, możesz oznaczyć je. Zostanie zastosowany do grupy obrazów, które zostały wybrane do przekazania, dlatego może być łatwiejsze do przekazania obrazy według znaczników, który ma być używany. Można również zmienić tagu dla wybranych obrazów po ich oznakowane i przekazać.

    > [!TIP]
    > Przekazywanie obrazów za pomocą aparatu różnych kątów oświetlenia, tła, typów, style, grup, rozmiary, itp. Umożliwia Sprawdź, czy Twoje klasyfikatora nie jest ukierunkowane i może również generalize różnych typów.

    Niestandardowe usługi wizji akceptuje szkolenia obrazów JPG, PNG i formacie BMP 6 MB na obrazie. (Obrazy prognozowania może mieć maksymalnie 4 MB na obraz.) Zaleca się obrazy 256 pikseli na krawędzi najkrótszy. Wszystkie obrazy krótsze niż 256 pikseli na krawędzi najkrótszy są skalowane w górę niestandardowe wizji usługi.

    ![Dodaj formant obrazów jest wyświetlany w lewym górnym rogu, jak i u dołu center.](./media/getting-started-build-a-classifier/add-images01.png)

    ![Przycisk przeglądania plików lokalnych jest wyświetlane u dołu center.](./media/getting-started-build-a-classifier/add-images02.png)

    >[!NOTE] 
    > Interfejs API REST może służyć do ładowania obrazów szkolenia z adresów URL.

2. Aby ustawić tagu, wprowadź tekst w __Moje znaczniki__ pola, a następnie użyj __+__ przycisku. Aby przekazać obrazów i oznaczyć je, użyj __przekazywania plików [numer]__ przycisku. Do obrazów, można dodać więcej niż jeden tag. 

    > [!NOTE]
    > Czas przekazywania zależy od liczby i rozmiaru obrazów, który wybrano.

    ![Obraz strony tagu i przekazywania](./media/getting-started-build-a-classifier/add-images03.png)

3. Wybierz __zrobić__ po obrazy zostały przekazane.

    ![Pasek postępu pokazuje wszystkie czynności zostały wykonane.](./media/getting-started-build-a-classifier/add-images04.png)

4. Aby przekazać innego zestawu obrazów, wróć do kroku 1. Na przykład aby odróżnić psów kuców, przekazywanie i obrazy kuców tagu.

## <a name="train-and-evaluate-the-classifier"></a>Nauczanie i Ewaluacja klasyfikatora

W celu przeszkolenia klasyfikatora, wybierz **uczenia** przycisku.

![Przycisk pociągu jest w prawej górnej części okna przeglądarki.](./media/getting-started-build-a-classifier/train01.png)

Potrwa to tylko kilka minut w celu przeszkolenia klasyfikatora. W tym czasie zostaną wyświetlone informacje o procesie szkolenia.

![Przycisk pociągu jest w prawej górnej części okna przeglądarki.](./media/getting-started-build-a-classifier/train02.png)

Po szkolenia, __wydajności__ jest wyświetlany. Wskaźniki dokładność i odwołania informujące, jaka Twojej klasyfikatora na którym opiera się testowanie automatyczne. Niestandardowe wizji usługa korzysta z obrazów, które przesłane szkolenia do obliczenia te wartości przy użyciu procesu wywoływanych [k składanie krzyżowego sprawdzania poprawności](https://en.wikipedia.org/wiki/Cross-validation_(statistics)).

![Wyniki szkolenia Pokaż ogólną dokładność i odwołania, a dokładność i odwołaj dla każdego znacznika w klasyfikatorze.](./media/getting-started-build-a-classifier/train03.png)

> [!NOTE] 
> Podczas wybierania **pociągu** przycisku, Utwórz nową iterację Twojej klasyfikatora. Można wyświetlić wszystkie iteracje starego w **wydajności** żadnego, które mogą być nieaktualne usunąć kartę, a. Po usunięciu iteracji przechodzili usuwanie żadnych obrazów, które jednoznacznie powiązanych z nim.

Klasyfikator używa wszystkich obrazów w celu utworzenia modelu, który identyfikuje tagami. Aby przetestować jakość modelu, Klasyfikator próbuje każdego obrazu na modelu na model znajduje się w temacie.

Jakość wyników klasyfikatora są wyświetlane.

|Termin|Definicja|
|---|---|
|__Dokładność__|Podczas klasyfikowania obrazu, jak prawdopodobnie jest Twoje klasyfikatora poprawnie klasyfikowania obrazu? Poza wszystkie obrazy, które są używane w celu przeszkolenia klasyfikatora (psów i kucyków) jaki procent czy modelu wyświetlony poprawna? 99 tagi poprawne poza 100 obrazów zapewnia dokładność 99%.|
|__Odwołania__|Poza wszystkich obrazów, które powinny mieć zostały poprawnie klasyfikowane ile może Twoje klasyfikatora ustalić poprawnie? Odwołania 100% oznacza, że jeśli występują 38 obrazy dog obrazów, które były używane w celu przeszkolenia klasyfikatora, Klasyfikator odnalezionych psów 38.|

## <a name="next-steps"></a>Kolejne kroki

[Testowanie i ponownie ucz modelu](test-your-model.md)

