---
title: Wdrażanie usługi Azure API Management na wiele regionów platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wdrożyć wystąpienia usługi Azure API Management na wiele regionów platformy Azure.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/30/2017
ms.author: apimpm
ms.openlocfilehash: ff0101bde54f99f99461d0f042af520b1642d0df
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2018
---
# <a name="how-to-deploy-an-azure-api-management-service-instance-to-multiple-azure-regions"></a>Jak wdrożyć wystąpienia usługi Azure API Management na wiele regionów platformy Azure
Zarządzanie interfejsami API obsługuje wdrażanie w przypadku, dzięki czemu wydawcy interfejsu API rozpowszechniają jednej usługi interfejsu API zarządzania dowolną liczbę żądaną regiony platformy Azure. Pozwala to zmniejszyć żądania opóźnienia postrzegane przez rozproszone geograficznie konsumentów interfejsu API i zwiększa również dostępność usługi, jeśli jeden region przejdzie do trybu offline. 

Podczas tworzenia usługi Zarządzanie interfejsami API w początkowo zawiera tylko jeden [jednostki] [ unit] znajdują się w pojedynczym regionie Azure, który jest wybrany jako regionu podstawowego. Dodatkowe regiony można łatwo dodawać za pośrednictwem portalu Azure. Zarządzanie interfejsami API serwer bramy jest wdrażany do każdego regionu i ruchu połączenia będą kierowane do najbliższego bramy. Jeśli region przejdzie do trybu offline, ruch jest automatycznie skierowana do następnego najbliższego bramy. 

> [!IMPORTANT]
> W przypadku wdrażania jest dostępna tylko w **[Premium] [ Premium]** warstwy.
> 
> 

## <a name="add-region"> </a>Wdrażanie wystąpienia usługi API Management do nowego regionu
> [!NOTE]
> Jeśli jeszcze nie utworzono wystąpienie usługi API Management, zobacz [Utwórz wystąpienie usługi Zarządzanie interfejsami API][Create an API Management service instance].
> 
> 

W portalu Azure przejdź do **skali i cenach** strony wystąpienia usługi Zarządzanie interfejsami API. 

![Kartę Skala][api-management-scale-service]

Aby wdrożyć nowy region, kliknij polecenie **+ Dodaj region** z paska narzędzi.

![Dodaj region][api-management-add-region]

Wybierz lokalizację z listy rozwijanej i ustaw liczbę jednostek dla suwaka.

![Określ jednostki][api-management-select-location-units]

Kliknij przycisk **Dodaj** można umieścić w tabeli Lokalizacje wybór. 

Powtórz ten proces, dopóki nie uzyskasz wszystkich skonfigurowanych lokalizacji, a następnie kliknij przycisk **zapisać** z paska narzędzi do rozpoczęcia procesu wdrażania.

## <a name="remove-region"> </a>Usuń wystąpienia usługi API Management z lokalizacji

W portalu Azure przejdź do **skali i cenach** strony wystąpienia usługi Zarządzanie interfejsami API. 

![Kartę Skala][api-management-scale-service]

Dla lokalizacji, które chcesz usunąć Otwórz za pomocą menu kontekstowego **...**  przycisk na prawym końcu tabeli. Wybierz **usunąć** opcji.

Potwierdzenie usunięcia, a następnie kliknij przycisk **zapisać** Aby zastosować zmiany.

[api-management-management-console]: ./media/api-management-howto-deploy-multi-region/api-management-management-console.png

[api-management-scale-service]: ./media/api-management-howto-deploy-multi-region/api-management-scale-service.png
[api-management-add-region]: ./media/api-management-howto-deploy-multi-region/api-management-add-region.png
[api-management-select-location-units]: ./media/api-management-howto-deploy-multi-region/api-management-select-location-units.png
[api-management-remove-region]: ./media/api-management-howto-deploy-multi-region/api-management-remove-region.png

[Create an API Management service instance]: get-started-create-service-instance.md
[Get started with Azure API Management]: get-started-create-service-instance.md

[Deploy an API Management service instance to a new region]: #add-region
[Delete an API Management service instance from a region]: #remove-region

[unit]: http://azure.microsoft.com/pricing/details/api-management/
[Premium]: http://azure.microsoft.com/pricing/details/api-management/

