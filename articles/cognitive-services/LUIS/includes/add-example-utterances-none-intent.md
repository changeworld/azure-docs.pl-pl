---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: luis
ms.topic: include
ms.custom: include file
ms.date: 11/20/2019
ms.author: diberry
ms.openlocfilehash: e4147fbb27c8538f801f6c49f8b535a283faf50f
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74325899"
---
Aplikacja kliencka musi wiedzieć, czy wypowiedź nie jest znacząca i czy nie jest odpowiednia dla aplikacji. Zamiara brak jest dodawana do każdej aplikacji w ramach procesu tworzenia, aby określić, czy dla aplikacji klienckiej **nie** powinien odpowiadać wypowiedź.

Jeśli usługa LUIS zwraca intencję wypowiedzi **None**, aplikacja kliencka może zapytać, czy użytkownik chce zakończyć konwersację lub dostarczyć więcej wskazówek pozwalających kontynuować konwersację. 

W przypadku pozostawienia pustego zamiaru **wartość** wypowiedź, która powinna zostać przewidywalna poza domenę podmiotu, zostanie przewidywalna w jednym z istniejących intencji domeny podmiotu. W efekcie aplikacja kliencka, taka jak rozmowa bot, wykona nieprawidłowe operacje w oparciu o niewłaściwe prognozowanie. 

1. Wybierz pozycję **Intents** (Intencje) na lewym panelu.

1. Wybierz intencję **None**. Dodaj trzy wyrażenia długości, które użytkownik może wprowadzić, ale które nie są istotne dla aplikacji Pizza Order:

    |`None` przykład wyrażenia długości|
    |--|
    |`Barking dogs are annoying`|
    |`Order a pizza for me`|
    |`Penguins in the ocean`|

    W tych przykładach nie należy używać wyrazów oczekiwanych w domenie podmiotu, takich jak `pizza`, `cheese`, `crust``pickup` `deliver`.