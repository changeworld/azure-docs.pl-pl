---
title: Seria SKU jest niedostępna | Microsoft Docs
description: Niektóre serie jednostek SKU są niedostępne dla wybranej subskrypcji w tym regionie.
services: Azure Supportability
author: stevendotwang
ms.service: azure-supportability
ms.topic: article
ms.date: 11/09/2017
ms.author: xingwan
ms.openlocfilehash: e317ae1ad88cf162f1d55a06d19e7b3b0b88ce60
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/11/2020
ms.locfileid: "75896729"
---
# <a name="region-or-sku-unavailable"></a>Region lub jednostka SKU jest niedostępna
W tym artykule opisano sposób rozwiązywania problemu z subskrypcją platformy Azure, która nie ma dostępu do regionu lub jednostki SKU maszyny wirtualnej.

## <a name="symptoms"></a>Objawy

### <a name="when-deploying-a-virtual-machine-you-receive-one-of-the-following-error-messages"></a>Podczas wdrażania maszyny wirtualnej jest wyświetlany jeden z następujących komunikatów o błędach:
```
Code: SkuNotAvailable
Message: The requested size for resource '<resource>' is currently not available in location 
'<location>' zones '<zone>' for subscription '<subscriptionID>'. Please try another size or 
deploy to a different location or zones. See https://aka.ms/azureskunotavailable for details.
```

```
Message: Your subscription doesn’t support virtual machine creation in <location>. Choose a 
different location. Supported locations are <list of locations>
```

```
Code: NotAvailableForSubscription
Message: This size is currently unavailable in this location for this subscription
```

### <a name="when-purchasing-reserved-virtual-machine-instances-you-receive-one-of-the-following-error-messages"></a>Podczas zakupu wystąpień zarezerwowanych maszyn wirtualnych otrzymujesz jeden z następujących komunikatów o błędach:

```
Message: Your subscription doesn’t support virtual machine reservation in <location>. Choose a 
different location. Supported locations are: <list of locations>  
```

```
Message: This size is currently unavailable in this location for this subscription
```

### <a name="when-creating-a-support-request-to-increase-compute-core-quota-a-region-or-a-sku-family-is-not-available-for-selection"></a>Podczas tworzenia żądania obsługi w celu zwiększenia przydziału rdzenia obliczeniowego nie można wybrać regionu lub rodziny jednostek SKU.

## <a name="solution"></a>Rozwiązanie
Najpierw Zalecamy rozważenie alternatywnego regionu lub jednostki SKU, która spełnia Twoje wymagania biznesowe. Jeśli nie możesz znaleźć odpowiedniego regionu lub jednostki SKU, Utwórz [żądanie pomocy technicznej](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) dotyczące zarządzania subskrypcjami, wykonując poniższe kroki:


- Na stronie podstawowe wybierz pozycję typ problemu jako "Zarządzanie subskrypcjami", wybierz subskrypcję i kliknij przycisk "dalej".

![Blok Podstawowe](./media/SKU-series-unavailable/BasicsSubMgmt.png)


-   Na stronie problem wybierz typ problemu jako "inne pytania ogólne".
- W sekcji szczegółów:
  - Wskaż, czy chcesz wdrażać maszyny wirtualne, czy zakupić zarezerwowane wystąpienia maszyn wirtualnych
  - Określ region, jednostkę SKU oraz liczbę wystąpień maszyn wirtualnych, które zamierzasz wdrożyć lub zakupić


![Problem](./media/SKU-series-unavailable/ProblemSubMgmt.png)

-   Wprowadź dane kontaktowe i kliknij przycisk "Utwórz".

![Informacje kontaktowe](./media/SKU-series-unavailable/ContactInformation.png)

## <a name="feedback"></a>Opinia
Zawsze jest otwarta opinia i sugestii. Wyślij nam swoje [sugestie](https://feedback.azure.com/forums/266794-support-feedback). Ponadto możesz poangażować się z nami za pośrednictwem usługi [Twitter](https://twitter.com/azuresupport) lub [forów MSDN](https://social.msdn.microsoft.com/Forums/azure).

## <a name="learn-more"></a>Dowiedz się więcej
[Pomoc techniczna platformy Azure — często zadawane pytania](https://azure.microsoft.com/support/faq)

