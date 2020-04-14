---
title: Niestandardowa umiejętność rozpoznawania formularzy (C#)
titleSuffix: Azure Cognitive Search
description: Dowiedz się, jak utworzyć niestandardową umiejętność rozpoznawania formularzy przy użyciu języka C# i programu Visual Studio.
manager: nitinme
author: PatrickFarley
ms.author: pafarley
ms.service: cognitive-search
ms.topic: article
ms.date: 01/21/2020
ms.openlocfilehash: 713b790c432f0e416392243262aed4b0fcda8892
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81274577"
---
# <a name="example-create-a-form-recognizer-custom-skill"></a>Przykład: Tworzenie niestandardowej umiejętności rozpoznawania formularzy

W tym przykładzie narzędzia umiejętności usługi Azure Cognitive Search dowiesz się, jak utworzyć niestandardową umiejętność rozpoznawania formularzy przy użyciu języka C# i programu Visual Studio. Aparat rozpoznawania formularzy analizuje dokumenty i wyodrębnia pary klucz/wartość oraz dane tabeli. Zapakowanie aparatu rozpoznawania formularzy do [niestandardowego interfejsu umiejętności](cognitive-search-custom-skill-interface.md)można dodać tę funkcję jako krok w potoku wzbogacania end-to-end. Potoku można następnie załadować dokumenty i wykonać inne przekształcenia.

## <a name="prerequisites"></a>Wymagania wstępne

- [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) (dowolna wersja).
- Co najmniej pięć form tego samego typu. Można użyć przykładowych danych dostarczonych z tym przewodnikiem.

## <a name="create-a-form-recognizer-resource"></a>Tworzenie zasobu aparatu rozpoznawania formularzy

[!INCLUDE [create resource](../cognitive-services/form-recognizer/includes/create-resource.md)]

## <a name="train-your-model"></a>Trenowanie modelu

Przed użyciem tej umiejętności należy wyszkolić model aparatu rozpoznawania formularzy za pomocą formularzy wejściowych. Postępuj zgodnie z [przewodnikiem szybki start cURL,](https://docs.microsoft.com/azure/cognitive-services/form-recognizer/quickstarts/curl-train-extract) aby dowiedzieć się, jak szkolić model. Można użyć przykładowych formularzy podanych w tym przewodniku Szybki start lub użyć własnych danych. Po przeszkoleniu modelu skopiuj jego wartość identyfikatora do bezpiecznej lokalizacji.

## <a name="set-up-the-custom-skill"></a>Konfigurowanie umiejętności niestandardowych

W tym samouczku użyto projektu [AnalyzeForm](https://github.com/Azure-Samples/azure-search-power-skills/tree/master/Vision/AnalyzeForm) w repozytorium Usługi [Azure Search Power Skills](https://github.com/Azure-Samples/azure-search-power-skills) GitHub. Sklonuj to repozytorium na komputerze lokalnym i przejdź do **wizji/analizyform/** w celu uzyskania dostępu do projektu. Następnie otwórz _analyzeform.csproj_ w programie Visual Studio. Ten projekt tworzy zasób funkcji platformy Azure, który spełnia [niestandardowy interfejs umiejętności](cognitive-search-custom-skill-interface.md) i może służyć do wzbogacania usługi Azure Cognitive Search. Przyjmuje dokumenty formularza jako dane wejściowe i wyprowadza (jako tekst) pary klucz/wartość, które określisz.

Najpierw dodaj zmienne środowiskowe na poziomie projektu. Znajdź projekt **AnalyzeForm** w lewym okienku, kliknij go prawym przyciskiem myszy i wybierz polecenie **Właściwości**. W oknie **Właściwości** kliknij kartę **Debugowanie,** a następnie znajdź pole **Zmienne środowisko.** Kliknij **przycisk Dodaj,** aby dodać następujące zmienne:
* `FORMS_RECOGNIZER_ENDPOINT_URL`z wartością ustawioną na adres URL punktu końcowego.
* `FORMS_RECOGNIZER_API_KEY`z wartością ustawioną na klucz subskrypcji.
* `FORMS_RECOGNIZER_MODEL_ID`z wartością ustawioną na identyfikator przeszkolonego modelu.
* `FORMS_RECOGNIZER_RETRY_DELAY`z wartością ustawioną na 1000. Ta wartość jest czas w milisekundach, że program będzie czekać przed ponowieniem próby kwerendy.
* `FORMS_RECOGNIZER_MAX_ATTEMPTS`z wartością ustawioną na 100. Ta wartość jest liczba razy program będzie kwerendy usługi podczas próby uzyskania pomyślnej odpowiedzi.

Następnie otwórz _AnalyzeForm.cs_ i znajdź `fieldMappings` zmienną, która odwołuje się do pliku *field-mappings.json.* Ten plik (i zmienna, która się do niego odwołuje) definiuje listę kluczy, które chcesz wyodrębnić z formularzy i etykietę niestandardową dla każdego klucza. Na przykład wartość `{ "Address:", "address" }, { "Invoice For:", "recipient" }` oznacza, że skrypt zapisze tylko `Address:` wartości `Invoice For:` dla wykrytych i pól, a etykiety te wartości z `"address"` i `"recipient"`, odpowiednio.

Na koniec należy `contentType` zwrócić uwagę na zmienną. Ten skrypt uruchamia dany model rozpoznawania formularzy na zdalnych dokumentach, `application/json`do których odwołuje się adres URL, więc typem zawartości jest . Jeśli chcesz analizować pliki lokalne, dołączając ich strumienie bajtów do `contentType` żądań HTTP, musisz zmienić odpowiedni [typ MIME](https://developer.mozilla.org/docs/Web/HTTP/Basics_of_HTTP/MIME_types/Complete_list_of_MIME_types) dla pliku.

## <a name="test-the-function-from-visual-studio"></a>Testowanie funkcji z programu Visual Studio

Po edycji projektu zapisz go i ustaw projekt **AnalyzeForm** jako projekt startowy w programie Visual Studio (jeśli nie jest jeszcze ustawiony). Następnie naciśnij **klawisz F5,** aby uruchomić tę funkcję w środowisku lokalnym. Użyj usługi REST, takiej jak [Postman,](https://www.postman.com/) aby wywołać tę funkcję.

### <a name="http-request"></a>Żądanie HTTP

Zostanie złożony następujący wniosek, aby wywołać funkcję.

```HTTP
POST https://localhost:7071/api/analyze-form
```

### <a name="request-body"></a>Treść żądania

Zacznij od poniższego szablonu treści żądania.

```json
{
    "values": [
        {
            "recordId": "record1",
            "data": { 
                "formUrl": "<your-form-url>",
                "formSasToken": "<your-sas-token>"
            }
        }
    ]
}
```

W tym miejscu należy podać adres URL formularza, który ma ten sam typ co formularze, z którymi został przeszkolony. Do celów testowych można użyć jednego z formularzy treningowych. Jeśli po cURL szybki start, formularze będą znajdować się w koncie magazynu obiektów blob platformy Azure. Otwórz Eksploratora usługi Azure Storage, znajdź plik formularza, kliknij go prawym przyciskiem myszy i wybierz pozycję **Pobierz podpis dostępu współdzielonego**. Następne okno dialogowe zapewni adres URL i token sygnatury dostępu Współdzielonego. Wprowadź te ciągi `"formUrl"` `"formSasToken"` w i pola treści żądania, odpowiednio.

> [!div class="mx-imgBorder"]
> ![Eksplorator magazynu platformy Azure; wybrany jest dokument pdf](media/cognitive-search-skill-form/form-sas.png)

Jeśli chcesz analizować zdalny dokument, który nie jest w magazynie obiektów `"formUrl"` blob `"formSasToken"` platformy Azure, wklej jego adres URL w polu i pozostaw pole puste.

> [!NOTE]
> Gdy umiejętność jest zintegrowana z zestawem umiejętności, adres URL i token będą dostarczane przez wyszukiwanie poznawcze.

### <a name="response"></a>Odpowiedź

Powinna zostać wyświetlna odpowiedź podobna do poniższego przykładu:

```json
{
    "values": [
        {
            "recordId": "record1",
            "data": {
                "address": "1111 8th st. Bellevue, WA 99501 ",
                "recipient": "Southridge Video 1060 Main St. Atlanta, GA 65024 "
            },
            "errors": null,
            "warnings": null
        }
    ]
}
```

## <a name="publish-the-function-to-azure"></a>Publikowanie funkcji na platformie Azure

Jeśli jesteś zadowolony z zachowania funkcji, można go opublikować.

1. W **Eksploratorze rozwiązań** w programie Visual Studio kliknij prawym przyciskiem myszy projekt i wybierz polecenie **Publikuj**. Wybierz pozycję **Utwórz nowy** > **publikowanie**.
1. Jeśli program Visual Studio nie został jeszcze połączony z kontem platformy Azure, wybierz pozycję **Dodaj konto....**
1. Postępuj zgodnie z instrukcjami wyświetlanymi na ekranie. Określ unikatową nazwę usługi aplikacji, subskrypcji platformy Azure, grupy zasobów, planu hostingu i konta magazynu, którego chcesz użyć. Jeśli jeszcze ich nie masz, możesz utworzyć nową grupę zasobów, nowy plan hostingu i nowe konto magazynu. Po zakończeniu wybierz pozycję **Utwórz**.
1. Po zakończeniu wdrażania zwróć uwagę na adres URL witryny. Ten adres URL to adres aplikacji funkcji na platformie Azure. Zapisz go w lokalizacji tymczasowej.
1. W [witrynie Azure portal](https://portal.azure.com)przejdź do grupy `AnalyzeForm` zasobów i poszukaj opublikowanej funkcji. W sekcji **Zarządzanie** powinny zostać wyświetlona sekcja Klucze hosta. Skopiuj *domyślny* klucz hosta i zapisz go w lokalizacji tymczasowej.

## <a name="connect-to-your-pipeline"></a>Łączenie się z rurociągiem

Aby użyć tej umiejętności w potoku wyszukiwania poznawczego, musisz dodać definicję umiejętności do swojego zestawu umiejętności. Następujący blok JSON jest przykładową definicją umiejętności (należy zaktualizować dane wejściowe i wyjściowe, aby odzwierciedlić określony scenariusz i środowisko zestaw umiejętności). Zamień `AzureFunctionEndpointUrl` na adres `AzureFunctionDefaultHostKey` URL funkcji i zastąp kluczem hosta.

```json
{ 
  "description":"Skillset that invokes the Form Recognizer custom skill",
  "skills":[ 
    "[... your existing skills go here]",
    { 
      "@odata.type":"#Microsoft.Skills.Custom.WebApiSkill",
      "name":"formrecognizer",
      "description":"Extracts fields from a form using a pre-trained form recognition model",
      "uri":"[AzureFunctionEndpointUrl]/api/analyze-form?code=[AzureFunctionDefaultHostKey]",
      "httpMethod":"POST",
      "timeout":"PT30S",
      "context":"/document",
      "batchSize":1,
      "inputs":[ 
        { 
          "name":"formUrl",
          "source":"/document/metadata_storage_path"
        },
        { 
          "name":"formSasToken",
          "source":"/document/metadata_storage_sas_token"
        }
      ],
      "outputs":[ 
        { 
          "name":"address",
          "targetName":"address"
        },
        { 
          "name":"recipient",
          "targetName":"recipient"
        }
      ]
    }
  ]
}
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku utworzono niestandardową umiejętność z usługi Azure Form Aparat rozpoznawania. Aby dowiedzieć się więcej o umiejętnościach niestandardowych, zobacz następujące zasoby. 

* [Umiejętności zasilania usługi Azure Search: repozytorium umiejętności niestandardowych](https://github.com/*zure-Samples/azure-search-power-skills)
* [Dodawanie umiejętności niestandardowych do potoku wzbogacania si.](cognitive-search-custom-skill-interface.md)
* [Definiowanie zestawu umiejętności](cognitive-search-defining-skillset.md)
* [Tworzenie zestawu umiejętności (REST)](https://docs.microsoft.com/rest/api/*earchservice/create-skillset)
* [Mapuj wzbogacone pola](cognitive-search-output-field-mapping.md)
