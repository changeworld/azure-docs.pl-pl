---
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.author: dobett
ms.openlocfilehash: 2d041ccbcf85f1931ca8ebc8b17595c9221c03cc
ms.sourcegitcommit: 82cdc26615829df3c57ee230d99eecfa1c4ba459
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/19/2019
ms.locfileid: "54414599"
---
## <a name="add-a-consumer-group-to-your-iot-hub"></a>Dodaj grupę odbiorców do usługi IoT hub

Grupy konsumentów są używane przez aplikacje do pobierania danych z usługi Azure IoT Hub. W tym samouczku utworzysz grupy odbiorców do użycia przez najbliższych usługi platformy Azure można odczytać danych z usługi IoT hub.

Aby dodać grupę odbiorców do Centrum IoT hub, wykonaj następujące kroki:

1. W witrynie [Azure Portal](https://portal.azure.com/) otwórz swoje centrum IoT Hub.

2. W okienku po lewej stronie kliknij **wbudowanych punktach końcowych**, wybierz opcję **zdarzenia** w górnym okienku i wprowadź nazwę w polu **grupy konsumentów** w okienku po prawej stronie. Kliknij przycisk **Zapisz** po zmianie **domyślny czas wygaśnięcia** wartość i zwrócić go do oryginalnej wartości.

   ![Utwórz grupę odbiorców w usłudze IoT hub](./media/iot-hub-get-started-create-consumer-group/iot-hub-create-consumer-group-azure.png)
