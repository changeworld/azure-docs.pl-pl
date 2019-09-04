---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 06/24/2019
ms.author: aahi
ms.openlocfilehash: 3b808f4eb853cf05eb08cd1acf08dedccabf71a6
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/04/2019
ms.locfileid: "70274630"
---
## <a name="azure-cognitive-service-resource-types"></a>Typy zasobów usługi poznawczej platformy Azure

<!-- > [!NOTE]
> Subscription owners can disable the creation of Cognitive Services resources for resource groups and subscriptions by applying [Azure policy](https://docs.microsoft.com/azure/governance/policy/overview#policy-definition), assigning a “Not allowed resource types” policy definition, and specifying **Microsoft.CognitiveServices/accounts** as the target resource type. -->
Dostęp do usługi Azure Cognitive Services można uzyskać za pomocą dwóch różnych zasobów: Zasób wielu usług lub jedna usługa. Te subskrypcje umożliwiają łączenie się z pojedynczą usługą poznawczej lub wieloma usługami poznawczymi jednocześnie.

### <a name="multi-service-resource"></a>Zasób obejmujący wiele usług

Subskrybowanie wielousługowego zasobu Cognitive Services:
* Umożliwia korzystanie z pojedynczego zasobu platformy Azure dla większości Cognitive Services platformy Azure.
* Użytkownik uzyskuje pojedynczy klucz, który może być używany z wieloma Cognitive Services platformy Azure.
* Konsoliduje rozliczenia od usług, z których korzystasz. Zobacz [cennik Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/) , aby uzyskać dodatkowe informacje.

>[!WARNING]
> W tej chwili te usługi **nie** obsługują kluczy wielousługowych: QnA Maker, usługi mowy, Custom Vision i wykrywania anomalii.

### <a name="single-service-resource"></a>Zasób pojedynczego usługi

Subskrybowanie zasobu Cognitive Services pojedynczego usługi:
* Umożliwia użycie określonej usługi poznawczej, dla której tworzony jest zasób (na przykład przetwarzanie obrazów lub usługi mowy).
* Uzyskujesz klucz, który jest specyficzny dla usługi poznawczej, dla której tworzysz zasób.