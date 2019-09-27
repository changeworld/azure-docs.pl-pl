---
title: 'Samouczek: Uruchamianie czytnika immersyjny przy użyciu języka Python'
titleSuffix: Azure Cognitive Services
description: W tym samouczku utworzysz aplikację w języku Python, która uruchamia czytnik immersyjny.
services: cognitive-services
author: dylankil
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: tutorial
ms.date: 08/02/2019
ms.author: dylankil
ms.openlocfilehash: 6404a5d49bd7af1ed5d74299f03eda8d0bb14b89
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/27/2019
ms.locfileid: "71326449"
---
# <a name="tutorial-launch-the-immersive-reader-using-the-python-sample-project"></a>Samouczek: Uruchamianie czytnika immersyjny przy użyciu przykładowego projektu języka Python

W [przeglądzie](./overview.md)zawarto informacje na temat tego, co to jest czytnik immersyjny i w jaki sposób implementuje sprawdzone techniki w celu zwiększenia czytelności dla osób uczące się, nowych czytelników i studentów z różnicami w nauce. W tym samouczku opisano sposób tworzenia aplikacji sieci Web w języku Python, która uruchamia czytnik immersyjny. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie aplikacji sieci Web w języku Python za pomocą PIP, kolby, jinja i virtualenv przy użyciu przykładowego projektu
> * Uzyskiwanie tokenu dostępu
> * Uruchom czytnik immersyjny z przykładową zawartością

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

* Zasób czytnika immersyjny skonfigurowany do uwierzytelniania Azure Active Directory (Azure AD). Postępuj zgodnie z [tymi instrukcjami](./azure-active-directory-authentication.md) , aby rozpocząć konfigurację. Podczas konfigurowania właściwości środowiska będą potrzebne pewne wartości. Zapisz dane wyjściowe sesji w pliku tekstowym do użycia w przyszłości.
* [Usługa Git](https://git-scm.com/)
* [Zestaw SDK czytnika immersyjny](https://github.com/microsoft/immersive-reader-sdk)
* [Python](https://www.python.org/downloads/) i [PIP](https://docs.python.org/3/installing/index.html). Począwszy od języka Python 3,4, narzędzie PIP jest domyślnie dołączone do instalatorów binarnych języka Python.
* [Flask](https://flask.palletsprojects.com/en/1.0.x/)
* [Jinja](http://jinja.pocoo.org/docs/2.10/)
* [virtualenv](https://virtualenv.pypa.io/en/latest/) i [virtualenvwrapper — win dla systemu Windows](https://pypi.org/project/virtualenvwrapper-win/) lub [virtualenvwrapper dla OSX](https://virtualenvwrapper.readthedocs.io/en/latest/)
* [Moduł żądań](https://pypi.org/project/requests/2.7.0/)
* IDE, takie jak [Visual Studio Code](https://code.visualstudio.com/)

## <a name="acquire-an-azure-ad-authentication-token"></a>Uzyskiwanie tokenu uwierzytelniania usługi Azure AD

Napisz interfejs API zaplecza, aby pobrać token uwierzytelniania usługi Azure AD.

Wymagane są pewne wartości z kroku wymagań wstępnych konfiguracji uwierzytelniania usługi Azure AD powyżej dla tej części. Odwołaj się do pliku tekstowego zapisanego w tej sesji.

````text
TenantId     => Azure subscription TenantId
ClientId     => Azure AD ApplicationId
ClientSecret => Azure AD Application Service Principal password
Subdomain    => Immersive Reader resource subdomain (resource 'Name' if the resource was created in the Azure portal, or 'CustomSubDomain' option if the resource was created with Azure CLI Powershell. Check the Azure portal for the subdomain on the Endpoint in the resource Overview page, for example, 'https://[SUBDOMAIN].cognitiveservices.azure.com/')
````

Po uzyskaniu tych wartości Utwórz nowy plik o nazwie _ENV_i wklej do niego następujący kod, dostarczając wartości właściwości niestandardowych z powyższych. Zastąp. _ENV._ plik w przykładowej aplikacji z nowo utworzonym plikiem.

```text
TENANT_ID={YOUR_TENANT_ID}
CLIENT_ID={YOUR_CLIENT_ID}
CLIENT_SECRET={YOUR_CLIENT_SECRET}
SUBDOMAIN={YOUR_SUBDOMAIN}
```

Nie należy zatwierdzić tego pliku w kontroli źródła, ponieważ zawiera on klucze tajne, które nie powinny być publiczne.

Punkt końcowy interfejsu API **getimmersivereadertoken** powinien być zabezpieczony za pomocą uwierzytelniania (na przykład [OAuth](https://oauth.net/2/)), aby uniemożliwić nieautoryzowanym użytkownikom uzyskanie tokenów, które mają być używane z usługą czytnika danych immersyjny i rozliczeniami. Ta praca wykracza poza zakres tego samouczka.

## <a name="create-a-python-web-app-on-windows"></a>Tworzenie aplikacji sieci Web w języku Python w systemie Windows

Utwórz aplikację sieci Web w języku Python przy użyciu `flask` w systemie Windows.

[Zainstaluj oprogramowanie Git](https://git-scm.com/).

Po zainstalowaniu usługi git Otwórz wiersz polecenia i "Klonuj" repozytorium git zestawu SDK dla czytnika immersyjny do folderu na komputerze

```cmd
git clone https://github.com/microsoft/immersive-reader-sdk.git
```

Zainstaluj język [Python](https://www.python.org/downloads/).

Zaznacz pole wyboru Dodaj język Python do ścieżki.

![Okno dialogowe instalacji systemu Windows w języku Python — krok 1](./media/pythoninstallone.jpg)

Dodaj funkcje opcjonalne, zaznaczając pola, a następnie klikając przycisk "dalej".

![Okno dialogowe instalacji systemu Windows w języku Python — krok 2](./media/pythoninstalltwo.jpg)

Wybierz pozycję "Instalacja niestandardowa" i Ustaw ścieżkę instalacji jako folder główny, np. `C:\Python37-32\`, a następnie kliknij przycisk "Zainstaluj".

![Okno dialogowe instalacji systemu Windows w języku Python — krok 3](./media/pythoninstallthree.jpg)

Po zakończeniu instalacji języka Python Otwórz wiersz polecenia i `cd` do folderu skryptów języka Python.

```cmd
cd C:\Python37-32\Scripts
```

Zainstaluj kolbę.

```cmd
pip install flask
```

Zainstaluj Jinja2. W pełni polecany aparat szablonów dla języka Python.

```cmd
pip install jinja2
```

Zainstaluj virtualenv. Narzędzie do tworzenia izolowanych środowisk Python.

```cmd
pip install virtualenv
```

Zainstaluj virtualenvwrapper-win. Pomysłem za virtualenvwrapper jest ułatwienie użycia virtualenv.

```cmd
pip install virtualenvwrapper-win
```

Zainstaluj moduł żądania. Żądania to Apache2a licencjonowana Biblioteka HTTP, zapisywana w języku Python.

```cmd
pip install requests
```

Zainstaluj moduł Python-dotenv. Ten moduł odczytuje parę klucz-wartość z pliku ENV i dodaje je do zmiennej środowiskowej.

```cmd
pip install python-dotenv
```

Tworzenie środowiska wirtualnego

```cmd
mkvirtualenv advanced-python
```

`cd` do folderu głównego projektu przykładowego.

```cmd
cd C:\immersive-reader-sdk\js\samples\advanced-python
```

Połącz przykładowy projekt ze środowiskiem. To mapuje nowo utworzone środowisko wirtualne do folderu głównego projektu przykładowego.

```cmd
setprojectdir .
```

Aktywuj środowisko wirtualne.

```cmd
activate
```

Projekt powinien być teraz aktywny i zobaczysz, jak `(advanced-python) C:\immersive-reader-sdk\js\samples\advanced-python>` w wierszu polecenia.

Dezaktywuj środowisko.

```cmd
deactivate
```

Prefiks `(advanced-python)` powinien teraz zostać usunięty, ponieważ środowisko jest teraz dezaktywowane.

Aby ponownie uaktywnić środowisko `workon advanced-python` z przykładowego folderu głównego projektu.

```cmd
workon advanced-python
```

### <a name="launch-the-immersive-reader-with-sample-content"></a>Uruchom czytnik immersyjny z przykładową zawartością

Gdy środowisko jest aktywne, uruchom przykładowy projekt, wprowadzając `flask run` z folderu głównego projektu przykładowego.

```cmd
flask run
```

Otwórz przeglądarkę i przejdź do _http://localhost:5000_ .

## <a name="create-a-python-web-app-on-osx"></a>Tworzenie aplikacji sieci Web w języku Python w witrynie OSX

Utwórz aplikację sieci Web w języku Python przy użyciu `flask` w OSX.

[Zainstaluj oprogramowanie Git](https://git-scm.com/).

Po zainstalowaniu usługi git Otwórz Terminal i "Klonuj" repozytorium git zestawu SDK czytnika immersyjny do folderu na komputerze

```bash
git clone https://github.com/microsoft/immersive-reader-sdk.git
```

Zainstaluj język [Python](https://www.python.org/downloads/).

Folder główny Python, np. `Python37-32` powinien teraz znajdować się w folderze aplikacji.

Po zakończeniu instalacji języka Python Otwórz pozycję Terminal i `cd` w folderze skryptów języka Python.

```bash
cd immersive-reader-sdk/js/samples/advanced-python
```

Zainstaluj program pip.

```bash
curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py
```

Następnie uruchom następujące polecenie, aby zainstalować narzędzie PIP dla obecnie zalogowanego użytkownika, aby uniknąć problemów z uprawnieniami.

```bash
python get-pip.py --user
```

```bash
sudo nano /etc/paths
```

- Po wyświetleniu monitu wprowadź hasło.
- Dodaj ścieżkę instalacji PIP do zmiennej PATH.
- Przejdź do dolnej części pliku, a następnie wprowadź ścieżkę, którą chcesz dodać jako ostatni element listy, np. `PATH=$PATH:/usr/local/bin`.
- Naciśnij klawisz Control — x, aby zakończyć.
- Wprowadź `Y`, aby zapisać zmodyfikowany bufor.
- To wszystko! Aby go przetestować, w nowym oknie terminalu wpisz: `echo $PATH`.

Zainstaluj kolbę.

```bash
pip install flask --user
```

Zainstaluj Jinja2. W pełni polecany aparat szablonów dla języka Python.

```bash
pip install Jinja2 --user
```

Zainstaluj virtualenv. Narzędzie do tworzenia izolowanych środowisk Python.

```bash
pip install virtualenv --user
```

Zainstaluj virtualenvwrapper. Pomysłem za virtualenvwrapper jest ułatwienie użycia virtualenv.

```bash
pip install virtualenvwrapper --user
```

Zainstaluj moduł żądania. Żądania to Apache2a licencjonowana Biblioteka HTTP, zapisywana w języku Python.

```bash
pip install requests --user
```

Zainstaluj moduł Python-dotenv. Ten moduł odczytuje parę klucz-wartość z pliku ENV i dodaje je do zmiennej środowiskowej.

```bash
pip install python-dotenv --user
```

Wybierz folder, w którym chcesz zachować swoje środowiska wirtualne, i Uruchom to polecenie

```bash
mkdir ~/.virtualenvs
```

`cd` do folderu przykładowej aplikacji zestawu SDK Python dla czytnika immersyjny.

```bash
cd immersive-reader-sdk/js/samples/advanced-python
```

Tworzenie środowiska wirtualnego

```bash
mkvirtualenv -p /usr/local/bin/python3 advanced-python
```

Połącz przykładowy projekt ze środowiskiem. To mapuje nowo utworzone środowisko wirtualne do folderu głównego projektu przykładowego.

```bash
setprojectdir .
```

Aktywuj środowisko wirtualne.

```bash
activate
```

Projekt powinien być teraz aktywny i zobaczysz, jak `(advanced-python) /immersive-reader-sdk/js/samples/advanced-python>` w wierszu polecenia.

Dezaktywuj środowisko.

```bash
deactivate
```

Prefiks `(advanced-python)` powinien teraz zostać usunięty, ponieważ środowisko jest teraz dezaktywowane.

Aby ponownie uaktywnić środowisko `workon advanced-python` z przykładowego folderu głównego projektu.

```bash
workon advanced-python
```

## <a name="launch-the-immersive-reader-with-sample-content"></a>Uruchom czytnik immersyjny z przykładową zawartością

Gdy środowisko jest aktywne, uruchom przykładowy projekt, wprowadzając `flask run` z folderu głównego projektu przykładowego.

```bash
flask run
```

Otwórz przeglądarkę i przejdź do _http://localhost:5000_ .

## <a name="next-steps"></a>Następne kroki

* Poznaj [zestaw SDK czytnika immersyjny](https://github.com/microsoft/immersive-reader-sdk) i [Kompendium zestawu SDK czytnika immersyjny](./reference.md)
* Wyświetl przykłady kodu w witrynie [GitHub](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/)
