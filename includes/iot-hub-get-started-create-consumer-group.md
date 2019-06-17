---
author: robinsh
manager: philmea
ms.author: robinsh
ms.topic: include
ms.date: 05/20/2019
ms.openlocfilehash: c164433efc6a34a3a06676a3145feb18d3de80b9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66249104"
---
## <a name="add-a-consumer-group-to-your-iot-hub"></a>Dodaj grupę odbiorców do usługi IoT hub

[Grupy konsumentów](https://docs.microsoft.com/azure/event-hubs/event-hubs-features#event-consumers) zapewnia niezależne widoki do strumienia zdarzeń, które umożliwiają aplikacjom i usługom platformy Azure niezależnie korzystają z tego samego punktu końcowego Centrum zdarzeń danych. W tej sekcji dodajesz grupy odbiorców do usługi IoT hub wbudowany punkt końcowy, który jest używany w dalszej części tego samouczka do pobierania danych z punktu końcowego.

Aby dodać grupę odbiorców do Centrum IoT hub, wykonaj następujące kroki:

1. W witrynie [Azure Portal](https://portal.azure.com/) otwórz swoje centrum IoT Hub.

2. W okienku po lewej stronie wybierz pozycję **wbudowanych punktach końcowych**, wybierz opcję **zdarzenia** w okienku po prawej stronie, a następnie wprowadź nazwę w polu **grup odbiorców**. Wybierz pozycję **Zapisz**.

   ![Utwórz grupę odbiorców w usłudze IoT hub](./media/iot-hub-get-started-create-consumer-group/iot-hub-create-consumer-group-azure.png)
