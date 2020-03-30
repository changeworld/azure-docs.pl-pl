---
title: Usługa Azure Security Center i rejestr kontenerów platformy Azure
description: Dowiedz się więcej o integracji usługi Azure Security Center z rejestrem kontenerów platformy Azure
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/19/2019
ms.author: memildin
ms.openlocfilehash: 069ce6ca1e76a9bd954031708702c973387abbaa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78205997"
---
# <a name="azure-container-registry-integration-with-security-center"></a>Integracja z usługą Azure Container Registry z Centrum zabezpieczeń

Usługa Azure Container Registry (ACR) to zarządzana, prywatna usługa rejestru platformy Docker, która przechowuje obrazy kontenerów i zarządza nimi dla wdrożeń platformy Azure w rejestrze centralnym. Jest on oparty na open-source Docker Registry 2.0.

Jeśli korzystasz ze standardowej warstwy usługi Azure Security Center, możesz dodać pakiet Rejestry kontenerów. Ta opcjonalna funkcja zapewnia lepszy wgląd w luki w zabezpieczeniach obrazów w rejestrach opartych na systemie ARM. Włącz lub wyłącz pakiet na poziomie subskrypcji, aby objąć wszystkie rejestry w ramach subskrypcji. Ta funkcja jest naliczana za obraz, jak pokazano na [stronie z cennikiem](security-center-pricing.md). Włączenie pakietu rejestrów kontenerów zapewnia, że usługa Security Center jest gotowa do skanowania obrazów, które są wypychane do rejestru. 

Za każdym razem, gdy obraz jest wypychany do rejestru, usługa Security Center automatycznie skanuje ten obraz. Aby wyzwolić skanowanie obrazu, wypchnij go do repozytorium.

Po zakończeniu skanowania (zazwyczaj po około 10 minutach) wyniki są dostępne w zaleceniach centrum zabezpieczeń w ten sposób:

[![Przykładowe zalecenie usługi Azure Security Center dotyczące luk wykrytych w obrazie hosta rejestru kontenerów platformy Azure (ACR)](media/azure-container-registry-integration/container-security-acr-page.png)](media/azure-container-registry-integration/container-security-acr-page.png#lightbox)

## <a name="benefits-of-integration"></a>Korzyści z integracji

Usługa Security Center identyfikuje rejestry ACR oparte na udziale ARM w ramach subskrypcji i bezproblemowo udostępnia:

* **Skanowanie luk w zabezpieczeniach natywnej platformy Azure** dla wszystkich wypychanych obrazów systemu Linux. Usługa Security Center skanuje obraz za pomocą skanera od wiodącego w branży dostawcy skanowania luk w zabezpieczeniach Qualys. To natywne rozwiązanie jest domyślnie bezproblemowo zintegrowane.

* **Zalecenia dotyczące zabezpieczeń** dla obrazów systemu Linux ze znanymi lukami w zabezpieczeniach. Usługa Security Center zawiera szczegółowe informacje o każdej zgłoszonej luce w zabezpieczeniach i klasyfikację ważności. Ponadto zawiera wskazówki dotyczące korygowania określonych luk znalezionych na każdym obrazie wypchniętym do rejestru.

![Omówienie wysokiego poziomu usługi Azure Security Center i azure container registry (ACR)](./media/azure-container-registry-integration/aks-acr-integration-detailed.png)

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o funkcjach zabezpieczeń kontenerów usługi Security Center, zobacz:

* [Usługa Azure Security Center i zabezpieczenia kontenerów](container-security.md)

* [Integracja z usługą Azure Kubernetes Service](azure-kubernetes-service-integration.md)

* [Ochrona maszyny wirtualnej](security-center-virtual-machine-protection.md) — zawiera opis zaleceń centrum zabezpieczeń
