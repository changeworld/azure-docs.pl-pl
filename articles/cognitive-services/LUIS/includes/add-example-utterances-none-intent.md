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
ms.date: 11/05/2019
ms.author: diberry
ms.openlocfilehash: bbe40083159d433c0b5746834e1c530b23b03851
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73648300"
---
Aplikacja kliencka musi wiedzieć, czy wypowiedź nie jest znacząca i czy nie jest odpowiednia dla aplikacji. Intencja **None** jest dodawana do każdej aplikacji w ramach procesu tworzenia, co pozwala określić, czy aplikacja kliencka może odpowiedzieć na wypowiedź.

Jeśli usługa LUIS zwraca intencję wypowiedzi **None**, aplikacja kliencka może zapytać, czy użytkownik chce zakończyć konwersację lub dostarczyć więcej wskazówek pozwalających kontynuować konwersację. 

W przypadku pozostawienia pustego zamiaru **wartość** wypowiedź, która powinna zostać przewidywalna poza domenę podmiotu, zostanie przewidywalna w jednym z istniejących intencji domeny podmiotu. W efekcie aplikacja kliencka, taka jak rozmowa bot, wykona nieprawidłowe operacje w oparciu o niewłaściwe prognozowanie. 

1. Wybierz pozycję **Intents** (Intencje) na lewym panelu.

1. Wybierz intencję **None**. Dodaj trzy wypowiedzi, które może wprowadzić użytkownik, ale które nie są istotne dla tej aplikacji:

    |`None` przykład wyrażenia długości|
    |--|
    |`Barking dogs are annoying`|
    |`Order a pizza for me`|
    |`Penguins in the ocean`|
