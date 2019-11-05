---
title: Konfigurowanie kontenerów — tłumaczenie tekstu w usłudze Translator
titleSuffix: Azure Cognitive Services
description: Tłumaczenie tekstu w usłudze Translator udostępnia kontenery ze wspólnym środowiskiem konfiguracji, dzięki czemu można łatwo konfigurować magazyn, rejestrowanie i dane telemetryczne oraz ustawienia zabezpieczeń dla kontenerów oraz zarządzać nimi.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: dapine
ms.openlocfilehash: 3dbedcb5e5212091dc8906defa2b1cf5c7868d60
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73507672"
---
# <a name="configure-translator-text-docker-containers"></a>Konfigurowanie tłumaczenie tekstu w usłudze Translator kontenerów platformy Docker

Tłumaczenie tekstu w usłudze Translator zapewnia każdy kontener ze wspólną strukturą konfiguracji, dzięki czemu można łatwo konfigurować magazyn, rejestrowanie i dane telemetryczne oraz ustawienia zabezpieczeń dla kontenerów oraz zarządzać nimi.

## <a name="configuration-settings"></a>Ustawienia konfiguracji

Kontener ma następujące ustawienia konfiguracji:

|Wymagany|Ustawienie|Przeznaczenie|
|--|--|--|
|Nie|[ApplicationInsights](#applicationinsights-setting)|Umożliwia dodawanie obsługi telemetrii [usługi Azure Application Insights](https://docs.microsoft.com/azure/application-insights) do kontenera.|
|Tak|[Umowy](#eula-setting)| Wskazuje, że licencja dla kontenera została zaakceptowana.|
|Nie|[Pozostało](#fluentd-settings)|Zapisuje dziennik i, opcjonalnie, dane metryki na serwerze z systemem.|
|Nie|Serwer proxy HTTP|Konfiguruje serwer proxy HTTP do wykonywania żądań wychodzących.|
|Nie|[Rejestrować](#logging-settings)|Zapewnia obsługę rejestrowania ASP.NET Core dla kontenera. |
|Nie|[Instaluje](#mount-settings)|Odczytuje i zapisuje dane z komputera hosta do kontenera oraz z kontenera z powrotem do komputera hosta.|

> [!IMPORTANT]
> Ustawienie [`Eula`](#eula-setting) musi być dostarczone z wartością `accept`; w przeciwnym razie kontener nie zostanie uruchomiony.

## <a name="applicationinsights-setting"></a>Ustawienie ApplicationInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

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

Kontenery tłumaczenie tekstu w usłudze Translator nie używają instalacji danych wejściowych lub wyjściowych do przechowywania danych szkoleniowych lub usług. 

Dokładna składnia lokalizacji instalacji hosta jest różna w zależności od systemu operacyjnego hosta. Ponadto lokalizacja instalacji [komputera hosta](how-to-install-containers.md#the-host-computer)może być niedostępna z powodu konfliktu między uprawnieniami używanymi przez konto usługi platformy Docker i uprawnieniami lokalizacji instalacji hosta. 

|Optional (Opcjonalność)| Nazwa | Typ danych | Opis |
|-------|------|-----------|-------------|
|Niedozwolone| `Input` | Ciąg | Kontenery tłumaczenie tekstu w usłudze Translator nie używają tego.|
|Optional (Opcjonalność)| `Output` | Ciąg | Obiekt docelowy instalacji wyjściowej. Wartość domyślna to `/output`. Jest to lokalizacja dzienników. Dotyczy to również dzienników kontenerów. <br><br>Przykład:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Przykładowe polecenia uruchamiania platformy Docker 

W poniższych przykładach użyto ustawień konfiguracji, aby zilustrować sposób pisania i używania poleceń `docker run`.  Po uruchomieniu kontenera kontynuuje działanie, dopóki nie zostanie [zatrzymane](how-to-install-containers.md#stop-the-container) .

* **Znak kontynuacji wiersza**: polecenia platformy Docker w poniższych sekcjach używają ukośnika odwrotnego, `\`jako znaku kontynuacji wiersza. Zastąp lub usuń to w zależności od wymagań systemu operacyjnego hosta. 
* **Kolejność**argumentów: nie zmieniaj kolejności argumentów, o ile nie znasz już kontenerów platformy Docker.

## <a name="translator-text-container-docker-examples"></a>Przykłady tłumaczenie tekstu w usłudze Translator Docker kontenera

Poniższe przykłady platformy Docker dotyczą kontenera tłumaczenie tekstu w usłudze Translator.

### <a name="basic-example"></a>Przykład podstawowy

```
docker run --rm -it -p 5000:80 --memory 4g --cpus 4 \
containerpreview.azurecr.io/microsoft/cognitive-services-translator-text \
Eula=accept
```

### <a name="logging-example"></a>Przykład rejestrowania

```
docker run --rm -it -p 5000:80 --memory 4g --cpus 4 \
containerpreview.azurecr.io/microsoft/cognitive-services-translator-text \
Eula=accept \
Logging:Console:LogLevel:Default=Information
```

## <a name="next-steps"></a>Następne kroki

* Zapoznaj [się z tematem Instalowanie i uruchamianie kontenerów](how-to-install-containers.md)
* Użyj więcej [kontenerów Cognitive Services](../cognitive-services-container-support.md)
