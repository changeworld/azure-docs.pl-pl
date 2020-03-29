---
title: Seria regionu lub jednostka SKU niedostępna — platforma Azure
description: Niektóre serie jednostek SKU są niedostępne dla wybranej subskrypcji dla tego regionu, co może wymagać żądania pomocy technicznej zarządzania subskrypcjami.
services: Azure Supportability
author: stevendotwang
ms.service: azure-supportability
ms.topic: article
ms.date: 01/27/2020
ms.author: xingwan
ms.openlocfilehash: b0f0762ded6804c0b0d90a19223c082f0fb8fd49
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76843644"
---
# <a name="region-or-sku-unavailable"></a>Region lub jednostka SKU jest niedostępna

W tym artykule opisano sposób rozwiązania problemu z subskrypcją platformy Azure nie mając dostęp do regionu lub jednostki SKU maszyny Wirtualnej.

## <a name="symptoms"></a>Objawy

Podczas wdrażania maszyny wirtualnej pojawia się jeden z następujących komunikatów o błędach:

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

Przy zakupie zarezerwowanych wystąpień maszyn wirtualnych otrzymasz jeden z następujących komunikatów o błędach:

```
Message: Your subscription doesn’t support virtual machine reservation in <location>. Choose a 
different location. Supported locations are: <list of locations>  
```

```
Message: This size is currently unavailable in this location for this subscription
```

Podczas tworzenia żądania pomocy technicznej w celu zwiększenia przydziału podstawowego obliczeń region lub rodzina jednostek SKU nie jest dostępna do wyboru.

## <a name="solution"></a>Rozwiązanie

Najpierw zaleca się rozważenie alternatywnego regionu lub jednostki SKU, która spełnia Twoje potrzeby biznesowe.

Jeśli nie możesz znaleźć odpowiedniego regionu lub jednostki SKU, utwórz [żądanie pomocy technicznej](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) **zarządzania subskrypcjami,** wykonując następujące kroki:

1. Z menu [portalu platformy Azure](https://portal.azure.com) wybierz pozycję Pomoc + obsługa **.** Następnie wybierz **pozycję Nowe żądanie pomocy technicznej**.

1. W **obszarze Podstawowe**informacje , dla typu **problem**, wybierz pozycję **Zarządzanie subskrypcją**.

1. Wybierz **subskrypcję** i wprowadź krótki opis w **podsumowaniu**.

   ![Karta Podstawy nowego żądania pomocy technicznej](./media/SKU-series-unavailable/support-request-basics.png)

1. W obszarze **Typ problemu**wybierz pozycję Wybierz **typ problemu**.

1. W przypadku **wyboru typu problemu**wybierz opcję, na przykład Nie można uzyskać dostępu do mojej subskrypcji lub **zasobu** > **Mój problem nie jest wymieniony powyżej**. Wybierz **pozycję Zapisz**.

   ![Określ problem dla żądania](./media/SKU-series-unavailable/support-request-select-problem-type.png)

1. Wybierz **dalej: Rozwiązania umożliwiające zbadanie** możliwych rozwiązań. W razie potrzeby wybierz **przycisk Dalej: Szczegóły,** aby kontynuować.

1. Wprowadź wszelkie dodatkowe informacje, które możesz podać, wraz z informacjami kontaktowym.

1. Wybierz pozycję **Przegląd + utwórz**. Po zweryfikowaniu informacji wybierz pozycję **Utwórz,** aby utworzyć żądanie.

## <a name="send-us-your-suggestions"></a>Wyślij nam swoje sugestie

Zawsze jesteśmy otwarci na opinie i sugestie! Wyślij nam swoje [sugestie](https://feedback.azure.com/forums/266794-support-feedback). Dodatkowo możesz współpracować z nami na [Twitterze](https://twitter.com/azuresupport) lub [forach MSDN.](https://social.msdn.microsoft.com/Forums/azure)

## <a name="learn-more"></a>Dowiedz się więcej

[Często zadawane pytania dotyczące pomocy technicznej platformy Azure](https://azure.microsoft.com/support/faq)
