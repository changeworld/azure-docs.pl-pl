---
title: Wybieranie trybu rozpoznawania | Dokumentacja firmy Microsoft
description: Jak wybrać najlepsze trybu rozpoznawania.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/15/2017
ms.author: zhouwang
ms.openlocfilehash: 4f02b683dde16b537ae5554e6435c068f0fcb808
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347400"
---
# <a name="speech-recognition-modes"></a>Tryby rozpoznawania mowy

Firmy Microsoft *mowy na tekst* interfejsów API obsługi wielu rodzajów rozpoznawania mowy. Wybierz tryb, który daje najlepsze wyniki rozpoznawania dla aplikacji.

| Tryb | Opis |
|---|---|
| *interakcyjne* | Rozpoznawanie "Command and control" dla scenariuszy aplikacji użytkownika interaktywnego. Użytkownicy mówią same zwroty pełnić rolę polecenia do aplikacji. |
| *dyktowania* | Ciągłe rozpoznawania dyktowania scenariuszach. Użytkownicy mówią same dłużej zdania, które są wyświetlane jako tekst. Użytkownicy przyjąć bardziej formalnych wymowy. |
| *Konwersacja* | Ciągłe rozpoznawania do konwersacji między ludzi przepisywania. Użytkownicy przyjmuje mniej formalny wymowy i mogą przełączać się między dłużej zdań i krótszy wyrażeń.

> [!NOTE]
> Te tryby są stosowane, gdy używasz [interfejsów API REST](../GetStarted/GetStartedREST.md). [Bibliotek klienckich](../GetStarted/GetStartedClientLibraries.md) użyć innych parametrów, aby określić tryb rozpoznawania. Aby uzyskać więcej informacji zobacz Biblioteka klienta wybranych przez użytkownika.

Aby uzyskać więcej informacji, zobacz [tryby rozpoznawania](../concepts.md#recognition-modes) strony.
