---
title: Weryfikowanie aktualizacje oprogramowania firmy Microsoft w usługi Azure Stack weryfikacji jako usługa | Dokumentacja firmy Microsoft
description: Dowiedz się, jak sprawdzić aktualizacje oprogramowania firmy Microsoft z weryfikacją jako usługa.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/14/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 01/14/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 4e6f5a17544c1419eb6101acdd6590f034ea4aa3
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/30/2019
ms.locfileid: "55241462"
---
# <a name="validate-software-updates-from-microsoft"></a>Weryfikowanie aktualizacje oprogramowania firmy Microsoft

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Firma Microsoft udostępni okresowe aktualizacje oprogramowania Azure Stack. Te aktualizacje są dostarczane do usługi Azure Stack coengineering partnerów. Aktualizacje są dostarczane z wyprzedzeniem publicznie dostępnych. Można sprawdzania aktualizacji rozwiązania i przesyłanie opinii do firmy Microsoft.

[!INCLUDE [azure-stack-vaas-workflow-validation-completion](includes/azure-stack-vaas-workflow-validation-completion.md)]

## <a name="apply-monthly-update"></a>Zastosuj comiesięcznej aktualizacji

[!INCLUDE [azure-stack-vaas-workflow-section_update-azs](includes/azure-stack-vaas-workflow-section_update-azs.md)]

## <a name="create-a-workflow"></a>Tworzenie przepływu pracy

Liczba ocen aktualizacji korzystania z tego samego przepływu pracy jako **sprawdzania poprawności rozwiązań**.

## <a name="run-tests"></a>Uruchom testy

1. Liczba ocen aktualizacji korzystania z tego samego przepływu pracy jako **sprawdzania poprawności rozwiązań**. 

2. Postępuj zgodnie z instrukcjami w artykule [testy Uruchom weryfikację rozwiązania](azure-stack-vaas-validate-oem-package.md#run-package-validation-tests). Zamiast tego wybierz następujące testy:
    - Miesięczne Weryfikacja aktualizacji usługi Azure Stack
    - Aparat symulacji w chmurze

Nie ma potrzeby żądania podpisywania dla ocen aktualizacji pakietu.

## <a name="next-steps"></a>Kolejne kroki

- [Monitorowanie i zarządzanie testami w portalu VaaS](azure-stack-vaas-monitor-test.md)