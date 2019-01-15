---
title: Przejrzyj tekst w usłudze Azure Content Moderator — Content Moderator
description: Dowiedz się, jak przeglądać tekstu w pakiecie Content Moderator, aby wyświetlić jego wynik i wykryto tagów. Skorzystaj z informacji, aby ustalić, czy zawartość jest odpowiednia.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 005e6ed853cbc6013d74e7ff479097dcbb0a1043
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2019
ms.locfileid: "54259567"
---
# <a name="let-human-reviewers-review-text"></a>Umożliwianie recenzentom ludzi, Przejrzyj tekst

Można użyć usługi Azure Content Moderator do przeglądania tekstu przy użyciu wyniki, a wykryto tagów. Skorzystaj z informacji w celu ustalenia, czy zawartość jest odpowiedni. 

## <a name="select-or-enter-the-text-to-review"></a>Wybierz lub wprowadź tekst, aby zapoznać się z

W pakiecie Content Moderator, wybierz **spróbuj** kartę. Następnie wybierz **tekstu** opcję, aby przejść do ekranu startowego Moderowanie tekstu. Wpisz dowolny tekst lub Prześlij zdarzenie zawiera domyślny tekst przykładowy Moderowanie tekstu automatycznych. Możesz wprowadzić maksymalnie 1024 znaki.

## <a name="get-ready-to-review-results"></a>Przygotowanie do przeglądania wyników

Narzędzie do przeglądu, najpierw wywołuje interfejs API moderowania tekstu. Następnie generuje przeglądy tekstu przy użyciu tagów wykryte. Narzędzie do przeglądu dopasowuje wyniki oceny uwagi zespołu.

## <a name="review-text-results"></a>Przejrzyj wyniki tekstu

Szczegółowe wyniki są wyświetlane w oknach. Wyniki zawierają tagi wykryte i warunki, które zostały zwrócone przez interfejs API moderowania tekstu. Aby przełączyć stan zaznaczenia tagu, wybierz znacznik. Może również współdziałać za pomocą tagów niestandardowych, które zostały utworzone.

![Zrzut ekranu przedstawiający narzędzie do przeglądu oflagowane tekstu w oknie przeglądarki Chrome](../images/reviewresults_text.png)
