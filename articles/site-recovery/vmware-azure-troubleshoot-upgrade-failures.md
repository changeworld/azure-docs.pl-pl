---
title: Rozwiązywanie problemów z błędami uaktualniania dostawcy Site Recovery Microsoft Azure | Microsoft Docs
description: Zrozumienie i
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: troubleshooting
ms.date: 02/05/2019
ms.author: raynew
ms.openlocfilehash: 6bb8263e7291506be6af7d5af7eda5786274d73c
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2019
ms.locfileid: "71972618"
---
# <a name="troubleshoot-microsoft-azure-site-recovery-provider-upgrade-failures"></a>Rozwiązywanie problemów z błędami uaktualniania dostawcy Microsoft Azure Site Recovery

Ten artykuł pomaga w rozwiązywaniu problemów, które mogą powodować błędy podczas uaktualniania dostawcy Site Recovery Microsoft Azure.

## <a name="the-upgrade-fails-reporting-that-the-latest-site-recovery-provider-is-already-installed"></a>Uaktualnienie nie powiedzie się, że jest już zainstalowany najnowszy dostawca Site Recovery

W przypadku uaktualniania Microsoft Azure Site Recovery dostawcy (DRA) ujednolicone Uaktualnienie Instalatora zakończy się niepowodzeniem i wygeneruje komunikat o błędzie:

Uaktualnianie nie jest obsługiwane, ponieważ jest już zainstalowana nowsza wersja oprogramowania.

Aby przeprowadzić uaktualnienie, wykonaj następujące czynności:

1. Pobierz Microsoft Azure Site Recovery ujednoliconą konfigurację:
   1. W sekcji "linki do obecnie obsługiwanych pakietów zbiorczych aktualizacji" [aktualizacji usługi w Azure Site Recovery](service-updates-how-to.md##links-to-currently-supported-update-rollups) artykule wybierz dostawcę, do którego chcesz przeprowadzić uaktualnienie.
   2. Na stronie zbiorczej Znajdź sekcję **Informacje o aktualizacji** i Pobierz pakiet zbiorczy aktualizacji dla Microsoft Azure Site Recovery Unified Setup.

2. Otwórz wiersz polecenia i przejdź do folderu, do którego został pobrany plik ujednoliconej konfiguracji. Wyodrębnij pliki Instalatora z pobrania przy użyciu następującego polecenia, MicrosoftAzureSiteRecoveryUnifiedSetup. exe/q/x: &lt;folder Path dla wyodrębnionych plików @ no__t-1.
    
    Przykładowe polecenie:

    MicrosoftAzureSiteRecoveryUnifiedSetup. exe/q/x: C:\Temp\Extracted

3. W wierszu polecenia przejdź do folderu, do którego wyodrębniono pliki, i uruchom następujące polecenia instalacji:
   
    CX_THIRDPARTY_SETUP. EXE/VERYSILENT/SUPPRESSMSGBOXES/NORESTART UCX_SERVER_SETUP. EXE/VERYSILENT/SUPPRESSMSGBOXES/NORESTART/UPGRADE

1. Wróć do folderu, do którego pobrano ujednoliconą konfigurację, i uruchom MicrosoftAzureSiteRecoveryUnifiedSetup. exe, aby zakończyć proces uaktualniania. 

## <a name="upgrade-failure-due-to-the-3rd-party-folder-being-renamed"></a>Niepowodzenie uaktualniania z powodu zmiany nazwy folderu innej firmy

Aby uaktualnienie zakończyło się pomyślnie, nie można zmienić nazwy folderu innej firmy.

Aby rozwiązać ten problem.

1. Uruchom Edytor rejestru (regedit. exe) i Otwórz gałąź HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\InMage Systems\Installed Products\10.
1. Sprawdź wartość klucza `Build_Version`. Jeśli jest ustawiona na najnowszą wersję, Zmniejsz numer wersji. Na przykład jeśli Najnowsza wersja to 9,22. \* i ustawionym kluczem `Build_Version` na tę wartość, Zmniejsz ją do 9,21. \*.
1. Pobierz najnowszą Microsoft Azure Site Recovery ujednoliconą konfigurację:
   1. W sekcji "linki do obecnie obsługiwanych pakietów zbiorczych aktualizacji" [aktualizacji usługi w Azure Site Recovery](service-updates-how-to.md##links-to-currently-supported-update-rollups) artykule wybierz dostawcę, do którego chcesz przeprowadzić uaktualnienie.
   2. Na stronie zbiorczej Znajdź sekcję **Informacje o aktualizacji** i Pobierz pakiet zbiorczy aktualizacji dla Microsoft Azure Site Recovery Unified Setup.
1. Otwórz wiersz polecenia i przejdź do folderu, do którego pobrano plik ujednoliconej konfiguracji i Wyodrębnij pliki instalacji z pobrania przy użyciu następującego polecenia, MicrosoftAzureSiteRecoveryUnifiedSetup. exe/q/x: &lt;folder Path dla wyodrębnionego Pliki @ no__t-1.

    Przykładowe polecenie:

    MicrosoftAzureSiteRecoveryUnifiedSetup. exe/q/x: C:\Temp\Extracted

1. W wierszu polecenia przejdź do folderu, do którego wyodrębniono pliki, i uruchom następujące polecenia instalacji:
   
    CX_THIRDPARTY_SETUP. EXE/VERYSILENT/SUPPRESSMSGBOXES/NORESTART

1. Za pomocą Menedżera zadań można monitorować postęp instalacji. Gdy proces CX_THIRDPARTY_SETUP. Plik EXE nie jest już widoczny w Menedżerze zadań, przejdź do następnego kroku.
1. Sprawdź, czy C:\thirdparty istnieje i czy folder zawiera biblioteki RRD.
1. Wróć do folderu, do którego pobrano ujednoliconą konfigurację, i uruchom MicrosoftAzureSiteRecoveryUnifiedSetup. exe, aby zakończyć proces uaktualniania. 