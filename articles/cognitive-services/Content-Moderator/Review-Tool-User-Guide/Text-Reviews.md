---
title: Przejrzyj tekst w zawartości moderatora Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak sprawdzić tekst w moderatora zawartości, aby wyświetlić jego wynik i wykryto tagów. Skorzystaj z informacji, aby ustalić, czy zawartość jest odpowiednia.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 02/03/2017
ms.author: sajagtap
ms.openlocfilehash: cb8774395b7374677e8de3b80630a2d4abf490f9
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35346961"
---
# <a name="review-text"></a>Przegląd tekstu

Umożliwia przeglądanie tekstu przy użyciu wyniki moderatora zawartości platformy Azure i wykryto tagów. Skorzystaj z informacji do ustalenia, czy zawartość jest odpowiedni. 

## <a name="select-or-enter-the-text-to-review"></a>Wybierz lub wprowadź tekst do przeglądu

W zawartości moderatora wybierz **spróbuj** kartę. Następnie wybierz opcję **tekst** opcję, aby przejść do ekranu startowego łagodzenia tekstu. Wprowadź tekst lub Prześlij domyślny tekst przykładowy łagodzenia automatycznych tekstu. Możesz wprowadzić maksymalnie 1024 znaki.

## <a name="get-ready-to-review-results"></a>Przygotowanie do przeglądania wyników

Narzędzie przeglądu najpierw wywołuje interfejs API łagodzenia tekstu. Następnie generuje przeglądami tekstu przy użyciu tagów wykryte. Narzędzie przeglądu odpowiada wyników klasyfikacji dla Twojego zespołu uwagi.

## <a name="review-text-results"></a>Przejrzyj wyniki tekstu

Szczegółowe wyniki są wyświetlane w systemie windows. Wyniki zawierają wykryto tagów i warunków, które zostały zwrócone przez interfejs API łagodzenia tekstu. Aby przełączyć stan zaznaczenia tag, zaznacz tag. Możesz także pracować z dowolnego znaczniki niestandardowe, które zostały utworzone.

![Przejrzyj wyniki tekstu](images/3-review-text-2.png)
