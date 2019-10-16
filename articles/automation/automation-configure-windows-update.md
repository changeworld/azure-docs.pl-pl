---
title: Konfigurowanie ustawień Windows Update do pracy z platformą Azure Update Management
description: W tym artykule opisano ustawienia Windows Update skonfigurowane do pracy z usługą Update Management
services: automation
ms.service: automation
ms.subservice: update-management
author: bobbytreed
ms.author: robreed
ms.date: 10/02/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: f50ca9515f12e8c9b5943904c4d0226f2ca3353c
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2019
ms.locfileid: "72377566"
---
# <a name="configure-windows-update-settings-for-update-management"></a>Skonfiguruj ustawienia Windows Update dla Update Management

Update Management polega na Windows Update do pobrania i zainstalowania aktualizacji systemu Windows. W związku z tym szanujemy wiele ustawień używanych przez Windows Update. Jeśli używasz ustawień do włączania aktualizacji innych niż Windows, Update Management będą również zarządzać tymi aktualizacjami. Jeśli chcesz włączyć pobieranie aktualizacji przed wystąpieniem wdrożenia aktualizacji, wdrożenia aktualizacji mogą być szybsze i mniejsze niż przekroczenie okna obsługi.

## <a name="pre-download-updates"></a>Pobierz wstępnie aktualizacje

Aby skonfigurować automatyczne pobieranie aktualizacji w zasady grupy, można ustawić [ustawienie Konfiguruj aktualizacje automatyczne](/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates##configure-automatic-updates) na **3**. Spowoduje to pobranie aktualizacji wymaganych w tle, ale nie ich zainstalowanie. Pozwala to zachować Update Management kontrolę nad harmonogramami, ale umożliwia pobieranie aktualizacji poza oknem obsługi Update Management. Może to uniemożliwić **przekroczenie przez okno obsługi** błędów w Update Management.

Możesz również ustawić ten program przy użyciu programu PowerShell, uruchom następujące polecenie programu PowerShell w systemie, który ma pobierać aktualizacje do pobrania.

```powershell
$WUSettings = (New-Object -com "Microsoft.Update.AutoUpdate").Settings
$WUSettings.NotificationLevel = 3
$WUSettings.Save()
```

## <a name="disable-automatic-installation"></a>Wyłącz instalację automatyczną

Automatyczne instalowanie aktualizacji maszyn wirtualnych platformy Azure jest domyślnie włączone. Może to spowodować zainstalowanie aktualizacji przed zaplanowaniem ich instalacji przez Update Management. To zachowanie można wyłączyć przez ustawienie klucza rejestru `NoAutoUpdate` na `1`. Poniższy fragment kodu programu PowerShell pokazuje, jak to zrobić.

```powershell
$AutoUpdatePath = "HKLM:SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU"
Set-ItemProperty -Path $AutoUpdatePath -Name NoAutoUpdate -Value 1
```

## <a name="configure-reboot-settings"></a>Konfigurowanie ustawień ponownego uruchamiania

Klucze rejestru wyświetlane w obszarze [Konfigurowanie aktualizacji automatycznych przez edycję rejestru](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-rej7uijui7jgistry) i [kluczy rejestru używanych do zarządzania ponownym uruchomieniem](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) mogą spowodować ponowne uruchomienie maszyn, nawet jeśli w ustawieniach wdrożenia aktualizacji określono opcję **nigdy nie uruchamiaj ponownie** . Należy skonfigurować te klucze rejestru jako odpowiednie dla danego środowiska.

## <a name="enable-updates-for-other-microsoft-products"></a>Włącz aktualizacje dla innych produktów firmy Microsoft

Domyślnie Windows Update udostępnia tylko aktualizacje dla systemu Windows. Jeśli włączysz opcję **Udostępnij aktualizacje dla innych produktów firmy Microsoft, gdy zaktualizuję system Windows**, masz aktualizacje dla innych produktów, w tym poprawki zabezpieczeń dla SQL Server lub oprogramowania innych firm. Tej opcji nie można skonfigurować za pomocą zasady grupy. Uruchom następujące polecenie programu PowerShell w systemach, na których chcesz włączyć inne poprawki pierwszej strony, a Update Management będzie przestrzegać tego ustawienia.

```powershell
$ServiceManager = (New-Object -com "Microsoft.Update.ServiceManager")
$ServiceManager.Services
$ServiceID = "7971f918-a847-4430-9279-4a52d1efe18d"
$ServiceManager.AddService2($ServiceId,7,"")
```

## <a name="wsus-configuration-settings"></a>Ustawienia konfiguracji programu WSUS

**Update Management** przestrzega ustawień konfiguracji usług WSUS. Poniżej wymieniono listę ustawień programu WSUS, które można skonfigurować do pracy z Update Management.

### <a name="intranet-microsoft-update-service-location"></a>Lokalizacja intranetowej usługi aktualizacji firmy Microsoft

Możesz określić źródła do skanowania i pobierania aktualizacji w obszarze [Intranet Microsoft Update lokalizację usługi](/windows/deployment/update/waas-wu-settings#specify-intranet-microsoft-update-service-location).

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu ustawień Windows Update można zaplanować wdrożenie aktualizacji, postępując zgodnie z instrukcjami w sekcji [Zarządzanie aktualizacjami i poprawkami dla maszyn wirtualnych platformy Azure](automation-tutorial-update-management.md)