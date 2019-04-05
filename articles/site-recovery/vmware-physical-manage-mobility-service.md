---
title: Zarządzanie agentem mobilności na serwerach do odzyskiwania po awarii maszyn wirtualnych programu VMware i serwerów fizycznych z usługą Azure Site Recovery | Dokumentacja firmy Microsoft
description: Zarządzaj agenta usługi mobilności na potrzeby odzyskiwania po awarii maszyn wirtualnych VMware i serwerów fizycznych do platformy Azure przy użyciu usługi Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: ramamill
ms.openlocfilehash: 69b8e1c533747d1bade69949911ea43f299f49e9
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2019
ms.locfileid: "59043812"
---
# <a name="manage-mobility-agent-on-protected-machines"></a>Zarządzanie agentem mobility na chronionych komputerach

Możesz skonfigurować agenta mobilności na serwerze korzystając z usługi Azure Site Recovery do odzyskiwania po awarii maszyn wirtualnych VMware i serwerów fizycznych na platformę Azure. Agenta mobilności służy do koordynowania komunikacji między chronionej maszyny, serwer przetwarzania serwera/skalowalnego w poziomie konfiguracji oraz do zarządzania replikacją danych. Ten artykuł zawiera podsumowanie typowych zadań zarządzania agenta mobilności po jej wdrożeniu.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="update-mobility-service-from-azure-portal"></a>Aktualizacja usługi mobilności z witryny Azure portal

1. Przed rozpoczęciem upewnij się, że serwer konfiguracji, serwerów przetwarzania skalowalnego w poziomie oraz wszystkie główne serwery docelowe, które są częścią wdrożenia są aktualizowane, przed uaktualnieniem usługi Mobility na chronionych komputerach.
2. W portalu można otworzyć magazynu > **zreplikowane elementy**.
3. Jeśli serwer konfiguracji jest najnowsza wersja, zobaczysz powiadomienie o treści "dostępna jest nowa aktualizacja agenta replikacji lokacji odzyskiwania. Kliknij, aby zainstalować. "

     ![Okno replikowane elementy](./media/vmware-azure-install-mobility-service/replicated-item-notif.png)

4. Kliknij powiadomienie, a następnie w **aktualizacja agenta**, wybierz maszyny, na których chcesz uaktualnić usługę mobilności. Następnie kliknij przycisk **OK**.

     ![Zreplikowane elementy listy maszyn wirtualnych](./media/vmware-azure-install-mobility-service/update-okpng.png)

5. Uruchamia zadanie Aktualizacja usługi mobilności dla każdego z wybranych maszyn.

## <a name="update-mobility-service-through-powershell-script-on-windows-server"></a>Aktualizacja usługi mobilności za pomocą skryptu programu powershell na serwerze Windows

Użyj następującego skryptu do uaktualniania usługi mobilności na serwerze za pomocą polecenia cmdlet powłoki power

```azurepowershell
Update-AzRecoveryServicesAsrMobilityService -ReplicationProtectedItem $rpi -Account $fabric.fabricSpecificDetails.RunAsAccounts[0]
```

## <a name="update-account-used-for-push-installation-of-mobility-service"></a>Aktualizuj konto używane na potrzeby instalacji wypychanej usługi mobilności

Po wdrożeniu Site Recovery, aby umożliwić instalację wypychaną usługi Mobility określono konta używanego przez serwer procesu odzyskiwania lokacji dostęp do maszyny i zainstalować usługę po włączeniu replikacji dla maszyny. Jeśli chcesz zaktualizować poświadczenia dla tego konta, postępuj zgodnie z [w instrukcjach](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation).

## <a name="uninstall-mobility-service"></a>Odinstaluj usługę mobilności

### <a name="on-a-windows-machine"></a>Na komputerze Windows

Odinstaluj z interfejsu użytkownika lub z wiersza polecenia.

- **W interfejsie użytkownika**: W Panelu sterowania na komputerze wybierz **programy**. Wybierz **Microsoft Azure lokacji odzyskiwania mobilności usługi/główny serwer docelowy** > **Odinstaluj**.
- **Z poziomu wiersza polecenia**: Otwórz okno wiersza polecenia z uprawnieniami administratora na komputerze. Uruchom następujące polecenie: 
    ```
    MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
    ```

### <a name="on-a-linux-machine"></a>Na maszynie z systemem Linux
1. Na komputerze z systemem Linux, zaloguj się jako **głównego** użytkownika.
2. W terminalu przejdź do /user/local/ASR.
3. Uruchom następujące polecenie:
    ```
    uninstall.sh -Y

## Install Site Recovery VSS provider on source machine

Azure Site Recovery VSS provider is required on the source machine to generate application consistency points. If the installation of the provider didn't succeed through push installation, follow the below given guidelines to install it manually.

1. Open admin cmd window.
2. Navigate to the mobility service installation location. (Eg - C:\Program Files (x86)\Microsoft Azure Site Recovery\agent)
3. Run the script InMageVSSProvider_Uninstall.cmd . This will uninstall the service if it already exists.
4. Run the script InMageVSSProvider_Install.cmd to install the VSS provider manually.

## Next steps

- [Set up disaster recovery for VMware VMs](vmware-azure-tutorial.md)
- [Set up disaster recovery for physical servers](physical-azure-disaster-recovery.md)
