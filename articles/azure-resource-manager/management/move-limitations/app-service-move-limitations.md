---
title: Przenoszenie zasobów usługi Azure App Service
description: Usługa Azure Resource Manager umożliwia przenoszenie zasobów usługi App Service do nowej grupy zasobów lub subskrypcji.
ms.topic: conceptual
ms.date: 12/13/2019
ms.openlocfilehash: 338b0559a5de9468ff60024b88d0f676a9fc3e8b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75479802"
---
# <a name="move-guidance-for-app-service-resources"></a>Przenoszenie wskazówek dotyczących zasobów usługi App Service

W tym artykule opisano kroki przenoszenia zasobów usługi App Service. Istnieją określone wymagania dotyczące przenoszenia zasobów usługi App Service do nowej subskrypcji.

## <a name="move-across-subscriptions"></a>Przechodzenie między subskrypcjami

Podczas przenoszenia aplikacji sieci Web między subskrypcjami stosuje się następujące wskazówki:

- Docelowa grupa zasobów nie może mieć żadnych istniejących zasobów usługi App Service. Zasoby usługi App Service obejmują:
    - Web Apps
    - Plany usługi App Service
    - Przesłane lub zaimportowane certyfikaty SSL
    - Środowiska usługi App Service
- Wszystkie zasoby usługi App Service w grupie zasobów muszą zostać przeniesione razem. Należy zauważyć, że środowiska usługi app service nie można przenieść do nowej grupy zasobów ani do nowej subskrypcji.
- Certyfikat powiązany z siecią Web można przenieść do sieci Web bez usuwania powiązań SSL, o ile certyfikat jest przenoszony ze wszystkimi innymi zasobami w grupie zasobów.
- Zasoby usługi App Service można przenosić tylko z grupy zasobów, w której zostały pierwotnie utworzone. Jeśli zasób usługi App Service nie znajduje się już w oryginalnej grupie zasobów, przenieś go z powrotem do pierwotnej grupy zasobów. Następnie przenieść zasób między subskrypcjami.

Jeśli nie pamiętasz oryginalnej grupy zasobów, możesz ją znaleźć za pomocą diagnostyki. W przypadku aplikacji internetowej wybierz pozycję **Diagnozuj i rozwiązuj problemy**. Następnie wybierz **opcję Konfiguracja i zarządzanie**.

![Wybierz diagnostykę](./media/app-service-move-limitations/select-diagnostics.png)

Wybierz **pozycję Opcje migracji**.

![Wybieranie opcji migracji](./media/app-service-move-limitations/select-migration.png)

Wybierz opcję zalecanych kroków, aby przenieść aplikację internetową.

![Wybieranie zalecanych kroków](./media/app-service-move-limitations/recommended-steps.png)

Przed przeniesieniem zasobów zostaną wyświetlona zalecane czynności. Informacje obejmują oryginalną grupę zasobów dla aplikacji sieci web.

![Zalecenia](./media/app-service-move-limitations/recommendations.png)

## <a name="move-support"></a>Obsługa przenoszenia

Aby ustalić, które zasoby usługi App Service można przenieść, zobacz przenieś stan pomocy technicznej dla:

- [Usługa Microsoft.AppService](../move-support-resources.md#microsoftappservice)
- [Rejestracja certyfikatów firmy Microsoft.Certificate](../move-support-resources.md#microsoftcertificateregistration)
- [Microsoft.DomainRegistration](../move-support-resources.md#microsoftdomainregistration)
- [Microsoft.Web](../move-support-resources.md#microsoftweb)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać polecenia przenoszenia zasobów, zobacz [Przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](../move-resource-group-and-subscription.md).
