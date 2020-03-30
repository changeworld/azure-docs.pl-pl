---
title: 'Szybki start: Tworzenie aplikacji Linux Ruby'
description: Rozpocznij pracę z aplikacjami systemu Linux w usłudze Azure App Service, wdrażając pierwszą aplikację Ruby w kontenerze systemu Linux w usłudze App Service.
keywords: usługa azure app, linux, oss, ruby, rails
ms.assetid: 6d00c73c-13cb-446f-8926-923db4101afa
ms.topic: quickstart
ms.date: 07/11/2019
ms.custom: mvc, cli-validate, seodec18
ms.openlocfilehash: 33723e6dd8fbcd91d2d5863816786c0f08cdf923
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "80045832"
---
# <a name="create-a-ruby-on-rails-app-in-app-service-on-linux"></a>Informacje na temat tworzenia aplikacji Ruby on Rails w usłudze App Service w systemie Linux

[Usługa App Service w systemie Linux](app-service-linux-intro.md) zapewnia wysoce skalowalną, samoładującą się usługę hostingową przy użyciu systemu operacyjnego Linux. W tym samouczku szybki start pokazano, jak wdrożyć aplikację Ruby on Rails w usłudze Azure App Service w systemie Linux przy użyciu [powłoki w chmurze.](https://docs.microsoft.com/azure/cloud-shell/overview)

> [!NOTE]
> Obecnie stos programistyczny Ruby obsługuje jedynie aplikację Ruby on Rails. Jeśli chcesz użyć innej platformy, takiej jak Sinatra, lub jeśli chcesz użyć [nieobsługiconej wersji Ruby,](app-service-linux-intro.md)musisz [uruchomić ją w niestandardowym kontenerze.](quickstart-docker-go.md)

![Hello-world](./media/quickstart-ruby/hello-world-configured.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* <a href="https://www.ruby-lang.org/en/documentation/installation/#rubyinstaller" target="_blank">Zainstaluj Ruby 2.6 lub nowszą</a>
* <a href="https://git-scm.com/" target="_blank">Zainstaluj oprogramowanie Git</a>

## <a name="download-the-sample"></a>Pobierz przykład

W oknie terminala uruchom następujące polecenie, aby sklonować przykładowe repozytorium aplikacji na maszynę lokalną:

```bash
git clone https://github.com/Azure-Samples/ruby-docs-hello-world
```

## <a name="run-the-application-locally"></a>Uruchamianie aplikacji lokalnie

Uruchom aplikację lokalnie, aby zobaczyć, jak powinna ona wyglądać, gdy wdrożysz ją na platformie Azure. Otwórz okno terminala, zmień katalog na `hello-world` i użyj polecenia `rails server`, aby uruchomić serwer.

Pierwszym krokiem jest zainstalowanie wymaganych rozwiązań Gem. Istnieje `Gemfile` zawarte w przykładzie, więc po prostu uruchom następujące polecenie:

```bash
bundle install
```

Po zainstalowaniu rozwiązań Gem użyjemy do uruchomienia aplikacji programu instalującego niezamówione pakiety oprogramowania:

```bash
bundle exec rails server
```

Przy użyciu przeglądarki internetowej przejdź pod adres `http://localhost:3000`, aby przetestować aplikację lokalnie.

![Skonfigurowana aplikacja Hello World](./media/quickstart-ruby/hello-world-updated.png)

[!INCLUDE [Try Cloud Shell](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user.md)]

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux.md)]

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux.md)]

## <a name="create-a-web-app"></a>Tworzenie aplikacji internetowej

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-ruby-linux-no-h.md)] 

Przejdź do aplikacji, aby wyświetlić nowo utworzoną aplikację internetową z wbudowanym obrazem. Zamień _ &lt;nazwę aplikacji>_ nazwą aplikacji internetowej.

```bash
http://<app_name>.azurewebsites.net
```

Tak powinna wyglądać nowa aplikacja internetowa:

![Strona powitalna](./media/quickstart-ruby/splash-page.png)

## <a name="deploy-your-application"></a>Wdrażanie aplikacji

Uruchom następujące polecenia, aby wdrożyć aplikację lokalną w aplikacji sieci web platformy Azure:

```bash
git remote add azure <Git deployment URL from above>
git push azure master
```

Upewnij się, że operacje zdalnego wdrażania raportują powodzenie. Polecenia tworzą dane wyjściowe podobne do następującego tekstu:

```bash
remote: Using turbolinks 5.2.0
remote: Using uglifier 4.1.20
remote: Using web-console 3.7.0
remote: Bundle complete! 18 Gemfile dependencies, 78 gems now installed.
remote: Bundled gems are installed into `/tmp/bundle`
remote: Zipping up bundle contents
remote: .......
remote: ~/site/repository
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Deployment successful.
remote: App container will begin restart within 10 seconds.
To https://<app-name>.scm.azurewebsites.net/<app-name>.git
   a6e73a2..ae34be9  master -> master
```

Po zakończeniu wdrażania poczekaj około 10 sekund na ponowne uruchomienie aplikacji sieci web, a następnie przejdź do aplikacji sieci web i sprawdź wyniki.

```bash
http://<app-name>.azurewebsites.net
```

![uaktualniona aplikacja internetowa](./media/quickstart-ruby/hello-world-configured.png)

> [!NOTE]
> Podczas ponownego uruchamiania aplikacji można zaobserwować `Error 503 Server unavailable` kod stanu HTTP `Hey, Ruby developers!` w przeglądarce lub stronę domyślną. Pełne ponowne uruchomienie aplikacji może potrwać kilka minut.
>

[!INCLUDE [Clean-up section](../../../includes/cli-script-clean-up.md)]

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tutorial: Ruby on Rails z Postgres](tutorial-ruby-postgres-app.md)

> [!div class="nextstepaction"]
> [Konfigurowanie aplikacji Ruby](configure-language-ruby.md)
