---
title: Uruchomienie testu poprawności w usłudze Azure Stack | Dokumentacja firmy Microsoft
description: Jak zbierać pliki dziennika dotyczące diagnostyki w usłudze Azure Stack.
services: azure-stack
author: mattbriggs
manager: femila
cloud: azure-stack
ms.assetid: D44641CB-BF3C-46FE-BCF1-D7F7E1D01AFA
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: article
ms.date: 07/19/2018
ms.author: mabrigg
ms.reviewer: hectorl
ms.openlocfilehash: a70c736489b25f6e8fd0d838c4c7b4b4db96a4f2
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/20/2018
ms.locfileid: "39188799"
---
# <a name="run-a-validation-test-for-azure-stack"></a>Uruchamianie testów sprawdzania poprawności dla usługi Azure Stack

*Dotyczy: Usługa Azure Stack zintegrowane systemy i usługi Azure Stack Development Kit*
 
Można sprawdzić stan usługi Azure Stack. Jeśli masz problem, skontaktuj się z działem obsługi klienta usług Microsoft. Pomocy technicznej prosi o uruchomienie **AzureStack testu** z węzła usługi zarządzania. Test weryfikacji izoluje awarii. Obsługa można następnie analizować szczegółowe dzienniki, skupić się na obszarze, w którym wystąpił błąd i pracy z Tobą w rozwiązaniu problemu.

## <a name="run-test-azurestack"></a>Run Test-AzureStack

Jeśli masz problem, skontaktuj się z pomocą techniczną usługi firmy Microsoft, a następnie uruchom **Uruchom Test-AzureStack**.

1. Występuje problem.
2. Skontaktuj się z pomocą klienta firmy Microsoft usług pomocy technicznej.
3. Uruchom **AzureStack testu** z uprzywilejowanym punktu końcowego.
    1. Dostęp uprzywilejowany punktu końcowego. Aby uzyskać instrukcje, zobacz [przy użyciu uprzywilejowanych punktu końcowego w usłudze Azure Stack](azure-stack-privileged-endpoint.md). 
    2. Na ASDK, zaloguj się do hosta zarządzania jako **AzureStack\CloudAdmin**.  
    W zintegrowanym systemie należy użyć adresu IP dla uprzywilejowanego punkt końcowy zarządzania dostarczonego przez dostawcę sprzętu OEM.
    3. Otwórz program PowerShell jako administrator.
    4. Uruchom polecenie: `Enter-PSSession -ComputerName <ERCS-VM-name> -ConfigurationName PrivilegedEndpoint`
    5. Uruchom polecenie: `Test-AzureStack`
4. Jeśli wszystkie testy zgłasza błąd, uruchom: `Get-AzureStackLog -FilterByRole SeedRing -OutputPath <Log output path>` polecenia cmdlet gromadzi dzienniki z AzureStack testu. Aby uzyskać więcej informacji na temat dzienników diagnostycznych, zobacz [narzędzia diagnostyczne usługi Azure Stack](azure-stack-diagnostics.md).
5. Wyślij **SeedRing** dzienniki w celu obsługi usług klienta firmy Microsoft. Usługi techniczną firmy Microsoft we współpracy z Tobą, aby rozwiązać ten problem.

## <a name="reference-for-test-azurestack"></a>Dokumentacja dotycząca AzureStack testu

Ta sekcja zawiera omówienie dla polecenia cmdlet Test-AzureStack i podsumowanie raportu sprawdzania poprawności.

### <a name="test-azurestack"></a>Test-AzureStack

Sprawdza stan usługi Azure Stack. Polecenie cmdlet zgłasza stan usługi Azure Stack sprzętu i oprogramowania. Działu pomocy technicznej, można użyć tego raportu w celu skrócenia czasu, aby rozwiązać przypadki pomocy technicznej usługi Azure Stack.

> [!Note]  
> **Test AzureStack** może wykryć błędy, które nie powodują awarii w chmurze, takich jak pojedynczy nie powiodło się, dysku lub awarii węzła jednym hoście fizycznym.

#### <a name="syntax"></a>Składnia

````PowerShell
  Test-AzureStack
````

#### <a name="parameters"></a>Parametry

| Parametr               | Wartość           | Wymagane | Domyślne |
| ---                     | ---             | ---      | ---     |
| ServiceAdminCredentials | PSCredential    | Nie       | WARTOŚĆ FALSE   |
| DoNotDeployTenantVm     | SwitchParameter | Nie       | WARTOŚĆ FALSE   |
| AdminCredential         | PSCredential    | Nie       | Nie dotyczy      |
| List                    | SwitchParameter | Nie       | WARTOŚĆ FALSE   |
| Zignoruj                  | Ciąg          | Nie       | Nie dotyczy      |
| Uwzględnij                 | Ciąg          | Nie       | Nie dotyczy      |
| BackupSharePath         | Ciąg          | Nie       | Nie dotyczy      |
| BackupShareCredential   | PSCredential    | Nie       | Nie dotyczy      |


Polecenia cmdlet Test-AzureStack obsługuje parametry wspólne: Verbose, Debug, ErrorAction, ErrorVariable, WarningAction, WarningVariable, OutBuffer, PipelineVariable i OutVariable. Aby uzyskać więcej informacji, zobacz [o typowych parametrach](http://go.microsoft.com/fwlink/?LinkID=113216). 

### <a name="examples-of-test-azurestack"></a>Przykłady AzureStack testu

W poniższych przykładach założono, zalogowano Cię jako **CloudAdmin** i uzyskiwania dostępu do uprzywilejowanych punktu końcowego (program ten). Aby uzyskać instrukcje, zobacz [przy użyciu uprzywilejowanych punktu końcowego w usłudze Azure Stack](azure-stack-privileged-endpoint.md). 

#### <a name="run-test-azurestack-interactively-without-cloud-scenarios"></a>Uruchom Test AzureStack interaktywnie bez scenariuszy w chmurze

W sesji program ten Uruchom polecenie:

````PowerShell
    Enter-PSSession -ComputerName <ERCS-VM-name> -ConfigurationName PrivilegedEndpoint -Credential $localcred
    Test-AzureStack
````

#### <a name="run-test-azurestack-with-cloud-scenarios"></a>Uruchom Test AzureStack ze scenariuszy w chmurze

Możesz użyć **AzureStack testu** uruchamiać scenariuszy w chmurze usługi Azure Stack. Scenariusze obejmują:

 - Tworzenie grupy zasobów
 - Tworzenie planów
 - Tworzenie oferty
 - Tworzenie konta magazynu
 - Tworzenie maszyny wirtualnej
 - Wykonaj operacje obiektów blob za pomocą konta magazynu utworzonego w scenariuszu testu
 - Wykonywanie operacji kolejki przy użyciu konta magazynu utworzonego w scenariuszu testu
 - Wykonaj operacje tabeli za pomocą konta magazynu utworzonego w scenariuszu testu

Scenariusze chmury wymaga poświadczeń administratora w chmurze. 
> [!Note]  
> Nie można uruchomić w scenariuszach chmur przy użyciu poświadczeń Active Directory federacyjnego Services (AD FS). **AzureStack testu** polecenia cmdlet jest tylko dostępne za pośrednictwem program ten. Jednak program ten nie obsługuje poświadczeń usług AD FS.

Wpisz nazwę użytkownika administratora chmury w formacie UPN serviceadmin@contoso.onmicrosoft.com (Azure AD). Po wyświetleniu monitu wpisz hasło do konta administratora w chmurze.

W sesji program ten Uruchom polecenie:

````PowerShell
  Enter-PSSession -ComputerName <ERCS-VM-name> -ConfigurationName PrivilegedEndpoint -Credential $localcred
  Test-AzureStack -ServiceAdminCredentials <Cloud administrator user name>
````

#### <a name="run-test-azurestack-without-cloud-scenarios"></a>Uruchom Test AzureStack bez scenariuszy w chmurze

W sesji program ten Uruchom polecenie:

````PowerShell
  $session = New-PSSession -ComputerName <ERCS-VM-name> -ConfigurationName PrivilegedEndpoint -Credential $localcred
  Invoke-Command -Session $session -ScriptBlock {Test-AzureStack}
````

#### <a name="list-available-test-scenarios"></a>Scenariusze testowania listy:

W sesji program ten Uruchom polecenie:

````PowerShell
  Enter-PSSession -ComputerName <ERCS-VM-name> -ConfigurationName PrivilegedEndpoint -Credential $localcred
  Test-AzureStack -List
````

#### <a name="run-a-specified-test"></a>Uruchom określony test

W sesji program ten Uruchom polecenie:

````PowerShell
  Enter-PSSession -ComputerName <ERCS-VM-name> -ConfigurationName PrivilegedEndpoint -Credential $localcred
  Test-AzureStack -Include AzsSFRoleSummary, AzsInfraCapacity
````

Aby wykluczyć określonych testów:

````PowerShell
    Enter-PSSession -ComputerName <ERCS-VM-name> -ConfigurationName PrivilegedEndpoint  -Credential $localcred
    Test-AzureStack -Ignore AzsInfraPerformance
````

### <a name="run-test-azurestack-to-test-infrastructure-backup-settings"></a>Uruchom Test-AzureStack, aby przetestować ustawienia kopii zapasowej infrastruktury

Przed rozpoczęciem konfigurowania infrastruktury kopii zapasowej, można przetestować ścieżki udziału kopii zapasowej, a poświadczenia, za pomocą **AzsBackupShareAccessibility** testu.

W sesji program ten Uruchom polecenie:

````PowerShell
    Enter-PSSession -ComputerName <ERCS-VM-name> -ConfigurationName PrivilegedEndpoint -Credential $localcred
    Test-AzureStack -Include AzsBackupShareAccessibility -BackupSharePath "\\<fileserver>\<fileshare>" -BackupShareCredential <PSCredentials-for-backup-share>
````
Po skonfigurowaniu kopii zapasowych, możesz uruchomić AzsBackupShareAccessibility do sprawdzania poprawności udziału jest dostępna z ERCS, od sesji program ten Uruchom:

````PowerShell
    Enter-PSSession -ComputerName <ERCS-VM-name> -ConfigurationName PrivilegedEndpoint  -Credential $localcred
    Test-AzureStack -Include AzsBackupShareAccessibility
````

Aby przetestować nowe poświadczenia za pomocą skonfigurowanego udziału kopii zapasowej z sesji program ten Uruchom:

````PowerShell
    Enter-PSSession -ComputerName <ERCS-VM-name> -ConfigurationName PrivilegedEndpoint -Credential $localcred
    Test-AzureStack -Include AzsBackupShareAccessibility -BackupShareCredential <PSCredential for backup share>
````

### <a name="validation-test"></a>Test sprawdzania poprawności

Poniższa tabela zawiera podsumowanie uruchomione przez testy weryfikacyjne **AzureStack testu**.

| Name (Nazwa)                                                                                                                              |
|-----------------------------------------------------------------------------------------------------------------------------------|-----------------------|
| Hostingu podsumowanie infrastruktury w chmurze usługi Azure Stack                                                                                  |
| Podsumowanie dotyczące usług magazynu usługi Azure Stack                                                                                              |
| Podsumowanie wystąpienia roli infrastruktury Azure Stack                                                                                  |
| Hostingu wykorzystanie infrastruktury w chmurze usługi Azure Stack                                                                              |
| Pojemność infrastruktury Azure Stack                                                                                               |
| Portal usługi Azure Stack oraz podsumowanie interfejsu API                                                                                                |
| Azure Stack Podsumowanie certyfikatu w usłudze Azure Resource Manager                                                                                               |
| Kontroler zarządzania infrastrukturą, kontrolera sieci, magazynowania i endpoint uprzywilejowanych ról infrastruktury          |
| Kontroler zarządzania infrastrukturą, kontrolera sieci, usługi magazynu i uprzywilejowanych punktu końcowego wystąpień roli infrastruktury |
| Azure Stack infrastruktury roli podsumowanie                                                                                           |
| Usługa Azure Stack chmury usługi Service Fabric                                                                                         |
| Wydajność wystąpień roli infrastruktury Azure Stack                                                                              |
| Podsumowanie wydajności hosta usługi Azure Stack w chmurze                                                                                        |
| Podsumowanie użycia zasobów usługi Azure Stack                                                                                  |
| Azure Stack skalowania jednostek zdarzeń krytycznych (ostatnie 8 godzin)                                                                             |
| Podsumowanie dysków fizycznych usług magazynu usługi Azure Stack                                                                               |
|Podsumowanie dostępności udziału kopii zapasowej usługi Azure Stack                                                                                     |

## <a name="next-steps"></a>Kolejne kroki

 - Aby dowiedzieć się więcej na temat rejestrowania problemu i narzędzia diagnostyczne usługi Azure Stack, zobacz [ narzędzia diagnostyczne usługi Azure Stack](azure-stack-diagnostics.md).
 - Aby dowiedzieć się więcej na temat rozwiązywania problemów, zobacz [Rozwiązywanie problemów z usługi Microsoft Azure Stack](azure-stack-troubleshooting.md)
