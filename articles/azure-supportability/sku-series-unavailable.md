---
title: Jednostka SKU serii niedostępny | Dokumentacja firmy Microsoft
description: Niektóre jednostki SKU serii są niedostępne dla wybranej subskrypcji dla tego obszaru.
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
ms.openlocfilehash: 62964d0c5d75168226a35b25e5c256a1b57f3f81
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/16/2017
ms.locfileid: "24870582"
---
# <a name="region-or-sku-unavailable"></a>Region lub SKU niedostępny
W tym artykule opisano, jak rozwiązać problem subskrypcji platformy Azure, nie ma dostępu do regionu lub SKU maszyny Wirtualnej.

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

### <a name="when-purchasing-reserved-virtual-machine-instances-you-receive-one-of-the-following-error-messages"></a>Przy zakupie zastrzeżone wystąpień maszyn wirtualnych, pojawi się następujący komunikat o błędzie:

```
Message: Your subscription doesn’t support virtual machine reservation in <location>. Choose a 
different location. Supported locations are: <list of locations>  
```

```
Message: This size is currently unavailable in this location for this subscription
```

### <a name="when-creating-a-support-request-to-increase-compute-core-quota-a-region-or-a-sku-family-is-not-available-for-selection"></a>Podczas tworzenia żądania obsługi, aby zwiększyć limit przydziału rdzeni obliczeń, regionu lub rodzina jednostek SKU nie jest dostępna do wyboru.

## <a name="solution"></a>Rozwiązanie
Zaleca się najpierw, należy rozważyć alternatywnych regionu lub jednostka SKU, która spełnia potrzeby biznesowe. Jeśli nie można odnaleźć odpowiedniego regionu lub jednostka SKU, tworzenia "Zarządzanie subskrypcją" [żądania obsługi](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) następujące kroki:


- Na stronie podstawy wybierz typ problemu jako "Zarządzanie subskrypcją", wybierz subskrypcję, a następnie kliknij przycisk "Dalej".

![Blok Podstawowe](./media/SKU-series-unavailable/BasicsSubMgmt.png)


-   Na stronie Problem wybierz typ problemu jako "Inne pytania ogólne".
- W sekcji szczegółów:
  - Określ, czy szukasz kupna zastrzeżone wystąpień maszyn wirtualnych lub wdrożyć maszyny wirtualne
  - Określ region, jednostki SKU i liczba wystąpień maszyn wirtualnych, które planujesz wdrożyć lub zakupu


![Problem](./media/SKU-series-unavailable/ProblemSubMgmt.png)

-   Wprowadź swoje szczegóły dotyczące kontaktu i kliknij przycisk "Utwórz".

![Informacje kontaktowe](./media/SKU-series-unavailable/ContactInformation.png)

## <a name="feedback"></a>Opinie
Firma Microsoft zawsze są otwarte na opinie i sugestie! Wyślij do nas z [sugestie](https://feedback.azure.com/forums/266794-support-feedback). Ponadto można Uwzględnij z nami za pośrednictwem [Twitter](https://twitter.com/azuresupport) lub [fora MSDN](https://social.msdn.microsoft.com/Forums/azure).

## <a name="learn-more"></a>Dowiedz się więcej
[Pomoc techniczna platformy Azure — często zadawane pytania](https://azure.microsoft.com/support/faq)

