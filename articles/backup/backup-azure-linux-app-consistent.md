---
title: 'Usługa Azure Backup: spójnych z aplikacją kopii zapasowych maszyn wirtualnych systemu Linux'
description: Utwórz spójnych z aplikacją kopii zapasowych maszyn wirtualnych z systemem Linux na platformie Azure. W tym artykule opisano konfigurowanie framework skrypt, aby utworzyć kopię zapasową maszyn wirtualnych z systemem Linux wdrożonych przez usługę Azure. Ten artykuł zawiera również informacje dotyczące rozwiązywania problemów.
services: backup
author: anuragmehrotra
manager: shivamg
keywords: Kopia zapasowa spójna z aplikacji; spójnych z aplikacją kopii zapasowych maszyn wirtualnych platformy Azure; Kopii zapasowych maszyny Wirtualnej systemu Linux. Usługa Azure Backup
ms.service: backup
ms.topic: conceptual
ms.date: 1/12/2018
ms.author: anuragm
ms.openlocfilehash: a81c0b9c87db85771fcecab87c6b9ac88dcbd472
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60641130"
---
# <a name="application-consistent-backup-of-azure-linux-vms"></a>Spójna na poziomie aplikacji Kopia zapasowa maszyn wirtualnych systemu Linux platformy Azure

Podczas robienia migawek kopii zapasowych maszyn wirtualnych, spójność aplikacji oznacza, że aplikacje uruchamiania podczas rozruchu maszyny wirtualnej po przywracana. Jak możesz sobie wyobrazić, spójność aplikacji jest bardzo ważna. Aby upewnić się, maszyny wirtualne systemu Linux są spójne na poziomie, można korzystać z kopii zapasowych spójnych z aplikacją struktury systemu Linux i skryptu używanego po utworzeniu aplikacji. Struktury i skryptu używanego po utworzeniu obsługuje maszyny wirtualne Linux wdrożonych przez usługę Azure Resource Manager. Skrypty w celu zachowania spójności aplikacji, które nie obsługują maszyn wirtualnych wdrożonych w programie Service Manager i maszyn wirtualnych Windows.

## <a name="how-the-framework-works"></a>Jak działa platformę

Struktura zawiera opcję, aby uruchomić skrypty przed i po utworzeniu skryptów podczas tworzenia migawki maszyny Wirtualnej. Wstępne skrypty uruchamiane przed wykonać migawki maszyny Wirtualnej i skryptu używanego po utworzeniu uruchomienia po wykonaniu migawki maszyny Wirtualnej. Przed i po skryptów zapewniają elastyczność kontroli aplikacji i środowiska, podczas tworzenia migawki maszyny Wirtualnej.

Skryptu poprzedzającego wywołania interfejsów API, które w stan spoczynku w aplikacji natywnej systemu IOs, a następnie opróżnić zawartość w pamięci na dysk. Te akcje upewnij się, że migawka jest spójne na poziomie aplikacji. Skryptu używanego po utworzeniu za pomocą aplikacji natywnej interfejsów API można odblokować z systemami IOs, które umożliwia aplikacji powrócić do normalnego działania po wykonaniu migawki maszyny Wirtualnej.

## <a name="steps-to-configure-pre-script-and-post-script"></a>Instrukcje dotyczące konfigurowania skryptu poprzedzającego i skryptu używanego po utworzeniu

1. Zaloguj się jako użytkownik główny do maszyny Wirtualnej systemu Linux, który chcesz utworzyć kopię zapasową.

2. Z [GitHub](https://github.com/MicrosoftAzureBackup/VMSnapshotPluginConfig), Pobierz **VMSnapshotScriptPluginConfig.json** i skopiuj go do **/etc/azure** folderów dla wszystkich maszyn wirtualnych, które chcesz utworzyć kopię zapasową. Jeśli **/etc/azure** folder nie istnieje, utwórz go.

3. Skopiuj skryptu poprzedzającego i skryptu używanego po utworzeniu aplikacji na wszystkich maszynach wirtualnych, zaplanować tworzenie kopii zapasowych. Skrypty można skopiować do dowolnej lokalizacji na maszynie Wirtualnej. Pamiętaj zaktualizować pełną ścieżkę pliku skryptu w **VMSnapshotScriptPluginConfig.json** pliku.

4. Upewnij się, następujące uprawnienia do tych plików:

   - **VMSnapshotScriptPluginConfig.json**: Uprawnienie "600". Na przykład tylko użytkownik "root" powinien mieć uprawnienia "Odczyt" i "write" do tego pliku, a użytkownik nie powinien mieć uprawnienia "execute".

   - **Plik skryptu poprzedzającego**: Uprawnienie "700".  Na przykład tylko użytkownik "root" powinien mieć "Odczyt", "write" i "execute" uprawnienia do tego pliku.

   - **Skrypt używany po utworzeniu** uprawnienie "700". Na przykład tylko użytkownik "root" powinien mieć "Odczyt", "write" i "execute" uprawnienia do tego pliku.

   > [!Important]
   > Struktura zapewnia użytkownikom wiele możliwości. Zabezpieczanie platformę i upewnij się, że tylko użytkownik "root" ma dostęp do krytycznych JSON i pliki skryptów.
   > Jeśli nie są spełnione wymagania, skrypt nie będzie uruchamiany, co spowodowało awarię systemu plików i niespójną kopię zapasową.
   >

5. Konfigurowanie **VMSnapshotScriptPluginConfig.json** zgodnie z opisem w tym miejscu:
    - **pluginName**: Pozostaw to pole jest lub skryptów mogą nie działać zgodnie z oczekiwaniami.

    - **preScriptLocation**: Podaj pełną ścieżkę skryptu poprzedzającego na maszynie Wirtualnej, która ma wejść do wykonania kopii zapasowej.

    - **postScriptLocation**: Podaj pełną ścieżkę skryptu używanego po utworzeniu maszyny wirtualnej, która ma wejść do wykonania kopii zapasowej.

    - **preScriptParams**: Podaj parametry opcjonalne, które muszą być przekazywane do skryptu poprzedzającego. Wszystkie parametry powinny być w cudzysłowie. Jeśli używasz wielu parametrów, oddziel przecinkami parametrów.

    - **postScriptParams**: Podaj parametry opcjonalne, które muszą być przekazywane do skryptu używanego po utworzeniu. Wszystkie parametry powinny być w cudzysłowie. Jeśli używasz wielu parametrów, oddziel przecinkami parametrów.

    - **preScriptNoOfRetries**: Ustaw liczbę przypadków, gdy skryptu poprzedzającego prób w przypadku wszelkie błędy przed przerwaniem. Spróbuj tylko jedno zero oznacza, że i bez ponawiania, jeśli wystąpi awaria.

    - **postScriptNoOfRetries**:  Ustaw liczbę przypadków, gdy skrypt używany po utworzeniu prób w przypadku wszelkie błędy przed przerwaniem. Spróbuj tylko jedno zero oznacza, że i bez ponawiania, jeśli wystąpi awaria.

    - **timeoutInSeconds**: Określ poszczególne przekroczeń limitu czasu skryptu poprzedzającego i skryptu używanego po utworzeniu (maksymalna wartość może być 1800).

    - **continueBackupOnFailure**: Ustaw tę wartość na **true** Jeśli chcesz, aby usługa Azure Backup należy wrócić do spójnego spójny awarii/kopia zapasowa systemu plików, jeśli skrypt przed lub po utworzeniu skryptu zakończy się niepowodzeniem. Ustawienie tej opcji na **false** nie powiodło się tworzenie kopii zapasowej w razie błędu skryptu (z wyjątkiem sytuacji, gdy masz maszynę Wirtualną z jednego dysku, która przechodzi do usługi Kopia zapasowa spójna w razie awarii niezależnie od tego ustawienia).

    - **fsFreezeEnabled**: Określ, czy Linux fsfreeze powinna być wywoływana podczas tworzenia migawki maszyny Wirtualnej w celu zapewnienia spójności systemu plików. Zaleca się pozostawienie tego ustawienia określona **true** chyba, że Twoja aplikacja ma zależności po wyłączeniu fsfreeze.

6. W ramach skryptu jest teraz skonfigurowane. Jeśli kopia zapasowa maszyny Wirtualnej jest już skonfigurowany, następną kopią zapasową wywołuje skrypty i wyzwala kopii zapasowych spójnych z aplikacją. Jeśli nie skonfigurowano kopii zapasowej maszyny Wirtualnej, jest skonfigurowana przy użyciu [wykonywanie kopii zapasowych maszyn wirtualnych platformy Azure w magazynach usługi Recovery Services.](https://docs.microsoft.com/azure/backup/backup-azure-vms-first-look-arm)

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Upewnij się, możesz dodać odpowiednie rejestrowania podczas zapisywania skryptu wstępnej i skryptu używanego po utworzeniu i przejrzeć swoje dzienniki skryptu, aby rozwiązać problemy dotyczące skryptu. Jeśli nadal masz problemy z uruchamianiem skryptów, zapoznaj się z poniższą tabelą, aby uzyskać więcej informacji.

| Błąd | Komunikat o błędzie | Zalecana akcja |
| ------------------------ | -------------- | ------------------ |
| Wstępnie ScriptExecutionFailed |Skryptu poprzedzającego zwrócił błąd, dzięki czemu kopii zapasowej może nie być spójna na poziomie aplikacji.   | Przyjrzyj się do dzienników błędów dla swojego skryptu, aby rozwiązać ten problem.|  
|   Wpis ScriptExecutionFailed |    Skrypt używany po utworzeniu zwrócił błąd mogą mieć wpływ na stan aplikacji. |    Przyjrzyj się do dzienników błędów dla swojego skryptu, aby rozwiązać ten problem i sprawdź stan aplikacji. |
| Pre-ScriptNotFound |  Nie znaleziono skryptu poprzedzającego w lokalizacji, która została określona w **VMSnapshotScriptPluginConfig.json** pliku konfiguracji. |   Upewnij się, że to skryptu poprzedzającego jest obecny w ścieżce, który jest określony w pliku konfiguracji, aby zapewnić kopię zapasową spójnych z aplikacją.|
| Post-ScriptNotFound | Skrypt używany po utworzeniu nie można znaleźć w lokalizacji, która została określona w **VMSnapshotScriptPluginConfig.json** pliku konfiguracji. |   Upewnij się, że to skrypt używany po utworzeniu jest obecny w ścieżce, który jest określony w pliku konfiguracji, aby zapewnić kopię zapasową spójnych z aplikacją.|
| IncorrectPluginhostFile | **Hosta** uszkodzony plik, który zawiera rozszerzenie VmSnapshotLinux, więc nie można uruchomić skryptu poprzedzającego i skryptu używanego po utworzeniu i tworzenia kopii zapasowej nie będzie spójna na poziomie aplikacji. | Odinstaluj **VmSnapshotLinux** rozszerzenia, a zostaną automatycznie zainstalowane ponownie z następną kopią zapasową, aby rozwiązać ten problem. |
| IncorrectJSONConfigFile | **VMSnapshotScriptPluginConfig.json** pliku jest niepoprawny, więc skryptu poprzedzającego i nie można uruchomić skryptu używanego po utworzeniu kopii zapasowej nie będzie spójna na poziomie aplikacji. | Pobierz kopię z [GitHub](https://github.com/MicrosoftAzureBackup/VMSnapshotPluginConfig) i ponownie go skonfigurować. |
| InsufficientPermissionforPre-Script | Uruchamianie skryptów, użytkownik "root" powinien być właścicielem pliku, a ten plik powinien zawierać uprawnienia "700" (czyli tylko "owner" powinien mieć "Odczyt", "write" i "execute" uprawnienia). | Upewnij się, że użytkownik "root" jest "owner" pliku skryptu i że tylko "owner" ma "uprawnienia do odczytu", "Zapisywanie" oraz "execute". |
| InsufficientPermissionforPost-Script | Uruchamianie skryptów, głównego użytkownika powinny być właściciela pliku i ten plik powinien zawierać uprawnienia "700" (czyli tylko "owner" powinien mieć "Odczyt", "write" i "execute" uprawnienia). | Upewnij się, że użytkownik "root" jest "owner" pliku skryptu i że tylko "owner" ma "uprawnienia do odczytu", "Zapisywanie" oraz "execute". |
| Pre-ScriptTimeout | Wykonanie spójnych z aplikacją kopii zapasowych skryptu poprzedzającego upłynął limit czasu. | Sprawdź skrypt i zwiększyć limit czasu w **VMSnapshotScriptPluginConfig.json** pliku, który znajduje się w **/etc/azure**. |
| Post-ScriptTimeout | Upłynął limit czasu wykonywania spójnych z aplikacją skrypt używany po utworzeniu kopii zapasowej. | Sprawdź skrypt i zwiększyć limit czasu w **VMSnapshotScriptPluginConfig.json** pliku, który znajduje się w **/etc/azure**. |

## <a name="next-steps"></a>Kolejne kroki
[Konfigurowanie kopii zapasowych maszyn wirtualnych w magazynie usługi Recovery Services](https://docs.microsoft.com/azure/backup/backup-azure-arm-vms)
