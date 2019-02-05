---
title: Konfigurowanie aplikacji języka Python — Azure App Service
description: W tym samouczku opisano opcje tworzenia i konfigurowania aplikacji języka Python dla usługi Azure App Service w systemie Linux.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 01/29/2019
ms.author: astay;cephalin;kraigb
ms.custom: seodec18
ms.openlocfilehash: 416566ac52e8df6324cbf6146919df160deb0f98
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2019
ms.locfileid: "55220998"
---
# <a name="configure-your-python-app-for-azure-app-service"></a>Konfigurowanie aplikacji języka Python dla usługi Azure App Service
W tym artykule opisano, jak usługa [Azure App Service](app-service-linux-intro.md) uruchamia aplikacje języka Python i jak można dostosować zachowanie usługi App Service w razie potrzeby. Aplikacje języka Python należy wdrożyć ze wszystkimi wymaganymi modułami [pip](https://pypi.org/project/pip/). Aparat wdrażania usługi App Service (Kudu) automatycznie aktywuje środowisko wirtualne i uruchamia polecenie `pip install -r requirements.txt`, gdy wdrażasz [repozytorium Git](../deploy-local-git.md) lub [pakiet ZIP](../deploy-zip.md) z włączonymi procesami kompilacji.

> [!NOTE]
> [Język Python w wersji systemu Windows dla usługi App Service](https://docs.microsoft.com/visualstudio/python/managing-python-on-azure-app-service) jest przestarzały i jego użycie nie jest zalecane.
>

## <a name="show-python-version"></a>Wyświetlanie wersji języka Python

Aby wyświetlić bieżącą wersję języka Python, uruchom następujące polecenie w usłudze [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource_group_name> --name <app_name> --query linuxFxVersion
```

Aby wyświetlić wszystkie obsługiwane wersje języka Python, uruchom następujące polecenie w usłudze [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes --linux | grep PYTHON
```

Możesz uruchomić nieobsługiwaną wersję języka Python, kompilując w zamian własny obraz kontenera. Aby uzyskać więcej informacji, zobacz [Używanie niestandardowego obrazu platformy Docker](tutorial-custom-docker-image.md).

## <a name="set-python-version"></a>Ustawianie wersji języka Python

Uruchom następujące polecenie w usłudze [Cloud Shell](https://shell.azure.com), aby ustawić środowisko Python na wersję 3.7:

```azurecli-interactive
az webapp config set --resource-group <group_name> --name <app_name> --linux-fx-version "PYTHON|3.7"
```

## <a name="container-characteristics"></a>Właściwości kontenera

Aplikacje języka Python wdrażane w usłudze App Service w systemie Linux działają w kontenerze Docker, który jest zdefiniowany w repozytorium GitHub — [Python 3.6](https://github.com/Azure-App-Service/python/tree/master/3.6.6) lub [Python 3.7](https://github.com/Azure-App-Service/python/tree/master/3.7.0).
Ten kontener ma następujące cechy:

- Aplikacje są uruchamiane przy użyciu [serwera HTTP Gunicorn WSGI](https://gunicorn.org/) z dodatkowymi argumentami `--bind=0.0.0.0 --timeout 600`.
- Domyślnie obraz podstawowy zawiera platformę internetową Flask, ale kontener obsługuje inne platformy zgodne z interfejsem WSGI i językiem Python 3.7, takie jak Django.
- Aby zainstalować dodatkowe pakiety, takie jak Django, utwórz plik [*requirements.txt*](https://pip.pypa.io/en/stable/user_guide/#requirements-files) w katalogu głównym projektu, używając polecenia `pip freeze > requirements.txt`. Następnie opublikuj projekt w usłudze App Service przy użyciu wdrożenia narzędzia Git, co spowoduje automatyczne uruchomienie polecenia `pip install -r requirements.txt` w kontenerze w celu zainstalowania zależności aplikacji.

## <a name="container-startup-process"></a>Proces uruchamiania kontenera

Podczas uruchamiania kontener usługi App Service w systemie Linux wykonuje następujące kroki:

1. Użyj [niestandardowego polecenia uruchomienia](#customize-startup-command), jeśli zostało udostępnione.
2. Sprawdź istnienie [aplikacji Django](#django-app), a następnie uruchom dla niej serwer Gunicorn, jeśli zostanie wykryta.
3. Sprawdź istnienie [aplikacji Flask](#flask-app), a następnie uruchom dla niej serwer Gunicorn, jeśli zostanie wykryta.
4. Jeśli nie została znaleziona żadna inna aplikacja, uruchomienie domyślnej aplikacji wbudowanej w kontener.

Poniższe sekcje zawierają dodatkowe szczegóły dla każdej z tych opcji.

### <a name="django-app"></a>Aplikacja platformy Django

W przypadku aplikacji Django usługa App Service szuka pliku o nazwie `wsgi.py` w kodzie aplikacji, a następnie uruchamia serwer Gunicorn przy użyciu następującego polecenia:

```bash
# <module> is the path to the folder that contains wsgi.py
gunicorn --bind=0.0.0.0 --timeout 600 <module>.wsgi
```

Jeśli chcesz mieć większą kontrolę nad poleceniem uruchamiania, użyj [niestandardowego polecenia uruchamiania](#custom-startup-command) i zamień wartość `<module>` na nazwę modułu zawierającego plik *wsgi.py*.

### <a name="flask-app"></a>Aplikacja Flask

W przypadku aplikacji Flask usługa App Service szuka pliku o nazwie *application.py* lub *app.py* i uruchamia serwer Gunicorn w następujący sposób:

```bash
# If application.py
gunicorn --bind=0.0.0.0 --timeout 600 application:app
# If app.py
gunicorn --bind=0.0.0.0 --timeout 600 app:app
```

Jeśli główny moduł aplikacji znajduje się w innym pliku, użyj innej nazwy dla obiektu aplikacji, a jeśli chcesz podać dodatkowe argumenty dla serwera Gunicorn, użyj [niestandardowego polecenia uruchamiania](#custom-startup-command).

### <a name="default-behavior"></a>Zachowanie domyślne

Jeśli usługa App Service nie znajdzie polecenia niestandardowego, aplikacji Django ani aplikacji Flask, uruchomi domyślną aplikację dostępną tylko do odczytu, która znajduje się w folderze _opt/defaultsite_. Aplikacja domyślna wygląda następująco:

![Domyślna strona internetowa usługi App Service w systemie Linux](media/how-to-configure-python/default-python-app.png)

## <a name="customize-startup-command"></a>Dostosowywanie polecenia uruchamiania

Aby kontrolować zachowanie kontenera podczas uruchamiania, możesz podać niestandardowe polecenie uruchamiania serwera Gunicorn. Jeśli na przykład masz aplikację Flask z modułem głównym *hello.py*, a obiekt aplikacji Flask w tym pliku nosi nazwę `myapp`, to polecenie wygląda następująco:

```bash
gunicorn --bind=0.0.0.0 --timeout 600 hello:myapp
```

Jeśli moduł główny znajduje się w podfolderze, takim jak `website`, określ ten folder za pomocą argumentu `--chdir`:

```bash
gunicorn --bind=0.0.0.0 --timeout 600 --chdir website hello:myapp
```

Możesz też dodać do polecenia inne argumenty dla serwera Gunicorn, takie jak `--workers=4`. Aby uzyskać więcej informacji, zobacz [Running Gunicorn](https://docs.gunicorn.org/en/stable/run.html) (Uruchamianie serwera Gunicorn) (docs.gunicorn.org).

Aby użyć serwera innego niż Gunicorn, takiego jak [aiohttp](https://aiohttp.readthedocs.io/en/stable/web_quickstart.html), możesz uruchomić polecenie:

```bash
python3.7 -m aiohttp.web -H localhost -P 8080 package.module:init_func
```

Aby określić polecenie niestandardowe, wykonaj następujące czynności:

1. Przejdź do strony [Ustawienia aplikacji](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) w witrynie Azure Portal.
1. W ustawieniach **Środowisko uruchomieniowe** ustaw opcję **Stos** na wartość **Python 3.7**, a następnie wprowadź polecenie bezpośrednio w polu **Plik startowy**.
Możesz też zapisać polecenie w pliku tekstowym w katalogu głównym projektu, używając nazwy takiej jak *startup.txt* (lub dowolnej innej). Następnie wdróż ten plik w usłudze App Service i określ jego nazwę w polu **Plik startowy**. Ta opcja umożliwia zarządzanie poleceniem w repozytorium kodu źródłowego zamiast w witrynie Azure Portal.
1. Wybierz pozycję **Zapisz**. Usługa App Service zostanie automatycznie uruchomiona ponownie, a niestandardowe polecenie startowe powinno zostać zastosowane po kilku sekundach.

> [!Note]
> Usługa App Service ignoruje wszelkie błędy występujące podczas przetwarzania pliku polecenia niestandardowego oraz kontynuuje proces uruchamiania, wyszukując aplikacje Django i Flask. Jeśli nie widzisz oczekiwanego zachowania, sprawdź, czy plik startowy jest wdrożony w usłudze App Service i czy nie zawiera błędów.

## <a name="access-environment-variables"></a>Uzyskiwanie dostępu do zmiennych środowiskowych

W usłudze App Service można określić ustawienia aplikacji poza kodem aplikacji (zobacz [Set environment variables (Ustawianie zmiennych środowiskowych)](../web-sites-configure.md)). Następnie można uzyskiwać do nich dostęp przy użyciu standardowego wzorca [os.environ](https://docs.python.org/3/library/os.html#os.environ). Aby na przykład uzyskać dostęp do ustawienia aplikacji o nazwie `WEBSITE_SITE_NAME`, użyj następującego kodu:

```python
os.environ['WEBSITE_SITE_NAME']
```

## <a name="detect-https-session"></a>Wykrywanie sesji protokołu HTTPS

W usłudze App Service [kończenie żądań SSL](https://wikipedia.org/wiki/TLS_termination_proxy) odbywa się w modułach równoważenia obciążenia sieciowego, dzięki czemu wszystkie żądania HTTPS docierają do aplikacji jako niezaszyfrowane żądania HTTP. Jeśli logika aplikacji musi sprawdzać, czy żądania użytkownika są szyfrowane, czy nie, zbadaj nagłówek `X-Forwarded-Proto`.

```python
if 'X-Forwarded-Proto' in request.headers and request.headers['X-Forwarded-Proto'] == 'https':
# Do something when HTTPS is used
```

Popularne platformy internetowe umożliwiają dostęp do informacji `X-Forwarded-*` w standardowym wzorcu aplikacji. Na platformie [CodeIgniter](https://codeigniter.com/) element [is_https()](https://github.com/bcit-ci/CodeIgniter/blob/master/system/core/Common.php#L338-L365) domyślnie sprawdza wartość `X_FORWARDED_PROTO`.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

- **Po wdrożeniu własnego kodu aplikacji wyświetlana jest aplikacja domyślna.** Aplikacja domyślna jest wyświetlana dlatego, że kod Twojej aplikacji nie został wdrożony w usłudze App Service albo usługa App Service nie znalazła kodu aplikacji i zamiast niej uruchomiła aplikację domyślną.
- Uruchom ponownie usługę App Service, poczekaj 15–20 sekund i sprawdź ponownie aplikację.
- Upewnij się, że używasz usługi App Service dla systemu Linux, a nie wystąpienia opartego na systemie Windows. W interfejsie wiersza polecenia platformy Azure uruchom polecenie `az webapp show --resource-group <resource_group_name> --name <app_service_name> --query kind`, zastępując zmienne `<resource_group_name>` i `<app_service_name>` odpowiednimi wartościami. Powinny zostać wyświetlone dane wyjściowe `app,linux`. W przeciwnym razie ponownie utwórz usługę App Service i wybierz system Linux.
- Połącz się bezpośrednio z usługą App Service przy użyciu konsoli SSH lub Kudu, a następnie sprawdź, czy Twoje pliki znajdują się w katalogu *site/wwwroot*. Jeśli pliki nie istnieją, sprawdź proces wdrażania i ponownie wdróż aplikację.
- Jeśli pliki istnieją, oznacza to, że usługa App Service nie mogła zidentyfikować określonego pliku startowego. Sprawdź, czy aplikacja ma strukturę zgodną z oczekiwaniami usługi App Service dla platformy [Django](#django-app) lub [Flask](#flask-app), albo użyj [niestandardowego polecenia uruchamiania](#custom-startup-command).
- **W przeglądarce jest wyświetlany komunikat „Usługa niedostępna”.** W przeglądarce upłynął limit czasu oczekiwania na odpowiedź usługi App Service, co wskazuje, że usługa App Service uruchomiła serwer Gunicorn, ale argumenty określające kod aplikacji są niepoprawne.
- Odśwież okno przeglądarki, zwłaszcza jeśli korzystasz z niższych warstw cenowych w planie usługi App Service. Na przykład podczas korzystania z warstw bezpłatnych aplikacja może być uruchamiana dłużej i zacznie odpowiadać po odświeżeniu okna przeglądarki.
- Sprawdź, czy aplikacja ma strukturę zgodną z oczekiwaniami usługi App Service dla platformy [Django](#django-app) lub [Flask](#flask-app), albo użyj [niestandardowego polecenia uruchamiania](#customize-startup-command).
- Połącz się z usługą App Service przy użyciu konsoli SSH lub Kudu, a następnie sprawdź dzienniki diagnostyczne przechowywane w folderze *LogFiles*. Aby uzyskać więcej informacji o rejestrowaniu, zobacz [Enable diagnostics logging for web apps in Azure App Service](../troubleshoot-diagnostic-logs.md) (Włączanie rejestrowania diagnostycznego dla aplikacji internetowych w usłudze Azure App Service).
