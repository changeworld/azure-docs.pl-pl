---
title: Ustawienia kontenera Docker — LUIS
titleSuffix: Azure Cognitive Services
description: Środowisko uruchomieniowe kontenera LUIS jest konfigurowane przy użyciu argumentów polecenia `docker run`. LUIS ma kilka wymaganych ustawień oraz kilka opcjonalnych ustawień.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/07/2019
ms.author: dapine
ms.openlocfilehash: a30fcd0ec7e53c78876596baf787639e81c638db
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2019
ms.locfileid: "73795010"
---
# <a name="configure-language-understanding-docker-containers"></a>Konfigurowanie Language Understanding kontenerów platformy Docker 

Środowisko uruchomieniowe kontenera **Language Understanding** (Luis) jest konfigurowane przy użyciu argumentów polecenia `docker run`. LUIS ma kilka wymaganych ustawień oraz kilka opcjonalnych ustawień. Kilka [przykładów](#example-docker-run-commands) polecenia jest dostępnych. Ustawienia dotyczące określonego kontenera to dane wejściowe ustawień [instalacji](#mount-settings) i rozliczeń. 

## <a name="configuration-settings"></a>Ustawienia konfiguracji

Ten kontener ma następujące ustawienia konfiguracji:

|Wymagany|Ustawienie|Przeznaczenie|
|--|--|--|
|Tak|[ApiKey](#apikey-setting)|Służy do śledzenia informacji dotyczących rozliczeń.|
|Nie|[ApplicationInsights](#applicationinsights-setting)|Umożliwia dodanie obsługi telemetrii [usługi Azure Application Insights](https://docs.microsoft.com/azure/application-insights) do kontenera.|
|Tak|[Rozliczenia](#billing-setting)|Określa identyfikator URI punktu końcowego zasobu usługi na platformie Azure.|
|Tak|[Umowy](#eula-setting)| Wskazuje, że licencja dla kontenera została zaakceptowana.|
|Nie|[Pozostało](#fluentd-settings)|Zapisz dziennik i, opcjonalnie, dane metryki na serwerze z systemem.|
|Nie|[Serwer proxy http](#http-proxy-credentials-settings)|Skonfiguruj serwer proxy HTTP do wykonywania żądań wychodzących.|
|Nie|[Rejestrować](#logging-settings)|Zapewnia obsługę rejestrowania ASP.NET Core dla kontenera. |
|Tak|[Instaluje](#mount-settings)|Odczytaj i Zapisz dane z komputera hosta do kontenera oraz z kontenera z powrotem do komputera hosta.|

> [!IMPORTANT]
> Ustawienia [`ApiKey`](#apikey-setting), [`Billing`](#billing-setting)i [`Eula`](#eula-setting) są używane razem i należy podać prawidłowe wartości dla wszystkich trzech z nich; w przeciwnym razie kontener nie zostanie uruchomiony. Aby uzyskać więcej informacji na temat tworzenia wystąpienia kontenera przy użyciu tych ustawień konfiguracji, zobacz [rozliczenia](luis-container-howto.md#billing).

## <a name="apikey-setting"></a>Ustawienie ApiKey

Ustawienie `ApiKey` określa klucz zasobów platformy Azure służący do śledzenia informacji rozliczeniowych dla kontenera. Należy określić wartość ApiKey, a wartość musi być prawidłowym kluczem dla zasobu _Cognitive Services_ określonego dla ustawienia konfiguracji [`Billing`](#billing-setting) .

To ustawienie można znaleźć w następujących miejscach:

* Azure Portal: **Cognitive Services** zarządzania zasobami w obszarze **klucze**
* Portal LUIS: strony **ustawień kluczy i punktów końcowych** . 

Nie używaj klucza początkowego ani klucza tworzenia. 

## <a name="applicationinsights-setting"></a>Ustawienie ApplicationInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-setting"></a>Ustawienie rozliczeń

Ustawienie `Billing` określa identyfikator URI punktu końcowego zasobu _Cognitive Services_ na platformie Azure używany do pomiaru informacji rozliczeniowych dla kontenera. Należy określić wartość tego ustawienia konfiguracji, a wartość musi być prawidłowym identyfikatorem URI punktu końcowego dla zasobu _Cognitive Services_ na platformie Azure. Kontener zgłasza użycie co 10 do 15 minut.

To ustawienie można znaleźć w następujących miejscach:

* Azure Portal: **Cognitive Services** Omówienie z etykietą `Endpoint`
* Portal LUIS: Strona **Ustawienia klucze i punkt końcowy** jako część identyfikatora URI punktu końcowego.

| Wymagany | Nazwa | Typ danych | Opis |
|----------|------|-----------|-------------|
| Tak      | `Billing` | ciąg | Identyfikator URI punktu końcowego rozliczenia. Aby uzyskać więcej informacji na temat uzyskiwania identyfikatora URI rozliczeń, zobacz [zbieranie wymaganych parametrów](luis-container-howto.md#gathering-required-parameters). Aby uzyskać więcej informacji i pełną listę regionalnych punktów końcowych, zobacz [niestandardowe nazwy domen poddomen dla Cognitive Services](../cognitive-services-custom-subdomains.md). |

## <a name="eula-setting"></a>Ustawienie umowy EULA

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Ustawienia pozostały

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Ustawienia poświadczeń serwera proxy HTTP

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Ustawienia rejestrowania
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>Ustawienia instalacji

Użyj instalacji powiązań, aby odczytywać i zapisywać dane w kontenerze i z niego. Można określić instalację wejściową lub instalację wyjściową, określając opcję `--mount` w poleceniu [Docker Run](https://docs.docker.com/engine/reference/commandline/run/) . 

Kontener LUIS nie używa instalacji danych wejściowych lub wyjściowych do przechowywania danych szkoleniowych lub usług. 

Dokładna składnia lokalizacji instalacji hosta różni się w zależności od systemu operacyjnego hosta. Ponadto lokalizacja instalacji [komputera hosta](luis-container-howto.md#the-host-computer)może być niedostępna z powodu konfliktu między uprawnieniami używanymi przez konto usługi platformy Docker i uprawnieniami lokalizacji instalacji hosta. 

W poniższej tabeli opisano obsługiwane ustawienia.

|Wymagany| Nazwa | Typ danych | Opis |
|-------|------|-----------|-------------|
|Tak| `Input` | Ciąg | Obiekt docelowy instalacji wejściowej. Wartość domyślna to `/input`. Jest to lokalizacja plików pakietu LUIS. <br><br>Przykład:<br>`--mount type=bind,src=c:\input,target=/input`|
|Nie| `Output` | Ciąg | Obiekt docelowy instalacji wyjściowej. Wartość domyślna to `/output`. Jest to lokalizacja dzienników. Dotyczy to również dzienników zapytań LUIS i dzienników kontenerów. <br><br>Przykład:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Przykładowe polecenia uruchamiania platformy Docker

W poniższych przykładach użyto ustawień konfiguracji, aby zilustrować sposób pisania i używania poleceń `docker run`.  Po uruchomieniu kontenera kontynuuje działanie, dopóki nie zostanie [zatrzymane](luis-container-howto.md#stop-the-container) .

* W poniższych przykładach można użyć katalogu poza dyskiem `C:`, aby uniknąć konfliktów uprawnień w systemie Windows. Jeśli musisz użyć określonego katalogu jako katalogu wejściowego, może być konieczne przyznanie uprawnienia usługi Docker. 
* Nie zmieniaj kolejności argumentów, o ile nie znasz już kontenerów platformy Docker.
* W przypadku korzystania z innego systemu operacyjnego należy użyć właściwej konsoli/terminalu, składni folderu dla instalacji i znaku kontynuacji wiersza dla systemu. W poniższych przykładach przyjęto założenie, że konsola systemu Windows ma `^`znak kontynuacji wiersza. Ponieważ kontener jest systemem operacyjnym Linux, instalacja docelowa używa składni folderu w stylu systemu Linux.

Zastąp {_argument_name_} własnymi wartościami:

| Symbol zastępczy | Wartość | Format lub przykład |
|-------------|-------|---|
| **{API_KEY}** | Klucz punktu końcowego zasobu `LUIS` na stronie klucze `LUIS` platformy Azure. | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` |
| **{ENDPOINT_URI}** | Wartość punktu końcowego rozliczenia jest dostępna na stronie Przegląd `LUIS` platformy Azure.| Zobacz [zbieranie wymaganych parametrów](luis-container-howto.md#gathering-required-parameters) dla jawnych przykładów. |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> Aby można było uruchomić kontener, należy określić opcje `Eula`, `Billing`i `ApiKey`. w przeciwnym razie kontener nie zostanie uruchomiony. Aby uzyskać więcej informacji, zobacz [rozliczenia](luis-container-howto.md#billing).
> Wartość ApiKey jest **kluczem** ze strony klucze i punkty końcowe w portalu Luis i jest również dostępna na stronie klucze zasobów `Cognitive Services` platformy Azure. 

### <a name="basic-example"></a>Przykład podstawowy

Poniższy przykład ma najmniejszą liczbę argumentów możliwych do uruchomienia kontenera:

```console
docker run --rm -it -p 5000:5000 --memory 4g --cpus 2 ^
--mount type=bind,src=c:\input,target=/input ^
--mount type=bind,src=c:\output,target=/output ^
mcr.microsoft.com/azure-cognitive-services/luis:latest ^
Eula=accept ^
Billing={ENDPOINT_URL} ^
ApiKey={API_KEY}
```

### <a name="applicationinsights-example"></a>Przykład ApplicationInsights

Poniższy przykład ustawia argument ApplicationInsights, aby wysyłać dane telemetryczne do Application Insights, gdy kontener jest uruchomiony:

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

Następujące polecenie ustawia poziom rejestrowania `Logging:Console:LogLevel`, aby skonfigurować poziom rejestrowania na [`Information`](https://msdn.microsoft.com). 

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

* Zapoznaj [się z tematem Instalowanie i uruchamianie kontenerów](luis-container-howto.md)
* [Rozwiązywanie](troubleshooting.md) problemów związanych z działaniem funkcji Luis można znaleźć w rozwiązaniu.
* Użyj więcej [kontenerów Cognitive Services](../cognitive-services-container-support.md)
