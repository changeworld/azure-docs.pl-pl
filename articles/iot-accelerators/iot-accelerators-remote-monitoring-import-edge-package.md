---
title: Pakiet usługi zdalnego monitorowania zaimportowania usługi Edge — Azure | Dokumenty firmy Microsoft
description: W tym artykule opisano sposób importowania pakietu usługi IoT Edge do akceleratora rozwiązań do zdalnego monitorowania
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/10/2018
ms.topic: conceptual
ms.openlocfilehash: 34222f396ed3c43932371aa9f64a459bb2a5dd0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "61443015"
---
# <a name="import-an-iot-edge-package-into-your-remote-monitoring-solution-accelerator"></a>Importowanie pakietu usługi IoT Edge do akceleratora rozwiązania do monitorowania zdalnego

Manifest wdrażania definiuje moduły do wdrożenia na urządzeniu usługi IoT Edge. W tym artykule założono, że deweloper w organizacji utworzył już manifest wdrożenia. Aby dowiedzieć się, jak deweloper tworzy manifest, zobacz jeden z następujących artykułów insytów IoT Edge:

- [Wdrażanie modułów usługi Azure IoT Edge z witryny Azure portal](../iot-edge/how-to-deploy-modules-portal.md)
- [Wdrażanie modułów usługi Azure IoT Edge za pomocą interfejsu wiersza polecenia platformy Azure](../iot-edge/how-to-deploy-modules-cli.md)
- [Wdrażanie modułów usługi Azure IoT Edge z kodu programu Visual Studio](../iot-edge/how-to-deploy-modules-vscode.md)

Deweloper tworzy i testuje manifest wdrażania w środowisku deweloperskim. Gdy będziesz gotowy, możesz zaimportować manifest do akceleratora rozwiązania zdalnego monitorowania.

## <a name="export-a-manifest"></a>Eksportowanie manifestu

Użyj witryny Azure portal do eksportowania manifestu wdrażania ze środowiska deweloperskiego:

1. W witrynie Azure portal przejdź do centrum IoT hub, którego używasz do tworzenia i testowania urządzeń usługi IoT Edge. Kliknij pozycję **IoT Edge,** a następnie **wdrożenia usługi IoT Edge**: ![IoT Edge](media/iot-accelerators-remote-monitoring-import-edge-package/iotedge.png)

1. Kliknij wdrożenie, które ma konfigurację wdrożenia, której chcesz użyć. Zostanie wyświetlona strona ![ **Szczegóły wdrożenia:** Szczegóły wdrożenia usługi IoT Edge](media/iot-accelerators-remote-monitoring-import-edge-package/deploymentdetails.png)

1. Kliknij **pozycję Pobierz manifest usługi IoT Edge**: ![Pobierz manifest wdrożenia](media/iot-accelerators-remote-monitoring-import-edge-package/download.png)

1. Zapisz plik JSON jako plik lokalny o nazwie **deploymentmanifest.json**.

Teraz masz plik, który zawiera manifest wdrożenia. W następnej sekcji zaimportujesz ten manifest jako pakiet do rozwiązania zdalnego monitorowania.

## <a name="import-a-package"></a>Importowanie pakietu

Wykonaj poniższe czynności, aby zaimportować manifest wdrożenia usługi Edge jako pakiet do rozwiązania:

1. Przejdź do strony **Pakiety** na stronie ![Interfejs użytkownika sieci Web zdalnego monitorowania: pakiety](media/iot-accelerators-remote-monitoring-import-edge-package/packagespage.png)

1. Kliknij **+ Nowy pakiet**, wybierz manifest **krawędzi** jako typ pakietu, a następnie kliknij przycisk **Przeglądaj,** ![aby wybrać plik **deploymentmanifest.json** zapisany w poprzedniej sekcji: Wybierz manifest](media/iot-accelerators-remote-monitoring-import-edge-package/selectmanifest.png)

1. Kliknij **przycisk Przekaż,** aby dodać pakiet ![do rozwiązania do zdalnego monitorowania: Przekazany pakiet](media/iot-accelerators-remote-monitoring-import-edge-package/uploadedpackage.png)

Teraz przekażesz manifest wdrożenia usługi IoT Edge jako pakiet. Na stronie **Wdrożenia** można wdrożyć ten pakiet na podłączonych urządzeniach usługi IoT Edge.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy dowiesz się, jak wdrożyć moduły na urządzeniu Usługi IoT Edge z rozwiązania do zdalnego monitorowania, następnym krokiem jest dowiedzenie się więcej o [Udręki IoT.](../iot-edge/about-iot-edge.md)
