---
title: Włącz ciągłą Integrację/ciągłe dostarczanie za pomocą rozwiązania jenkins — usługi Azure IoT Edge | Dokumentacja firmy Microsoft
description: Rozszerzenia serwera Jenkins dla usługi Azure IoT Edge umożliwia włączenie zadań rozwój całego i wdrożenia usługi IoT Edge do istniejącego rozwiązania DevOps.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/22/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 016d1c5d389cf1b9e82194e9d273863da1138d2b
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2019
ms.locfileid: "54830375"
---
# <a name="integrate-azure-iot-edge-with-jenkins-pipelines"></a>Integracja usługi Azure IoT Edge z potokach systemu Jenkins

Usługa Azure IoT Edge ma wbudowaną obsługę metodyki DevOps platformy Azure i usługi Azure DevOps Projects, ale również zapewnia wtyczki, aby rozszerzyć funkcje podejścia DevOps do systemu Jenkins. [Jenkins](https://jenkins.io/) to serwer automatyzacji typu open source, korzystającą z wtyczki w celu obsługi dla wielu typów opracowywania i wdrażania projektów, w tym usługi IoT Edge. 

Wtyczka usługi Azure IoT Edge dla serwera Jenkins koncentruje się na ciągłej integracji i ciągłego wdrażania. Można utworzyć potoku kompilacji i wypychania, który kompiluje modułów i wypychanie obrazów kontenera do rejestru kontenerów. Następnie utwórz potok wersji, który wdraża modułów na urządzeniach usługi IoT Edge. 

Przed rozpoczęciem korzystania z wtyczki usługi IoT Edge dla serwera Jenkins, konieczne jest Centrum IoT na platformie Azure i rejestru kontenerów do przechowywania obrazów kontenerów. Aby udzielić uprawnienia współautora usługi Jenkins do usługi IoT hub, aby wtyczka może tworzyć wdrożenia dla usługi IoT Edge urządzenia, należy użyć jednostki usługi platformy Azure. 

Gdy wszystko będzie gotowe rozpocząć pracę, Znajdź instalacji i szczegóły dla [wtyczka usługi Azure IoT Edge dla Jenkinx](https://plugins.jenkins.io/azure-iot-edge).