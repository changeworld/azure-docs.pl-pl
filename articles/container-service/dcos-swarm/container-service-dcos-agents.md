---
title: Pule agentów platformy DC/OS usługi kontenera platformy Azure
description: Jak działają pul agentów publicznych i prywatnych przy użyciu klastra usługi Azure Container Service DC/OS
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 01/04/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 17029f51be9fed8fc36c5f919ece84acbf0461d9
ms.sourcegitcommit: a2ae233e20e670e2f9e6b75e83253bd301f5067c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/13/2018
ms.locfileid: "42061489"
---
# <a name="dcos-agent-pools-for-azure-container-service"></a>Pule agentów platformy DC/OS usługi kontenera platformy Azure
Klastry DC/OS w usłudze Azure Container Service zawiera węzły agenta w dwie pule, pulę publicznych i prywatnych puli. Aplikację można wdrożyć do jednej puli wpływu na dostępność między komputerami w usłudze kontenera. Maszyny można połączenie z Internetem (publicznego) lub przechowywane wewnętrzny (prywatny). Ten artykuł zawiera krótkie omówienie, dlatego istnieją pule prywatnych i publicznych.


* **Agenci prywatni**: węzły agenta prywatnego uruchamiać za pośrednictwem sieci bez obsługi routingu. Ta sieć jest dostępny tylko w przypadku, ze strefy administratora lub za pośrednictwem routera brzegowego strefy publicznej. Domyślnie DC/OS spowoduje uruchomienie aplikacji w węzłach agenta prywatnego. 

* **Agentów publicznych**: węzły agenta publicznego uruchamiać aplikacje platformy DC/OS i usługi za pomocą publicznie dostępnej sieci. 

Aby uzyskać więcej informacji na temat zabezpieczeń sieciowych DC/OS, zobacz [dokumentacji rozwiązania DC/OS](https://dcos.io/docs/1.8/administration/securing-your-cluster/).

## <a name="deploy-agent-pools"></a>Pule agentów wdrażania

Pule agentów platformy DC/OS w usłudze Azure Container Service są tworzone w następujący sposób:

* **Prywatnej puli** zawiera liczbę węzłów agenta, możesz określić, kiedy użytkownik [wdrażanie klastra DC/OS](container-service-deployment.md). 

* **Puli publicznych** początkowo zawiera wstępnie określoną liczbę węzłów agenta. Ta pula zostanie dodana automatycznie po zainicjowaniu obsługi klastra DC/OS.

Pula prywatnych i publicznych puli są zestawy skalowania maszyn wirtualnych platformy Azure. Po wdrożeniu, można zmienić rozmiar tych pul.

## <a name="use-agent-pools"></a>Pule agenta
Domyślnie **Marathon** służy do wdrażania dowolnej nowej aplikacji do *prywatnej* węzłów agentów. Musisz jawnie wdrożenia aplikacji na *publicznych* węzły podczas tworzenia aplikacji. Wybierz **opcjonalnie** kartę, a następnie wprowadź **slave_public** dla **zaakceptowane role zasobów** wartość. Ten proces jest udokumentowany [tutaj](container-service-mesos-marathon-ui.md#deploy-a-docker-formatted-container) i [DC/OS](https://dcos.io/docs/1.7/administration/installing/custom/create-public-agent/) dokumentacji.

## <a name="next-steps"></a>Kolejne kroki
* Przeczytaj więcej na temat [Zarządzanie kontenerów DC/OS](container-service-mesos-marathon-ui.md).

* Dowiedz się, jak [otwierająca zaporę](container-service-enable-public-access.md) udostępnianych przez platformę Azure, aby zezwolić na publiczny dostęp do kontenerów DC/OS.

