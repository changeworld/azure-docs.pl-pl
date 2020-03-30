---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/11/2018
ms.author: rogarana
ms.openlocfilehash: aeb15fbb8da44a203789e06a359cb664998602ab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77123221"
---
Agent usługi Azure File Sync jest aktualizowany regularnie, aby dodać nowe funkcje i rozwiązać problemy. Zaleca się skonfigurowanie usługi Microsoft Update, aby uzyskać aktualizacje dla agenta usługi Azure File Sync, gdy są one dostępne.

#### <a name="major-vs-minor-agent-versions"></a>Wersje głównych i pomocniczych agentów
* Wersje głównych agentów często zawierają nowe funkcje i mają coraz większą liczbę jako pierwszą część numeru wersji. Na przykład: \*2.\*.\*\*
* Wersje agenta pomocniczego są również nazywane "poprawkami" i są wydawane częściej niż wersje główne. Często zawierają poprawki błędów i mniejsze ulepszenia, ale nie mają nowych funkcji. Na przykład: \* \*.3.\*\*

#### <a name="upgrade-paths"></a>Ścieżki uaktualniania
Istnieją cztery zatwierdzone i przetestowane sposoby instalowania aktualizacji agenta usługi Azure File Sync. 
1. **(Preferowane) Skonfiguruj usługę Microsoft Update, aby automatycznie pobierała i instalowano aktualizacje agenta.**  
    Zawsze zalecamy wykonanie każdej aktualizacji usługi Azure File Sync, aby upewnić się, że masz dostęp do najnowszych poprawek dla agenta serwera. Usługa Microsoft Update sprawia, że ten proces jest bezproblemowy, automatycznie pobierając i instalując aktualizacje.
2. **Użyj afsUpdater.exe, aby pobrać i zainstalować aktualizacje agenta.**  
    Program AfsUpdater.exe znajduje się w katalogu instalacji agenta. Kliknij dwukrotnie plik wykonywalny, aby pobrać i zainstalować aktualizacje agenta. 
3. **Popraw istniejący agent synchronizacji plików platformy Azure przy użyciu pliku poprawek usługi Microsoft Update lub pliku wykonywalnego msp. Najnowszy pakiet aktualizacji synchronizacji plików platformy Azure można pobrać z [wykazu Microsoft Update Catalog](https://www.catalog.update.microsoft.com/Search.aspx?q=Azure%20File%20Sync).**  
    Uruchomienie pliku wykonywalnego .msp spowoduje uaktualnienie instalacji synchronizacji plików platformy Azure przy użyciu tej samej metody, która jest automatycznie używana przez usługę Microsoft Update w poprzedniej ścieżce uaktualnienia. Zastosowanie poprawki usługi Microsoft Update spowoduje przeprowadzenie uaktualnienia w miejscu instalacji synchronizacji plików platformy Azure.
4. **Pobierz najnowszy instalator agenta usługi Azure File Sync z [Centrum pobierania Microsoft](https://go.microsoft.com/fwlink/?linkid=858257).**  
    Aby uaktualnić istniejącą instalację agenta usługi Azure File Sync, odinstaluj starszą wersję, a następnie zainstaluj najnowszą wersję z pobranego instalatora. Rejestracja serwera, grupy synchronizacji i inne ustawienia są obsługiwane przez instalatora synchronizacji plików platformy Azure.

#### <a name="automatic-agent-lifecycle-management"></a>Automatyczne zarządzanie cyklem życia agenta
W wersji agenta 6 zespół synchronizacji plików wprowadził funkcję automatycznego uaktualniania agenta. Można wybrać jeden z dwóch trybów i określić okno konserwacji, w którym należy podjąć próbę uaktualnienia na serwerze. Ta funkcja ma na celu pomóc w zarządzaniu cyklem życia agenta, zapewniając barierkę zapobiegającą wygasaniu agenta lub pozwalającą na bezproblemowe ustawienie na bieżąco.
1. Domyślne **ustawienie** będzie próbował zapobiec agentowi wygasania. W ciągu 21 dni od opublikowanej daty wygaśnięcia agenta agent podejmie próbę samodzielnego uaktualnienia. Rozpocznie się próba uaktualnienia raz w tygodniu w ciągu 21 dni przed wygaśnięciem i w wybranym oknie konserwacji. **Ta opcja nie eliminuje konieczności stosowania regularnych poprawek microsoft update.**
1. Opcjonalnie można wybrać, że agent automatycznie uaktualni się, jak tylko nowa wersja agenta staje się dostępna (obecnie nie dotyczy serwerów klastrowanych). Ta aktualizacja nastąpi podczas wybranego okna konserwacji i pozwoli serwerowi korzystać z nowych funkcji i ulepszeń, gdy tylko staną się one ogólnie dostępne. Jest to zalecane, bezproblemowe ustawienie, które zapewni główne wersje agenta, a także regularne poprawki aktualizacji na serwerze. Każdy zwolniony agent jest w jakości GA. Jeśli wybierzesz tę opcję, firma Microsoft przeleci do Ciebie najnowszą wersję agenta. Serwery klastrowane są wykluczone. Po zakończeniu lotu agent będzie również dostępny w [Centrum pobierania Microsoft](https://go.microsoft.com/fwlink/?linkid=858257) aka.ms/AFS/agent.

 ##### <a name="changing-the-auto-upgrade-setting"></a>Zmiana ustawienia automatycznego uaktualniania

W poniższych instrukcjach opisano, jak zmienić ustawienia po zakończeniu instalacji, jeśli trzeba wprowadzić zmiany.

Otwórz konsolę programu PowerShell i przejdź do katalogu, w którym zainstalowano agenta synchronizacji, a następnie zaimportuj polecenia cmdlet serwera. Domyślnie będzie to wyglądać mniej więcej tak:
```powershell
cd 'C:\Program Files\Azure\StorageSyncAgent'
Import-Module -Name .\StorageSync.Management.ServerCmdlets.dll
```

Można uruchomić, `Get-StorageSyncAgentAutoUpdatePolicy` aby sprawdzić bieżące ustawienie zasad i określić, czy chcesz go zmienić.

Aby zmienić bieżące ustawienie zasad na ścieżkę opóźnionej aktualizacji, można użyć:
```powershell
Set-StorageSyncAgentAutoUpdatePolicy -PolicyMode UpdateBeforeExpiration
```

Aby zmienić bieżące ustawienie zasad na ścieżkę natychmiastowej aktualizacji, można użyć:
```powershell
Set-StorageSyncAgentAutoUpdatePolicy -PolicyMode InstallLatest
```

#### <a name="agent-lifecycle-and-change-management-guarantees"></a>Gwarancje cyklu życia agenta i zarządzania zmianami
Usługa Azure File Sync to usługa w chmurze, która stale wprowadza nowe funkcje i ulepszenia. Oznacza to, że określona wersja agenta usługi Azure File Sync może być obsługiwana tylko przez ograniczony czas. Aby ułatwić wdrożenie, następujące reguły gwarantują, że masz wystarczająco dużo czasu i powiadomień, aby uwzględnić aktualizacje/uaktualnienia agenta w procesie zarządzania zmianami:

- Wersje głównych agentów są obsługiwane przez co najmniej sześć miesięcy od daty pierwszego wydania.
- Gwarantujemy, że istnieje nakładanie się co najmniej trzech miesięcy między obsługą wersji głównych agentów. 
- Ostrzeżenia są wydawane dla zarejestrowanych serwerów przy użyciu wkrótce wygasłego agenta co najmniej trzy miesiące przed wygaśnięciem. Można sprawdzić, czy zarejestrowany serwer używa starszej wersji agenta w sekcji zarejestrowanych serwerów usługi synchronizacji magazynu.
- Okres istnienia wersji agenta pomocniczego jest powiązany ze skojarzoną wersją główną. Na przykład po wydaniu agenta w wersji 3.0, agent w wersji 2. \* wszystkie zostaną ustawione, aby wygasnąć razem.

> [!Note]
> Zainstalowanie wersji agenta z ostrzeżeniem o wygaśnięciu wyświetli ostrzeżenie, ale zakończy się pomyślnie. Próba zainstalowania lub nawiązania połączenia z wygasłą wersją agenta nie jest obsługiwana i zostanie zablokowana.
