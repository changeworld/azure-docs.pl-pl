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
ms.openlocfilehash: b7fa03cdf52fc3218e9556c9664daafdc60243f3
ms.sourcegitcommit: a8948ddcbaaa22bccbb6f187b20720eba7a17edc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/21/2019
ms.locfileid: "56593221"
---
# <a name="validate-software-updates-from-microsoft"></a>Weryfikowanie aktualizacje oprogramowania firmy Microsoft

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Firma Microsoft udostępni okresowe aktualizacje oprogramowania Azure Stack. Te aktualizacje są dostarczane do usługi Azure Stack coengineering partnerów. Aktualizacje są dostarczane z wyprzedzeniem publicznie dostępnych. Można sprawdzania aktualizacji rozwiązania i przesyłanie opinii do firmy Microsoft.

Aktualizacji oprogramowania firmy Microsoft do usługi Azure Stack zostały oznaczone za pomocą konwencji nazewnictwa, na przykład 1803 wskazujący aktualizacji dla marca 2018 r. Dla informacji na temat zasad aktualizacji usługi Azure Stack, tempo i wersji są dostępne, zobacz [obsługi zasad w usłudze Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-servicing-policy).

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem korzystania z miesięcznych procesu aktualizacji w VaaS, należy zapoznać się z następujących elementów:

- [Sprawdzanie poprawności jako kluczowe pojęcia dotyczące usługi](azure-stack-vaas-key-concepts.md)
- [Testowanie weryfikacyjne interaktywnych funkcji](azure-stack-vaas-interactive-feature-verification.md)

## <a name="required-tests"></a>Wymagane testy

Następujące testy mają zostać wykonane w następującej kolejności miesięczne weryfikacji oprogramowania:

1. Miesięczne Weryfikacja aktualizacji usługi Azure Stack
2. Aparat symulacji w chmurze

## <a name="validating-software-updates"></a>Sprawdzanie poprawności aktualizacji oprogramowania

1. Utwórz nową **sprawdzanie poprawności pakietu** przepływu pracy.
1. Dla wymaganych testów powyżej, postępuj zgodnie z instrukcjami z [Uruchom sprawdzanie poprawności pakietu testów](azure-stack-vaas-validate-oem-package.md#run-package-validation-tests). Zobacz sekcję poniżej, aby uzyskać dodatkowe instrukcje na **miesięczne usługi Azure Stack aktualizacji weryfikacji** testu.

### <a name="apply-the-monthly-update"></a>Zastosuj comiesięcznej aktualizacji

1. Wybierz agenta w celu wykonania testów przed.
1. Harmonogram **miesięczne Weryfikacja aktualizacji usługi Azure Stack**.
1. Podaj lokalizację, aby pakiet rozszerzenia OEM, które są aktualnie wdrożonych na sygnatury i lokalizację, aby pakiet rozszerzenia OEM, które mają zostać zastosowane podczas aktualizacji. Aby skonfigurować adresy URL dla tych pakietów, zobacz [zarządzania pakietami w celu weryfikacji](azure-stack-vaas-validate-oem-package.md#managing-packages-for-validation).
1. Wykonaj kroki w interfejsie użytkownika z wybranego agenta.

Jeśli masz pytania lub wątpliwości skontaktuj się z pomocą [pomocy VaaS](mailto:vaashelp@microsoft.com).

## <a name="next-steps"></a>Kolejne kroki

- [Monitorowanie i zarządzanie testami w portalu VaaS](azure-stack-vaas-monitor-test.md)