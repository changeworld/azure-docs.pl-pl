---
title: Migrowanie danych z serii StorSimple 5000-7000 do usługi Azure File Sync| Dokumenty firmy Microsoft
description: W tym artykule opisano sposób migracji danych z serii StorSimple 5000/7000 do usługi Azure File Sync (AFS).
services: storsimple
documentationcenter: NA
author: alkohli
manager: twooley
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/19/2019
ms.author: alkohli
ms.openlocfilehash: b46e9ee8fc3e14981a01cc2425a8ce55d06c5a9a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "65150733"
---
# <a name="migrate-data-from-storsimple-5000-7000-series-to-azure-file-sync"></a>Migrowanie danych z serii StorSimple 5000-7000 do usługi Azure File Sync

> [!IMPORTANT]
> 9 lipca 2019 r. seria StorSimple 5000/7000 osiągnie status końca wsparcia (EOS). Firma Microsoft zaleca, aby klienci serii StorSimple 5000/7000 migrować do jednej z alternatyw opisanych w dokumencie.

Migracja danych to proces przenoszenia danych z jednej lokalizacji magazynu do drugiej. Wiąże się to z wykonaniem dokładnej kopii bieżących danych organizacji z jednego urządzenia na inne — najlepiej bez zakłócania lub wyłączania aktywnych aplikacji — a następnie przekierowaniem wszystkich działań wejścia/wyjścia (We/Wy) do nowego urządzenia. 

Urządzenia pamięci masowej storSimple serii 5000 i 7000 zakończą się w lipcu 2019 roku. Oznacza to, że firma Microsoft nie będzie już mogła obsługiwać sprzętu i oprogramowania dla serii StorSimple 5000/7000 po lipcu 2019. Klienci korzystający z tych urządzeń powinni przeprowadzić migrację danych StorSimple do innych rozwiązań magazynu hybrydowego na platformie Azure. W tym artykule opisano migrację danych z urządzenia z serii StorSimple 5000/7000 do usługi Azure File Sync (AFS).

## <a name="intended-audience"></a>Docelowi odbiorcy

Ten artykuł jest przeznaczony dla specjalistów technologii informatycznych (IT) i pracowników wiedzy odpowiedzialnych za wdrażanie i zarządzanie urządzeniami z serii StorSimple 5000/7000 w centrum danych. Klienci korzystający ze swoich urządzeń StorSimple dla obciążeń serwera plików (z systemem Windows Server) mogą uznać tę ścieżkę migracji za szczególnie atrakcyjną. Jeśli uważasz, że funkcje usługi Azure File Sync działają dobrze dla Twojej organizacji, w tym artykule pomoże Ci zrozumieć, jak przejść do tych rozwiązań z StorSimple.

## <a name="migration-considerations"></a>Zagadnienia dotyczące migracji

Ten proces działa dla klientów, którzy skonfigurowali udział plików systemu Windows przy użyciu woluminu StorSimple do przechowywania. Migrowanie danych z storsimple 5000/7000 do usługi Azure File Sync obejmuje konwertowanie tej lokalizacji udziału plików do [punktu końcowego serwera,](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning) a następnie przy użyciu lokalnie dołączonych dysków jako innego punktu końcowego, który następnie stanie się nową lokalizacją. 

Podczas przechodzenia do AFS należy wziąć pod uwagę następujące kwestie:

1. Usługa Azure Files ma obecnie ograniczenie 5 TB/udział. To ograniczenie można przezwyciężyć przy użyciu usługi Azure File Sync z danymi rozłożonymi na wiele udziałów usługi Azure File. Aby uzyskać więcej informacji, zapoznaj [się z wzorcem wzrostu danych dla wdrożenia usługi Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-planning).
2. Ta migracja pobiera cały zestaw danych podstawowych do urządzenia lokalnego, jak kopia danych odbywa się z urządzenia lokalnego. Upewnij się, że masz wystarczającą przepustowość, aby pomieścić ten transfer.
3. Ten proces nie zachowuje migawek, które zostały już utworzone. Migruje tylko dane podstawowe. Proces nie zachowuje również skojarzonych szablonów przepustowości ani zasad tworzenia kopii zapasowych. [Użyj usługi Azure Backup,](https://docs.microsoft.com/azure/backup/backup-azure-files) aby skonfigurować zasady tworzenia kopii zapasowych po migracji danych w udziale plików platformy Azure.
4. StorSimple udostępnia sprzęt pierwszej firmy. Jednak w przypadku usługi Azure Files/Azure File Sync używasz własnego lokalnego sprzętu z systemem Windows Server jako lokalnej pamięci podręcznej. Należy upewnić się, że masz wystarczającą pojemność magazynu, aby zachować wybrany zestaw danych na lokalnym miejscu. Aby uzyskać więcej informacji na temat warstwowania i ustawiania wymaganego miejsca docelowego wolnego miejsca, zapoznaj się z tym, jak [utworzyć punkt końcowy serwera podczas wdrażania usługi Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=portal). 
5. Przejrzyj [ceny dla usługi Azure File Sync,](https://azure.microsoft.com/pricing/details/storage/files/) ponieważ różni się od StorSimple. AFS nie ma deduplikacji i kompresji jak StorSimple.

## <a name="migration-prerequisites"></a>Wymagania wstępne dotyczące migracji

W tym miejscu znajdziesz wymagania wstępne dotyczące migracji starszego urządzenia z serii 5000 lub 7000 do usługi Azure File Sync. Przed rozpoczęciem upewnij się, że:

- Dostęp do urządzenia z serii StorSimple 5000/7000 z systemem oprogramowania w wersji 2.1.1.518 lub nowszej. Starsze wersje nie są obsługiwane. W prawym górnym rogu interfejsu użytkownika sieci Web urządzenia StorSimple powinna być wyświetlana wersja oprogramowania, która jest uruchomiona.  
    Jeśli na urządzeniu nie jest uruchomiony system wersji 2.1.1.518, należy uaktualnić system do wymaganej wersji minimalnej. Szczegółowe instrukcje można znaleźć w [: Uaktualnianie systemu do wersji 2.1.1.518](http://onlinehelp.storsimple.com/111_Appliance/6_System_Upgrade_Guides/Current_(v2.1.1)/000_Software_Patch_Upgrade_Guide_v2.1.1.518).
- Sprawdź, czy nie ma aktywnych zadań tworzenia kopii zapasowych, które są uruchomione na urządzeniu źródłowym. Obejmuje to zadania na hoście Konsoli ochrony danych StorSimple. Poczekaj na zakończenie bieżących zadań. 
- Dostęp do hosta systemu Windows Server podłączonego do urządzenia z serii StorSimple 5000-7000. Host musi mieć obsługiwaną wersję systemu Windows Server zgodnie z opisem w [programie Azure File Sync .](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning)
- StorSimple woluminy są zainstalowane na hoście i zawierają udziały plików.
- Host ma wystarczającą ilość magazynu lokalnego do przechowywania lokalnie buforowanych danych.
- Dostęp na poziomie właściciela do subskrypcji platformy Azure, która będzie używana do wdrażania synchronizacji plików platformy Azure. Mogą wystąpić problemy podczas tworzenia punktu końcowego chmury dla grupy synchronizacji, jeśli nie masz uprawnień na poziomie właściciela lub administratora.
- Dostęp do [konta magazynu ogólnego przeznaczenia w wersji 2](https://docs.microsoft.com/azure/storage/common/storage-account-overview) z udziałem plików platformy Azure, z którego chcesz zsynchronizować. Aby uzyskać więcej informacji, zobacz temat [Tworzenie konta](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account).
  - Jak [utworzyć udział plików platformy Azure](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-file-share).

## <a name="migration-process"></a>Proces migracji

Migracja danych z StorSimple 5000-7000 do AFS jest procesem dwuetapowym:
1.  Replikuj dane z lokalnego serwera plików, gdzie woluminy StorSimple są zainstalowane w udziale usługi Azure Files.  Replikacja odbywa się za pośrednictwem agenta AFS, który można zainstalować.
2.  Odłącz urządzenie StorSimple. Dyski lokalne działają następnie jako lokalna pamięć podręczna.

### <a name="migration-steps"></a>Kroki migracji

Wykonaj następujące kroki, aby przeprowadzić migrację udziału plików systemu Windows skonfigurowanego na woluminach StorSimple do udziału usługi Azure File Sync. 
1.  Wykonaj te kroki na tym samym hoście systemu Windows Server, na którym są zainstalowane woluminy StorSimple lub użyj innego systemu. 
    - [Przygotuj system Windows Server do użycia z synchronizacją plików platformy Azure](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#prepare-windows-server-to-use-with-azure-file-sync).
    - [Zainstaluj agenta synchronizacji plików platformy Azure](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#install-the-azure-file-sync-agent).
    - [Wdrażanie usługi synchronizacji magazynu](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#deploy-the-storage-sync-service). 
    - [Zarejestruj system Windows Server z usługą synchronizacji magazynu](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#register-windows-server-with-storage-sync-service). 
    - [Utwórz grupę synchronizacji i punkt końcowy w chmurze](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#create-a-sync-group-and-a-cloud-endpoint). Grupy synchronizacji muszą być wykonane dla każdego udziału plików systemu Windows, który musi zostać zmigrowany z hosta.
    - [Tworzenie punktu końcowego serwera](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=portal#create-a-server-endpoint). Określ ścieżkę jako ścieżkę woluminu StorSimple, który zawiera dane udziału plików. Na przykład jeśli wolumin StorSimple `J`jest dyskiem, a `J:/<myafsshare>`dane znajdują się w , a następnie dodaj tę ścieżkę jako punkt końcowy serwera. Pozostaw **warstwę** jako **wyłączona**.
2.  Poczekaj na zakończenie synchronizacji serwera plików. Dla każdego serwera w danej grupie synchronizacji upewnij się, że:
    - Sygnatury czasowe ostatniej próby synchronizacji zarówno dla przekazywania, jak i pobierania są najnowsze.
    - Stan jest zielony zarówno dla wysyłania, jak i pobierania.
    - Działanie **synchronizacji** pokazuje bardzo niewiele plików lub nie ma żadnych plików pozostałych do synchronizacji.
    - **Niesynchronizowane pliki** jest 0 dla przekazywania i pobierania.
    Aby uzyskać więcej informacji na temat zakończenia synchronizacji serwera, przejdź do [witryny Rozwiązywanie problemów z synchronizacją plików platformy Azure](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#how-do-i-know-if-my-servers-are-in-sync-with-each-other). Synchronizacja może potrwać kilka godzin do dni, w zależności od rozmiaru danych i przepustowości. Po zakończeniu synchronizacji wszystkie dane są bezpiecznie w udziale plików platformy Azure. 
3.  Przejdź do akcji na StorSimple woluminów. Wybierz udział, kliknij prawym przyciskiem myszy i wybierz polecenie **Właściwości**. Zanotuj uprawnienia udziału w obszarze **Zabezpieczenia**. Te uprawnienia będą musiały być stosowane ręcznie do nowego udziału w późniejszym kroku.
4.  W zależności od tego, czy używasz tego samego hosta systemu Windows Server, czy innego, następne kroki będą inne.

    Pomiń ten krok i przejdź do następnego kroku, jeśli używasz innego hosta systemu Windows Server. Jeśli używasz tego samego serwera plików systemu Windows dla systemu AFS, teraz wystąpi kilka minut przestoju. 
    - **Rozpoczyna się przestoje** — usuwanie punktu końcowego serwera utworzonego w *kroku 1F*. 
    - Utwórz nowy punkt końcowy serwera ze ścieżką, w której mają się od niej przebywać w przyszłości.
    - Gdy punkt końcowy serwera pokazuje jako w dobrej kondycji (może to potrwać kilka minut), zobaczysz dane w tej nowej lokalizacji. Teraz można skonfigurować hosta systemu Windows Server do obsługi plików z tej nowej lokalizacji. - **Koniec przestojów**.
5.  Jeśli używasz innego serwera plików systemu Windows dla synchronizacji plików platformy Azure, nie wystąpią żadne przestoje. 
    - Dodaj kolejny punkt końcowy serwera ze ścieżką magazynu lokalnego, który jest przygotowany do użycia jako pamięć podręczna zamiast urządzenia StorSimple. 
    - Pliki na nowym serwerze będzie można zobaczyć w ciągu kilku minut. W każdej chwili możesz w każdej chwili przełączyć się z urządzenia StorSimple do tej nowej lokalizacji na hoście.

    > [!TIP] 
    > Należy rozważyć skonfigurowanie tego nowego udziału plików o tej samej nazwie i tej samej ścieżce, co ta, którą zastępuje, aby zminimalizować zakłócenia. W przypadku korzystania z systemu DFS-N może to wymagać zmiany w konfiguracji.
6.  Ponownie skonfiguruj uprawnienia udostępniania, jak wspomniano w *kroku 3*.

Jeśli podczas migracji danych wystąpią jakiekolwiek problemy, [skontaktuj się z pomocą techniczną firmy Microsoft](storsimple-8000-contact-microsoft-support.md). 



## <a name="next-steps"></a>Następne kroki

Jeśli afs nie jest właściwym rozwiązaniem dla Ciebie, dowiedz się, jak [migrować dane z serii StorSimple 5000-7000 do urządzenia z serii 8000](storsimple-8000-migrate-from-5000-7000.md).

