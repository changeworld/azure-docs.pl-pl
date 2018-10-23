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
ms.date: 10/19/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: 7fcc7d5a1d87fe93d32772dbbb84f1d3c91d5631
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2018
ms.locfileid: "49648789"
---
# <a name="validate-software-updates-from-microsoft"></a>Weryfikowanie aktualizacje oprogramowania firmy Microsoft

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Firma Microsoft udostępni okresowe aktualizacje oprogramowania Azure Stack. Te aktualizacje są dostarczane do usługi Azure Stack wspólnej inżynierii partnerów ewentualnej udostępniane publicznie tak, aby mogli zweryfikować aktualizacje przed ich rozwiązania i przesyłanie opinii do firmy Microsoft.

[!INCLUDE [azure-stack-vaas-workflow-validation-completion](includes/azure-stack-vaas-workflow-validation-completion.md)]

## <a name="apply-monthly-update"></a>Zastosuj comiesięcznej aktualizacji

[!INCLUDE [azure-stack-vaas-workflow-section_update-azs](includes/azure-stack-vaas-workflow-section_update-azs.md)]

## <a name="create-a-workflow"></a>Tworzenie przepływu pracy

Liczba ocen aktualizacji korzystania z tego samego przepływu pracy jako **sprawdzanie poprawności pakietu**. Postępuj zgodnie z instrukcjami w artykule [tworzenia przepływu pracy, sprawdzanie poprawności pakietu](azure-stack-vaas-validate-oem-package.md#create-a-package-validation-workflow).

## <a name="run-tests"></a>Uruchom testy

Liczba ocen aktualizacji korzystania z tego samego przepływu pracy jako **sprawdzanie poprawności pakietu**. Postępuj zgodnie z instrukcjami w artykule [wykonać sprawdzanie poprawności pakietu testów](azure-stack-vaas-validate-oem-package.md#run-package-validation-tests).

Nie trzeba żądania podpisywania dla ocen aktualizacji pakietu.

## <a name="next-steps"></a>Kolejne kroki

- [Monitorowanie i zarządzanie testami w portalu VaaS](azure-stack-vaas-monitor-test.md)
