---
title: Przenoszenie zasobów usługi Azure App Service do nowej subskrypcji lub grupy zasobów
description: Usługa Azure Resource Manager, aby przenieść zasoby usługi App Service do nowej grupy zasobów lub subskrypcji.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: tomfitz
ms.openlocfilehash: c1a09ff4c29a2fedfea2c165a95c042985b3c83a
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2019
ms.locfileid: "67723573"
---
# <a name="move-guidance-for-app-service-resources"></a>Przenieś wskazówki dotyczące zasobów usługi App Service

Kroki umożliwiające przeniesienie różnią się zasoby usługi App Service na podstawie tego, czy przenosisz zasoby w ramach subskrypcji lub do nowej subskrypcji.

## <a name="move-in-same-subscription"></a>Przenieś w tej samej subskrypcji

Podczas przenoszenia aplikacji sieci Web _w ramach tej samej subskrypcji_, nie można przenieść certyfikaty SSL innej firmy. Aplikacja sieci Web można przenieść do nowej grupy zasobów, bez konieczności przenoszenia jego certyfikatów innych firm i funkcje protokołu SSL aplikacji nadal działa.

Jeśli chcesz przenieść certyfikatu SSL z aplikacją sieci Web, wykonaj następujące czynności:

1. Usuwanie certyfikatu innej firmy z aplikacji sieci Web, ale zachować kopię certyfikatu
2. Przenoszenie aplikacji sieci Web.
3. Przekaż certyfikat innej firmy do przeniesionych aplikacji sieci Web.

## <a name="move-across-subscriptions"></a>Przenoszenie między subskrypcjami

Podczas przenoszenia aplikacji sieci Web _w subskrypcjach_, obowiązują następujące ograniczenia:

- Docelowa grupa zasobów nie może mieć żadnych istniejących zasobów usługi App Service. Zasoby usługi App Service obejmują:
    - Web Apps
    - Plany usługi App Service
    - Przekazane lub zaimportowane certyfikaty SSL
    - Środowiska usługi App Service
- Wszystkie zasoby usługi App Service w grupie zasobów, muszą zostać przeniesione razem.
- Zasoby usługi App Service mogą być przenoszone z grupy zasobów, w której zostały pierwotnie utworzone. Jeśli zasób usługi App Service nie jest już w jego oryginalnej grupie zasobów, przenieś go ponownie do jego oryginalnej grupy zasobów. Następnie przenieś zasób w subskrypcjach.

Jeśli nie pamiętasz oryginalnej grupy zasobów, mogą ją odnaleźć za pomocą diagnostyki. Dla aplikacji sieci web wybierz **diagnozowanie i rozwiązywanie problemów**. Następnie wybierz **Konfiguracja i zarządzanie nim**.

![Wybierz pozycję Diagnostyka](./media/app-service-move-limitations/select-diagnostics.png)

Wybierz **opcje migracji**.

![Wybierz opcje migracji](./media/app-service-move-limitations/select-migration.png)

Wybierz opcję, aby uzyskać zalecane kroki, aby przenieść aplikację sieci web.

![Wybierz zalecane czynności](./media/app-service-move-limitations/recommended-steps.png)

Zostanie wyświetlony zalecane akcje, które należy wykonać przed przenoszenia zasobów. Informacje dotyczące oryginalnej grupy zasobów dla aplikacji sieci web.

![Zalecenia](./media/app-service-move-limitations/recommendations.png)

## <a name="move-app-service-certificate"></a>Przenieś certyfikatu usługi App Service

Certyfikatu usługi App Service można przenieść do nowej grupy zasobów lub subskrypcji. Jeśli certyfikatu usługi App Service jest powiązana z aplikacją internetową, należy wykonać pewne kroki przed przeniesieniem zasobów do nowej subskrypcji. Usuwanie powiązania SSL i certyfikatu prywatnego aplikacji sieci web przed przejściem do zasobów. Certyfikat usługi App Service nie ma konieczności zostać usunięte, po prostu certyfikatu prywatnego aplikacji sieci web.

## <a name="move-support"></a>Obsługa przenoszenia

Aby określić, które zasoby usługi App Service można przenosić, zobacz przenieść stan pomocy technicznej:

- [Microsoft.AppService](../move-support-resources.md#microsoftappservice)
- [Microsoft.CertificateRegistration](../move-support-resources.md#microsoftcertificateregistration)
- [Microsoft.DomainRegistration](../move-support-resources.md#microsoftdomainregistration)
- [Microsoft.Web](../move-support-resources.md#microsoftweb)

## <a name="next-steps"></a>Następne kroki

Poleceń przenieść zasoby można znaleźć [przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](../resource-group-move-resources.md).
