---
title: Experiments galerii Azure AI | Dokumentacja firmy Microsoft
description: Odnajdywanie i udostępniać eksperymenty w galerii Azure AI.
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cgronlun
ms.assetid: f4248922-c961-4d3a-9e1b-aec743210166
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2017
ms.openlocfilehash: d57e780fbf5187f2481d40bde28b113181c15b4a
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "34834662"
---
# <a name="discover-experiments-in-azure-ai-gallery"></a>Odnajdywanie eksperymenty w galerii Azure AI
[!INCLUDE [machine-learning-gallery-item-selector](../../../includes/machine-learning-gallery-item-selector.md)]

## <a name="experiments-for-machine-learning-studio"></a>Eksperymentów w usłudze Machine Learning Studio
Galeria zawiera szeroką gamę [eksperymenty](https://gallery.cortanaintelligence.com/experiments) zostały opracowane w [Azure Machine Learning Studio](https://studio.azureml.net). Zakres eksperymentów z szybkiego eksperymenty Weryfikacja koncepcji, które pokazują określonej maszyny uczenia chcemy pełni rozwinięte rozwiązań problemów learning złożonych maszyny.

> [!NOTE]
> ***Eksperymentu*** jest kanwy w usłudze Machine Learning Studio używanego w celu utworzenia modelu analizy predykcyjnej. Łączenie danych z różnych modułów analitycznych do tworzenia modelu. Można spróbuj różnych pomysły, czy działa w wersji próbnej i ostatecznie wdrożyć model jako usługę sieci web na platformie Azure. Na przykład sposobu tworzenia podstawowych eksperymentu zobacz [samouczek dotyczący uczenia maszynowego: Tworzenie pierwszego eksperymentu w usłudze Azure Machine Learning Studio](create-experiment.md). Na pełniejsze Przewodnik tworzenia rozwiązania analizy predykcyjnej, zobacz [wskazówki: tworzenie rozwiązania analizy predykcyjnej w celu oceny ryzyka kredytowego w usłudze Azure Machine Learning](walkthrough-develop-predictive-solution.md).
>
>

## <a name="discover"></a>Wykrywanie
Aby przeglądać eksperymenty [w galerii](http://gallery.cortanaintelligence.com), w górnej części strony głównej galerii, wybierz **eksperymenty**.

**[Eksperymenty](https://gallery.cortanaintelligence.com/experiments)** zostanie wyświetlona lista ostatnio dodany i popularne eksperymenty. Aby zobaczyć wszystkie eksperymenty, zaznacz **zobaczyć wszystkie** przycisku. Do wyszukiwania określonych eksperymentu, wybierz **zobaczyć wszystkie**, a następnie wybierz kryteria. Można również wprowadzić terminy wyszukiwania w **wyszukiwania** u góry strony galerii.

Możesz uzyskać więcej informacji na temat eksperyment na stronie szczegółów eksperymentu. Aby otworzyć stronę szczegółów eksperymentu, wybierz eksperymentu. W eksperymencie szczegóły strony w **komentarze** sekcji, można dodać komentarz, wyrazić swoją opinię lub zadać pytania dotyczące eksperymentu. Możesz nawet udostępniać eksperyment znajomych lub współpracowników w serwisie Twitter i LinkedIn. Można również e-mail łącze do strony szczegółów doświadczenia z zaproszeniem dla innych użytkowników, aby wyświetlić stronę.

![Udostępnij ten element znajomych](./media/gallery-how-to-use-contribute-publish/share-links.png)

![Dodaj komentarz własnych](./media/gallery-how-to-use-contribute-publish/comments.png)

## <a name="download"></a>Do pobrania
Do swojego obszaru roboczego usługi Machine Learning Studio, możesz pobrać kopię każdego doświadczenia w galerii. Następnie można zmodyfikować kopii programu do tworzenia własnych rozwiązań.

Galerii AI Azure oferuje można importować kopię eksperyment na dwa sposoby:

* **Z poziomu galerii**. Jeśli znajdziesz eksperymentu w galerii, możesz pobrać kopię, a następnie otwórz go w obszarze roboczym Machine Learning Studio.
* **Za pomocą uczenia maszynowego Studio**. W usłudze Machine Learning Studio służy każdego doświadczenia w galerii jako szablon, aby utworzyć nowy eksperyment.

### <a name="from-the-gallery"></a>Z galerii

1. W galerii Otwórz stronę szczegółów eksperymentu.
2. Wybierz **Otwórz w Studio**.

    ![Otwórz doświadczenia z galerii](./media/gallery-experiments/open-experiment-from-gallery.png)

Po wybraniu **Otwórz w Studio**, otwiera eksperymentu w obszarze roboczym Machine Learning Studio. (Jeśli jeszcze nie zalogowano Cię do usługi Machine Learning Studio, zostanie wyświetlony monit pierwsze logowanie przy użyciu konta Microsoft.)

### <a name="from-within-machine-learning-studio"></a>Za pomocą uczenia maszynowego Studio

1. W usłudze Machine Learning Studio, wybierz **nowy**.
2. Wybierz **eksperymentu**. Wybierz z listy eksperymenty w galerii lub znaleźć eksperyment określonego za pomocą **wyszukiwania** pole.
3. Wskaż myszy eksperymentu, a następnie wybierz **Otwórz w Studio**. (Aby wyświetlić informacje o eksperyment, wybierz **Wyświetl w galerii**. Powoduje to przejście do strony szczegółów eksperymentu w galerii.)

    ![Otwórz galerię Poeksperymentuj z wewnątrz usługi Machine Learning Studio](./media/gallery-experiments/open-experiment-from-studio.png)

Można dostosować, iteracji i wdrożyć pobrany eksperymentu, podobnie jak inne eksperymentu, które są tworzone w usłudze Machine Learning Studio.

![Eksperymentu otwarty w Studio](./media/gallery-experiments/experiment-open-in-studio.png)

## <a name="contribute"></a>Współtworzenia
Po zalogowaniu się do galerii staje się członkami społeczności galerii. Jest członkiem społeczności może przyczynić się własne eksperymentów, aby inni użytkownicy mogą korzystać z rozwiązań, które zostały odnalezione.

### <a name="publish-your-experiment-to-the-gallery"></a>Publikowanie eksperymentu w galerii

1. Zaloguj się do usługi Machine Learning Studio za pomocą konta Microsoft.
2. Tworzenie eksperymentu, a następnie ją uruchom.
3. Jeśli wszystko jest gotowe do publikowania eksperymentu w galerii, na liście poniżej obszaru roboczego eksperymentu Akcje wybierz **publikowania do galerii**.

    ![Wybierz pozycję "Publikuj do galerii"](./media/gallery-experiments/publish-experiment-to-gallery.png)
4. Na **opis eksperymentu** wprowadź tytuł i tagów. Należy opisowy tytuł i tagów. Wyróżnij metod, które zostanie użyte lub rozwiązywanie rzeczywistych problemów. Na przykład eksperymentu opisowy tytuł "klasyfikacji binarnej: Twitter analizy wskaźniki nastrojów klientów."

    ![Wprowadź tytuł i tagi do publikowania](./media/gallery-experiments/experiment-description.png)
5. W **podsumowania** wprowadź podsumowanie eksperymentu. Zwięźle opisz eksperyment rozwiązuje problemu i jak złożoną.
6. W **szczegółowy opis** pozycję opisano kroki wykonane w każdej części eksperymentu. Niektóre tematy przydatne do uwzględnienia są:
   * Zrzut ekranu wykres eksperymentu
   * Wyjaśnienie i źródeł danych
   * Przetwarzanie danych
   * Inżynieria funkcji
   * Opis modelu
   * Wyniki i ocena wydajności modelu

   Można użyć języka znaczników markdown sformatować opis. Aby zobaczyć, jak będzie wyglądać wpisy na stronie z opisem eksperymentu eksperyment został opublikowany, zaznacz **Podgląd**.

   ![Wybierz opcję "Wersji zapoznawczej", aby zobaczyć, jak będzie wyglądać tekst](./media/gallery-experiments/preview-markdown-text.png)

   > [!TIP]
   > Pola tekstowe podana edycji markdown i Podgląd są niewielkie. Zaleca się, że zapisu dokumentacji eksperymentu w edytorze języka znaczników markdown, skopiuj go, a następnie wklej ukończone dokumentacji w polu tekstowym w galerii. Po opublikowaniu eksperymentu, użytkownik może wprowadzić poprawki przy użyciu standardowych narzędzi sieci web tego markdown używany do edycji i podglądu.

7. Na **wybór obrazu** wybierz obraz miniatury swojego eksperymentu. Obraz miniatury wyświetlany u góry strony szczegółów eksperymentu, w kafelku eksperymentu. Inni użytkownicy zobaczą obraz miniatury przeglądających galerię. Przekaż obraz z komputera lub Wybierz podstawowy obraz w galerii.
    </br>
    ![Przekazywanie lub wybrać obraz w galerii](./media/gallery-experiments/select-gallery-image.png)
8. Na **ustawienia** w obszarze **widoczność**, określ, czy do publikowania zawartości publicznie (**publicznego**) lub jest ona dostępna tylko dla osób, które mają łącze do strony ( **Nieznajdujące się na liście**).

    ![Wybierz, czy chcesz opublikować publicznie lub jako nieznajdujące się na liście](./media/gallery-experiments/choose-public-or-unlisted.png)

    <!-- -->

   > [!TIP]
   > Jeśli chcesz upewnij się, że w dokumentacji poprawne przed udostępnieniem publicznie, należy najpierw opublikować eksperymentu jako **Unlisted**. Później, możesz zmienić ustawienie widoczności do **publicznego** na stronie szczegółów eksperymentu.
   >
   >
9. Aby opublikować eksperymentu w galerii, wybierz **OK** znacznik wyboru.

    ![Wybierz znacznik wyboru OK, aby opublikować eksperymentu](./media/gallery-experiments/ok-checkmark.png)

Aby uzyskać wskazówki dotyczące sposobu publikowania eksperyment galerii wysokiej jakości, zobacz [porady dotyczące dokumentowania i publikowania eksperymentu](#tips-for-documenting-and-publishing-your-experiment).

To wszystko — wszystko gotowe.

Można teraz wyświetlić eksperymentu w galerii i udostępnić link. Jeśli publikowany eksperymentu przy użyciu **publicznego** widoczność ustawienie eksperyment zostaną wyświetlone w przeglądania i wyników wyszukiwania w galerii. Można edytować dokumentacji eksperymentu na stronie szczegółów eksperymentu zawsze, gdy użytkownik jest zalogowany do galerii.

Aby wyświetlić listę Twój wkład, wybierz obraz w prawym górnym rogu dowolnej strony w galerii. Następnie wybierz nazwę użytkownika, aby otworzyć strony swojego konta.

![Wybierz nazwę swojego konta](./media/gallery-experiments/click-account-name.png)

### <a name="update-your-experiment"></a>Zaktualizuj eksperymentu
Jeśli chcesz, możesz wprowadzić zmiany do przepływu pracy (modułów, parametry i tak dalej) w eksperymencie opublikowany w galerii. W usłudze Machine Learning Studio wprowadź wszelkie zmiany, które chcesz dokonać eksperymentu, a następnie opublikować ponownie. Opublikowane eksperymentu zostaną zaktualizowane o wprowadzone zmiany.

Dla eksperymentu bezpośrednio w galerii, można zmienić dowolne z poniższych informacji:

* Nazwa eksperymentu
* Podsumowanie lub opisu
* Tagi
* Image (Obraz)
* Ustawienie widoczności (**publicznego** lub **Unlisted**)

Można również usunąć doświadczenia z galerii.

Możesz wprowadzić te zmiany lub usunąć eksperymentu, na stronie szczegółów eksperymentu lub ze strony profilu w galerii.


#### <a name="from-the-experiment-details-page"></a>Na stronie szczegółów eksperymentu
Na stronie szczegółów eksperymentu, aby zmienić szczegóły eksperymentu, wybierz **Edytuj**.

![Wybierz pozycję Edytuj, aby edytować eksperymentu](./media/gallery-experiments/edit-button.png)

Na stronie Szczegóły przejdzie do trybu edycji. Aby wprowadzić zmiany, wybierz **Edytuj** obok nazwy eksperymentu, podsumowanie lub znaczników. Po zakończeniu wprowadzania zmiany wybierz **gotowe**.

![Wybierz pozycję "Edytuj", aby edytować szczegóły, a następnie wybierz pozycję "Gotowe", po zakończeniu](./media/gallery-experiments/edit-details-page.png)

Aby zmienić ustawienia widoczności dla eksperyment (**publicznego** lub **Unlisted**), lub aby usunąć doświadczenia z galerii, wybierz **ustawienia** ikony.

![Wybierz pozycję "Ustawienia", aby zmienić widoczność lub usunąć eksperymentu](./media/gallery-experiments/settings-button.png)

#### <a name="from-your-profile-page"></a>Ze strony profilu
Na stronie profilu wybierz strzałkę w dół do eksperymentu, a następnie wybierz **Edytuj**. Powoduje to przejście do strony szczegółów swojego eksperymentu w trybie edycji. Po zakończeniu wprowadzania zmiany wybierz **gotowe**.

Aby usunąć doświadczenia z galerii, wybierz **usunąć**.

![Wybierz opcję "Edytuj" lub "Delete"](./media/gallery-experiments/edit-delete-buttons.png)

### <a name="tips-for-documenting-and-publishing-your-experiment"></a>Porady dotyczące dokumentowania i publikowania eksperymentu
* Można założyć, że czytelnik ma wystąpić nauki poprzednich danych, ale może być przydatne do używania prostego języka. Wyjaśnień szczegółowo, jeśli to możliwe.
* Pakietu Cortana Intelligence Suite jest stosunkowo nowe. Nie wszystkie czytników znają jak z niego korzystać. Podaj wystarczającej ilości informacji i krok wyjaśnienia, aby ułatwić Przejdź eksperymentu.
* Elementy wizualne mogą być przydatne dla czytników interpretowania i korzystać z dokumentacją eksperymentu poprawnie. Elementy wizualne obejmują wykresów eksperymentów i zrzuty ekranu danych. Aby uzyskać więcej informacji o sposobie obejmują obrazy w dokumentacji eksperymentu, zobacz [przykłady i wskazówki dotyczące publikowania kolekcji](https://gallery.cortanaintelligence.com/Collection/Publishing-Guidelines-and-Examples-1).
* Jeśli dołączysz zestawu danych w eksperymencie (to znaczy nie importowany zestawu danych za pośrednictwem modułu importu danych), zestaw danych jest częścią eksperymentu i został opublikowany w galerii. Upewnij się, że zestaw danych, który można opublikować warunki, które umożliwiają udostępnianie i pobieranie wszystkim osobom licencjonowania. Galeria udziały są objęte Azure [warunki użytkowania](https://azure.microsoft.com/support/legal/website-terms-of-use/).

## <a name="frequently-asked-questions"></a>Często zadawane pytania
**Jakie są wymagania dotyczące przesyłania lub edytowanie obrazu dla moim eksperymencie?**

Obrazy przesyłane z eksperymencie są używane do tworzenia kafelka eksperymentu dla Ciebie. Zaleca się obrazy mniejszy niż 500 KB aspect ratio 3:2 i rozdzielczością 960 &#215; 640.

**Co się dzieje z zestawu danych używanych w eksperymencie? Jest zestaw danych także opublikowany w galerii?**

Zestaw danych jest częścią eksperymentu, jest on zaimportowany przez moduł importowanie danych w zestawie danych jest opublikowany w galerii w ramach eksperymentu. Upewnij się, że zestaw danych, która publikować z eksperymentu ma odpowiednie postanowień licencyjnych. Postanowienia licencyjne powinna zezwalać na każdy do udziału, a następnie Pobierz dane.

**Masz eksperymentu, która używa modułu importowanie danych do pobierania danych z usługi Azure HDInsight lub SQL Server. Używa moich poświadczeń do pobierania danych. Można opublikować tego rodzaju eksperymentu? Jak można mieć pewność, nie będzie można udostępniać moje poświadczenia?**

Obecnie nie można opublikować eksperymentu, który korzysta z poświadczeń w galerii.

**Jak wprowadzić wiele tagów?**

Po wprowadzeniu tag, aby wprowadzić innego tagu, naciśnij klawisz Tab.

**[Przejdź do galerii](http://gallery.cortanaintelligence.com)**

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]
