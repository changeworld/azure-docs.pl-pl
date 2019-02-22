---
title: Omówienie sprawdzania poprawności jako usługa dla usługi Azure Stack | Dokumentacja firmy Microsoft
description: Omówienie usługi Azure Stack weryfikacji jako usługa.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/20/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 12/20/2018
ROBOTS: NOINDEX
ms.openlocfilehash: 6126bacf50d47029c29772b35f6dc1d552d47029
ms.sourcegitcommit: a8948ddcbaaa22bccbb6f187b20720eba7a17edc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/21/2019
ms.locfileid: "56592642"
---
# <a name="what-is-validation-as-a-service-for-azure-stack"></a>Co to jest weryfikacja jako usługa dla usługi Azure Stack?

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Sprawdzanie poprawności jako usługa (VaaS) jest natywny usługi platformy Azure, przeznaczony dla partnerów rozwiązań, którzy są wspólnie inżynierów firmy Microsoft w ofercie usługi Azure Stack. Rozwiązanie partnerzy mogą używać usługi, aby sprawdzić, czy swoich rozwiązań spełniały wymagania firmy Microsoft i działają zgodnie z oczekiwaniami z usługą Azure Stack.

Podstawowy zastosowań VaaS należą:

- Weryfikowanie nowych rozwiązań usługi Azure Stack
- Sprawdzanie poprawności zmian do oprogramowania Azure Stack
- Cyfrowe podpisywanie pakietów rozwiązania partnera, używane podczas wdrażania
- Podgląd VaaS testowanie zabezpieczeń

## <a name="validate-a-new-azure-stack-solution"></a>Weryfikowanie nowych rozwiązań usługi Azure Stack

Użycie w ramach której partnerzy **sprawdzania poprawności rozwiązań** przepływu pracy, aby zweryfikować nowe rozwiązania usługi Azure Stack. Rozwiązanie musi pomyślnie przejść wymagane testy składnika sprzętowego zestawu laboratoryjnego (HLK) usługi Azure Stack. Aby przeprowadzić certyfikację szeroką gamę konfiguracji sprzętu, należy uruchomić przepływ pracy, dwa razy dla każdego nowego rozwiązania: raz dla każdej konfiguracji minimalne i maksymalne.

Aby uzyskać więcej informacji, zobacz [weryfikowanie nowych rozwiązań usługi Azure Stack](azure-stack-vaas-validate-solution-new.md).

## <a name="validate-changes-to-the-azure-stack-software"></a>Sprawdzenia poprawności zmian do oprogramowania Azure Stack

Użycie w ramach której partnerzy **sprawdzanie poprawności pakietu** przepływu pracy, aby sprawdzić, czy ich rozwiązanie działa przy użyciu najnowszych aktualizacji oprogramowania Azure Stack. Przepływu pracy weryfikacji pakietu muszą być uruchamiane w środowisko sprzętowe zalecanymi przez firmę Microsoft, w których poprawek i aktualizacji (P & N) była używana do stosowania aktualizacji. Zalecane jest również uruchomić przepływ pracy dla kompilacji linii bazowej.

Aby uzyskać więcej informacji, zobacz [Zweryfikuj aktualizacje oprogramowania firmy Microsoft](azure-stack-vaas-validate-microsoft-updates.md).

## <a name="get-digitally-signed-solution-partner-packages"></a>Pobieranie rozwiązania cyfrowo podpisanych pakietów partnera

Oprócz sprawdzania aktualizacji usługi Azure Stack, w ramach której partnerzy użyj **sprawdzanie poprawności pakietu** przepływu pracy, aby sprawdzić aktualizacje pakietów dostosowywania OEM, obejmujących sterowniki specyficzne dla partnerów usługi Azure Stack, oprogramowania układowego i inne oprogramowanie używane podczas wdrażania oprogramowania Azure Stack. Wdróż pakiet, który jest sprawdzana poprawność w bieżącej wersji oprogramowania usługi Azure Stack przy użyciu co najmniej o rozmiarze co najmniej rozwiązania, które będą obsługiwane. Pakiet jest przesyłany do usługi VaaS przed wykonaniem testów. Jeśli testy zakończą się powodzeniem, powiadom [ vaashelp@microsoft.com ](mailto:vaashelp@microsoft.com) czy pakietu zostało zakończone, testowania i powinna być cyfrowo podpisany przy użyciu podpisu cyfrowego w usłudze Azure Stack. Firma Microsoft podpisuje pakiet i powiadamia partnera usługi Azure Stack, że pakiet jest dostępny do pobrania w witrynie portal VaaS.

Aby uzyskać więcej informacji, zobacz [pakietów zweryfikować OEM](azure-stack-vaas-validate-oem-package.md).

## <a name="preview-vaas-test-collateral"></a>VaaS (wersja zapoznawcza) test zabezpieczeń

Firma Microsoft regularnie udostępnia nowe funkcje w usłudze Azure Stack. W ramach procesu tworzenia, dostarczania te funkcje na rynek, nowe zabezpieczenia testu ma zostać udostępnione w **przebiegu testu** przepływu pracy. Przepływ pracy przebieg testu zawiera test zabezpieczenia w przepływy pracy umożliwiające wykonywanie testów nieoficjalny. Nie należy używać przebiegu testu przepływu pracy można przesłać wyniki do zatwierdzenia. Użyj sprawdzania poprawności rozwiązań i sprawdzanie poprawności pakietu przepływów pracy, aby uzyskać urzędowego zatwierdzenia dla Twojego rozwiązania.

Aby uzyskać więcej informacji, zobacz temat [Szybki start: Użyj weryfikacji jako portal usługi do zaplanowania Twojego pierwszego testu](azure-stack-vaas-schedule-test-pass.md).

## <a name="validation-workflow-tests-summary"></a>Podsumowanie testów przepływu pracy weryfikacji

| Sprawdzanie poprawności przepływu pracy | Wymagane testy |
|----|------------|
| [Nowe rozwiązanie sprawdzania poprawności.](azure-stack-vaas-validate-solution-new.md) | Aparat symulacji w chmurze<br>Obliczenia operacyjnej zestaw SDK<br>Test identyfikator dysku<br>Pakiet operacyjnej zestaw SDK rozszerzenia usługi KeyVault<br>Operacyjnej zestaw SDK magazynu kluczy<br>Network SDK Operational Suite<br>Pakiet operacyjnej zestaw SDK konta magazynu<br> |
| [Sprawdzanie poprawności pakietu producenta OEM](azure-stack-vaas-validate-oem-package.md) | Weryfikacja pakietu rozszerzenia producenta OEM<br>Aparat symulacji w chmurze |
| [Miesięczne weryfikacji operacji update.](azure-stack-vaas-validate-microsoft-updates.md) | Miesięczne Weryfikacja AzureStack aktualizacji<br>Aparat symulacji w chmurze<br> |

## <a name="next-steps"></a>Kolejne kroki

- [Konfigurowanie walidacji jako zasoby usługi](azure-stack-vaas-set-up-resources.md)
- Dowiedz się więcej o [weryfikacji jako kluczowe pojęcia dotyczące usługi](azure-stack-vaas-key-concepts.md)
