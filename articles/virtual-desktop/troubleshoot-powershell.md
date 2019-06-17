---
title: Wirtualne PowerShell pulpitu Windows — platformy Azure
description: Jak rozwiązywać problemy przy użyciu programu PowerShell podczas konfigurowania środowiska dzierżawy Windows pulpitu wirtualnego.
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: troubleshoot
ms.date: 04/08/2019
ms.author: v-chjenk
ms.openlocfilehash: ad32f7ff883812830dbcf2ed900c4034bd90abfc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64927510"
---
# <a name="windows-virtual-desktop-powershell"></a>Program PowerShell dla usługi Windows Virtual Desktop

Użyj w tym artykule, aby rozwiązać błędy i problemy podczas korzystania z programu PowerShell z usługą Windows pulpitu wirtualnego. Aby uzyskać więcej informacji na temat programu PowerShell usługi pulpitu zdalnego, zobacz [środowiska Powershell pulpitu wirtualnego Windows](https://docs.microsoft.com/powershell/module/windowsvirtualdesktop/).

## <a name="provide-feedback"></a>Przekazywanie opinii

Firma Microsoft obecnie nie są zbyt przypadki pomocy technicznej Windows pulpitu wirtualnego jest dostępna w wersji zapoznawczej. Odwiedź stronę [społeczności technicznej pulpitu wirtualnego Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) w celu omówienia usługi Windows pulpitu wirtualnego przy użyciu zespół pracujący nad produktem i elementów członkowskich aktywnej społeczności użytkowników.

## <a name="powershell-commands-used-during-windows-virtual-desktop-setup"></a>Polecenia programu PowerShell, używane podczas instalacji Windows pulpitu wirtualnego

W tej sekcji przedstawiono polecenia programu PowerShell, które są zwykle używane podczas konfigurowania pulpitu wirtualnego Windows i zawiera sposoby rozwiązania problemów, które mogą wystąpić podczas korzystania z nich.

### <a name="error-add-rdsappgroupuser-command----the-specified-userprincipalname-is-already-assigned-to-a-remoteapp-app-group-in-the-specified-host-pool"></a>Błąd: Polecenie Dodaj RdsAppGroupUser — określony UserPrincipalName jest już przypisany do grupy aplikacji programów RemoteApp w określonej puli hosta

```Powershell
Add-RdsAppGroupUser -TenantName <TenantName> -HostPoolName <HostPoolName> -AppGroupName 'Desktop Application Group' -UserPrincipalName <UserName>
```

**Przyczyna:** Nazwa użytkownika służąca zostały już przypisane do grupy aplikacji innego typu. Nie można przypisać użytkowników do obu grupy zdalnego pulpitu i zdalnej aplikacji w ramach tej samej puli hosta sesji.

**Poprawka:** Jeśli użytkownik potrzebuje aplikacji zdalnych i usług pulpitu zdalnego, tworzenie pul inny host lub udzielić użytkownikowi dostępu do pulpitu zdalnego, który pozwoli na korzystanie z dowolnej aplikacji na hoście sesji maszyny Wirtualnej.

### <a name="error-add-rdsappgroupuser-command----the-specified-userprincipalname-doesnt-exist-in-the-azure-active-directory-associated-with-the-remote-desktop-tenant"></a>Błąd: Polecenie Dodaj RdsAppGroupUser — określony UserPrincipalName nie istnieje w usłudze Azure Active Directory skojarzone z dzierżawcą pulpitu zdalnego

```PowerShell
Add-RdsAppGroupUser -TenantName <TenantName> -HostPoolName <HostPoolName> -AppGroupName “Desktop Application Group” -UserPrincipalName <UserPrincipalName>
```

**Przyczyna:** Nie można odnaleźć użytkownika określonego przez parametr-UserPrincipalName usługi Azure Active Directory, powiązany z dzierżawą Windows pulpitu wirtualnego.

**Poprawka:** Upewnij się, elementy z poniższej listy.

- Użytkownik jest zsynchronizowane z usługą Azure Active Directory.
- Użytkownik nie jest powiązany firma-klient (B2C) lub business-to-business (B2B) commerce.
- Dzierżawy Windows pulpitu wirtualnego jest powiązany poprawne usługi Azure Active Directory.

### <a name="error-get-rdsdiagnosticactivities----user-isnt-authorized-to-query-the-management-service"></a>Błąd: Get-RdsDiagnosticActivities — Użytkownik nie ma uprawnień do zapytań do usługi zarządzania

```PowerShell
Get-RdsDiagnosticActivities -ActivityId <ActivityId>
```

**Przyczyna:** parametru - TenantName

**Poprawka:** Wystawiać Get-RdsDiagnosticActivities przy użyciu - TenantName <TenantName>.

### <a name="error-get-rdsdiagnosticactivities----the-user-isnt-authorized-to-query-the-management-service"></a>Błąd: Get-RdsDiagnosticActivities — użytkownik nie ma uprawnień do zapytań do usługi zarządzania

```PowerShell
Get-RdsDiagnosticActivities -Deployment -username <username>
```

**Przyczyna:** Za pomocą parametru - wdrożenia przełącznika.

**Poprawka:** — wdrożenie przełącznika może służyć tylko przez administratorów wdrożenia. Ci Administratorzy zwykle są członkami zespołu zdalnego pulpitu wirtualnego usług/Windows Desktop. Zastąp przełącznika wdrożenia przy użyciu - TenantName <TenantName>.

### <a name="error-new-rdsroleassignment----the-user-isnt-authorized-to-query-the-management-service"></a>Błąd: Nowe RdsRoleAssignment — użytkownik nie ma uprawnień do zapytań do usługi zarządzania

**Przyczyny 1:** Konto używane, nie ma uprawnienia właściciela usług pulpitu zdalnego w ramach dzierżawy.

**Napraw 1:** Użytkownik z uprawnieniami właściciela usług pulpitu zdalnego musi można wykonać przypisania roli.

**Przyczyny 2:** Konto używane uprawnieniami właściciela usług pulpitu zdalnego, ale nie jest częścią dzierżawy usługi Azure Active Directory lub nie ma uprawnień do wykonywania zapytań usługi Azure Active Directory, gdzie znajduje się użytkownik.

**Napraw 2:** Użytkownik mający uprawnienia usługi Active Directory musi wykonać przypisania roli.

>[!Note]
>Nowe RdsRoleAssignment nie można nadać uprawnienia do użytkownika, który nie istnieje w usłudze Azure Active Directory (AD).

## <a name="next-steps"></a>Kolejne kroki

- Aby uzyskać ogólne na temat rozwiązywania problemów ścieżki eskalacji i pulpitu wirtualnego Windows zobacz [Rozwiązywanie problemów — omówienie, opinie i pomoc techniczna](troubleshoot-set-up-overview.md).
- Aby rozwiązywać problemy podczas tworzenia puli dzierżawy i hosta w środowisku Windows pulpitu wirtualnego, zobacz [dzierżawy i hostów puli tworzenia](troubleshoot-set-up-issues.md).
- Aby rozwiązywać problemy podczas konfigurowania maszyny wirtualnej (VM) w Windows pulpitu wirtualnego, zobacz [konfigurację maszyny wirtualnej hosta sesji](troubleshoot-vm-configuration.md).
- Aby rozwiązywać problemy z połączeniami klienta pulpitu wirtualnego Windows, zobacz [połączeń klienta usług pulpitu zdalnego](troubleshoot-client-connection.md).
- Aby dowiedzieć się więcej na temat usługi w wersji zapoznawczej, zobacz [Windows Desktop w wersji zapoznawczej środowiska](https://docs.microsoft.com/azure/virtual-desktop/environment-setup).
- Aby przejść przez samouczek rozwiązywania problemów, zobacz [samouczka: Rozwiązywanie problemów z wdrożeniami szablonu usługi Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-tutorial-troubleshoot).
- Aby dowiedzieć się więcej na temat inspekcji akcji, zobacz [inspekcji operacji przy użyciu usługi Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).
- Aby dowiedzieć się więcej o akcjach, aby określić błędy podczas wdrażania, zobacz [wyświetlanie operacji wdrażania](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations).