---
title: Zarządzanie agentem mobilności na serwerach na potrzeby odzyskiwania po awarii maszyn wirtualnych VMware i serwerów fizycznych za pomocą Azure Site Recovery | Microsoft Docs
description: Zarządzaj agentem usługi mobilności na potrzeby odzyskiwania po awarii maszyn wirtualnych VMware i serwerów fizycznych na platformę Azure przy użyciu usługi Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: ramamill
ms.openlocfilehash: 7cd555f66bb6f65f498f9b3e5db9bbeda0505a8f
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/22/2019
ms.locfileid: "68384975"
---
# <a name="manage-mobility-agent-on-protected-machines"></a>Zarządzanie agentem mobilności na chronionych maszynach

Agenta mobilności konfiguruje się na serwerze, korzystając z Azure Site Recovery na potrzeby odzyskiwania po awarii maszyn wirtualnych VMware i serwerów fizycznych na platformie Azure. Agent mobilności koordynuje komunikację między komputerem chronionym, serwerem konfiguracji/skalowalnym w poziomie serwerem przetwarzania i zarządza replikacją danych. Ten artykuł zawiera podsumowanie typowych zadań związanych z zarządzaniem agentem mobilności po jego wdrożeniu.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="update-mobility-service-from-azure-portal"></a>Aktualizuj usługę mobilności z Azure Portal

1. Przed rozpoczęciem upewnij się, że serwer konfiguracji, serwery przetwarzania skalowalnego w poziomie i wszystkie główne serwery docelowe, które są częścią wdrożenia, są aktualizowane przed aktualizacją usługi mobilności na chronionych maszynach.
2. W portalu otwórz magazyn > zreplikowanych **elementów**.
3. Jeśli jest to Najnowsza wersja serwera konfiguracji, zobaczysz powiadomienie z informacją, że dostępna jest nowa aktualizacja agenta replikacji Site Recovery. Kliknij, aby zainstalować ".

     ![Okno zreplikowane elementy](./media/vmware-azure-install-mobility-service/replicated-item-notif.png)

4. Kliknij powiadomienie, a następnie w obszarze **aktualizacja agenta**wybierz komputery, na których chcesz uaktualnić usługę mobilności. Następnie kliknij przycisk **OK**.

     ![Lista replikowanych elementów maszyn wirtualnych](./media/vmware-azure-install-mobility-service/update-okpng.png)

5. Zadanie aktualizowania usługi mobilności jest uruchamiane dla każdej z wybranych maszyn.

## <a name="update-mobility-service-through-powershell-script-on-windows-server"></a>Aktualizowanie usługi mobilności za pomocą skryptu programu PowerShell w systemie Windows Server

Użyj poniższego skryptu, aby uaktualnić usługę mobilności na serwerze za pomocą polecenia cmdlet programu PowerShell

```azurepowershell
Update-AzRecoveryServicesAsrMobilityService -ReplicationProtectedItem $rpi -Account $fabric.fabricSpecificDetails.RunAsAccounts[0]
```

## <a name="update-account-used-for-push-installation-of-mobility-service"></a>Konto aktualizacji używane na potrzeby instalacji wypychanej usługi mobilności

Po wdrożeniu Site Recovery, w celu włączenia instalacji wypychanej usługi mobilności, należy określić konto, które będzie używane przez serwer przetwarzania Site Recovery do uzyskiwania dostępu do maszyn i zainstalować usługę po włączeniu replikacji dla maszyny. Jeśli chcesz zaktualizować poświadczenia dla tego konta, postępuj zgodnie z [tymi instrukcjami](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation).

## <a name="uninstall-mobility-service"></a>Odinstaluj usługę mobilności

### <a name="on-a-windows-machine"></a>Na komputerze z systemem Windows

Odinstaluj z interfejsu użytkownika lub z wiersza polecenia.

- **Z poziomu interfejsu użytkownika**: W panelu sterowania maszyny wybierz pozycję **programy**. Wybierz pozycję **Microsoft Azure Site Recovery usługi mobilności/Odinstaluj główny serwer** > docelowy.
- **W wierszu polecenia**: Otwórz okno wiersza polecenia jako administrator na komputerze. Uruchom następujące polecenie: 
    ```
    MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
    ```

### <a name="on-a-linux-machine"></a>Na komputerze z systemem Linux
1. Na komputerze z systemem Linux Zaloguj się jako użytkownik **root** .
2. W terminalu przejdź do/user/local/ASR.
3. Uruchom następujące polecenie:
    ```
    uninstall.sh -Y
   ```
   
## <a name="install-site-recovery-vss-provider-on-source-machine"></a>Zainstaluj Site Recovery dostawcę usługi VSS na maszynie źródłowej

Aby wygenerować punkty spójności aplikacji, na maszynie źródłowej jest wymagany Dostawca usługi VSS Azure Site Recovery. Jeśli instalacja dostawcy zakończyła się niepowodzeniem za pomocą instalacji wypychanej, postępuj zgodnie z poniższymi wskazówkami, aby zainstalować je ręcznie.

1. Otwórz okno polecenia administratora.
2. Przejdź do lokalizacji instalacji usługi mobilności. (Np. C:\Program Files (x86) \Microsoft Azure Site Recovery\agent)
3. Uruchom skrypt InMageVSSProvider_Uninstall. cmd. Spowoduje to odinstalowanie usługi, jeśli już istnieje.
4. Uruchom skrypt InMageVSSProvider_Install. cmd, aby zainstalować dostawcę usługi VSS ręcznie.

## <a name="next-steps"></a>Następne kroki

- [Konfigurowanie odzyskiwania po awarii dla maszyn wirtualnych VMware](vmware-azure-tutorial.md)
- [Konfigurowanie odzyskiwania po awarii dla serwerów fizycznych](physical-azure-disaster-recovery.md)
