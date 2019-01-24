---
title: Przykłady kodu — Content Moderator, .NET
description: Korzystaj z usługi Content Moderator w swoich aplikacjach platformy .NET za pomocą zestawu SDK.
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: sample
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 6911b9b718edfc84a7c219be448dee06f4a0f835
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2019
ms.locfileid: "54259194"
---
# <a name="content-moderator-net-sdk-samples"></a>Przykłady zestawu SDK platformy .NET dla usługi Content Moderator

Poniższa lista zawiera hiperlinki do przykładów kodu utworzonych przy użyciu zestawu SDK Azure Content Moderator dla platformy .NET.

- **Biblioteka pomocnika**: [tworzenie klienta usługi Content Moderator do użytku w innych przykładach](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ModeratorHelper/Clients.cs). Zobacz [Szybki start](content-moderator-helper-quickstart-dotnet.md).

## <a name="moderation"></a>Moderowanie

- **Moderowanie obrazów**: [ocena obrazu pod kątem zawartości erotycznej i przeznaczonej dla osób dorosłych, tekstu i twarzy](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageModeration/Program.cs). Zobacz [Szybki start](image-moderation-quickstart-dotnet.md).
- **Obrazy niestandardowe**: [moderowanie przy użyciu list obrazów niestandardowych](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageListManagement/Program.cs). Zobacz [Szybki start](image-lists-quickstart-dotnet.md).

> [!NOTE]
> Istnieje maksymalny limit wynoszący **5 list obrazów**, a poszczególne listy **nie mogą przekraczać 10 000 obrazów**.
>

- **Moderowanie tekstu**: [sprawdzanie tekstu w celu wykrycia wulgaryzmów i danych osobowych](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/TextModeration/Program.cs). Zobacz [Szybki start](text-moderation-quickstart-dotnet.md).
- **Terminy niestandardowe**: [moderowanie przy użyciu list terminów niestandardowych](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/TermListManagement/Program.cs). Zobacz [Szybki start](term-lists-quickstart-dotnet.md).

> [!NOTE]
> Istnieje maksymalny limit wynoszący **5 list terminów**, a poszczególne listy **nie mogą przekraczać 10 000 terminów**.
>

- **Moderowanie filmów wideo**: [skanowanie wideo w celu wykrycia zawartości erotycznej i dla dorosłych oraz uzyskiwanie wyników](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoModeration/Program.cs). Zobacz [Szybki start](video-moderation-api.md).

## <a name="review"></a>Przegląd

- **Zadania obrazów**: [uruchamianie zadania moderowania, które skanuje, a następnie tworzy przeglądy](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageJobs/Program.cs). Zobacz [Szybki start](moderation-jobs-quickstart-dotnet.md).
- **Przeglądy obrazów**: [tworzenie przeglądów wymagających udziału człowieka](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageReviews/Program.cs). Zobacz [Szybki start](moderation-reviews-quickstart-dotnet.md).
- **Przeglądy wideo**: [tworzenie przeglądów wideo wymagających interakcji z człowiekiem](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoReviews/Program.cs). Zobacz [Szybki start](video-reviews-quickstart-dotnet.md)
- **Przeglądy transkrypcji wideo**: [tworzenie przeglądów transkrypcji wideo wymagających interakcji z człowiekiem](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoTranscriptReviews/Program.cs) Zobacz [Szybki Start](video-reviews-quickstart-dotnet.md)

Zobacz wszystkie przykłady dla platformy .NET wśród [przykładów użycia usług Content Moderator na platformie .NET w witrynie GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator).
