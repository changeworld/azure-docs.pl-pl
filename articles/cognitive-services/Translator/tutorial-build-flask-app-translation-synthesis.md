---
title: 'Samouczek: Tworzenie aplikacji Flask do tłumaczenia, syntezy i analizowania tekstu - Translator Text API'
titleSuffix: Azure Cognitive Services
description: W tym samouczku zbudujemy aplikację internetową opartą na kolbie do tłumaczenia tekstu, analizowania tonacji i syntezy przetłumaczonego tekstu na mowę.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: tutorial
ms.date: 02/10/2020
ms.author: swmachan
ms.openlocfilehash: b41b68725b6747cbada13a9acc321724b3f89d67
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77118581"
---
# <a name="tutorial-build-a-flask-app-with-azure-cognitive-services"></a>Samouczek: Tworzenie aplikacji Flask za pomocą usług Azure Cognitive Services

W tym samouczku skompilujesz aplikację internetową Flask, która używa usługi Azure Cognitive Services do tłumaczenia tekstu, analizowania tonacji i syntezy przetłumaczonego tekstu na mowę. Koncentrujemy się na kodzie Pythona i trasach Flask, które umożliwiają naszą aplikację, jednak pomożemy Ci z HTML i Javascript, który ściąga aplikację razem. Jeśli napotkasz jakiekolwiek problemy, poinformuj nas o tym za pomocą poniższego przycisku opinii.

Oto, co obejmuje ten poradnik:

> [!div class="checklist"]
> * Pobierz klucze subskrypcji platformy Azure
> * Konfigurowanie środowiska programistycznego i instalowanie zależności
> * Tworzenie aplikacji Flask
> * Tłumaczenie tekstu za pomocą interfejsu API tekstu tłumacza
> * Analiza tekstu umożliwia analizowanie pozytywnych/negatywnych nastrojów tekstu wejściowego i tłumaczeń
> * Konwertowanie przetłumaczonego tekstu na mowę syntetyzowaną za pomocą usług mowy
> * Uruchom aplikację Flask lokalnie

> [!TIP]
> Jeśli chcesz przejść do przodu i zobaczyć cały kod naraz, cały przykład, wraz z instrukcjami kompilacji są dostępne na [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Flask-App-Tutorial).

## <a name="what-is-flask"></a>Co to jest Kolby?

Flask to mikrozakład do tworzenia aplikacji internetowych. Oznacza to, że Flask zapewnia narzędzia, biblioteki i technologie, które umożliwiają tworzenie aplikacji sieci web. Ta aplikacja internetowa może być niektóre strony internetowe, blog, wiki lub przejść tak istotne, jak aplikacja kalendarza opartego na sieci web lub komercyjnej stronie internetowej.

Dla tych z Was, którzy chcą głęboko nurkować po tym kursie oto kilka przydatnych linków:

* [Dokumentacja kolby](http://flask.pocoo.org/)
* [Kolba do manekinów - Przewodnik dla początkujących po Kolbie](https://codeburst.io/flask-for-dummies-a-beginners-guide-to-flask-part-uno-53aec6afc5b1)

## <a name="prerequisites"></a>Wymagania wstępne

Przejrzyjmy oprogramowanie i klucze subskrypcji, które będą potrzebne do tego samouczka.

* [Python 3.5.2 lub nowszy](https://www.python.org/downloads/)
* [Narzędzia Git](https://git-scm.com/downloads)
* Ide lub edytor tekstu, takich jak [Visual Studio Code](https://code.visualstudio.com/) lub [Atom](https://atom.io/)  
* [Chrome](https://www.google.com/chrome/browser/) lub [Firefox](https://www.mozilla.org/firefox)
* Klucz subskrypcji **Tekst tłumacza** (należy pamiętać, że nie jest wymagane wybranie regionu).
* Klucz subskrypcji **analizy tekstu** w regionie Zachodnie **stany USA.**
* Klucz subskrypcji **usług mowy** w regionie Zachodnie **stany USA.**

## <a name="create-an-account-and-subscribe-to-resources"></a>Tworzenie konta i subskrybowanie zasobów

Jak wcześniej wspomniano, będziesz potrzebować trzech kluczy subskrypcji dla tego samouczka. Oznacza to, że musisz utworzyć zasób w ramach konta platformy Azure dla:
* Tekst usługi Translator
* Analiza tekstu
* Usługi mowy

Utwórz [konto usług Cognitive Services w witrynie Azure portal,](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) aby uzyskać instrukcje krok po kroku w celu utworzenia zasobów.

> [!IMPORTANT]
> W tym samouczku utwórz swoje zasoby w regionie Zachodnie stany USA. Jeśli używasz innego regionu, musisz dostosować podstawowy adres URL w każdym z plików Języka Python.

## <a name="set-up-your-dev-environment"></a>Konfigurowanie środowiska deweloperskiego

Przed utworzeniem aplikacji sieci Web Flask należy utworzyć katalog roboczy dla projektu i zainstalować kilka pakietów Języka Python.

### <a name="create-a-working-directory"></a>Tworzenie katalogu roboczego

1. Otwórz wiersz polecenia (Windows) lub terminal (macOS/Linux). Następnie utwórz katalog roboczy i katalogi podrzędne dla projektu:  

   ```
   mkdir -p flask-cog-services/static/scripts && mkdir flask-cog-services/templates
   ```
2. Przejdź do katalogu roboczego projektu:  

   ```
   cd flask-cog-services
   ```

### <a name="create-and-activate-your-virtual-environment-with-virtualenv"></a>Tworzenie i aktywowanie środowiska wirtualnego za pomocą`virtualenv`

Stwórzmy wirtualne środowisko dla naszej `virtualenv`aplikacji Flask za pomocą . Korzystanie ze środowiska wirtualnego zapewnia, że masz czyste środowisko do pracy z.

1. W katalogu roboczym uruchom to polecenie, aby utworzyć środowisko wirtualne: **macOS/Linux:**
   ```
   virtualenv venv --python=python3
   ```
   Mamy jawnie zadeklarowane, że środowisko wirtualne należy używać języka Python 3. Gwarantuje to, że użytkownicy z wieloma instalacjami Języka Python używają poprawnej wersji.

   **Windows CMD / Windows Bash:**
   ```
   virtualenv venv
   ```
   Aby wszystko było proste, nazywamy twoje środowisko wirtualne venv.

2. Polecenia aktywacji środowiska wirtualnego będą się różnić w zależności od platformy/powłoki:   

   | Platforma | Powłoka | Polecenie |
   |----------|-------|---------|
   | system macOS/Linux | bash/zsh | `source venv/bin/activate` |
   | Windows | bash | `source venv/Scripts/activate` |
   | | Wiersz polecenia | `venv\Scripts\activate.bat` |
   | | PowerShell | `venv\Scripts\Activate.ps1` |

   Po uruchomieniu tego polecenia wiersz polecenia lub sesja terminala powinna być poprzedzone `venv`.

3. Sesję można wyłączyć w dowolnym momencie, wpisując ją w `deactivate`wierszu polecenia lub terminalu: .

> [!NOTE]
> Python posiada obszerną dokumentację do tworzenia środowisk wirtualnych i zarządzania nimi, zobacz [virtualenv](https://virtualenv.pypa.io/en/latest/).

### <a name="install-requests"></a>Żądania instalacji

Żądania to popularny moduł, który służy do wysyłania żądań HTTP 1.1. Nie ma potrzeby ręcznego dodawania ciągów zapytań do adresów URL ani kodowania danych POST.

1. Aby zainstalować żądania, uruchom:

   ```
   pip install requests
   ```

> [!NOTE]
> Jeśli chcesz dowiedzieć się więcej o żądaniach, zobacz [Żądania: HTTP dla ludzi](https://2.python-requests.org/en/master/).

### <a name="install-and-configure-flask"></a>Instalowanie i konfigurowanie kolby

Następnie musimy zainstalować Flask. Flask obsługuje routing dla naszej aplikacji internetowej i pozwala nam na wykonywanie wywołań serwer-serwer, które ukrywają nasze klucze subskrypcji przed użytkownikiem końcowym.

1. Aby zainstalować kolbę, uruchom:
   ```
   pip install Flask
   ```
   Upewnijmy się, że Kolba została zainstalowana. Uruchomienie:
   ```
   flask --version
   ```
   Wersja powinna być wydrukowana na terminalu. Cokolwiek innego oznacza, że coś poszło nie tak.

2. Aby uruchomić aplikację Flask, możesz użyć polecenia kolby lub przełącznika Pythona -m z Flask. Zanim to zrobisz, musisz poinformować terminal, z którą aplikacją należy pracować, eksportując zmienną środowiskową: `FLASK_APP`

   **macOS/Linux**:
   ```
   export FLASK_APP=app.py
   ```

   **Okna**:
   ```
   set FLASK_APP=app.py
   ```

## <a name="create-your-flask-app"></a>Tworzenie aplikacji Flask

W tej sekcji utworzysz aplikację barebones Flask, która zwraca plik HTML, gdy użytkownicy trafią do katalogu głównego aplikacji. Nie spędzaj zbyt wiele czasu próbując oddzielić kod, wrócimy, aby zaktualizować ten plik później.

### <a name="what-is-a-flask-route"></a>Co to jest trasa Kolby?

Poświęćmy chwilę, aby porozmawiać o "[trasach](http://flask.pocoo.org/docs/1.0/api/#flask.Flask.route)". Routing jest używany do powiązania adresu URL z określoną funkcją. Flask używa dekoratorów tras do rejestrowania funkcji do określonych adresów URL. Na przykład, gdy użytkownik przechodzi do`/`katalogu głównego ( `index.html` ) naszej aplikacji internetowej, jest renderowany.  

```python
@app.route('/')
def index():
    return render_template('index.html')
```

Spójrzmy na jeszcze jeden przykład, aby wbić ten dom.

```python
@app.route('/about')
def about():
    return render_template('about.html')
```

Ten kod zapewnia, że gdy `http://your-web-app.com/about` użytkownik `about.html` przechodzi do tego pliku jest renderowany.

Chociaż te przykłady ilustrują sposób renderowania stron html dla użytkownika, trasy mogą być również używane do wywoływania interfejsów API po naciśnięciu przycisku lub podejmowania dowolnej liczby akcji bez konieczności odsuwania się od strony głównej. Zobaczysz to w akcji podczas tworzenia tras do tłumaczenia, tonacji i syntezy mowy.

### <a name="get-started"></a>Wprowadzenie

1. Otwórz projekt w ide, a następnie `app.py` utwórz plik o nazwie w katalogu głównym katalogu roboczego. Następnie skopiuj `app.py` ten kod i zapisz:

   ```python
   from flask import Flask, render_template, url_for, jsonify, request

   app = Flask(__name__)
   app.config['JSON_AS_ASCII'] = False

   @app.route('/')
   def index():
       return render_template('index.html')
   ```

   Ten blok kodu informuje `index.html` aplikację do wyświetlania za każdym razem,`/`gdy użytkownik przechodzi do katalogu głównego aplikacji sieci web ( ).

2. Następnie utwórzmy front-end dla naszej aplikacji internetowej. Utwórz plik `index.html` o `templates` nazwie w katalogu. Następnie skopiuj ten kod do pliku `templates/index.html`.

   ```html
   <!doctype html>
   <html lang="en">
     <head>
       <!-- Required metadata tags -->
       <meta charset="utf-8">
       <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
       <meta name="description" content="Translate and analyze text with Azure Cognitive Services.">
       <!-- Bootstrap CSS -->
       <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/css/bootstrap.min.css" integrity="sha384-Gn5384xqQ1aoWXA+058RXPxPg6fy4IWvTNh0E263XmFcJlSAwiGgFAW/dAiS6JXm" crossorigin="anonymous">
       <title>Translate and analyze text with Azure Cognitive Services</title>
     </head>
     <body>
       <div class="container">
         <h1>Translate, synthesize, and analyze text with Azure</h1>
         <p>This simple web app uses Azure for text translation, text-to-speech conversion, and sentiment analysis of input text and translations. Learn more about <a href="https://docs.microsoft.com/azure/cognitive-services/">Azure Cognitive Services</a>.
        </p>
        <!-- HTML provided in the following sections goes here. -->

        <!-- End -->
       </div>

       <!-- Required Javascript for this tutorial -->
       <script src="https://code.jquery.com/jquery-3.2.1.slim.min.js" integrity="sha384-KJ3o2DKtIkvYIK3UENzmM7KCkRr/rE9/Qpg6aAZGJwFDMVNA/GpGFF93hXpG5KkN" crossorigin="anonymous"></script>
       <script src="https://ajax.googleapis.com/ajax/libs/jquery/3.3.1/jquery.min.js"></script>
       <script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.12.9/umd/popper.min.js" integrity="sha384-ApNbgh9B+Y1QKtv3Rn7W3mgPxhU9K/ScQsAP7hUibX39j7fakFPskvXusvfa0b4Q" crossorigin="anonymous"></script>
       <script src="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/js/bootstrap.min.js" integrity="sha384-JZR6Spejh4U02d8jOt6vLEHfe/JQGiRRSQQxSfFWpi1MquVdAyjUar5+76PVCmYl" crossorigin="anonymous"></script>
       <script type = "text/javascript" src ="static/scripts/main.js"></script>
     </body>
   </html>
   ```

3. Przetestujmy aplikację Flask. Z terminalu uruchom:

   ```
   flask run
   ```

4. Otwórz przeglądarkę i przejdź do podanego adresu URL. Powinna zostać wyświetlona aplikacja jednostronicowa. Naciśnij **klawisze Ctrl + c,** aby zabić aplikację.

## <a name="translate-text"></a>Tłumaczenie tekstu

Teraz, gdy masz pomysł, jak działa prosta aplikacja Flask, niech:

* Napisz pythona, aby wywołać interfejs API tekstu tłumacza i zwrócić odpowiedź
* Tworzenie trasy kolby w celu wywołania kodu Języka Python
* Aktualizowanie kodu HTML za pomocą obszaru wprowadzania i tłumaczenia tekstu, selektora języka i przycisku tłumaczenia
* Napisz Javascript, który pozwala użytkownikom na interakcję z aplikacją Flask z HTML

### <a name="call-the-translator-text-api"></a>Wywoływanie interfejsu API tekstu tłumacza

Pierwszą rzeczą, którą musisz zrobić, to napisać funkcję, aby wywołać interfejs API tekstu translatora. Ta funkcja będzie przyjmować dwa argumenty: `text_input` i `language_output`. Ta funkcja jest wywoływana za każdym razem, gdy użytkownik naciśnie przycisk tłumaczenia w aplikacji. Obszar tekstowy w kodzie `text_input`HTML jest wysyłany jako wartość , `language_output`a wartość wyboru języka w kodzie HTML jest wysyłana jako .

1. Zacznijmy od utworzenia pliku `translate.py` wywoływanego w katalogu głównym katalogu roboczego.
2. Następnie dodaj ten `translate.py`kod do pliku . Ta funkcja przyjmuje `text_input` dwa `language_output`argumenty: i .
   ```python
   import os, requests, uuid, json

   # Don't forget to replace with your Cog Services subscription key!
   # If you prefer to use environment variables, see Extra Credit for more info.
   subscription_key = 'YOUR_TRANSLATOR_TEXT_SUBSCRIPTION_KEY'
   
   # Don't forget to replace with your Cog Services location!
   # Our Flask route will supply two arguments: text_input and language_output.
   # When the translate text button is pressed in our Flask app, the Ajax request
   # will grab these values from our web app, and use them in the request.
   # See main.js for Ajax calls.
   def get_translation(text_input, language_output):
       base_url = 'https://api.cognitive.microsofttranslator.com'
       path = '/translate?api-version=3.0'
       params = '&to=' + language_output
       constructed_url = base_url + path + params

       headers = {
           'Ocp-Apim-Subscription-Key': subscription_key,
           'Ocp-Apim-Subscription-Region': 'location',
           'Content-type': 'application/json',
           'X-ClientTraceId': str(uuid.uuid4())
       }

       # You can pass more than one object in body.
       body = [{
           'text' : text_input
       }]
       response = requests.post(constructed_url, headers=headers, json=body)
       return response.json()
   ```
3. Dodaj klucz subskrypcji Translator Text i zapisz.

### <a name="add-a-route-to-apppy"></a>Dodawanie trasy do`app.py`

Następnie musisz utworzyć trasę w aplikacji Flask, która `translate.py`wywołuje . Ta trasa będzie wywoływana za każdym razem, gdy użytkownik naciśnie przycisk tłumaczenia w aplikacji.

W przypadku tej aplikacji trasa `POST` będzie akceptować żądania. Dzieje się tak, ponieważ funkcja oczekuje, że tekst do tłumaczenia i język wyjściowy dla tłumaczenia.

Flask zapewnia funkcje pomocnicze, które pomagają przeanalizować i zarządzać każdym żądaniem. W podany kod `get_json()` zwraca dane `POST` z żądania jako JSON. Następnie `data['text']` za `data['to']`pomocą i , tekst i `get_translation()` wyjściowe `translate.py`wartości języka są przekazywane do funkcji dostępnych z . Ostatnim krokiem jest zwrócenie odpowiedzi jako JSON, ponieważ należy wyświetlić te dane w aplikacji sieci web.

W poniższych sekcjach powtórzysz ten proces podczas tworzenia tras analizy tonacji i syntezy mowy.

1. Otwórz `app.py` i znajdź instrukcję importu u góry `app.py` i dodaj następujący wiersz:

   ```python
   import translate
   ```
   Teraz nasza aplikacja Flask może `translate.py`korzystać z metody dostępnej za pośrednictwem .

2. Skopiuj ten `app.py` kod na koniec i zapisz:

   ```python
   @app.route('/translate-text', methods=['POST'])
   def translate_text():
       data = request.get_json()
       text_input = data['text']
       translation_output = data['to']
       response = translate.get_translation(text_input, translation_output)
       return jsonify(response)
   ```

### <a name="update-indexhtml"></a>Aktualizowanie pliku `index.html`

Teraz, gdy masz funkcję tłumaczenia tekstu i trasę w aplikacji Flask, aby go wywołać, następnym krokiem jest rozpoczęcie tworzenia kodu HTML dla aplikacji. W poniższym html robi kilka rzeczy:

* Udostępnia obszar tekstowy, w którym użytkownicy mogą wprowadzać tekst do tłumaczenia.
* Zawiera selektor języka.
* Zawiera elementy HTML do renderowania wykrytego języka i punktów zaufania zwróconych podczas tłumaczenia.
* Udostępnia obszar tekstowy tylko do odczytu, w którym są wyświetlane dane wyjściowe tłumaczenia.
* Zawiera symbole zastępcze do analizy tonacji i kodu syntezy mowy, który zostanie dodany do tego pliku w dalszej części samouczka.

Zaktualizujmy `index.html`.

1. Otwórz `index.html` i znajdź te komentarze do kodu:
   ```html
   <!-- HTML provided in the following sections goes here. -->

   <!-- End -->
   ```

2. Zastąp komentarze kodu tym blokiem HTML:
   ```html
   <div class="row">
     <div class="col">
       <form>
         <!-- Enter text to translate. -->
         <div class="form-group">
           <label for="text-to-translate"><strong>Enter the text you'd like to translate:</strong></label>
           <textarea class="form-control" id="text-to-translate" rows="5"></textarea>
         </div>
         <!-- Select output language. -->
         <div class="form-group">
           <label for="select-language"><strong>Translate to:</strong></label>
           <select class="form-control" id="select-language">
             <option value="ar">Arabic</option>
             <option value="ca">Catalan</option>
             <option value="zh-Hans">Chinese (Simplified)</option>
             <option value="zh-Hant">Chinese (Traditional)</option>
             <option value="hr">Croatian</option>
             <option value="en">English</option>
             <option value="fr">French</option>
             <option value="de">German</option>
             <option value="el">Greek</option>
             <option value="he">Hebrew</option>
             <option value="hi">Hindi</option>
             <option value="it">Italian</option>
             <option value="ja">Japanese</option>
             <option value="ko">Korean</option>
             <option value="pt">Portuguese</option>
             <option value="ru">Russian</option>
             <option value="es">Spanish</option>
             <option value="th">Thai</option>
             <option value="tr">Turkish</option>
             <option value="vi">Vietnamese</option>
           </select>
         </div>
         <button type="submit" class="btn btn-primary mb-2" id="translate">Translate text</button></br>
         <div id="detected-language" style="display: none">
           <strong>Detected language:</strong> <span id="detected-language-result"></span><br />
           <strong>Detection confidence:</strong> <span id="confidence"></span><br /><br />
         </div>

         <!-- Start sentiment code-->

         <!-- End sentiment code -->

       </form>
     </div>
     <div class="col">
       <!-- Translated text returned by the Translate API is rendered here. -->
       <form>
         <div class="form-group" id="translator-text-response">
           <label for="translation-result"><strong>Translated text:</strong></label>
           <textarea readonly class="form-control" id="translation-result" rows="5"></textarea>
         </div>

         <!-- Start voice font selection code -->

         <!-- End voice font selection code -->

       </form>

       <!-- Add Speech Synthesis button and audio element -->

       <!-- End Speech Synthesis button -->

     </div>
   </div>
   ```

Następnym krokiem jest napisanie niektórych Javascript. Jest to pomost między trasą HTML i Flask.

### <a name="create-mainjs"></a>Utworzyć`main.js`  

Plik `main.js` jest mostem między trasą HTML i Flask. Aplikacja użyje kombinacji jQuery, Ajax i XMLHttpRequest do renderowania zawartości i żądania `POST` do tras Flask.

W poniższym kodzie zawartość z kodu HTML jest używana do konstruowania żądania do trasy Flask. W szczególności zawartość obszaru tekstowego i selektora języka są przypisywane do zmiennych, `translate-text`a następnie przekazywane w żądaniu do .

Kod następnie iteruje za pośrednictwem odpowiedzi i aktualizuje HTML z tłumaczeniem, wykrytym językiem i wynikiem zaufania.

1. Z ide, utwórz `main.js` plik `static/scripts` o nazwie w katalogu.
2. Skopiuj ten kod do: `static/scripts/main.js`
   ```javascript
   //Initiate jQuery on load.
   $(function() {
     //Translate text with flask route
     $("#translate").on("click", function(e) {
       e.preventDefault();
       var translateVal = document.getElementById("text-to-translate").value;
       var languageVal = document.getElementById("select-language").value;
       var translateRequest = { 'text': translateVal, 'to': languageVal }

       if (translateVal !== "") {
         $.ajax({
           url: '/translate-text',
           method: 'POST',
           headers: {
               'Content-Type':'application/json'
           },
           dataType: 'json',
           data: JSON.stringify(translateRequest),
           success: function(data) {
             for (var i = 0; i < data.length; i++) {
               document.getElementById("translation-result").textContent = data[i].translations[0].text;
               document.getElementById("detected-language-result").textContent = data[i].detectedLanguage.language;
               if (document.getElementById("detected-language-result").textContent !== ""){
                 document.getElementById("detected-language").style.display = "block";
               }
               document.getElementById("confidence").textContent = data[i].detectedLanguage.score;
             }
           }
         });
       };
     });
     // In the following sections, you'll add code for sentiment analysis and
     // speech synthesis here.
   })
   ```

### <a name="test-translation"></a>Tłumaczenie testowe

Przetestujmy tłumaczenie w aplikacji.

```
flask run
```

Przejdź do podanego adresu serwera. Wpisz tekst w obszarze wprowadzania, wybierz język i naciśnij przycisk Tłumacz. Powinieneś otrzymać tłumaczenie. Jeśli to nie zadziała, upewnij się, że dodano klucz subskrypcji.

> [!TIP]
> Jeśli wprowadzone zmiany nie są wyświetlane lub aplikacja nie działa tak, jak tego oczekujesz, spróbuj wyczyścić pamięć podręczną lub otworzyć okno prywatne/incognito.

Naciśnij **klawisze CTRL + c,** aby zabić aplikację, a następnie przejdź do następnej sekcji.

## <a name="analyze-sentiment"></a>Analiza tonacji

Interfejs [API analizy tekstu](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) może służyć do przeprowadzania analizy tonacji, wyodrębniania kluczowych fraz z tekstu lub wykrywania języka źródłowego. W tej aplikacji użyjemy analizy tonacji, aby ustalić, czy podany tekst jest pozytywny, neutralny czy negatywny. Interfejs API zwraca wynik liczbowy z zakresu od 0 do 1. Wyniki zbliżone do 1 wskazują na pozytywne nastroje, a wyniki bliskie 0 wskazują na negatywne nastroje.

W tej sekcji zrobisz kilka czynności:

* Napisz pythona, aby wywołać interfejs API analizy tekstu, aby przeprowadzić analizę tonacji i zwrócić odpowiedź
* Tworzenie trasy kolby w celu wywołania kodu Języka Python
* Aktualizowanie kodu HTML za pomocą obszaru dla wyników tonacji i przycisku do przeprowadzania analizy
* Napisz Javascript, który pozwala użytkownikom na interakcję z aplikacją Flask z HTML

### <a name="call-the-text-analytics-api"></a>Wywoływanie interfejsu API usługi Text Analytics

Napiszmy funkcję, aby wywołać interfejs API analizy tekstu. Ta funkcja będzie przyjmować `input_language` `output_text`cztery `output_language`argumenty: `input_text`, , i . Ta funkcja jest wywoływana za każdym razem, gdy użytkownik naciśnie przycisk analizy tonacji uruchamiania w aplikacji. Dane dostarczone przez użytkownika z obszaru tekstowego i selektora języka, a także wykryty język i tłumaczenie są dostarczane z każdym żądaniem. Obiekt odpowiedzi zawiera wyniki tonacji dla źródła i tłumaczenia. W poniższych sekcjach napiszesz javascript, aby przeanalizować odpowiedź i użyć jej w aplikacji. Na razie skupmy się na wywołaniu interfejsu API analizy tekstu.

1. Utwórzmy plik o `sentiment.py` nazwie w katalogu głównym katalogu roboczego.
2. Następnie dodaj ten `sentiment.py`kod do pliku .
   ```python
   import os, requests, uuid, json

   # Don't forget to replace with your Cog Services subscription key!
   subscription_key = 'YOUR_TEXT_ANALYTICS_SUBSCRIPTION_KEY'

   # Our Flask route will supply four arguments: input_text, input_language,
   # output_text, output_language.
   # When the run sentiment analysis button is pressed in our Flask app,
   # the Ajax request will grab these values from our web app, and use them
   # in the request. See main.js for Ajax calls.

   def get_sentiment(input_text, input_language, output_text, output_language):
       base_url = 'https://westus.api.cognitive.microsoft.com/text/analytics'
       path = '/v2.0/sentiment'
       constructed_url = base_url + path

       headers = {
           'Ocp-Apim-Subscription-Key': subscription_key,
           'Content-type': 'application/json',
           'X-ClientTraceId': str(uuid.uuid4())
       }

       # You can pass more than one object in body.
       body = {
           'documents': [
               {
                   'language': input_language,
                   'id': '1',
                   'text': input_text
               },
               {
                   'language': output_language,
                   'id': '2',
                   'text': output_text
               }
           ]
       }
       response = requests.post(constructed_url, headers=headers, json=body)
       return response.json()
   ```
3. Dodaj klucz subskrypcji Analizy tekstowej i zapisz.

### <a name="add-a-route-to-apppy"></a>Dodawanie trasy do`app.py`

Utwórzmy trasę w aplikacji Flask, `sentiment.py`która wywołuje . Ta trasa będzie wywoływana za każdym razem, gdy użytkownik naciśnie przycisk analizy tonacji uruchamiania w aplikacji. Podobnie jak trasa do tłumaczenia, `POST` ta trasa będzie akceptować żądania, ponieważ funkcja oczekuje argumentów.

1. Otwórz `app.py` i znajdź instrukcję importu u góry `app.py` i zaktualizuj ją:

   ```python
   import translate, sentiment
   ```
   Teraz nasza aplikacja Flask może `sentiment.py`korzystać z metody dostępnej za pośrednictwem .

2. Skopiuj ten `app.py` kod na koniec i zapisz:
   ```python
   @app.route('/sentiment-analysis', methods=['POST'])
   def sentiment_analysis():
       data = request.get_json()
       input_text = data['inputText']
       input_lang = data['inputLanguage']
       output_text = data['outputText']
       output_lang =  data['outputLanguage']
       response = sentiment.get_sentiment(input_text, input_lang, output_text, output_lang)
       return jsonify(response)
   ```

### <a name="update-indexhtml"></a>Aktualizowanie pliku `index.html`

Teraz, gdy masz funkcję uruchamiania analizy tonacji i trasę w aplikacji Flask, aby ją wywołać, następnym krokiem jest rozpoczęcie pisania kodu HTML dla aplikacji. W poniższym html robi kilka rzeczy:

* Dodaje przycisk do aplikacji w celu uruchamiania analizy tonacji
* Dodaje element wyjaśniający ocenianie tonów
* Dodaje element do wyświetlania wyników tonacji

1. Otwórz `index.html` i znajdź te komentarze do kodu:
   ```html
   <!-- Start sentiment code-->

   <!-- End sentiment code -->
   ```

2. Zastąp komentarze kodu tym blokiem HTML:
   ```html
   <button type="submit" class="btn btn-primary mb-2" id="sentiment-analysis">Run sentiment analysis</button></br>
   <div id="sentiment" style="display: none">
      <p>Sentiment scores are provided on a 1 point scale. The closer the sentiment score is to 1, indicates positive sentiment. The closer it is to 0, indicates negative sentiment.</p>
      <strong>Sentiment score for input:</strong> <span id="input-sentiment"></span><br />
      <strong>Sentiment score for translation:</strong> <span id="translation-sentiment"></span>
   </div>
   ```

### <a name="update-mainjs"></a>Aktualizowanie pliku `main.js`

W poniższym kodzie zawartość z kodu HTML jest używana do konstruowania żądania do trasy Flask. W szczególności zawartość obszaru tekstowego i selektora języka są przypisywane do zmiennych, a `sentiment-analysis` następnie przekazywane w żądaniu do trasy.

Kod następnie iteruje za pośrednictwem odpowiedzi i aktualizuje HTML z wynikami tonacji.

1. Z ide, utwórz `main.js` plik `static` o nazwie w katalogu.

2. Skopiuj ten kod do: `static/scripts/main.js`
   ```javascript
   //Run sentinment analysis on input and translation.
   $("#sentiment-analysis").on("click", function(e) {
     e.preventDefault();
     var inputText = document.getElementById("text-to-translate").value;
     var inputLanguage = document.getElementById("detected-language-result").innerHTML;
     var outputText = document.getElementById("translation-result").value;
     var outputLanguage = document.getElementById("select-language").value;

     var sentimentRequest = { "inputText": inputText, "inputLanguage": inputLanguage, "outputText": outputText,  "outputLanguage": outputLanguage };

     if (inputText !== "") {
       $.ajax({
         url: "/sentiment-analysis",
         method: "POST",
         headers: {
             "Content-Type":"application/json"
         },
         dataType: "json",
         data: JSON.stringify(sentimentRequest),
         success: function(data) {
           for (var i = 0; i < data.documents.length; i++) {
             if (typeof data.documents[i] !== "undefined"){
               if (data.documents[i].id === "1") {
                 document.getElementById("input-sentiment").textContent = data.documents[i].score;
               }
               if (data.documents[i].id === "2") {
                 document.getElementById("translation-sentiment").textContent = data.documents[i].score;
               }
             }
           }
           for (var i = 0; i < data.errors.length; i++) {
             if (typeof data.errors[i] !== "undefined"){
               if (data.errors[i].id === "1") {
                 document.getElementById("input-sentiment").textContent = data.errors[i].message;
               }
               if (data.errors[i].id === "2") {
                 document.getElementById("translation-sentiment").textContent = data.errors[i].message;
               }
             }
           }
           if (document.getElementById("input-sentiment").textContent !== '' && document.getElementById("translation-sentiment").textContent !== ""){
             document.getElementById("sentiment").style.display = "block";
           }
         }
       });
     }
   });
   // In the next section, you'll add code for speech synthesis here.
   ```

### <a name="test-sentiment-analysis"></a>Analiza tonacji testowych

Przetestujmy analizę tonacji w aplikacji.

```
flask run
```

Przejdź do podanego adresu serwera. Wpisz tekst w obszarze wprowadzania, wybierz język i naciśnij przycisk Tłumacz. Powinieneś otrzymać tłumaczenie. Następnie naciśnij przycisk analizy tonacji przebiegu. Powinieneś zobaczyć dwa wyniki. Jeśli to nie zadziała, upewnij się, że dodano klucz subskrypcji.

> [!TIP]
> Jeśli wprowadzone zmiany nie są wyświetlane lub aplikacja nie działa tak, jak tego oczekujesz, spróbuj wyczyścić pamięć podręczną lub otworzyć okno prywatne/incognito.

Naciśnij **klawisze CTRL + c,** aby zabić aplikację, a następnie przejdź do następnej sekcji.

## <a name="convert-text-to-speech"></a>Zamiana tekstu na mowę

Interfejs [API zamiany tekstu na mowę](https://docs.microsoft.com/azure/cognitive-services/speech-service/text-to-speech) umożliwia aplikacji konwertowanie tekstu na naturalną syntezowaną mowę podobną do człowieka. Usługa obsługuje głosy standardowe, neuronowe i niestandardowe. Nasza przykładowa aplikacja wykorzystuje kilka dostępnych głosów, aby uzyskać pełną listę, zobacz [obsługiwane języki.](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#text-to-speech)

W tej sekcji zrobisz kilka czynności:

* Napisz pythona, aby przekonwertować tekst na mowę za pomocą interfejsu API zamiany tekstu na mowę
* Tworzenie trasy kolby w celu wywołania kodu Języka Python
* Aktualizowanie kodu HTML za pomocą przycisku do konwersji tekstu na mowę oraz elementu do odtwarzania dźwięku
* Napisz Javascript, który pozwala użytkownikom na interakcję z aplikacją Flask

### <a name="call-the-text-to-speech-api"></a>Wywoływanie interfejsu API zamiany tekstu na mowę

Napiszmy funkcję konwersji tekstu na mowę. Ta funkcja będzie przyjmować dwa argumenty: `input_text` i `voice_font`. Ta funkcja jest wywoływana za każdym razem, gdy użytkownik naciśnie przycisk konwersji tekstu na mowę w aplikacji. `input_text`jest dane wyjściowe tłumaczenia zwracane `voice_font` przez wywołanie tłumaczenia tekstu, jest wartością z selektora czcionek głosowych w HTML.

1. Utwórzmy plik o `synthesize.py` nazwie w katalogu głównym katalogu roboczego.

2. Następnie dodaj ten `synthesize.py`kod do pliku .
   ```Python
   import os, requests, time
   from xml.etree import ElementTree

   class TextToSpeech(object):
       def __init__(self, input_text, voice_font):
           subscription_key = 'YOUR_SPEECH_SERVICES_SUBSCRIPTION_KEY'
           self.subscription_key = subscription_key
           self.input_text = input_text
           self.voice_font = voice_font
           self.timestr = time.strftime('%Y%m%d-%H%M')
           self.access_token = None

       # This function performs the token exchange.
       def get_token(self):
           fetch_token_url = 'https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken'
           headers = {
               'Ocp-Apim-Subscription-Key': self.subscription_key
           }
           response = requests.post(fetch_token_url, headers=headers)
           self.access_token = str(response.text)

       # This function calls the TTS endpoint with the access token.
       def save_audio(self):
           base_url = 'https://westus.tts.speech.microsoft.com/'
           path = 'cognitiveservices/v1'
           constructed_url = base_url + path
           headers = {
               'Authorization': 'Bearer ' + self.access_token,
               'Content-Type': 'application/ssml+xml',
               'X-Microsoft-OutputFormat': 'riff-24khz-16bit-mono-pcm',
               'User-Agent': 'YOUR_RESOURCE_NAME',
           }
           # Build the SSML request with ElementTree
           xml_body = ElementTree.Element('speak', version='1.0')
           xml_body.set('{http://www.w3.org/XML/1998/namespace}lang', 'en-us')
           voice = ElementTree.SubElement(xml_body, 'voice')
           voice.set('{http://www.w3.org/XML/1998/namespace}lang', 'en-US')
           voice.set('name', 'Microsoft Server Speech Text to Speech Voice {}'.format(self.voice_font))
           voice.text = self.input_text
           # The body must be encoded as UTF-8 to handle non-ascii characters.
           body = ElementTree.tostring(xml_body, encoding="utf-8")

           #Send the request
           response = requests.post(constructed_url, headers=headers, data=body)

           # Write the response as a wav file for playback. The file is located
           # in the same directory where this sample is run.
           return response.content
   ```
3. Dodaj klucz subskrypcji usług mowy i zapisz.

### <a name="add-a-route-to-apppy"></a>Dodawanie trasy do`app.py`

Utwórzmy trasę w aplikacji Flask, `synthesize.py`która wywołuje . Ta trasa będzie wywoływana za każdym razem, gdy użytkownik naciśnie przycisk konwersji tekstu na mowę w aplikacji. Podobnie jak trasy do analizy tłumaczeń i `POST` tonacji, ta trasa będzie akceptować żądania, ponieważ funkcja oczekuje dwóch argumentów: tekstu do syntezy i czcionki głosowej do odtwarzania.

1. Otwórz `app.py` i znajdź instrukcję importu u góry `app.py` i zaktualizuj ją:

   ```python
   import translate, sentiment, synthesize
   ```
   Teraz nasza aplikacja Flask może `synthesize.py`korzystać z metody dostępnej za pośrednictwem .

2. Skopiuj ten `app.py` kod na koniec i zapisz:

   ```Python
   @app.route('/text-to-speech', methods=['POST'])
   def text_to_speech():
       data = request.get_json()
       text_input = data['text']
       voice_font = data['voice']
       tts = synthesize.TextToSpeech(text_input, voice_font)
       tts.get_token()
       audio_response = tts.save_audio()
       return audio_response
   ```

### <a name="update-indexhtml"></a>Aktualizowanie pliku `index.html`

Teraz, gdy masz funkcję konwertowania tekstu na mowę i trasę w aplikacji Flask, aby ją wywołać, następnym krokiem jest rozpoczęcie pisania kodu HTML dla aplikacji. W poniższym html robi kilka rzeczy:

* Zapewnia wybór głosu z listy rozwijanej
* Dodaje przycisk do konwertowania tekstu na mowę
* Dodaje element audio, który jest używany do odtwarzania syntetyzowanej mowy

1. Otwórz `index.html` i znajdź te komentarze do kodu:
   ```html
   <!-- Start voice font selection code -->

   <!-- End voice font selection code -->
   ```

2. Zastąp komentarze kodu tym blokiem HTML:
   ```html
   <div class="form-group">
     <label for="select-voice"><strong>Select voice font:</strong></label>
     <select class="form-control" id="select-voice">
       <option value="(ar-SA, Naayf)">Arabic | Male | Naayf</option>
       <option value="(ca-ES, HerenaRUS)">Catalan | Female | HerenaRUS</option>
       <option value="(zh-CN, HuihuiRUS)">Chinese (Mainland) | Female | HuihuiRUS</option>
       <option value="(zh-CN, Kangkang, Apollo)">Chinese (Mainland) | Male | Kangkang, Apollo</option>
       <option value="(zh-HK, Tracy, Apollo)">Chinese (Hong Kong)| Female | Tracy, Apollo</option>
       <option value="(zh-HK, Danny, Apollo)">Chinese (Hong Kong) | Male | Danny, Apollo</option>
       <option value="(zh-TW, Yating, Apollo)">Chinese (Taiwan)| Female | Yaiting, Apollo</option>
       <option value="(zh-TW, Zhiwei, Apollo)">Chinese (Taiwan) | Male | Zhiwei, Apollo</option>
       <option value="(hr-HR, Matej)">Croatian | Male | Matej</option>
       <option value="(en-US, Jessa24kRUS)">English (US) | Female | Jessa24kRUS</option>
       <option value="(en-US, Guy24kRUS)">English (US) | Male | Guy24kRUS</option>
       <option value="(en-IE, Sean)">English (IE) | Male | Sean</option>
       <option value="(fr-FR, Julie, Apollo)">French | Female | Julie, Apollo</option>
       <option value="(fr-FR, HortenseRUS)">French | Female | Julie, HortenseRUS</option>
       <option value="(fr-FR, Paul, Apollo)">French | Male | Paul, Apollo</option>
       <option value="(de-DE, Hedda)">German | Female | Hedda</option>
       <option value="(de-DE, HeddaRUS)">German | Female | HeddaRUS</option>
       <option value="(de-DE, Stefan, Apollo)">German | Male | Apollo</option>
       <option value="(el-GR, Stefanos)">Greek | Male | Stefanos</option>
       <option value="(he-IL, Asaf)">Hebrew (Isreal) | Male | Asaf</option>
       <option value="(hi-IN, Kalpana, Apollo)">Hindi | Female | Kalpana, Apollo</option>
       <option value="(hi-IN, Hemant)">Hindi | Male | Hemant</option>
       <option value="(it-IT, LuciaRUS)">Italian | Female | LuciaRUS</option>
       <option value="(it-IT, Cosimo, Apollo)">Italian | Male | Cosimo, Apollo</option>
       <option value="(ja-JP, Ichiro, Apollo)">Japanese | Male | Ichiro</option>
       <option value="(ja-JP, HarukaRUS)">Japanese | Female | HarukaRUS</option>
       <option value="(ko-KR, HeamiRUS)">Korean | Female | Haemi</option>
       <option value="(pt-BR, HeloisaRUS)">Portuguese (Brazil) | Female | HeloisaRUS</option>
       <option value="(pt-BR, Daniel, Apollo)">Portuguese (Brazil) | Male | Daniel, Apollo</option>
       <option value="(pt-PT, HeliaRUS)">Portuguese (Portugal) | Female | HeliaRUS</option>
       <option value="(ru-RU, Irina, Apollo)">Russian | Female | Irina, Apollo</option>
       <option value="(ru-RU, Pavel, Apollo)">Russian | Male | Pavel, Apollo</option>
       <option value="(ru-RU, EkaterinaRUS)">Russian | Female | EkaterinaRUS</option>
       <option value="(es-ES, Laura, Apollo)">Spanish | Female | Laura, Apollo</option>
       <option value="(es-ES, HelenaRUS)">Spanish | Female | HelenaRUS</option>
       <option value="(es-ES, Pablo, Apollo)">Spanish | Male | Pablo, Apollo</option>
       <option value="(th-TH, Pattara)">Thai | Male | Pattara</option>
       <option value="(tr-TR, SedaRUS)">Turkish | Female | SedaRUS</option>
       <option value="(vi-VN, An)">Vietnamese | Male | An</option>
     </select>
   </div>
   ```

3. Następnie zlokalizuj te komentarze do kodu:
   ```html
   <!-- Add Speech Synthesis button and audio element -->

   <!-- End Speech Synthesis button -->
   ```

4. Zastąp komentarze kodu tym blokiem HTML:

```html
<button type="submit" class="btn btn-primary mb-2" id="text-to-speech">Convert text-to-speech</button>
<div id="audio-playback">
  <audio id="audio" controls>
    <source id="audio-source" type="audio/mpeg" />
  </audio>
</div>
```

5. Pamiętaj, aby zapisać swoją pracę.

### <a name="update-mainjs"></a>Aktualizowanie pliku `main.js`

W poniższym kodzie zawartość z kodu HTML jest używana do konstruowania żądania do trasy Flask. W szczególności tłumaczenie i czcionka głosowa są przypisane do zmiennych, a `text-to-speech` następnie przekazywane w żądaniu do trasy.

Kod następnie iteruje za pośrednictwem odpowiedzi i aktualizuje HTML z wynikami tonacji.

1. Z ide, utwórz `main.js` plik `static` o nazwie w katalogu.
2. Skopiuj ten kod do: `static/scripts/main.js`
   ```javascript
   // Convert text-to-speech
   $("#text-to-speech").on("click", function(e) {
     e.preventDefault();
     var ttsInput = document.getElementById("translation-result").value;
     var ttsVoice = document.getElementById("select-voice").value;
     var ttsRequest = { 'text': ttsInput, 'voice': ttsVoice }

     var xhr = new XMLHttpRequest();
     xhr.open("post", "/text-to-speech", true);
     xhr.setRequestHeader("Content-Type", "application/json");
     xhr.responseType = "blob";
     xhr.onload = function(evt){
       if (xhr.status === 200) {
         audioBlob = new Blob([xhr.response], {type: "audio/mpeg"});
         audioURL = URL.createObjectURL(audioBlob);
         if (audioURL.length > 5){
           var audio = document.getElementById("audio");
           var source = document.getElementById("audio-source");
           source.src = audioURL;
           audio.load();
           audio.play();
         }else{
           console.log("An error occurred getting and playing the audio.")
         }
       }
     }
     xhr.send(JSON.stringify(ttsRequest));
   });
   // Code for automatic language selection goes here.
   ```
3. To już prawie koniec. Ostatnią rzeczą, którą zamierzasz zrobić, to `main.js` dodać kod, aby automatycznie wybrać czcionkę głosową na podstawie języka wybranego do tłumaczenia. Dodaj ten blok `main.js`kodu do:
   ```javascript
   // Automatic voice font selection based on translation output.
   $('select[id="select-language"]').change(function(e) {
     if ($(this).val() == "ar"){
       document.getElementById("select-voice").value = "(ar-SA, Naayf)";
     }
     if ($(this).val() == "ca"){
       document.getElementById("select-voice").value = "(ca-ES, HerenaRUS)";
     }
     if ($(this).val() == "zh-Hans"){
       document.getElementById("select-voice").value = "(zh-HK, Tracy, Apollo)";
     }
     if ($(this).val() == "zh-Hant"){
       document.getElementById("select-voice").value = "(zh-HK, Tracy, Apollo)";
     }
     if ($(this).val() == "hr"){
       document.getElementById("select-voice").value = "(hr-HR, Matej)";
     }
     if ($(this).val() == "en"){
       document.getElementById("select-voice").value = "(en-US, Jessa24kRUS)";
     }
     if ($(this).val() == "fr"){
       document.getElementById("select-voice").value = "(fr-FR, HortenseRUS)";
     }
     if ($(this).val() == "de"){
       document.getElementById("select-voice").value = "(de-DE, HeddaRUS)";
     }
     if ($(this).val() == "el"){
       document.getElementById("select-voice").value = "(el-GR, Stefanos)";
     }
     if ($(this).val() == "he"){
       document.getElementById("select-voice").value = "(he-IL, Asaf)";
     }
     if ($(this).val() == "hi"){
       document.getElementById("select-voice").value = "(hi-IN, Kalpana, Apollo)";
     }
     if ($(this).val() == "it"){
       document.getElementById("select-voice").value = "(it-IT, LuciaRUS)";
     }
     if ($(this).val() == "ja"){
       document.getElementById("select-voice").value = "(ja-JP, HarukaRUS)";
     }
     if ($(this).val() == "ko"){
       document.getElementById("select-voice").value = "(ko-KR, HeamiRUS)";
     }
     if ($(this).val() == "pt"){
       document.getElementById("select-voice").value = "(pt-BR, HeloisaRUS)";
     }
     if ($(this).val() == "ru"){
       document.getElementById("select-voice").value = "(ru-RU, EkaterinaRUS)";
     }
     if ($(this).val() == "es"){
       document.getElementById("select-voice").value = "(es-ES, HelenaRUS)";
     }
     if ($(this).val() == "th"){
       document.getElementById("select-voice").value = "(th-TH, Pattara)";
     }
     if ($(this).val() == "tr"){
       document.getElementById("select-voice").value = "(tr-TR, SedaRUS)";
     }
     if ($(this).val() == "vi"){
       document.getElementById("select-voice").value = "(vi-VN, An)";
     }
   });
   ```

### <a name="test-your-app"></a>Testowanie aplikacji

Przetestujmy syntezę mowy w aplikacji.

```
flask run
```

Przejdź do podanego adresu serwera. Wpisz tekst w obszarze wprowadzania, wybierz język i naciśnij przycisk Tłumacz. Powinieneś otrzymać tłumaczenie. Następnie wybierz głos, a następnie naciśnij przycisk konwertuj tekst na mowę. tłumaczenie powinno być odtwarzane jako syntetyzowana mowa. Jeśli to nie zadziała, upewnij się, że dodano klucz subskrypcji.

> [!TIP]
> Jeśli wprowadzone zmiany nie są wyświetlane lub aplikacja nie działa tak, jak tego oczekujesz, spróbuj wyczyścić pamięć podręczną lub otworzyć okno prywatne/incognito.

To wszystko, masz działającą aplikację, która wykonuje tłumaczenia, analizuje tonację i syntetyzowaną mowę. Naciśnij **klawisze CTRL + c,** aby zabić aplikację. Pamiętaj, aby sprawdzić inne [usługi Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/).

## <a name="get-the-source-code"></a>Uzyskiwanie kodu źródłowego

Kod źródłowy tego projektu jest dostępny w [usłudze GitHub.](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Flask-App-Tutorial)

## <a name="next-steps"></a>Następne kroki

* [Odwołanie do interfejsu API tłumaczenia tekstu w usłudze Translator](https://docs.microsoft.com/azure/cognitive-services/Translator/reference/v3-0-reference)
* [Dokumentacja interfejsu API analizy tekstu](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7)
* [Dokumentacja interfejsu API zamiany tekstu na mowę](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-text-to-speech)
