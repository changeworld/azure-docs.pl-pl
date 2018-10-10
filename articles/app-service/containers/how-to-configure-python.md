---
title: Konfigurowanie wbudowanego obrazu w języku Python w usłudze Azure App Service
description: W tym samouczku opisano opcje tworzenia i konfigurowania aplikacji w języku Python w usłudze Azure App Service za pomocą wbudowanego obrazu w języku Python.
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
ms.date: 09/25/2018
ms.author: astay;cephalin
ms.custom: mvc
ms.openlocfilehash: 9316805993b81e4d2511e833e0cc8f240807a1f9
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/26/2018
ms.locfileid: "47228556"
---
# <a name="configure-built-in-python-image-in-azure-app-service-preview"></a>Konfigurowanie wbudowanego obrazu w języku Python w usłudze Azure App Service (wersja zapoznawcza)

W tym artykule opisano, jak skonfigurować wbudowany obraz w języku Python w usłudze [App Service w systemie Linux](app-service-linux-intro.md) do uruchamiania aplikacji w języku Python.

## <a name="python-version"></a>Wersja języka Python

Środowisko uruchomieniowe języka Python w usłudze App Service w systemie Linux używa wersji `python-3.7.0`.

## <a name="supported-frameworks"></a>Obsługiwane struktury

Obsługiwane są wszystkie wersje struktur internetowych zgodnych z interfejsem Web Server Gateway Interface(WSGI), które są zgodne ze środowiskiem uruchomieniowym `python-3.7`.

## <a name="package-management"></a>Zarządzanie pakietami

Podczas publikowania w usłudze Git aparat Kudu wyszukuje plik [requirements.txt](https://pip.pypa.io/en/stable/user_guide/#requirements-files) w katalogu głównym repozytorium i automatycznie instaluje pakiety na platformie Azure przy użyciu `pip`.

Aby wygenerować ten plik przed opublikowaniem, przejdź do katalogu głównego repozytorium i uruchom następujące polecenie w środowisku języka Python:

```bash
pip freeze > requirements.txt
```

## <a name="configure-your-python-app"></a>Konfigurowanie aplikacji w języku Python

Wbudowany obraz języka Python w usłudze App Service używa serwera [Gunicorn](http://gunicorn.org/) do uruchomienia aplikacji w języku Python. Gunicorn to serwer HTTP interfejsu WSGI języka Python dla systemu UNIX. Usługa App Service automatyzuje serwer Gunicorn pod kątem projektów Django i Flask.

### <a name="django-app"></a>Aplikacja platformy Django

Podczas publikowania projektu Django, który zawiera moduł `wsgi.py`, platforma Azure automatycznie wywołuje serwer Gunicorn przy użyciu następującego polecenia:

```bash
gunicorn <path_to_wsgi>
```

### <a name="flask-app"></a>Aplikacja Flask

Jeśli publikujesz aplikację Flask i punkt wejścia znajduje się w module `application.py` lub `app.py`, platforma Azure automatycznie wywołuje serwer Gunicorn przy użyciu jednego z następujących poleceń:

```bash
gunicorn application:app
```

Lub 

```bash
gunicorn app:app
```

### <a name="customize-start-up"></a>Dostosowywanie uruchamiania

Aby zdefiniować niestandardowy punkt wejścia dla aplikacji, najpierw utwórz plik _.txt_ zawierający niestandardowe polecenie serwera Gunicorn i umieść go w katalogu głównym projektu. Na przykład, aby uruchomić serwer za pomocą modułu _helloworld.py_ i zmiennej `app`, utwórz plik _startup.txt_ o następującej zawartości:

```bash
gunicorn helloworld:app
```

Na stronie [Ustawienia aplikacji](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) wybierz **Python|3.7** w polu **Stos środowiska uruchomieniowego** i podaj nazwę w polu **Plik uruchomieniowy** z poprzedniego kroku. Na przykład _startup.txt_.
