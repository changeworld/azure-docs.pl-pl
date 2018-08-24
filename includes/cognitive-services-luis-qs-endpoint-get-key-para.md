---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: luis
ms.topic: include
ms.custom: include file
ms.date: 08/16/2018
ms.author: diberry
ms.openlocfilehash: 25a70d314ecb690a34e89a3dff7d2c4fad8b7a00
ms.sourcegitcommit: 58c5cd866ade5aac4354ea1fe8705cee2b50ba9f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/24/2018
ms.locfileid: "42820023"
---
Dostęp do endpoint prognoz znajduje się przy użyciu klucza punktu końcowego. Na potrzeby tego przewodnika Szybki Start za pomocą klawisza bezpłatne starter skojarzonych z Twoim kontem usługi LUIS. 
 
1. Zaloguj się przy użyciu swojego konta usługi LUIS. 

    [![Zrzut ekranu z Language Understanding (LUIS) listy aplikacji](media/cognitive-services-luis/app-list.png "listy aplikacji zrzut ekranu z Language Understanding (LUIS)")](media/cognitive-services-luis/app-list.png)

2. Wybierz nazwę w prawym górnym menu, a następnie wybierz **ustawienia**.

    ![Usługa LUIS dostęp do menu Ustawienia dla użytkownika](media/cognitive-services-luis/get-user-settings-in-luis.png)

3. Skopiuj wartość **Tworzenie klucza**. Użyjesz go w dalszej części tego przewodnika Szybki Start. 

    [![Zrzut ekranu z Language Understanding (LUIS), ustawień użytkownika](media/cognitive-services-luis/get-user-authoring-key.png "ustawienia użytkownika zrzut ekranu z Language Understanding (LUIS)")](media/cognitive-services-luis/get-user-authoring-key.png)

    Klucz tworzenia umożliwia bezpłatne żądania bez ograniczeń do tworzenia interfejsu API i maksymalnie 1000 zapytań do endpoint prognoz interfejsu API na miesiąc dla wszystkich aplikacji usługi LUIS. <!--Once the prediction endpoint quota from the authoring key is used for the month, you need to create a **Language Understanding** key from the Azure portal. The key created in the portal is known as the endpoint key. The endpoint key is used _only_ for prediction endpoint queries.-->