---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 06/24/2019
ms.author: aahi
ms.openlocfilehash: 674dd30ff3e493ec4c4036f032f82624a6ca5749
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/19/2019
ms.locfileid: "68334232"
---
## <a name="azure-cognitive-service-resource-types"></a>Typy zasobów usługi poznawczej platformy Azure

> [!NOTE]
> Właściciele subskrypcji mogą wyłączyć tworzenie zasobów Cognitive Services dla grup zasobów i subskrypcji, stosując [Zasady platformy Azure](https://docs.microsoft.com/azure/governance/policy/overview#policy-definition), przypisując definicje zasad "Niedozwolone typy zasobów" i określając  **Microsoft. CognitiveServices/konta** jako docelowy typ zasobu.

Dostęp do usługi Azure Cognitive Services można uzyskać za pomocą dwóch różnych zasobów: Zasób wielu usług lub jedna usługa. Te subskrypcje umożliwiają łączenie się z jedną usługą lub wieloma usługami jednocześnie.

### <a name="multi-service-resource"></a>Zasób obejmujący wiele usług

>[!WARNING]
> W tej chwili te usługi **nie** obsługują kluczy wielousługowych: QnA Maker, usługi mowy, Custom Vision i wykrywania anomalii.

Subskrybowanie wielousługowego zasobu Cognitive Services:
* Umożliwia korzystanie z pojedynczego zasobu platformy Azure dla większości Cognitive Services platformy Azure.
* Konsoliduje rozliczenia od usług, z których korzystasz. Zobacz [cennik Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/) , aby uzyskać dodatkowe informacje.

### <a name="single-service-resource"></a>Zasób pojedynczego usługi

Zasoby pojedynczej usługi (takie jak przetwarzanie obrazów lub usługi mowy) są ograniczone do określonej usługi.