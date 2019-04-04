---
title: Informacje o wersji usługi Azure Stack sprawdzania poprawności jako usługa | Dokumentacja firmy Microsoft
description: Informacje o wersji usługi Azure Stack sprawdzania poprawności jako usługa.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/11/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 03/11/2019
ms.openlocfilehash: eefd39c751bdbd9ed9c8f3b9112fee1ddbffb9a0
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2019
ms.locfileid: "58486941"
---
# <a name="release-notes-for-validation-as-a-service"></a>Informacje o wersji dotyczące weryfikacji jako usługa

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

W tym artykule znajdują się informacje o wersji dla usługi Azure Stack weryfikacji jako usługa.

## <a name="version-405"></a>Wersja 4.0.5

17 stycznia 2019 r

- Zaktualizowano w celu adres magazynu puli niespójności Test identyfikacji dysku. Wersja: 5.1.14.0 -> 5.1.15.0
- Azure Stack miesięcznych aktualizacji weryfikacji zaktualizowane w celu rozwiązania zatwierdzone oprogramowanie i zawartość niespójności sprawdzania poprawności. Wersja: 5.1.14.0 -> 5.1.17.0
- Weryfikacja pakietu rozszerzenia OEM zaktualizowany, aby wykonać wymagane kontrole przed wykonaniem kroku aktualizacji usługi Azure Stack. Wersja: 5.1.14.0 -> 5.1.16.0
- Poprawki błędów wewnętrznych

## <a name="version-402"></a>W wersji 4.0.2

2019 stycznia 7

Jeśli używasz usługi Azure Stack miesięcznych aktualizacji weryfikacji przepływu pracy, a wersja pakietu aktualizacji OEM nie jest 1810 lub nowszej, zostanie wyświetlony błąd, po pobraniu do kroku aktualizacji OEM. Jest to błąd. Poprawka jest opracowywany. Kroki zaradcze są następujące:

1. Uruchom aktualizację OEM, jak zwykle.
2. Wykonaj Test AzureStack po zastosowaniu zakończonym powodzeniem pakietu i Zapisz dane wyjściowe.
3. Anuluj testu.
4. Zapisane dane wyjściowe do wysyłania VaaSHelp@microsoft.com do otrzymywania przekazywanie wyników do uruchomienia.

## <a name="version-402"></a>W wersji 4.0.2

30 listopada 2018

- Poprawki błędów wewnętrznych

## <a name="version-401"></a>Wersja 4.0.1

8 października 2018

- Wymagania wstępne VaaS

    `Install-VaaSPrerequisites` nie wymaga już poświadczenia administratora chmury. Jeśli używasz najnowszej wersji tego polecenia cmdlet, zobacz [pobrać i zainstalować agenta](azure-stack-vaas-local-agent.md#download-and-install-the-agent) poprawione poleceń instalacji wymagań wstępnych. Oto odpowiednie polecenia:

    ```powershell
    $ServiceAdminCreds = New-Object System.Management.Automation.PSCredential "<aadServiceAdminUser>", (ConvertTo-SecureString "<aadServiceAdminPassword>" -AsPlainText -Force)
    Import-Module .\VaaSPreReqs.psm1 -Force
    Install-VaaSPrerequisites -AadTenantId $AadTenantId `
                              -ServiceAdminCreds $ServiceAdminCreds `
                              -ArmEndpoint https://adminmanagement.$ExternalFqdn `
                              -Region $Region
    ```

## <a name="version-400"></a>Wersja 4.0.0

29 sierpnia 2018 r.

- Wymagania wstępne VaaS i wirtualny dysk twardy z aktualizacjami

    `Install-VaaSPrerequisites` teraz wymaga poświadczeń administratora chmury, aby rozwiązać problem podczas sprawdzania poprawności pakietu. Dokumentacja w [pobrać i zainstalować agenta](azure-stack-vaas-local-agent.md#download-and-install-the-agent) został zaktualizowany o następujące czynności:

    ```powershell
    $ServiceAdminCreds = New-Object System.Management.Automation.PSCredential "<aadServiceAdminUser>", (ConvertTo-SecureString "<aadServiceAdminPassword>" -AsPlainText -Force)
    $CloudAdminCreds = New-Object System.Management.Automation.PSCredential "<cloudAdminDomain\username>", (ConvertTo-SecureString "<cloudAdminPassword>" -AsPlainText -Force)
    Import-Module .\VaaSPreReqs.psm1 -Force
    Install-VaaSPrerequisites -AadTenantId $AadTenantId `
                              -ServiceAdminCreds $ServiceAdminCreds `
                              -ArmEndpoint https://adminmanagement.$ExternalFqdn `
                              -Region $Region `
                              -CloudAdminCredentials $CloudAdminCreds
    ```
    > [!NOTE]
    > `$CloudAdminCreds` Wymagane przez skrypt dla usługi Azure Stack wystąpienia Trwa sprawdzanie poprawności. Nie są one używanych przez dzierżawcę VaaS poświadczenia usługi Azure Active Directory.

- Aktualizacja agenta lokalnego

    Poprzednią wersję agenta lokalnego nie jest zgodny z bieżącym 4.0.0 wersji usługi. Wszyscy użytkownicy, musisz zaktualizować swoje lokalne agenty. Zobacz [pobrać i zainstalować agenta](azure-stack-vaas-local-agent.md#download-and-install-the-agent) instrukcje na temat instalowania najnowszej agenta.

- Aktualizacja automatyzacji programu PowerShell

    Wprowadzono zmiany `LaunchVaaSTests` skryptów programu PowerShell, które wymagają najnowszej wersji pakietów obsługi skryptów. Zobacz [uruchomienia przepływu pracy przebiegu testu](azure-stack-vaas-automate-with-powershell.md#launch-the-test-pass-workflow) instrukcje na temat instalowania najnowszej wersji pakietów obsługi skryptów.

- Sprawdzanie poprawności jako Portal usługi

  - Powiadomienia podpisywania pakietu

    Po przesłaniu pakietu dostosowywania OEM jako część przepływu pracy weryfikacji pakietu formatu pakietu zostanie zweryfikowana, aby upewnić się, czy jest zgodna z opublikowanych specyfikacji. Jeśli pakiet nie jest zgodne, działanie zakończy się niepowodzeniem. Powiadomienia e-mail będą wysyłane na adres e-mail zarejestrowany skontaktuj się z usługi Azure Active Directory dla dzierżawy.

  - Kategoria testu interaktywne

    **Interactive** kategorii testów został dodany. Te testy wykonują interaktywne, Nieautomatyczny scenariusze usługi Azure Stack.

  - Weryfikacja interaktywnych funkcji

    Możliwość opinii wąsko zdefiniowany dla niektórych funkcji jest teraz dostępna w przepływie pracy przebiegu testu. `OEM Update on Azure Stack 1806 RC Validation 5.1.4.0` Test sprawdza, jeśli określone aktualizacje zostały zastosowane poprawnie, a następnie służy do zbierania opinii.

## <a name="next-steps"></a>Kolejne kroki

- [Rozwiązywanie problemów z weryfikacji jako usługa](azure-stack-vaas-troubleshoot.md)
