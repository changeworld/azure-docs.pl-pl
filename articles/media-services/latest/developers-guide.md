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
ms.date: 04/11/2019
ms.author: juliako
ms.custom: ''
ms.openlocfilehash: d97682de6c8eb7dd36e25cbd4b8d66d328745748
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2019
ms.locfileid: "59543640"
---
# <a name="start-developing-with-media-services-v3-api-using-sdkstools"></a>Rozpoczęcie programowania przy użyciu interfejsu API usługi Media Services 3 za pomocą zestawów SDK i narzędzi

Jako deweloper możesz użyć [interfejsu API REST](https://aka.ms/ams-v3-rest-ref) usługi Media Services lub bibliotek klienckich, które umożliwiają interakcję z interfejsem API REST, aby łatwo tworzyć i utrzymywać niestandardowe przepływy pracy multimediów oraz zarządzać nimi. Interfejs API usługi [Media Services v3](https://aka.ms/ams-v3-rest-sdk) opiera się na specyfikacji interfejsu OpenAPI (wcześniej znanej jako struktura Swagger).

Przed rozpoczęciem tworzenia, przejrzyj [opracowywanie zawartości przy użyciu usługi Media Services v3 API](media-services-apis-overview.md).

> [!NOTE]
> Zestawy SDK usługi Azure Media Services v3 nie musi być metodą o bezpiecznych wątkach. Podczas tworzenia aplikacji wielowątkowych, należy dodać własną logiką synchronizacji wątków do ochrony klienta lub użyć nowego obiektu AzureMediaServicesClient na wątek. Ponadto należy ostrożnie wielowątkowości problemy wynikające z opcjonalne obiekty udostępniany przez kod (na przykład wystąpienie klasy HttpClient w programie .NET).

Ten temat zawiera łącza do zestawów SDK, narzędzia, zobacz inne.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć opracowywanie pod kątem usługi Media Services, potrzebne są:

- Aktywna subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Dowiedz się więcej o podstawowych pojęciach](concepts-overview.md)
- [Tworzenie konta usługi Media Services — interfejs wiersza polecenia](create-account-cli-how-to.md)

## <a name="start-developing-with-sdks"></a>Rozpoczynanie programowania z użyciem zestawów SDK

### <a name="net"></a>.NET

Użyj [zestawu .NET SDK](https://aka.ms/ams-v3-dotnet-sdk).

Eksploruj usługę Media Services [.NET ref](https://aka.ms/ams-v3-dotnet-ref) dokumentacji.

### <a name="java"></a>Java

Użyj [Java SDK](https://aka.ms/ams-v3-java-sdk).

Przejrzyj [dokumentację dla języka Java](https://aka.ms/ams-v3-java-ref) usługi Media Services.

### <a name="nodejs"></a>Node.js

Użyj [zestawu SDK środowiska Node.js](https://aka.ms/ams-v3-nodejs-sdk).

Przeanalizuj [dokumentację oprogramowania Node.js](https://aka.ms/ams-v3-nodejs-ref) usługi Media Services i zapoznaj się z [przykładami](https://github.com/Azure-Samples/media-services-v3-node-tutorials) pokazującymi, jak używać interfejsu API usługi Media Services przy użyciu środowiska node.js.

### <a name="python"></a>Python

Użyj [Python SDK](https://aka.ms/ams-v3-python-sdk).

Przejrzyj [dokumentację dla języka Python](https://aka.ms/ams-v3-python-ref) usługi Media Services.

### <a name="go"></a>Przejdź

Użyj [Przejdź SDK](https://aka.ms/ams-v3-go-sdk).

Przejrzyj [dokumentację dla języka Go](https://aka.ms/ams-v3-go-ref) usługi Media Services.

### <a name="ruby"></a>Ruby

Użyj [zestaw Ruby SDK](https://aka.ms/ams-v3-ruby-sdk).

## <a name="azure-media-services-explorer"></a>Azure Media Services Explorer

[Azure Media Services Explorer](https://github.com/Azure/Azure-Media-Services-Explorer) to narzędzie dostępne dla użytkowników systemu Windows, którzy chcą dowiedzieć się więcej o usłudze Media Services. Narzędzie AMSE to aplikacja Winforms/C# obsługująca przekazywanie, pobieranie, kodowanie oraz przesyłanie strumieniowe wideo na żądanie i na żywo zawartości za pomocą usługi Media Services. Narzędzie AMSE jest przeznaczone dla klientów, którzy chcą przetestować usługę Media Services bez konieczności pisania jakiegokolwiek kodu. Kod AMSE jest dostarczany jako zasób dla klientów, którzy chcą tworzyć aplikacje za pomocą usługi Media Services.

Narzędzie AMSE to projekt typu Open Source, w przypadku którego pomoc techniczna jest świadczona przez społeczność (problemy można zgłaszać pod adresem https://github.com/Azure/Azure-Media-Services-Explorer/issues). W tym projekcie przyjęto [Kodeks postępowania oprogramowania Open Source firmy Microsoft](https://opensource.microsoft.com/codeofconduct/). Aby uzyskać więcej informacji, zobacz [Code of Conduct FAQ](https://opensource.microsoft.com/codeofconduct/faq/) (Często zadawane pytania dotyczące kodeksu postępowania) lub wyślij wiadomość e-mail na adres opencode@microsoft.com w przypadku jakichkolwiek dodatkowych pytań lub komentarzy.

## <a name="next-steps"></a>Kolejne kroki

[Omówienie](media-services-overview.md)
