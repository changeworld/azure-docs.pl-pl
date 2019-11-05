---
title: Instalowanie i uruchamianie kontenerów — tłumaczenie tekstu w usłudze Translator
titleSuffix: Azure Cognitive Services
description: Jak pobrać, zainstalować i uruchomić kontenery do analizy tłumaczenie tekstu w usłudze Translator w tym samouczku.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: dapine
ms.openlocfilehash: 7b2b07f71d00e6da66062d1826f8c5c88bb6be7a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73507881"
---
# <a name="install-and-run-translator-text-containers"></a>Instalowanie i uruchamianie kontenerów tłumaczenie tekstu w usłudze Translator

<!--
    ACOM forward link:
    https://docs.microsoft.com/azure/cognitive-services/translator/howto-install-containers
-->

Kontenery umożliwiają uruchamianie tłumaczenie tekstu w usłudze Translator interfejsów API we własnym środowisku i są doskonałe dla konkretnych wymagań dotyczących zabezpieczeń i zarządzania danymi.

## <a name="prerequisites"></a>Wymagania wstępne

Przed użyciem kontenerów tłumaczenie tekstu w usłudze Translator należy spełnić następujące wymagania wstępne:

|Wymagany|Przeznaczenie|
|--|--|
|Aparat platformy Docker| Aparat platformy Docker musi być zainstalowany na [komputerze-hoście](#the-host-computer). Platforma Docker zawiera pakiety, które konfigurują środowisko platformy Docker w systemach [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/)i [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Podstawowe informacje dotyczące platformy Docker i kontenera można znaleźć w temacie [Omówienie platformy Docker](https://docs.docker.com/engine/docker-overview/).<br><br> **W systemie Windows**program Docker musi być również skonfigurowany do obsługi kontenerów systemu Linux.<br><br>|
|Znajomość platformy Docker | Należy dysponować podstawową wiedzą na temat pojęć platformy Docker, takich jak rejestry, repozytoria, kontenery i obrazy kontenerów, a także znajomość podstawowych poleceń `docker`.|

## <a name="request-access-to-the-container-registry"></a>Zażądaj dostępu do rejestru kontenerów

Aby zażądać dostępu do kontenera, musisz najpierw zakończyć i przesłać [formularz żądania dotyczącego kontenerów tłumaczenie tekstu w usłudze translator poznawcze](https://aka.ms/translatorcontainerform) .

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

### <a name="the-host-computer"></a>Komputer-host

Host jest komputerem opartym na architekturze x64 z systemem operacyjnym Linux, który uruchamia kontener platformy Docker. Może to być komputer lokalny lub usługa hostingu platformy Docker na platformie Azure, na przykład:

* Usługa Azure Kubernetes.
* Azure Container Instances.
* Klaster [Kubernetes](https://kubernetes.io/) wdrożony w Azure Stack.

### <a name="container-requirements-and-recommendations"></a>Wymagania i zalecenia dotyczące kontenera

W poniższej tabeli opisano minimalne i zalecane rdzenie procesora CPU, co najmniej 2,6 gigaherca (GHz) lub szybszy i pamięć, w gigabajtach (GB) do przydzielenia dla każdego kontenera tłumaczenie tekstu w usłudze Translator.

| Kontener | Minimalne | Para językowa |
|-----------|---------|---------------|
| Tekst usługi Translator | 4 rdzenie, 4 GB pamięci | 4 |

Dla każdej pary językowej zaleca się posiadanie 1 GB pamięci. Domyślnie kontener tłumaczenie tekstu w usłudze Translator ma 3 lub 4 pary językowe, w zależności od używanej `<image-tag>`. Aby uzyskać szczegółowe informacje, zobacz [obsługiwane języki i tłumaczenie](#supported-languages-and-translation) . Rdzeń i pamięć odpowiadają ustawieniom `--cpus` i `--memory`, które są używane jako część polecenia `docker run`.

## <a name="get-the-container-image-with-docker-pull"></a>Pobierz obraz kontenera za pomocą `docker pull`

Obrazy kontenerów dla tłumaczenie tekstu w usłudze Translator są dostępne w następującym repozytorium kontenerów. Tabela mapuje także Tagi obrazu kontenera i odpowiadające im języki obsługiwane.

| Kontener | Tag obrazu | Obsługiwane języki |
|-----------|-----------|---------------------|
| `containerpreview.azurecr.io/microsoft/cognitive-services-translator-text` | `ar_de_en_ru_zh_1.0.0` | `ar-SA`, `zh-CN`, `de-DE`i `ru-RU` |
| `containerpreview.azurecr.io/microsoft/cognitive-services-translator-text` | `de_en_es_fr_1.0.0` | `de-DE`, `fr-FR`i `es-ES` |

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-translator-text-container"></a>Wypychanie platformy Docker dla kontenera tłumaczenie tekstu w usłudze Translator

Aby wykonać [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) polecenie, musisz najpierw uzyskać dostęp do rejestru kontenerów. W interfejsie wiersza polecenia platformy Azure możesz zalogować się do Azure Container Registry przy użyciu [`az acr login`](https://docs.microsoft.com/cli/azure/acr?view=azure-cli-latest#az-acr-login) polecenie.

```azureinteractive
az acr login --name containerpreview.azurecr.io
```

To polecenie spowoduje uwierzytelnienie bieżącego użytkownika przy użyciu odpowiednich Azure Container Registry. Teraz możesz wykonać `docker pull` polecenie.

> [!NOTE]
> W zależności od wymaganej obsługi języka podaj odpowiednie `<image-tag>`.

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-translator-text:<image-tag>
```

## <a name="how-to-use-the-container"></a>Jak używać kontenera

Gdy kontener znajduje się na [komputerze hosta](#the-host-computer), użyj następującego procesu, aby współpracować z kontenerem.

1. [Uruchom kontener](#run-the-container-with-docker-run). Więcej [przykładów](translator-text-container-config.md#example-docker-run-commands) polecenia `docker run` są dostępne.
1. [Zbadaj punkt końcowy tłumaczenia kontenera](#query-the-containers-translate-endpoint).

## <a name="run-the-container-with-docker-run"></a>Uruchom kontener za pomocą `docker run`

Użyj polecenia [Docker Run](https://docs.docker.com/engine/reference/commandline/run/) , aby uruchomić jeden z trzech kontenerów. [Przykłady](translator-text-container-config.md#example-docker-run-commands) polecenia `docker run` są dostępne.

### <a name="run-container-example-of-docker-run-command"></a>Przykład uruchomienia kontenera dla polecenia Docker Run

```Docker
docker run --rm -it -p 5000:80 --memory 4g --cpus 4 \
containerpreview.azurecr.io/microsoft/cognitive-services-translator-text:ar_de_en_ru_zh_1.0.0 \
Eula=accept
```

To polecenie:

* Uruchamia tłumaczenie tekstu w usłudze Translator kontener z obrazu kontenera
* Przydziela 4 rdzenie procesora CPU i 4 gigabajty (GB) pamięci
* Udostępnia port TCP 5000 i przydziela pseudo-TTY dla kontenera
* Akceptuje umowę użytkownika końcowego (EULA)
* Automatycznie usuwa kontener po zakończeniu. Obraz kontenera jest nadal dostępny na komputerze hosta

### <a name="how-to-collect-docker-logs"></a>Jak zbierać dzienniki platformy Docker

W celu rozwiązywania problemów warto wyświetlić dzienniki platformy Docker z poziomu wykonywania kontenera. Najpierw należy wykonać polecenie [Docker PS](https://docs.docker.com/engine/reference/commandline/ps/) z flagą formatowania, aby ograniczyć Szczegóły wyświetlane dla wszystkich kontenerów.

```Docker
docker ps -a --format "table {{.ID}}\t{{.Image}}\t{{.Status}}"

CONTAINER ID    IMAGE                                                                       STATUS
ed6f115697f3    containerpreview.azurecr.io/microsoft/cognitive-services-translator-text    Up 4 minutes
```

Następnie uruchom polecenie [Docker Logs](https://docs.docker.com/engine/reference/commandline/logs/) z `<Container ID>` dla odpowiedniego kontenera, aby wyświetlić jego dzienniki.

```Docker
docker logs <Container ID> --timestamps --since=4h | grep Error
```

Powyższym poleceniem Docker Logs jest zbieranie dzienników błędów dla ostatnich czterech godzin.

## <a name="supported-languages-and-translation"></a>Obsługiwane języki i tłumaczenie

Metoda `POST /translate` obsługuje następujące konwersje języków, które są przenoszone z *angielskiej* do języka docelowego i na odwrót. Należy pamiętać, że w przypadku przejścia do i z języka angielskiego z jednym z wymienionych języków *nie można* przejść z jednego języka *w języku innym* *niż angielski.*

> [!NOTE]
> W celu uzyskania optymalnej jakości konsumenci powinni wysyłać tylko jedno zdanie na żądanie.

| Konwersja języka | Konwersja języka ISO | Tagi obrazu |
|--|--|:--|
| Angielski: left_right_arrow: chiński | `en-US`: left_right_arrow: `zh-CN` | `ar_de_en_ru_zh_1.0.0` |
| Angielski: left_right_arrow: rosyjski | `en-US`: left_right_arrow: `ru-RU` | `ar_de_en_ru_zh_1.0.0` |
| Angielski: left_right_arrow: arabski | `en-US`: left_right_arrow: `ar-SA` | `ar_de_en_ru_zh_1.0.0` |
| Angielski: left_right_arrow: niemiecki | `en-US`: left_right_arrow: `de-DE` | `ar_de_en_ru_zh_1.0.0` i `de_en_es_fr_1.0.0` |
| Angielski: left_right_arrow: hiszpański | `en-US`: left_right_arrow: `es-ES` | `de_en_es_fr_1.0.0` |
| Angielski: left_right_arrow: francuski | `en-US`: left_right_arrow: `fr-FR` | `de_en_es_fr_1.0.0` |

> [!IMPORTANT]
> Aby można było uruchomić kontener, należy określić `Eula`. w przeciwnym razie kontener nie zostanie uruchomiony.

## <a name="query-the-containers-translate-endpoint"></a>Zbadaj punkt końcowy tłumaczenia kontenera

Kontener udostępnia interfejs API usługi tłumaczenia opartego na protokole REST. Poniżej przedstawiono kilka przykładowych zastosowań tego punktu końcowego:

# <a name="curltabcurl"></a>[cURL](#tab/curl)


Wykonaj następujące polecenie zwinięcie z żądanego interfejsu wiersza polecenia.

```curl
curl -X POST "http://localhost:5000/translate?api-version=3.0&from=en-US&to=de-DE"
    -H "Content-Type: application/json" -d "[{'Text':'hello, how are you'}]"
```

> [!TIP]
> Jeśli próbujesz wykonać tę kompilację przed gotowością kontenera, otrzymasz odpowiedź "usługa jest tymczasowo niedostępna". Po prostu poczekaj na zakończenie kontenera, a następnie spróbuj ponownie.

Następujące dane wyjściowe będą drukowane w konsoli programu.

```console
"translations": [
    {
        "text": "hallo, wie geht es dir",
        "to": "de-DE"
    }
]
```

# <a name="swaggertabswagger"></a>[Swagger](#tab/Swagger)

Przejdź do strony Swagger, http://localhost:5000/swagger/index.html

1. Wybierz pozycję **post/translate**
1. Wybierz pozycję **Wypróbuj**
1. Wprowadź parametr **from** jako `en-US`
1. Wprowadź parametr **do** jako `de-DE`
1. Wprowadź parametr **API-Version** jako `3.0`
1. W obszarze **teksty**zastąp `string` następującym kodem JSON
    ```json
    [
        {
            "text": "hello, how are you"
        }
    ]
    ```
1. Wybierz pozycję **Execute**, wynikowe tłumaczenia są wyprowadzane w **treści odpowiedzi**. Należy oczekiwać, że coś podobnego do poniższego:
    ```json
    "translations": [
      {
          "text": "hallo, wie geht es dir",
          "to": "de-DE"
      }
    ]
    ```

# <a name="net-coretabnetcore"></a>[.NET Core](#tab/netcore)

1. Uruchom program Visual Studio i Utwórz nową aplikację konsolową.
1. Edytuj plik `*.csproj`, aby dodać węzeł `<LangVersion>7.1</LangVersion>` — spowoduje to określenie C# 7,1.
1. Dodaj pakiet NuGet [Newtonsoft. JSON](https://www.nuget.org/packages/Newtonsoft.Json/) , wersja 11.0.2.
1. W `Program.cs`Zastąp cały istniejący kod następującym kodem:
    ```csharp
    using Newtonsoft.Json;
    using System;
    using System.Net.Http;
    using System.Text;
    using System.Threading.Tasks;

    namespace TranslateContainer
    {
        class Program
        {
            const string ApiHostEndpoint = "http://localhost:5000";
            const string TranslateApi = "/translate?api-version=3.0&from=en-US&to=de-DE";

            static async Task Main(string[] args)
            {
                var textToTranslate = "hello, how are you";
                var result = await TranslateTextAsync(textToTranslate);

                Console.WriteLine(result);
                Console.ReadLine();
            }

            static async Task<string> TranslateTextAsync(string textToTranslate)
            {
                var body = new object[] { new { Text = textToTranslate } };
                var requestBody = JsonConvert.SerializeObject(body);

                var client = new HttpClient();
                using (var request =
                    new HttpRequestMessage
                    {
                        Method = HttpMethod.Post,
                        RequestUri = new Uri($"{ApiHostEndpoint}{TranslateApi}"),
                        Content = new StringContent(requestBody, Encoding.UTF8, "application/json")
                    })
                {
                    // Send the request and await a response.
                    var response = await client.SendAsync(request);

                    return await response.Content.ReadAsStringAsync();
                }
            }
        }
    }
    ```
1. Naciśnij klawisz **F5** , aby uruchomić program.
1. Następujące dane wyjściowe będą drukowane w konsoli programu.
    ```console
    "translations": [
      {
          "text": "hallo, wie geht es dir",
          "to": "de-DE"
      }
    ]
    ```

***

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Zatrzymaj kontener

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli uruchamiasz kontener z [instalacją wyjściową](translator-text-container-config.md#mount-settings) i włączono rejestrowanie, kontener generuje pliki dziennika, które są przydatne do rozwiązywania problemów występujących podczas uruchamiania lub uruchamiania kontenera.

<!--blogs/samples/video course -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Podsumowanie

W tym artykule przedstawiono koncepcje i przepływ pracy służące do pobierania, instalowania i uruchamiania kontenerów tłumaczenie tekstu w usłudze Translator. Podsumowanie:

* Tłumaczenie tekstu w usłudze Translator udostępnia wiele kontenerów systemu Linux dla platformy Docker, hermetyzowając różne pary językowe.
* Obrazy kontenerów są pobierane z rejestru "w wersji zapoznawczej".
* Obrazy kontenerów są uruchamiane w platformie Docker.
* Można użyć interfejsu API REST lub zestawu SDK do wywoływania operacji w kontenerach tłumaczenie tekstu w usłudze Translator, określając identyfikator URI hosta kontenera.

## <a name="next-steps"></a>Następne kroki

* Przegląd [konfigurowania kontenerów](translator-text-container-config.md) dla ustawień konfiguracji
* Zapoznaj się z [kontenerem często zadawanych pytań (FAQ)](../containers/container-faq.md) , aby rozwiązać problemy związane z funkcjonalnością.
