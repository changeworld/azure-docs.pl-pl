---
title: Rozwiązywanie problemów z błędami uaktualniania dostawcy Microsoft Azure Site Recovery | Dokumentacja firmy Microsoft
description: Zrozumienie i
author: vDonGlover
manager: jarrettr
ms.service: site-recovery
ms.topic: troubleshooting
ms.date: 02/05/2019
ms.author: v-doglov
ms.openlocfilehash: fc50be2a960784895947f3f154a0251f41716fc7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60565705"
---
# <a name="troubleshoot-microsoft-azure-site-recovery-provider-upgrade-failures"></a>Rozwiązywanie problemów z błędami uaktualniania dostawcy Microsoft Azure Site Recovery

Ten artykuł pomoże Ci rozwiązać problemy, które mogą powodować błędy podczas dostawcy Microsoft Azure Site Recovery uaktualnienia.

## <a name="the-upgrade-fails-reporting-that-the-latest-site-recovery-provider-is-already-installed"></a>Uaktualnienie nie powiedzie się raportowania, że najnowsze dostawcę usługi Site Recovery jest już zainstalowana

Podczas uaktualniania dostawcy odzyskiwania witryny Microsoft Azure (DRA), uaktualnienie ujednoliconej konfiguracji nie powiedzie się i generuje komunikat o błędzie:

Uaktualnienie nie jest obsługiwane, zgodnie z nowszej wersji oprogramowania jest już zainstalowany.

Aby przeprowadzić uaktualnienie, wykonaj następujące kroki:

1. Pobierz witryna platformy Microsoft Azure Recovery ujednoliconej konfiguracji:
   1. W sekcji "Linki do pakietów zbiorczych aktualizacji aktualnie obsługiwany" [usługi aktualizacji w usłudze Azure Site Recovery](service-updates-how-to.md##links-to-currently-supported-update-rollups) artykuł, wybierz dostawcę, z którą uaktualniasz.
   2. Na stronie pakiet zbiorczy zlokalizować **zaktualizować informacje o** sekcji i Pobierz pakiet zbiorczy aktualizacji dla programu Microsoft Azure Site Recovery Unified Setup.

2. Otwórz wiersz polecenia i przejdź do folderu, do którego został pobrany plik ujednoliconego Instalatora. Wyodrębnij pliki instalacyjne z pliki do pobrania przy użyciu następującego polecenia, / x:/q MicrosoftAzureSiteRecoveryUnifiedSetup.exe&lt;ścieżki folderu dla wyodrębnionych plików&gt;.
    
    Przykładowe polecenie:

    MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /x:C:\Temp\Extracted

3. W wierszu polecenia przejdź do folderu, do którego wyodrębniono pliki, a następnie uruchom następujące polecenia instalacji:
   
    CX_THIRDPARTY_SETUP. UCX_SERVER_SETUP/NORESTART / /SUPPRESSMSGBOXES /VERYSILENT EXE. OPCJA/UPGRADE EXE /VERYSILENT /SUPPRESSMSGBOXES/NORESTART /

1. Wróć do folderu, do którego pobrano ujednoliconego Instalatora i uruchom MicrosoftAzureSiteRecoveryUnifiedSetup.exe w celu zakończenia uaktualniania. 

## <a name="upgrade-failure-due-to-the-3rd-party-folder-being-renamed"></a>Niepowodzenie z powodu folderu firm 3, nazwa jest zmieniana uaktualnienia

Dla uaktualnienie powiodło się firm 3 musi nie można zmienić nazwy folderu.

Aby rozwiązać ten problem.

1. Uruchom Edytor rejestru (regedit.exe), a następnie otwórz gałęzi HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\InMage Systems\Installed Products\10.
1. Sprawdzanie `Build_Version` wartość klucza. Jeśli jest ustawiona do najnowszej wersji, należy zmniejszyć numer wersji. Jeśli na przykład najnowsza wersja to 9.22. \* i `Build_Version` ustawiona na wartość tego klucza, a następnie jej zmniejszenia do 9.21.\*.
1. Pobierz najnowszy program Microsoft Azure Site Recovery Unified Setup:
   1. W sekcji "Linki do pakietów zbiorczych aktualizacji aktualnie obsługiwany" [usługi aktualizacji w usłudze Azure Site Recovery](service-updates-how-to.md##links-to-currently-supported-update-rollups) artykuł, wybierz dostawcę, z którą uaktualniasz.
   2. Na stronie pakiet zbiorczy zlokalizować **zaktualizować informacje o** sekcji i Pobierz pakiet zbiorczy aktualizacji dla programu Microsoft Azure Site Recovery Unified Setup.
1. Otwórz wiersz polecenia i przejdź do folderu, do którego pobrano plik ujednoliconego Instalatora i wyodrębniania plików Instalatora z pobrania przy użyciu następującego polecenia, / x:/q MicrosoftAzureSiteRecoveryUnifiedSetup.exe&lt;ścieżka folderu wyodrębnione pliki&gt;.

    Przykładowe polecenie:

    MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /x:C:\Temp\Extracted

1. W wierszu polecenia przejdź do folderu, do którego wyodrębniono pliki, a następnie uruchom następujące polecenia instalacji:
   
    CX_THIRDPARTY_SETUP. / NORESTART /SUPPRESSMSGBOXES /VERYSILENT EXE

1. Za pomocą Menedżera zadań można monitorować postęp instalacji. Podczas procesu CX_THIRDPARTY_SETUP. Plik EXE nie jest już widoczna w Menedżerze zadań, przejdź do następnego kroku.
1. Sprawdź, czy C:\thirdparty istnieje i że folder zawiera biblioteki RRD.
1. Wróć do folderu, do którego pobrano ujednoliconego Instalatora i uruchom MicrosoftAzureSiteRecoveryUnifiedSetup.exe w celu zakończenia uaktualniania. 