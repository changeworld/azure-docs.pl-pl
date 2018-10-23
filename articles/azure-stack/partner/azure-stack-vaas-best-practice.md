---
title: Najlepsze praktyki walidacji usługi Azure Stack. | Microsoft Docs
description: Ten artykuł zawiera najlepsze rozwiązania do weryfikacji jako usługa.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2018
ms.author: mabrigg
ms.reviewer: John.Haskin
ms.openlocfilehash: 988b32d378b9affccb79f3351761f0eca5c91346
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2018
ms.locfileid: "49651583"
---
# <a name="best-practices-for-validation-as-a-service"></a>Najlepsze rozwiązania dotyczące weryfikacji jako usługa

[!INCLUDE[Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

W tym artykule opisano najlepsze rozwiązania dotyczące zarządzania zasobami w weryfikacji jako usługa (VaaS). Aby uzyskać omówienie VaaS zasobów, zobacz [weryfikacji jako usługa z kluczowych pojęć](azure-stack-vaas-key-concepts.md).

## <a name="solution-management"></a>Rozwiązanie Zarządzanie

### <a name="naming-convention-for-vaas-solutions"></a>Konwencje nazewnictwa dla rozwiązania VaaS

Stosowanie spójnej konwencji nazewnictwa dla wszystkich rozwiązań zarejestrowane w VaaS. Na przykład nazwa rozwiązania można skonstruować przy użyciu właściwości sprzętu poniżej w następujący sposób:

|Nazwa produktu | Element sprzętu 1 | Unikatowe Element 2 | Nazwa rozwiązania
|---|---|---|---|
Moje rozwiązanie XYZ |  Wszystkie Flash | Moje przełącznika X01 | MySolutionXYZ_AllFlash_MySwitchX01

### <a name="when-to-create-a-new-vaas-solution"></a>Kiedy należy utworzyć nowe rozwiązanie VaaS

Użyj tego samego rozwiązania VaaS podczas uruchamiania przepływów pracy dla tego samego sprzętu jednostki SKU. Nowe rozwiązanie VaaS powinny być tworzone tylko wtedy, gdy zmiany sprzętu jednostki SKU.

## <a name="workflow-management"></a>Zarządzanie przepływem pracy

### <a name="naming-convention-for-vaas-workflows"></a>Konwencje nazewnictwa dla przepływów pracy VaaS

Użyj spójnej konwencji nazewnictwa dla wszystkich przebiegów przepływu pracy VaaS. Na przykład konstrukcja przepływu pracy nazwę z poniższych właściwości kompilacji w następujący sposób:

|(Waga) numer kompilacji | Data | Rozmiar rozwiązania | Nazwa przepływu pracy
|---|---|---| ---|
1808 | 081518 | 4NODE | 1808_081518_4NODE

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [weryfikacji jako kluczowe pojęcia dotyczące usługi](azure-stack-vaas-key-concepts.md)