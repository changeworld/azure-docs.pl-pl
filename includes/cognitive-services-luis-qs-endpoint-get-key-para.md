---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.subservice: luis
ms.topic: include
ms.custom: include file
ms.date: 08/16/2018
ms.author: diberry
ms.openlocfilehash: 14678a143e1b14b9b0b89435f356ac5df98ef40c
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/31/2019
ms.locfileid: "55478687"
---
Dostęp do punktu końcowego przewidywania jest zapewniany przy użyciu klucza punktu końcowego. Na potrzeby tego przewodnika Szybki start użyj bezpłatnego klucza początkowego skojarzonego z Twoim kontem usługi LUIS. 
 
1. Zaloguj się przy użyciu konta usługi LUIS. 

    [![Zrzut ekranu przedstawiający listę aplikacji usługi Language Understanding (LUIS)](media/cognitive-services-luis/app-list.png "Zrzut ekranu przedstawiający listę aplikacji usługi Language Understanding (LUIS)")](media/cognitive-services-luis/app-list.png)

2. Wybierz swoją nazwę w prawym górnym menu, a następnie wybierz pozycję **Settings** (Ustawienia).

    ![Dostęp do menu ustawień użytkownika usługi LUIS](media/cognitive-services-luis/get-user-settings-in-luis.png)

3. Skopiuj wartość **Authoring key** (Klucz tworzenia). Użyjesz go w dalszej części tego przewodnika Szybki start. 

    [![Zrzut ekranu przedstawiający ustawienia użytkownika usługi Language Understanding (LUIS)](media/cognitive-services-luis/get-user-authoring-key.png "Zrzut ekranu przedstawiający ustawienia użytkownika usługi Language Understanding (LUIS)")](media/cognitive-services-luis/get-user-authoring-key.png)

    Klucz tworzenia umożliwia nieograniczone bezpłatne wysyłanie żądań do interfejsu API tworzenia oraz wysyłanie maksymalnie 1000 zapytań miesięcznie do punktu końcowego przewidywania dla wszystkich Twoich aplikacji LUIS. <!--Once the prediction endpoint quota from the authoring key is used for the month, you need to create a **Language Understanding** key from the Azure portal. The key created in the portal is known as the endpoint key. The endpoint key is used _only_ for prediction endpoint queries.-->
