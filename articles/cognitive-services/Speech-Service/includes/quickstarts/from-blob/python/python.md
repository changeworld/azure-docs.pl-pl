---
title: 'Szybki Start: Rozpoznawanie mowy przechowywanej w usłudze C# BLOB Storage, usługa mowy'
titleSuffix: Azure Cognitive Services
description: do ustalenia
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/28/2019
ms.author: erhopf
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 2f06d0015bd80b37407df28045d4ced4a128e47e
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75466591"
---
## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że:

> [!div class="checklist"]
> * [Tworzenie zasobu usługi Azure Speech](../../../../get-started.md)
> * [Przekazywanie pliku źródłowego do obiektu blob platformy Azure](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)
> * [Konfigurowanie środowiska deweloperskiego](../../../../quickstarts/setup-platform.md)
> * [Tworzenie pustego przykładowego projektu](../../../../quickstarts/create-project.md)

## <a name="download-and-install-the-api-client-library"></a>Pobieranie i Instalowanie biblioteki klienta interfejsu API

Aby wykonać przykład potrzebny do wygenerowania biblioteki języka Python dla interfejsu API REST, który jest generowany przez strukturę [Swagger](https://swagger.io).

Wykonaj następujące kroki instalacji:

1. Przejdź do pozycji https://editor.swagger.io (Plik > Nowy > Inny).
1. Kliknij pozycję **plik**, a następnie kliknij pozycję **Importuj adres URL**.
1. Wprowadź adres URL programu Swagger, w tym Region subskrypcji usługi mowy: `https://<your-region>.cris.ai/docs/v2.0/swagger`.
1. Kliknij przycisk **Generuj klienta** i wybierz język **Python**.
1. Zapisz bibliotekę kliencką.
1. Wyodrębnij pobrane Python-Client-generated. zip w systemie plików.
1. Instalowanie wyodrębnionego modułu Python-Client w środowisku języka Python przy użyciu narzędzia PIP: `pip install path/to/package/python-client`.
1. Zainstalowany pakiet ma nazwę `swagger_client`. Możesz sprawdzić, czy instalacja działała przy użyciu polecenia `python -c "import swagger_client"`.

> **Uwaga:** Ze względu na [znaną usterkę w autogeneracji struktury Swagger](https://github.com/swagger-api/swagger-codegen/issues/7541)mogą wystąpić błędy podczas importowania pakietu `swagger_client`.
> Można je usunąć, usuwając wiersz z zawartością.
> ```py
> from swagger_client.models.model import Model  # noqa: F401,E501
> ```
> z pliku `swagger_client/models/model.py` i wiersza z zawartością
> ```py
> from swagger_client.models.inner_error import InnerError  # noqa: F401,E501
> ```
> z pliku `swagger_client/models/inner_error.py` wewnątrz zainstalowanego pakietu. Zostanie wyświetlony komunikat o błędzie z informacją o tym, gdzie znajdują się te pliki na potrzeby instalacji.

## <a name="install-other-dependencies"></a>Instalowanie innych zależności

Przykład używa biblioteki `requests`. Można go zainstalować za pomocą polecenia

```bash
pip install requests
```

## <a name="start-with-some-boilerplate-code"></a>Zacznij od pewnego kodu standardowego

Dodajmy kod, który działa jako szkielet dla projektu.

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=1-2,7-34,115-119)]
(Należy zastąpić wartości `YourSubscriptionKey`, `YourServiceRegion`i `YourFileUrl` własnymi wartościami).

## <a name="create-and-configure-an-http-client"></a>Tworzenie i Konfigurowanie klienta http
Najpierw musimy być klientem http z prawidłowym podstawowym adresem URL i zestawem uwierzytelniania.
Wstaw ten kod w `transcribe` [!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=37-45)]

## <a name="generate-a-transcription-request"></a>Generuj żądanie transkrypcji
Następnie wygenerujemy żądanie transkrypcji. Dodaj ten kod do `transcribe` [!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=52-54)]

## <a name="send-the-request-and-check-its-status"></a>Wyślij żądanie i sprawdź jego stan
Teraz wyślemy żądanie do usługi mowy i Sprawdzamy początkowy kod odpowiedzi. Ten kod odpowiedzi będzie po prostu wskazywać, czy usługa odebrała żądanie. Usługa zwróci adres URL w nagłówkach odpowiedzi, które są lokalizacją przechowywania stanu transkrypcji.
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=65-73)]

## <a name="wait-for-the-transcription-to-complete"></a>Poczekaj na zakończenie transkrypcji
Ponieważ usługa przetwarza proces transkrypcji asynchronicznie, musimy wykonać sondowanie pod kątem jego stanu co do tego często. Sprawdzimy co 5 sekund.

Wyliczmy wszystkie transkrypcje, które są przetwarzane przez ten zasób usługi mowy, i poszukamy utworzonego przez nas.

Oto kod sondowania z wyświetlaniem stanu dla wszystkiego, z wyjątkiem pomyślnego zakończenia, zajmiemy się tym dalej.
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=75-94,99-112)]

## <a name="display-the-transcription-results"></a>Wyświetlanie wyników transkrypcji
Gdy usługa pomyślnie ukończy transkrypcję, wyniki będą przechowywane w innym adresie URL, który można uzyskać z odpowiedzi na stan.

W tym miejscu zostanie pobrany kod JSON wyniku i zostanie on wyświetlony.
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=95-98)]

## <a name="check-your-code"></a>Sprawdź swój kod
W tym momencie kod powinien wyglądać następująco: (dodaliśmy Komentarze do tej wersji) [!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=1-118)]

## <a name="build-and-run-your-app"></a>Kompilowanie i uruchamianie aplikacji

Teraz wszystko jest gotowe do skompilowania aplikacji i przetestowania rozpoznawania mowy przy użyciu usługi mowy.

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [footer](./footer.md)]
