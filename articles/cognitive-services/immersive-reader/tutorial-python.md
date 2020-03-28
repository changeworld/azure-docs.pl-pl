---
title: 'Samouczek: Uruchom wciągający czytnik za pomocą Pythona'
titleSuffix: Azure Cognitive Services
description: W tym samouczku utworzysz aplikację języka Python, która uruchamia czytnik Immersive.
services: cognitive-services
author: dylankil
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: tutorial
ms.date: 01/14/2020
ms.author: dylankil
ms.openlocfilehash: a252afae0a007ee0b791b56d19ffb0685848d30a
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76844364"
---
# <a name="tutorial-launch-the-immersive-reader-using-the-python-sample-project"></a>Samouczek: Uruchom program Immersive Reader przy użyciu przykładowego projektu języka Python

W [przeglądzie](./overview.md)dowiedziałeś się o tym, czym jest Immersive Reader i jak wdraża sprawdzone techniki poprawy rozumienia czytania dla osób uczących się języków, nowych czytelników i uczniów z różnicami w uczeniu się. W tym samouczku opisano sposób tworzenia aplikacji sieci web języka Python, która uruchamia program Immersive Reader. Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie aplikacji internetowej Języka Python z pip, flask, Jinja i virtualenv przy użyciu przykładowego projektu
> * Uzyskiwanie tokenu dostępu
> * Uruchom czytnik Immersive Reader z przykładową zawartością

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

* Zasób programu Immersive Reader skonfigurowany do uwierzytelniania usługi Azure Active Directory. Postępuj zgodnie [z tymi instrukcjami,](./how-to-create-immersive-reader.md) aby skonfigurować. Podczas konfigurowania właściwości środowiska potrzebne będą niektóre wartości utworzone w tym miejscu. Zapisz dane wyjściowe sesji w pliku tekstowym do wykorzystania w przyszłości.
* [Git](https://git-scm.com/)
* [Wciągający czytnik SDK](https://github.com/microsoft/immersive-reader-sdk)
* [Python](https://www.python.org/downloads/) i [pip](https://docs.python.org/3/installing/index.html). Począwszy od Pythona 3.4, pip jest domyślnie dołączony do instalatorów binarnych Pythona.
* [Kolby](https://flask.palletsprojects.com/en/1.0.x/)
* [Jinja](http://jinja.pocoo.org/docs/2.10/)
* [virtualenv](https://virtualenv.pypa.io/en/latest/) i [virtualenvwrapper-win dla Windows](https://pypi.org/project/virtualenvwrapper-win/) lub [virtualenvwrapper dla OSX](https://virtualenvwrapper.readthedocs.io/en/latest/)
* [moduł żądań](https://pypi.org/project/requests/2.7.0/)
* Ide, takie jak [Visual Studio Code](https://code.visualstudio.com/)

## <a name="configure-authentication-credentials"></a>Konfigurowanie poświadczeń uwierzytelniania

Utwórz nowy plik o nazwie _.env_i wklej do niego następujący kod, podając wartości podane podczas tworzenia zasobu programu Immersive Reader.

```text
TENANT_ID={YOUR_TENANT_ID}
CLIENT_ID={YOUR_CLIENT_ID}
CLIENT_SECRET={YOUR_CLIENT_SECRET}
SUBDOMAIN={YOUR_SUBDOMAIN}
```

Pamiętaj, aby nie zatwierdzać tego pliku do kontroli źródła, ponieważ zawiera on wpisy tajne, które nie powinny być upublicznione.

Punkt końcowy interfejsu API **getimmersivereadertoken** powinien być zabezpieczony za jakąś formą uwierzytelniania (na przykład [OAuth),](https://oauth.net/2/)aby uniemożliwić nieautoryzowanym użytkownikom uzyskanie tokenów do użycia w usłudze Immersive Reader i rozliczeniach; że praca wykracza poza zakres tego samouczka.

## <a name="create-a-python-web-app-on-windows"></a>Tworzenie aplikacji internetowej języka Python w systemie Windows

Utwórz aplikację internetową `flask` języka Python w systemie Windows.

Zainstaluj [Git](https://git-scm.com/).

Po zainstalowaniu git otwórz wiersz polecenia i "sklonuj" repozytorium Immersive Reader SDK Git do folderu na komputerze

```cmd
git clone https://github.com/microsoft/immersive-reader-sdk.git
```

Zainstaluj [Pythona](https://www.python.org/downloads/).

Zaznacz pole Dodaj pythona do ścieżki.

![Okno dialogowe 1 instalacji systemu Python](./media/pythoninstallone.jpg)

Dodaj funkcje opcjonalne, zaznaczając pola, a następnie kliknij przycisk "Dalej".

![Okno dialogowe 2 instalacji systemu Python](./media/pythoninstalltwo.jpg)

Wybierz opcję "Instalacja niestandardowa" i ustaw ścieżkę `C:\Python37-32\` instalacji jako folder główny, a następnie kliknij przycisk "Zainstaluj".

![Okno dialogowe 3 instalacji systemu Python](./media/pythoninstallthree.jpg)

Po zakończeniu instalacji języka Python `cd` otwórz wiersz polecenia i folder Skrypty Języka Python.

```cmd
cd C:\Python37-32\Scripts
```

Zainstaluj Kolbę.

```cmd
pip install flask
```

Zainstaluj Jinja2. W pełni funkcjonalny silnik szablonów dla Pythona.

```cmd
pip install jinja2
```

Zainstaluj virtualenv. Narzędzie do tworzenia izolowanych środowisk Języka Python.

```cmd
pip install virtualenv
```

Zainstaluj virtualenvwrapper-win. Ideą virtualenvwrapper jest ułatwienie korzystania z virtualenv.

```cmd
pip install virtualenvwrapper-win
```

Zainstaluj moduł żądań. Żądania to licencjonowana biblioteka HTTP Apache2, napisana w Pythonie.

```cmd
pip install requests
```

Zainstaluj moduł python-dotenv. Ten moduł odczytuje parę klucz-wartość z pliku .env i dodaje je do zmiennej środowiskowej.

```cmd
pip install python-dotenv
```

Skondytomać środowisko wirtualne

```cmd
mkvirtualenv advanced-python
```

`cd`do przykładowego folderu głównego projektu.

```cmd
cd C:\immersive-reader-sdk\js\samples\advanced-python
```

Połącz przykładowy projekt ze środowiskiem. Spowoduje to mapowanie nowo utworzonego środowiska wirtualnego do przykładowego folderu głównego projektu.

```cmd
setprojectdir .
```

Aktywuj środowisko wirtualne.

```cmd
activate
```

Projekt powinien być teraz aktywny, a `(advanced-python) C:\immersive-reader-sdk\js\samples\advanced-python>` zobaczysz coś podobnego w wierszu polecenia.

Dezaktywuj środowisko.

```cmd
deactivate
```

`(advanced-python)` Prefiks powinien teraz zniknąć, ponieważ środowisko jest teraz dezaktywowane.

Aby ponownie uaktywnić środowisko uruchomione `workon advanced-python` z przykładowego folderu głównego projektu.

```cmd
workon advanced-python
```

### <a name="launch-the-immersive-reader-with-sample-content"></a>Uruchom czytnik Immersive Reader z przykładową zawartością

Gdy środowisko jest aktywne, uruchom przykładowy `flask run` projekt, wprowadzając z przykładowego folderu głównego projektu.

```cmd
flask run
```

Otwórz przeglądarkę i _http://localhost:5000_przejdź do pliku .

## <a name="create-a-python-web-app-on-osx"></a>Tworzenie aplikacji internetowej Języka Python na OSX

Utwórz aplikację internetową `flask` języka Python przy użyciu systemu OSX.

Zainstaluj [Git](https://git-scm.com/).

Po zainstalowaniu git otwórz terminal i "klon" repozytorium Immersive Reader SDK Git do folderu na komputerze

```bash
git clone https://github.com/microsoft/immersive-reader-sdk.git
```

Zainstaluj [Pythona](https://www.python.org/downloads/).

Folder główny Języka Python, `Python37-32` np.

Po zakończeniu instalacji języka `cd` Python otwórz terminal i folder Skrypty Języka Python.

```bash
cd immersive-reader-sdk/js/samples/advanced-python
```

Zainstaluj program pip.

```bash
curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py
```

Następnie uruchom następujące czynności, aby zainstalować pip dla aktualnie zalogowany użytkownik, aby uniknąć problemów z uprawnieniami.

```bash
python get-pip.py --user
```

```bash
sudo nano /etc/paths
```

- Po wyświetleniu monitu wprowadź hasło.
- Dodaj ścieżkę instalacji pip do zmiennej PATH.
- Przejdź do dolnej części pliku i wprowadź ścieżkę, którą chcesz dodać jako `PATH=$PATH:/usr/local/bin`ostatni element listy np.
- Naciśnij control-x, aby zamknąć.
- Wprowadź, `Y` aby zapisać zmodyfikowany bufor.
- Gotowe. Aby go przetestować, w nowym `echo $PATH`oknie terminala wpisz: .

Zainstaluj Kolbę.

```bash
pip install flask --user
```

Zainstaluj Jinja2. W pełni funkcjonalny silnik szablonów dla Pythona.

```bash
pip install Jinja2 --user
```

Zainstaluj virtualenv. Narzędzie do tworzenia izolowanych środowisk Języka Python.

```bash
pip install virtualenv --user
```

Zainstaluj virtualenvwrapper. Ideą virtualenvwrapper jest ułatwienie korzystania z virtualenv.

```bash
pip install virtualenvwrapper --user
```

Zainstaluj moduł żądań. Żądania to licencjonowana biblioteka HTTP Apache2, napisana w Pythonie.

```bash
pip install requests --user
```

Zainstaluj moduł python-dotenv. Ten moduł odczytuje parę klucz-wartość z pliku .env i dodaje je do zmiennej środowiskowej.

```bash
pip install python-dotenv --user
```

Wybierz folder, w którym chcesz zachować środowiska wirtualne i uruchom to polecenie

```bash
mkdir ~/.virtualenvs
```

`cd`do przykładowego folderu aplikacji Immersive Reader SDK Python.

```bash
cd immersive-reader-sdk/js/samples/advanced-python
```

Skondytomać środowisko wirtualne

```bash
mkvirtualenv -p /usr/local/bin/python3 advanced-python
```

Połącz przykładowy projekt ze środowiskiem. Spowoduje to mapowanie nowo utworzonego środowiska wirtualnego do przykładowego folderu głównego projektu.

```bash
setprojectdir .
```

Aktywuj środowisko wirtualne.

```bash
activate
```

Projekt powinien być teraz aktywny, a `(advanced-python) /immersive-reader-sdk/js/samples/advanced-python>` zobaczysz coś podobnego w wierszu polecenia.

Dezaktywuj środowisko.

```bash
deactivate
```

`(advanced-python)` Prefiks powinien teraz zniknąć, ponieważ środowisko jest teraz dezaktywowane.

Aby ponownie uaktywnić środowisko uruchomione `workon advanced-python` z przykładowego folderu głównego projektu.

```bash
workon advanced-python
```

## <a name="launch-the-immersive-reader-with-sample-content"></a>Uruchom czytnik Immersive Reader z przykładową zawartością

Gdy środowisko jest aktywne, uruchom przykładowy `flask run` projekt, wprowadzając z przykładowego folderu głównego projektu.

```bash
flask run
```

Otwórz przeglądarkę i _http://localhost:5000_przejdź do pliku .

## <a name="next-steps"></a>Następne kroki

* Poznaj [immersyjny moduł SDK czytnika](https://github.com/microsoft/immersive-reader-sdk) i [immersyjny moduł SDK czytnika](./reference.md)
* Wyświetlanie przykładów kodu w [usłudze GitHub](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/)
