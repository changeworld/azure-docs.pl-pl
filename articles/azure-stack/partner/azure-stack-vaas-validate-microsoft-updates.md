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
ms.date: 1/07/2018
ms.author: mabrigg
ms.reviewer: johnhas
ROBOTS: NOINDEX
ms.openlocfilehash: 8ea0f36f3d167109fc0dc0d91707a08cff1c6a17
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/12/2019
ms.locfileid: "54243988"
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