---
title: Wyzwalanie usługi Azure Functions przy użyciu elementów webhook w usłudze Azure IoT Central
description: Tworzenie aplikacji funkcji, która działa z każdym razem, gdy reguła jest wyzwalana w usłudze Azure IoT Central.
author: viv-liu
ms.author: viviali
ms.date: 02/20/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 8d638743b3cb07b0d060e754ecb76e74bd02719d
ms.sourcegitcommit: c712cb5c80bed4b5801be214788770b66bf7a009
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/01/2019
ms.locfileid: "57215017"
---
# <a name="trigger-azure-functions-using-webhooks-in-azure-iot-central"></a>Wyzwalanie usługi Azure Functions przy użyciu elementów webhook w usłudze Azure IoT Central

*Ten temat dotyczy konstruktorów i administratorów.*

Uruchamianie kodu bez serwera na dane wyjściowe elementu webhook z IoT Central reguły za pomocą usługi Azure Functions. Nie trzeba aprowizować maszynę Wirtualną lub publikowania aplikacji sieci web za pomocą usługi Azure Functions, ale zamiast tego można uruchomić ten kod serverlessly. Przekształcanie ładunek elementu webhook przed wysłaniem ich do miejsca docelowego, takich jak bazy danych SQL lub usługi Event Grid przy użyciu usługi Azure Functions.

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="how-to-connect-azure-functions"></a>Sposób połączenia usługi Azure Functions

1. [Utwórz nową aplikację funkcji w witrynie Azure Portal](https://ms.portal.azure.com/#create/Microsoft.FunctionApp).

    ![Utwórz nową aplikację funkcji w witrynie Azure Portal](media/howto-trigger-azure-functions/createfunction.png)

2. Rozwiń swoją aplikację funkcji, a następnie kliknij przycisk **przycisk +** obok funkcji. Jeśli jest to pierwsza funkcja w aplikacji funkcji, wybierz pozycję **Funkcja niestandardowa**. Spowoduje to wyświetlenie pełnego zestawu szablonów funkcji.

    ![Wybierz funkcję niestandardowego w aplikacji funkcji](media/howto-trigger-azure-functions/customfunction.png)

3. W polu wyszukiwania wpisz **"generic"** , a następnie wybierz żądany język dla szablonu wyzwalacza ogólnego elementu webhook. W tym temacie użyto funkcji języka C#. 

    ![Wybieranie wyzwalacza ogólnego elementu webhook](media/howto-trigger-azure-functions/genericwebhooktrigger.png)

4. W nowej funkcji kliknij pozycję **</> Pobierz adres URL funkcji**, po czym skopiuj i zapisz wartość. Będzie ona używana do skonfigurowania elementu webhook.

    ![Pobierz adres URL funkcji](media/howto-trigger-azure-functions/getfunctionurl.png)

4. W IoT Central przejdź do reguły, którą chcesz nawiązać połączenie z aplikacją funkcji.

5. Dodawanie akcji elementu webhook. Wprowadź **nazwę wyświetlaną** i wklej adres URL funkcji, które wcześniej zostały skopiowane do **adresów URL wywołania zwrotnego**.

    ![Wprowadź adres URL funkcji w polu adres URL wywołania zwrotnego](media/howto-trigger-azure-functions/configurewebhook.PNG)

6. Zapisać reguły. Teraz po wyzwoleniu reguły, element webhook wywołuje aplikację funkcji, aby uruchomić. W aplikacji funkcji, można kliknąć opcje **Monitor** Aby wyświetlić historię wywołania funkcji. App Insights lub widok klasyczny umożliwia Przyjrzyj się historii.

    ![Monitorowanie historii wywołania funkcji](media/howto-trigger-azure-functions/monitorfunction.PNG)

Więcej informacji można znaleźć w artykule usługi Azure Functions o [Tworzenie funkcji wyzwalanej przez ogólny element webhook](https://docs.microsoft.com/azure/azure-functions/functions-create-generic-webhook-triggered-function).

## <a name="next-steps"></a>Kolejne kroki
Teraz, gdy wiesz jak konfigurowanie i używanie elementów webhook sugerowane następnym krokiem jest Eksplorowanie [tworzenia przepływów pracy w programie Microsoft Flow](howto-add-microsoft-flow.md).
