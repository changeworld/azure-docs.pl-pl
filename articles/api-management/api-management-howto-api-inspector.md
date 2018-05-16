---
title: Debugowanie interfejsów API za pomocą funkcji śledzenia żądań w usłudze Azure API Management | Microsoft Docs
description: Postępuj zgodnie z krokami w tym samouczku, aby dowiedzieć się, jak przeprowadzać inspekcję kroków przetwarzania żądań w usłudze Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 11/19/2017
ms.author: apimpm
ms.openlocfilehash: 01a9120af2fb240eed1463613f7a6f946a9c16ed
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
---
# <a name="debug-your-apis-using-request-tracing"></a>Debugowanie interfejsów API za pomocą funkcji śledzenia żądań

W tym samouczku opisano, jak przeprowadzać inspekcję przetwarzania żądań w celu debugowania interfejsu API i rozwiązywania problemów z nim. 

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Śledzenie wywołania

![Inspektor interfejsu API](media/api-management-howto-api-inspector/api-inspector001.PNG)

## <a name="prerequisites"></a>Wymagania wstępne

+ Wykonaj procedury przedstawione w następującym przewodniku Szybki start: [Tworzenie wystąpienia usługi Azure API Management](get-started-create-service-instance.md).
+ Ponadto wykonaj zadania z następującego samouczka: [Importowanie i publikowanie pierwszego interfejsu API](import-and-publish.md).

## <a name="trace-a-call"></a>Śledzenie wywołania

1. Wybierz pozycję **Interfejsy API**.
2. Kliknij pozycję **Demo Conference API** (Pokazowy interfejs API konferencji) na liście interfejsów API.
3. Wybierz operację **GetSpeakers**.
4. Przełącz się na kartę **Test**.
5. Pamiętaj, aby dołączyć nagłówek HTTP o nazwie **Ocp-Apim-Trace** z wartością ustawioną na **true**.
6. Kliknij przycisk **„Wyślij”** w celu wygenerowania wywoływania interfejsu API. 
7. Poczekaj na ukończenie wywołania. 
8. Przejdź na kartę **Śledzenie** w **konsoli interfejsu API**. Możesz kliknąć dowolny z następujących linków, aby przejść bezpośrednio do szczegółowych informacji śledzenia: **dane przychodzące**, **zaplecze**, **dane wychodzące**.

    W sekcji **dane przychodzące** widać oryginalne żądanie odebrane przez usługę API Management od wywołującego i wszystkie zasady zastosowane do tego żądania, w tym limit szybkości i zasady set-header dodane w kroku 2.

    W sekcji **zaplecze** widać żądania wysłane przez usługę API Management do zaplecza interfejsu API i otrzymane odpowiedzi.
    
    W sekcji **dane wychodzące** widać wszystkie zasady zastosowane do odpowiedzi przed wysłaniem ich z powrotem do wywołującego.
 
    > [!TIP]
    > Przy każdym kroku widać również czas, jaki upłynął od odebrania żądania przez usługę API Management.

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Śledzenie wywołania

Przejdź do następnego samouczka:

> [!div class="nextstepaction"]
> [Korzystanie z poprawek](api-management-get-started-revise-api.md)
