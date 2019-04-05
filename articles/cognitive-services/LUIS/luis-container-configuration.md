---
title: Ustawienia kontenera platformy docker
titleSuffix: Language Understanding - Azure Cognitive Services
description: Środowisko uruchomieniowe kontenera usługi LUIS jest konfigurowana przy użyciu `docker run` argumenty polecenia. Usługa LUIS ma wymagane ustawienia, wraz z kilku ustawień opcjonalnych.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: diberry
ms.openlocfilehash: e93a81f2c081daa58a37b1e2823d7bf0cc5a6361
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2019
ms.locfileid: "58883117"
---
# <a name="configure-language-understanding-docker-containers"></a>Konfigurowanie kontenerów platformy Docker interpretacji języka 

**Language Understanding** środowisko uruchomieniowe kontenera (LUIS) jest skonfigurowany przy użyciu `docker run` argumenty polecenia. Usługa LUIS ma wymagane ustawienia, wraz z kilku ustawień opcjonalnych. Kilka [przykłady](#example-docker-run-commands) polecenia są dostępne. Ustawienia specyficzne dla kontenera są dane wejściowe [ustawienia instalacji](#mount-settings) i ustawienia rozliczeń. 

## <a name="configuration-settings"></a>Ustawienia konfiguracji

Ten kontener ma następujące ustawienia konfiguracji:

|Wymagane|Ustawienie|Przeznaczenie|
|--|--|--|
|Yes|[ApiKey](#apikey-setting)|Używane do śledzenia informacji dotyczących rozliczeń.|
|Nie|[ApplicationInsights](#applicationinsights-setting)|Umożliwia dodanie [usługi Azure Application Insights](https://docs.microsoft.com/azure/application-insights) obsługi telemetrii do kontenera.|
|Yes|[Rozliczenia](#billing-setting)|Określa identyfikator URI punktu końcowego zasobu usługi na platformie Azure.|
|Yes|[Eula](#eula-setting)| Wskazuje, że zaakceptowano licencję dla kontenera.|
|Nie|[Fluentd](#fluentd-settings)|Zapisać dziennik i, opcjonalnie, metryki danych na serwerze Fluentd.|
|Nie|[Serwer Proxy http](#http-proxy-credentials-settings)|Skonfiguruj serwer proxy HTTP dla żądania wychodzącego.|
|Nie|[Rejestrowanie](#logging-settings)|Udostępnia obsługę rejestrowania platformy ASP.NET Core dla kontenera. |
|Yes|[Instaluje](#mount-settings)|Odczytywanie i zapisywanie danych z komputera hosta do kontenera i z kontenera do komputera hosta.|

> [!IMPORTANT]
> [ `ApiKey` ](#apikey-setting), [ `Billing` ](#billing-setting), I [ `Eula` ](#eula-setting) ustawienia są używane razem. Ponadto należy podać prawidłowe wartości dla wszystkich trzech ich; w przeciwnym razie Nie można uruchomić kontener. Aby uzyskać więcej informacji na temat tworzenia wystąpienia kontenera za pomocą tych ustawień konfiguracji, zobacz [rozliczeń](luis-container-howto.md#billing).

## <a name="apikey-setting"></a>Ustawienie ApiKey

`ApiKey` Ustawienie umożliwia określenie klucza zasobów platformy Azure używane do śledzenia informacji rozliczeniowych dla kontenera. Należy określić wartość dla ApiKey i wartość musi być prawidłowy klucz dla _Language Understanding_ zasób określony dla [ `Billing` ](#billing-setting) ustawienia konfiguracji.

To ustawienie można znaleźć w następujących miejscach:

* Azure Portal: **Language Understanding** zarządzanie zasobami w obszarze **kluczy**
* Portal usługi LUIS: **Ustawienia punktu końcowego i klucze** strony. 

Nie należy używać klucza starter lub tworzenia klucza. 

## <a name="applicationinsights-setting"></a>Ustawienie dotycząca usługi Application Insights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-setting"></a>Ustawienie rozliczeń

`Billing` Ustawienie określa identyfikator URI punktu końcowego z _Language Understanding_ zasobów na platformie Azure używane do pomiarów informacji rozliczeniowych dla kontenera. Należy określić wartość dla tego ustawienia konfiguracji, a wartość musi być prawidłowy identyfikator URI punktu końcowego dla _Language Understanding_ zasobów na platformie Azure. Kontener raportów użycia dotyczących co 10 do 15 minut.

To ustawienie można znaleźć w następujących miejscach:

* Azure Portal: **Language Understanding** Przegląd, etykietą `Endpoint`
* Portal usługi LUIS: **Ustawienia punktu końcowego i klucze** stronie jako część identyfikatora URI punktu końcowego.

|Wymagany| Name (Nazwa) | Typ danych | Opis |
|--|------|-----------|-------------|
|Yes| `Billing` | Ciąg | Identyfikator URI punktu końcowego rozliczeń<br><br>Przykład:<br>`Billing=https://westus.api.cognitive.microsoft.com/luis/v2.0` |

## <a name="eula-setting"></a>Umowa licencyjna EULA ustawienie

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Ustawienia Fluentd


[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Ustawienia poświadczeń serwera proxy HTTP

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


* **Znak kontynuacji wiersza**: Platformy docker w poniższych sekcjach użyto ukośnika, `\`, jako znak kontynuacji wiersza. Zamień lub Usuń ten na podstawie wymagań systemu operacyjnego hosta. 
* **Kolejność argumentów**: Nie należy zmieniać kolejność argumentów, jeśli nie znasz bardzo kontenerów platformy docker.

Zastąp {_argument_name_} własnymi wartościami:

| Symbol zastępczy | Wartość | Format lub przykład |
|-------------|-------|---|
|{ENDPOINT_KEY} | Klucz punktu końcowego uczonego aplikacji usługi LUIS. |xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx|
|{BILLING_ENDPOINT} | Rozliczeń wartości punktu końcowego jest dostępna na stronie Przegląd interpretacji języka w witrynie Azure portal.|https://westus.api.cognitive.microsoft.com/luis/v2.0|

> [!IMPORTANT]
> `Eula`, `Billing`, I `ApiKey` opcje muszą być określone w celu uruchomienia kontenera; w przeciwnym razie nie uruchamia się kontener.  Aby uzyskać więcej informacji, zobacz [rozliczeń](luis-container-howto.md#billing).
> Wartość ApiKey **klucz** z kluczy i punktów końcowych strony w portalu usługi LUIS, a także jest dostępny na stronie klucze zasobów interpretacji języka platformy Azure. 

### <a name="basic-example"></a>Podstawowy przykład

Poniższy przykład zawiera najmniejszą liczbą argumentów, można uruchomić kontener:

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 2 \
--mount type=bind,src=c:\input,target=/input \
--mount type=bind,src=c:\output,target=/output \
mcr.microsoft.com/azure-cognitive-services/luis:latest \
Eula=accept \
Billing={BILLING_ENDPOINT} \
ApiKey={ENDPOINT_KEY}
```

> [!Note] 
> Poprzednie polecenie korzysta z katalogu, wyłącz `c:` dysku, aby uniknąć konfliktów uprawnień na Windows. Jeśli musisz użyć określonego katalogu jako katalog wejściowy może być konieczne przyznanie platformy docker usługi uprawnienia. Poprzednie polecenie docker używa ukośnik, `\`, jako znak kontynuacji wiersza. Zamień lub Usuń na podstawie Twojej [komputerze-hoście](luis-container-howto.md#the-host-computer) wymagania dotyczące systemu operacyjnego. Nie należy zmieniać kolejność argumentów, jeśli nie znasz bardzo kontenerów platformy docker.


### <a name="applicationinsights-example"></a>Przykład dotycząca usługi Application Insights

W poniższym przykładzie ustawiono argument dotycząca usługi Application Insights do wysyłania telemetrii do usługi Application Insights, gdy kontener jest uruchomiony:

```bash
docker run --rm -it -p 5000:5000 --memory 6g --cpus 2 \
--mount type=bind,src=c:\input,target=/input \
--mount type=bind,src=c:\output,target=/output \
mcr.microsoft.com/azure-cognitive-services/luis:latest \
Eula=accept \
Billing={BILLING_ENDPOINT} \
ApiKey={ENDPOINT_KEY}
InstrumentationKey={INSTRUMENTATION_KEY}
```

### <a name="logging-example"></a>Przykład rejestrowania 

Następujące polecenie ustawia poziom rejestrowania `Logging:Console:LogLevel`, aby skonfigurować poziom rejestrowania, aby [ `Information` ](https://msdn.microsoft.com). 

```bash
docker run --rm -it -p 5000:5000 --memory 6g --cpus 2 \
--mount type=bind,src=c:\input,target=/input \
--mount type=bind,src=c:\output,target=/output \
mcr.microsoft.com/azure-cognitive-services/luis:latest \
Eula=accept \
Billing={BILLING_ENDPOINT} \
ApiKey={ENDPOINT_KEY} \
Logging:Console:LogLevel:Default=Information
```

## <a name="next-steps"></a>Kolejne kroki

* Przegląd [sposobu instalowania i uruchamiania kontenerów](luis-container-howto.md)
* Zapoznaj się [Rozwiązywanie problemów](troubleshooting.md) Aby rozwiązać problemy związane z działaniem usługi LUIS.
* Użycie [kontenerów usługi Cognitive Services](../cognitive-services-container-support.md)
