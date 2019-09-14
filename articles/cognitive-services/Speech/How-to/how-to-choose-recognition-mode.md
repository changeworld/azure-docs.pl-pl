---
title: Jak wybrać tryb rozpoznawania rozpoznawanie mowy Bing | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Jak wybrać tryb najlepszego rozpoznawania w rozpoznawanie mowy Bing.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 1860acb571c837a5eb4c75be69a96d1b22682118
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/13/2019
ms.locfileid: "70965670"
---
# <a name="bing-speech-recognition-modes"></a>rozpoznawanie mowy Bing tryby rozpoznawania

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Interfejsy API zamiana mowy na tekst Bing obsługują wiele trybów rozpoznawania mowy. Wybierz tryb, który daje najlepsze wyniki rozpoznawania dla aplikacji.

| Tryb | Opis |
|---|---|
| *interactive* | Rozpoznawanie "polecenie i kontrola" dla interaktywnych scenariuszy aplikacji użytkownika. Użytkownicy czytają krótkie frazy zamierzone jako polecenia do aplikacji. |
| *Dyktowanie* | Ciągłe rozpoznawanie scenariuszy dyktowania. Użytkownicy czytają dłuższe zdania, które są wyświetlane jako tekst. Użytkownicy przyjmują bardziej formalny styl mówiący. |
| *konwersacji* | Ciągłe rozpoznawanie konwersacji jego przepisywania między ludźmi. Użytkownicy przyjmują mniej formalny styl mówiący i mogą być zamienne z dłuższych zdań i krótszych fraz.

> [!NOTE]
> Te tryby są stosowane w przypadku używania [interfejsów API REST](../GetStarted/GetStartedREST.md). [Biblioteki klienckie](../GetStarted/GetStartedClientLibraries.md) używają różnych parametrów do określenia trybu rozpoznawania. Aby uzyskać więcej informacji, zapoznaj się z dowolnie wybraną biblioteką klienta.

Aby uzyskać więcej informacji, zobacz stronę [trybów rozpoznawania](../concepts.md#recognition-modes) .
