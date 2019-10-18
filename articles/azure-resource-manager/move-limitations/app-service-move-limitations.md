---
title: Przenoszenie Azure App Service zasobów
description: Użyj Azure Resource Manager, aby przenieść zasoby App Service do nowej grupy zasobów lub subskrypcji.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: tomfitz
ms.openlocfilehash: 04ddf644c58434531dde708ee3b6432b1fce8f91
ms.sourcegitcommit: 6eecb9a71f8d69851bc962e2751971fccf29557f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2019
ms.locfileid: "72533513"
---
# <a name="move-guidance-for-app-service-resources"></a>Wskazówki dotyczące przenoszenia dla App Service zasobów

Kroki służące do przenoszenia zasobów App Service różnią się w zależności od tego, czy przenosisz zasoby w ramach subskrypcji, czy na nową subskrypcję.

## <a name="move-in-same-subscription"></a>Przenieś w ramach tej samej subskrypcji

Podczas przenoszenia aplikacji sieci Web _w ramach tej samej subskrypcji_nie można przenieść certyfikatów SSL innych firm. Można jednak przenieść aplikację sieci Web do nowej grupy zasobów bez przenoszenia jej certyfikatu innej firmy, a funkcja SSL aplikacji nadal działa.

Jeśli chcesz przenieść certyfikat SSL przy użyciu aplikacji sieci Web, wykonaj następujące kroki:

1. Usuń certyfikat innej firmy z aplikacji sieci Web, ale zachowaj kopię certyfikatu
2. Przenieś aplikację sieci Web.
3. Przekaż certyfikat innej firmy do przeniesionej aplikacji sieci Web.

## <a name="move-across-subscriptions"></a>Przechodzenie między subskrypcjami

Podczas przemieszczania aplikacji sieci Web _w ramach subskrypcji_obowiązują następujące ograniczenia:

- Docelowa Grupa zasobów nie może mieć żadnych istniejących zasobów App Service. App Service zasoby obejmują:
    - Aplikacje internetowe
    - Plany usługi App Service
    - Przekazane lub zaimportowane certyfikaty SSL
    - Środowiska usługi App Service
- Wszystkie zasoby App Service w grupie zasobów należy przenieść razem.
- Zasoby App Service można przenosić tylko z grupy zasobów, w której zostały pierwotnie utworzone. Jeśli zasób App Service nie jest już w oryginalnej grupie zasobów, przenieś go z powrotem do jego oryginalnej grupy zasobów. Następnie przenieś zasób między subskrypcjami.

Jeśli nie pamiętasz oryginalnej grupy zasobów, możesz ją znaleźć za pomocą diagnostyki. W przypadku aplikacji sieci Web wybierz opcję **Diagnozuj i rozwiąż problemy**. Następnie wybierz pozycję **Konfiguracja i zarządzanie**.

![Wybierz diagnostykę](./media/app-service-move-limitations/select-diagnostics.png)

Wybierz **Opcje migracji**.

![Wybieranie opcji migracji](./media/app-service-move-limitations/select-migration.png)

Wybierz opcję zalecanych kroków, aby przenieść aplikację sieci Web.

![Wybierz zalecane kroki](./media/app-service-move-limitations/recommended-steps.png)

Przed przeniesieniem zasobów zobaczysz zalecane czynności do wykonania. Informacje obejmują oryginalną grupę zasobów dla aplikacji sieci Web.

![Polecane elementy](./media/app-service-move-limitations/recommendations.png)

## <a name="move-app-service-certificate"></a>Przenieś Certyfikat usługi App Service

Możesz przenieść Certyfikat usługi App Service do nowej grupy zasobów lub subskrypcji. Jeśli Certyfikat usługi App Service jest powiązany z aplikacją sieci Web, przed przeniesieniem zasobów do nowej subskrypcji należy wykonać kilka czynności. Przed przeniesieniem zasobów Usuń powiązanie SSL i certyfikat prywatny z aplikacji sieci Web. Nie trzeba usuwać Certyfikat usługi App Service, tylko certyfikatu prywatnego w aplikacji sieci Web.

## <a name="move-support"></a>Obsługa przenoszenia

Aby określić, które zasoby App Service można przenieść, zobacz Przenoszenie stanu wsparcia dla:

- [Microsoft. AppService](../move-support-resources.md#microsoftappservice)
- [Microsoft. CertificateRegistration](../move-support-resources.md#microsoftcertificateregistration)
- [Microsoft. DomainRegistration](../move-support-resources.md#microsoftdomainregistration)
- [Microsoft. Web](../move-support-resources.md#microsoftweb)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać polecenia przenoszenia zasobów, zobacz [przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](../resource-group-move-resources.md).
