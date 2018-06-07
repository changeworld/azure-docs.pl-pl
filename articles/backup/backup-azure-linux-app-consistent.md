---
title: 'Kopia zapasowa Azure: spójnych z aplikacją kopii zapasowych maszyn wirtualnych systemu Linux'
description: Tworzenie spójnych z aplikacją kopii zapasowych maszyn wirtualnych systemu Linux na platformie Azure. W tym artykule opisano konfigurowanie framework skryptu, aby utworzyć kopię zapasową maszyn wirtualnych systemu Linux wdrożonych Azure. Ten artykuł zawiera także informacje dotyczące rozwiązywania problemów.
services: backup
author: anuragmehrotra
manager: shivamg
keywords: kopii zapasowej całej aplikacji. spójnych z aplikacją kopii zapasowej maszyny Wirtualnej platformy Azure; Kopii zapasowej maszyny Wirtualnej systemu Linux. Kopia zapasowa Azure
ms.service: backup
ms.topic: conceptual
ms.date: 1/12/2018
ms.author: anuragm
ms.openlocfilehash: 027fc4098e7760de276a8548453bb83599ed0521
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34605216"
---
# <a name="application-consistent-backup-of-azure-linux-vms"></a>Spójnych z aplikacją kopii zapasowej maszyn wirtualnych systemu Linux platformy Azure

Gdy tworzenie kopii zapasowych migawek maszyn wirtualnych, spójności aplikacji oznacza, że aplikacje uruchamiania podczas rozruchu maszyn wirtualnych po przywracana. Jak można wyobrazić sobie spójności aplikacji jest bardzo ważne. Aby upewnić się, maszyn wirtualnych systemu Linux są zgodne, można użyć framework skryptów przed i po skryptu Linux Twórz kopie zapasowe spójnych z aplikacją aplikacji. Struktura skryptu przed i po skryptu obsługuje maszyn wirtualnych wdrożonych przez Menedżera zasobów Azure systemu Linux. Skrypty w celu zachowania spójności aplikacji nie obsługują wdrożeniu programu Service Manager maszyn wirtualnych lub maszyn wirtualnych systemu Windows.

## <a name="how-the-framework-works"></a>Jak działa w ramach

Platformę udostępnia opcję umożliwiają uruchamianie niestandardowych skryptów przed i po skrypty podczas tworzenia migawki maszyny Wirtualnej. Wstępne skrypty uruchamiane tak, aby wykonać migawki maszyny Wirtualnej, a po skrypty uruchamiane natychmiast po migawki maszyny Wirtualnej. Przed i po skryptów zapewniają elastyczność kontroli aplikacji i środowiska, podczas tworzenia migawki maszyny Wirtualnej.

Wstępne skrypty wywołania interfejsów API, które w stan spoczynku aplikacji natywnej systemu IOs i opróżnić zawartość w pamięci na dysk. Te akcje upewnij się, że migawka jest zgodny. Skrypty po korzystać z aplikacji natywnych interfejsów API można odblokować systemu IOs, które umożliwia aplikacji przywrócić normalne działanie po wykonaniu migawki maszyny Wirtualnej.

## <a name="steps-to-configure-pre-script-and-post-script"></a>Kroki konfigurowania skryptów przed i po skryptu

1. Zaloguj się jako użytkownik główny do maszyny Wirtualnej systemu Linux, który chcesz utworzyć kopię zapasową.

2. Z [GitHub](https://github.com/MicrosoftAzureBackup/VMSnapshotPluginConfig), Pobierz **VMSnapshotScriptPluginConfig.json** i skopiuj go do **/etc/azure** folderu dla wszystkich maszyn wirtualnych, które chcesz utworzyć kopię zapasową. Jeśli **/etc/azure** folder nie istnieje, utwórz go.

3. Skopiuj skrypt przed i po skryptu aplikacji na wszystkich maszynach wirtualnych, należy zaplanować tworzenie kopii zapasowych. Skrypty można skopiować do dowolnej lokalizacji na maszynie Wirtualnej. Należy upewnić się, że pełna ścieżka plików skryptów w **VMSnapshotScriptPluginConfig.json** pliku.

4. Upewnij się, następujących uprawnień do tych plików:

   - **VMSnapshotScriptPluginConfig.json**: uprawnienie "600." Na przykład tylko użytkownika "root" powinien mieć uprawnienia "Odczyt" i "write" do tego pliku, a żaden użytkownik nie ma uprawnienia "execute".

   - **Plik skryptu przed**: uprawnienie "700."  Na przykład tylko "root" użytkownik powinien mieć "do odczytu", "write" i "execute" uprawnień do tego pliku.
  
   - **Skrypt po** uprawnienia "700." Na przykład tylko "root" użytkownik powinien mieć "do odczytu", "write" i "execute" uprawnień do tego pliku.

   > [!Important]
   > Platformę umożliwia użytkownikom dużo energii. Zabezpieczenie platformę i upewnij się, że tylko "root" użytkownik ma dostęp do krytycznych JSON i pliki skryptów.
   > Jeśli nie są spełnione wymagania, skrypt nie będzie uruchamiany, które powoduje awarii systemu plików oraz niespójne kopii zapasowej.
   >

5. Skonfiguruj **VMSnapshotScriptPluginConfig.json** zgodnie z opisem w tym miejscu:
    - **pluginName**: pozostaw to pole jest lub skryptów mogą nie działać zgodnie z oczekiwaniami.

    - **preScriptLocation**: Podaj pełną ścieżkę skryptu przed na maszynie Wirtualnej, która ma wejść do wykonania kopii zapasowej.

    - **postScriptLocation**: Podaj pełną ścieżkę skryptu po na maszynie Wirtualnej, która ma wejść do wykonania kopii zapasowej.

    - **preScriptParams**: Podaj parametry opcjonalne, które muszą zostać przekazane do skryptu wstępne. Wszystkie parametry powinny mieć w cudzysłowy. Jeśli używasz wielu parametrów, oddziel przecinkami parametrów.

    - **postScriptParams**: Podaj parametry opcjonalne, które muszą być przekazywane do skryptu po. Wszystkie parametry powinny mieć w cudzysłowy. Jeśli używasz wielu parametrów, oddziel przecinkami parametrów.

    - **preScriptNoOfRetries**: Ustaw liczbę godzin przed skryptu należy wykonać ponownie razie błędu przed zakończeniem. Zero oznacza, że tylko jedna próba i nie ponownych prób w przypadku awarii.

    - **postScriptNoOfRetries**: Ustaw liczbę razy skryptu po powinno być ponowione w razie błędu przed zakończeniem. Zero oznacza, że tylko jedna próba i nie ponownych prób w przypadku awarii.
    
    - **LimitCzasuWSekundach**: Określ poszczególnych limitów czasu dla skryptów przed i po skryptu.

    - **continueBackupOnFailure**: Ustaw tę wartość na **true** Jeśli chcesz, kopia zapasowa Azure może wrócić do kopia zapasowa spójna spójne/awarii systemu plików, jeśli skrypt przed lub po wykonaniu skryptu kończy się niepowodzeniem. To ustawienie **false** niepowodzenia tworzenia kopii zapasowej w przypadku błędu skryptu (z wyjątkiem przypadków, gdy masz jednego dysku maszyny Wirtualnej, która przechodzi do usługi Kopia zapasowa spójna w razie awarii niezależnie od tego ustawienia).

    - **fsFreezeEnabled**: Określ, czy Linux fsfreeze powinna być wywoływana podczas tworzenia migawki maszyny Wirtualnej w celu zapewnienia spójności systemu plików. Zaleca się pozostawienie to ustawienie, ustaw **true** chyba, że aplikacja ma zależności wyłączenie fsfreeze.

6. W ramach skryptu jest teraz skonfigurowany. Jeśli kopia zapasowa maszyny Wirtualnej jest już skonfigurowana, następnej kopii zapasowej wywołuje skrypty i wyzwala spójnych z aplikacją kopii zapasowej. Jeśli nie skonfigurowano kopii zapasowej maszyny Wirtualnej, skonfiguruj ją za pomocą [kopii zapasowych maszyn wirtualnych platformy Azure do magazynów usług odzyskiwania.](https://docs.microsoft.com/azure/backup/backup-azure-vms-first-look-arm)

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Upewnij się, należy dodać odpowiednią rejestrowania podczas zapisywania w skrypcie przed i po skryptu i dziennikach skryptu Rozwiąż wszelkie problemy skryptu. Jeśli nadal występują problemy z uruchamianiem skryptów, zapoznaj się z poniższą tabelą, aby uzyskać więcej informacji.

| Błąd | Komunikat o błędzie | Zalecana akcja |
| ------------------------ | -------------- | ------------------ |
| Wstępnie ScriptExecutionFailed |Wstępne skryptu zwrócił błąd, aby kopia zapasowa może nie być spójnych z aplikacją.   | Sprawdź w dziennikach błędu skrypt, aby rozwiązać ten problem.|  
|   Post ScriptExecutionFailed |    Skrypt po zwrócił błąd, który może mieć wpływ na stan aplikacji. |    Sprawdź w dziennikach błędu skrypt, aby rozwiązać ten problem i sprawdź stan aplikacji. |
| Wstępnie ScriptNotFound |  Nie znaleziono wstępne skryptu w lokalizacji określonej w **VMSnapshotScriptPluginConfig.json** pliku konfiguracji. |   Upewnij się, że to wstępne skryptu znajduje się w ścieżce określonej w pliku konfiguracji, aby zapewnić spójne z aplikacjami tworzenia kopii zapasowych.|
| Post ScriptNotFound | Po skryptu nie można znaleźć w lokalizacji określonej w **VMSnapshotScriptPluginConfig.json** pliku konfiguracji. |   Upewnij się, że to po skryptu znajduje się w ścieżce określonej w pliku konfiguracji, aby zapewnić spójne z aplikacjami tworzenia kopii zapasowych.|
| IncorrectPluginhostFile | **Pluginhost** pliku, który jest dostarczany z rozszerzeniem VmSnapshotLinux, jest uszkodzony, więc nie można uruchomić skryptu przed i po skrypt i tworzenia kopii zapasowej nie będzie spójnych z aplikacją. | Odinstaluj **VmSnapshotLinux** rozszerzenia, a zostaną automatycznie zainstalowane ponownie z następnej kopii zapasowej, aby rozwiązać ten problem. |
| IncorrectJSONConfigFile | **VMSnapshotScriptPluginConfig.json** plik jest nieprawidłowy, dlatego przed skryptu i nie można uruchomić skrypt po i kopii zapasowej nie będzie spójnych z aplikacją. | Pobierz kopię z [GitHub](https://github.com/MicrosoftAzureBackup/VMSnapshotPluginConfig) i skonfiguruj go ponownie. |
| InsufficientPermissionforPre skryptu | Uruchamianie skryptów, użytkownika "root" powinien być właściciel pliku i ten plik powinien mieć uprawnienia "700" (to znaczy powinny mieć tylko "właściciela" "do odczytu", "write" i "uprawnienia do wykonywania"). | Upewnij się, użytkownika "root" jest "właścicielem" pliku skryptu i że tylko "właściciela" ma "uprawnienia do odczytu", "write" i "wykonywanie". |
| InsufficientPermissionforPost skryptu | Uruchamianie skryptów, użytkownika głównego powinny być właściciel pliku i ten plik powinien mieć uprawnienia "700" (to znaczy powinny mieć tylko "właściciela" "do odczytu", "write" i "uprawnienia do wykonywania"). | Upewnij się, użytkownika "root" jest "właścicielem" pliku skryptu i że tylko "właściciela" ma "uprawnienia do odczytu", "write" i "wykonywanie". |
| Wstępnie ScriptTimeout | Spójnych z aplikacją kopii zapasowej skryptu przed upłynął limit czasu wykonywania. | Sprawdź skrypt i zwiększyć limit czasu w **VMSnapshotScriptPluginConfig.json** pliku znajdującego się na **/etc/azure**. |
| Post ScriptTimeout | Upłynął limit czasu wykonywania skryptu po kopii zapasowej zapewniających spójność aplikacji. | Sprawdź skrypt i zwiększyć limit czasu w **VMSnapshotScriptPluginConfig.json** pliku znajdującego się na **/etc/azure**. |

## <a name="next-steps"></a>Kolejne kroki
[Konfigurowanie kopii zapasowej maszyny Wirtualnej w magazynie usług odzyskiwania](https://docs.microsoft.com/azure/backup/backup-azure-arm-vms)
