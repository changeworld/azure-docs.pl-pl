---
title: Serie jednostek SKU są niedostępne | Dokumentacja firmy Microsoft
description: Niektóre serie jednostek SKU są niedostępne dla wybranych subskrypcji tego regionu.
services: Azure Supportability
documentationcenter: ''
author: stevendotwang
manager: rajatk
editor: ''
ms.service: azure-supportability
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: xingwan
ms.openlocfilehash: a57899e36a6716a6fd59cb018119c225b7396c0d
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60649137"
---
# <a name="region-or-sku-unavailable"></a>Regionu lub jednostki SKU jest niedostępny
W tym artykule opisano sposób rozwiązywania problemów z subskrypcją platformy Azure nie mają dostępu do regionu lub jednostki SKU maszyny Wirtualnej.

## <a name="symptoms"></a>Objawy

### <a name="when-deploying-a-virtual-machine-you-receive-one-of-the-following-error-messages"></a>Podczas wdrażania maszyny wirtualnej, wyświetlany jest jeden z następujących komunikatów o błędzie:
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

### <a name="when-purchasing-reserved-virtual-machine-instances-you-receive-one-of-the-following-error-messages"></a>Przy zakupie zarezerwowane wystąpienia maszyn wirtualnych, pojawi się następujący komunikat o błędzie:

```
Message: Your subscription doesn’t support virtual machine reservation in <location>. Choose a 
different location. Supported locations are: <list of locations>  
```

```
Message: This size is currently unavailable in this location for this subscription
```

### <a name="when-creating-a-support-request-to-increase-compute-core-quota-a-region-or-a-sku-family-is-not-available-for-selection"></a>Podczas tworzenia żądania pomocy technicznej, aby zwiększyć limit przydziału rdzeni obliczeniowych, regionu lub rodzina jednostek SKU nie jest dostępne do wyboru.

## <a name="solution"></a>Rozwiązanie
Zaleca się najpierw, które należy wziąć pod uwagę inny region lub jednostki SKU, która spełnia Twoje wymagania biznesowe. Jeśli nie możesz znaleźć odpowiedniego regionu lub jednostki SKU, należy utworzyć "Zarządzanie subskrypcjami" [żądania pomocy technicznej](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) następujące czynności:


- Na stronie podstawy wybierz typ problemu jako "Zarządzanie subskrypcjami", wybierz subskrypcję, a następnie kliknij przycisk "Dalej".

![Blok Podstawowe](./media/SKU-series-unavailable/BasicsSubMgmt.png)


-   Na stronie Problem wybierz typ problemu jako "Inne pytania ogólne".
- W sekcji szczegółów:
  - Określ, jeśli chcesz wdrożyć maszyny wirtualne lub Kup zarezerwowane wystąpienia maszyn wirtualnych
  - Określ region, jednostki SKU i liczbę wystąpień maszyn wirtualnych, które zamierzasz wdrożyć lub zakupu


![Problem](./media/SKU-series-unavailable/ProblemSubMgmt.png)

-   Twoje dane kontaktowe i kliknij przycisk "Utwórz".

![Informacje kontaktowe](./media/SKU-series-unavailable/ContactInformation.png)

## <a name="feedback"></a>Opinia
Firma Microsoft zawsze są otwarte opinie i sugestie! Wyślij nam swoje [sugestie](https://feedback.azure.com/forums/266794-support-feedback). Ponadto można być w kontakcie z nami za pośrednictwem [Twitter](https://twitter.com/azuresupport) lub [fora MSDN](https://social.msdn.microsoft.com/Forums/azure).

## <a name="learn-more"></a>Dowiedz się więcej
[Pomoc techniczna platformy Azure — często zadawane pytania](https://azure.microsoft.com/support/faq)

