---
title: (PRZESTARZAŁE) Pule agentów dc/os dla usługi kontenera Azure
description: Jak bufory agentów publicznych i prywatnych współpracują z klastrem kontrolera domeny/systemu operacyjnego usługi kontenerowej azure
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 01/04/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: bb9b33bf537ebd5a563f8e8a8afd45cd2e5b292d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76278382"
---
# <a name="deprecated-dcos-agent-pools-for-azure-container-service"></a>(PRZESTARZAŁE) Pule agentów dc/os dla usługi kontenera Azure

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Klastry kontrolera domeny/systemu operacyjnego w usłudze Azure Container Service zawierają węzły agenta w dwóch pulach, puli publicznej i puli prywatnej. Aplikację można wdrożyć w obu pulach, co wpływa na dostępność między komputerami w usłudze kontenera. Maszyny mogą być narażone na działanie Internetu (publicznego) lub przechowywane w wewnętrznej (prywatnej). W tym artykule przedstawiono krótki przegląd powodów, dla których istnieją publiczne i prywatne baseny.


* **Agenci prywatni:** Węzły agenta prywatnego są uruchamiane za pośrednictwem sieci nierutowalnej. Ta sieć jest dostępna tylko ze strefy administracyjnej lub za pośrednictwem routera brzegowego strefy publicznej. Domyślnie dc/os uruchamia aplikacje w węzłach agenta prywatnego. 

* **Agenci publiczni:** węzły agentów publicznych uruchamiają aplikacje i usługi DC/OS za pośrednictwem publicznie dostępnej sieci. 

Aby uzyskać więcej informacji na temat zabezpieczeń sieci DC/OS, zobacz [dokumentację kontrolera domeny/systemu operacyjnego](https://docs.mesosphere.com/).

## <a name="deploy-agent-pools"></a>Wdrażanie pul agentów

Pule agentów kontrolera domeny/systemu operacyjnego w usłudze kontenera platformy Azure są tworzone w następujący sposób:

* **Pula prywatna** zawiera liczbę węzłów agenta, które można określić podczas [wdrażania klastra kontrolera domeny/systemu operacyjnego](container-service-deployment.md). 

* **Pula publiczna** początkowo zawiera wstępnie określoną liczbę węzłów agenta. Ta pula jest dodawana automatycznie po zainicjowaniu obsługi administracyjnej klastra kontrolera domeny/systemu operacyjnego.

Pula prywatna i pula publiczna są zestawami skalowania maszyny wirtualnej platformy Azure. Można zmienić rozmiar tych pul po wdrożeniu.

## <a name="use-agent-pools"></a>Korzystanie z pul agentów
Domyślnie **Marathon** wdraża dowolną nową aplikację w węzłach agenta *prywatnego.* Należy jawnie wdrożyć aplikację do węzłów *publicznych* podczas tworzenia aplikacji. Wybierz kartę **Opcjonalne** i wprowadź **slave_public** dla wartości **Akceptowane role zasobów.** Ten proces jest udokumentowany [tutaj](container-service-mesos-marathon-ui.md#deploy-a-docker-formatted-container) i w dokumentacji [DC/OS.](https://docs.mesosphere.com/1.7/administration/installing/oss/custom/create-public-agent/)

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o [zarządzaniu kontenerami DC/OS](container-service-mesos-marathon-ui.md).

* Dowiedz się, jak [otworzyć zaporę dostarczoną](container-service-enable-public-access.md) przez platformę Azure, aby umożliwić publiczny dostęp do kontenerów kontrolera domeny/systemu operacyjnego.

