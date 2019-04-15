---
title: Zestawy SDK usługi Azure Media Services 3 — Azure
description: Ten artykuł zawiera omówienie sposobu uruchamiania, tworzenie aplikacji za pomocą usługi Media Services v3 API za pomocą zestawów SDK.
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
ms.date: 04/11/2019
ms.author: juliako
ms.custom: ''
ms.openlocfilehash: 9fb4d1561a661387f759aada9e776d43a95aa5c7
ms.sourcegitcommit: b8a8d29fdf199158d96736fbbb0c3773502a092d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2019
ms.locfileid: "59564513"
---
# <a name="develop-against-media-services-v3-api-using-sdks"></a>Tworzenie interfejsu API w wersji 3 usługa Media Services za pomocą zestawów SDK

Jako deweloper możesz użyć [interfejsu API REST](https://aka.ms/ams-v3-rest-ref) usługi Media Services lub bibliotek klienckich, które umożliwiają interakcję z interfejsem API REST, aby łatwo tworzyć i utrzymywać niestandardowe przepływy pracy multimediów oraz zarządzać nimi. Interfejs API usługi [Media Services v3](https://aka.ms/ams-v3-rest-sdk) opiera się na specyfikacji interfejsu OpenAPI (wcześniej znanej jako struktura Swagger).

> [!NOTE]
> Zestawy SDK usługi Azure Media Services v3 nie musi być metodą o bezpiecznych wątkach. Podczas tworzenia aplikacji wielowątkowych, należy dodać własną logiką synchronizacji wątków do ochrony klienta lub użyć nowego obiektu AzureMediaServicesClient na wątek. Ponadto należy ostrożnie wielowątkowości problemy wynikające z opcjonalne obiekty udostępniany przez kod (na przykład wystąpienie klasy HttpClient w programie .NET).

Ten temat zawiera łącza do zestawów SDK, narzędzia, przewodniki z instrukcjami.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć opracowywanie pod kątem usługi Media Services, potrzebne są:

- Aktywna subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Dowiedz się więcej o podstawowych pojęciach](concepts-overview.md)
- Przegląd [opracowywanie zawartości przy użyciu nośnika usług interfejsów API w wersji 3](media-services-apis-overview.md)
- [Tworzenie konta usługi Media Services — interfejs wiersza polecenia](create-account-cli-how-to.md)

## <a name="start-developing-with-sdks"></a>Rozpoczynanie programowania z użyciem zestawów SDK

### <a name="net"></a>.NET

Użyj [zestawu .NET SDK](https://aka.ms/ams-v3-dotnet-sdk) do [nawiązać połączenie z usługi Media Services](configure-connect-dotnet-howto.md).

Eksploruj usługę Media Services [dokumentacja platformy .NET](https://aka.ms/ams-v3-dotnet-ref) dokumentacji.

### <a name="java"></a>Java

Użyj [zestawu Java SDK](https://aka.ms/ams-v3-java-sdk) do [nawiązać połączenie z usługi Media Services](configure-connect-java-howto.md).

Przegląd usługi Media Services [dokumentacja języka Java](https://aka.ms/ams-v3-java-ref) dokumentacji.

### <a name="nodejs"></a>Node.js

Użyj [zestawu SDK środowiska Node.js](https://aka.ms/ams-v3-nodejs-sdk) do [nawiązać połączenie z usługi Media Services](configure-connect-nodejs-howto.md).

Eksploruj usługę Media Services [dokumentacja środowiska Node.js](https://aka.ms/ams-v3-nodejs-ref) dokumentacji i sprawdź [przykłady](https://github.com/Azure-Samples/media-services-v3-node-tutorials) które pokazują sposób użycia interfejsu API usługi Media Services przy użyciu środowiska node.js.

### <a name="python"></a>Python

Użyj [Python SDK](https://aka.ms/ams-v3-python-sdk).

Przegląd usługi Media Services [dokumentacja języka Python](https://aka.ms/ams-v3-python-ref) dokumentacji.

### <a name="go"></a>Przejdź

Użyj [Przejdź SDK](https://aka.ms/ams-v3-go-sdk).

Przegląd usługi Media Services [Przejdź odwołanie](https://aka.ms/ams-v3-go-ref) dokumentacji.

### <a name="ruby"></a>Ruby

Użyj [zestaw Ruby SDK](https://aka.ms/ams-v3-ruby-sdk).

## <a name="azure-media-services-explorer"></a>Azure Media Services Explorer

[Azure Media Services Explorer](https://github.com/Azure/Azure-Media-Services-Explorer) to narzędzie dostępne dla użytkowników systemu Windows, którzy chcą dowiedzieć się więcej o usłudze Media Services. Narzędzie AMSE to aplikacja Winforms/C# obsługująca przekazywanie, pobieranie, kodowanie oraz przesyłanie strumieniowe wideo na żądanie i na żywo zawartości za pomocą usługi Media Services. Narzędzie AMSE jest przeznaczone dla klientów, którzy chcą przetestować usługę Media Services bez konieczności pisania jakiegokolwiek kodu. Kod AMSE jest dostarczany jako zasób dla klientów, którzy chcą tworzyć aplikacje za pomocą usługi Media Services.

Narzędzie AMSE to projekt typu Open Source, w przypadku którego pomoc techniczna jest świadczona przez społeczność (problemy można zgłaszać pod adresem https://github.com/Azure/Azure-Media-Services-Explorer/issues). W tym projekcie przyjęto [Kodeks postępowania oprogramowania Open Source firmy Microsoft](https://opensource.microsoft.com/codeofconduct/). Aby uzyskać więcej informacji, zobacz [Code of Conduct FAQ](https://opensource.microsoft.com/codeofconduct/faq/) (Często zadawane pytania dotyczące kodeksu postępowania) lub wyślij wiadomość e-mail na adres opencode@microsoft.com w przypadku jakichkolwiek dodatkowych pytań lub komentarzy.

## <a name="next-steps"></a>Kolejne kroki

[Omówienie](media-services-overview.md)
