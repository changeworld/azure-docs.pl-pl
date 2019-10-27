---
title: Wyzwalanie Azure Functions przy użyciu elementów webhook na platformie Azure IoT Central
description: Utwórz aplikację funkcji, która jest uruchamiana przy każdym wyzwoleniu reguły na platformie Azure IoT Central.
author: viv-liu
ms.author: viviali
ms.date: 07/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: a9590e5554956418859a07b43fb57724783343fe
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/25/2019
ms.locfileid: "72952851"
---
# <a name="trigger-azure-functions-using-webhooks-in-azure-iot-central"></a>Wyzwalanie Azure Functions przy użyciu elementów webhook na platformie Azure IoT Central

*Ten temat dotyczy konstruktorów i administratorów.*

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

Użyj Azure Functions, aby uruchomić kod bezserwerowy w danych wyjściowych elementu webhook z reguł IoT Central. Nie trzeba udostępniać maszyny wirtualnej ani publikować aplikacji sieci Web w celu używania Azure Functions, ale zamiast tego można uruchomić ten serwer kodu. Użyj Azure Functions, aby przekształcić ładunek elementu webhook przed wysłaniem go do ostatecznego miejsca docelowego, takiego jak baza danych SQL lub Event Grid.

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="how-to-connect-azure-functions"></a>Jak nawiązać połączenie Azure Functions

1. [Utwórz nową aplikację funkcji w Azure Portal](https://ms.portal.azure.com/#create/Microsoft.FunctionApp).

    ![Utwórz nową aplikację funkcji w Azure Portal](media/howto-trigger-azure-functions/createfunction.png)

2. Rozwiń aplikację funkcji i wybierz **przycisk +** obok pozycji funkcje. Jeśli ta funkcja jest pierwszym z nich w aplikacji funkcji, wybierz **w portalu** jako środowisko programistyczne i wybierz pozycję **Kontynuuj**.

    ![Wybierz funkcję niestandardową w aplikacji funkcji](media/howto-trigger-azure-functions/customfunction.png)

3. Wybierz pozycję **element webhook i szablon interfejsu API** i wybierz pozycję **Utwórz**. W tym temacie jest stosowana oparta na platformie .NET funkcja Azure Functions.

    ![Wybierz wyzwalacz ogólnego elementu webhook](media/howto-trigger-azure-functions/genericwebhooktrigger.png)

4. W nowej funkcji wybierz pozycję **</> Pobierz adres URL funkcji**, a następnie skopiuj i Zapisz wartość. Będzie ona używana do skonfigurowania elementu webhook.

    ![Pobierz adres URL funkcji](media/howto-trigger-azure-functions/getfunctionurl.png)

4. W IoT Central przejdź do reguły, którą chcesz połączyć z aplikacją funkcji.

5. Dodaj akcję elementu webhook. Wprowadź **nazwę wyświetlaną** i wklej w adresie URL funkcji skopiowanej wcześniej do **adresu URL wywołania zwrotnego**.

    ![Wprowadź adres URL funkcji w polu adres URL wywołania zwrotnego](media/howto-trigger-azure-functions/configurewebhook.PNG)

6. Zapisz regułę. Teraz, gdy reguła zostanie wyzwolona, element webhook wywoła aplikację funkcji do uruchomienia. W aplikacji funkcji można wybrać pozycję **monitor** , aby zobaczyć historię wywołania funkcji. Aby sprawdzić historię, możesz użyć usługi App Insights lub klasycznego widoku.

    ![Monitoruj historię wywołania funkcji](media/howto-trigger-azure-functions/monitorfunction.PNG)

Aby uzyskać więcej informacji, odwiedź artykuł Azure Functions dotyczący [tworzenia funkcji wyzwalanej przez ogólny element webhook](https://docs.microsoft.com/azure/azure-functions/functions-create-generic-webhook-triggered-function).

## <a name="next-steps"></a>Następne kroki
Teraz, gdy już wiesz, jak skonfigurować elementy webhook i korzystać z nich, sugerowanym następnym krokiem jest Eksplorowanie [przepływów pracy w Microsoft Flow](howto-add-microsoft-flow.md).
