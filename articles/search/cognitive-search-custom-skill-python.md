---
title: Przykład umiejętności niestandardowych (Python)
titleSuffix: Azure Cognitive Search
description: Dla deweloperów języka Python zapoznaj się z narzędziami i technikami tworzenia umiejętności niestandardowych przy użyciu funkcji azure i programu Visual Studio. Umiejętności niestandardowe zawierają modele zdefiniowane przez użytkownika lub logikę, które można dodać do potoku indeksowania wzbogaconego o sztuczną inteligencję w usłudze Azure Cognitive Search.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/15/2020
ms.openlocfilehash: fc69761a05ea381d39d58d5ebf0046e0d9874961
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77210469"
---
# <a name="example-create-a-custom-skill-using-python"></a>Przykład: Tworzenie umiejętności niestandardowych za pomocą języka Python

W tym przykładzie narzędzia umiejętności usługi Azure Cognitive Search dowiesz się, jak utworzyć niestandardową umiejętność interfejsu API sieci Web przy użyciu języka Python i kodu programu Visual Studio. W przykładzie użyto [funkcji platformy Azure,](https://azure.microsoft.com/services/functions/) która implementuje [niestandardowy interfejs umiejętności](cognitive-search-custom-skill-interface.md).

Niestandardowa umiejętność jest prosta według projektu (łączy dwa ciągi), dzięki czemu można skupić się na narzędziach i technologiach używanych do projektowania umiejętności niestandardowych w języku Python. Gdy odniesiesz sukces dzięki prostej umiejętności, możesz rozgałęzić się z bardziej złożonymi scenariuszami.

## <a name="prerequisites"></a>Wymagania wstępne

+ Przejrzyj [niestandardowy interfejs umiejętności,](cognitive-search-custom-skill-interface.md) aby wprowadzić go do interfejsu wejścia/wyjścia, który należy zaimplementować umiejętności niestandardowe.

+ Skonfiguruj swoje środowisko. Po [tym samouczku end-to-end, aby](https://docs.microsoft.com/azure/python/tutorial-vs-code-serverless-python-01) skonfigurować bezserwerową funkcję Azure przy użyciu kodu programu Visual Studio i rozszerzeń języka Python. Samouczek prowadzi użytkownika przez instalację następujących narzędzi i składników: 

  + [Python 3.75](https://www.python.org/downloads/release/python-375/)
  + [Kod programu Visual Studio](https://code.visualstudio.com/)
  + [Rozszerzenie języka Python dla programu Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-python.python)
  + [Podstawowe narzędzia funkcji platformy Azure](https://docs.microsoft.com/azure/azure-functions/functions-run-local#v2)
  + [Rozszerzenie usługi Azure Functions dla programu Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)

## <a name="create-an-azure-function"></a>Tworzenie funkcji platformy Azure

W tym przykładzie użyto funkcji platformy Azure, aby zademonstrować koncepcję hostingu interfejsu API sieci web, ale możliwe są inne podejścia. Tak długo, jak spełniają [wymagania interfejsu dla umiejętności poznawczych,](cognitive-search-custom-skill-interface.md)podejście, które można podjąć jest bez znaczenia. Usługa Azure Functions ułatwia jednak tworzenie umiejętności niestandardowych.

### <a name="create-a-function-app"></a>Tworzenie aplikacji funkcji

Szablon projektu usługi Azure Functions w programie Visual Studio Code umożliwia utworzenie projektu, który można opublikować w aplikacji funkcji na platformie Azure. Aplikacja funkcji umożliwia grupowanie funkcji w jednostki logiczne, co ułatwia wdrażanie i udostępnianie zasobów oraz zarządzanie nimi.

1. W programie Visual Studio Code naciśnij klawisz F1, aby otworzyć paletę poleceń. W palecie poleceń wyszukaj i wybierz opcję `Azure Functions: Create new project...`.

1. Wybierz lokalizację katalogu dla obszaru roboczego projektu i wybierz pozycję **Wybierz**.

    > [!NOTE]
    > Te kroki zostały zaprojektowane do wykonania poza obszarem roboczym. Z tego powodu nie należy wybierać folderu projektu, który jest częścią obszaru roboczego.

1. Wybierz język dla projektu aplikacji funkcji. W tym samouczku **wybierz**python .
1. Wybierz wersję języka Python (wersja 3.7.5 jest obsługiwana przez usługę Azure Functions)
1. Wybierz szablon dla pierwszej funkcji projektu. Wybierz **wyzwalacz HTTP,** aby utworzyć funkcję wyzwalaną http w nowej aplikacji funkcji.
1. Podaj nazwę funkcji. W takim przypadku użyjmy **Konkadatora** 
1. Wybierz **pozycję Funkcja** jako poziom autoryzacji. Oznacza to, że udostępnimy [klucz funkcji](../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys) do wywołania punktu końcowego HTTP funkcji. 
1. Wybierz sposób otwierania projektu. W tym kroku wybierz pozycję **Dodaj do obszaru roboczego,** aby utworzyć aplikację funkcji w bieżącym obszarze roboczym.

Program Visual Studio Code utworzy projekt aplikacji funkcji w nowym obszarze roboczym. Ten projekt zawiera pliki konfiguracyjne [host.json](../azure-functions/functions-host-json.md) i [local.settings.json](../azure-functions/functions-run-local.md#local-settings-file), a także wszystkie specyficzne dla języka pliki projektu. 

Nowa funkcja wyzwalana http jest również tworzona w folderze **Konkadator** projektu aplikacji funkcji. Wewnątrz znajdzie się plik o\_\_nazwie " init__.py", z tą zawartością:

```py
import logging

import azure.functions as func


def main(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')

    if name:
        return func.HttpResponse(f"Hello {name}!")
    else:
        return func.HttpResponse(
             "Please pass a name on the query string or in the request body",
             status_code=400
        )

```

Teraz zmodyfikujmy ten kod, aby postępować zgodnie z [niestandardowym interfejsem umiejętności](cognitive-search-custom-skill-interface.md)). Zmodyfikuj kod o następującej zawartości:

```py
import logging
import azure.functions as func
import json

def main(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')

    try:
        body = json.dumps(req.get_json())
    except ValueError:
        return func.HttpResponse(
             "Invalid body",
             status_code=400
        )
    
    if body:
        result = compose_response(body)
        return func.HttpResponse(result, mimetype="application/json")
    else:
        return func.HttpResponse(
             "Invalid body",
             status_code=400
        )


def compose_response(json_data):
    values = json.loads(json_data)['values']
    
    # Prepare the Output before the loop
    results = {}
    results["values"] = []
    
    for value in values:
        output_record = transform_value(value)
        if output_record != None:
            results["values"].append(output_record)
    return json.dumps(results, ensure_ascii=False)

## Perform an operation on a record
def transform_value(value):
    try:
        recordId = value['recordId']
    except AssertionError  as error:
        return None

    # Validate the inputs
    try:         
        assert ('data' in value), "'data' field is required."
        data = value['data']        
        assert ('text1' in data), "'text1' field is required in 'data' object."
        assert ('text2' in data), "'text2' field is required in 'data' object."
    except AssertionError  as error:
        return (
            {
            "recordId": recordId,
            "errors": [ { "message": "Error:" + error.args[0] }   ]       
            })

    try:                
        concatenated_string = value['data']['text1'] + " " + value['data']['text2']  
        # Here you could do something more interesting with the inputs

    except:
        return (
            {
            "recordId": recordId,
            "errors": [ { "message": "Could not complete operation for record." }   ]       
            })

    return ({
            "recordId": recordId,
            "data": {
                "text": concatenated_string
                    }
            })
```

Metoda **transform_value** wykonuje operację na jednym rekordzie. Możesz zmodyfikować metodę, aby spełnić określone potrzeby. Pamiętaj, aby wykonać wszelkie niezbędne sprawdzanie poprawności danych wejściowych i zwrócić wszelkie błędy i ostrzeżenia wyprodukowane, jeśli operacja nie może zostać ukończona dla rekordu.

### <a name="debug-your-code-locally"></a>Debugowanie kodu lokalnie

Visual Studio Code ułatwia debugowanie kodu. Naciśnij klawisz "F5" lub przejdź do menu **Debugowania** i wybierz polecenie **Rozpocznij debugowanie**.

Można ustawić wszystkie punkty przerwania w kodzie, naciskając "F9" w wierszu zainteresowania.

Po rozpoczęciu debugowania funkcja będzie działać lokalnie. Można użyć narzędzia, takiego jak Listonosz lub Skrzypek, aby wysłać żądanie do localhost. Zanotuj lokalizację lokalnego punktu końcowego w oknie Terminal. 

## <a name="publish-your-function"></a>Opublikuj swoją funkcję

Jeśli jesteś zadowolony z zachowania funkcji, można go opublikować.

1. W programie Visual Studio Code naciśnij klawisz F1, aby otworzyć paletę poleceń. W palecie poleceń wyszukaj i wybierz **pozycję Wdrażaj w aplikacji funkcji...**. 

1. Wybierz subskrypcję platformy Azure, w której chcesz wdrożyć aplikację.

1. Wybierz **+ Utwórz nową aplikację funkcji na platformie Azure**

1. Wprowadź globalnie unikatową nazwę aplikacji funkcji.

1. Wybierz wersję Pythona (Python 3.7.x działa dla tej funkcji).

1. Wybierz lokalizację dla nowego zasobu (na przykład Zachodnie stany USA 2).

W tym momencie niezbędne zasoby zostaną utworzone w ramach subskrypcji platformy Azure do obsługi nowej funkcji platformy Azure na platformie Azure. Zaczekaj na zakończenie wdrożenia. Okno danych wyjściowych wyświetli stan procesu wdrażania.

1. W [witrynie Azure portal](https://portal.azure.com)przejdź do **pozycji Wszystkie zasoby** i poszukaj funkcji opublikowanej przez jej nazwę. Jeśli nazwano go **Konkadator**, wybierz zasób.

1. Kliknij przycisk **</> Pobierz adres URL funkcji.** Pozwoli to skopiować adres URL, aby wywołać funkcję.

## <a name="test-the-function-in-azure"></a>Testowanie funkcji na platformie Azure

Teraz, gdy masz domyślny klucz hosta, przetestuj swoją funkcję w następujący sposób:

```http
POST [Function URL you copied above]
```

### <a name="request-body"></a>Treść żądania
```json
{
    "values": [
        {
            "recordId": "e1",
            "data":
            {
                "text1":  "Hello",
                "text2":  "World"
            }
        },
        {
            "recordId": "e2",
            "data": "This is an invalid input"
        }
    ]
}
```

W tym przykładzie należy uzyskać taki sam wynik, który widziałeś wcześniej podczas uruchamiania funkcji w środowisku lokalnym.

## <a name="connect-to-your-pipeline"></a>Łączenie się z rurociągiem

Teraz, gdy masz nową umiejętność niestandardową, możesz dodać ją do swojego umiejętności. Poniższy przykład pokazuje, jak wywołać umiejętności, aby połączyć tytuł i autor dokumentu w jednym polu, które nazywamy merged_title_author. Zamień `[your-function-url-here]` na adres URL nowej funkcji platformy Azure.

```json
{
    "skills": [
      "[... your existing skills remain here]",  
      {
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "description": "Our new search custom skill",
        "uri": "https://[your-function-url-here]",        
          "context": "/document/merged_content/organizations/*",
          "inputs": [
            {
              "name": "text1",
              "source": "/document/metadata_title"
            },
            {
              "name": "text2",
              "source": "/document/metadata_author"
            },
          ],
          "outputs": [
            {
              "name": "text",
              "targetName": "merged_title_author"
            }
          ]
      }
  ]
}
```

## <a name="next-steps"></a>Następne kroki
Gratulacje! Stworzyłeś swoją pierwszą umiejętność niestandardową. Teraz możesz wykonać ten sam wzorzec, aby dodać własne funkcje niestandardowe. Kliknij poniższe linki, aby dowiedzieć się więcej.

+ [Power Skills: repozytorium umiejętności niestandardowych](https://github.com/Azure-Samples/azure-search-power-skills)
+ [Dodawanie umiejętności niestandardowych do potoku wzbogacania si.](cognitive-search-custom-skill-interface.md)
+ [Jak zdefiniować zestaw umiejętności](cognitive-search-defining-skillset.md)
+ [Tworzenie skillset (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [Jak mapować wzbogacone pola](cognitive-search-output-field-mapping.md)
