---
title: Ustawienia kontenera platformy Docker — usługa LUIS
titleSuffix: Azure Cognitive Services
description: Środowisko wykonawcze kontenera usługi LUIS `docker run` jest konfigurowane przy użyciu argumentów polecenia. Usługa LUIS ma kilka wymaganych ustawień wraz z kilkoma ustawieniami opcjonalnymi.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 48a9856c58a815eabcc0b105efcd548e66ddd552
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80874215"
---
# <a name="configure-language-understanding-docker-containers"></a>Konfigurowanie kontenerów dokowania opisu języka 

Środowisko wykonawcze kontenera **zrozumienia języka** (LUIS) `docker run` jest konfigurowane przy użyciu argumentów polecenia. Usługa LUIS ma kilka wymaganych ustawień wraz z kilkoma ustawieniami opcjonalnymi. Dostępnych jest kilka [przykładów](#example-docker-run-commands) polecenia. Ustawienia specyficzne dla kontenera to [ustawienia instalacji](#mount-settings) wejściowej i ustawienia rozliczeń. 

## <a name="configuration-settings"></a>Ustawienia konfiguracji

Ten kontener ma następujące ustawienia konfiguracji:

|Wymagany|Ustawienie|Przeznaczenie|
|--|--|--|
|Tak|[Klawisz ApiKey](#apikey-setting)|Służy do śledzenia informacji rozliczeniowych.|
|Nie|[ApplicationInsights](#applicationinsights-setting)|Umożliwia dodawanie obsługi telemetrii [usługi Azure Application Insights](https://docs.microsoft.com/azure/application-insights) do kontenera.|
|Tak|[Rozliczenia](#billing-setting)|Określa identyfikator URI punktu końcowego zasobu usługi na platformie Azure.|
|Tak|[Eula](#eula-setting)| Wskazuje, że licencja dla kontenera została zaakceptowana.|
|Nie|[Biegle](#fluentd-settings)|Zapis dziennika i, opcjonalnie, dane metryki na serwerze Fluentd.|
|Nie|[Http Proxy](#http-proxy-credentials-settings)|Konfigurowanie serwera proxy HTTP do wysyłania żądań wychodzących.|
|Nie|[Rejestrowanie](#logging-settings)|Zapewnia ASP.NET podstawową obsługę rejestrowania kontenera. |
|Tak|[Wierzchowce](#mount-settings)|Odczytywanie i zapisywanie danych z komputera-hosta do kontenera i z kontenera z powrotem do komputera hosta.|

> [!IMPORTANT]
> Ustawienia [`ApiKey`](#apikey-setting) [`Billing`](#billing-setting)i [`Eula`](#eula-setting) ustawienia są używane razem i należy podać prawidłowe wartości dla wszystkich trzech z nich; w przeciwnym razie kontener nie zostanie uruchomiony. Aby uzyskać więcej informacji na temat używania tych ustawień konfiguracji do tworzenia wystąpienia kontenera, zobacz [Rozliczenia](luis-container-howto.md#billing).

## <a name="apikey-setting"></a>Ustawienie apikey

Ustawienie `ApiKey` określa klucz zasobów platformy Azure używany do śledzenia informacji rozliczeniowych dla kontenera. Należy określić wartość dla apikey i wartość musi być prawidłowy klucz [`Billing`](#billing-setting) dla zasobu usług Cognitive _Services_ określonych dla ustawienia konfiguracji.

To ustawienie można znaleźć w następujących miejscach:

* Portal Azure: Zarządzanie zasobami **usług Cognitive Services** w obszarze **Klucze**
* Portal usługi LUIS: klucze i strona **ustawień punktu końcowego.** 

Nie należy używać klucza startowego ani klucza autora. 

## <a name="applicationinsights-setting"></a>Ustawienie ApplicationInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-setting"></a>Ustawienie rozliczeń

Ustawienie `Billing` określa identyfikator URI punktu końcowego zasobu _usług Cognitive Services_ na platformie Azure używanego do pomiaru informacji rozliczeniowych dla kontenera. Należy określić wartość dla tego ustawienia konfiguracji, a wartość musi być prawidłowym identyfikatorem URI punktu końcowego dla zasobu _usług Cognitive Services_ na platformie Azure. Kontener raportuje użycie co 10 do 15 minut.

To ustawienie można znaleźć w następujących miejscach:

* Portal Azure: Omówienie **usług Cognitive Services,** oznaczone etykietą`Endpoint`
* Portal usługi LUIS: **klucze i** strony ustawień punktu końcowego, jako część identyfikatora URI punktu końcowego.

| Wymagany | Nazwa | Typ danych | Opis |
|----------|------|-----------|-------------|
| Tak      | `Billing` | ciąg | Identyfikator URI punktu końcowego rozliczeń. Aby uzyskać więcej informacji na temat uzyskiwania identyfikatora URI rozliczeń, zobacz [zbieranie wymaganych parametrów](luis-container-howto.md#gathering-required-parameters). Aby uzyskać więcej informacji i pełną listę regionalnych punktów końcowych, zobacz [Niestandardowe nazwy poddomen dla usług Cognitive Services](../cognitive-services-custom-subdomains.md). |

## <a name="eula-setting"></a>Ustawienie Eula

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Płynne ustawienia

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Ustawienia poświadczeń serwera proxy HTTP

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Ustawienia logowania
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>Ustawienia instalacji

Użyj powiązania wierzchowce do odczytu i zapisu danych do i z kontenera. Można określić mocowanie wejściowe lub `--mount` mocowanie wyjściowe, określając opcję w poleceniu [uruchamiania platformy docker.](https://docs.docker.com/engine/reference/commandline/run/) 

Kontener usługi LUIS nie używa instalacji wejściowych lub wyjściowych do przechowywania danych szkoleniowych lub usługowych. 

Dokładna składnia lokalizacji instalacji hosta różni się w zależności od systemu operacyjnego hosta. Ponadto lokalizacja instalacji [komputera-hosta](luis-container-howto.md#the-host-computer)może być niedostępna z powodu konfliktu między uprawnieniami używanymi przez konto usługi platformy docker a uprawnieniami lokalizacji instalacji hosta. 

W poniższej tabeli opisano obsługiwane ustawienia.

|Wymagany| Nazwa | Typ danych | Opis |
|-------|------|-----------|-------------|
|Tak| `Input` | Ciąg | Miejsce docelowe instalacji wejściowej. Wartością domyślną jest `/input`. Jest to lokalizacja plików pakietu usługi LUIS. <br><br>Przykład:<br>`--mount type=bind,src=c:\input,target=/input`|
|Nie| `Output` | Ciąg | Miejsce docelowe mocowania wyjściowego. Wartością domyślną jest `/output`. Jest to lokalizacja dzienników. Obejmuje to dzienniki zapytań usługi LUIS i dzienniki kontenerów. <br><br>Przykład:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Przykładowe polecenia uruchamiania platformy dok.

Poniższe przykłady używają ustawień konfiguracji, aby zilustrować sposób pisania i używania `docker run` poleceń.  Po uruchomieniu kontener będzie nadal działał, dopóki go nie [zatrzymasz.](luis-container-howto.md#stop-the-container)

* W tych przykładach katalog `C:` jest wyłączony z dysku, aby uniknąć konfliktów uprawnień w systemie Windows. Jeśli musisz użyć określonego katalogu jako katalogu wejściowego, może być konieczne udzielenie uprawnienia usługi docker. 
* Nie należy zmieniać kolejności argumentów, chyba że są bardzo zaznajomieni z kontenerów docker.
* Jeśli używasz innego systemu operacyjnego, użyj poprawnej konsoli/terminala, składni folderu dla instalacji i znaku kontynuacji linii dla systemu. W tych przykładach przyjęto założenie, `^`że konsola systemu Windows ma znak kontynuacji wiersza . Ponieważ kontener jest systemem operacyjnym Linux, instalacja docelowa używa składni folderów w stylu linuksa.

Zamień {_argument_name_} własnymi wartościami:

| Symbol zastępczy | Wartość | Format lub przykład |
|-------------|-------|---|
| **{API_KEY}** | Klucz punktu końcowego `LUIS` zasobu `LUIS` na stronie Klucze platformy Azure. | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` |
| **{ENDPOINT_URI}** | Wartość punktu końcowego rozliczeń jest `LUIS` dostępna na stronie Przegląd platformy Azure.| Zobacz [zbieranie wymaganych parametrów](luis-container-howto.md#gathering-required-parameters) dla jawnych przykładów. |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> Aby `Eula` `Billing`uruchomić `ApiKey` kontener, należy określić opcje i opcje; w przeciwnym razie kontener nie zostanie uruchomiony. Aby uzyskać więcej informacji, zobacz [Rozliczenia](luis-container-howto.md#billing).
> Wartość ApiKey jest **kluczem** ze strony Klucze i punkty końcowe w `Cognitive Services` portalu usługi LUIS i jest również dostępna na stronie klucze zasobów platformy Azure. 

### <a name="basic-example"></a>Przykład podstawowy

Poniższy przykład ma najmniejszą możliwą do uruchomienia kontenera:

```console
docker run --rm -it -p 5000:5000 --memory 4g --cpus 2 ^
--mount type=bind,src=c:\input,target=/input ^
--mount type=bind,src=c:\output,target=/output ^
mcr.microsoft.com/azure-cognitive-services/luis:latest ^
Eula=accept ^
Billing={ENDPOINT_URL} ^
ApiKey={API_KEY}
```

### <a name="applicationinsights-example"></a>Przykład applicationinsights

W poniższym przykładzie ustawia ApplicationInsights argument do wysyłania danych telemetrycznych do usługi Application Insights, gdy kontener jest uruchomiony:

```console
docker run --rm -it -p 5000:5000 --memory 6g --cpus 2 ^
--mount type=bind,src=c:\input,target=/input ^
--mount type=bind,src=c:\output,target=/output ^
mcr.microsoft.com/azure-cognitive-services/luis:latest ^
Eula=accept ^
Billing={ENDPOINT_URL} ^
ApiKey={API_KEY} ^
InstrumentationKey={INSTRUMENTATION_KEY}
```

### <a name="logging-example"></a>Przykład rejestrowania 

Następujące polecenie ustawia poziom rejestrowania, `Logging:Console:LogLevel`aby skonfigurować poziom [`Information`](https://msdn.microsoft.com)rejestrowania na . 

```console
docker run --rm -it -p 5000:5000 --memory 6g --cpus 2 ^
--mount type=bind,src=c:\input,target=/input ^
--mount type=bind,src=c:\output,target=/output ^
mcr.microsoft.com/azure-cognitive-services/luis:latest ^
Eula=accept ^
Billing={ENDPOINT_URL} ^
ApiKey={API_KEY} ^
Logging:Console:LogLevel:Default=Information
```

## <a name="next-steps"></a>Następne kroki

* Sprawdź, [jak zainstalować i uruchomić kontenery](luis-container-howto.md)
* Zapoznaj się [z rozwiązywaniem problemów](troubleshooting.md) związanych z funkcją usługi LUIS.
* Użyj większej liczby [kontenerów usług Cognitive Services](../cognitive-services-container-support.md)
