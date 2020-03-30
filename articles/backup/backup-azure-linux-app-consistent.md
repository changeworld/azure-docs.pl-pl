---
title: Spójne dla aplikacji kopie zapasowe maszyn wirtualnych z systemem Linux
description: Tworzenie spójnych aplikacji kopii zapasowych maszyn wirtualnych systemu Linux na platformie Azure. W tym artykule opisano konfigurowanie struktury skryptów do tworzenia kopii zapasowych maszyn wirtualnych z systemem Linux wdrożonych w usłudze Azure. Ten artykuł zawiera również informacje dotyczące rozwiązywania problemów.
ms.reviewer: anuragm
ms.topic: conceptual
ms.date: 01/12/2018
ms.openlocfilehash: 36eeb9f63c67a01bf37412101e23be035596de94
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74173005"
---
# <a name="application-consistent-backup-of-azure-linux-vms"></a>Tworzenie kopii zapasowych maszyn wirtualnych z systemem Linux w celu spójnego zastosowania

Podczas wykonywania migawek kopii zapasowej maszyn wirtualnych spójność aplikacji oznacza, że aplikacje są uruchamiane po uruchomieniu maszyn wirtualnych po przywróceniu. Jak można sobie wyobrazić, spójność aplikacji jest niezwykle ważne. Aby upewnić się, że maszyny wirtualne z systemem Linux są spójne z aplikacjami, można użyć struktury pre-script i post-script systemu Linux do wykonywania kopii zapasowych spójnych z aplikacjami. Struktura przedserkrytą i po skrypcie obsługuje maszyny wirtualne z systemem Linux wdrożone przez usługę Azure Resource Manager. Skrypty spójności aplikacji nie obsługują maszyn wirtualnych wdrożonych przez program Service Manager ani maszyn wirtualnych systemu Windows.

## <a name="how-the-framework-works"></a>Jak działa struktura

Struktura zapewnia opcję uruchamiania niestandardowych skryptów wstępnych i skryptów post podczas wykonywania migawek maszyn wirtualnych. Skrypty wstępne są uruchamiane tuż przed wykonywaniem migawki maszyny Wirtualnej, a skrypty po uruchomieniu natychmiast po uruchomieniu migawki maszyny Wirtualnej. Skrypty wstępne i skrypty post zapewniają elastyczność kontrolowania aplikacji i środowiska podczas robienia migawek maszyn wirtualnych.

Skrypty wstępne wywoływać natywne interfejsy API aplikacji, które quiesce ie ie zawartości w pamięci do dysku. Te akcje upewnij się, że migawka jest spójne aplikacji. Post-skrypty używają natywnych interfejsów API aplikacji do odmrożenia operacji we/wy, które umożliwiają aplikacji wznowienie normalnych operacji po migawce maszyny Wirtualnej.

## <a name="steps-to-configure-pre-script-and-post-script"></a>Kroki konfigurowania skryptu wstępnego i po skrypcie

1. Zaloguj się jako użytkownik główny do maszyny Wirtualnej systemu Linux, której chcesz wyw.

2. Z [GitHub](https://github.com/MicrosoftAzureBackup/VMSnapshotPluginConfig)pobierz **aplikację VMSnapshotScriptPluginConfig.json** i skopiuj go do folderu **/etc/azure** dla wszystkich maszyn wirtualnych, których kopię zapasową chcesz uzyskać. Jeśli **/etc/azure** folder nie istnieje, utwórz go.

3. Skopiuj skrypt wstępny i skrypt po aplikacji na wszystkich maszynach wirtualnych, których kopię zapasową planujesz. Skrypty można skopiować do dowolnej lokalizacji na maszynie Wirtualnej. Pamiętaj, aby zaktualizować pełną ścieżkę plików skryptów w pliku **VMSnapshotScriptPluginConfig.json.**

4. Upewnij się, że dla tych plików są one następujące uprawnienia:

   - **VMSnapshotScriptPluginConfig.json**: Uprawnienie "600". Na przykład tylko użytkownik "root" powinien mieć uprawnienia "odczytu" i "zapisu" do tego pliku, a żaden użytkownik nie powinien mieć uprawnień "execute".

   - **Plik skryptu wstępnego:** Uprawnienie "700".  Na przykład tylko użytkownik "root" powinien mieć uprawnienia "read", "write" i "execute" do tego pliku.

   - **Post-skrypt** Pozwolenie "700". Na przykład tylko użytkownik "root" powinien mieć uprawnienia "read", "write" i "execute" do tego pliku.

   > [!IMPORTANT]
   > Struktura daje użytkownikom dużo mocy. Zabezpiecz platformę i upewnij się, że tylko użytkownik "root" ma dostęp do krytycznych plików JSON i skryptów.
   > Jeśli wymagania nie są spełnione, skrypt nie zostanie uruchomiony, co powoduje awarię systemu plików i niespójną kopię zapasową.
   >

5. Skonfiguruj **plik VMSnapshotScriptPluginConfig.json** zgodnie z opisem w tym miejscu:
    - **pluginName**: Pozostaw to pole w takim stanie, w jakim jest, lub skrypty mogą nie działać zgodnie z oczekiwaniami.

    - **preScriptLocation**: Podaj pełną ścieżkę wstępnego skryptu na maszynie Wirtualnej, która zostanie utworzona kopia zapasowa.

    - **postScriptLocation**: Podaj pełną ścieżkę skryptu post na maszynie Wirtualnej, która zostanie utworzona kopia zapasowa.

    - **preScriptParams**: Podaj parametry opcjonalne, które muszą zostać przekazane do skryptu wstępnego. Wszystkie parametry powinny być w cudzysłowie. Jeśli używasz wielu parametrów, należy oddzielić parametry przecinkiem.

    - **postScriptParams**: Podaj parametry opcjonalne, które muszą zostać przekazane do skryptu post-script. Wszystkie parametry powinny być w cudzysłowie. Jeśli używasz wielu parametrów, należy oddzielić parametry przecinkiem.

    - **preScriptNoOf Ponownych prób:** Ustaw, ile razy pre-skrypt powinien być ponowiony, jeśli wystąpił błąd przed zakończeniem. Zero oznacza tylko jedną próbę i nie ponów próby, jeśli wystąpi błąd.

    - **postScriptNoOfRetries**: Ustaw liczbę ponownych prób poskrecie, jeśli wystąpił błąd przed zakończeniem. Zero oznacza tylko jedną próbę i nie ponów próby, jeśli wystąpi błąd.

    - **timeoutInSeconds**: Określ indywidualne limity czasu dla skryptu wstępnego i skryptu post (maksymalna wartość może wynosić 1800).

    - **continueBackupOnFailure:** Ustaw tę wartość na **true,** jeśli chcesz, aby usługa Azure Backup powróciła do spójnej/awaryjnej kopii zapasowej systemu plików, jeśli skrypt wstępny lub skrypt po awarii. Ustawienie false **nie** powiedzie się kopii zapasowej w przypadku niepowodzenia skryptu (z wyjątkiem sytuacji, gdy masz jednodyskowej maszyny Wirtualnej, która przechodzi z powrotem do kopii zapasowej spójneją z awarią, niezależnie od tego ustawienia).

    - **fsFreezeEnabled**: Określ, czy linux fsfreeze powinien być wywoływany podczas robienia migawki maszyny Wirtualnej, aby zapewnić spójność systemu plików. Zaleca się zachowanie tego ustawienia ustawionego na **true,** chyba że aplikacja ma zależność od wyłączania fsfreeze.

    - **SkryptyExecutionPollTimeSeconds:** Ustaw czas, w której rozszerzenie musi spać między każdą ankietą do wykonania skryptu. Na przykład jeśli wartość wynosi 2, rozszerzenie sprawdza, czy wykonanie skryptu przed/po zakończone co 2 sekundy. Minimalna i maksymalna wartość, jaki może przyjąć, to odpowiednio 1 i 5. Wartość powinna być ściśle całkowitej liczby.

6. Struktura skryptu jest teraz skonfigurowana. Jeśli kopia zapasowa maszyny Wirtualnej jest już skonfigurowana, następna kopia zapasowa wywołuje skrypty i wyzwala tworzenie kopii zapasowej spójnej z aplikacją. Jeśli kopia zapasowa maszyny Wirtualnej nie jest skonfigurowana, skonfiguruj ją przy użyciu [kopii zapasowej maszyn wirtualnych platformy Azure w magazynach usług odzyskiwania.](https://docs.microsoft.com/azure/backup/backup-azure-vms-first-look-arm)

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Upewnij się, że podczas pisania skryptu wstępnego i skryptu po skrypcie dodano odpowiednie rejestrowanie, a następnie przejrzyj dzienniki skryptów, aby rozwiązać wszelkie problemy ze skryptami. Jeśli nadal występują problemy z uruchamianiem skryptów, zapoznaj się z poniższą tabelą, aby uzyskać więcej informacji.

| Błąd | Komunikat o błędzie | Zalecana akcja |
| ------------------------ | -------------- | ------------------ |
| Pre-ScriptWykonawiększośćFailowana |Skrypt wstępny zwrócił błąd, więc kopia zapasowa może nie być spójna z aplikacją.| Spójrz na dzienniki błędów dla skryptu, aby rozwiązać ten problem.|  
|Post-ScriptWykonanyFailowany |Skrypt post zwrócił błąd, który może mieć wpływ na stan aplikacji. |Spójrz na dzienniki błędów dla skryptu, aby rozwiązać problem i sprawdzić stan aplikacji. |
| Pre-ScriptNotFound |Nie znaleziono wstępnego skryptu w lokalizacji określonej w pliku konfiguracyjnym **VMSnapshotScriptPluginConfig.json.** |Upewnij się, że skrypt wstępny jest obecny na ścieżce określonej w pliku konfiguracyjnym, aby zapewnić spójną z aplikacją kopię zapasową.|
| Post-ScriptNotFound |Nie znaleziono skryptu post w lokalizacji określonej w pliku konfiguracyjnym **VMSnapshotScriptPluginConfig.json.** |Upewnij się, że skrypt post jest obecny na ścieżce określonej w pliku konfiguracyjnym, aby zapewnić spójną z aplikacją kopię zapasową.|
| NiepoprawnyPluginhostFile |Plik **Pluginhost,** który jest dostarczany z rozszerzeniem VmSnapshotLinux, jest uszkodzony, więc pre-script i post-script nie mogą działać, a kopia zapasowa nie będzie spójna z aplikacją.| Odinstaluj rozszerzenie **VmSnapshotLinux** i zostanie ono automatycznie ponownie zainstalowane z następną kopią zapasową, aby rozwiązać problem. |
| Niepoprawny plik JSONConfigFile | Plik **VMSnapshotScriptPluginConfig.json** jest niepoprawny, więc nie można uruchomić skryptu wstępnego i skryptu po skryptu, a kopia zapasowa nie będzie spójna z aplikacją. | Pobierz kopię z [GitHub](https://github.com/MicrosoftAzureBackup/VMSnapshotPluginConfig) i skonfiguruj ją ponownie. |
| NiewystarczającePermissionforPre-Script | W przypadku uruchamiania skryptów użytkownik "root" powinien być właścicielem pliku, a plik powinien mieć uprawnienia "700" (to oznacza, że tylko "właściciel" powinien mieć uprawnienia "odczyt", "zapis" i "wykonanie"). | Upewnij się, że użytkownik "root" jest "właścicielem" pliku skryptu i że tylko "właściciel" ma uprawnienia "read", "write" i "execute". |
| NiewystarczającePermissionforPost-Script | W przypadku uruchamiania skryptów użytkownik root powinien być właścicielem pliku, a plik powinien mieć uprawnienia "700" (to oznacza, że tylko "właściciel" powinien mieć uprawnienia "odczyt", "zapis" i "wykonanie"). | Upewnij się, że użytkownik "root" jest "właścicielem" pliku skryptu i że tylko "właściciel" ma uprawnienia "read", "write" i "execute". |
| Czas przed skryptem | Wykonanie spójnej aplikacji kopii zapasowej przed przedserwem czasu. | Sprawdź skrypt i zwiększ limit czasu w pliku **VMSnapshotScriptPluginConfig.json,** który znajduje się na **/etc/azure**. |
| Czas po skrypcie | Limit czasu wykonania kopii zapasowej spójnej aplikacji. | Sprawdź skrypt i zwiększ limit czasu w pliku **VMSnapshotScriptPluginConfig.json,** który znajduje się na **/etc/azure**. |

## <a name="next-steps"></a>Następne kroki

[Konfigurowanie kopii zapasowej maszyny Wirtualnej w magazynie usług odzyskiwania](https://docs.microsoft.com/azure/backup/backup-azure-arm-vms)
