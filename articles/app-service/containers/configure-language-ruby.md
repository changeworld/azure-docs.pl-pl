---
title: Konfigurowanie aplikacji języka Ruby — usługa Azure App Service
description: W tym samouczku opisano opcje tworzenia i konfigurowania aplikacji języka Ruby dla usługi Azure App Service w systemie Linux.
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
ms.date: 03/28/2019
ms.author: astay;cephalin;kraigb
ms.custom: seodec18
ms.openlocfilehash: aec85b16ab0357b28a564f75509e147e5555137b
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67205064"
---
# <a name="configure-a-linux-ruby-app-for-azure-app-service"></a>Konfigurowanie aplikacji w języku Ruby systemu Linux dla usługi Azure App Service

W tym artykule opisano sposób [usługi Azure App Service](app-service-linux-intro.md) uruchamia aplikacje języka Ruby i w jaki sposób dostosować zachowanie usługi App Service w razie potrzeby. Aplikacje Ruby musi zostać wdrożony w przypadku wszystkich wymaganych [pip](https://pypi.org/project/pip/) modułów.

Ten przewodnik zawiera podstawowe pojęcia i instrukcje dla deweloperów języka Ruby, którzy korzystają z wbudowanych kontenera systemu Linux w usłudze App Service. Jeśli nie znasz usługi Azure App Service, należy wykonać [Szybki Start języka Ruby](quickstart-ruby.md) i [języka Ruby z samouczkiem PostgreSQL](tutorial-ruby-postgres-app.md) pierwszy.

## <a name="show-ruby-version"></a>Pokaż wersja środowiska Ruby

Aby wyświetlić bieżącą wersję języka Ruby, uruchom następujące polecenie [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Aby wyświetlić wszystkie obsługiwane wersje języka Ruby, uruchom następujące polecenie [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes --linux | grep RUBY
```

Nieobsługiwana wersja środowiska Ruby można uruchomić, tworząc własny obraz kontenera, zamiast tego. Aby uzyskać więcej informacji, zobacz [Używanie niestandardowego obrazu platformy Docker](tutorial-custom-docker-image.md).

## <a name="set-ruby-version"></a>Ustawianie wersji języka Ruby

Uruchom następujące polecenie [Cloud Shell](https://shell.azure.com) ustawić wersja środowiska Ruby 2.3:

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --linux-fx-version "RUBY|2.3"
```

> [!NOTE]
> Jeśli widzisz błędy podobne do następujących w czasie wdrażania:
> ```
> Your Ruby version is 2.3.3, but your Gemfile specified 2.3.1
> ```
> lub
> ```
> rbenv: version `2.3.1' is not installed
> ```
> Oznacza to, czy wersja środowiska Ruby skonfigurowane w projekcie różni się od wersji zainstalowanej w kontenerze korzystasz z (`2.3.3` w powyższym przykładzie). W powyższym przykładzie Sprawdź zarówno *Gemfile* i *wersji .ruby* i sprawdź, czy wersja środowiska Ruby nie jest ustawiona lub jest ustawiony do wersji, który jest zainstalowany w kontenerze używasz (`2.3.3` w przykład powyżej).

## <a name="access-environment-variables"></a>Uzyskiwanie dostępu do zmiennych środowiskowych

W usłudze App Service możesz [określić ustawienia aplikacji](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) poza kodu aplikacji. Następnie uzyskiwaniu dostępu do nich przy użyciu standardu [ENV ['< ścieżka name >"]](https://ruby-doc.org/core-2.3.3/ENV.html) wzorca. Aby na przykład uzyskać dostęp do ustawienia aplikacji o nazwie `WEBSITE_SITE_NAME`, użyj następującego kodu:

```ruby
ENV['WEBSITE_SITE_NAME']
```

## <a name="customize-deployment"></a>Dostosowywanie wdrożenia

Podczas wdrażania [repozytorium Git](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json), lub [spakowany pakiet](../deploy-zip.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) za pomocą procesów kompilacji jest włączona, aparat wdrażania (Kudu) automatycznie wykonywane są następujące czynności po wdrożeniu na domyślnie:

1. Sprawdź, czy *Gemfile* istnieje.
1. Uruchom polecenie `bundle clean`. 
1. Uruchom polecenie `bundle install --path "vendor/bundle"`.
1. Uruchom `bundle package` do Klejnoty pakiet do folderu dostawcy/pamięci podręcznej.

### <a name="use---without-flag"></a>Użyj parametru--bez flagi

Aby uruchomić `bundle install` z [— bez](https://bundler.io/man/bundle-install.1.html) Flaga, ustaw `BUNDLE_WITHOUT` [ustawienia aplikacji](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) rozdzielaną przecinkami listę grup. Na przykład następujące polecenie ustawia ją na `development,test`.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings BUNDLE_WITHOUT="development,test"
```

Jeśli to ustawienie jest zdefiniowana, a następnie uruchamia aparatu wdrażania `bundle install` z `--without $BUNDLE_WITHOUT`.

### <a name="precompile-assets"></a>Wstępnie skompilował zasoby

Czynności po wdrożeniu nie wstępnie skompilował zasoby domyślnie. Aby włączyć wstępnej kompilacji zasobów, należy ustawić `ASSETS_PRECOMPILE` [ustawienia aplikacji](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) do `true`. Następnie polecenia `bundle exec rake --trace assets:precompile` jest uruchamiany na końcu po wdrożeniu kroki. Na przykład:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings ASSETS_PRECOMPILE=true
```

Aby uzyskać więcej informacji, zobacz [obsługiwać zasoby statyczne](#serve-static-assets).

## <a name="customize-start-up"></a>Dostosowywanie uruchamiania

Domyślny kontener języka Ruby uruchamia serwer platformy Rails w następującej kolejności (Aby uzyskać więcej informacji, zobacz [skrypt uruchamiania](https://github.com/Azure-App-Service/ruby/blob/master/2.3.8/startup.sh)):

1. Generowanie [secret_key_base](https://edgeguides.rubyonrails.org/security.html#environmental-security) wartość, jeśli nie już istnieje. Ta wartość jest wymagana dla aplikacji do uruchamiania w trybie produkcyjnym.
1. Ustaw `RAILS_ENV` zmiennej środowiskowej, aby `production`.
1. Usuń wszystkie *.pid* w pliku *tmp/PID* katalogu, co zostało wcześniej uruchomione serwer platformy Rails.
1. Sprawdź, czy wszystkie zależności są zainstalowane. Jeśli nie, spróbuj zainstalować Klejnoty z lokalnego *dostawcy/pamięci podręcznej* katalogu.
1. Uruchom polecenie `rails server -e $RAILS_ENV`.

Proces uruchamiania można dostosować w następujący sposób:

- [Obsługiwać zasoby statyczne](#serve-static-assets)
- [Uruchom w trybie innym niż produkcyjne](#run-in-non-production-mode)
- [Ręcznie ustawić secret_key_base](#set-secret_key_base-manually)

### <a name="serve-static-assets"></a>Obsługiwać zasoby statyczne

Serwer platformy Rails w kontenerze dopisków fonetycznych jest uruchamiany w trybie produkcyjnym domyślnie i [przyjęto założenie, że zasoby są wstępnie skompilowane i są one obsługiwane przez serwer sieci web](https://guides.rubyonrails.org/asset_pipeline.html#in-production). Aby obsługiwać zasoby statyczne z serwer platformy Rails, należy wykonać dwie czynności:

- **Wstępnie skompilował zasoby** - [Prekompilowanie statycznych zasobów lokalnie](https://guides.rubyonrails.org/asset_pipeline.html#local-precompilation) i wdrażać je ręcznie. Lub pozwól, aby aparat wdrażania, zamiast go obsłużyć (zobacz [wstępnie skompilował zasoby](#precompile-assets).
- **Włącz dostarczanie plików statycznych** — w celu obsługi statycznych zasobów z kontenera dopisków fonetycznych, ustaw `RAILS_SERVE_STATIC_FILES` [ustaw `RAILS_SERVE_STATIC_FILES` ustawienia aplikacji](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) do `true`. Na przykład:

    ```azurecli-interactive
    az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings RAILS_SERVE_STATIC_FILES=true
    ```

### <a name="run-in-non-production-mode"></a>Uruchom w trybie innym niż produkcyjne

Domyślnie serwer platformy Rails jest uruchamiana w trybie produkcyjnym. Aby uruchomić w trybie projektowania, na przykład ustawić `RAILS_ENV` [ustawienia aplikacji](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) do `development`.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings RAILS_ENV="development"
```

Jednak to samo ustawienie powoduje, że serwer platformy Rails, aby uruchomić w trybie projektowania, który akceptuje żądania tylko localhost i jest niedostępny poza kontenerem. Aby akceptować żądania klienta zdalnego, należy ustawić `APP_COMMAND_LINE` [ustawienia aplikacji](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) do `rails server -b 0.0.0.0`. To ustawienie aplikacji umożliwia uruchamianie poleceń niestandardowych w kontenerze dopisków fonetycznych. Na przykład:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings APP_COMMAND_LINE="rails server -b 0.0.0.0"
```

### <a name="set-secret_key_base-manually"></a> Ręcznie ustawić secret_key_base

Aby użyć własnego `secret_key_base` wartość samodzielny usługi App Service wygeneruje, ustaw `SECRET_KEY_BASE` [ustawienia aplikacji](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) wartością ma. Na przykład:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings SECRET_KEY_BASE="<key-base-value>"
```

## <a name="access-diagnostic-logs"></a>Uzyskiwanie dostępu do dzienników diagnostycznych

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>Otwórz sesję SSH w przeglądarce

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Samouczek: Aplikacja platformy Rails z bazą danych PostgreSQL](tutorial-ruby-postgres-app.md)

> [!div class="nextstepaction"]
> [App Service dla systemu Linux — często zadawane pytania](app-service-linux-faq.md)
