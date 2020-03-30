---
title: Rozwiązywanie problemów z połączeniem usługi Pulpit wirtualny systemu Windows — platforma Azure
description: Jak rozwiązać problemy podczas konfigurowania połączeń klientów w środowisku dzierżawy pulpitu wirtualnego systemu Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 12/13/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 57d5198cb54dc096fb09bb52d76539b1e4bbc1f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127446"
---
# <a name="windows-virtual-desktop-service-connections"></a>Połączenia usługi pulpitu wirtualnego systemu Windows

Ten artykuł służy do rozwiązywania problemów z połączeniami klientów pulpitu wirtualnego systemu Windows.

## <a name="provide-feedback"></a>Przekazywanie opinii

Możesz przekazać nam opinię i omówić usługę pulpitu wirtualnego systemu Windows z zespołem produktu i innymi aktywnymi członkami społeczności w [społeczności windows virtual desktop tech.](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)

## <a name="user-connects-but-nothing-is-displayed-no-feed"></a>Użytkownik łączy się, ale nic nie jest wyświetlane (bez źródła)

Użytkownik może uruchomić klientów pulpitu zdalnego i jest w stanie uwierzytelnić, jednak użytkownik nie widzi żadnych ikon w kanale odnajdywania sieci Web.

Upewnij się, że użytkownik zgłaszający problemy został przypisany do grup aplikacji za pomocą tego wiersza polecenia:

```PowerShell
Get-RdsAppGroupUser <tenantname> <hostpoolname> <appgroupname>
```

Upewnij się, że użytkownik loguje się przy użyciu poprawnych poświadczeń.

Jeśli klient sieci web jest używany, upewnij się, że nie ma żadnych problemów z poświadczeniami w pamięci podręcznej.

## <a name="windows-10-enterprise-multi-session-virtual-machines-dont-respond"></a>Wielosesyjne maszyny wirtualne systemu Windows 10 Enterprise nie odpowiadają

Jeśli maszyna wirtualna nie reaguje i nie można uzyskać do niej dostępu za pośrednictwem protokołu RDP, należy rozwiązać problem z funkcją diagnostyki, sprawdzając stan hosta.

Aby sprawdzić stan hosta, uruchom polecenie cmdlet:

```powershell
Get-RdsSessionHost -TenantName $TenantName -HostPoolName $HostPool | ft SessionHostName, LastHeartBeat, AllowNewSession, Status
```

Jeśli stan hosta jest, `NoHeartBeat`oznacza to, że maszyna wirtualna nie odpowiada i agent nie może komunikować się z usługą pulpitu wirtualnego systemu Windows.

```powershell
SessionHostName          LastHeartBeat     AllowNewSession    Status 
---------------          -------------     ---------------    ------ 
WVDHost1.contoso.com     21-Nov-19 5:21:35            True     Available 
WVDHost2.contoso.com     21-Nov-19 5:21:35            True     Available 
WVDHost3.contoso.com     21-Nov-19 5:21:35            True     NoHeartBeat 
WVDHost4.contoso.com     21-Nov-19 5:21:35            True     NoHeartBeat 
WVDHost5.contoso.com     21-Nov-19 5:21:35            True     NoHeartBeat 
```

Istnieje kilka rzeczy, które możesz zrobić, aby naprawić status NoHeartBeat.

### <a name="update-fslogix"></a>Aktualizacja FSLogix

Jeśli FSLogix nie jest aktualny, zwłaszcza jeśli jest to wersja 2.9.7205.27375 frxdrvvt.sys, może to spowodować zakleszczenie. Pamiętaj, aby [zaktualizować FSLogix do najnowszej wersji](https://go.microsoft.com/fwlink/?linkid=2084562).

### <a name="disable-bgtaskregistrationmaintenancetask"></a>Wyłącz bgtaskregistrationMaintenanceTask

Jeśli aktualizowanie FSLogix nie działa, problem może być, że składnik BiSrv jest wyczerpanie zasobów systemowych podczas cotygodniowego zadania konserwacji. Tymczasowo wyłącz zadanie konserwacji, wyłączając bgTaskRegistrationMaintenanceTask za pomocą jednej z tych dwóch metod:

- Przejdź do menu Start i wyszukaj **harmonogram zadań**. Przejdź do **biblioteki harmonogramu** > zadań**Microsoft** > **Windows** > **BrokerInfrastruktura**. Poszukaj zadania o nazwie **BgTaskRegistrationMaintenanceTask**. Po znalezieniu go kliknij prawym przyciskiem myszy i wybierz **polecenie Wyłącz** z menu rozwijanego.
- Otwórz menu wiersza polecenia jako administrator i uruchom następujące polecenie:
    
    ```cmd
    schtasks /change /tn "\Microsoft\Windows\BrokerInfrastructure\BgTaskRegistrationMaintenanceTask" /disable 
    ```

## <a name="next-steps"></a>Następne kroki

- Aby zapoznać się z omówieniem rozwiązywania problemów z pulpitem wirtualnym systemu Windows i ścieżkami eskalacji, zobacz [Omówienie rozwiązywania problemów, opinie i pomoc techniczna](troubleshoot-set-up-overview.md).
- Aby rozwiązać problemy podczas tworzenia puli dzierżawy i hosta w środowisku pulpitu wirtualnego systemu Windows, zobacz [Tworzenie puli dzierżawy i hosta](troubleshoot-set-up-issues.md).
- Aby rozwiązać problemy podczas konfigurowania maszyny wirtualnej (VM) na pulpicie wirtualnym systemu Windows, zobacz [Konfiguracja maszyny wirtualnej hosta sesji](troubleshoot-vm-configuration.md).
- Aby rozwiązać problemy podczas korzystania z programu PowerShell z pulpitem wirtualnym systemu Windows, zobacz [Pulpit wirtualny systemu Windows PowerShell](troubleshoot-powershell.md).
- Aby przejść przez samouczek rozwiązywania problemów, zobacz [Samouczek: Rozwiązywanie problemów z wdrażaniem szablonów Menedżera zasobów](../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
