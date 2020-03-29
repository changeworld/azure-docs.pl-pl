---
title: Konfigurowanie kontenerów mowy
titleSuffix: Azure Cognitive Services
description: Usługa mowy udostępnia każdemu kontenerowi wspólną strukturę konfiguracji, dzięki czemu można łatwo skonfigurować i zarządzać pamięcią masową, rejestrowaniem i telemetrią oraz ustawieniami zabezpieczeń kontenerów.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/09/2020
ms.author: dapine
ms.openlocfilehash: dd5a531e4a979cba9c2a766c7774762a0427ad02
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79037326"
---
# <a name="configure-speech-service-containers"></a>Konfigurowanie kontenerów usługi mowy

Kontenery mowy umożliwiają klientom tworzenie architektury aplikacji mowy, która jest zoptymalizowana pod kątem korzystania z niezawodnych funkcji chmury i lokalizacji brzegowej. Cztery kontenery mowy, które teraz obsługujemy, to **zamiana mowy na tekst,** **zamiana mowy na tekst,** **zamiana tekstu na mowę**oraz **zamiana tekstu na niestandardowych.**

Środowisko wykonawcze kontenera **mowy** jest `docker run` konfigurowane przy użyciu argumentów polecenia. Ten kontener ma kilka wymaganych ustawień, wraz z kilkoma ustawieniami opcjonalnymi. Dostępnych jest kilka [przykładów](#example-docker-run-commands) polecenia. Ustawienia specyficzne dla kontenera to ustawienia rozliczeń.

## <a name="configuration-settings"></a>Ustawienia konfiguracji

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> Ustawienia [`ApiKey`](#apikey-configuration-setting) [`Billing`](#billing-configuration-setting)i [`Eula`](#eula-setting) ustawienia są używane razem i należy podać prawidłowe wartości dla wszystkich trzech z nich; w przeciwnym razie kontener nie zostanie uruchomiony. Aby uzyskać więcej informacji na temat używania tych ustawień konfiguracji do tworzenia wystąpienia kontenera, zobacz [Rozliczenia](speech-container-howto.md#billing).

## <a name="apikey-configuration-setting"></a>Ustawienie konfiguracji apikey

Ustawienie `ApiKey` określa klucz zasobów platformy Azure używany do śledzenia informacji rozliczeniowych dla kontenera. Należy określić wartość dla apikey i wartość musi być _Speech_ prawidłowy klucz [`Billing`](#billing-configuration-setting) dla zasobu mowy określonego dla ustawienia konfiguracji.

To ustawienie można znaleźć w następującym miejscu:

- Portal Azure: Zarządzanie zasobami **mowy** w obszarze **Klucze**

## <a name="applicationinsights-setting"></a>Ustawienie ApplicationInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Ustawienie konfiguracji rozliczeń

Ustawienie `Billing` określa identyfikator URI punktu końcowego zasobu _mowy_ na platformie Azure używane do pomiaru informacji rozliczeniowych dla kontenera. Należy określić wartość dla tego ustawienia konfiguracji, a wartość musi być prawidłowym identyfikatorem URI punktu końcowego dla zasobu _mowy_ na platformie Azure. Kontener raportuje użycie co 10 do 15 minut.

To ustawienie można znaleźć w następującym miejscu:

- Portal Azure: **Omówienie mowy,** oznaczone etykietą`Endpoint`

| Wymagany | Nazwa | Typ danych | Opis |
| -------- | ---- | --------- | ----------- |
| Tak | `Billing` | Ciąg | Identyfikator URI punktu końcowego rozliczeń. Aby uzyskać więcej informacji na temat uzyskiwania identyfikatora URI rozliczeń, zobacz [zbieranie wymaganych parametrów](speech-container-howto.md#gathering-required-parameters). Aby uzyskać więcej informacji i pełną listę regionalnych punktów końcowych, zobacz [Niestandardowe nazwy poddomen dla usług Cognitive Services](../cognitive-services-custom-subdomains.md). |

## <a name="eula-setting"></a>Ustawienie Eula

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Płynne ustawienia

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Ustawienia poświadczeń serwera proxy HTTP

[!INCLUDE [Container shared HTTP proxy settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Ustawienia logowania

[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>Ustawienia instalacji

Użyj powiązania wierzchowce do odczytu i zapisu danych do i z kontenera. Można określić mocowanie wejściowe lub `--mount` mocowanie wyjściowe, określając opcję w poleceniu [uruchamiania platformy docker.](https://docs.docker.com/engine/reference/commandline/run/)

Kontenery mowy standardowego nie używają instalacji wejściowych ani wyjściowych do przechowywania danych szkoleniowych lub usługowych. Jednak kontenery mowy niestandardowe są zależne od instalacji woluminów.

Dokładna składnia lokalizacji instalacji hosta różni się w zależności od systemu operacyjnego hosta. Ponadto lokalizacja instalacji [komputera-hosta](speech-container-howto.md#the-host-computer)może być niedostępna z powodu konfliktu między uprawnieniami używanymi przez konto usługi platformy docker a uprawnieniami lokalizacji instalacji hosta.

| Optional (Opcjonalność) | Nazwa | Typ danych | Opis |
| -------- | ---- | --------- | ----------- |
| Niedozwolone | `Input` | Ciąg | Standardowe pojemniki mowy nie używają tego. Niestandardowe kontenery mowy używają [instalacji głośności](#volume-mount-settings).                                                                                    |
| Optional (Opcjonalność) | `Output` | Ciąg | Miejsce docelowe mocowania wyjściowego. Wartością domyślną jest `/output`. Jest to lokalizacja dzienników. Obejmuje to dzienniki kontenerów. <br><br>Przykład:<br>`--mount type=bind,src=c:\output,target=/output` |

## <a name="volume-mount-settings"></a>Ustawienia montażu głośności

Kontenery mowy niestandardowe używać [instalowania woluminu](https://docs.docker.com/storage/volumes/) do utrwalania modeli niestandardowych. Można określić instalację woluminu, dodając `-v` opcję (lub) `--volume`do polecenia uruchom [docker.](https://docs.docker.com/engine/reference/commandline/run/)

Modele niestandardowe są pobierane po raz pierwszy, że nowy model jest pozyskiwania jako część polecenia uruchamiania dokowanego kontenera mowy niestandardowe. Kolejne przebiegi tego samego `ModelId` dla niestandardowego kontenera mowy będą używać wcześniej pobranego modelu. Jeśli nie podano instalacji woluminu, nie można utrwalić modeli niestandardowych.

Ustawienie mocowania woluminu `:` składa się z trzech pól oddzielonych kolorami:

1. Pierwsze pole to nazwa woluminu na komputerze-hoście, na przykład _C:\input_.
2. Drugie pole to katalog w kontenerze, na przykład _/usr/local/models_.
3. Trzecie pole (opcjonalnie) jest oddzieloną przecinkami listą opcji, aby uzyskać więcej informacji, zobacz [użyj woluminów](https://docs.docker.com/storage/volumes/).

### <a name="volume-mount-example"></a>Przykład montażu woluminu

```bash
-v C:\input:/usr/local/models
```

To polecenie umożliwia zainstalowanie katalogu _C:\input_ komputera hosta w katalogu kontenerów _/usr/local/models._

> [!IMPORTANT]
> Ustawienia instalacji woluminu mają zastosowanie tylko **do kontenerów Zamiana mowy na tekst** i **niestandardowego tekstu na mowę.** Standardowe **kontenery Zamiana mowy na tekst** i **zamiana tekstu na mowę** nie używają instalacji woluminów.

## <a name="example-docker-run-commands"></a>Przykładowe polecenia uruchamiania platformy dok.

Poniższe przykłady używają ustawień konfiguracji, aby zilustrować sposób pisania i używania `docker run` poleceń. Po uruchomieniu kontener będzie nadal działał, dopóki go nie [zatrzymasz.](speech-container-howto.md#stop-the-container)

- **Znak kontynuacji wiersza:** Polecenia platformy Docker w poniższych `\`sekcjach używają ukośnika wstecznego, jako znaku kontynuacji wiersza. Zastąp lub usuń to na podstawie wymagań systemu operacyjnego hosta.
- **Kolejność argumentów:** Nie należy zmieniać kolejności argumentów, chyba że znasz kontenery platformy Docker.

Zamień {_argument_name_} własnymi wartościami:

| Symbol zastępczy | Wartość | Format lub przykład |
| ----------- | ----- | ----------------- |
| **{API_KEY}** | Klucz punktu końcowego `Speech` zasobu `Speech` na stronie Klucze platformy Azure.   | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`                                                                                  |
| **{ENDPOINT_URI}** | Wartość punktu końcowego rozliczeń jest `Speech` dostępna na stronie Przegląd platformy Azure. | Zobacz [zbieranie wymaganych parametrów](speech-container-howto.md#gathering-required-parameters) dla jawnych przykładów. |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> Aby `Eula` `Billing`uruchomić `ApiKey` kontener, należy określić opcje i opcje; w przeciwnym razie kontener nie zostanie uruchomiony. Aby uzyskać więcej informacji, zobacz [Rozliczenia](#billing-configuration-setting).
> Wartość ApiKey jest **kluczem** ze strony kluczy zasobów mowy platformy Azure.

## <a name="speech-container-docker-examples"></a>Przykłady platformy Docker kontenera mowy

Poniższe przykłady platformy Docker są dla kontenera mowy.

## <a name="speech-to-text"></a>[Zamiana mowy na tekst](#tab/stt)

### <a name="basic-example-for-speech-to-text"></a>Podstawowy przykład zamiany mowy na tekst

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="logging-example-for-speech-to-text"></a>Rejestrowanie przykładu zamiany mowy na tekst

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
```

## <a name="custom-speech-to-text"></a>[Niestandardowa zamiana mowy na tekst](#tab/cstt)

### <a name="basic-example-for-custom-speech-to-text"></a>Podstawowy przykład niestandardowej zamiany mowy na tekst

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
-v {VOLUME_MOUNT}:/usr/local/models \
containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="logging-example-for-custom-speech-to-text"></a>Rejestrowanie przykładu niestandardowego zamiany mowy na tekst

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

## <a name="text-to-speech"></a>[Zamiana tekstu na mowę](#tab/tss)

### <a name="basic-example-for-text-to-speech"></a>Podstawowy przykład zamiany tekstu na mowę

```Docker
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="logging-example-for-text-to-speech"></a>Rejestrowanie przykładu zamiany tekstu na mowę

```Docker
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
```

## <a name="custom-text-to-speech"></a>[Niestandardowy tekst na mowę](#tab/ctts)

### <a name="basic-example-for-custom-text-to-speech"></a>Podstawowy przykład niestandardowego zamiany tekstu na mowę

```Docker
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
-v {VOLUME_MOUNT}:/usr/local/models \
containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="logging-example-for-custom-text-to-speech"></a>Rejestrowanie przykładu niestandardowego zamiany tekstu na mowę

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

---

## <a name="next-steps"></a>Następne kroki

- Sprawdź, [jak zainstalować i uruchomić kontenery](speech-container-howto.md)
