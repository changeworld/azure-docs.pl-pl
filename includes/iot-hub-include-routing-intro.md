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
ms.openlocfilehash: 9a20dca71727e83db98c4c97567949bd127fc7fb
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77111193"
---
[Routing wiadomości](../articles/iot-hub/iot-hub-devguide-messages-d2c.md) umożliwia wysyłanie danych telemetrycznych z urządzeń IoT do wbudowanych punktów końcowych zgodnych z Centrum zdarzeń lub niestandardowych punktów końcowych, takich jak magazyn obiektów blob, kolejki magistrali usług, tematy magistrali usług i centra zdarzeń. Aby skonfigurować niestandardowe routing wiadomości, należy utworzyć [kwerendy routingu,](../articles/iot-hub/iot-hub-devguide-routing-query-syntax.md) aby dostosować trasę, która odpowiada określonej warunku. Po zakończeniu konfiguracji dane przychodzące są automatycznie rozsyłane do punktów końcowych przez usługę IoT Hub. Jeśli wiadomość nie pasuje do żadnego z zdefiniowanych zapytań routingu, jest kierowana do domyślnego punktu końcowego.

W tym dwuczęściowym samouczku dowiesz się, jak skonfigurować te niestandardowe zapytania routingu i używać ich za pomocą usługi IoT Hub. Wysyłasz wiadomości z urządzenia IoT do jednego z wielu punktów końcowych, w tym magazynu obiektów blob i kolejki usługi Service Bus. Wiadomości do kolejki usługi Service Bus są odbierane przez aplikację logiki i wysyłane za pośrednictwem poczty e-mail. Wiadomości, które nie mają zdefiniowanego routingu wiadomości niestandardowych, są wysyłane do domyślnego punktu końcowego, a następnie odbierane przez usługę Azure Stream Analytics i wyświetlane w wizualizacji usługi Power BI.

Aby ukończyć części 1 i 2 tego samouczka, należy wykonać następujące zadania:

**Część I: Tworzenie zasobów, konfigurowanie routingu wiadomości**
> [!div class="checklist"]
> * Tworzenie zasobów — centrum IoT hub, konto magazynu, kolejka usługi Service Bus i symulowane urządzenie. Można to zrobić za pomocą witryny Azure portal, szablon usługi Azure Resource Manager, interfejsu wiersza polecenia platformy Azure lub programu Azure PowerShell.
> * Skonfiguruj punkty końcowe i trasy komunikatów w Centrum IoT dla konta magazynu i kolejki usługi Service Bus.

**Część II: Wysyłanie wiadomości do centrum, wyświetlanie trasowania wyników**
> [!div class="checklist"]
> * Tworzenie aplikacji logiki, która jest wyzwalana i wysyła wiadomość e-mail po dodaniu komunikatu do kolejki usługi Service Bus.
> * Pobieranie i uruchamianie aplikacji, która symuluje urządzenie IoT wysyłające komunikaty do centrum przy użyciu różnych opcji routingu.
> * Tworzenie wizualizacji usługi Power BI na potrzeby danych wysyłanych do domyślnego punktu końcowego.
> * Przeglądanie wyników...
> * ...w kolejce usługi Service Bus i wiadomościach e-mail.
> * ...na koncie magazynu.
> * ...w wizualizacji usługi Power BI.

## <a name="prerequisites"></a>Wymagania wstępne

* W części 1 tego samouczka:
  - Wymagana jest subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

* W części 2 tego samouczka:
  - Musisz ukończyć część 1 tego samouczka i mieć zasoby nadal dostępne.
  - Zainstaluj [program Visual Studio](https://www.visualstudio.com/).
  - Mieć dostęp do konta usługi Power BI do analizowania domyślnej analizy strumienia punktu końcowego. ([Wypróbuj bezpłatnie usługę Power BI](https://app.powerbi.com/signupredirect?pbi_source=web)).
  - Mieć konto usługi Office 365 do wysyłania wiadomości e-mail z powiadomieniami.
  - Upewnij się, że port 8883 jest otwarty w zaporze. Przykład w tym samouczku używa protokołu MQTT, który komunikuje się za pomocą portu 8883. Ten port może być zablokowany w niektórych środowiskach sieci firmowych i edukacyjnych. Aby uzyskać więcej informacji i sposobów obejść ten problem, zobacz [Łączenie się z centrum IoT Hub (MQTT)](../articles/iot-hub/iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [cloud-shell-try-it.md](cloud-shell-try-it.md)]
