---
title: 'Szybki start: biblioteka klienta moderatora zawartości dla języka Python'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku Szybki start dowiedz się, jak rozpocząć pracę z biblioteką klienta moderatora zawartości usług Azure Cognitive Services dla języka Python.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: quickstart
ms.date: 01/27/2020
ms.author: pafarley
ms.openlocfilehash: 31cdc9663283b580acc10e7ac8d5a77f9036a7a8
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76772359"
---
# <a name="quickstart-content-moderator-client-library-for-python"></a>Szybki start: biblioteka klienta moderatora zawartości dla języka Python

Wprowadzenie do biblioteki klienta moderatora zawartości dla języka Python. Wykonaj następujące kroki, aby zainstalować pakiet i wypróbować przykładowy kod dla podstawowych zadań. Content Moderator to usługa kognitywna, która sprawdza zawartość tekstową, obrazową i wideo pod kątem materiałów potencjalnie obraźliwych, ryzykownych lub w inny sposób niepożądanych. W przypadku znalezienia takich treści usługa stosuje odpowiednie etykiety (flagi) do zawartości. Aplikacja może następnie obsłużyć oflagowaną zawartość w celu zachowania zgodności z przepisami lub zapewnienia odpowiedniego środowiska dla użytkowników.

Użyj biblioteki klienta moderatora zawartości dla języka Python, aby:

* [Moderowanie tekstu](#moderate-text)
* [Używanie listy terminów niestandardowych](#use-a-custom-terms-list)
* [Umiarkowane obrazy](#moderate-images)
* [Używanie niestandardowej listy obrazów](#use-a-custom-image-list)
* [Tworzenie recenzji](#create-a-review)

[Dokumentacja](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/contentmoderator?view=azure-python) | [referencyjna Przykłady pakietu kodu źródłowego](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-vision-contentmoderator) | [(PiPy)](https://pypi.org/project/azure-cognitiveservices-vision-contentmoderator/) | [biblioteki](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [utwórz bezpłatną subskrypcję platformy Azure](https://azure.microsoft.com/free/)
* [Python 3.x](https://www.python.org/)

## <a name="setting-up"></a>Konfigurowanie

### <a name="create-a-content-moderator-azure-resource"></a>Tworzenie zasobu platformy Azure moderatora zawartości

Usługi Azure Cognitive Services są reprezentowane przez zasoby platformy Azure, które subskrybujesz. Utwórz zasób dla moderatora zawartości przy użyciu [witryny Azure portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) lub [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) na komputerze lokalnym. Możesz również wykonać następujące czynności:

* Pobierz [klucz wersji próbnej](https://azure.microsoft.com/try/cognitive-services/#decision) ważny przez siedem dni za darmo. Po zarejestrowaniu się będzie on dostępny w [witrynie sieci Web platformy Azure.](https://azure.microsoft.com/try/cognitive-services/my-apis/)  
* Wyświetlanie zasobu w [witrynie Azure portal](https://portal.azure.com/)

Po otrzymasz klucz z subskrypcji próbnej lub zasobu, [utwórz zmienne środowiskowe](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) dla klucza i adresu URL punktu końcowego, odpowiednio `CONTENT_MODERATOR_SUBSCRIPTION_KEY` i `CONTENT_MODERATOR_ENDPOINT`, odpowiednio.
 
### <a name="create-a-python-script"></a>Tworzenie skryptu języka Python

Utwórz nowy skrypt języka Python i otwórz go w preferowanym edytorze lub IDE. Następnie dodaj `import` następujące instrukcje do górnej części pliku.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imports)]

Następnie utwórz zmienne dla lokalizacji punktu końcowego zasobu i klucz jako zmienne środowiskowe. 

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_vars)]

> [!NOTE]
> Jeśli utworzono zmienne środowiskowe po uruchomieniu aplikacji, należy zamknąć i ponownie otworzyć edytor, IDE lub powłoki z systemem go, aby uzyskać dostęp do zmiennych.

### <a name="install-the-client-library"></a>Instalowanie biblioteki klienta

Bibliotekę klienta moderatora zawartości można zainstalować za pomocą następującego polecenia:

```console
pip install --upgrade azure-cognitiveservices-vision-contentmoderator
```

## <a name="object-model"></a>Model obiektu

Następujące klasy obsługują niektóre z głównych funkcji SDK języka Python moderatora zawartości.

|Nazwa|Opis|
|---|---|
|[ContentModeratorClient (łak.](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.content_moderator_client.contentmoderatorclient?view=azure-python)|Ta klasa jest potrzebna dla wszystkich funkcji moderatora zawartości. Tworzenie wystąpienia z informacjami o subskrypcji i używać go do tworzenia wystąpień innych klas.|
|[ImageModerationOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.imagemoderationoperations?view=azure-python)|Ta klasa zapewnia funkcje do analizowania obrazów dla zawartości dla dorosłych, informacji osobistych lub ludzkich twarzy.|
|[TextModerationOperations (Efekty)](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.textmoderationoperations?view=azure-python)|Ta klasa zapewnia funkcje do analizowania tekstu dla języka, wulgaryzmów, błędów i informacji osobistych.|
[OpinieOperacje](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.reviewsoperations?view=azure-python)|Ta klasa udostępnia funkcje interfejsu API przeglądu, w tym metody tworzenia zadań, niestandardowych przepływów pracy i przeglądów ludzkich.|

## <a name="code-examples"></a>Przykłady kodu

Te fragmenty kodu pokazują, jak wykonać następujące zadania za pomocą biblioteki klienta moderatora zawartości dla języka Python:

* [Uwierzytelnij klienta](#authenticate-the-client)
* [Moderowanie tekstu](#moderate-text)
* [Używanie listy terminów niestandardowych](#use-a-custom-terms-list)
* [Umiarkowane obrazy](#moderate-images)
* [Używanie niestandardowej listy obrazów](#use-a-custom-image-list)
* [Tworzenie recenzji](#create-a-review)

## <a name="authenticate-the-client"></a>Uwierzytelnij klienta

> [!NOTE]
> Ten przewodnik Szybki start zakłada, że [utworzono zmienne środowiskowe](../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) dla klucza i punktu końcowego moderatora zawartości.

Tworzenie wystąpienia klienta z punktem końcowym i kluczem. Utwórz [obiekt CognitiveServicesCredentials](https://docs.microsoft.com/python/api/msrest/msrest.authentication.cognitiveservicescredentials?view=azure-python) za pomocą klucza i użyj go z punktem końcowym do utworzenia [contentmoderatorclient](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.content_moderator_client.contentmoderatorclient?view=azure-python) obiektu.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_client)]

## <a name="moderate-text"></a>Moderowanie tekstu

Poniższy kod używa klienta content moderatora do analizowania treści tekstu i drukowania wyników na konsoli. Najpierw utwórz **folder text_files/** w katalogu głównym projektu i dodaj plik *content_moderator_text_moderation.txt.* Dodaj własny tekst do tego pliku lub użyj następującego przykładowego tekstu:

```
Is this a grabage email abcdef@abcd.com, phone: 4255550111, IP: 255.255.255.255, 1234 Main Boulevard, Panapolis WA 96555.
Crap is the profanity here. Is this information PII? phone 2065550111
```

Dodaj odwołanie do nowego folderu.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_textfolder)]

Następnie dodaj następujący kod do skryptu Języka Python.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_textmod)]

## <a name="use-a-custom-terms-list"></a>Używanie listy terminów niestandardowych

Poniższy kod pokazuje, jak zarządzać listą terminów niestandardowych dla moderowania tekstu. Za pomocą [listManagementTermListsOperationsOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.listmanagementtermlistsoperations?view=azure-python) klasy, aby utworzyć listę terminów, zarządzać poszczególnych terminów i ekran innych obiektów tekstu przeciwko niemu.

### <a name="get-sample-text"></a>Pobieranie przykładowego tekstu

Aby użyć tego przykładu, należy utworzyć folder **text_files/** w katalogu głównym projektu i dodać plik *content_moderator_term_list.txt.* Ten plik powinien zawierać tekst organiczny, który zostanie sprawdzony względem listy terminów. Można użyć następującego przykładowego tekstu:

```
This text contains the terms "term1" and "term2".
```

Dodaj odwołanie do folderu, jeśli nie zostały jeszcze zdefiniowane.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_textfolder)]

### <a name="create-a-list"></a>Tworzenie listy

Dodaj następujący kod do skryptu języka Python, aby utworzyć listę terminów niestandardowych i zapisać jego wartość identyfikatora.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_create)]

### <a name="define-list-details"></a>Definiowanie szczegółów listy

Identyfikator listy służy do edytowania jej nazwy i opisu.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_details)]

### <a name="add-a-term-to-the-list"></a>Dodawanie terminu do listy

Poniższy kod dodaje `"term1"` `"term2"` terminy i do listy.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_add)]

### <a name="get-all-terms-in-the-list"></a>Pobierz wszystkie terminy na liście

Identyfikator listy umożliwia zwrócenie wszystkich terminów na liście.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_getterms)]

### <a name="refresh-the-list-index"></a>Odświeżanie indeksu listy

Za każdym razem, gdy dodajesz lub usuwasz terminy z listy, należy odświeżyć indeks, aby można było użyć zaktualizowanej listy.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_refresh)]

### <a name="screen-text-against-the-list"></a>Tekst ekranu względem listy

Główną funkcją listy terminów niestandardowych jest porównanie treści tekstu z listą i ustalenie, czy istnieją pasujące terminy. 

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_screen)]

### <a name="remove-a-term-from-a-list"></a>Usuwanie terminu z listy

Poniższy kod usuwa `"term1"` termin z listy.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_remove)]

### <a name="remove-all-terms-from-a-list"></a>Usuwanie wszystkich terminów z listy

Użyj następującego kodu, aby wyczyścić listę wszystkich jego warunków.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_removeall)]

### <a name="delete-list"></a>Usuń listę

Użyj następującego kodu, aby usunąć listę terminów niestandardowych.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_deletelist)]

## <a name="moderate-images"></a>Umiarkowane obrazy

Poniższy kod używa klienta moderatora zawartości, wraz z [ImageModerationOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.imagemoderationoperations?view=azure-python) obiektu do analizowania obrazów dla dorosłych i zawartości rasistowskie.

### <a name="get-images"></a>Pobieranie obrazów

Zdefiniuj odwołanie do niektórych obrazów do analizy.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagemodvars)]

Następnie dodaj następujący kod do iteracji za pośrednictwem obrazów. Pozostała część kodu w tej sekcji przejdzie do tej pętli.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagemod_iterate)]

### <a name="check-for-adultracy-content"></a>Sprawdź, czy treści dla dorosłych/rasy

Poniższy kod sprawdza obraz pod podanym adresem URL dla treści dla dorosłych lub rasistowskich i drukuje wyniki na konsoli. Zobacz przewodnik [pojęć moderowania obrazu,](./image-moderation-api.md) aby uzyskać informacje na temat tego, co oznaczają te terminy.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagemod_ar)]

### <a name="check-for-visible-text"></a>Sprawdzanie widocznego tekstu

Poniższy kod sprawdza obraz pod kątem widocznej zawartości tekstowej i drukuje wyniki na konsoli.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagemod_text)]

### <a name="check-for-faces"></a>Sprawdź, czy twarze

Poniższy kod sprawdza obraz dla ludzkich twarzy i drukuje wyniki na konsoli.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagemod_face)]

## <a name="use-a-custom-image-list"></a>Używanie niestandardowej listy obrazów

Poniższy kod pokazuje, jak zarządzać niestandardową listę obrazów dla moderacji obrazu. Ta funkcja jest przydatna, jeśli platforma często otrzymuje wystąpienia tego samego zestawu obrazów, które mają zostać wyświetlone. Utrzymując listę tych konkretnych obrazów, można zwiększyć wydajność. [Klasa ListManagementImageListsOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.listmanagementimagelistsoperations?view=azure-python) umożliwia utworzenie listy obrazów, zarządzanie poszczególnymi obrazami na liście i porównywanie innych obrazów z nią.

Utwórz następujące zmienne tekstowe, aby zapisać adresy URL obrazów, które będą używane w tym scenariuszu.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelistvars)]

> [!NOTE]
> Nie jest to właściwa lista, ale nieformalna lista obrazów, które zostaną dodane w `add images` sekcji kodu.


### <a name="create-an-image-list"></a>Tworzenie listy obrazów

Dodaj poniższy kod, aby utworzyć listę obrazów i zapisać odwołanie do jego identyfikatora.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_create)]

### <a name="add-images-to-a-list"></a>Dodawanie obrazów do listy

Poniższy kod dodaje wszystkie obrazy do listy.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_add)]

Zdefiniuj funkcję **pomocnika add_images** w innym miejscu skryptu.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_addhelper)]

### <a name="get-images-in-list"></a>Pobierz obrazy na listę

Poniższy kod drukuje nazwy wszystkich obrazów na liście.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_getimages)]

### <a name="update-list-details"></a>Szczegóły listy aktualizacji

Identyfikator listy służy do aktualizowania nazwy i opisu listy.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_updatedetails)]

### <a name="get-list-details"></a>Uzyskaj szczegółowe informacje o liście

Użyj następującego kodu, aby wydrukować bieżące szczegóły listy.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_getdetails)]

### <a name="refresh-the-list-index"></a>Odświeżanie indeksu listy

Po dodaniu lub usunięciu obrazów należy odświeżyć indeks listy, aby można było go używać do ekranów innych obrazów.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_refresh)]

### <a name="match-images-against-the-list"></a>Dopasowyuj obrazy do listy

Główną funkcją list obrazów jest porównanie nowych obrazów i sprawdzenie, czy są jakieś dopasowania.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_match)]

### <a name="remove-an-image-from-the-list"></a>Usuwanie obrazu z listy

Poniższy kod usuwa element z listy. W takim przypadku jest to obraz, który nie pasuje do kategorii listy.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_remove)]

### <a name="remove-all-images-from-a-list"></a>Usuwanie wszystkich obrazów z listy

Użyj następującego kodu, aby wyczyścić listę obrazów.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_removeall)]

### <a name="delete-the-image-list"></a>Usuwanie listy obrazów

Użyj następującego kodu, aby usunąć daną listę obrazów.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_delete)]

## <a name="create-a-review"></a>Tworzenie recenzji

Za pomocą zestawu SDK języka Python moderatora zawartości można przesyłać zawartość do [narzędzia Recenzja,](https://contentmoderator.cognitive.microsoft.com) aby moderatorzy mogli je przejrzeć. Aby dowiedzieć się więcej o narzędziu Recenzja, zobacz [przewodnik koncepcyjny narzędzia Recenzji](./review-tool-user-guide/human-in-the-loop.md).

Poniższy kod używa [ReviewsOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.reviewsoperations?view=azure-python) klasy do tworzenia przeglądu, pobrać jego identyfikator i sprawdzić jego szczegóły po otrzymaniu danych wejściowych człowieka za pośrednictwem portalu sieci web narzędzia przeglądu.

### <a name="get-review-credentials"></a>Pobierz poświadczenia recenzji

Najpierw zaloguj się do narzędzia Recenzja i odzyskaj nazwę zespołu. Następnie przypisz go do odpowiedniej zmiennej w kodzie. Opcjonalnie można skonfigurować punkt końcowy wywołania zwrotnego, aby otrzymywać aktualizacje dotyczące działania przeglądu.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagereview_vars)]

### <a name="create-an-image-review"></a>Tworzenie recenzji obrazu

Dodaj następujący kod, aby utworzyć i opublikować recenzję dla danego adresu URL obrazu. Kod zapisuje odwołanie do identyfikatora recenzji. 
[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagereview_create)]

### <a name="get-review-details"></a>Uzyskaj szczegółowe informacje o recenzji

Użyj poniższego kodu, aby sprawdzić szczegóły danej recenzji. Po utworzeniu recenzji możesz samodzielnie przejść do narzędzia Recenzja i wchodzić w interakcje z zawartością. Aby uzyskać informacje na temat tego, jak to zrobić, zobacz [recenzje poradnik .](https://docs.microsoft.com/azure/cognitive-services/content-moderator/review-tool-user-guide/review-moderated-images) Po zakończeniu można uruchomić ten kod ponownie i będzie pobierać wyniki procesu przeglądu.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagereview_getdetails)]

Jeśli użyto punktu końcowego wywołania zwrotnego w tym scenariuszu, powinien on otrzymać zdarzenie w tym formacie:

```console
{'callback_endpoint': 'https://requestb.in/qmsakwqm',
 'content': '',
 'content_id': '3ebe16cb-31ed-4292-8b71-1dfe9b0e821f',
 'created_by': 'cspythonsdk',
 'metadata': [{'key': 'sc', 'value': 'True'}],
 'review_id': '201901i14682e2afe624fee95ebb248643139e7',
 'reviewer_result_tags': [{'key': 'a', 'value': 'True'},
                          {'key': 'r', 'value': 'True'}],
 'status': 'Complete',
 'sub_team': 'public',
 'type': 'Image'}
```

## <a name="run-the-application"></a>Uruchamianie aplikacji

Uruchom aplikację za `python` pomocą polecenia w pliku szybkiego startu.

```console
python quickstart-file.py
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli chcesz wyczyścić i usunąć subskrypcję usług Cognitive Services, możesz usunąć zasób lub grupę zasobów. Usunięcie grupy zasobów powoduje również usunięcie innych skojarzonych z nią zasobów.

* [Portal](../cognitive-services-apis-create-account.md#clean-up-resources)
* [Interfejs wiersza polecenia platformy Azure](../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start dowiesz się, jak używać biblioteki Języka Python moderatora zawartości do wykonywania zadań moderowania. Następnie dowiedz się więcej o moderowaniu obrazów lub innych mediów, czytając przewodnik koncepcyjny.

> [!div class="nextstepaction"]
>[Pojęcia dotyczące moderowania obrazu](https://docs.microsoft.com/azure/cognitive-services/content-moderator/image-moderation-api)

* [Czym jest usługa Azure Content Moderator?](./overview.md)
* Kod źródłowy tego przykładu można znaleźć w usłudze [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/ContentModerator/ContentModeratorQuickstart.py).