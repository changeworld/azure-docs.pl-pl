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
ms.openlocfilehash: cacd5510147ce997efec922f4b4656956a098d88
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66676941"
---
# <a name="register-the-microsoftvmwarecloudsimple-resource-provider-on-your-azure-subscription"></a>Zarejestruj dostawcę zasobów Microsoft.VMwareCloudSimple w subskrypcji platformy Azure

Usługa CloudSimple umożliwia korzystanie z platformy Azure VMware Solution by CloudSimple. Aby korzystać z niej CloudSimple go należy najpierw włączyć w Twojej subskrypcji platformy Azure. Następnie można zarejestrować usługi Microsoft.VMwareCloudSimple jako dostawcy zasobów.

## <a name="enable-the-cloudsimple-service"></a>Włącz usługę CloudSimple

Aby włączyć usługę CloudSimple w Twojej subskrypcji platformy Azure, otwórz żądanie obsługi z [pomocy technicznej firmy Microsoft](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). Wybierz następujące opcje podczas przesyłania żądania.

* Typ problemu: **Technical Preview**
* Subskrypcja: **Identyfikator subskrypcji**
* Typ usługi: **VMware Solution by CloudSimple**
* Typ problemu: **Dedykowane węzły limitu przydziału**
* Podtyp problemu: **Zwiększ limit przydziału wynoszący węzły dedykowane**
* Temat: **Włącz usługę CloudSimple**

Możesz również skontaktować się ze swoim przedstawicielem firmy Microsoft w [ azurevmwaresales@microsoft.com ](mailto:azurevmwaresales@microsoft.com). Prześlij identyfikator swojej subskrypcji platformy Azure w taki sposób, w wiadomości e-mail.  

Po włączeniu usługi CloudSimple dla Twojej subskrypcji można włączyć dostawcy zasobów w ramach subskrypcji.

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