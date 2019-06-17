---
title: Omówienie usługi Azure Media Services | Microsoft Docs
description: Ten temat zawiera omówienie usługi Azure Media Services
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/19/2019
ms.author: juliako
ms.openlocfilehash: 7f0a071012685ee192d4f866396a5953d0a4508c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64937251"
---
# <a name="azure-media-services-overview"></a>Omówienie usługi Azure Media Services 

> [!div class="op_single_selector" title1="Wybierz wersję usługi Media Services, którego używasz:"]
> * [Wersja 3](../latest/media-services-overview.md)
> * [Wersja 2](media-services-overview.md)

> [!NOTE]
> Do usługi Media Services w wersji 2 nie są już dodawane żadne nowe funkcje. <br/>Zapoznaj się z najnowszą wersją, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Zobacz też [wskazówek dotyczących migracji od v2 do v3](../latest/migrate-from-v2-to-v3.md)

Microsoft Azure Media Services (AMS) to oparta na chmurze rozszerzalna platforma, która umożliwia deweloperom tworzenie skalowalnych aplikacji do dostarczania multimediów i zarządzania nimi. Usługi Media Services są oparte na interfejsach API REST, które umożliwiają bezpieczne przekazywanie, przechowywanie, kodowanie i tworzenie pakietów zawartości wideo lub audio na potrzeby transmisji strumieniowej na żądanie i na żywo do różnych klientów (np. odbiorników TV, komputerów i urządzeń przenośnych).

Korzystając wyłącznie z usługi Media Services, można tworzyć kompleksowe przepływy pracy. W przypadku niektórych części przepływu pracy można użyć składników innych firm. Na przykład kodowanie można wykonać przy użyciu kodera innego producenta. Natomiast przekazywanie, zabezpieczanie, tworzenie pakietów i dostarczanie można realizować za pomocą usługi Media Services. Możliwe jest przesyłanie strumieniowe zawartości na żywo lub dostarczanie zawartości na żądanie. 

## <a name="prerequisites"></a>Wymagania wstępne

Do rozpoczęcia korzystania z usługi Azure Media Services potrzebne są:

* Konto platformy Azure. Jeśli jej nie masz, możesz utworzyć bezpłatne konto próbne w zaledwie kilka minut. Aby uzyskać szczegółowe informacje, zobacz artykuł [Bezpłatna wersja próbna platformy Azure](https://azure.microsoft.com).
* Konto usługi Azure Media Services. Aby uzyskać więcej informacji, zobacz temat [Tworzenie konta](media-services-portal-create-account.md).
* (Opcjonalnie) Konfigurowanie środowiska deweloperskiego. Wybierz platformę .NET lub interfejs API REST dla środowiska deweloperskiego. Aby uzyskać więcej informacji, zobacz temat [Konfigurowanie środowiska](media-services-dotnet-how-to-use.md).

    Dowiedz się także, jak [programowo ustanawiać połączenie z interfejsem API usługi AMS](media-services-use-aad-auth-to-access-ams-api.md).
* Punkt końcowy przesyłania strumieniowego (standardowy lub Premium) w stanie uruchomionym.  Aby uzyskać więcej informacji, zobacz [Zarządzanie punktami końcowymi przesyłania strumieniowego](media-services-portal-manage-streaming-endpoints.md)

## <a name="sdks-and-tools"></a>Zestawy SDK i narzędzia

W celu tworzenia rozwiązań Media Services można użyć następujących elementów:

* [Interfejs API REST usługi Media Services](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference)
* Jeden z dostępnych zestawów SDK klienta:
    * Usługi Azure Media Services SDK dla platformy .NET
    
        * [Pakiet NuGet](https://www.nuget.org/packages/windowsazure.mediaservices/)
        * [Kod źródłowy w serwisie Github](https://github.com/Azure/azure-sdk-for-media-services)
    * [Zestaw Azure SDK dla języka Java](https://github.com/Azure/azure-sdk-for-java)
    * [Zestaw Azure PHP SDK](https://github.com/Azure/azure-sdk-for-php)
    * [Azure Media Services dla środowiska Node.js](https://github.com/michelle-becker/node-ams-sdk/blob/master/lib/request.js) (Jest to wersja zestawu Node.js SDK firmy innej niż Microsoft. Jest ona opracowywana przez społeczność i aktualnie nie obsługuje 100% interfejsów API usług AMS.)
* Istniejące narzędzia:
    * [Azure Portal](https://portal.azure.com/)
    * [Azure-Media-Services-Explorer](https://github.com/Azure/Azure-Media-Services-Explorer) (Azure Media Services Explorer (AMSE) to aplikacja Winforms/C# dla systemu Windows)

> [!NOTE]
> Aby uzyskać najnowszą wersję zestawu SDK języka Java i zacząć programować w języku Java, zobacz [Rozpoczynanie korzystania z zestawu SDK klienta Java dla usług Azure Media Services](https://docs.microsoft.com/azure/media-services/media-services-java-how-to-use). <br/>
> Aby pobrać najnowszy zestaw SDK języka PHP dla usługi Media Services, poszukaj wersji 0.5.7 pakietu Microsoft/WindowAzure w [repozytorium Packagist](https://packagist.org/packages/microsoft/windowsazure#v0.5.7).  

## <a name="code-samples"></a>Przykłady kodu

Wiele przykładów kodu możesz znaleźć w galerii **Przykłady kodu platformy Azure**: [Przykłady kodu usługi Azure Media Services](https://azure.microsoft.com/resources/samples/?service=media-services&sort=0).

## <a name="concepts"></a>Pojęcia

Pojęcia związane z usługą Azure Media Services zostały przedstawione w temacie [Pojęcia](media-services-concepts.md).

## <a name="supported-scenarios-and-availability-of-media-services-across-data-centers"></a>Obsługiwane scenariusze i dostępność usługi Media Services w centrach danych

Aby uzyskać szczegółowe informacje, zobacz temat [Scenariusze oraz dostępność funkcji i usług AMS w centrach danych](scenarios-and-availability.md).

## <a name="service-level-agreement-sla"></a>Umowa dotycząca poziomu usług (SLA)

Aby uzyskać więcej informacji, zobacz temat [Umowy dotyczące poziomu usług platformy Microsoft Azure](https://azure.microsoft.com/support/legal/sla/).

Aby uzyskać informacje na temat dostępności w centrach danych, zobacz sekcję [Dostępność](scenarios-and-availability.md#availability).

## <a name="support"></a>Pomoc techniczna

[Pomoc techniczna platformy Azure](https://azure.microsoft.com/support/options/) zapewnia opcje wsparcia technicznego dla platformy Azure, w tym dla usługi Media Services.

## <a name="provide-feedback"></a>Przekazywanie opinii

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
