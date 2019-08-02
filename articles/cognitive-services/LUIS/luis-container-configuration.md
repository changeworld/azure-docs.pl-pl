---
title: Ustawienia kontenera Docker — LUIS
titleSuffix: Azure Cognitive Services
description: Środowisko uruchomieniowe kontenera usługi LUIS jest konfigurowana przy użyciu `docker run` argumenty polecenia. Usługa LUIS ma wymagane ustawienia, wraz z kilku ustawień opcjonalnych.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 06/11/2019
ms.author: dapine
ms.openlocfilehash: e6a13688bba1c3a0e62e427e078e78c8f8dd4e70
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68560620"
---
# <a name="configure-language-understanding-docker-containers"></a>Konfigurowanie Language Understanding kontenerów platformy Docker 

Środowisko uruchomieniowe kontenera **Language Understanding** (Luis) jest konfigurowane przy użyciu `docker run` argumentów polecenia. Usługa LUIS ma wymagane ustawienia, wraz z kilku ustawień opcjonalnych. Kilka [przykłady](#example-docker-run-commands) polecenia są dostępne. Ustawienia specyficzne dla kontenera są dane wejściowe [ustawienia instalacji](#mount-settings) i ustawienia rozliczeń. 

## <a name="configuration-settings"></a>Ustawienia konfiguracji

Ten kontener ma następujące ustawienia konfiguracji:

|Wymagane|Ustawienie|Przeznaczenie|
|--|--|--|
|Yes|[ApiKey](#apikey-setting)|Używane do śledzenia informacji dotyczących rozliczeń.|
|Nie|[ApplicationInsights](#applicationinsights-setting)|Umożliwia dodanie [usługi Azure Application Insights](https://docs.microsoft.com/azure/application-insights) obsługi telemetrii do kontenera.|
|Yes|[Billing](#billing-setting)|Określa identyfikator URI punktu końcowego zasobu usługi na platformie Azure.|
|Yes|[Eula](#eula-setting)| Wskazuje, że zaakceptowano licencję dla kontenera.|
|Nie|[Fluentd](#fluentd-settings)|Zapisać dziennik i, opcjonalnie, metryki danych na serwerze Fluentd.|
|Nie|[Serwer proxy http](#http-proxy-credentials-settings)|Skonfiguruj serwer proxy HTTP do wykonywania żądań wychodzących.|
|Nie|[Logging](#logging-settings)|Udostępnia obsługę rejestrowania platformy ASP.NET Core dla kontenera. |
|Yes|[Mounts](#mount-settings)|Odczytywanie i zapisywanie danych z komputera hosta do kontenera i z kontenera do komputera hosta.|

> [!IMPORTANT]
> [ `ApiKey` ](#apikey-setting), [ `Billing` ](#billing-setting), I [ `Eula` ](#eula-setting) ustawienia są używane razem. Ponadto należy podać prawidłowe wartości dla wszystkich trzech ich; w przeciwnym razie Nie można uruchomić kontener. Aby uzyskać więcej informacji na temat tworzenia wystąpienia kontenera za pomocą tych ustawień konfiguracji, zobacz [rozliczeń](luis-container-howto.md#billing).

## <a name="apikey-setting"></a>Ustawienie ApiKey

`ApiKey` Ustawienie umożliwia określenie klucza zasobów platformy Azure używane do śledzenia informacji rozliczeniowych dla kontenera. Należy określić wartość ApiKey, a wartość musi być prawidłowym kluczem dla zasobu _Cognitive Services_ określonego dla [`Billing`](#billing-setting) ustawienia konfiguracji.

To ustawienie można znaleźć w następujących miejscach:

* Azure Portal: **Cognitive Services** Zarządzanie zasobami w obszarze **klucze**
* Portal LUIS: Strona **Ustawienia kluczy i punktów końcowych** . 

Nie należy używać klucza starter lub tworzenia klucza. 

## <a name="applicationinsights-setting"></a>Ustawienie dotycząca usługi Application Insights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-setting"></a>Ustawienie rozliczeń

Ustawienie określa identyfikator URI punktu końcowego zasobu Cognitive Services na platformie Azure używany do mierzenia informacji rozliczeniowych dla kontenera.  `Billing` Należy określić wartość tego ustawienia konfiguracji, a wartość musi być prawidłowym identyfikatorem URI punktu końcowego dla zasobu _Cognitive Services_ na platformie Azure. Kontener zgłasza użycie co 10 do 15 minut.

To ustawienie można znaleźć w następujących miejscach:

* Azure Portal: **Cognitive Services** Omówienie, etykieta`Endpoint`
* Portal LUIS: Strona **Ustawienia klucze i punkt końcowy** jako część identyfikatora URI punktu końcowego.

Pamiętaj o uwzględnieniu `luis/v2.0` trasy w adresie URL, jak pokazano w poniższej tabeli:


|Wymagane| Name (Nazwa) | Typ danych | Opis |
|--|------|-----------|-------------|
|Yes| `Billing` | Ciąg | Identyfikator URI punktu końcowego rozliczeń<br><br>Przykład:<br>`Billing=https://westus.api.cognitive.microsoft.com/luis/v2.0` |

## <a name="eula-setting"></a>Umowa licencyjna EULA ustawienie

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Ustawienia Fluentd

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Ustawienia poświadczeń serwera proxy http

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Ustawienia rejestrowania
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>Ustawienia instalacji

Użyj powiązania instaluje do odczytu i zapisu danych do i z kontenera. Można określić instalacji danych wejściowych lub wyjściowych instalacji, określając `--mount` opcji [platformy docker, uruchom](https://docs.docker.com/engine/reference/commandline/run/) polecenia. 

Kontener usługi LUIS nie korzysta z danych wejściowych lub danych wyjściowych instaluje do przechowywania szkolenie i usługi danych. 

Dokładna składnia lokalizację instalacji hosta różni się zależnie od systemu operacyjnego hosta. Ponadto [komputerze-hoście](luis-container-howto.md#the-host-computer)w lokalizacji instalacji może być niedostępna z powodu konfliktu między uprawnienia użyte dla konta usługi docker i hosta instalacji uprawnienia do lokalizacji. 

W poniższej tabeli opisano ustawienia obsługiwane.

|Wymagane| Name (Nazwa) | Typ danych | Opis |
|-------|------|-----------|-------------|
|Yes| `Input` | Ciąg | Miejsce docelowe instalacji danych wejściowych. Wartość domyślna to `/input`. To jest lokalizacja plików pakietu usługi LUIS. <br><br>Przykład:<br>`--mount type=bind,src=c:\input,target=/input`|
|Nie| `Output` | Ciąg | Miejsce docelowe instalacji danych wyjściowych. Wartość domyślna to `/output`. Jest to Lokalizacja dzienników. Obejmuje to usługi LUIS dzienniki zapytań i dzienniki kontenerów. <br><br>Przykład:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Przykład platformy docker, Uruchom polecenia

W poniższych przykładach używane ustawienia konfiguracji, aby zilustrować, jak pisać i użyj `docker run` poleceń.  Po uruchomieniu kontenera będzie działać do momentu [zatrzymać](luis-container-howto.md#stop-the-container) go.

* W tych przykładach można użyć katalogu `c:` poza dyskiem, aby uniknąć konfliktów uprawnień w systemie Windows. Jeśli musisz użyć określonego katalogu jako katalog wejściowy może być konieczne przyznanie platformy docker usługi uprawnienia. 
* Nie należy zmieniać kolejność argumentów, jeśli nie znasz bardzo kontenerów platformy docker.
* W przypadku korzystania z innego systemu operacyjnego należy użyć właściwej konsoli/terminalu, składni folderu dla instalacji i znaku kontynuacji wiersza dla systemu. W poniższych przykładach założono, że konsola systemu Windows `^`ma znak kontynuacji wiersza. Ponieważ kontener jest systemem operacyjnym Linux, instalacja docelowa używa składni folderu w stylu systemu Linux.

Pamiętaj o uwzględnieniu `luis/v2.0` trasy w adresie URL, jak pokazano w poniższej tabeli.

Zastąp {_argument_name_} własnymi wartościami:

| Symbol zastępczy | Wartość | Format lub przykład |
|-------------|-------|---|
|{API_KEY} | Klucz punktu końcowego uczonego aplikacji usługi LUIS. |xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx|
|{ENDPOINT_URL} | Wartość punktu końcowego rozliczenia jest dostępna na stronie `Cognitive Services` przegląd platformy Azure. |https://westus.api.cognitive.microsoft.com/luis/v2.0|

> [!IMPORTANT]
> `Eula`, `Billing`, I `ApiKey` opcje muszą być określone w celu uruchomienia kontenera; w przeciwnym razie nie uruchamia się kontener.  Aby uzyskać więcej informacji, zobacz [rozliczeń](luis-container-howto.md#billing).
> Wartość ApiKey jest **kluczem** ze strony klucze i punkty końcowe w portalu Luis i jest również dostępna na stronie klucze zasobów platformy Azure `Cognitive Services` . 

### <a name="basic-example"></a>Podstawowy przykład

Poniższy przykład zawiera najmniejszą liczbą argumentów, można uruchomić kontener:

```console
docker run --rm -it -p 5000:5000 --memory 4g --cpus 2 ^
--mount type=bind,src=c:\input,target=/input ^
--mount type=bind,src=c:\output,target=/output ^
mcr.microsoft.com/azure-cognitive-services/luis:latest ^
Eula=accept ^
Billing={ENDPOINT_URL} ^
ApiKey={API_KEY}
```

### <a name="applicationinsights-example"></a>Przykład dotycząca usługi Application Insights

W poniższym przykładzie ustawiono argument dotycząca usługi Application Insights do wysyłania telemetrii do usługi Application Insights, gdy kontener jest uruchomiony:

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

Następujące polecenie ustawia poziom rejestrowania `Logging:Console:LogLevel`, aby skonfigurować poziom rejestrowania, aby [ `Information` ](https://msdn.microsoft.com). 

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

## <a name="next-steps"></a>Kolejne kroki

* Przegląd [sposobu instalowania i uruchamiania kontenerów](luis-container-howto.md)
* Rozwiązywanie [](troubleshooting.md) problemów związanych z działaniem funkcji Luis można znaleźć w rozwiązaniu.
* Użyj więcej [kontenerów Cognitive Services](../cognitive-services-container-support.md)
