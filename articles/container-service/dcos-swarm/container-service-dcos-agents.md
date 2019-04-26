---
title: (PRZESTARZAŁE) Pule agentów platformy DC/OS usługi kontenera platformy Azure
description: Jak działają pul agentów publicznych i prywatnych przy użyciu klastra usługi Azure Container Service DC/OS
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 01/04/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 03cacda1aa405cb2d0ded579c8ddb5f6011ce3bb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60478450"
---
# <a name="deprecated-dcos-agent-pools-for-azure-container-service"></a>(PRZESTARZAŁE) Pule agentów platformy DC/OS usługi kontenera platformy Azure

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Klastry DC/OS w usłudze Azure Container Service zawiera węzły agenta w dwie pule, pulę publicznych i prywatnych puli. Aplikację można wdrożyć do jednej puli wpływu na dostępność między komputerami w usłudze kontenera. Maszyny można połączenie z Internetem (publicznego) lub przechowywane wewnętrzny (prywatny). Ten artykuł zawiera krótkie omówienie, dlatego istnieją pule prywatnych i publicznych.


* **Agenci prywatni**: Węzły agenta prywatnego uruchamiać za pośrednictwem sieci bez obsługi routingu. Ta sieć jest dostępny tylko w przypadku, ze strefy administratora lub za pośrednictwem routera brzegowego strefy publicznej. Domyślnie DC/OS spowoduje uruchomienie aplikacji w węzłach agenta prywatnego. 

* **Agentów publicznych**: Uruchamianie aplikacji platformy DC/OS i usługi za pośrednictwem publicznie dostępnej sieci węzły agenta publicznego. 

Aby uzyskać więcej informacji na temat zabezpieczeń sieciowych DC/OS, zobacz [dokumentacji rozwiązania DC/OS](https://docs.mesosphere.com/).

## <a name="deploy-agent-pools"></a>Pule agentów wdrażania

Pule agentów platformy DC/OS w usłudze Azure Container Service są tworzone w następujący sposób:

* **Prywatnej puli** zawiera liczbę węzłów agenta, możesz określić, kiedy użytkownik [wdrażanie klastra DC/OS](container-service-deployment.md). 

* **Puli publicznych** początkowo zawiera wstępnie określoną liczbę węzłów agenta. Ta pula zostanie dodana automatycznie po zainicjowaniu obsługi klastra DC/OS.

Pula prywatnych i publicznych puli są zestawy skalowania maszyn wirtualnych platformy Azure. Po wdrożeniu, można zmienić rozmiar tych pul.

## <a name="use-agent-pools"></a>Pule agenta
Domyślnie **Marathon** służy do wdrażania dowolnej nowej aplikacji do *prywatnej* węzłów agentów. Musisz jawnie wdrożenia aplikacji na *publicznych* węzły podczas tworzenia aplikacji. Wybierz **opcjonalnie** kartę, a następnie wprowadź **slave_public** dla **zaakceptowane role zasobów** wartość. Ten proces jest udokumentowany [tutaj](container-service-mesos-marathon-ui.md#deploy-a-docker-formatted-container) i [DC/OS](https://docs.mesosphere.com/1.7/administration/installing/oss/custom/create-public-agent/) dokumentacji.

## <a name="next-steps"></a>Kolejne kroki
* Przeczytaj więcej na temat [Zarządzanie kontenerów DC/OS](container-service-mesos-marathon-ui.md).

* Dowiedz się, jak [otwierająca zaporę](container-service-enable-public-access.md) udostępnianych przez platformę Azure, aby zezwolić na publiczny dostęp do kontenerów DC/OS.

