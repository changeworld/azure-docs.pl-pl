---
title: Przejrzyj tekst w usłudze Azure Content Moderator — Content Moderator
description: Dowiedz się, jak przeglądać tekstu w pakiecie Content Moderator, aby wyświetlić jego wynik i wykryto tagów. Skorzystaj z informacji, aby ustalić, czy zawartość jest odpowiednia.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: article
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 896956653ed924b7e30e63c1ae5cfa90dd0cc94d
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2019
ms.locfileid: "55219791"
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
