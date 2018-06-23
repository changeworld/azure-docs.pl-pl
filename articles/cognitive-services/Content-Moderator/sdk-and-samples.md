---
title: Zawartości łagodzenia zestawy SDK i próbki dla moderatora zawartości platformy Azure | Dokumentacja firmy Microsoft
description: Pobierz zestawy SDK i próbki dla moderatora zawartości
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 02/27/2018
ms.author: sajagtap
ms.openlocfilehash: 40b8fc0f63383e837f0813f876f20806e6e8c6eb
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347109"
---
# <a name="content-moderator-sdks-and-samples"></a>Zawartość moderatora zestawy SDK i przykłady

## <a name="sdks-for-python-java-nodejs-and-net"></a>Zestawy SDK dla języka Python, Java, Node.js i .NET

- [Moderatora zawartość zestawu SDK dla języka Python](https://pypi.python.org/pypi/azure-cognitiveservices-vision-contentmoderator)
- [Moderatora zawartość zestawu SDK dla języka Java](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-cognitiveservices-contentmoderator%22)
- [Moderatora zawartość zestawu SDK dla środowiska Node.js](https://www.npmjs.com/package/azure-cognitiveservices-contentmoderator)
- [Zawartości moderatora SDK dla platformy .NET](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/)

## <a name="net-sdk-samples"></a>Przykłady zestawu .NET SDK

Poniższa lista zawiera łącza do przykładów kodu, utworzony przy użyciu zestawu SDK usługi Azure zawartości moderatora dla platformy .NET.

- **Biblioteka Pomocnika**: [utworzyć klienta moderatora zawartości do użycia w innych próbek](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ModeratorHelper/Clients.cs). Zobacz [szybkiego startu](content-moderator-helper-quickstart-dotnet.md).

### <a name="moderation"></a>Łagodzenie 
- **Obraz łagodzenia**: [oceny obrazu zawartość dla dorosłych i luksusowych, tekst i kroje](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageModeration/Program.cs). Zobacz [szybkiego startu](image-moderation-quickstart-dotnet.md).
- **Niestandardowe obrazy**: [średnią z listy obrazów niestandardowych](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageListManagement/Program.cs). Zobacz [szybkiego startu](image-lists-quickstart-dotnet.md).

> [!NOTE]
> Istnieje limit maksymalny wynoszący **5 obrazu list** z każdej listy **przekracza 10 000 obrazów**.
>

- **Tekst łagodzenia**: [ekranu tekst niestosownych wyrażeń i informacje umożliwiające identyfikację (dane osobowe)](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/TextModeration/Program.cs). Zobacz [szybkiego startu](text-moderation-quickstart-dotnet.md).
- **Niestandardowe warunki**: [umiarkowane przy użyciu list niestandardowych termin](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/TermListManagement/Program.cs). Zobacz [szybkiego startu](term-lists-quickstart-dotnet.md).

> [!NOTE]
> Istnieje limit maksymalny wynoszący **wymieniono 5 termin** z każdej listy **przekracza 10 000 warunki**.
>

- **Łagodzenie wideo**: [skanowania wideo na zawartość dla dorosłych i luksusowych i uzyskiwać wyniki z](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoModeration/Program.cs). Zobacz [szybkiego startu](video-moderation-api.md).

### <a name="review"></a>Przegląd
- **Obraz zadania**: [uruchomić zadanie łagodzenia skanuje i tworzy przeglądami](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageJobs/Program.cs). Zobacz [szybkiego startu](moderation-jobs-quickstart-dotnet.md).
- **Obraz przeglądami**: [utworzyć przeglądami dla człowieka w pętli](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageReviews/Program.cs). Zobacz [szybkiego startu](moderation-reviews-quickstart-dotnet.md).
- **Przegląda wideo**: [wideo Utwórz przegląda dla człowieka w pętli](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoReviews/Program.cs). Zobacz [Szybki Start](video-reviews-quickstart-dotnet.md)
- **Przegląda wideo wykaz**: [wykaz wideo Utwórz przegląda ludzkich w pętli for](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoTranscriptReviews/Program.cs) zobacz [Szybki Start](video-reviews-quickstart-dotnet.md)

Zobacz wszystkie próbki .NET na [przykłady zawartości moderatora .NET w witrynie GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator).

## <a name="rest-api-samples-in-c"></a>Przykłady interfejsu API REST w języku C#

- [Moderowanie obrazów](https://github.com/MicrosoftContentModerator/ContentModerator-API-Samples/tree/master/ImageModeration)
- [Moderowanie tekstu](https://github.com/MicrosoftContentModerator/ContentModerator-API-Samples/tree/master/TextModeration)
- [Moderowanie filmów wideo](https://github.com/MicrosoftContentModerator/ContentModerator-API-Samples/tree/master/VideoModeration)
- [Przeglądy obrazów](https://github.com/MicrosoftContentModerator/ContentModerator-API-Samples/tree/master/ImageReviews)
- [Zadania obrazu](https://github.com/MicrosoftContentModerator/ContentModerator-API-Samples/tree/master/ImageJob)

Aby uzyskać wskazówki dotyczące tych przykładów, zapoznaj się z [webinar na żądanie](https://info.microsoft.com/cognitive-services-content-moderator-ondemand.html).

## <a name="tutorials"></a>Samouczki
- [łagodzenie katalogu handlu elektronicznego](https://github.com/MicrosoftContentModerator/samples-eCommerceCatalogModeration). Zobacz [samouczek](ecommerce-retail-catalog-moderation.md).
- [Łagodzenie zawartości Facebook](https://github.com/MicrosoftContentModerator/samples-fbPageModeration). Zobacz [samouczek](facebook-post-moderation.md).
- [Wideo i zapis rozwiązania łagodzenia i przejrzyj](https://github.com/MicrosoftContentModerator/VideoReviewConsoleApp) zobacz [samouczka](video-transcript-moderation-review-tutorial-dotnet.md)

## <a name="on-demand-webinars"></a>Seminaria internetowe na żądanie
- [Asystowane maszyny łagodzenia zawartości na dużą skalę z moderatora zawartości](https://info.microsoft.com/cognitive-services-content-moderator-ondemand.html)
