---
title: Rozwiązania do zdalnego monitorowania importowania pakietu Edge — Azure | Dokumentacja firmy Microsoft
description: W tym artykule opisano jak zaimportować pakiet IoT Edge do akceleratora rozwiązania monitorowania zdalnego
author: dominicbetts
manager: timlt
ms.author: v-yiso
ms.service: iot-accelerators
services: iot-accelerators
origin.date: 10/10/2018
ms.date: 12/17/2018
ms.topic: conceptual
ms.openlocfilehash: 34222f396ed3c43932371aa9f64a459bb2a5dd0e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61443015"
---
# <a name="import-an-iot-edge-package-into-your-remote-monitoring-solution-accelerator"></a>Importowanie pakietu IoT Edge w akceleratorze rozwiązanie monitorowania zdalnego

Manifest wdrażania określa moduły do wdrożenia na urządzeniu usługi IoT Edge. W tym artykule przyjęto założenie, że deweloper w Twojej organizacji został już utworzony manifest wdrożenia. Aby dowiedzieć się, jak deweloper tworzy manifestu, zobacz jeden z następujących artykułów z instrukcjami usługi IoT Edge:

- [Wdrożyć moduły usługi Azure IoT Edge w witrynie Azure portal](../iot-edge/how-to-deploy-modules-portal.md)
- [Wdrożyć moduły usługi Azure IoT Edge przy użyciu wiersza polecenia platformy Azure](../iot-edge/how-to-deploy-modules-cli.md)
- [Wdrażanie usługi Azure IoT Edge modułów z programu Visual Studio Code](../iot-edge/how-to-deploy-modules-vscode.md)

Deweloper tworzy i testuje manifest wdrożenia w środowisku programistycznym. Gdy wszystko będzie gotowe, możesz zaimportować manifest w akceleratorze rozwiązanie monitorowania zdalnego.

## <a name="export-a-manifest"></a>Eksportuj manifestu

Eksportowanie pliku manifestu wdrożenia ze środowiska programowania za pomocą witryny Azure portal:

1. W witrynie Azure portal przejdź do Centrum IoT hub, używane do tworzenia i testowania na urządzeniach usługi IoT Edge. Kliknij przycisk **usługi IoT Edge** i następnie **wdrożenia usługi IoT Edge**: ![IoT Edge](media/iot-accelerators-remote-monitoring-import-edge-package/iotedge.png)

1. Kliknij wdrożenie konfiguracji wdrożenia, którego chcesz użyć. **Szczegóły wdrożenia** spowoduje wyświetlenie strony: ![Szczegóły wdrożenia usługi IoT Edge](media/iot-accelerators-remote-monitoring-import-edge-package/deploymentdetails.png)

1. Kliknij przycisk **manifestu usługi IoT Edge Pobierz**:  ![Pobierz manifest wdrażania](media/iot-accelerators-remote-monitoring-import-edge-package/download.png)

1. Zapisz plik JSON jako plik lokalny o nazwie **deploymentmanifest.json**.

Masz teraz plik, który zawiera manifest wdrożenia. W następnej sekcji można zaimportować tego manifestu w pakiecie do rozwiązania do zdalnego monitorowania.

## <a name="import-a-package"></a>Importowanie pakietu

Wykonaj poniższe kroki, aby zaimportować manifest wdrożenia Edge jako pakiet do rozwiązania:

1. Przejdź do **pakietów** strony w internetowym interfejsie użytkownika zdalnego monitorowania:  ![Strona pakietów](media/iot-accelerators-remote-monitoring-import-edge-package/packagespage.png)

1. Kliknij przycisk **+ nowy pakiet**, wybierz **manifestu krawędzi** typ pakietu, a następnie kliknij przycisk **Przeglądaj** wybrać **deploymentmanifest.json** pliku został zapisany w poprzedniej sekcji:  ![Wybierz manifestu](media/iot-accelerators-remote-monitoring-import-edge-package/selectmanifest.png)

1. Kliknij przycisk **przekazywanie** Dodaj pakiet do rozwiązania monitorowania zdalnego:  ![Przekazany pakiet.](media/iot-accelerators-remote-monitoring-import-edge-package/uploadedpackage.png)

Teraz gdy już przesłano manifest wdrożenia usługi IoT Edge jako pakiet. Na **wdrożeń** strony, można wdrożyć tego pakietu do połączonych urządzeń usługi IoT Edge.

## <a name="next-steps"></a>Kolejne kroki

Teraz, gdy wiesz jak wdrażać moduły na urządzeniu usługi IoT Edge z rozwiązania do zdalnego monitorowania, następnym krokiem jest więcej informacji na temat [usługi IoT Edge](../iot-edge/about-iot-edge.md).
