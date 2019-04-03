---
title: Samouczek dotyczący aplikacji internetowej platformy Python Flask dla usługi Azure Cosmos DB
description: Przejrzyj samouczek bazy danych na temat korzystania z usługi Azure Cosmos DB w celu przechowywania i uzyskiwania dostępu do danych z aplikacji internetowej platformy Python Flask hostowanej na platformie Azure. Znajdź rozwiązania do tworzenia aplikacji.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: python
ms.topic: tutorial
ms.date: 02/23/2017
ms.author: sngun
ms.openlocfilehash: 24426a4249934af1d85f43552617cda7c1d7e172
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2019
ms.locfileid: "58883891"
---
# <a name="build-a-python-flask-web-application-using-azure-cosmos-db"></a>Tworzenie aplikacji internetowej platformy Python Flask za pomocą usługi Azure Cosmos DB

> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [Java](sql-api-java-application.md)
> * [Node.js](sql-api-nodejs-application.md)
> * [Python](sql-api-python-application.md)
> * [Xamarin](mobile-apps-with-xamarin.md)
> 

W tym samouczku pokazano, jak przy użyciu usługi Azure Cosmos DB przechowywać dane i uzyskiwać do nich dostęp z poziomu aplikacji internetowej Python Flask hostowanej w usłudze Azure App Service. Ten samouczek zakłada, że masz już pewne doświadczenie w korzystaniu z witryn internetowych Python i Azure.

Ten samouczek bazy danych obejmuje następujące zagadnienia:

1. Tworzenie i aprowizacja konta usługi Azure Cosmos DB.
2. Tworzenie aplikacji Python Flask.
3. Nawiązywanie połączenia z usługą Azure Cosmos DB i korzystanie z niej z poziomu aplikacji internetowej.
4. Wdrażanie aplikacji internetowej w usłudze Azure App Service.

Wykonując poszczególne kroki tego samouczka, utworzysz prostą aplikację do głosowania, która umożliwia głosowanie w ankiecie.

![Zrzut ekranu aplikacji do głosowania utworzone w ramach tego samouczka bazy danych](./media/sql-api-python-application/cosmos-db-pythonr-run-application.png)

## <a name="database-tutorial-prerequisites"></a>Wymagania wstępne dotyczące samouczka
Przed wykonaniem instrukcji zawartych w tym artykule upewnij się, że masz następujące elementy:

* [Subskrypcja platformy Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* Program [Visual Studio 2017](https://www.visualstudio.com/downloads/) z pakietami roboczymi **Programowanie na platformie Azure** i **Opracowywanie zawartości w języku Python**. Możesz sprawdzić, czy wstępnie wymagane oprogramowanie jest zainstalowane i zainstalować je, otwierając **Instalator programu Visual Studio** lokalnie.   
* [Zestaw SDK platformy Microsoft Azure dla języka Python w wersji 2.7](https://azure.microsoft.com/downloads/). 
* [Python 2.7](https://www.python.org/downloads/windows/). Możesz użyć instalacji 32-bitowej lub 64-bitowej.

> [!IMPORTANT]
> Jeśli instalujesz środowisko Python w wersji 2.7 po raz pierwszy, upewnij się, że na ekranie Customize Python 2.7.13 (Dostosowywanie środowiska Python 2.7.11) wybrano pozycję **Add python.exe to Path** (Dodaj plik python.exe do ścieżki).
> 
> ![Zrzut ekranu przedstawiający ekran Dostosowywanie środowiska Python 2.7.11, w którym należy wybrać Add python.exe do ścieżki](./media/sql-api-python-application/cosmos-db-python-install.png)
> 
> 

* [Microsoft Visual C++ Compiler for Python 2.7](https://www.microsoft.com/en-us/download/details.aspx?id=44266).

## <a name="step-1-create-an-azure-cosmos-db-database-account"></a>Krok 1. Tworzenie konta bazy danych usługi Azure Cosmos DB
Zacznijmy od utworzenia konta usługi Azure Cosmos DB. Jeśli masz już konto lub jeśli korzystasz z emulatora usługi Azure Cosmos DB na potrzeby tego samouczka, możesz od razu przejść do sekcji [Krok 2. Tworzenie nowej aplikacji internetowej platformy Python Flask](#step-2-create-a-new-python-flask-web-application).

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

<br/>
Teraz przejdziemy przez proces tworzenia nowej aplikacji internetowej platformy Python Flask od podstaw.

## <a name="step-2-create-a-new-python-flask-web-application"></a>Krok 2. Tworzenie nowej aplikacji internetowej platformy Python Flask
1. W menu **Plik** programu Visual Studio wskaż pozycję **Nowy**, a następnie kliknij pozycję **Projekt**.
   
    Zostanie wyświetlone okno dialogowe **Nowy projekt**.
2. W lewym okienku rozwiń pozycję **Szablony** i **Python**, a następnie kliknij pozycję **Sieć Web**. 
3. W środkowym okienku wybierz pozycję **Projekt sieci Web platformy Flask**, a następnie w polu **Nazwa** wpisz wartość **tutorial** i kliknij przycisk **OK**. Pamiętaj, że wszystkie nazwy pakietów języka Python powinny być pisane małymi literami, zgodnie z opisem zawartym w publikacji [Style Guide for Python Code](https://www.python.org/dev/peps/pep-0008/#package-and-module-names) (Przewodnik po stylu kodu Python).
   
    Python Flask to platforma programistyczna aplikacji internetowych, która umożliwia szybsze tworzenie aplikacji internetowych w języku Python.
   
    ![Zrzut ekranu przedstawiający okno nowy projekt w programie Visual Studio z wyróżnioną po lewej stronie projektu sieci Web w języku Python Flask wybrane w środku oraz nazwą tutorial w polu Nazwa języka Python](./media/sql-api-python-application/image9.png)
4. W oknie **Python Tools for Visual Studio** kliknij pozycję **Zainstaluj w środowisku wirtualnym**. 
   
    ![Zrzut ekranu przedstawiający samouczek bazy danych — narzędzia Python Tools for Visual Studio okna](./media/sql-api-python-application/python-install-virtual-environment.png)
5. W oknie **Dodawanie środowiska wirtualnego** w polu wyboru interpretera wybierz środowisko Python 2.7 lub Python 3.5, zaakceptuj pozostałe ustawienia domyślne, a następnie kliknij pozycję **Utwórz**. Spowoduje to skonfigurowanie wymaganego środowiska wirtualnego Python dla Twojego projektu.
   
    ![Zrzut ekranu przedstawiający samouczek bazy danych — narzędzia Python Tools for Visual Studio okna](./media/sql-api-python-application/image10_A.png)
   
    Po pomyślnym zainstalowaniu środowiska w oknie Dane wyjściowe zostanie wyświetlona informacja `Successfully installed Flask-0.10.1 Jinja2-2.8 MarkupSafe-0.23 Werkzeug-0.11.5 itsdangerous-0.24 'requirements.txt' was installed successfully.`.

## <a name="step-3-modify-the-python-flask-web-application"></a>Krok 3. Modyfikowanie aplikacji internetowej platformy Python Flask
### <a name="add-the-python-flask-packages-to-your-project"></a>Dodawanie pakietów platformy Python Flask do projektu
Po skonfigurowaniu projektu musisz dodać do niego wymagane pakiety platformy Flask, w tym pydocumentdb — pakiet języka Python dla interfejsu API SQL usługi Azure Cosmos DB.

1. W Eksploratorze rozwiązań otwórz plik o nazwie **requirements.txt** i zastąp jego zawartość następującym kodem:
   
        flask==0.9
        flask-mail==0.7.6
        sqlalchemy==0.7.9
        flask-sqlalchemy==0.16
        sqlalchemy-migrate==0.7.2
        flask-whooshalchemy==0.55a
        flask-wtf==0.8.4
        pytz==2013b
        flask-babel==0.8
        flup
        pydocumentdb>=1.0.0
2. Zapisz plik **requirements.txt**. 
3. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy pozycję **env**, a następnie kliknij polecenie **Zainstaluj z pliku requirements.txt**.
   
    ![Zrzut ekranu przedstawiający env (Python 2.7) wybrano instalację z pliku requirements.txt wyróżnione na liście](./media/sql-api-python-application/cosmos-db-python-install-from-requirements.png)
   
    Po pomyślnej instalacji w oknie Dane wyjściowe zostaną wyświetlone następujące informacje:
   
        Successfully installed Babel-2.3.2 Tempita-0.5.2 WTForms-2.1 Whoosh-2.7.4 blinker-1.4 decorator-4.0.9 flask-0.9 flask-babel-0.8 flask-mail-0.7.6 flask-sqlalchemy-0.16 flask-whooshalchemy-0.55a0 flask-wtf-0.8.4 flup-1.0.2 pydocumentdb-1.6.1 pytz-2013b0 speaklater-1.3 sqlalchemy-0.7.9 sqlalchemy-migrate-0.7.2
   
   > [!NOTE]
   > W rzadkich przypadkach w oknie Dane wyjściowe może zostać wyświetlony błąd. Jeśli tak się stanie, sprawdź, czy błąd jest związany z oczyszczaniem. Czasami oczyszczanie może się nie powieść, ale instalacja i tak zakończy się pomyślnie (aby to sprawdzić, przewiń w górę okno Dane wyjściowe). Instalację można sprawdzić przez [zweryfikowanie środowiska wirtualnego](#verify-the-virtual-environment). Jeśli instalacja nie powiodła się, ale weryfikacja zakończyła się pomyślnie, można kontynuować.
   > 
   > 

### <a name="verify-the-virtual-environment"></a>Weryfikowanie środowiska wirtualnego
Upewnijmy się, że wszystko jest poprawnie zainstalowane.

1. Skompiluj rozwiązanie, naciskając klawisze **Ctrl**+**Shift**+**B**.
2. Gdy kompilacja zakończy się powodzeniem, uruchom witrynę sieci Web, naciskając klawisz **F5**. Powoduje to uruchomienie serwera programistycznego platformy Flask i przeglądarki sieci Web. Powinna zostać wyświetlona następująca strona.
   
    ![Pusty projekt aplikacji sieci Web platformy Python Flask wyświetlony w przeglądarce](./media/sql-api-python-application/image12.png)
3. Zatrzymaj debugowanie witryny sieci Web, naciskając klawisze **Shift**+**F5** w programie Visual Studio.

### <a name="create-database-collection-and-document-definitions"></a>Tworzenie definicji bazy danych, kolekcji i dokumentu
Teraz utwórzmy aplikację do głosowania przez dodanie nowych plików i zaktualizowanie pozostałych.

1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy projekt **tutorial**, kliknij polecenie **Dodaj**, a następnie kliknij pozycję **Nowy element**. Wybierz pozycję **Pusty plik Python** i nazwij ten plik **forms.py**.  
2. Dodaj następujący kod do pliku forms.py, a następnie zapisz plik.

```python
from flask_wtf import Form
from wtforms import RadioField

class VoteForm(Form):
    deploy_preference  = RadioField('Deployment Preference', choices=[
        ('Web Site', 'Web Site'),
        ('Cloud Service', 'Cloud Service'),
        ('Virtual Machine', 'Virtual Machine')], default='Web Site')
```


### <a name="add-the-required-imports-to-viewspy"></a>Dodawanie wymaganych importów do pliku views.py
1. W Eksploratorze rozwiązań rozwiń folder **tutorial**, a następnie otwórz plik **views.py**. 
2. Dodaj następujące instrukcje importu u góry pliku **views.py**, a następnie zapisz plik. Spowoduje to zaimportowanie zestawu SDK Python usługi Azure Cosmos DB oraz pakietów platformy Flask.
   
    ```python
    from forms import VoteForm
    import config_cosmos
    import pydocumentdb.document_client as document_client
    ```

### <a name="create-database-collection-and-document"></a>Tworzenie bazy danych, kolekcji i dokumentu
* Dodaj następujący kod na końcu pliku **views.py**. Odpowiada on za tworzenie bazy danych używanej przez formularz. Nie usuwaj żadnego kodu znajdującego się w pliku **views.py**. Po prostu dołącz podany kod na końcu.

```python
@app.route('/create')
def create():
    """Renders the contact page."""
    client = document_client.DocumentClient(config_cosmos.COSMOSDB_HOST, {'masterKey': config_cosmos.COSMOSDB_KEY})

    # Attempt to delete the database.  This allows this to be used to recreate as well as create
    try:
        db = next((data for data in client.ReadDatabases() if data['id'] == config_cosmos.COSMOSDB_DATABASE))
        client.DeleteDatabase(db['_self'])
    except:
        pass

    # Create database
    db = client.CreateDatabase({ 'id': config_cosmos.COSMOSDB_DATABASE })

    # Create collection
    collection = client.CreateCollection(db['_self'],{ 'id': config_cosmos.COSMOSDB_COLLECTION })

    # Create document
    document = client.CreateDocument(collection['_self'],
        { 'id': config_cosmos.COSMOSDB_DOCUMENT,
          'Web Site': 0,
          'Cloud Service': 0,
          'Virtual Machine': 0,
          'name': config_cosmos.COSMOSDB_DOCUMENT 
        })

    return render_template(
       'create.html',
        title='Create Page',
        year=datetime.now().year,
        message='You just created a new database, collection, and document.  Your old votes have been deleted')
```


### <a name="read-database-collection-document-and-submit-form"></a>Odczytywanie bazy danych, kolekcji i dokumentów oraz przesyłanie formularza
* Dodaj następujący kod na końcu pliku **views.py**. Odpowiada on za konfigurowanie formularza oraz odczytywanie bazy danych, kolekcji i dokumentu. Nie usuwaj żadnego kodu znajdującego się w pliku **views.py**. Po prostu dołącz podany kod na końcu.

```python
@app.route('/vote', methods=['GET', 'POST'])
def vote(): 
    form = VoteForm()
    replaced_document ={}
    if form.validate_on_submit(): # is user submitted vote  
        client = document_client.DocumentClient(config_cosmos.COSMOSDB_HOST, {'masterKey': config_cosmos.COSMOSDB_KEY})

        # Read databases and take first since id should not be duplicated.
        db = next((data for data in client.ReadDatabases() if data['id'] == config_cosmos.COSMOSDB_DATABASE))

        # Read collections and take first since id should not be duplicated.
        coll = next((coll for coll in client.ReadCollections(db['_self']) if coll['id'] == config_cosmos.COSMOSDB_COLLECTION))

        # Read documents and take first since id should not be duplicated.
        doc = next((doc for doc in client.ReadDocuments(coll['_self']) if doc['id'] == config_cosmos.COSMOSDB_DOCUMENT))

        # Take the data from the deploy_preference and increment our database
        doc[form.deploy_preference.data] = doc[form.deploy_preference.data] + 1
        replaced_document = client.ReplaceDocument(doc['_self'], doc)

        # Create a model to pass to results.html
        class VoteObject:
            choices = dict()
            total_votes = 0

        vote_object = VoteObject()
        vote_object.choices = {
            "Web Site" : doc['Web Site'],
            "Cloud Service" : doc['Cloud Service'],
            "Virtual Machine" : doc['Virtual Machine']
        }
        vote_object.total_votes = sum(vote_object.choices.values())

        return render_template(
            'results.html', 
            year=datetime.now().year, 
            vote_object = vote_object)

    else :
        return render_template(
            'vote.html', 
            title = 'Vote',
            year=datetime.now().year,
            form = form)
```


### <a name="create-the-html-files"></a>Tworzenie plików HTML
1. W Eksploratorze rozwiązań w folderze **tutorial** kliknij prawym przyciskiem myszy folder **templates**, kliknij polecenie **Dodaj**, a następnie kliknij pozycję **Nowy element**. 
2. Wybierz pozycję **Strona HTML**, a następnie w polu nazwy wpisz **create.html**. 
3. Powtórz kroki 1 i 2, aby utworzyć dwa dodatkowe pliki HTML: results.html i vote.html.
4. Dodaj następujący kod do pliku **create.html** w elemencie `<body>`. Służy do wyświetlania komunikatu informującego o tym, że utworzyliśmy nową bazę danych, kolekcję i dokument.
   
    ```html
    {% extends "layout.html" %}
    {% block content %}
    <h2>{{ title }}.</h2>
    <h3>{{ message }}</h3>
    <p><a href="{{ url_for('vote') }}" class="btn btn-primary btn-large">Vote &raquo;</a></p>
    {% endblock %}
    ```
5. Dodaj następujący kod do pliku **results.html** w elemencie `<body`>. Służy do wyświetlania wyników ankiety.
   
    ```html
    {% extends "layout.html" %}
    {% block content %}
    <h2>Results of the vote</h2>
        <br />
   
    {% for choice in vote_object.choices %}
    <div class="row">
        <div class="col-sm-5">{{choice}}</div>
            <div class="col-sm-5">
                <div class="progress">
                    <div class="progress-bar" role="progressbar" aria-valuenow="{{vote_object.choices[choice]}}" aria-valuemin="0" aria-valuemax="{{vote_object.total_votes}}" style="width: {{(vote_object.choices[choice]/vote_object.total_votes)*100}}%;">
                                {{vote_object.choices[choice]}}
                </div>
            </div>
            </div>
    </div>
    {% endfor %}
   
    <br />
    <a class="btn btn-primary" href="{{ url_for('vote') }}">Vote again?</a>
    {% endblock %}
    ```
6. Dodaj następujący kod do pliku **vote.html** w elemencie `<body`>. Służy do wyświetlania ankiety i akceptowania oddanych głosów. Po zarejestrowaniu głosów kontrola jest przekazywana do pliku views.py, gdzie usługa Azure Cosmos DB rozpoznaje oddany głos i dołącza odpowiednie dane do dokumentu.
   
    ```html
    {% extends "layout.html" %}
    {% block content %}
    <h2>What is your favorite way to host an application on Azure?</h2>
    <form action="" method="post" name="vote">
        {{form.hidden_tag()}}
            {{form.deploy_preference}}
            <button class="btn btn-primary" type="submit">Vote</button>
    </form>
    {% endblock %}
    ```
7. W folderze **templates** zastąp zawartość pliku **index.html** poniższym kodem. Będzie to strona docelowa dla Twojej aplikacji.
   
    ```html
    {% extends "layout.html" %}
    {% block content %}
    <h2>Python + Azure Cosmos DB Voting Application.</h2>
    <h3>This is a sample Cosmos DB voting application using PyDocumentDB</h3>
    <p><a href="{{ url_for('create') }}" class="btn btn-primary btn-large">Create/Clear the Voting Database &raquo;</a></p>
    <p><a href="{{ url_for('vote') }}" class="btn btn-primary btn-large">Vote &raquo;</a></p>
    {% endblock %}
    ```

### <a name="add-a-configuration-file-and-change-the-initpy"></a>Dodawanie pliku konfiguracji i zmienianie pliku \_\_init\_\_.py
1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy projekt **tutorial**, kliknij polecenie **Dodaj**, kliknij pozycję **Nowy element**, a następnie wybierz opcję **Pusty plik Python** i podaj nazwę pliku **config_cosmos.py**. Ten plik konfiguracji jest wymagany przez formularze na platformie Flask. Można go również użyć, aby dostarczyć klucz tajny. Ten klucz nie jest jednak potrzebny w tym samouczku.
2. Dodaj poniższy kod do pliku config_cosmos.py. Konieczna będzie zmiana wartości **COSMOSDB\_HOST** i **COSMOSDB\_KEY** w następnym kroku.
   
    ```python
    CSRF_ENABLED = True
    SECRET_KEY = 'you-will-never-guess'
   
    COSMOSDB_HOST = 'https://YOUR_COSMOSDB_NAME.documents.azure.com:443/'
    COSMOSDB_KEY = 'YOUR_SECRET_KEY_ENDING_IN_=='
   
    COSMOSDB_DATABASE = 'voting database'
    COSMOSDB_COLLECTION = 'voting collection'
    COSMOSDB_DOCUMENT = 'voting document'
    ```
3. W witrynie [Azure Portal](https://portal.azure.com/) przejdź do strony **Klucze**, klikając kolejno pozycje **Przeglądaj** i **Konta usługi Azure Cosmos DB**, kliknij dwukrotnie nazwę konta, którego chcesz użyć, a następnie kliknij przycisk **Klucze** w obszarze **Podstawy**. Na stronie **Klucze** skopiuj wartość **URI** i wklej ją do pliku **config.py** jako wartość właściwości **COSMOSDB\_HOST**. 
4. Na stronie **Klucze** w witrynie Azure Portal skopiuj wartość **Klucz podstawowy** lub **Klucz pomocniczy** i wklej ją do pliku **config_cosmos.py** jako wartość właściwości **COSMOSDB\_KEY**.
5. W pliku **\_\_init\_\_.py** dodaj następujące wiersze zawierające funkcje odczytywanie pliku konfiguracji i podstawowe funkcje rejestrowania: 
   
        app.config.from_object('config_cosmos')
        logging.basicConfig(level=logging.INFO,format='%(asctime)s - %(name)s - %(levelname)s - %(message)s')
        logger = logging.getLogger(__name__)
   
    Plik powinien mieć następującą zawartość:
   
    ```python
    import logging
    from flask import Flask
    app = Flask(__name__)
    app.config.from_pyfile('config_cosmos')
    logging.basicConfig(level=logging.INFO,format='%(asctime)s - %(name)s - %(levelname)s - %(message)s')
    logger = logging.getLogger(__name__)

    import tutorial.views
    ```
6. Po dodaniu wszystkich plików Eksplorator rozwiązań powinien wyglądać następująco:
   
    ![Zrzut ekranu okna Eksploratora rozwiązań w usłudze Visual Studio](./media/sql-api-python-application/cosmos-db-python-solution-explorer.png)

## <a name="step-4-run-your-web-application-locally"></a>Krok 4: Uruchamianie aplikacji internetowej lokalnie
1. Skompiluj rozwiązanie, naciskając klawisze **Ctrl**+**Shift**+**B**.
2. Gdy kompilacja zakończy się powodzeniem, uruchom witrynę sieci Web, naciskając klawisz **F5**. Na ekranie powinna być widoczna następująca strona.
   
    ![Zrzut ekranu przedstawiający na języku Python i usługi Azure Cosmos DB aplikacji do głosowania wyświetlonej w przeglądarce sieci web](./media/sql-api-python-application/cosmos-db-pythonr-run-application.png)
3. Kliknij przycisk **Create/Clear the Voting Database** (Utwórz/wyczyść bazę danych głosowania), aby wygenerować bazę danych.
   
    ![Zrzut ekranu strony tworzenia aplikacji sieci web — szczegóły programowania](./media/sql-api-python-application/cosmos-db-python-run-create-page.png)
4. Następnie kliknij przycisk **Vote** (Głosuj) i wybierz jedną z opcji.
   
    ![Zrzut ekranu z pytaniem, aplikacji sieci web](./media/sql-api-python-application/cosmos-db-vote.png)
5. Każdy oddany głos powoduje zwiększenie odpowiedniego licznika.
   
    ![Zrzut ekranu przedstawiający wyniki wyświetlane strony głos](./media/sql-api-python-application/cosmos-db-voting-results.png)
6. Zatrzymaj debugowanie projektu przez naciśnięcie klawiszy Shift+F5.

## <a name="step-5-deploy-the-web-application-to-azure"></a>Krok 5. Wdrażanie aplikacji internetowej na platformie Azure
Teraz, gdy kompletna aplikacja działa poprawnie z usługą Azure Cosmos DB lokalnie, utworzymy plik web.config, zaktualizujemy pliki na serwerze, aby dopasować je do środowiska lokalnego, a następnie wyświetlimy ukończoną aplikację na platformie Azure. Poniższa procedura dotyczy programu Visual Studio 2017. Jeśli używasz innej wersji programu Visual Studio, zobacz [Publikowanie w usłudze Azure App Service](/visualstudio/python/publishing-to-azure).

1. W **Eksploratorze rozwiązań** programu Visual Studio kliknij prawym przyciskiem myszy projekt i wybierz pozycję **Dodaj > Nowy element**. W wyświetlonym oknie dialogowym wybierz szablon **Azure web.config (Fast CGI)**, a następnie wybierz pozycję **OK**. Spowoduje to utworzenie pliku `web.config` w katalogu głównym projektu. 

2. Zmodyfikuj sekcję `<system.webServer>` w pliku `web.config` tak, aby ścieżka odpowiadała instalacji języka Python. Na przykład w przypadku języka Python 2.7 x64 wpis powinien wyglądać tak:
    
    ```xml
    <system.webServer>
        <handlers>
            <add name="PythonHandler" path="*" verb="*" modules="FastCgiModule" scriptProcessor="D:\home\Python27\python.exe|D:\home\Python27\wfastcgi.py" resourceType="Unspecified" requireAccess="Script"/>
        </handlers>
    </system.webServer>
    ```

3. Ustaw wartość wpisu `WSGI_HANDLER` w pliku `web.config` na `tutorial.app`, aby odpowiadał on nazwie projektu. 

    ```xml
    <!-- Flask apps only: change the project name to match your app -->
    <add key="WSGI_HANDLER" value="tutorial.app"/>
    ```

4. W **Eksploratorze rozwiązań** programu Visual Studio rozwiń folder **tutorial**, kliknij prawym przyciskiem myszy folder `static`, wybierz pozycję **Dodaj > Nowy element**, wybierz szablon „Azure static files web.config”, a następnie wybierz pozycję **OK**. Ta akcja spowoduje utworzenie kolejnego pliku `web.config` w folderze `static`, co z kolei spowoduje wyłączenie przetwarzania języka Python dla tego folderu. Ta konfiguracja wysyła żądania dotyczące plików statycznych do domyślnego serwera internetowego, zamiast korzystać z aplikacji Python.

5. Zapisz pliki, a następnie kliknij prawym przyciskiem myszy projekt w Eksploratorze rozwiązań (upewnij się, że nie jest uruchomiony lokalnie) i wybierz polecenie **Publikuj**.  
   
     ![Zrzut ekranu przedstawiający pozycję tutorial wybraną w Eksploratorze rozwiązań z wyróżnioną opcją publikowania](./media/sql-api-python-application/image20.png)
6. W oknie dialogowym **Publikowanie** wybierz pozycję **Microsoft Azure App Service**, wybierz pozycję **Utwórz nowy**, a następnie kliknij pozycję **Publikuj**.
   
    ![Zrzut ekranu okna publikowanie w sieci Web z wyróżnioną pozycją program Microsoft Azure App Service](./media/sql-api-python-application/cosmos-db-python-publish.png)
7. W oknie dialogowym **Tworzenie usługi App Service** wprowadź nazwę aplikacji internetowej, uzupełnij pola **Subskrypcja**, **Grupa zasobów** i **Plan usługi App Service**, a następnie kliknij pozycję **Utwórz**.
   
    ![Zrzut ekranu przedstawiający okno okno aplikacji sieci Web programu Microsoft Azure](./media/sql-api-python-application/cosmos-db-python-create-app-service.png)
8. W ciągu kilku sekund program Visual Studio zakończy kopiowanie plików na serwer i wyświetli komunikat „Nie można wyświetlić strony, ponieważ wystąpił wewnętrzny błąd serwera” na stronie `http://<your app service>.azurewebsites.net/`.

9. W witrynie Azure Portal otwórz swoje nowe konto usługi App Service, a następnie w menu nawigacji przewiń w dół do sekcji **Narzędzia programistyczne**, wybierz pozycję **Rozszerzenia**, a następnie kliknij pozycję **+ Dodaj**.

10. Na stronie **Wybierz rozszerzenie** przewiń w dół do najnowszej instalacji języka Python 2.7 i wybierz opcję x86 lub x64, a następnie kliknij pozycję **OK**, aby zaakceptować postanowienia prawne.  
   
11. Za pomocą konsoli Kudu, którą możesz znaleźć pod adresem `https://<your app service name>.scm.azurewebsites.net/DebugConsole`, zainstaluj pakiety wymienione w pliku `requirements.txt` aplikacji. Aby to zrobić, w konsoli diagnostycznej Kudu przejdź do folderu Python `D:\home\Python27`, a następnie uruchom następujące polecenie zgodnie z opisem w sekcji [Kudu console (Konsola Kudu)](/visualstudio/python/managing-python-on-azure-app-service#azure-app-service-kudu-console):

    ```
    D:\home\Python27>python -m pip install --upgrade -r /home/site/wwwroot/requirements.txt
    ```          

12. Po zainstalowaniu nowych pakietów uruchom ponownie usługę App Service w witrynie Azure Portal, naciskając przycisk **Uruchom ponownie**. 

    > [!Tip] 
    > Jeśli wprowadzisz jakiekolwiek zmiany w pliku `requirements.txt` aplikacji, zainstaluj ponownie wszystkie pakiety wymienione w tym pliku, używając konsoli Kudu. 

13. Po całkowitym skonfigurowaniu środowiska serwera odśwież stronę w przeglądarce. Powinna zostać wyświetlona aplikacja internetowa.

    ![Wyniki publikowania aplikacji Bottle, Flask i Django w usłudze App Service](./media/sql-api-python-application/python-published-app-services.png)

    > [!Tip] 
    > Jeśli strona internetowa nie jest wyświetlana lub w dalszym ciągu jest widoczny komunikat „Nie można wyświetlić strony, ponieważ wystąpił wewnętrzny błąd serwera”, otwórz plik web.config w usłudze Kudu i dodaj wpis `<httpErrors errorMode="Detailed"></httpErrors>` w sekcji system.webServer, a następnie odśwież stronę. Spowoduje to dostarczenie szczegółowych informacji o błędzie do przeglądarki. 

## <a name="troubleshooting"></a>Rozwiązywanie problemów
Jeśli jest to pierwsza aplikacja napisana w języku Python uruchomiona na Twoim komputerze, upewnij się, że następujące foldery (lub odpowiadające im lokalizacje instalacji) są uwzględnione w zmiennej PATH:

    C:\Python27\site-packages;C:\Python27\;C:\Python27\Scripts;

Jeśli wystąpi błąd na stronie głosowania, a nazwa projektu jest inna niż **tutorial**, upewnij się, że plik **\_\_init\_\_.py** odwołuje się do właściwej nazwy projektu w wierszu: `import tutorial.view`.

## <a name="next-steps"></a>Następne kroki
Gratulacje! Udało Ci się utworzyć swoją pierwszą aplikację internetową w języku Python za pomocą usługi Azure Cosmos DB i opublikować ją na platformie Azure.

Aby dodać dodatkowe funkcje do aplikacji internetowej, zapoznaj się z interfejsami API dostępnymi w temacie [Azure Cosmos DB Python SDK](sql-api-sdk-python.md) (Zestaw SDK języka Python dla usługi Azure Cosmos DB).

Aby uzyskać więcej informacji na temat platformy Azure, programu Visual Studio i języka Python, zobacz [Centrum deweloperów języka Python](https://azure.microsoft.com/develop/python/). 

Dodatkowe samouczki dotyczące platformy Python Flask znajdziesz na stronie [The Flask Mega-Tutorial, Part I: Hello, World!](https://blog.miguelgrinberg.com/post/the-flask-mega-tutorial-part-i-hello-world) (Megasamouczek platformy Flask, część I: Witaj, świecie!). 
