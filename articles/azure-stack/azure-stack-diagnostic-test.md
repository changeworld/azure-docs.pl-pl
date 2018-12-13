---
title: Użyj narzędzia sprawdzającego poprawność usługi Azure Stack | Dokumentacja firmy Microsoft
description: Jak zbierać pliki dziennika dotyczące diagnostyki w usłudze Azure Stack.
services: azure-stack
author: jeffgilb
manager: femila
cloud: azure-stack
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: article
ms.date: 12/03/2018
ms.author: jeffgilb
ms.reviewer: adshar
ms.openlocfilehash: f754242d0cf7ee30572b21a3f4daf6fd2c0f63ff
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/12/2018
ms.locfileid: "53275911"
---
# <a name="validate-azure-stack-system-state"></a>Sprawdzanie poprawności stanu systemu w usłudze Azure Stack

*Dotyczy: Zintegrowane usługi Azure Stack, systemy i usługi Azure Stack Development Kit*

Jako operatorów usługi Azure Stack niezbędne jest posiadanie możliwości poinformowania, kondycję i stan systemu na żądanie. Narzędzie sprawdzania poprawności w usłudze Azure Stack (**AzureStack testu**) jest polecenia cmdlet programu PowerShell, która umożliwia uruchamianie szereg testów w systemie, aby zidentyfikować błędy, jeśli jest obecny. Zazwyczaj uzyskasz do uruchomienia tego narzędzia, za pośrednictwem [uprzywilejowanych punktu końcowego (program ten)](azure-stack-privileged-endpoint.md) po możesz skontaktuj się z działem pomocy technicznej usługi firmy Microsoft (CSS) problemu. Kondycji całego systemu i stan odpowiednie informacje CSS można zbierać i analizować szczegółowe dzienniki, skupić się na obszar, w którym wystąpił błąd i współpraca z Tobą w celu rozwiązania problemu.

## <a name="running-the-validation-tool-and-accessing-results"></a>Uruchamianie narzędzia sprawdzającego poprawność i uzyskiwania dostępu do wyników

Jak wspomniano wcześniej, za pośrednictwem program ten uruchomienie narzędzia sprawdzania poprawności. Zwraca każdy test **Powodzenie/niepowodzenie** stanu w oknie programu PowerShell. Ponadto szczegółowy raport HTML jest tworzony, który może uzyskać dostęp później podczas [zbierania dzienników](azure-stack-diagnostics.md). Poniżej przedstawiono zarys weryfikacji end-to-end procesu testowania: 

1. Dostęp uprzywilejowany punktu końcowego (program ten). Uruchom następujące polecenia, aby ustanowić sesję program ten:

   ```powershell
   Enter-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
   ```

   > [!TIP]
   > Aby otworzyć program ten, na komputerze-hoście ASDK, na użytek AzS ERCS01 - ComputerName.

2. Gdy program ten, uruchom: 

   ```powershell
   Test-AzureStack
   ```

   Zapoznaj się [zagadnienia dotyczące parametru](azure-stack-diagnostic-test.md#parameter-considerations) i [przykładowych przypadków używać](azure-stack-diagnostic-test.md#use-case-examples) sekcje, aby uzyskać więcej informacji.

3. Jeśli dowolne testy raportu **się nie POWIEŚĆ**Uruchom:

   ```powershell
   Get-AzureStackLog -FilterByRole SeedRing -OutputSharePath "<path>" -OutputShareCredential $cred
   ```

   Polecenia cmdlet zbiera dzienniki generowane przez AzureStack testu. Aby uzyskać więcej informacji na temat dzienników diagnostycznych, zobacz [narzędzia diagnostyczne usługi Azure Stack](azure-stack-diagnostics.md). Nie należy zbierać dzienniki lub skontaktuj się z CSS, jeśli testy raportu **OSTRZEGAJ**.

4. Jeśli z instrukcją należy uruchomić narzędzie sprawdzania poprawności, CSS, przedstawiciela CSS zażąda dzienniki zebrane do kontynuowania rozwiązywania problemu.

## <a name="tests-available"></a>Testy dostępności

Narzędzie sprawdzania poprawności umożliwia uruchamianie szereg testów na poziomie systemu i scenariusze podstawowe chmury, zapewniające wgląd do bieżącego stanu i ustalić problemy w systemie.

### <a name="cloud-infrastructure-tests"></a>Badania infrastruktury chmury

Te testy mały wpływ pracować na poziomie infrastruktury i uzyskać informacje na temat różnych składników systemu i funkcje. Aktualnie testy są zgrupowane w następujące kategorie:

| Kategoria testu                                        | Obejmują argument- i - Ignoruj |
| :--------------------------------------------------- | :-------------------------------- |
| Podsumowanie alertu w usłudze Azure Stack                            | AzsAlertSummary                   |
| Podsumowanie dostępności udziału kopii zapasowej usługi Azure Stack       | AzsBackupShareAccessibility       |
| Usługa Azure Stack płaszczyznę sterowania — podsumowanie                    | AzsControlPlane                   |
| Podsumowanie Defender usługi Azure Stack                         | AzsDefenderSummary                |
| Usługa Azure Stack podsumowanie oprogramowania układowego infrastruktury hostingu  | AzsHostingInfraFWSummary          |
| Hostingu podsumowanie infrastruktury w chmurze usługi Azure Stack     | AzsHostingInfraSummary            |
| Hostingu wykorzystanie infrastruktury w chmurze usługi Azure Stack | AzsHostingInfraUtilization        |
| Pojemność infrastruktury Azure Stack                  | AzsInfraCapacity                  |
| Wydajność infrastruktury Azure Stack               | AzsInfraPerformance               |
| Podsumowanie ról infrastruktury Azure Stack              | AzsInfraRoleSummary               |
| Podsumowanie aktualizacji usługi Azure Stack                           | AzsInfraUpdateSummary             |
| Portal usługi Azure Stack oraz podsumowanie interfejsu API                   | AzsPortalAPISummary               |
| Zdarzeń maszyny Wirtualnej jednostki skalowania usługi Azure Stack                     | AzsScaleUnitEvents                |
| Zasoby maszyny Wirtualnej jednostki skalowania usługi Azure Stack                  | AzsScaleUnitResources             |
| Podsumowanie sprawdzania poprawności SDN usługi Azure Stack                   | AzsSDNValidation                  |
| Podsumowanie roli sieci szkieletowej usługi Azure Stack              | AzsSFRoleSummary                  |
| Podsumowanie kontrolera zarządzania płytą GŁÓWNĄ usługi Azure Stack                              | AzsStampBMCSummary                |
| Podsumowanie dotyczące usług magazynu usługi Azure Stack                 | AzsStorageSvcsSummary             |
| Podsumowanie usługi Azure Stack SQL Store                        | AzsStoreSummary                   |
| Podsumowanie rozmieszczania maszyn wirtualnych usługi Azure Stack                     | AzsVmPlacement                    |

### <a name="cloud-scenario-tests"></a>Testy scenariusza chmury

Oprócz powyższych testów infrastruktury również mieć możliwość uruchamiania testów scenariusz chmury, aby sprawdzić funkcjonalność składnikami infrastruktury. Poświadczenia administratora chmury są wymagane do uruchamiania tych testów, ponieważ wymagają one wdrażania zasobów. 
    > [!NOTE]
    >
    > Currently you cannot run cloud scenario tests using Active Directory Federated Services (AD FS) credentials. 

W poniższych scenariuszach chmury są testowane przez narzędzie sprawdzania poprawności:
- Tworzenie grupy zasobów   
- Tworzenie planu              
- Tworzenie oferty            
- Tworzenie konta magazynu   
- Tworzenie maszyny wirtualnej 
- Operacja magazynu obiektów blob   
- Operacja magazynu kolejki  
- Operacja magazynu tabel  

## <a name="parameter-considerations"></a>Zagadnienia dotyczące parametru

- Parametr **listy** może służyć do wyświetlenia wszystkich kategorii testów dostępności.

- Parametry **Include** i **Ignoruj** może służyć do dołączania lub wykluczania kategorii testów. Zobacz [testy dostępności](azure-stack-diagnostic-test.md#tests-available) sekcji, aby uzyskać więcej informacji na skrót do użycia z tymi argumentami.

  ```powershell
  Test-AzureStack -Include AzsSFRoleSummary, AzsInfraCapacity
  ```

  ```powershell
  Test-AzureStack -Ignore AzsInfraPerformance
  ```

- Dzierżawy, w których maszyna wirtualna jest wdrażana stanowiącego część jednej testuje scenariusz chmury. Możesz użyć **DoNotDeployTenantVm** Aby wyłączyć tę opcję. 

- Należy podać **ServiceAdminCredential** parametru, aby uruchomić testy scenariusza w chmurze, zgodnie z opisem w [przykładowych przypadków używać](azure-stack-diagnostic-test.md#use-case-examples) sekcji.

- **BackupSharePath** i **BackupShareCredential** są używane podczas testowania kopii zapasowej ustawień infrastruktury, jak pokazano na [przykładowych przypadków używać](azure-stack-diagnostic-test.md#use-case-examples) sekcji.

- Narzędzie sprawdzania poprawności obsługuje również typowe parametry programu PowerShell: Verbose, Debug ErrorAction, ErrorVariable, WarningAction, WarningVariable, OutBuffer, PipelineVariable i OutVariable. Aby uzyskać więcej informacji, zobacz [o typowych parametrach](http://go.microsoft.com/fwlink/?LinkID=113216).  

## <a name="use-case-examples"></a>Skorzystaj z przykładów wielkości liter

### <a name="run-validation-without-cloud-scenarios"></a>Uruchom weryfikację bez scenariuszy w chmurze

Uruchamianie narzędzia sprawdzającego poprawność bez **ServiceAdminCredential** parametru, aby pominąć Uruchamianie testów w scenariuszu chmury: 

```powershell
New-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred
Test-AzureStack
```

### <a name="run-validation-with-cloud-scenarios"></a>Weryfikacja została uruchomiona przy użyciu scenariuszy w chmurze

Dostarczanie narzędzia sprawdzania poprawności z **ServiceAdminCredentials** parametru domyślnie uruchamia testy scenariusza chmury: 

```powershell
Enter-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
Test-AzureStack -ServiceAdminCredential "<Cloud administrator user name>" 
```

Jeśli chcesz uruchomić scenariuszy tylko w chmurze bez konieczności uruchamiania pozostałe testy, możesz użyć **Include** parametru, aby to zrobić: 

```powershell
Enter-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
Test-AzureStack -ServiceAdminCredential "<Cloud administrator user name>" -Include AzsScenarios   
```

Nazwa użytkownika administratora chmury należy wpisać w formacie UPN: serviceadmin@contoso.onmicrosoft.com (Azure AD). Po wyświetleniu monitu wpisz hasło do konta administratora w chmurze.

### <a name="run-validation-tool-to-test-system-readiness-before-installing-update-or-hotfix"></a>Uruchamianie narzędzia sprawdzającego poprawność do testowania gotowości systemu przed zainstalowaniem aktualizacji lub poprawki

Przed rozpoczęciem instalacji aktualizacji lub poprawki, należy uruchomić narzędzie sprawdzania poprawności, aby sprawdzić stan usługi Azure Stack:

```powershell
New-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
Test-AzureStack -Include AzsControlPlane, AzsDefenderSummary, AzsHostingInfraSummary, AzsHostingInfraUtilization, AzsInfraCapacity, AzsInfraRoleSummary, AzsPortalAPISummary, AzsSFRoleSummary, AzsStampBMCSummary
```

### <a name="run-validation-tool-to-test-infrastructure-backup-settings"></a>Uruchamianie narzędzia sprawdzającego poprawność, aby przetestować ustawienia kopii zapasowej infrastruktury

*Przed* Konfigurowanie infrastruktury kopii zapasowej, można przetestować ścieżki udziału kopii zapasowej i poświadczeń przy użyciu **AzsBackupShareAccessibility** testu: 

  ```powershell
  New-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
  Test-AzureStack -Include AzsBackupShareAccessibility -BackupSharePath "\\<fileserver>\<fileshare>" -BackupShareCredential <PSCredentials-for-backup-share>
  ```

*Po* Konfigurowanie kopii zapasowej, można uruchomić **AzsBackupShareAccessibility** do sprawdzania poprawności udziału jest dostępna z ERCS:

  ```powershell
  Enter-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
  Test-AzureStack -Include AzsBackupShareAccessibility
  ```

Aby przetestować nowe poświadczenia za pomocą skonfigurowanego udziału kopii zapasowej, uruchom polecenie: 

  ```powershell
  Enter-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
  Test-AzureStack -Include AzsBackupShareAccessibility -BackupShareCredential "<PSCredential for backup share>"
  ```



## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat rejestrowania problemu i narzędzia diagnostyczne usługi Azure Stack, zobacz [narzędzia diagnostyczne usługi Azure Stack](azure-stack-diagnostics.md).

Aby dowiedzieć się więcej na temat rozwiązywania problemów, zobacz [Rozwiązywanie problemów z usługi Microsoft Azure Stack](azure-stack-troubleshooting.md).