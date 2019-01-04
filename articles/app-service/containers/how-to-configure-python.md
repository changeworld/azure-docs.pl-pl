---
title: Konfigurowanie aplikacji języka Python w systemie Linux — Azure App Service
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
ms.date: 10/09/2018
ms.author: astay;cephalin;kraigb
ms.custom: seodec18
ms.openlocfilehash: f7e63fa75f473d5da911fbf845f0662d8eec5c70
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/21/2018
ms.locfileid: "53717402"
---
# <a name="configure-your-python-app-for-the-azure-app-service-on-linux"></a>Konfigurowanie aplikacji języka Python dla usługi Azure App Service w systemie Linux

W tym artykule opisano, jak [usługa Azure App Service w systemie Linux](app-service-linux-intro.md) uruchamia aplikacje języka Python i jak można dostosować zachowanie usługi App Service w razie potrzeby.

## <a name="set-python-version"></a>Ustawianie wersji języka Python

Dostępne są dwa podstawowe obrazy: środowisko Python 3.6 i Python 3.7. Możesz utworzyć aplikację, korzystając z wybranego obrazu opartego na języku Python. Aby na przykład utworzyć aplikację przy użyciu języka Python 3.7, uruchom następujące polecenie w usłudze Cloud Shell:

```azurecli-interactive
az webapp create --resource-group <group_name> --plan <plan_name> --name <app_name> --runtime "PYTHON|3.7"
```

Aby zmienić wersję języka Python (podstawowego obrazu), na przykład na 3.6, uruchom następujące polecenie w usłudze Cloud Shell:

```azurecli-interactive
az webapp config set --resource-group <group_name> --name <app_name> --linux-fx-version "PYTHON|3.6"
```

Jeśli potrzebujesz innej wersji języka Python, należy zamiast tego utworzyć i wdrożyć własny obraz kontenera. Aby uzyskać więcej informacji, zobacz [Używanie niestandardowego obrazu platformy Docker dla usługi Web App for Containers](tutorial-custom-docker-image.md).

## <a name="container-characteristics"></a>Właściwości kontenera

Aplikacje języka Python wdrażane w usłudze App Service w systemie Linux działają w kontenerze Docker, który jest zdefiniowany w repozytorium GitHub — [Python 3.6](https://github.com/Azure-App-Service/python/tree/master/3.6.6) lub [Python 3.7](https://github.com/Azure-App-Service/python/tree/master/3.7.0).

Ten kontener ma następujące cechy:

- Aplikacje są uruchamiane przy użyciu [serwera HTTP Gunicorn WSGI](https://gunicorn.org/) z dodatkowymi argumentami `--bind=0.0.0.0 --timeout 600`.

- Domyślnie obraz podstawowy zawiera platformę internetową Flask, ale kontener obsługuje inne platformy zgodne z interfejsem WSGI i językiem Python 3.7, takie jak Django.

- Aby zainstalować dodatkowe pakiety, takie jak Django, utwórz plik [*requirements.txt*](https://pip.pypa.io/en/stable/user_guide/#requirements-files) w katalogu głównym projektu, używając polecenia `pip freeze > requirements.txt`. Następnie opublikuj projekt w usłudze App Service przy użyciu wdrożenia narzędzia Git, co spowoduje automatyczne uruchomienie polecenia `pip install -r requirements.txt` w kontenerze w celu zainstalowania zależności aplikacji.

## <a name="container-startup-process-and-customizations"></a>Proces uruchamiania kontenera oraz dostosowania

Podczas uruchamiania kontener usługi App Service w systemie Linux wykonuje następujące kroki:

1. Sprawdzenie obecności niestandardowego polecenia uruchamiania i zastosowanie go, jeśli zostało podane.
2. Sprawdzenie obecności pliku *wsgi.py* aplikacji Django oraz (jeśli plik istnieje) uruchomienie serwera Gunicorn przy użyciu tego pliku.
3. Sprawdzenie obecności pliku o nazwie *application.py* oraz (jeśli plik istnieje) uruchomienie serwera Gunicorn z użyciem wartości `application:app` przy założeniu aplikacji Flask.
4. Jeśli nie została znaleziona żadna inna aplikacja, uruchomienie domyślnej aplikacji wbudowanej w kontener.

Poniższe sekcje zawierają dodatkowe szczegóły dla każdej z tych opcji.

### <a name="django-app"></a>Aplikacja platformy Django

W przypadku aplikacji Django usługa App Service szuka pliku o nazwie `wsgi.py` w kodzie aplikacji, a następnie uruchamia serwer Gunicorn przy użyciu następującego polecenia:

```bash
# <module> is the path to the folder containing wsgi.py
gunicorn --bind=0.0.0.0 --timeout 600 <module>.wsgi
```

Jeśli chcesz mieć większą kontrolę nad poleceniem uruchamiania, użyj [niestandardowego polecenia uruchamiania](#custom-startup-command) i zamień wartość `<module>` na nazwę modułu zawierającego plik *wsgi.py*.

### <a name="flask-app"></a>Aplikacja Flask

W przypadku aplikacji Flask usługa App Service szuka pliku o nazwie *application.py* i uruchamia serwer Gunicorn w następujący sposób:

```bash
gunicorn --bind=0.0.0.0 --timeout 600 application:app
```

Jeśli główny moduł aplikacji znajduje się w innym pliku, użyj innej nazwy dla obiektu aplikacji, a jeśli chcesz podać dodatkowe argumenty dla serwera Gunicorn, użyj [niestandardowego polecenia uruchamiania](#custom-startup-command). Ta sekcja zawiera przykład dla platformy Flask z użyciem kodu początkowego w pliku *hello.py* i obiekt aplikacji Flask o nazwie `myapp`.

### <a name="custom-startup-command"></a>Niestandardowe polecenie uruchamiania

Aby kontrolować zachowanie kontenera podczas uruchamiania, możesz podać niestandardowe polecenie uruchamiania serwera Gunicorn. Jeśli na przykład masz aplikację Flask z modułem głównym *hello.py*, a obiekt aplikacji Flask w tym pliku nosi nazwę `myapp`, to polecenie wygląda następująco:

```bash
gunicorn --bind=0.0.0.0 --timeout 600 hello:myapp
```

Jeśli moduł główny znajduje się w podfolderze, takim jak `website`, określ ten folder za pomocą argumentu `--chdir`:

```bash
gunicorn --bind=0.0.0.0 --timeout 600 --chdir website hello:myapp
```

Możesz też dodać do polecenia inne argumenty dla serwera Gunicorn, takie jak `--workers=4`. Aby uzyskać więcej informacji, zobacz [Running Gunicorn](https://docs.gunicorn.org/en/stable/run.html) (Uruchamianie serwera Gunicorn) (docs.gunicorn.org).

Aby określić polecenie niestandardowe, wykonaj następujące czynności:

1. Przejdź do strony [Ustawienia aplikacji](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) w witrynie Azure Portal.

1. W ustawieniach **Środowisko uruchomieniowe** ustaw opcję **Stos** na wartość **Python 3.7**, a następnie wprowadź polecenie bezpośrednio w polu **Plik startowy**.

    Możesz też zapisać polecenie w pliku tekstowym w katalogu głównym projektu, używając nazwy takiej jak *startup.txt* (lub dowolnej innej). Następnie wdróż ten plik w usłudze App Service i określ jego nazwę w polu **Plik startowy**. Ta opcja umożliwia zarządzanie poleceniem w repozytorium kodu źródłowego zamiast w witrynie Azure Portal.

1. Wybierz pozycję **Zapisz**. Usługa App Service zostanie automatycznie uruchomiona ponownie, a niestandardowe polecenie startowe powinno zostać zastosowane po kilku sekundach.

> [!Note]
> Usługa App Service ignoruje wszelkie błędy występujące podczas przetwarzania pliku polecenia niestandardowego oraz kontynuuje proces uruchamiania, wyszukując aplikacje Django i Flask. Jeśli nie widzisz oczekiwanego zachowania, sprawdź, czy plik startowy jest wdrożony w usłudze App Service i czy nie zawiera błędów.

### <a name="default-behavior"></a>Zachowanie domyślne

Jeśli usługa App Service nie znajdzie polecenia niestandardowego, aplikacji Django ani aplikacji Flask, uruchomi domyślną aplikację dostępną tylko do odczytu, która znajduje się w folderze _opt/defaultsite_. Aplikacja domyślna wygląda następująco:

![Domyślna strona internetowa usługi App Service w systemie Linux](media/how-to-configure-python/default-python-app.png)

## <a name="troubleshooting"></a>Rozwiązywanie problemów

- **Po wdrożeniu własnego kodu aplikacji wyświetlana jest aplikacja domyślna.**  Aplikacja domyślna jest wyświetlana dlatego, że kod Twojej aplikacji nie został faktycznie wdrożony w usłudze App Service albo usługa App Service nie znalazła kodu aplikacji i zamiast niej uruchomiła aplikację domyślną.
  - Uruchom ponownie usługę App Service, poczekaj 15–20 sekund i sprawdź ponownie aplikację.
  - Upewnij się, że używasz usługi App Service dla systemu Linux, a nie wystąpienia opartego na systemie Windows. W interfejsie wiersza polecenia platformy Azure uruchom polecenie `az webapp show --resource-group <resource_group_name> --name <app_service_name> --query kind`, zastępując zmienne `<resource_group_name>` i `<app_service_name>` odpowiednimi wartościami. Powinny zostać wyświetlone dane wyjściowe `app,linux`. W przeciwnym razie ponownie utwórz usługę App Service i wybierz system Linux.
    - Połącz się bezpośrednio z usługą App Service przy użyciu konsoli SSH lub Kudu, a następnie sprawdź, czy Twoje pliki znajdują się w katalogu *site/wwwroot*. Jeśli pliki nie istnieją, sprawdź proces wdrażania i ponownie wdróż aplikację.
  - Jeśli pliki istnieją, oznacza to, że usługa App Service nie mogła zidentyfikować określonego pliku startowego. Sprawdź, czy aplikacja ma strukturę zgodną z oczekiwaniami usługi App Service dla platformy [Django](#django-app) lub [Flask](#flask-app), albo użyj [niestandardowego polecenia uruchamiania](#custom-startup-command).
  
- **W przeglądarce jest wyświetlany komunikat „Usługa niedostępna”.** W przeglądarce upłynął limit czasu oczekiwania na odpowiedź usługi App Service, co wskazuje, że usługa App Service uruchomiła serwer Gunicorn, ale argumenty określające kod aplikacji są niepoprawne.
  - Odśwież okno przeglądarki, zwłaszcza jeśli korzystasz z niższych warstw cenowych w planie usługi App Service. Na przykład podczas korzystania z warstw bezpłatnych aplikacja może być uruchamiana dłużej i zacznie odpowiadać po odświeżeniu okna przeglądarki.
  - Sprawdź, czy aplikacja ma strukturę zgodną z oczekiwaniami usługi App Service dla platformy [Django](#django-app) lub [Flask](#flask-app), albo użyj [niestandardowego polecenia uruchamiania](#custom-startup-command).
  - Połącz się z usługą App Service przy użyciu konsoli SSH lub Kudu, a następnie sprawdź dzienniki diagnostyczne przechowywane w folderze *LogFiles*. Aby uzyskać więcej informacji o rejestrowaniu, zobacz [Enable diagnostics logging for web apps in Azure App Service](../troubleshoot-diagnostic-logs.md) (Włączanie rejestrowania diagnostycznego dla aplikacji internetowych w usłudze Azure App Service).
