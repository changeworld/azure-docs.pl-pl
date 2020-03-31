---
title: Włącz integrację/dysk CD z wtyczką Jenkins — Usługa Azure IoT Edge | Dokumenty firmy Microsoft
description: Rozszerzenie usługi Azure IoT Edge dla usługi Jenkins umożliwia integrację zadań deweloperskich i wdrożeniowych usługi IoT Edge z istniejącym rozwiązaniem DevOps.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/22/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 75ed5f5eda48f10776b854ce4de4d2f855a53634
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76510247"
---
# <a name="integrate-azure-iot-edge-with-jenkins-pipelines"></a>Integracja usługi Azure IoT Edge z potokami usługi Jenkins

Usługa Azure IoT Edge ma wbudowaną obsługę projektów Azure DevOps i Azure DevOps, ale także zawiera wtyczkę, aby rozwinąć funkcjonalność DevOps do usługi Jenkins. [Jenkins](https://jenkins.io/) to serwer automatyzacji typu open source, który używa wtyczek do obsługi wielu typów projektów deweloperskich i wdrożeniowych, w tym usługi IoT Edge.

Wtyczka usługi Azure IoT Edge dla usługi Jenkins koncentruje się na ciągłej integracji i ciągłego wdrażania. Można utworzyć potok kompilacji i wypychania, który tworzy moduły i wypycha ich obrazy kontenerów do rejestru kontenerów. Następnie utwórz potok wersji, który wdraża moduły na urządzeniach usługi IoT Edge.

Przed rozpoczęciem korzystania z wtyczki usługi IoT Edge dla usługi Jenkins, potrzebujesz centrum IoT na platformie Azure i rejestru kontenerów do przechowywania obrazów kontenerów. Użyj jednostki usługi Azure, aby przyznać uprawnienia współautora usługi Jenkins do centrum IoT hub, aby wtyczka mogła tworzyć wdrożenia dla urządzeń usługi IoT Edge.

Jeśli jesteś gotowy do rozpoczęcia, znajdź instalację i użyj szczegółów [wtyczki usługi Azure IoT Edge dla usługi Jenkins.](https://plugins.jenkins.io/azure-iot-edge)
