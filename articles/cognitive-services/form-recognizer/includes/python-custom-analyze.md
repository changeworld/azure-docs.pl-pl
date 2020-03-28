---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 11/14/2019
ms.author: pafarley
ms.openlocfilehash: 3c6059e131eadf1144fd189c47691b2352176745
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75446444"
---
## <a name="analyze-forms-for-key-value-pairs-and-tables"></a>Analizowanie formularzy dla par klucza i wartości i tabel

Następnie użyjesz nowo przeszkolonego modelu do analizowania dokumentu i wyodrębniania z niego par klucza wartości i tabel. Wywołanie interfejsu API **[formularza analizy,](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeWithCustomForm)** uruchamiając następujący kod w nowym skrypcie języka Python. Przed uruchomieniem skryptu należy wprowadzić następujące zmiany:

1. Zastąp `<file path>` ścieżką pliku formularza (na przykład C:\temp\file.pdf). Może to być również adres URL pliku zdalnego. W tym przewodniku Szybki start można użyć plików w folderze **Test** [przykładowego zestawu danych](https://go.microsoft.com/fwlink/?linkid=2090451).
1. Zamień `<model_id>` identyfikator modelu otrzymany w poprzedniej sekcji.
1. Zamień `<endpoint>` punkt końcowy uzyskany za pomocą klucza subskrypcji aparatu rozpoznawania formularzy. Można go znaleźć na karcie **Omówienie** zasobu aparatu rozpoznawania formularzy.
1. Zamień `<file type>` na typ pliku. Obsługiwane `application/pdf`typy: `image/jpeg` `image/png`, `image/tiff`, , .
1. Zastąp element `<subscription key>` kluczem subskrypcji.

    ```python
    ########### Python Form Recognizer Async Analyze #############
    import json
    import time
    from requests import get, post
    
    # Endpoint URL
    endpoint = r"<endpoint>"
    apim_key = "<subsription key>"
    model_id = "<model_id>"
    post_url = endpoint + "/formrecognizer/v2.0-preview/custom/models/%s/analyze" % model_id
    source = r"<file path>"
    params = {
        "includeTextDetails": True
    }
    
    headers = {
        # Request headers
        'Content-Type': '<file type>',
        'Ocp-Apim-Subscription-Key': apim_key,
    }
    with open(source, "rb") as f:
        data_bytes = f.read()
    
    try:
        resp = post(url = post_url, data = data_bytes, headers = headers, params = params)
        if resp.status_code != 202:
            print("POST analyze failed:\n%s" % json.dumps(resp.json()))
            quit()
        print("POST analyze succeeded:\n%s" % resp.headers)
        get_url = resp.headers["operation-location"]
    except Exception as e:
        print("POST analyze failed:\n%s" % str(e))
        quit() 
    ```

1. Zapisz kod w pliku z rozszerzeniem .py. Na przykład *form-recognizer-analyze.py*.
1. Otwórz okno wiersza polecenia.
1. W wierszu polecenia użyj polecenia `python`, aby uruchomić próbkę. Na przykład `python form-recognizer-analyze.py`.

Po **wywołaniu interfejsu** API formularza analizy `201 (Success)` otrzymasz odpowiedź z nagłówkiem **Lokalizacja operacji.** Wartość tego nagłówka jest identyfikatorem, który będzie używany do śledzenia wyników operacji Analizowanie. Powyższy skrypt drukuje wartość tego nagłówka na konsoli.

## <a name="get-the-analyze-results"></a>Uzyskaj wyniki analizy

Dodaj następujący kod na dole skryptu Pythona. Używa wartości identyfikatora z poprzedniego wywołania w nowym wywołaniu interfejsu API, aby pobrać wyniki analizy. Operacja **Analizuj formularz** jest asynchroniza, więc ten skrypt wywołuje interfejs API w regularnych odstępach czasu, dopóki wyniki nie będą dostępne. Zalecamy interwał jednej sekundy lub więcej.

```python 
n_tries = 15
n_try = 0
wait_sec = 5
max_wait_sec = 60
while n_try < n_tries:
    try:
        resp = get(url = get_url, headers = {"Ocp-Apim-Subscription-Key": apim_key})
        resp_json = resp.json()
        if resp.status_code != 200:
            print("GET analyze results failed:\n%s" % json.dumps(resp_json))
            quit()
        status = resp_json["status"]
        if status == "succeeded":
            print("Analysis succeeded:\n%s" % json.dumps(resp_json))
            quit()
        if status == "failed":
            print("Analysis failed:\n%s" % json.dumps(resp_json))
            quit()
        # Analysis still running. Wait and retry.
        time.sleep(wait_sec)
        n_try += 1
        wait_sec = min(2*wait_sec, max_wait_sec)     
    except Exception as e:
        msg = "GET analyze results failed:\n%s" % str(e)
        print(msg)
        quit()
print("Analyze operation did not complete within the allocated time.")
```
