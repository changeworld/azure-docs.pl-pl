---
title: Plik dyrektywy include
description: Plik dyrektywy include
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 03/05/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 400f12237ae8b8cbaf6d66bda1663ecb680136f3
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58630934"
---
[Routing komunikatów](../articles/iot-hub/iot-hub-devguide-messages-d2c.md) umożliwia wysyłanie danych telemetrycznych z urządzeń IoT do wbudowanych punktach końcowych zgodnego z Centrum zdarzeń lub niestandardowe punkty końcowe takich jak usługi blob storage, kolejek usługi Service Bus, tematy usługi Service Bus i Event Hubs. Aby skonfigurować routing niestandardowy komunikat, należy utworzyć [zapytań routingu](../articles/iot-hub/iot-hub-devguide-routing-query-syntax.md) dostosować trasy, który odpowiada określony warunek. Po zakończeniu konfiguracji dane przychodzące są automatycznie rozsyłane do punktów końcowych przez usługę IoT Hub. Jeśli komunikat nie pasuje do żadnego z zdefiniowane zapytania routingu, odbywa się do domyślnego punktu końcowego.

W tym samouczku 2-częściowych dowiesz się, jak skonfigurować i korzystać z tych niestandardowych zapytań routingu za pomocą usługi IoT Hub. Możesz przesyłać wiadomości z urządzenia IoT do jednego z wielu punktów końcowych, w tym magazyn obiektów blob i kolejki usługi Service Bus. Komunikaty do kolejki usługi Service Bus są pobierane przez aplikację logiki i wysyłane za pośrednictwem poczty e-mail. Komunikaty, które nie mają niestandardowy komunikat routing zdefiniowany są wysyłane do domyślnego punktu końcowego, a następnie pobierane przez usługę Azure Stream Analytics i wyświetlane w wizualizacji usługi Power BI.

 Na zakończenie części 1 i 2 tego samouczka należy wykonać następujące zadania:

**Część I: Tworzenie zasobów, skonfigurować routing wiadomości**
> [!div class="checklist"]
> * Tworzenie zasobów — Centrum IoT hub, konto magazynu, kolejki usługi Service Bus i symulowanego urządzenia. Można to zrobić za pomocą portalu, interfejsu wiersza polecenia platformy Azure, programu Azure PowerShell lub szablonu usługi Azure Resource Manager.
> * Punkty końcowe i skonfigurować trasy wiadomości w usłudze IoT Hub dla konta magazynu i kolejki usługi Service Bus.

**Part II: Wysyłanie komunikatów do Centrum, wyświetlanie wyników trasowane**
> [!div class="checklist"]
> * Tworzenie aplikacji logiki, która jest wyzwalana i wysyła wiadomość e-mail po dodaniu komunikatu do kolejki usługi Service Bus.
> * Pobieranie i uruchamianie aplikacji, która symuluje urządzenie IoT wysyłające komunikaty do centrum przy użyciu różnych opcji routingu.
> * Tworzenie wizualizacji usługi Power BI na potrzeby danych wysyłanych do domyślnego punktu końcowego.
> * Przeglądanie wyników...
> * ...w kolejce usługi Service Bus i wiadomościach e-mail.
> * ...na koncie magazynu.
> * ...w wizualizacji usługi Power BI.

## <a name="prerequisites"></a>Wymagania wstępne

* Dla części 1 tego samouczka:
  - Wymagana jest subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Dla tego samouczka, część 2:
  - Konieczne jest posiadanie ukończone części 1 tego samouczka oraz zasoby, które są nadal dostępne.
  - Instalacja programu [Visual Studio](https://www.visualstudio.com/).
  - Konto usługi Power BI do analizowania danych analitycznych strumienia domyślnego punktu końcowego. ([Wypróbuj bezpłatnie usługę Power BI](https://app.powerbi.com/signupredirect?pbi_source=web)).
  - Konto usługi Office 365 do wysyłania powiadomień e-mail.

[!INCLUDE [cloud-shell-try-it.md](cloud-shell-try-it.md)]
