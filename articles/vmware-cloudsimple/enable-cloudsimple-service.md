---
title: Włączanie rozwiązania programu VMware na platformie Azure przez usługę CloudSimple
description: Opisano, jak włączyć usługę CloudSimple subskrypcji platformy Azure, a następnie zarejestruj dostawcę zasobów CLoudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/04/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 2553aa95d5028c510b4e1a1b7f51a9f410bcea51
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/17/2019
ms.locfileid: "67154854"
---
# <a name="register-the-microsoftvmwarecloudsimple-resource-provider-on-your-azure-subscription"></a>Zarejestruj dostawcę zasobów Microsoft.VMwareCloudSimple w subskrypcji platformy Azure

Usługa CloudSimple umożliwia korzystanie z platformy Azure VMware Solution by CloudSimple. Należy zarejestrować usługę Microsoft.VMwareCloudSimple, jako dostawcy zasobów.

## <a name="register-the-resource-provider"></a>Rejestrowanie dostawcy zasobów

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. Wybierz pozycję **Wszystkie usługi**.

3. Wyszukaj i wybierz pozycję **subskrypcje**.

    ![Wybierz subskrypcje](media/cloudsimple-service-select-subscriptions.png)

4. Wybierz subskrypcję, dla którego chcesz włączyć usługę CloudSimple.

5. Kliknij przycisk **dostawców zasobów** dla subskrypcji.

6. Użyj **Microsoft.VMwareCloudSimple** do filtrowania dostawcy zasobów.

7. Wybierz dostawcę zasobów, a następnie kliknij przycisk **zarejestrować**.

    ![Rejestrowanie dostawcy zasobów](media/cloudsimple-service-enable-resource-provider.png)

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się, jak [utworzyć usługę CloudSimple](create-cloudsimple-service.md)
* Dowiedz się, jak [konfigurowania środowiska chmury prywatnej](quickstart-create-private-cloud.md)
