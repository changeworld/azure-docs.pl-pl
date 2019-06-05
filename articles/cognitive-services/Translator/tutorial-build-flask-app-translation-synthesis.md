---
title: 'Samouczek: Tworzenie aplikacji Flask tłumaczenie, syntetyzowania i Analizuj tekst — interfejs API tekstu usługi Translator'
titleSuffix: Azure Cognitive Services
description: W tym samouczku utworzysz aplikację sieci web opartych na Flask, która używa usług Azure Cognitive Services tłumaczenie tekstu, analizowanie tonacji i syntetyzowania przetłumaczonego tekstu na mowę. Naszym głównym celem jest kodu w języku Python i Flask trasy, umożliwiające naszej aplikacji. Firma Microsoft nie będzie poświęcać dużo czasu na język JavaScript, który kontroluje aplikacji, ale zapewnia wszystkich plików do wglądu.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: tutorial
ms.date: 06/04/2019
ms.author: erhopf
ms.openlocfilehash: 4df2de7f0428ffe35712a29bfef645b6feb66813
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2019
ms.locfileid: "66515005"
---
# <a name="tutorial-build-a-flask-app-with-azure-cognitive-services"></a>Samouczek: Tworzenie aplikacji Flask za pomocą usług Azure Cognitive Services

W tym samouczku utworzysz aplikację sieci web Flask, która używa usług Azure Cognitive Services tłumaczenie tekstu, analizowanie tonacji i syntetyzowania przetłumaczonego tekstu na mowę. Naszym głównym celem jest kodu w języku Python i Flask trasy, umożliwiające naszej aplikacji, jednak firma Microsoft pomoże Ci za pomocą kodu HTML i Javascript, który zbiera aplikacji. Jeśli napotkasz problemy Daj nam znać, za pomocą przycisku opinii poniżej.

Poniżej przedstawiono, w tym samouczku opisano:

> [!div class="checklist"]
> * Uzyskiwanie kluczy subskrypcji platformy Azure
> * Konfigurowanie środowiska deweloperskiego i zainstaluj zależności
> * Tworzenie aplikacji Flask
> * Interfejs API tekstu usługi Translator umożliwia tłumaczenie tekstu
> * Korzystanie z analizy tekstu do analizy tonacji dodatnie i ujemne, wprowadzany tekst oraz tłumaczenia
> * Użyj usług przetwarzania mowy do konwertowania przetłumaczonego tekstu do syntezatora mowy
> * Uruchom aplikację Flask lokalnie

> [!TIP]
> Jeśli chcesz przejść od razu i wyświetlić cały kod od razu całego próbki, wraz z kompilacji instrukcje są dostępne w [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Flask-App-Tutorial).

## <a name="what-is-flask"></a>Co to jest Flask?

Flask jest microframework do tworzenia aplikacji sieci web. Oznacza to, że Flask zapewnia narzędzia, biblioteki i technologie, które umożliwiają tworzenie aplikacji sieci web. Ta aplikacja sieci web może być niektóre strony sieci web, blogu, stron typu wiki lub go jako merytorycznych jako aplikacja sieci web kalendarza lub komercyjnych witryn sieci Web.

Dla osób, które mają być zagłębia po w tym samouczku poniżej przedstawiono kilka pomocnych linków:

* [Dokumentacja Flask](http://flask.pocoo.org/)
* [Flask manekinów — przewodnik dla początkujących Flask](https://codeburst.io/flask-for-dummies-a-beginners-guide-to-flask-part-uno-53aec6afc5b1)

## <a name="prerequisites"></a>Wymagania wstępne

Omówmy teraz kluczy oprogramowania i subskrypcji, które należy w tym samouczku.

* [Język Python 3.5.2 lub nowszy](https://www.python.org/downloads/)
* [Narzędzia Git](https://git-scm.com/downloads)
* Środowisko IDE lub edytorze tekstów, takich jak [programu Visual Studio Code](https://code.visualstudio.com/) lub [Atom](https://atom.io/)  
* [Dla programu Chrome](https://www.google.com/chrome/browser/) lub [Firefox](https://www.mozilla.org/firefox)
* A **tekstu usługi Translator** klucz subskrypcji (Zauważ, że nie są wymagane w celu wybrania regionu.)
* A **analizy tekstu** klucz subskrypcji w **zachodnie stany USA** regionu.
* A **usług przetwarzania mowy** klucz subskrypcji w **zachodnie stany USA** regionu.

## <a name="create-an-account-and-subscribe-to-resources"></a>Tworzenie konta i subskrybowanie zasobów

Jak wspomniano wcześniej możesz zacząć wymagane są trzy klucze subskrypcji w ramach tego samouczka. Oznacza to, należy utworzyć zasób w ramach konta platformy Azure dla:
* Tekst usługi Translator
* Analiza tekstu
* Usługi mowy

Użyj [Utwórz konto Cognitive Services w witrynie Azure portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) instrukcje krok po kroku do tworzenia zasobów.

> [!IMPORTANT]
> Na potrzeby tego samouczka Utwórz swoje zasoby w regionie zachodnie stany USA. Jeśli używasz innego regionu, należy dostosować podstawowy adres URL w każdym z plików języka Python.

## <a name="set-up-your-dev-environment"></a>Konfigurowanie środowiska deweloperskiego

Przed utworzeniem aplikacji internetowej Flask, należy utworzyć katalog roboczy dla projektu i zainstalować kilka pakietów języka Python.

### <a name="create-a-working-directory"></a>Utwórz katalog roboczy

1. Otwórz wiersz polecenia (Windows) lub terminalu (z systemem macOS/Linux). Następnie można utworzyć, przejdź do katalogu roboczego i podkatalogi w projekcie:  

   ```
   mkdir -p flask-cog-services/static/scripts && mkdir flask-cog-services/templates
   ```
2. Zmień katalog roboczy projektu:  

   ```
   cd flask-cog-services
   ```

### <a name="create-and-activate-your-virtual-environment-with-virtualenv"></a>Utworzyć i aktywować w środowisku wirtualnym przy użyciu `virtualenv`

Utwórz środowisko wirtualne dla naszych Flask aplikacji za pomocą `virtualenv`. Użycie środowiska wirtualnego gwarantuje, czy masz czystego środowiska, aby działało z.

1. W katalogu roboczym, uruchom następujące polecenie, aby utworzyć środowisko wirtualne: **systemu macOS/Linux:**
   ```
   virtualenv venv --python=python3
   ```
   Firma Microsoft został zadeklarowany w sposób jawny, środowiska wirtualnego, należy użyć Python 3. Daje to gwarancję, że użytkownicy z wiele instalacji języka Python korzystają poprawnej wersji.

   **Windows CMD / Windows Bash:**
   ```
   virtualenv venv
   ```
   Aby zachować ich prostotę, firma Microsoft jest nazewnictwa Twojej venv środowiska wirtualnego.

2. Te polecenia, aby aktywować swoje środowisko wirtualne różnią się w zależności od platformy/powłoki:   

   | Platforma | Powłoka | Polecenie |
   |----------|-------|---------|
   | macOS/Linux | bash/zsh | `source venv/bin/activate` |
   | Windows | Bash | `source venv/Scripts/activate` |
   | | Wiersz polecenia | `venv\Scripts\activate.bat` |
   | | PowerShell | `venv\Scripts\Activate.ps1` |

   Po uruchomieniu tego polecenia z wiersza polecenia lub sesji terminalowej powinien być poprzedzony znakami `venv`.

3. Możesz dezaktywować sesji w dowolnym momencie, wpisując w wierszu polecenia lub terminalu to: `deactivate`.

> [!NOTE]
> Python ma obszerną dokumentację do tworzenia i zarządzania środowiskami wirtualnymi, zobacz [virtualenv](https://virtualenv.pypa.io/en/latest/).

### <a name="install-requests"></a>Zainstaluj żądań

Liczba żądań to popularny moduł, który służy do wysyłania żądań HTTP 1.1. Nie ma potrzeby ręcznie Dodaj ciągów zapytań do adresami URL lub formularza kodowanie danych POST.

1. Aby zainstalować żądań, uruchom polecenie:

   ```
   pip install requests
   ```

> [!NOTE]
> Jeśli chcesz dowiedzieć się więcej na temat żądań, zobacz [żądań: HTTP dla ludzi](http://docs.python-requests.org/en/master/).

### <a name="install-and-configure-flask"></a>Instalowanie i konfigurowanie Flask

Następnie należy zainstalować Flask. Flask obsługuje routing dla naszej aplikacji sieci web i pozwala na wykonywanie wywołań serwera serwera, które ukryć naszych klucze subskrypcji użytkownika końcowego.

1. Aby zainstalować Flask, uruchom polecenie:
   ```
   pip install Flask
   ```
   Upewnijmy się, że Flask został zainstalowany. Uruchomienie:
   ```
   flask --version
   ```
   Wersja powinien zostać wydrukowany do terminala. Czymkolwiek oznacza, że coś poszło źle.

2. Aby uruchomić aplikację Flask, możesz użyć polecenia flask lub przełącznika -m języka Python z Flask. Możesz to zrobić, musisz najpierw Poinformuj terminalu aplikacji, które chcesz pracować, eksportując `FLASK_APP` zmienną środowiskową:

   **macOS/Linux**:
   ```
   export FLASK_APP=app.py
   ```

   **Windows**:
   ```
   set FLASK_APP=app.py
   ```

## <a name="create-your-flask-app"></a>Tworzenie aplikacji Flask

W tej sekcji możesz zacząć tworzenie aplikacji Flask podstawowe, która zwraca plik HTML, gdy użytkownicy trafić katalogu głównego aplikacji. Nie poświęcać zbyt dużo czasu, próby pobrania od siebie kod, wrócimy można później zaktualizować ten plik.

### <a name="what-is-a-flask-route"></a>Co to jest trasa Flask?

Poświęćmy chwilę, aby porozmawiać na temat "[trasy](http://flask.pocoo.org/docs/1.0/api/#flask.Flask.route)". Routing służy do tworzenia powiązania adres URL do określonych funkcji. Flask używa dekoratory trasy, aby zarejestrować funkcji do określonych adresów URL. Na przykład, gdy użytkownik przejdzie do katalogu głównego (`/`) aplikacji sieci web, `index.html` jest renderowany.  

```python
@app.route('/')
def index():
    return render_template('index.html')
```

Spójrzmy na więcej przykład hammer tej głównej.

```python
@app.route('/about')
def about():
    return render_template('about.html')
```

Ten kod zapewnia, że gdy użytkownik przechodzi do `http://your-web-app.com/about` , `about.html` pliku jest renderowany.

Chociaż te przykłady ilustrują sposób renderowania stron html dla użytkownika, trasy można również do wywoływania interfejsów API, gdy przycisk jest wciśnięty lub wykonać dowolną liczbę akcji bez konieczności opuścić stronę główną. Zobaczysz to w akcji podczas tworzenia trasy dla tłumaczenia, tonację i synteza mowy.

### <a name="get-started"></a>Rozpoczęcie pracy

1. Otwórz projekt w środowisku IDE, a następnie utwórz plik o nazwie `app.py` w głównym katalogu roboczego. Następnie skopiuj ten kod do `app.py` i Zapisz:

   ```python
   from flask import Flask, render_template, url_for, jsonify, request

   app = Flask(__name__)
   app.config['JSON_AS_ASCII'] = False

   @app.route('/')
   def index():
       return render_template('index.html')
   ```

   Ten blok kodu informuje aplikację, aby wyświetlić `index.html` zawsze, gdy użytkownik przejdzie do katalogu głównego aplikacji sieci web (`/`).

2. Następnie utworzymy frontonu dla naszej aplikacji sieci web. Utwórz plik o nazwie `index.html` w `templates` katalogu. Następnie skopiuj ten kod do `templates/index.html`.

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

3. Teraz przetestuj aplikację Flask. W terminalu Uruchom polecenie:

   ```
   flask run
   ```

4. Otwórz przeglądarkę i przejdź do adresu URL, pod warunkiem. Powinien zostać wyświetlony aplikację jednostronicową. Naciśnij klawisz **Ctrl + c** do zakończenia aplikacji.

## <a name="translate-text"></a>Tłumaczenie tekstu

Teraz, gdy masz pomysł na prostej aplikacji Flask działania umożliwia:

* Zapis niektóre Python wywołania interfejsu API tekstu usługi Translator i zwróć odpowiedź
* Utwórz trasę do wywoływania z kodu w języku Python Flask
* Zaktualizuj kod HTML za pomocą obszaru wprowadzania tekstu i translację, selektor języka i tłumaczenie przycisku
* Pisanie kodu Javascript, który pozwala użytkownikom na interakcję z aplikacji Flask za pomocą kodu HTML

### <a name="call-the-translator-text-api"></a>Wywołaj interfejs API tekstu usługi Translator

Pierwszą rzeczą, jaką należy wykonać to pisanie funkcji do wywołania interfejsu API tłumaczenia tekstu. Tej funkcji potrwa dwa argumenty: `text_input` i `language_output`. Ta funkcja jest wywoływana zawsze wtedy, gdy użytkownik naciśnie przycisk translate w swojej aplikacji. Obszar tekstu w formacie HTML jest wysyłany jako `text_input`, a wartość wybór języka, w kodzie HTML jest wysyłany jako `language_output`.

1. Zacznijmy od utworzenia pliku o nazwie `translate.py` w głównym katalogu roboczego.
2. Następnie dodaj ten kod w celu `translate.py`. Ta funkcja przyjmuje dwa argumenty: `text_input` i `language_output`.
   ```python
   import os, requests, uuid, json

   # Don't forget to replace with your Cog Services subscription key!
   # If you prefer to use environment variables, see Extra Credit for more info.
   subscription_key = 'YOUR_TRANSLATOR_TEXT_SUBSCRIPTION_KEY'

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
3. Dodaj klucz subskrypcji tekstu usługi Translator i Zapisz.

### <a name="add-a-route-to-apppy"></a>Dodać trasę do `app.py`

Następnie musisz utworzyć trasę w swojej aplikacji Flask, która wywołuje `translate.py`. Ta trasa będzie być wywoływana za każdym razem, użytkownik naciśnie przycisk translate w swojej aplikacji.

Dla tej aplikacji będzie akceptować trasy `POST` żądań. Jest to spowodowane funkcja oczekuje tekstu do przetłumaczenia i język danych wyjściowych do tłumaczenia.

Flask udostępnia funkcje pomocnicze, które ułatwiają analizowanie i zarządzanie nimi każdego żądania. W kodzie `get_json()` zwraca dane z `POST` żądania w formacie JSON. Następnie przy użyciu `data['text']` i `data['to']`, wartości język tekstu i dane wyjściowe są przekazywane do `get_translation()` funkcji dostępne `translate.py`. Ostatnim krokiem jest do zwrócenia w odpowiedzi JSON, ponieważ będzie potrzebne wyświetlić te dane w aplikacji sieci web.

W poniższych sekcjach należy powtórzyć ten proces pomocne podczas tworzenia trasy dla tonacji syntezy analizy i mowy.

1. Otwórz `app.py` i zlokalizuj instrukcję importu na początku `app.py` i Dodaj następujący wiersz:

   ```python
   import translate
   ```
   Teraz naszej aplikacji Flask za pomocą metody, które są dostępne za pośrednictwem `translate.py`.

2. Skopiuj ten kod na końcu `app.py` i Zapisz:

   ```python
   @app.route('/translate-text', methods=['POST'])
   def translate_text():
       data = request.get_json()
       text_input = data['text']
       translation_output = data['to']
       response = translate.get_translation(text_input, translation_output)
       return jsonify(response)
   ```

### <a name="update-indexhtml"></a>Aktualizacja `index.html`

Teraz, gdy funkcja tłumaczenie tekstu i tras w celu wywołania go aplikacji Flask, następnym krokiem jest zacznij tworzyć kod HTML dla aplikacji. Poniższy kod HTML wykonuje kilka rzeczy:

* Zapewnia obszar tekstu, w którym użytkownicy można wpisać tekst do tłumaczenia.
* Zawiera selektor języka.
* Zawiera elementy HTML do renderowania wykryty język i oceny zaufania zwrócił podczas tłumaczenia.
* Zapewnia obszar tekstu tylko do odczytu, gdzie wyświetlone dane wyjściowe tłumaczenia.
* Zawiera symbole zastępcze dla tonacji analizy i mowy syntezy kodu, który dodasz do tego pliku w dalszej części tego samouczka.

Zaktualizujmy `index.html`.

1. Otwórz `index.html` i Znajdź te komentarze w kodzie:
   ```html
   <!-- HTML provided in the following sections goes here. -->

   <!-- End -->
   ```

2. Komentarze w kodzie należy zastąpić ten blok kodu HTML:
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

Następnym krokiem jest do zapisania fragmentów kodu Javascript. Jest to most między trasy HTML i Flask.

### <a name="create-mainjs"></a>Utwórz `main.js`  

`main.js` Plik jest most między trasy HTML i Flask. Twoja aplikacja będzie używać kombinacji jQuery, wywołań Ajax i XMLHttpRequest do renderowania zawartości i podejmowania `POST` żądań trasy Flask.

W poniższym kodzie zawartość HTML jest używana do konstruowania żądania trasy Flask. W szczególności zawartość obszaru tekstu i selektor języka są przypisane do zmiennych i przekazywane w żądaniu `translate-text`.

Kod wykonuje iterację przez odpowiedzi i aktualizuje HTML przy użyciu translacji, wykryty język i współczynnik ufności.

1. Z poziomu środowiska IDE, Utwórz plik o nazwie `main.js` w `static/scripts` katalogu.
2. Skopiuj ten kod do `static/scripts/main.js`:
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

### <a name="test-translation"></a>Test translation

Możemy przetestować tłumaczenia w aplikacji.

```
flask run
```

Przejdź do adresu podanego serwera. Wpisz tekst do wejściowego obszaru, wybierz język, a następnie naciśnij klawisz tłumaczenie. Powinna pojawić się tłumaczenia. Jeśli to nie zadziała, upewnij się, że dodano klucz subskrypcji.

> [!TIP]
> Jeśli nie są wyświetlane zmiany wprowadzone lub aplikacji nie działają w ten sposób oczekujesz, spróbuj wyczyścić pamięć podręczną lub otwierając okno w trybie prywatnym/incognito.

Naciśnij klawisz **CTRL + c** do zakończenia aplikacji, a następnie przejdź do następnej sekcji.

## <a name="analyze-sentiment"></a>Analiza tonacji

[Interfejsu API analizy tekstu](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) może służyć do przeprowadzenia analizy tonacji, wyodrębnianie kluczowych fraz z pliku tekstowego lub wykrywanie języka źródłowego. W tej aplikacji będziemy używać analizy tonacji do określenia, czy podany tekst jest pozytywny, neutralne lub ujemna. Interfejs API zwraca wynik liczbowy z zakresu od 0 do 1. Wyniki zbliżone do wartości 1 wskazują na pozytywną tonację, a wyniki zbliżone do wartości 0 wskazują na negatywną tonację.

W tej sekcji więc chcesz wykonać kilka czynności:

* Zapis niektóre języka Python do wywoływania interfejsu API analizy tekstu, aby przeprowadzić analizę tonacji i zwróć odpowiedź
* Utwórz trasę do wywoływania z kodu w języku Python Flask
* Zaktualizuj kod HTML z obszarem wyników tonacji i przycisk do przeprowadzenia analizy
* Pisanie kodu Javascript, który pozwala użytkownikom na interakcję z aplikacji Flask za pomocą kodu HTML

### <a name="call-the-text-analytics-api"></a>Wywoływanie interfejsu API usługi Text Analytics

Napiszmy funkcja do wywołania interfejsu API analizy tekstu. Ta funkcja spowoduje przejście cztery argumenty: `input_text`, `input_language`, `output_text`, i `output_language`. Ta funkcja jest wywoływana zawsze wtedy, gdy użytkownik naciśnie przycisk Analiza tonacji wykonywania w aplikacji. Dane dostarczone przez użytkownika z selektora obszaru i język tekstu, a także wykrytego języka i tłumaczenie dane wyjściowe są dostarczane z każdym żądaniem. Obiekt odpowiedzi zawiera wyniki tonacji dla źródła i tłumaczenia. W poniższych sekcjach możesz zacząć zapisu fragmentów kodu Javascript, aby przeanalizować odpowiedzi i używać go w aplikacji. Teraz możemy skoncentrować się na wywołania interfejsu API analizy tekstu.

1. Utwórz plik o nazwie `sentiment.py` w głównym katalogu roboczego.
2. Następnie dodaj ten kod w celu `sentiment.py`.
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
3. Dodaj klucz subskrypcji analizy tekstu, a następnie zapisz.

### <a name="add-a-route-to-apppy"></a>Dodać trasę do `app.py`

Utwórz trasę w swojej aplikacji Flask, która wywołuje `sentiment.py`. Ta trasa będzie być wywoływana za każdym razem, użytkownik naciśnie przycisk Analiza tonacji wykonywania w aplikacji. Jak trasy do tłumaczenia zamierza zaakceptować ta trasa `POST` żądania, ponieważ funkcja oczekuje argumentów.

1. Otwórz `app.py` i zlokalizuj instrukcję importu na początku `app.py` i zaktualizować go:

   ```python
   import translate, sentiment
   ```
   Teraz naszej aplikacji Flask za pomocą metody, które są dostępne za pośrednictwem `sentiment.py`.

2. Skopiuj ten kod na końcu `app.py` i Zapisz:
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

### <a name="update-indexhtml"></a>Aktualizacja `index.html`

Teraz, gdy funkcja uruchamiać analizę tonacji i trasę w aplikacji Flask i wywołać go, następnym krokiem jest do rozpoczęcia pisania kodu HTML dla aplikacji. Poniższy kod HTML wykonuje kilka rzeczy:

* Dodaje przycisk do swojej aplikacji, jak przeprowadzać analizę tonacji
* Dodaje element, który objaśnia, ocenianie opinii
* Dodaje element do wyświetlenia wyników tonacji

1. Otwórz `index.html` i Znajdź te komentarze w kodzie:
   ```html
   <!-- Start sentiment code-->

   <!-- End sentiment code -->
   ```

2. Komentarze w kodzie należy zastąpić ten blok kodu HTML:
   ```html
   <button type="submit" class="btn btn-primary mb-2" id="sentiment-analysis">Run sentiment analysis</button></br>
   <div id="sentiment" style="display: none">
      <p>Sentiment scores are provided on a 1 point scale. The closer the sentiment score is to 1, indicates positive sentiment. The closer it is to 0, indicates negative sentiment.</p>
      <strong>Sentiment score for input:</strong> <span id="input-sentiment"></span><br />
      <strong>Sentiment score for translation:</strong> <span id="translation-sentiment"></span>
   </div>
   ```

### <a name="update-mainjs"></a>Aktualizacja `main.js`

W poniższym kodzie zawartość HTML jest używana do konstruowania żądania trasy Flask. W szczególności zawartość obszaru tekstu i selektor języka są przypisane do zmiennych i przekazywane w żądaniu `sentiment-analysis` trasy.

Kod wykonuje iterację przez odpowiedzi i aktualizuje HTML przy użyciu wyników tonacji.

1. Z poziomu środowiska IDE, Utwórz plik o nazwie `main.js` w `static` katalogu.

2. Skopiuj ten kod do `static/scripts/main.js`:
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

### <a name="test-sentiment-analysis"></a>Analiza tonacji testu

Możemy przetestować analiza opinii w aplikacji.

```
flask run
```

Przejdź do adresu podanego serwera. Wpisz tekst do wejściowego obszaru, wybierz język, a następnie naciśnij klawisz tłumaczenie. Powinna pojawić się tłumaczenia. Następnie naciśnij przycisk Analiza tonacji wykonywania. Powinny być widoczne dwa wyniki. Jeśli to nie zadziała, upewnij się, że dodano klucz subskrypcji.

> [!TIP]
> Jeśli nie są wyświetlane zmiany wprowadzone lub aplikacji nie działają w ten sposób oczekujesz, spróbuj wyczyścić pamięć podręczną lub otwierając okno w trybie prywatnym/incognito.

Naciśnij klawisz **CTRL + c** do zakończenia aplikacji, a następnie przejdź do następnej sekcji.

## <a name="convert-text-to-speech"></a>Zamiana tekstu na mowę

[Interfejsu API zamiany tekstu na mowę](https://docs.microsoft.com/azure/cognitive-services/speech-service/text-to-speech) umożliwia aplikacji do konwertowania tekstu do naturalnym przypominającej ludzką syntezatora mowy. Usługa obsługuje standardowe, neuronowych i niestandardowe głosów. Nasza Przykładowa aplikacja korzysta z kilku dostępnych głosów pełną listę, zobacz [obsługiwane języki](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#text-to-speech).

W tej sekcji więc chcesz wykonać kilka czynności:

* Zapis niektóre języka Python, aby przekonwertować zamiany tekstu na mowę przy użyciu interfejsu API zamiany tekstu na mowę
* Utwórz trasę do wywoływania z kodu w języku Python Flask
* Zaktualizuj kod HTML przy użyciu przycisku, aby przekonwertować zamiany tekstu na mowę i element do odtwarzania dźwięku
* Pisanie kodu Javascript, który pozwala użytkownikom na interakcję z aplikacją Flask

### <a name="call-the-text-to-speech-api"></a>Wywoływanie interfejsu API zamiany tekstu na mowę

Napiszmy funkcję, aby skonwertować zamiany tekstu na mowę. Tej funkcji potrwa dwa argumenty: `input_text` i `voice_font`. Ta funkcja jest wywoływana zawsze wtedy, gdy użytkownik naciśnie przycisku Konwertuj tekst na mowę w swojej aplikacji. `input_text` jest to produkt wyjściowy tłumaczenia, zwracany przez wywołanie umożliwia tłumaczenie tekstu, `voice_font` jest wartością z selektora czcionki głosowe w kodzie HTML.

1. Utwórz plik o nazwie `synthesize.py` w głównym katalogu roboczego.

2. Następnie dodaj ten kod w celu `synthesize.py`.
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
3. Dodaj klucz subskrypcji usług przetwarzania mowy i Zapisz.

### <a name="add-a-route-to-apppy"></a>Dodać trasę do `app.py`

Utwórz trasę w swojej aplikacji Flask, która wywołuje `synthesize.py`. Ta trasa zostanie wywołana w każdym naciśnięciu przycisku Konwertuj tekst na mowę w swojej aplikacji. Jak trasy tłumaczenia i analiza opinii, zamierza zaakceptować tę trasę `POST` żądania, ponieważ funkcja oczekuje dwóch argumentów: tekst, który ma syntetyzowania i czcionka głosowa do odtwarzania.

1. Otwórz `app.py` i zlokalizuj instrukcję importu na początku `app.py` i zaktualizować go:

   ```python
   import translate, sentiment, synthesize
   ```
   Teraz naszej aplikacji Flask za pomocą metody, które są dostępne za pośrednictwem `synthesize.py`.

2. Skopiuj ten kod na końcu `app.py` i Zapisz:

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

### <a name="update-indexhtml"></a>Aktualizacja `index.html`

Teraz, gdy funkcja konwersji zamiany tekstu na mowę i tras w celu wywołania go aplikacji Flask, następnym krokiem jest do rozpoczęcia pisania kodu HTML dla aplikacji. Poniższy kod HTML wykonuje kilka rzeczy:

* Umożliwia wybór głosu listy rozwijanej
* Dodaje przycisk do przekonwertowania zamiany tekstu na mowę
* Dodaje element audio, który służy do odtwarzania syntezatora mowy

1. Otwórz `index.html` i Znajdź te komentarze w kodzie:
   ```html
   <!-- Start voice font selection code -->

   <!-- End voice font selection code -->
   ```

2. Komentarze w kodzie należy zastąpić ten blok kodu HTML:
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

3. Następnie znajdź te komentarze w kodzie:
   ```html
   <!-- Add Speech Synthesis button and audio element -->

   <!-- End Speech Synthesis button -->
   ```

4. Komentarze w kodzie należy zastąpić ten blok kodu HTML:

```html
<button type="submit" class="btn btn-primary mb-2" id="text-to-speech">Convert text-to-speech</button>
<div id="audio-playback">
  <audio id="audio" controls>
    <source id="audio-source" type="audio/mpeg" />
  </audio>
</div>
```

5. Upewnij się zapisać swoją pracę.

### <a name="update-mainjs"></a>Aktualizacja `main.js`

W poniższym kodzie zawartość HTML jest używana do konstruowania żądania trasy Flask. W szczególności tłumaczenie czcionka głosowa są przypisane do zmiennych i przekazywane w żądaniu `text-to-speech` trasy.

Kod wykonuje iterację przez odpowiedzi i aktualizuje HTML przy użyciu wyników tonacji.

1. Z poziomu środowiska IDE, Utwórz plik o nazwie `main.js` w `static` katalogu.
2. Skopiuj ten kod do `static/scripts/main.js`:
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
3. To już prawie koniec. Ostatnim zadaniem, które zamierzasz wykonać, będzie dodanie kodu w celu `main.js` do automatycznego wybierania czcionki głosowej, oparte na języku, który został wybrany do tłumaczenia. Ten blok kodu, aby dodać `main.js`:
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

Możemy przetestować synteza mowy w aplikacji.

```
flask run
```

Przejdź do adresu podanego serwera. Wpisz tekst do wejściowego obszaru, wybierz język, a następnie naciśnij klawisz tłumaczenie. Powinna pojawić się tłumaczenia. Następnie wybierz głos, a następnie kliknij przycisk Konwertuj tekst na mowę. Tłumaczenie powinny być odtwarzane jako syntezatora mowy. Jeśli to nie zadziała, upewnij się, że dodano klucz subskrypcji.

> [!TIP]
> Jeśli nie są wyświetlane zmiany wprowadzone lub aplikacji nie działają w ten sposób oczekujesz, spróbuj wyczyścić pamięć podręczną lub otwierając okno w trybie prywatnym/incognito.

To wszystko, masz działającą aplikację, która wykonuje tłumaczenia, analizę tonacji i syntezatora mowy. Naciśnij klawisz **CTRL + c** do zakończenia aplikacji. Należy zapoznać się z innymi [usług Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/).

## <a name="get-the-source-code"></a>Uzyskiwanie kodu źródłowego

Kod źródłowy dla tego projektu jest dostępna w [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Flask-App-Tutorial).

## <a name="next-steps"></a>Kolejne kroki

* [Odwołanie do interfejsu API tłumaczenia tekstu w usłudze Translator](https://docs.microsoft.com/azure/cognitive-services/Translator/reference/v3-0-reference)
* [Dokumentacja interfejsu API analizy tekstu](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7)
* [Dokumentacja interfejsu API zamiany tekstu na mowę](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-text-to-speech)
