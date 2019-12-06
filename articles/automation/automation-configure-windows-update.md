---
title: Konfigurowanie ustawień Windows Update do pracy z platformą Azure Update Management
description: W tym artykule opisano ustawienia Windows Update, które można skonfigurować do pracy z usługą Azure Update Management.
services: automation
ms.service: automation
ms.subservice: update-management
author: mgoedtel
ms.author: magoedte
ms.date: 10/02/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 804f42121293e142cf77ad73c4aab36e62e3242d
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850418"
---
# <a name="configure-windows-update-settings-for-update-management"></a>Skonfiguruj ustawienia Windows Update dla Update Management

Usługa Azure Update Management opiera się na Windows Update do pobrania i zainstalowania aktualizacji systemu Windows. W związku z tym Update Management uwzględnia wiele ustawień używanych przez Windows Update. W przypadku korzystania z ustawień w celu włączenia aktualizacji innych niż Windows program Update Management również będzie zarządzać tymi aktualizacjami. Jeśli chcesz włączyć pobieranie aktualizacji przed wystąpieniem wdrożenia aktualizacji, wdrożenie aktualizacji może być szybsze, wydajniejsze i mniejsze niż okno obsługi.

## <a name="pre-download-updates"></a>Aktualizacje przed pobraniem

Aby skonfigurować automatyczne pobieranie aktualizacji w zasady grupy, ustaw [ustawienie Konfiguruj aktualizacje automatyczne](/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates##configure-automatic-updates) na **3**. To ustawienie włącza pobieranie wymaganych aktualizacji w tle, ale nie instaluje ich. W ten sposób Update Management pozostaje w kontroli nad harmonogramami, ale aktualizacje można pobrać poza oknem obsługi Update Management. To zachowanie zapobiega błędom "Przekroczono okno obsługi" w Update Management.

To ustawienie można również włączyć, uruchamiając następujące polecenie programu PowerShell w systemie, który ma zostać skonfigurowany do pobierania aktualizacji:

```powershell
$WUSettings = (New-Object -com "Microsoft.Update.AutoUpdate").Settings
$WUSettings.NotificationLevel = 3
$WUSettings.Save()
```

## <a name="disable-automatic-installation"></a>Wyłącz instalację automatyczną

Domyślnie na maszynach wirtualnych platformy Azure jest włączona automatyczna instalacja aktualizacji. Może to spowodować zainstalowanie aktualizacji przed zaplanowaniem ich instalacji przez Update Management. To zachowanie można wyłączyć, ustawiając klucz rejestru `NoAutoUpdate` na `1`. Poniższy fragment kodu programu PowerShell pokazuje, jak to zrobić:

```powershell
$AutoUpdatePath = "HKLM:SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU"
Set-ItemProperty -Path $AutoUpdatePath -Name NoAutoUpdate -Value 1
```

## <a name="configure-reboot-settings"></a>Konfigurowanie ustawień ponownego uruchamiania

Klucze rejestru wymienione w temacie [Konfigurowanie aktualizacji automatycznych przez edycję rejestru](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry) i [kluczy rejestru używanych do zarządzania ponownym uruchomieniem](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) mogą spowodować ponowne uruchomienie maszyn, nawet jeśli w ustawieniach **wdrożenia aktualizacji** określono opcję **nigdy nie uruchamiaj ponownie** . Te klucze rejestru należy skonfigurować w taki sposób, aby najlepiej odpowiadały Twojemu środowisku.

## <a name="enable-updates-for-other-microsoft-products"></a>Włącz aktualizacje dla innych produktów firmy Microsoft

Domyślnie Windows Update udostępnia aktualizacje tylko dla systemu Windows. Jeśli włączysz ustawienie **Chcę otrzymywać aktualizacje dla innych produktów firmy Microsoft podczas aktualizacji systemu Windows** , uzyskasz także aktualizacje dla innych produktów, w tym poprawki zabezpieczeń dla Microsoft SQL Server i innego oprogramowania firmy Microsoft. Tej opcji nie można skonfigurować za pomocą zasady grupy. Uruchom następujące polecenie programu PowerShell w systemach, w których chcesz włączyć inne aktualizacje firmy Microsoft. Update Management będzie zgodne z tym ustawieniem.

```powershell
$ServiceManager = (New-Object -com "Microsoft.Update.ServiceManager")
$ServiceManager.Services
$ServiceID = "7971f918-a847-4430-9279-4a52d1efe18d"
$ServiceManager.AddService2($ServiceId,7,"")
```

## <a name="wsus-configuration-settings"></a>Ustawienia konfiguracji programu WSUS

Update Management jest zgodny z ustawieniami Windows Server Update Services (WSUS). Ustawienia programu WSUS, które można skonfigurować do pracy z Update Management są wymienione poniżej.

### <a name="intranet-microsoft-update-service-location"></a>Lokalizacja intranetowej usługi aktualizacji firmy Microsoft

Możesz określić źródła do skanowania i pobierania aktualizacji w obszarze [Określ lokalizację intranetową Microsoft Update usługi](/windows/deployment/update/waas-wu-settings#specify-intranet-microsoft-update-service-location).

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu ustawień Windows Update można zaplanować wdrożenie aktualizacji, postępując zgodnie z instrukcjami w temacie [Zarządzanie aktualizacjami i poprawkami dla maszyn wirtualnych platformy Azure](automation-tutorial-update-management.md).
