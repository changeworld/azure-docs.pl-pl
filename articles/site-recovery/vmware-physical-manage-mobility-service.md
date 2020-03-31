---
title: Zarządzanie agentem mobilności dla serwerów VMware/fizycznym za pomocą usługi Azure Site Recovery
description: Zarządzaj agentem usługi mobilności w celu odzyskiwania po awarii maszyn wirtualnych vmware i serwerów fizycznych na platformie Azure przy użyciu usługi Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: ramamill
ms.openlocfilehash: 9be758c286e072b0fbefc5f8b20b7accc4e6741b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256967"
---
# <a name="manage-the-mobility-agent"></a>Zarządzanie agentem mobilności 

Agent mobilności jest skonfigurowany na serwerze podczas korzystania z usługi Azure Site Recovery do odzyskiwania po awarii maszyn wirtualnych vmware i serwerów fizycznych na platformie Azure. Agent mobilności koordynuje komunikację między chronionym komputerem, serwerem konfiguracji/serwerem procesów skalowanych w poziomie i zarządza replikacją danych. W tym artykule podsumowano typowe zadania związane z zarządzaniem agentem mobilności po jego wdrożeniu.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="update-mobility-service-from-azure-portal"></a>Aktualizowanie usługi mobilności z witryny Azure portal

1. Przed rozpoczęciem upewnij się, że serwer konfiguracji, skalowane w poziomie serwery procesów i wszystkie główne serwery docelowe, które są częścią wdrożenia są aktualizowane przed zaktualizowaniem usługi mobilności na chronionych komputerach.
2. W portalu otwórz magazyn > **elementy replikowane**.
3. Jeśli serwer konfiguracji jest najnowszą wersją, zostanie wyświetlone powiadomienie z napisem "Aktualizacja agenta replikacji odzyskiwania nowej lokacji jest dostępna. Kliknij, aby zainstalować."

     ![Okno Elementy replikowane](./media/vmware-azure-install-mobility-service/replicated-item-notif.png)

4. Kliknij powiadomienie, a następnie w **aktualizacji agenta**wybierz maszyny, na których chcesz uaktualnić usługę mobilności. Następnie kliknij przycisk **OK**.

     ![Lista elementów replikowanych](./media/vmware-azure-install-mobility-service/update-okpng.png)

5. Zadanie aktualizacji usługi mobilności zostanie uruchomiony dla każdego z wybranych komputerów.

## <a name="update-mobility-service-through-powershell-script-on-windows-server"></a>Aktualizowanie usługi mobilności za pomocą skryptu programu PowerShell na serwerze Windows

Przed rozpoczęciem upewnij się, że serwer konfiguracji, skalowane w poziomie serwery procesów i wszystkie główne serwery docelowe, które są częścią wdrożenia są aktualizowane przed zaktualizowaniem usługi mobilności na chronionych komputerach.

Użyj następującego skryptu, aby uaktualnić usługę mobilności na serwerze za pomocą polecenia cmdlet powłoki zasilania

```azurepowershell
Update-AzRecoveryServicesAsrMobilityService -ReplicationProtectedItem $rpi -Account $fabric.fabricSpecificDetails.RunAsAccounts[0]
```

## <a name="update-mobility-service-manually-on-each-protected-server"></a>Ręczne aktualizowanie usługi mobilności na każdym chronionym serwerze

1. Przed rozpoczęciem upewnij się, że serwer konfiguracji, skalowane w poziomie serwery procesów i wszystkie główne serwery docelowe, które są częścią wdrożenia są aktualizowane przed zaktualizowaniem usługi mobilności na chronionych komputerach.

2. [Znajdź instalatora agenta](vmware-physical-mobility-service-overview.md#locate-installer-files) na podstawie systemu operacyjnego serwera.

>[!IMPORTANT]
> Jeśli replikujesz maszynę wirtualną usługi Azure IaaS z jednego regionu platformy Azure do innego, nie używaj tej metody. Zapoznaj się z [naszymi wskazówkami,](azure-to-azure-autoupdate.md) aby uzyskać informacje na temat wszystkich dostępnych opcji.

3. Skopiuj plik instalacyjny na chroniony komputer i uruchom go, aby zaktualizować agenta mobilności.

## <a name="update-account-used-for-push-installation-of-mobility-service"></a>Aktualizuj konto używane do instalacji push usługi mobilności

Po wdrożeniu usługi Site Recovery, aby włączyć instalację wypychaną usługi mobilności, określono konto, którego serwer przetwarzania odzyskiwania lokacji używa do uzyskiwania dostępu do komputerów i instalowania usługi, gdy replikacja jest włączona dla komputera. Jeśli chcesz zaktualizować poświadczenia dla tego konta, postępuj zgodnie z [tymi instrukcjami](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation).

## <a name="uninstall-mobility-service"></a>Odinstaluj usługę mobilności

### <a name="on-a-windows-machine"></a>Na komputerze z systemem Windows

Odinstaluj z interfejsu użytkownika lub z wiersza polecenia.

- **W interfejsie użytkownika**: W panelu sterowania urządzenia wybierz pozycję **Programy**. Wybierz **usługę mobilności odzyskiwania witryny platformy Microsoft Azure/serwer** > docelowy**wzorca Odinstaluj**.
- **Z wiersza polecenia**: Otwórz okno wiersza polecenia jako administrator na komputerze. Uruchom następujące polecenie: 
    ```
    MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
    ```

### <a name="on-a-linux-machine"></a>Na komputerze z systemem Linux
1. Na komputerze z systemem Linux zaloguj się jako użytkownik **root.**
2. W terminalu przejdź do /usr/local/ASR.
3. Uruchom następujące polecenie:
    ```
    uninstall.sh -Y
   ```
   
## <a name="install-site-recovery-vss-provider-on-source-machine"></a>Instalowanie dostawcy usługi VSS odzyskiwania lokacji na komputerze źródłowym

Dostawca usługi Azure Site Recovery VSS jest wymagany na komputerze źródłowym do generowania punktów spójności aplikacji. Jeśli instalacja dostawcy nie powiodła się za pomocą instalacji wypychanej, postępuj zgodnie z poniższymi wytycznymi, aby zainstalować go ręcznie.

1. Otwórz okno cmd administratora.
2. Przejdź do lokalizacji instalacji usługi mobilności. (Np. — C:\Pliki programów (x86)\Microsoft Azure Site Recovery\agent)
3. Uruchom skrypt InMageVSSProvider_Uninstall.cmd . Spowoduje to odinstalowanie usługi, jeśli już istnieje.
4. Uruchom skrypt InMageVSSProvider_Install.cmd, aby ręcznie zainstalować dostawcę usługi VSS.

## <a name="next-steps"></a>Następne kroki

- [Konfigurowanie odzyskiwania po awarii dla maszyn wirtualnych VMware](vmware-azure-tutorial.md)
- [Konfigurowanie odzyskiwania po awarii dla serwerów fizycznych](physical-azure-disaster-recovery.md)
