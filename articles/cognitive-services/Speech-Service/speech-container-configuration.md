---
title: Konfigurowanie kontenerów mowy
titleSuffix: Azure Cognitive Services
description: Usługi mowy zapewniają każdy kontener ze wspólną strukturą konfiguracji, dzięki czemu można łatwo konfigurować magazyn, rejestrowanie i dane telemetryczne oraz ustawienia zabezpieczeń dla kontenerów.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: dapine
ms.openlocfilehash: 15739b735b67e29ed07521d31857f1b176447487
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73491055"
---
# <a name="configure-speech-service-containers"></a>Konfigurowanie kontenerów usługi mowy

Kontenery mowy umożliwiają klientom tworzenie jednej architektury aplikacji mowy, która jest zoptymalizowana pod kątem wykorzystania zarówno niezawodnej możliwości chmury, jak i lokalizacji brzegowej. Obecnie obsługiwane są cztery kontenery mowy, które obsługujemy funkcję **zamiany mowy na tekst**, **niestandardowe-Zamiana mowy na tekst**, zamiany **tekstu na mowę**i **niestandardową zamianę tekstu na mowę**.

Środowisko uruchomieniowe kontenera **mowy** jest konfigurowane przy użyciu argumentów polecenia `docker run`. Ten kontener ma kilka wymaganych ustawień oraz kilka opcjonalnych ustawień. Kilka [przykładów](#example-docker-run-commands) polecenia jest dostępnych. Ustawienia dotyczące rozliczeń dotyczą tylko kontenera. 

## <a name="configuration-settings"></a>Ustawienia konfiguracji

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> Ustawienia [`ApiKey`](#apikey-configuration-setting), [`Billing`](#billing-configuration-setting)i [`Eula`](#eula-setting) są używane razem i należy podać prawidłowe wartości dla wszystkich trzech z nich; w przeciwnym razie kontener nie zostanie uruchomiony. Aby uzyskać więcej informacji na temat tworzenia wystąpienia kontenera przy użyciu tych ustawień konfiguracji, zobacz [rozliczenia](speech-container-howto.md#billing).

## <a name="apikey-configuration-setting"></a>Ustawienie konfiguracji ApiKey

Ustawienie `ApiKey` określa klucz zasobów platformy Azure służący do śledzenia informacji rozliczeniowych dla kontenera. Należy określić wartość ApiKey, a wartość musi być prawidłowym kluczem dla zasobu _mowy_ określonego dla ustawienia konfiguracji [`Billing`](#billing-configuration-setting) .

To ustawienie można znaleźć w następujących miejscach:

* Azure Portal: zarządzanie zasobami **mowy** , w obszarze **klucze**

## <a name="applicationinsights-setting"></a>Ustawienie ApplicationInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Ustawienie konfiguracji rozliczeń

Ustawienie `Billing` określa identyfikator URI punktu końcowego zasobu _mowy_ na platformie Azure używany do pomiaru informacji rozliczeniowych dla kontenera. Należy określić wartość tego ustawienia konfiguracji, a wartość musi być prawidłowym identyfikatorem URI punktu końcowego dla zasobu _mowy_ na platformie Azure. Kontener zgłasza użycie co 10 do 15 minut.

To ustawienie można znaleźć w następujących miejscach:

* Azure Portal: Omówienie **mowy** , etykieta `Endpoint`

|Wymagany| Nazwa | Typ danych | Opis |
|--|------|-----------|-------------|
|Tak| `Billing` | Ciąg | Identyfikator URI punktu końcowego rozliczenia. Aby uzyskać więcej informacji na temat identyfikatora URI rozliczeń, zobacz [zbieranie wymaganych parametrów](speech-container-howto.md#gathering-required-parameters). |

## <a name="eula-setting"></a>Ustawienie umowy EULA

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Ustawienia pozostały

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Ustawienia poświadczeń serwera proxy HTTP

[!INCLUDE [Container shared HTTP proxy settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Ustawienia rejestrowania
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>Ustawienia instalacji

Użyj instalacji powiązań, aby odczytywać i zapisywać dane w kontenerze i z niego. Można określić instalację wejściową lub instalację wyjściową, określając opcję `--mount` w poleceniu [Docker Run](https://docs.docker.com/engine/reference/commandline/run/) .

Standardowe kontenery mowy nie używają instalacji danych wejściowych lub wyjściowych do przechowywania danych szkoleniowych lub usług. Jednak niestandardowe kontenery mowy polegają na instalacjach woluminów.

Dokładna składnia lokalizacji instalacji hosta różni się w zależności od systemu operacyjnego hosta. Ponadto lokalizacja instalacji [komputera hosta](speech-container-howto.md#the-host-computer)może być niedostępna z powodu konfliktu między uprawnieniami używanymi przez konto usługi platformy Docker i uprawnieniami lokalizacji instalacji hosta. 

|Optional (Opcjonalność)| Nazwa | Typ danych | Opis |
|-------|------|-----------|-------------|
|Niedozwolone| `Input` | Ciąg | Standardowe kontenery mowy nie używają tego programu. Niestandardowe kontenery mowy używają [instalacji woluminów](#volume-mount-settings). |
|Optional (Opcjonalność)| `Output` | Ciąg | Obiekt docelowy instalacji wyjściowej. Wartość domyślna to `/output`. Jest to lokalizacja dzienników. Dotyczy to również dzienników kontenerów. <br><br>Przykład:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="volume-mount-settings"></a>Ustawienia instalacji woluminu

Niestandardowe kontenery mowy używają [instalacji woluminów](https://docs.docker.com/storage/volumes/) do utrwalania modeli niestandardowych. Instalację woluminu można określić, dodając opcję `-v` (lub `--volume`) do polecenia [Docker Run](https://docs.docker.com/engine/reference/commandline/run/) .

Modele niestandardowe są pobierane po raz pierwszy, gdy nowy model jest pobierany w ramach polecenia modułu Docker niestandardowego kontenera mowy. Kolejne uruchomienia tego samego `ModelId` dla niestandardowego kontenera mowy będą korzystać z pobranego wcześniej modelu. Jeśli nie podano instalacji woluminu, nie można utrwalić modeli niestandardowych.

Ustawienie instalacji woluminu składa się z trzech `:` kolorowych pól rozdzielonych:

1. Pierwsze pole jest nazwą woluminu na komputerze-hoście, na przykład *C:\input*.
2. Drugie pole jest katalogiem w kontenerze, na przykład */usr/local/models*.
3. Trzecie pole (opcjonalnie) jest rozdzielaną przecinkami listą opcji, aby uzyskać więcej informacji, zobacz [Używanie woluminów](https://docs.docker.com/storage/volumes/).

### <a name="volume-mount-example"></a>Przykład instalacji woluminu

```bash
-v C:\input:/usr/local/models
```

To polecenie powoduje zainstalowanie katalogu *C:\input* komputera hosta w katalogu kontenerów */usr/local/models* .

> [!IMPORTANT]
> Ustawienia instalacji woluminu mają zastosowanie tylko **do kontenerów** **Custom Speech i do tekstu** . Standardowe kontenery **zamiany mowy na tekst** i **zamiany tekstu na mowę** nie używają instalacji woluminów.

## <a name="example-docker-run-commands"></a>Przykładowe polecenia uruchamiania platformy Docker 

W poniższych przykładach użyto ustawień konfiguracji, aby zilustrować sposób pisania i używania poleceń `docker run`.  Po uruchomieniu kontenera kontynuuje działanie, dopóki nie zostanie [zatrzymane](speech-container-howto.md#stop-the-container) .

* **Znak kontynuacji wiersza**: polecenia platformy Docker w poniższych sekcjach używają ukośnika odwrotnego, `\`jako znaku kontynuacji wiersza. Zastąp lub usuń to w zależności od wymagań systemu operacyjnego hosta. 
* **Kolejnooć**argumentów: nie zmieniaj kolejności argumentów, chyba że znasz kontenery Docker.

Zastąp {_argument_name_} własnymi wartościami:

| Symbol zastępczy | Wartość | Format lub przykład |
|-------------|-------|---|
| **{API_KEY}** | Klucz punktu końcowego zasobu `Speech` na stronie klucze `Speech` platformy Azure. | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` |
| **{ENDPOINT_URI}** | Wartość punktu końcowego rozliczenia jest dostępna na stronie Przegląd `Speech` platformy Azure.| Zobacz [zbieranie wymaganych parametrów](speech-container-howto.md#gathering-required-parameters) dla jawnych przykładów. |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> Aby można było uruchomić kontener, należy określić opcje `Eula`, `Billing`i `ApiKey`. w przeciwnym razie kontener nie zostanie uruchomiony.  Aby uzyskać więcej informacji, zobacz [rozliczenia](#billing-configuration-setting).
> Wartość ApiKey jest **kluczem** ze strony klucze zasobów usługi Azure Speech. 

## <a name="speech-container-docker-examples"></a>Przykłady platformy Docker kontenera mowy

Poniższe przykłady platformy Docker dotyczą kontenera mowy. 

# <a name="speech-to-texttabstt"></a>[Zamiana mowy na tekst](#tab/stt)

### <a name="basic-example-for-speech-to-text"></a>Podstawowy przykład zamiany mowy na tekst

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="logging-example-for-speech-to-text"></a>Przykład rejestrowania dla zamiany mowy na tekst

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
```

# <a name="custom-speech-to-texttabcstt"></a>[Custom Speech do tekstu](#tab/cstt)

### <a name="basic-example-for-custom-speech-to-text"></a>Podstawowy przykład dla Custom Speech do tekstu

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
-v {VOLUME_MOUNT}:/usr/local/models \
containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="logging-example-for-custom-speech-to-text"></a>Przykład rejestrowania dla Custom Speech do tekstu

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
-v {VOLUME_MOUNT}:/usr/local/models \
containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
```

# <a name="text-to-speechtabtss"></a>[Zamiana tekstu na mowę](#tab/tss)

### <a name="basic-example-for-text-to-speech"></a>Podstawowy przykład zamiany tekstu na mowę

```Docker
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="logging-example-for-text-to-speech"></a>Przykład rejestrowania dla zamiany tekstu na mowę

```Docker
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
```

# <a name="custom-text-to-speechtabctts"></a>[Niestandardowa Zamiana tekstu na mowę](#tab/ctts)

### <a name="basic-example-for-custom-text-to-speech"></a>Podstawowy przykład niestandardowego tekstu na mowę

```Docker
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
-v {VOLUME_MOUNT}:/usr/local/models \
containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="logging-example-for-custom-text-to-speech"></a>Przykład rejestrowania dla niestandardowego tekstu na mowę

```Docker
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
-v {VOLUME_MOUNT}:/usr/local/models \
containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
```

***

## <a name="next-steps"></a>Następne kroki

* Zapoznaj [się z tematem Instalowanie i uruchamianie kontenerów](speech-container-howto.md)
