---
title: Program Windows Virtual Desktop PowerShell — platforma Azure
description: Jak rozwiązywać problemy z programem PowerShell podczas konfigurowania środowiska dzierżawy pulpitu wirtualnego systemu Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 04/08/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 3fb5436c2b5c30c5336385792d0597bdcea2b538
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127470"
---
# <a name="windows-virtual-desktop-powershell"></a>Program PowerShell dla usługi Windows Virtual Desktop

Ten artykuł służy do rozwiązywania problemów podczas korzystania z programu PowerShell z pulpitem wirtualnym systemu Windows. Aby uzyskać więcej informacji na temat usług pulpitu zdalnego programu PowerShell, zobacz [Program Windows Virtual Desktop Powershell](/powershell/module/windowsvirtualdesktop/).

## <a name="provide-feedback"></a>Przekazywanie opinii

Odwiedź [społeczność techniczną pulpitu wirtualnego systemu Windows,](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) aby omówić usługę Pulpit wirtualny systemu Windows z zespołem produktu i aktywnymi członkami społeczności.

## <a name="powershell-commands-used-during-windows-virtual-desktop-setup"></a>Polecenia programu PowerShell używane podczas konfigurowania pulpitu wirtualnego systemu Windows

W tej sekcji wymieniono polecenia programu PowerShell, które są zwykle używane podczas konfigurowania pulpitu wirtualnego systemu Windows, i przedstawiono sposoby rozwiązywania problemów, które mogą wystąpić podczas korzystania z nich.

### <a name="error-add-rdsappgroupuser-command----the-specified-userprincipalname-is-already-assigned-to-a-remoteapp-app-group-in-the-specified-host-pool"></a>Błąd: Polecenie Add-RdsAppGroupUser — określona nazwa użytkownika jest już przypisana do grupy aplikacji RemoteApp w określonej puli hostów

```Powershell
Add-RdsAppGroupUser -TenantName <TenantName> -HostPoolName <HostPoolName> -AppGroupName 'Desktop Application Group' -UserPrincipalName <UserName>
```

**Przyczyna:** Użyta nazwa użytkownika została już przypisana do grupy aplikacji innego typu. Nie można przypisać użytkowników zarówno do grupy pulpitu zdalnego, jak i zdalnej aplikacji w tej samej puli hostów sesji.

**Poprawka:** Jeśli użytkownik potrzebuje zarówno aplikacji zdalnych, jak i pulpitu zdalnego, utwórz różne pule hostów lub udziel użytkownikowi dostępu do pulpitu zdalnego, co umożliwi użycie dowolnej aplikacji na maszynie wirtualnej hosta sesji.

### <a name="error-add-rdsappgroupuser-command----the-specified-userprincipalname-doesnt-exist-in-the-azure-active-directory-associated-with-the-remote-desktop-tenant"></a>Błąd: polecenie Add-RdsAppGroupUser — określona nazwa użytkownika nie istnieje w usłudze Azure Active Directory skojarzonej z dzierżawą pulpitu zdalnego

```PowerShell
Add-RdsAppGroupUser -TenantName <TenantName> -HostPoolName <HostPoolName> -AppGroupName "Desktop Application Group" -UserPrincipalName <UserPrincipalName>
```

**Przyczyna:** Nie można odnaleźć użytkownika określonego przez —UserPrincipalName w usłudze Azure Active Directory powiązanej z dzierżawą pulpitu wirtualnego systemu Windows.

**Poprawka:** Potwierdź elementy na poniższej liście.

- Użytkownik jest zsynchronizowany z usługą Azure Active Directory.
- Użytkownik nie jest powiązany z biznesem z konsumentami (B2C) ani handlem między przedsiębiorstwami (B2B).
- Dzierżawa pulpitu wirtualnego systemu Windows jest powiązana z poprawną usługą Azure Active Directory.

### <a name="error-get-rdsdiagnosticactivities----user-isnt-authorized-to-query-the-management-service"></a>Błąd: Get-RdsDiagnosticActivities — użytkownik nie jest upoważniony do wykonywania zapytań o usługę zarządzania

```PowerShell
Get-RdsDiagnosticActivities -ActivityId <ActivityId>
```

**Przyczyna:** -Parametr TenantName

**Poprawka:** Problem Get-RdsDiagnosticActivities z -TenantName \<TenantName>.

### <a name="error-get-rdsdiagnosticactivities----the-user-isnt-authorized-to-query-the-management-service"></a>Błąd: Get-RdsDiagnosticActivities — użytkownik nie jest upoważniony do wykonywania zapytań do usługi zarządzania

```PowerShell
Get-RdsDiagnosticActivities -Deployment -username <username>
```

**Przyczyna:** Za pomocą przełącznika -Deployment.

**Poprawka:** -Przełącznik wdrażania może być używany tylko przez administratorów wdrażania. Administratorzy ci są zazwyczaj członkami zespołu Usług pulpitu zdalnego/pulpitu wirtualnego systemu Windows. Zastąp przełącznik -Deployment \<na -TenantName TenantName>.

### <a name="error-new-rdsroleassignment----the-user-isnt-authorized-to-query-the-management-service"></a>Błąd: New-RdsRoleAssignment - użytkownik nie jest upoważniony do wykonywania zapytań do usługi zarządzania

**Przyczyna 1:** Używane konto nie ma uprawnień właściciela usług pulpitu zdalnego w dzierżawie.

**Poprawka 1:** Użytkownik z uprawnieniami właściciela usług pulpitu zdalnego musi wykonać przypisanie roli.

**Przyczyna 2:** Używane konto ma uprawnienia właściciela usług pulpitu zdalnego, ale nie jest częścią usługi Azure Active Directory dzierżawy lub nie ma uprawnień do wykonywania zapytań o usługę Azure Active Directory, w której znajduje się użytkownik.

**Poprawka 2:** Użytkownik z uprawnieniami usługi Active Directory musi wykonać przypisanie roli.

>[!Note]
>New-RdsRoleAssignment nie może udzielić uprawnień użytkownikowi, który nie istnieje w usłudze Azure Active Directory (AD).

## <a name="next-steps"></a>Następne kroki

- Aby zapoznać się z omówieniem rozwiązywania problemów z pulpitem wirtualnym systemu Windows i ścieżkami eskalacji, zobacz [Omówienie rozwiązywania problemów, opinie i pomoc techniczna](troubleshoot-set-up-overview.md).
- Aby rozwiązać problemy podczas tworzenia puli dzierżawy i hosta w środowisku pulpitu wirtualnego systemu Windows, zobacz [Tworzenie puli dzierżawy i hosta](troubleshoot-set-up-issues.md).
- Aby rozwiązać problemy podczas konfigurowania maszyny wirtualnej (VM) na pulpicie wirtualnym systemu Windows, zobacz [Konfiguracja maszyny wirtualnej hosta sesji](troubleshoot-vm-configuration.md).
- Aby rozwiązać problemy z połączeniami klientów pulpitu wirtualnego systemu Windows, zobacz [Połączenia usługi pulpitu wirtualnego systemu Windows](troubleshoot-service-connection.md).
- Aby rozwiązać problemy z klientami usług pulpitu zdalnego, zobacz [Rozwiązywanie problemów z klientem pulpitu zdalnego](troubleshoot-client.md)
- Aby dowiedzieć się więcej o usłudze, zobacz [Środowisko pulpitu wirtualnego systemu Windows](environment-setup.md).
- Aby przejść przez samouczek rozwiązywania problemów, zobacz [Samouczek: Rozwiązywanie problemów z wdrażaniem szablonów Menedżera zasobów](../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
- Aby dowiedzieć się więcej o akcjach inspekcji, zobacz [Inspekcja operacji za pomocą Menedżera zasobów](../azure-resource-manager/management/view-activity-logs.md).
- Aby dowiedzieć się więcej o akcjach w celu określenia błędów podczas wdrażania, zobacz [Wyświetlanie operacji wdrażania](../azure-resource-manager/templates/deployment-history.md).
