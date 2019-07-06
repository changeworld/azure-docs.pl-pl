---
title: Przykłady kodu — Content Moderator, .NET
description: Korzystaj z usługi Content Moderator w swoich aplikacjach platformy .NET za pomocą zestawu SDK.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: sample
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 0857d571e3bb029f564299efbe8cc4fdf5fbb3ff
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2019
ms.locfileid: "67604107"
---
# <a name="content-moderator-net-sdk-samples"></a>Przykłady zestawu SDK platformy .NET dla usługi Content Moderator

Poniższa lista zawiera hiperlinki do przykładów kodu utworzonych przy użyciu zestawu SDK Azure Content Moderator dla platformy .NET.

## <a name="moderation"></a>Moderowanie

- **Moderowanie obrazów**: [ocena obrazu pod kątem zawartości erotycznej i przeznaczonej dla osób dorosłych, tekstu i twarzy](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageModeration/Program.cs). Zobacz [Szybki start](image-moderation-quickstart-dotnet.md).
- **Obrazy niestandardowe**: [moderowanie przy użyciu list obrazów niestandardowych](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageListManagement/Program.cs). Zobacz [Szybki start](image-lists-quickstart-dotnet.md).

> [!NOTE]
> Istnieje maksymalny limit wynoszący **5 list obrazów**, a poszczególne listy **nie mogą przekraczać 10 000 obrazów**.
>

- **Moderowanie tekstu**: [Ekran tekst wulgaryzmów i danych osobowych](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/TextModeration/Program.cs). Zobacz [Szybki start](text-moderation-quickstart-dotnet.md).
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
