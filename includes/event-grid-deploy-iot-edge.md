---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: include
ms.date: 10/10/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: b453a04a170764a037eed7415eaf71e5a4d37526
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76844596"
---
## <a name="deploy-event-grid-iot-edge-module"></a>Wdrażanie modułu IoT Edge w siatce zdarzeń

Istnieje kilka sposobów wdrażania modułów na urządzeniu usługi IoT Edge i wszystkie z nich działają w usłudze Azure Event Grid w usłudze IoT Edge. W tym artykule opisano kroki wdrażania usługi Event Grid w usłudze IoT Edge z witryny Azure portal.

>[!NOTE]
> W tym samouczku wdrożysz moduł siatki zdarzeń bez trwałości. Oznacza to, że wszystkie tematy i subskrypcje utworzone w tym samouczku zostaną usunięte po przemieszczeniu modułu. Aby uzyskać więcej informacji na temat konfigurowania trwałości, zobacz następujące artykuły: [Utrwalić stan w systemie Linux](../articles/event-grid/edge/persist-state-linux.md) lub Persist state w systemie [Windows](../articles/event-grid/edge/persist-state-windows.md). W przypadku obciążeń produkcyjnych zaleca się zainstalowanie modułu event grid z trwałością.

>[!IMPORTANT]
> W tym samouczku moduł siatki zdarzeń zostanie wdrożony z wyłączonym uwierzytelnianiem klienta i zezwolić subskrybentom HTTP. W przypadku obciążeń produkcyjnych zaleca się włączenie tylko żądań HTTPS i subskrybentów z włączonym uwierzytelnianiem klienta. Aby uzyskać więcej informacji na temat bezpiecznego konfigurowania modułu event grid, zobacz [Zabezpieczenia i uwierzytelnianie](../articles/event-grid/edge/security-authentication.md).
 
### <a name="select-your-iot-edge-device"></a>Wybieranie urządzenia IoT Edge

1. Logowanie się do [witryny Azure portal](https://portal.azure.com)
1. Przejdź do centrum IoT Hub.
1. Wybierz pozycję **IoT Edge** z menu w sekcji **Automatyczne zarządzanie urządzeniami.** 
1. Kliknij identyfikator urządzenia docelowego z listy urządzeń
1. Wybierz pozycję **Ustaw moduły**. Zachowaj otwartą stronę. Będziesz kontynuować kroki w następnej sekcji.

### <a name="configure-a-deployment-manifest"></a>Konfigurowanie manifestu wdrażania

Manifest wdrożenia to dokument JSON, który opisuje, które moduły do wdrożenia, jak przepływy danych między modułami i żądane właściwości bliźniaczych reprezentacji modułu. Portal Azure ma kreatora, który przeprowadzi Cię przez tworzenie manifestu wdrożenia, zamiast ręcznego tworzenia dokumentu JSON.  Ma trzy kroki: **Dodaj moduły,** **Określ trasy**i **Przejrzyj wdrożenie**.

### <a name="add-modules"></a>Dodaj moduły

1. W sekcji **Moduły wdrażania** wybierz pozycję **Dodaj**
1. Z typów modułów z listy rozwijanej wybierz opcję **Moduł krawędzi IoT**
1. Podaj nazwę, obraz, opcje tworzenia kontenera:

[!INCLUDE [event-grid-edge-module-version-update](event-grid-edge-module-version-update.md)]

   * **Nazwa**: eventgridmodule
   * **Identyfikator URI obrazu**:`mcr.microsoft.com/azure-event-grid/iotedge:latest`
   * **Opcje tworzenia kontenera:**

    ```json
        {
          "Env": [
            "inbound__clientAuth:clientCert__enabled=false",
            "outbound__webhook__httpsOnly=false"
          ],
          "HostConfig": {
            "PortBindings": {
              "4438/tcp": [
                {
                  "HostPort": "4438"
                }
              ]
            }
          }
        }
    ```

 1. Kliknij **przycisk Zapisz**
 1. Kliknij **przycisk Dalej,** aby przejść do sekcji Trasy

    > [!NOTE]
    > Jeśli używasz maszyny Wirtualnej platformy Azure jako urządzenia brzegowego, dodaj regułę portu przychodzącego, aby zezwolić na ruch przychodzący na porcie 4438. Aby uzyskać instrukcje dotyczące dodawania reguły, zobacz [Jak otworzyć porty na maszynie Wirtualnej](../articles/virtual-machines/windows/nsg-quickstart-portal.md).


### <a name="setup-routes"></a>Ustawianie tras

 Zachowaj trasy domyślne, a następnie wybierz pozycję **Dalej,** aby przejść do sekcji recenzji

### <a name="review-deployment"></a>Przegląd wdrożenia

1. Sekcja przeglądu zawiera manifest wdrożenia JSON, który został utworzony na podstawie wybranych w poprzednich dwóch sekcjach. Upewnij się, że na liście są widoczne dwa moduły: **$edgeAgent** i **$edgeHub**. Te dwa moduły tworzą środowisko uruchomieniowe usługi IoT Edge i są wymagane domyślne w każdym wdrożeniu.
1. Przejrzyj informacje o wdrożeniu, a następnie wybierz pozycję **Prześlij**.

### <a name="verify-your-deployment"></a>Weryfikowanie wdrożenia

1. Po przesłaniu wdrożenia powrócisz do strony usługi IoT Edge w centrum IoT Hub.
1. Wybierz **urządzenie usługi IoT Edge,** które są przeznaczone dla wdrożenia, aby otworzyć jego szczegóły.
1. W szczegółach urządzenia sprawdź, czy moduł siatki zdarzeń jest wymieniony jako **określony we wdrożeniu** i **zgłoszony przez urządzenie**.

Może upłynąć kilka chwil, aby moduł został uruchomiony na urządzeniu, a następnie zgłoszony z powrotem do usługi IoT Hub. Odśwież stronę, aby wyświetlić zaktualizowany stan.