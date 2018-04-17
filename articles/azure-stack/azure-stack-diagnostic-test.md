---
title: Uruchomienie testu poprawności w stosie Azure | Dokumentacja firmy Microsoft
description: Jak zbierać pliki dziennika diagnostyki Azure stosu
services: azure-stack
author: mattbriggs
manager: femila
cloud: azure-stack
ms.assetid: D44641CB-BF3C-46FE-BCF1-D7F7E1D01AFA
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/06/2018
ms.author: mabrigg
ms.openlocfilehash: c28216ced2a7cd2995c55a9faacb93cf27e60c65
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="run-a-validation-test-for-azure-stack"></a>Uruchomienie testu poprawności Azure stosu

*Dotyczy: Azure stosu zintegrowanych systemów i Azure stosu Development Kit*
 
Można sprawdzić stan stosu Azure. Jeśli masz problem, skontaktuj się z pomocą techniczną usługi firmy Microsoft. Obsługa prośba do uruchamiania testów AzureStack z węźle zarządzania. Test weryfikacji izoluje awarii. Obsługa można następnie analizować szczegółowe dzienniki, skupić się na obszarze, w którym wystąpił błąd i pracy z Tobą w rozwiązaniu problemu.

## <a name="run-test-azurestack"></a>Run Test-AzureStack

Jeśli masz problem, skontaktuj się z pomocą techniczną usługi firmy Microsoft, a następnie uruchom **Uruchom Test-AzureStack**.

1. Masz problem.
2. Skontaktuj się z pomocą klienta firmy Microsoft usług pomocy technicznej.
3. Uruchom **AzureStack testu** z uprzywilejowanego punktu końcowego.
    1. Dostęp uprzywilejowany punktu końcowego. Aby uzyskać instrukcje, zobacz [przy użyciu punktu końcowego uprzywilejowanych w stosie Azure](azure-stack-privileged-endpoint.md). 
    2. Na ASDK, zaloguj się do hosta zarządzania jako **AzureStack\CloudAdmin**.  
    Zintegrowane w systemie należy użyć adresu IP dla uprzywilejowanych punkt końcowy dla zarządzania dostarczonego przez producenta OEM komputera.
    3. Otwórz program PowerShell jako administrator.
    4. Uruchom: `Enter-PSSession -ComputerName <ERCS VM name> -ConfigurationName PrivilegedEndpoint`
    5. Uruchom: `Test-AzureStack`
4. Jeśli żadnych testów zgłosić błąd, uruchom: `Get-AzureStackLog -FilterByRole SeedRing -OutputPath <Log output path>` polecenia cmdlet zbiera dzienniki z AzureStack testu. Aby uzyskać więcej informacji na temat dzienników diagnostycznych, zobacz [narzędzia diagnostyki Azure stosu](azure-stack-diagnostics.md).
5. Wyślij **SeedRing** dzienniki w celu obsługi usług klienta firmy Microsoft. Usługi techniczną firmy Microsoft współpracuje z Tobą w celu rozwiązania problemu.

## <a name="reference-for-test-azurestack"></a>Dokumentacja dotycząca AzureStack testu

Ta sekcja zawiera omówienie dla polecenia cmdlet Test-AzureStack i podsumowanie z raportu sprawdzania poprawności.

### <a name="test-azurestack"></a>Test-AzureStack

Sprawdza stan stosu Azure. Polecenie cmdlet informuje o stanie stosu Azure sprzętu i oprogramowania. Pracownikami działu pomocy technicznej można użyć tego raportu, aby skrócić czas do rozwiązania Azure stos obsługi przypadków.

> [!Note]  
> AzureStack testu może wykryć błędów, które nie powodują awarii chmury, takich jak pojedynczy nie powiodło się z dysku lub awarii węzła jednym hoście fizycznym.

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
<!-- | StorageConnectionString | Ciąg          | Nie       | Nie dotyczy      | nieobsługiwane w 1802-->
| List                    | SwitchParameter | Nie       | WARTOŚĆ FALSE   |
| Zignoruj                  | Ciąg          | Nie       | Nie dotyczy      |
| Uwzględnij                 | Ciąg          | Nie       | Nie dotyczy      |

Polecenia cmdlet Test-AzureStack obsługuje typowe parametry: Verbose, Debug, ErrorAction ErrorVariable, WarningAction, WarningVariable, OutBuffer, PipelineVariable i OutVariable. Aby uzyskać więcej informacji, zobacz [o typowych parametrach](http://go.microsoft.com/fwlink/?LinkID=113216). 

### <a name="examples-of-test-azurestack"></a>Przykłady AzureStack testu

W następujących przykładach przyjmuje zalogowano Cię jako **CloudAdmin** i uzyskiwania dostępu do uprzywilejowanych punktu końcowego (program ten). Aby uzyskać instrukcje, zobacz [przy użyciu punktu końcowego uprzywilejowanych w stosie Azure](azure-stack-privileged-endpoint.md). 

#### <a name="run-test-azurestack-interactively-without-cloud-scenarios"></a>Uruchom Test AzureStack interaktywnie bez scenariusze chmury

W sesji program ten Uruchom polecenie:

````PowerShell
  Enter-PSSession -ComputerName <ERCS VM name> -ConfigurationName PrivilegedEndpoint `
      Test-AzureStack
````

#### <a name="run-test-azurestack-with-cloud-scenarios"></a>Uruchom Test AzureStack ze scenariuszami chmury

Test AzureStack służy do uruchamiania scenariusze chmury względem stosu Azure. Scenariusze obejmują:

 - Tworzenie grupy zasobów
 - Tworzenie planów
 - Tworzenie oferty
 - Tworzenie konta magazynu
 - Tworzenie maszyny wirtualnej
 - Wykonywanie operacji obiektu blob przy użyciu konta magazynu utworzone w scenariuszu testu
 - Wykonywanie operacji kolejki przy użyciu konta magazynu utworzone w scenariuszu testu
 - Wykonaj operacje tabeli za pomocą konta magazynu utworzone w scenariuszu testu

W scenariuszach chmura wymaga poświadczeń administratora chmury. 
> [!Note]  
> Nie można uruchomić w scenariuszach chmur przy użyciu poświadczeń Active Directory federacyjnych Services (AD FS). **AzureStack testu** polecenia cmdlet jest dostępny za pośrednictwem program ten tylko. Jednak program ten nie obsługuje poświadczeń usług AD FS.

Wpisz nazwę użytkownika administratora chmury w formacie nazwy UPN serviceadmin@contoso.onmicrosoft.com (AAD). Po wyświetleniu monitu wpisz hasło do konta administratora w chmurze.

W sesji program ten Uruchom polecenie:

````PowerShell
  Enter-PSSession -ComputerName <ERCS VM name> -ConfigurationName PrivilegedEndpoint `
  Test-AzureStack -ServiceAdminCredentials <Cloud administrator user name>
````

#### <a name="run-test-azurestack-without-cloud-scenarios"></a>Uruchom Test AzureStack bez scenariusze chmury

W sesji program ten Uruchom polecenie:

````PowerShell
  $session = New-PSSession -ComputerName <ERCS VM name> -ConfigurationName PrivilegedEndpoint `
  Invoke-Command -Session $session -ScriptBlock {Test-AzureStack}
````

#### <a name="list-available-test-scenarios"></a>Scenariusze testowania listy:

W sesji program ten Uruchom polecenie:

````PowerShell
  Enter-PSSession -ComputerName <ERCS VM name> -ConfigurationName PrivilegedEndpoint `
  Test-AzureStack -List
````

#### <a name="run-a-specified-test"></a>Uruchamianie testu określony

W sesji program ten Uruchom polecenie:

````PowerShell
  Enter-PSSession -ComputerName <ERCS VM name> -ConfigurationName PrivilegedEndpoint `
  Test-AzureStack -Include AzsSFRoleSummary, AzsInfraCapacity
````

Aby wykluczyć określonych testów:

````PowerShell
  Enter-PSSession -ComputerName <ERCS VM name> -ConfigurationName PrivilegedEndpoint `
  Test-AzureStack -Ignore AzsInfraPerformance
````

### <a name="validation-test"></a>Test weryfikacji

Poniższa tabela zawiera podsumowanie testy weryfikacyjne, uruchamiając AzureStack testu.

| Name (Nazwa)                                                                                                                              |
|-----------------------------------------------------------------------------------------------------------------------------------|-----------------------|
| Hostingu podsumowanie infrastruktury w chmurze Azure stosu                                                                                  |
| Podsumowanie dotyczące usług magazynu Azure stosu                                                                                              |
| Podsumowanie wystąpienia roli infrastruktury Azure stosu                                                                                  |
| Hostingu wykorzystanie infrastruktury w chmurze Azure stosu                                                                              |
| Pojemność infrastruktury Azure stosu                                                                                               |
| Portal Azure stosu oraz podsumowanie interfejsu API                                                                                                |
| Azure stosu Podsumowanie certyfikatu w usłudze Azure Resource Manager                                                                                               |
| Kontroler zarządzania infrastruktury, kontrolera sieci usług magazynu i punktu końcowego uprzywilejowanych ról infrastruktury          |
| Kontroler zarządzania infrastruktury, kontrolera sieci usług magazynu i uprzywilejowane punktu końcowego wystąpień roli infrastruktury |
| Podsumowanie stosu infrastruktury roli Azure                                                                                           |
| Usługi sieci szkieletowej usług Azure stosu w chmurze                                                                                         |
| Wydajność wystąpienia roli infrastruktury Azure stosu                                                                              |
| Podsumowanie wydajności hosta chmury Azure stosu                                                                                        |
| Podsumowanie użycia zasobów usługi Azure stosu                                                                                  |
| Azure stosu skali jednostek zdarzeń krytycznych (ostatni 8 godzin)                                                                             |
| Podsumowanie dysków fizycznych usług magazynu Azure stosu                                                                               |

## <a name="next-steps"></a>Kolejne kroki

 - Aby uzyskać więcej informacji na temat narzędzia diagnostyki Azure stosu i rejestrowanie problemu, zobacz [ narzędzia diagnostyki Azure stosu](azure-stack-diagnostics.md).
 - Aby dowiedzieć się więcej na temat rozwiązywania problemów, zobacz [Microsoft Azure stosu Rozwiązywanie problemów](azure-stack-troubleshooting.md)