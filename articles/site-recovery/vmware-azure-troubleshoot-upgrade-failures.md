---
title: Rozwiązywanie problemów z uaktualnieniem dostawcy odzyskiwania witryny platformy Microsoft Azure
description: Rozwiąż typowe problemy, które występują podczas uaktualniania dostawcy usługi Microsoft Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: troubleshooting
ms.date: 11/10/2019
ms.author: raynew
ms.openlocfilehash: b59f933fedd5f1d3ed3f7972b1a1fe653df31be2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75893909"
---
# <a name="troubleshoot-microsoft-azure-site-recovery-provider-upgrade-failures"></a>Rozwiązywanie problemów z błędami uaktualniania dostawcy Microsoft Azure Site Recovery

Ten artykuł ułatwia rozwiązywanie problemów, które mogą powodować błędy podczas uaktualniania dostawcy odzyskiwania witryny Platformy Microsoft Azure.

## <a name="the-upgrade-fails-reporting-that-the-latest-site-recovery-provider-is-already-installed"></a>Uaktualnienie kończy się niepowodzeniem, informując, że najnowszy dostawca odzyskiwania witryny jest już zainstalowany

Podczas uaktualniania dostawcy odzyskiwania witryny platformy Microsoft Azure (DRA) uaktualnienie ujednoliconej instalacji kończy się niepowodzeniem i pojawia się komunikat o błędzie:

Uaktualnienie nie jest obsługiwane, ponieważ wyższa wersja oprogramowania jest już zainstalowana.

Aby uaktualnić, należy wykonać następujące czynności:

1. Pobierz ujednoliconą instalację odzyskiwania witryny platformy Microsoft Azure:
   1. W sekcji "Łącza do aktualnie obsługiwanych pakietów zbiorczych aktualizacji" w artykule [Aktualizacje usługi w usłudze Azure Site Recovery](service-updates-how-to.md#links-to-currently-supported-update-rollups) wybierz dostawcę, do którego uaktualniasz.
   2. Na stronie pakietu zbiorczego znajdź sekcję **Aktualizuj informacje** i pobierz pakiet zbiorczy aktualizacji dla ujednoliconej instalacji odzyskiwania witryny platformy Microsoft Azure.

2. Otwórz wiersz polecenia i przejdź do folderu, do którego pobrano plik Unified Setup. Wyodrębnij pliki instalacyjne z pobierania za pomocą następującego polecenia, MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /x:&lt;ścieżka folderu wyodrębnionych plików&gt;.
    
    Przykładowe polecenie:

    MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /x:C:\Temp\Wyodrębnione

3. W wierszu polecenia przejdź do folderu, do którego wyodrębnione pliki zostały wyodrębnione, i uruchom następujące polecenia instalacyjne:
   
    CX_THIRDPARTY_SETUP. EXE /VERYSILENT /SUPPRESSMSGBOXES /NORESTART UCX_SERVER_SETUP. EXE /VERYSILENT /SUPPRESSMSGBOXES /NORESTART /UPGRADE

1. Wróć do folderu, do którego pobrano ujednoliconą instalację i uruchom program MicrosoftAzureSiteRecoveryUnifiedSetup.exe, aby zakończyć uaktualnienie. 

## <a name="upgrade-failure-due-to-the-3rd-party-folder-being-renamed"></a>Niepowodzenie uaktualnienia z powodu zmiany nazwy folderu innej firmy

Aby uaktualnienie zakończyło się pomyślnie, nie można zmienić nazwy folderu innej firmy.

Aby rozwiązać ten problem.

1. Uruchom Edytor rejestru (regedit.exe) i otwórz gałąź HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\InMage Systems\Zainstalowane produkty\10.
1. Sprawdź `Build_Version` wartość klucza. Jeśli jest ustawiona na najnowszą wersję, zmniejsz numer wersji. Na przykład jeśli najnowsza wersja jest 9.22. \* i `Build_Version` klucz ustawiony na tę wartość, a następnie zmniejszyć ją do 9,21. \*.
1. Pobierz najnowszą ujednoliconą instalację odzyskiwania witryny platformy Microsoft Azure:
   1. W sekcji "Łącza do aktualnie obsługiwanych pakietów zbiorczych aktualizacji" w artykule [Aktualizacje usługi w usłudze Azure Site Recovery](service-updates-how-to.md#links-to-currently-supported-update-rollups) wybierz dostawcę, do którego uaktualniasz.
   2. Na stronie pakietu zbiorczego znajdź sekcję **Aktualizuj informacje** i pobierz pakiet zbiorczy aktualizacji dla ujednoliconej instalacji odzyskiwania witryny platformy Microsoft Azure.
1. Otwórz wiersz polecenia i przejdź do folderu, do którego pobrano plik Unified Setup i wyodrębnij pliki instalacyjne z pobierania za pomocą następującego&lt;polecenia, MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /x: ścieżka folderu wyodrębnionych plików&gt;.

    Przykładowe polecenie:

    MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /x:C:\Temp\Wyodrębnione

1. W wierszu polecenia przejdź do folderu, do którego wyodrębnione pliki zostały wyodrębnione, i uruchom następujące polecenia instalacyjne:
   
    CX_THIRDPARTY_SETUP. EXE /VERYSILENT /SUPPRESSMSGBOXES /NORESTART

1. Menedżer zadań służy do monitorowania postępu instalacji. Gdy proces CX_THIRDPARTY_SETUP. EXE nie jest już widoczny w Menedżerze zadań, przejdź do następnego kroku.
1. Sprawdź, czy istnieje strona C:\thirdparty i czy folder zawiera biblioteki RRD.
1. Wróć do folderu, do którego pobrano ujednoliconą instalację i uruchom program MicrosoftAzureSiteRecoveryUnifiedSetup.exe, aby zakończyć uaktualnienie. 