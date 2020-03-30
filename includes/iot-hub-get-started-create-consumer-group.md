---
author: robinsh
manager: philmea
ms.author: robinsh
ms.topic: include
ms.date: 05/20/2019
ms.openlocfilehash: c164433efc6a34a3a06676a3145feb18d3de80b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "66249104"
---
## <a name="add-a-consumer-group-to-your-iot-hub"></a>Dodawanie grupy odbiorców do centrum IoT

[Grupy konsumentów](https://docs.microsoft.com/azure/event-hubs/event-hubs-features#event-consumers) zapewniają niezależne widoki do strumienia zdarzeń, które umożliwiają aplikacjom i usługom platformy Azure niezależne korzystanie z danych z tego samego punktu końcowego usługi Event Hub. W tej sekcji należy dodać grupę odbiorców do wbudowanego punktu końcowego centrum IoT, który jest używany w dalszej części tego samouczka do ściągania danych z punktu końcowego.

Aby dodać grupę odbiorców do centrum IoT hub, wykonaj następujące kroki:

1. W witrynie [Azure Portal](https://portal.azure.com/) otwórz swoje centrum IoT Hub.

2. W lewym okienku wybierz **pozycję Wbudowane punkty końcowe**, wybierz **pozycję Zdarzenia** w prawym okienku i wprowadź nazwę w obszarze **Grupy odbiorców**. Wybierz **pozycję Zapisz**.

   ![Tworzenie grupy odbiorców w centrum IoT](./media/iot-hub-get-started-create-consumer-group/iot-hub-create-consumer-group-azure.png)
