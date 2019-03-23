---
title: Zestawy SDK usługi Azure Media Services 3 — Azure
description: Ten artykuł przedstawia omówienie sposobu rozpoczęcia opracowywania za pomocą interfejsu API usługi Media Services 3 przy użyciu zestawów SDK i narzędzi.
services: media-services
documentationcenter: na
author: Juliako
manager: femila
editor: ''
tags: ''
keywords: ''
ms.service: media-services
ms.devlang: multiple
ms.topic: overview
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 03/20/2019
ms.author: juliako
ms.custom: ''
ms.openlocfilehash: cda6c9cd1f9c8b9305349f0904aeb744ba373711
ms.sourcegitcommit: 87bd7bf35c469f84d6ca6599ac3f5ea5545159c9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2019
ms.locfileid: "58350253"
---
# <a name="start-developing-with-media-services-v3-api-using-sdkstools"></a>Rozpoczęcie programowania przy użyciu interfejsu API usługi Media Services 3 za pomocą zestawów SDK i narzędzi

Jako deweloper możesz użyć [interfejsu API REST](https://aka.ms/ams-v3-rest-ref) usługi Media Services lub bibliotek klienckich, które umożliwiają interakcję z interfejsem API REST, aby łatwo tworzyć i utrzymywać niestandardowe przepływy pracy multimediów oraz zarządzać nimi. Interfejs API usługi [Media Services v3](https://aka.ms/ams-v3-rest-sdk) opiera się na specyfikacji interfejsu OpenAPI (wcześniej znanej jako struktura Swagger).

Ten temat zawiera linki do zestawów SDK, narzędzi i dokumentacji. Zawiera także pewne przydatne informacje dla różnych środowisk programistycznych.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć opracowywanie pod kątem usługi Media Services, potrzebne są:

- Aktywna subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Dowiedz się więcej o podstawowych pojęciach](concepts-overview.md)
- [Konto usługi Media Services](create-account-cli-how-to.md)

## <a name="start-developing"></a>Rozpoczynanie programowania

Usługa Azure Media Services obsługuje następujące zestawy SDK i narzędzia 

- [Interfejs wiersza polecenia platformy Azure](https://aka.ms/ams-v3-cli) 
- [Zestaw SDK platformy .NET](https://aka.ms/ams-v3-dotnet-sdk)
- [Zestaw SDK Java](https://aka.ms/ams-v3-java-sdk)
- [Zestaw SDK dla platformy Node.js](https://aka.ms/ams-v3-nodejs-sdk)
- [Zestaw SDK dla języka Python](https://aka.ms/ams-v3-python-sdk)
- [Zestaw SDK dla języka Go](https://aka.ms/ams-v3-go-sdk)
- [Zestaw SDK dla języka Ruby](https://aka.ms/ams-v3-ruby-sdk)

### <a name="azure-media-services-explorer"></a>Azure Media Services Explorer

[Azure Media Services Explorer](https://github.com/Azure/Azure-Media-Services-Explorer) to narzędzie dostępne dla użytkowników systemu Windows, którzy chcą dowiedzieć się więcej o usłudze Media Services. Narzędzie AMSE to aplikacja Winforms/C# obsługująca przekazywanie, pobieranie, kodowanie oraz przesyłanie strumieniowe wideo na żądanie i na żywo zawartości za pomocą usługi Media Services. Narzędzie AMSE jest przeznaczone dla klientów, którzy chcą przetestować usługę Media Services bez konieczności pisania jakiegokolwiek kodu. Kod AMSE jest dostarczany jako zasób dla klientów, którzy chcą tworzyć aplikacje za pomocą usługi Media Services.

Narzędzie AMSE to projekt typu Open Source, w przypadku którego pomoc techniczna jest świadczona przez społeczność (problemy można zgłaszać pod adresem https://github.com/Azure/Azure-Media-Services-Explorer/issues). W tym projekcie przyjęto [Kodeks postępowania oprogramowania Open Source firmy Microsoft](https://opensource.microsoft.com/codeofconduct/). Aby uzyskać więcej informacji, zobacz [Code of Conduct FAQ](https://opensource.microsoft.com/codeofconduct/faq/) (Często zadawane pytania dotyczące kodeksu postępowania) lub wyślij wiadomość e-mail na adres opencode@microsoft.com w przypadku jakichkolwiek dodatkowych pytań lub komentarzy.

## <a name="rest"></a>REST

Aby rozpocząć opracowywanie za pomocą interfejsów API REST usługi Media Services, zainstaluj klienta REST. Na przykład program **Postman**, program **Visual Studio Code** z wtyczką REST lub narzędzie **Telerik Fiddler**. Na potrzeby przykładów usługi Media Services 3 używamy programu [Postman](media-rest-apis-with-postman.md).

Przeanalizuj [dokumentację interfejsu API REST](https://aka.ms/ams-v3-rest-ref) usługi Media Services i zapoznaj się z następującymi przykładami:

- [Samouczek: Kodowanie pliku zdalnego na podstawie adresu URL i przesyłanie strumieniowe wideo — REST](stream-files-tutorial-with-rest.md)
- [Przekazywanie plików na konto usługi Media Services — REST](upload-files-rest-how-to.md)
- [Tworzenie filtrów za pomocą usługi Media Services — REST](filters-dynamic-manifest-rest-howto.md)
- [Interfejs API REST bazujący na usłudze Azure Resource Manager](https://github.com/Azure-Samples/media-services-v3-arm-templates)

<!-- ## CLI -->
[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

Przeanalizuj [dokumentację interfejsu wiersza polecenia platformy Azure](https://aka.ms/ams-v3-cli-ref) usługi Media Services i zapoznaj się z następującymi przykładami:

- [Skalowanie jednostek zarezerwowanych multimediów — interfejs wiersza polecenia](media-reserved-units-cli-how-to.md)
- [Tworzenie konta usługi Media Services — interfejs wiersza polecenia](./scripts/cli-create-account.md) 
- [Resetowanie poświadczeń konta — interfejs wiersza polecenia](./scripts/cli-reset-account-credentials.md)
- [Tworzenie zasobów — interfejs wiersza polecenia](./scripts/cli-create-asset.md)
- [Przekazywanie pliku — interfejs wiersza polecenia](./scripts/cli-upload-file-asset.md)
- [Tworzenie transformacji — interfejs wiersza polecenia](./scripts/cli-create-transform.md)
- [Tworzenie zadań — interfejs wiersza polecenia](./scripts/cli-create-jobs.md)
- [Tworzenie usługi EventGrid — interfejs wiersza polecenia](./scripts/cli-create-event-grid.md)
- [Publikowanie elementu zawartości — interfejs wiersza polecenia](./scripts/cli-publish-asset.md)
- [Filtr — interfejs wiersza polecenia](filters-dynamic-manifest-cli-howto.md)

## <a name="net"></a>.NET

Przeanalizuj [dokumentację struktury .NET](https://aka.ms/ams-v3-dotnet-ref) usługi Media Services i zapoznaj się z następującymi przykładami:

- [Samouczek: Przekazywanie, kodowanie i przesyłanie strumieniowe klipów wideo — .NET](stream-files-tutorial-with-api.md) 
- [Samouczek: Transmisja strumieniowa na żywo z usługi Media Services 3 — .NET](stream-live-tutorial-with-api.md)
- [Samouczek: Analizowanie wideo za pomocą usługi Media Services 3 — .NET](analyze-videos-tutorial-with-api.md)
- [Tworzenie danych wejściowych zadania z pliku lokalnego — .NET](job-input-from-local-file-how-to.md)
- [Tworzenie danych wejściowych zadania na podstawie adresu URL protokołu HTTPS — .NET](job-input-from-http-how-to.md)
- [Kodowanie za pomocą transformacji niestandardowej — .NET](customize-encoder-presets-how-to.md)
- [Używanie dynamicznego szyfrowania AES-128 i usługi dostarczania kluczy — .NET](protect-with-aes128.md)
- [Używanie usługi dostarczania licencji i szyfrowania dynamicznego w technologii DRM — .NET](protect-with-drm.md)
- [Pobieranie klucza podpisywania z istniejących zasad — .NET](get-content-key-policy-dotnet-howto.md)
- [Tworzenie filtrów za pomocą usługi Media Services — .NET](filters-dynamic-manifest-dotnet-howto.md)
- [Zaawansowane przykłady wideo na żądanie usługi Azure Functions v2 z usługą Media Services v3](https://aka.ms/ams3functions)

## <a name="java"></a>Java

Przejrzyj [dokumentację dla języka Java](https://aka.ms/ams-v3-java-ref) usługi Media Services.

## <a name="nodejs"></a>Node.js

Przeanalizuj [dokumentację oprogramowania Node.js](https://aka.ms/ams-v3-nodejs-ref) usługi Media Services i zapoznaj się z [przykładami](https://github.com/Azure-Samples/media-services-v3-node-tutorials) pokazującymi, jak używać interfejsu API usługi Media Services przy użyciu środowiska node.js.

## <a name="python"></a>Python

Przejrzyj [dokumentację dla języka Python](https://aka.ms/ams-v3-python-ref) usługi Media Services.

## <a name="go"></a>Przejdź

Przejrzyj [dokumentację dla języka Go](https://aka.ms/ams-v3-go-ref) usługi Media Services.

## <a name="next-steps"></a>Kolejne kroki

- [Tworzenie konta — interfejs wiersza polecenia](create-account-cli-how-to.md)
- [Dostęp do interfejsów API — interfejs wiersza polecenia](access-api-cli-how-to.md)

