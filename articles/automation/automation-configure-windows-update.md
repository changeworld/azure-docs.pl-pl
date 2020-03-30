---
title: Konfigurowanie ustawień usługi Windows Update do pracy z usługą Azure Update Management
description: W tym artykule opisano ustawienia usługi Windows Update skonfigurowane do pracy z usługą Azure Update Management.
services: automation
ms.subservice: update-management
ms.date: 03/02/2020
ms.topic: conceptual
ms.openlocfilehash: 7f226c4d297d25644b2650d085655f70d8326927
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279028"
---
# <a name="configure-windows-update-settings-for-update-management"></a>Konfigurowanie ustawień usługi Windows Update dla zarządzania aktualizacjami

Usługa Azure Update Management polega na [kliencie usługi Windows Update,](https://docs.microsoft.com//windows/deployment/update/windows-update-overview) aby pobrać i zainstalować aktualizacje systemu Windows. Istnieją określone ustawienia używane przez klienta usługi Windows Update podczas łączenia się z usługami Windows Server Update Services (WSUS) lub Windows Update. Wieloma z tych ustawień można zarządzać za pomocą:

- Edytor lokalnych zasad grupy
- Zasady grupy
- PowerShell
- Bezpośrednia edycja rejestru

Zarządzanie aktualizacjami przestrzega wielu ustawień określonych do kontrolowania klienta usługi Windows Update. Jeśli używasz ustawień do włączania aktualizacji innych niż Windows, zarządzanie aktualizacjami będzie również zarządzać tymi aktualizacjami. Jeśli chcesz włączyć pobieranie aktualizacji przed wdrożeniem aktualizacji, wdrożenie aktualizacji może być szybsze, wydajniejsze i mniej prawdopodobne, aby przekroczyć okno konserwacji.

## <a name="pre-download-updates"></a>Aktualizacje przed pobraniem

Aby skonfigurować automatyczne pobieranie aktualizacji, ale nie instalować ich automatycznie, można użyć zasad grupy, aby ustawić [ustawienie Konfiguruj aktualizacje automatyczne](/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates##configure-automatic-updates) na **3**. To ustawienie umożliwia pobieranie wymaganych aktualizacji w tle i powiadamia, że aktualizacje są gotowe do zainstalowania. W ten sposób zarządzanie aktualizacjami pozostaje pod kontrolą harmonogramów, ale aktualizacje można pobrać poza oknem konserwacja usługi zarządzania aktualizacjami. To zachowanie zapobiega **przekroczenia** błędów w oknie Konserwacja w zarządzania aktualizacjami.

To ustawienie można włączyć za pomocą programu PowerShell, uruchamiając następujące polecenie:

```powershell
$WUSettings = (New-Object -com "Microsoft.Update.AutoUpdate").Settings
$WUSettings.NotificationLevel = 3
$WUSettings.Save()
```

## <a name="configure-reboot-settings"></a>Konfigurowanie ustawień ponownego uruchamiania

Klucze rejestru wymienione w [pozycji Konfigurowanie aktualizacji automatycznych przez edytowanie kluczy rejestru](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry) i [rejestru używanych do zarządzania ponownym uruchomieniem](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) może spowodować ponowne uruchomienie komputerów, nawet jeśli określą opcję **Nigdy nie uruchamiaj ponownie** w ustawieniach wdrażania **aktualizacji.** Skonfiguruj te klucze rejestru, aby najlepiej odpowiadać twojemu środowisku.

## <a name="enable-updates-for-other-microsoft-products"></a>Włączanie aktualizacji dla innych produktów firmy Microsoft

Domyślnie klient usługi Windows Update jest skonfigurowany do dostarczania aktualizacji tylko dla systemu Windows. Jeśli podczas **aktualizowania** ustawień systemu Windows zostanie włączona funkcja Give me updates dla innych produktów firmy Microsoft, otrzymasz również aktualizacje dotyczące innych produktów, w tym poprawek zabezpieczeń dla programu Microsoft SQL Server i innego oprogramowania firmy Microsoft. Tę opcję można skonfigurować, jeśli pobrano i skopiowano najnowsze [pliki szablonów administracyjnych](https://support.microsoft.com/help/3087759/how-to-create-and-manage-the-central-store-for-group-policy-administra) dostępne dla systemu Windows 2016 lub nowszego.

Jeśli korzystasz z systemu Windows Server 2012 R2, to ustawienie nie może być skonfigurowane przez zasady grupy. Uruchom następujące polecenie programu PowerShell na tych komputerach. Zarządzanie aktualizacjami jest zgodne z tym ustawieniem.

```powershell
$ServiceManager = (New-Object -com "Microsoft.Update.ServiceManager")
$ServiceManager.Services
$ServiceID = "7971f918-a847-4430-9279-4a52d1efe18d"
$ServiceManager.AddService2($ServiceId,7,"")
```

## <a name="wsus-configuration-settings"></a>Ustawienia konfiguracji programu WSUS

Usługa Update Management obsługuje ustawienia programu WSUS. Ustawienia programu WSUS, które można skonfigurować do pracy z zarządzaniem aktualizacjami, są wymienione poniżej.

### <a name="intranet-microsoft-update-service-location"></a>Intranet Microsoft update service location Intranet Microsoft update service location Intranet Microsoft update service location Intra

W obszarze Określanie lokalizacji usługi Microsoft Update w obszarze [Określanie lokalizacji usługi intranetowej usługi Microsoft Update](/windows/deployment/update/waas-wu-settings#specify-intranet-microsoft-update-service-location)można określić źródła skanowania i pobierania aktualizacji . Domyślnie klient usługi Windows Update jest skonfigurowany do pobierania aktualizacji z witryny Windows Update. Po określeniu serwera WSUS jako źródła dla komputerów, jeśli aktualizacje nie są zatwierdzone w usłudze WSUS, wdrożenie aktualizacji kończy się niepowodzeniem. 

Aby ograniczyć maszyny tylko do tej wewnętrznej usługi aktualizacji, [skonfiguruj Pozycję Nie łącz się z żadnymi lokalizacjami internetowymi usługi Windows Update](https://docs.microsoft.com/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates#do-not-connect-to-any-windows-update-internet-locations). 

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu ustawień usługi Windows Update można zaplanować wdrożenie aktualizacji, postępując zgodnie z instrukcjami w [temacie Zarządzanie aktualizacjami i poprawkami dla maszyn wirtualnych platformy Azure](automation-tutorial-update-management.md).
