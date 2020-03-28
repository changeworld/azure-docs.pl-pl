---
title: Aktualizacje usługi puli hostów pulpitu wirtualnego systemu Windows — platforma Azure
description: Jak utworzyć pulę hostów sprawdzania poprawności, aby monitorować aktualizacje usługi przed wprowadzeniem aktualizacji do produkcji.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 03/13/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: f2b51213dfc6d7e55f76e78b92d12111f84736be
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79365393"
---
# <a name="tutorial-create-a-host-pool-to-validate-service-updates"></a>Samouczek: Tworzenie puli hostów w celu sprawdzania poprawności aktualizacji usługi

Pule hostów to zbiór co najmniej jednej identycznej maszyny wirtualnej w środowiskach dzierżawy pulpitu wirtualnego systemu Windows. Przed wdrożeniem pul hostów w środowisku produkcyjnym zdecydowanie zaleca się utworzenie puli hostów sprawdzania poprawności. Aktualizacje są stosowane najpierw do pul hostów hostów sprawdzania poprawności, co pozwala monitorować aktualizacje usługi przed wprowadzeniem ich do środowiska produkcyjnego. Bez puli hostów sprawdzania poprawności nie można wykryć zmian, które wprowadzają błędy, co może spowodować przestoje dla użytkowników w środowisku produkcyjnym.

Aby upewnić się, że aplikacje działają z najnowszymi aktualizacjami, pula hostów sprawdzania poprawności powinna być tak podobna do pul hostów w środowisku produkcyjnym, jak to możliwe. Użytkownicy powinni łączyć się tak często z pulą hosta sprawdzania poprawności, jak z pulą hostów produkcyjnych. Jeśli masz automatyczne testowanie w puli hostów, należy uwzględnić automatyczne testowanie w puli hostów sprawdzania poprawności.

Problemy z debugowaniem w puli hostów sprawdzania poprawności można za pomocą [funkcji diagnostyki](diagnostics-role-service.md) lub [artykułów dotyczących rozwiązywania problemów z pulpitem wirtualnym systemu Windows](troubleshoot-set-up-overview.md).

>[!NOTE]
> Zaleca się pozostawienie puli hostów sprawdzania poprawności w celu przetestowania wszystkich przyszłych aktualizacji.

Przed rozpoczęciem [pobierz i zaimportuj moduł Programu PowerShell pulpitu wirtualnego systemu Windows](/powershell/windows-virtual-desktop/overview/), jeśli jeszcze tego nie zrobiłeś. Następnie uruchom następujące polecenie cmdlet, aby zalogować się na swoje konto:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="create-your-host-pool"></a>Tworzenie puli hostów

Pulę hosta można utworzyć, postępując zgodnie z instrukcjami zawartymi w dowolnym z następujących artykułów:
- [Samouczek: Tworzenie puli hostów w portalu Azure Marketplace](create-host-pools-azure-marketplace.md)
- [Tworzenie puli hostów za pomocą szablonu usługi Azure Resource Manager](create-host-pools-arm-template.md)
- [Tworzenie puli hostów przy użyciu programu PowerShell](create-host-pools-powershell.md)

## <a name="define-your-host-pool-as-a-validation-host-pool"></a>Definiowanie puli hostów jako puli hostów sprawdzania poprawności

Uruchom następujące polecenia cmdlet programu PowerShell, aby zdefiniować nową pulę hostów jako pulę hostów sprawdzania poprawności. Zastąp wartości w cudzysłowie wartościami istotnymi dla sesji:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
Set-RdsHostPool -TenantName $myTenantName -Name "contosoHostPool" -ValidationEnv $true
```

Uruchom następujące polecenie cmdlet programu PowerShell, aby potwierdzić, że właściwość sprawdzania poprawności została ustawiona. Zastąp wartości w cudzysłowie wartościami odpowiednimi dla sesji.

```powershell
Get-RdsHostPool -TenantName $myTenantName -Name "contosoHostPool"
```

Wyniki polecenia cmdlet powinny wyglądać podobnie do tego wyjścia:

```
    TenantName          : contoso 
    TenantGroupName     : Default Tenant Group
    HostPoolName        : contosoHostPool
    FriendlyName        :
    Description         :
    Persistent          : False 
    CustomRdpProperty    : use multimon:i:0;
    MaxSessionLimit     : 10
    LoadBalancerType    : BreadthFirst
    ValidationEnv       : True
    Ring                :
```

## <a name="update-schedule"></a>Harmonogram aktualizacji

Aktualizacje usługi są aktualizowane co miesiąc. Jeśli występują poważne problemy, krytyczne aktualizacje będą dostarczane w częstszym tempie.

## <a name="next-steps"></a>Następne kroki

Po utworzeniu puli hostów sprawdzania poprawności możesz dowiedzieć się, jak monitorować wdrażanie pulpitu wirtualnego systemu Windows za pomocą usługi Azure Service Health. 

> [!div class="nextstepaction"]
> [Konfigurowanie alertów usługi](./set-up-service-alerts.md)
