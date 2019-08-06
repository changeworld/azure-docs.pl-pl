---
title: Tworzenie puli hostów programu Windows Virtual Desktop w wersji zapoznawczej w celu sprawdzenia poprawności aktualizacji usługi — Azure
description: Jak utworzyć pulę hostów weryfikacji do monitorowania aktualizacji usługi przed przeprowadzeniem aktualizacji do środowiska produkcyjnego.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 05/08/2019
ms.author: helohr
ms.openlocfilehash: a23d9fe932556b0a685b373b060901d2c7fb8c85
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2019
ms.locfileid: "68816461"
---
# <a name="tutorial-create-a-host-pool-to-validate-service-updates"></a>Samouczek: Tworzenie puli hostów na potrzeby weryfikacji aktualizacji usług

Pule hostów to zbiór co najmniej jednej identycznej maszyny wirtualnej w środowiskach dzierżawy systemu Windows Virtual Desktop w wersji zapoznawczej. Przed wdrożeniem pul hostów w środowisku produkcyjnym zdecydowanie zalecamy utworzenie puli hostów weryfikacji. Aktualizacje są stosowane najpierw w przypadku pul hostów weryfikacji, umożliwiając monitorowanie aktualizacji usługi przed ich wycofaniem do środowiska produkcyjnego. Bez puli hostów weryfikacji nie można wykryć zmian wprowadzających błędy, co może spowodować przestoje dla użytkowników w środowisku produkcyjnym.

Aby zapewnić, że aplikacje będą działać z najnowszymi aktualizacjami, Pula hostów weryfikacji powinna być podobna do pul hostów w środowisku produkcyjnym, jak to możliwe. Użytkownicy powinni często łączyć się z pulą hostów weryfikacji, tak jak w przypadku puli hostów produkcyjnych. W przypadku automatycznego testowania w puli hostów należy uwzględnić automatyczne testowanie w puli hostów weryfikacji.

Problemy w puli hostów weryfikacji można debugować za pomocą [funkcji diagnostyki](diagnostics-role-service.md) lub artykułów do rozwiązywania [problemów z pulpitem wirtualnym systemu Windows](https://docs.microsoft.com/Azure/virtual-desktop/troubleshoot-set-up-overview).

>[!NOTE]
> Zaleca się pozostawienie puli hostów weryfikacji w celu przetestowania wszystkich przyszłych aktualizacji.

Przed rozpoczęciem [Pobierz i zaimportuj moduł programu PowerShell dla pulpitu wirtualnego systemu Windows](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview), jeśli jeszcze tego nie zrobiono.

## <a name="create-your-host-pool"></a>Tworzenie puli hostów

Pulę hostów można utworzyć, postępując zgodnie z instrukcjami zawartymi w poniższych artykułach:
- [Samouczek: Tworzenie puli hostów za pomocą witryny Azure Marketplace](create-host-pools-azure-marketplace.md)
- [Tworzenie puli hostów przy użyciu szablonu Azure Resource Manager](create-host-pools-arm-template.md)
- [Tworzenie puli hostów przy użyciu programu PowerShell](create-host-pools-powershell.md)

## <a name="define-your-host-pool-as-a-validation-host-pool"></a>Definiowanie puli hostów jako puli hostów weryfikacji

Uruchom następujące polecenia cmdlet programu PowerShell, aby zdefiniować nową pulę hostów jako pulę hostów weryfikacji. Zamień wartości w cudzysłowy przez wartości odpowiednie dla danej sesji:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
Set-RdsHostPool -TenantName $myTenantName -Name "contosoHostPool" -ValidationEnv $true
```

Uruchom następujące polecenie cmdlet programu PowerShell, aby upewnić się, że właściwość walidacji została ustawiona. Zamień wartości w cudzysłowy przez wartości odpowiednie dla danej sesji.

```powershell
Get-RdsHostPool -TenantName $myTenantName -Name "contosoHostPool" -ValidationEnv $true
```

Wyniki polecenia cmdlet powinny wyglądać podobnie do tych danych wyjściowych:

```
    TenantName          : contoso 
    TenantGroupName     : Default Tenant Group
    HostPoolName        : contosoHostPool
    FriendlyName        :
    Description         :
    Persistent          : False 
    CustomRdpProperty   : use multimon:i:0;
    MaxSessionLimit     : 10
    LoadBalancerType    : BreadthFirst
    ValidationEnv       : True
    Ring                :
```

## <a name="update-schedule"></a>Harmonogram aktualizacji

W wersji zapoznawczej aktualizacje usługi są wykonywane w około erze miesięcznie. W przypadku wystąpienia poważnych problemów aktualizacje krytyczne będą udostępniane w sposób bardziej częsty erze.

## <a name="next-steps"></a>Kolejne kroki

Po utworzeniu puli hostów weryfikacji można dowiedzieć się, jak wdrożyć narzędzie do zarządzania i połączyć się z nim w celu zarządzania zasobami klasycznymi firmy Microsoft.

> [!div class="nextstepaction"]
> [Samouczek wdrażania narzędzia do zarządzania](./manage-resources-using-ui.md)
