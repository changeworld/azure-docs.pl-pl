---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.custom: include file
ms.date: 02/14/2020
ms.subservice: language-understanding
ms.author: diberry
ms.openlocfilehash: 956aa308bf1cb3736c491031239661ec6b295ddb
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77279614"
---
Aplikacja kliencka musi wiedzieć, czy wypowiedź nie jest znacząca i czy nie jest odpowiednia dla aplikacji. Brak **None** intencji jest dodawany do każdej aplikacji w ramach procesu tworzenia, aby ustalić, czy wypowiedź nie powinna być odbierana przez aplikację kliencką.

Jeśli usługa LUIS zwraca intencję wypowiedzi **None**, aplikacja kliencka może zapytać, czy użytkownik chce zakończyć konwersację lub dostarczyć więcej wskazówek pozwalających kontynuować konwersację.

Jeśli pozostawisz **Brak** intencji puste, wypowiedź, które powinny być przewidywane poza domeną tematu będą przewidywane w jednym z istniejących intencji domeny podmiotu. Wynik jest, że aplikacja kliencka, takich jak czat bot, będzie wykonywać niepoprawne operacje na podstawie przewidywania niepoprawne.

1. Wybierz pozycję **Intents** (Intencje) na lewym panelu.

1. Wybierz intencję **None**. Dodaj trzy wypowiedzi, które użytkownik może wprowadzić, ale nie są istotne dla aplikacji do zamawiania pizzy:

    |`None`przykładowe wypowiedzi|
    |--|
    |`Barking dogs are annoying`|
    |`Penguins in the ocean`|

    Te przykłady nie powinny używać słów oczekiwanych `pizza`w `cheese` `crust`domenie tematu, takich jak , , . `pickup` `deliver`