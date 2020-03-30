---
title: Konfigurowanie aplikacji Ruby — usługa Azure App Service
description: Dowiedz się, jak skonfigurować wstępnie utworzony kontener Ruby dla aplikacji. W tym artykule przedstawiono najczęstsze zadania konfiguracyjne.
ms.topic: quickstart
ms.date: 03/28/2019
ms.reviewer: astay; kraigb
ms.custom: mvc, seodec18
ms.openlocfilehash: 8daebba840223d050a14b4b99cb6ae15472ee4f5
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "80046324"
---
# <a name="configure-a-linux-ruby-app-for-azure-app-service"></a>Konfigurowanie aplikacji Linux Ruby dla usługi Azure App Service

W tym artykule opisano, jak [usługa Azure App Service](app-service-linux-intro.md) uruchamia aplikacje Ruby i jak w razie potrzeby można dostosować zachowanie usługi App Service. Aplikacje Ruby muszą być wdrożone ze wszystkimi [wymaganymi klejnotami.](https://rubygems.org/gems)

Ten przewodnik zawiera kluczowe pojęcia i instrukcje dla deweloperów Ruby, którzy używają wbudowanego kontenera systemu Linux w usłudze App Service. Jeśli nigdy nie korzystałeś z usługi Azure App Service, należy najpierw wykonać samouczek [Szybki start Ruby](quickstart-ruby.md) i [Ruby z postgresql.](tutorial-ruby-postgres-app.md)

## <a name="show-ruby-version"></a>Pokaż wersję Ruby

Aby wyświetlić bieżącą wersję Ruby, uruchom następujące polecenie w [przysłonie chmury:](https://shell.azure.com)

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Aby wyświetlić wszystkie obsługiwane wersje Ruby, uruchom następujące polecenie w [przyłowisce chmury:](https://shell.azure.com)

```azurecli-interactive
az webapp list-runtimes --linux | grep RUBY
```

Możesz uruchomić nieobsługiconą wersję Ruby, tworząc zamiast tego własny obraz kontenera. Aby uzyskać więcej informacji, zobacz [Używanie niestandardowego obrazu platformy Docker](tutorial-custom-docker-image.md).

## <a name="set-ruby-version"></a>Ustaw wersję Ruby

Uruchom następujące polecenie w [usłudze Cloud Shell,](https://shell.azure.com) aby ustawić wersję Ruby na 2.3:

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --linux-fx-version "RUBY|2.3"
```

> [!NOTE]
> Jeśli w czasie wdrażania są widoczne błędy podobne do następujących:
> ```
> Your Ruby version is 2.3.3, but your Gemfile specified 2.3.1
> ```
> lub
> ```
> rbenv: version `2.3.1' is not installed
> ```
> Oznacza to, że wersja Ruby skonfigurowana w projekcie różni się od wersji zainstalowanej`2.3.3` w uruchomionym kontenerze (w powyższym przykładzie). W powyższym przykładzie sprawdź zarówno *Gemfile,* jak i *.ruby-version* i sprawdź, czy wersja Ruby nie jest ustawiona`2.3.3` lub jest ustawiona na wersję zainstalowaną w uruchomionym kontenerze (w powyższym przykładzie).

## <a name="access-environment-variables"></a>Uzyskiwanie dostępu do zmiennych środowiskowych

W usłudze App Service możesz [ustawić ustawienia aplikacji](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) poza kodem aplikacji. Następnie można uzyskać do nich dostęp przy użyciu standardowego wzorca [ENV['\<path-name>'].](https://ruby-doc.org/core-2.3.3/ENV.html) Aby na przykład uzyskać dostęp do ustawienia aplikacji o nazwie `WEBSITE_SITE_NAME`, użyj następującego kodu:

```ruby
ENV['WEBSITE_SITE_NAME']
```

## <a name="customize-deployment"></a>Dostosowywanie wdrażania

Po wdrożeniu [repozytorium Git](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)lub [pakietu Zip](../deploy-zip.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) z włączonymi procesami kompilacji, aparat wdrażania (Kudu) domyślnie uruchamia następujące kroki po wdrożeniu:

1. Sprawdź, czy *gemfile* istnieje.
1. Uruchom polecenie `bundle clean`. 
1. Uruchom polecenie `bundle install --path "vendor/bundle"`.
1. Uruchom, `bundle package` aby spakować klejnoty do folderu dostawca/pamięć podręczna.

### <a name="use---without-flag"></a>Użyj --bez flagi

Aby `bundle install` uruchomić z flagą [--bez,](https://bundler.io/man/bundle-install.1.html) ustaw `BUNDLE_WITHOUT` [ustawienie aplikacji](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) na listę grup oddzielonych przecinkami. Na przykład następujące polecenie ustawia `development,test`go na .

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings BUNDLE_WITHOUT="development,test"
```

Jeśli to ustawienie jest zdefiniowane, `bundle install` `--without $BUNDLE_WITHOUT`aparat wdrażania działa z programem .

### <a name="precompile-assets"></a>Zasoby wstępnie kompiluj

Kroki po wdrożeniu domyślnie nie wstępnie kompilują zasobów. Aby włączyć wstępną kompilację zasobów, ustaw `ASSETS_PRECOMPILE` [ustawienie aplikacji](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) na `true`. Następnie polecenie `bundle exec rake --trace assets:precompile` jest uruchamiane na końcu kroków po wdrożeniu. Przykład:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings ASSETS_PRECOMPILE=true
```

Aby uzyskać więcej informacji, zobacz [Obsługa zasobów statycznych](#serve-static-assets).

## <a name="customize-start-up"></a>Dostosowywanie uruchamiania

Domyślnie kontener Ruby uruchamia serwer Rails w następującej kolejności (aby uzyskać więcej informacji, zobacz [skrypt rozruchowy):](https://github.com/Azure-App-Service/ruby/blob/master/2.3.8/startup.sh)

1. Wygeneruj [wartość secret_key_base,](https://edgeguides.rubyonrails.org/security.html#environmental-security) jeśli jeszcze nie istnieje. Ta wartość jest wymagana do uruchomienia aplikacji w trybie produkcyjnym.
1. Ustaw `RAILS_ENV` zmienną `production`środowiskową na .
1. Usuń dowolny plik *pid* w katalogu *tmp/pids,* który pozostał pozostawiony przez poprzednio uruchomiony serwer Rails.
1. Sprawdź, czy wszystkie zależności są zainstalowane. Jeśli nie, spróbuj zainstalować klejnoty z lokalnego *katalogu dostawcy/pamięci podręcznej.*
1. Uruchom polecenie `rails server -e $RAILS_ENV`.

Proces uruchamiania można dostosować w następujący sposób:

- [Obsługa zasobów statycznych](#serve-static-assets)
- [Uruchamianie w trybie nieprodukcyjnym](#run-in-non-production-mode)
- [Ustawianie secret_key_base ręcznie](#set-secret_key_base-manually)

### <a name="serve-static-assets"></a>Obsługa zasobów statycznych

Serwer Rails w kontenerze Ruby działa domyślnie w trybie produkcyjnym i [zakłada, że zasoby są wstępnie kompilowane i są obsługiwane przez serwer www](https://guides.rubyonrails.org/asset_pipeline.html#in-production). Aby obsługiwać zasoby statyczne z serwera Rails, należy wykonać dwie czynności:

- **Wstępnie skompiluj zasoby** - [Wstępnie kompiluj zasoby statyczne lokalnie](https://guides.rubyonrails.org/asset_pipeline.html#local-precompilation) i wdrażaj je ręcznie. Lub pozwól aparatowi wdrażania obsłużyć go zamiast (zobacz [Wstępnie skompiluj zasoby](#precompile-assets).
- **Włącz wyświetlanie plików statycznych** — aby obsługiwać zasoby statyczne z `true`kontenera Ruby, ustaw ustawienie aplikacji [ `RAILS_SERVE_STATIC_FILES` na](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) . Przykład:

    ```azurecli-interactive
    az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings RAILS_SERVE_STATIC_FILES=true
    ```

### <a name="run-in-non-production-mode"></a>Uruchamianie w trybie nieprodukcyjnym

Serwer Rails jest domyślnie uruchamiany w trybie produkcyjnym. Aby uruchomić w trybie rozwoju, `RAILS_ENV` na `development`przykład ustaw ustawienie [aplikacji](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) na .

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings RAILS_ENV="development"
```

Jednak to ustawienie powoduje, że serwer Rails, aby uruchomić w trybie rozwoju, który akceptuje żądania localhost tylko i nie jest dostępny poza kontenerem. Aby akceptować żądania klientów `APP_COMMAND_LINE` zdalnych, ustaw [ustawienie aplikacji](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) na `rails server -b 0.0.0.0`. To ustawienie aplikacji umożliwia uruchomienie niestandardowego polecenia w kontenerze Ruby. Przykład:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings APP_COMMAND_LINE="rails server -b 0.0.0.0"
```

### <a name="set-secret_key_base-manually"></a><a name="set-secret_key_base-manually"></a>Ustawianie secret_key_base ręcznie

Aby użyć `secret_key_base` własnej wartości zamiast pozwolić usłudze App `SECRET_KEY_BASE` Service wygenerować ją dla Ciebie, ustaw [ustawienie aplikacji](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) z odpowiednią wartością. Przykład:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings SECRET_KEY_BASE="<key-base-value>"
```

## <a name="access-diagnostic-logs"></a>Uzyskiwanie dostępu do dzienników diagnostycznych

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>Otwórz sesję SSH w przeglądarce

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

[!INCLUDE [robots933456](../../../includes/app-service-web-configure-robots933456.md)]

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Samouczek: Aplikacja Rails z PostgreSQL](tutorial-ruby-postgres-app.md)

> [!div class="nextstepaction"]
> [Często zadawane pytania dotyczące usługi aplikacji Linux](app-service-linux-faq.md)
