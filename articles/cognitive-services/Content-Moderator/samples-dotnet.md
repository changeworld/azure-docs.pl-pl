---
title: Przykłady kodu moderatora zawartości platformy Azure | Dokumentacja firmy Microsoft
description: Użyj moderatora zawartości w aplikacjach
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 01/10/2018
ms.author: sajagtap
ms.openlocfilehash: a7f5b0a7433631e303de47667871cc1354053c08
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347664"
---
# <a name="net-sdk-samples"></a>Przykłady zestawu .NET SDK

Poniższa lista zawiera łącza do przykładów kodu, utworzony przy użyciu zestawu SDK usługi Azure zawartości moderatora dla platformy .NET.

- **Biblioteka Pomocnika**: [utworzyć klienta moderatora zawartości do użycia w innych próbek](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ModeratorHelper/Clients.cs). Zobacz [szybkiego startu](content-moderator-helper-quickstart-dotnet.md).

## <a name="moderation"></a>Łagodzenie

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

## <a name="review"></a>Przegląd

- **Obraz zadania**: [uruchomić zadanie łagodzenia skanuje i tworzy przeglądami](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageJobs/Program.cs). Zobacz [szybkiego startu](moderation-jobs-quickstart-dotnet.md).
- **Obraz przeglądami**: [utworzyć przeglądami dla człowieka w pętli](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageReviews/Program.cs). Zobacz [szybkiego startu](moderation-reviews-quickstart-dotnet.md).
- **Przegląda wideo**: [wideo Utwórz przegląda dla człowieka w pętli](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoReviews/Program.cs). Zobacz [Szybki Start](video-reviews-quickstart-dotnet.md)
- **Przegląda wideo wykaz**: [wykaz wideo Utwórz przegląda ludzkich w pętli for](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoTranscriptReviews/Program.cs) zobacz [Szybki Start](video-reviews-quickstart-dotnet.md)

Zobacz wszystkie próbki .NET na [przykłady zawartości moderatora .NET w witrynie GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator).
