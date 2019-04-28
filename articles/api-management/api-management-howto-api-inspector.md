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
ms.date: 06/15/2018
ms.author: apimpm
ms.openlocfilehash: ff3dde8ac95b678866ba6f5216ba23357b067765
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62119099"
---
# <a name="debug-your-apis-using-request-tracing"></a>Debugowanie interfejsów API za pomocą funkcji śledzenia żądań

W tym samouczku opisano, jak przeprowadzać inspekcję przetwarzania żądań w celu debugowania interfejsu API i rozwiązywania problemów z nim. 

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Śledzenie wywołania

![Inspektor interfejsu API](media/api-management-howto-api-inspector/api-inspector001.PNG)

## <a name="prerequisites"></a>Wymagania wstępne

+ Poznaj [terminologię dotyczącą usługi Azure API Management](api-management-terminology.md).
+ Wykonaj procedury przedstawione w następującym przewodniku Szybki start: [Tworzenie wystąpienia usługi Azure API Management](get-started-create-service-instance.md).
+ Ukończ również następujący samouczek: [Importowanie i publikowanie pierwszego interfejsu API](import-and-publish.md).

## <a name="trace-a-call"></a>Śledzenie wywołania

![Śledzenie interfejsu API](media/api-management-howto-api-inspector/06-DebugYourAPIs-01-TraceCall.png)

1. Wybierz pozycję **Interfejsy API**.
2. Kliknij pozycję **Demo Conference API** (Pokazowy interfejs API konferencji) na liście interfejsów API.
3. Przełącz się na kartę **Test**.
4. Wybierz operację **GetSpeakers**.
5. Pamiętaj, aby dołączyć nagłówek HTTP o nazwie **Ocp-Apim-Trace** z wartością ustawioną na **true**.

    > [!NOTE]
    > Jeśli klucz subskrypcji Ocp-Apim-Subscription-Key nie zostanie wypełniony automatycznie, można go pobrać, przechodząc do portalu dla deweloperów i wyświetlając klucze na stronie profilu.

6. Kliknij przycisk **„Wyślij”** w celu wygenerowania wywoływania interfejsu API. 
7. Poczekaj na ukończenie wywołania. 
8. Przejdź na kartę **Śledzenie** w **konsoli interfejsu API**. Możesz kliknąć dowolny z następujących linków, aby przejść bezpośrednio do szczegółowych informacji śledzenia: **dane przychodzące**, **zaplecze**, **dane wychodzące**.

    W sekcji **dane przychodzące** widać oryginalne żądanie odebrane przez usługę API Management od wywołującego i wszystkie zasady zastosowane do tego żądania, w tym limit szybkości i zasady set-header dodane w kroku 2.

    W sekcji **zaplecze** widać żądania wysłane przez usługę API Management do zaplecza interfejsu API i otrzymane odpowiedzi.

    W sekcji **dane wychodzące** widać wszystkie zasady zastosowane do odpowiedzi przed wysłaniem ich z powrotem do wywołującego.

    > [!TIP]
    > Przy każdym kroku widać również czas, jaki upłynął od odebrania żądania przez usługę API Management.

## <a name="next-steps"></a>Kolejne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Śledzenie wywołania

Przejdź do następnego samouczka:

> [!div class="nextstepaction"]
> [Korzystanie z poprawek](api-management-get-started-revise-api.md)
