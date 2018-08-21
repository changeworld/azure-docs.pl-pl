---
title: Omówienie sprawdzania poprawności jako usługa dla usługi Azure Stack | Dokumentacja firmy Microsoft
description: Omówienie usługi Azure Stack weryfikacji jako usługa, znane problemy.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/24/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: c96e7385356354d398108ad69492603d38667e46
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/20/2018
ms.locfileid: "40235038"
---
# <a name="what-is-validation-as-a-service-for-azure-stack"></a>Co to jest weryfikacja jako usługa dla usługi Azure Stack?

[!INCLUDE[Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Sprawdzanie poprawności jako usługa (VaaS) jest natywny usługi platformy Azure, przeznaczony dla partnerów rozwiązań, którzy są wspólnie inżynierów firmy Microsoft w ofercie usługi Azure Stack. Rozwiązanie partnerzy mogą używać usługi, aby sprawdzić, czy swoich rozwiązań spełniały wymagania firmy Microsoft i działają zgodnie z oczekiwaniami z usługą Azure Stack.

Podstawowym zastosowaniem VaaS jest:

- Weryfikowanie nowych rozwiązań usługi Azure Stack
- Sprawdzenia poprawności zmian do oprogramowania Azure Stack
- Pobierz partnera z zakresu rozwiązań cyfrowo podpisanych pakietów używane podczas wdrażania
- Zabezpieczenia walidacji usługi Azure Stack w wersji zapoznawczej

## <a name="validate-new-azure-stack-solution"></a>Weryfikowanie nowych rozwiązań usługi Azure Stack

Partnerzy użyć przepływu pracy weryfikacji rozwiązania, aby sprawdzić nowych rozwiązań usługi Azure Stack. Rozwiązanie musi pomyślnie przejść wymagane testy składników testów sprzętowego zestawu laboratoryjnego (HKL) usługi Azure Stack. Należy uruchomić przepływ pracy w każdym nowe rozwiązanie: jeden raz dla minimalnej i maksymalnej konfiguracji.

Aby uzyskać więcej informacji, zobacz [weryfikowanie nowych rozwiązań usługi Azure Stack](azure-stack-vaas-validate-solution-new.md).

## <a name="validate-changes-to-the-azure-stack-software"></a>Sprawdzenia poprawności zmian do oprogramowania Azure Stack

Partnerzy użyć przepływu pracy weryfikacji pakietu, aby sprawdzić, czy ich rozwiązanie działa przy użyciu najnowszych aktualizacji oprogramowania Azure Stack. Co najmniej przepływu pracy weryfikacji pakietu musi działać w środowisku sprzętu zalecane przez firmę Microsoft, a także na rozwiązanie gdzie poprawek i aktualizacji (P & N) zostały użyte do stosowania aktualizacji. Sprawdzanie poprawności pakietu należy uruchamiać kompilacji linii bazowej.

Aby uzyskać więcej informacji, zobacz [Zweryfikuj aktualizacje oprogramowania firmy Microsoft](azure-stack-vaas-validate-microsoft-updates.md).

## <a name="get-digitally-signed-solution-partner-packages"></a>Pobieranie rozwiązania cyfrowo podpisanych pakietów partnera

Oprócz sprawdzania aktualizacji usługi Azure Stack, można użyć przepływu pracy weryfikacji pakietu do sprawdzenia aktualizacji do pakietów dostosowywania OEM, które obejmują usługi Azure Stack specyficzne dla partnerów sterowników, oprogramowania układowego i inne oprogramowanie używane podczas wdrażania usługi Azure Stack oprogramowanie. Wdrażanie pakietu, które są na bieżącą wersję usługi Azure Stack przy użyciu co najmniej minimalną wielkości rozwiązania będą obsługiwane. Zaktualizowany pakiet należy przekazać do usługi przed rozpoczęciem badania. Jeśli testy zakończą się powodzeniem, powiadom vaashelp@microsoft.com. Poinformuj partnerów usługi Azure Stack, że pakiet została ukończona, testowania i powinno zostać podpisane cyfrowo za pomocą podpisu cyfrowego w usłudze Azure Stack. Firma Microsoft podpisuje pakiet i powiadamia partnera usługi Azure Stack, że pakiet jest dostępny do pobrania w portalu.

Aby uzyskać więcej informacji, zobacz [pakietów zweryfikować OEM](azure-stack-vaas-validate-oem-package.md).

## <a name="preview-azure-stack-validation-collateral"></a>Zabezpieczenia walidacji usługi Azure Stack w wersji zapoznawczej

Firma Microsoft regularnie udostępnia nowe funkcje w usłudze Azure Stack. W ramach procesu tworzenia, dostarczania te funkcje na rynek nowe zabezpieczenia testu jest udostępniany w przepływie pracy przebiegu testu. Przepływ pracy przebieg testu zawiera test zabezpieczenia w przepływy pracy umożliwiające wykonywanie testów nieoficjalny. Nie należy używać przebiegu testu przepływu pracy można przesłać wyniki do zatwierdzenia. Użyj rozwiązania sprawdzania poprawności i pakiet walidacji przepływu pracy, aby uzyskać urzędowego zatwierdzenia dla Twojego rozwiązania.

## <a name="next-steps"></a>Kolejne kroki

- Rozpocznij pracę i [Konfigurowanie walidacji jako konto usługi](azure-stack-vaas-validate-solution-new.md)
