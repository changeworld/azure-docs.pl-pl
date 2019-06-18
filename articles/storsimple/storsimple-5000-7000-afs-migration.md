---
title: Migrowanie danych z rozwiązania StorSimple serii 5000 – 7000 do usługi Azure File Sync | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak przeprowadzić migrację danych z serii StorSimple 5000/7000 do usługi Azure File Sync (AFS).
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
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65150733"
---
# <a name="migrate-data-from-storsimple-5000-7000-series-to-azure-file-sync"></a>Migrację danych z serii StorSimple 5000 – 7000 do usługi Azure File Sync

> [!IMPORTANT]
> Na 9 lipca 2019 koniec stanie pomocy technicznej (EOS) będzie korzystał z serii StorSimple 5000/7000. Zaleca się, że klienci serii StorSimple 5000/7000 migrację do jednej z alternatyw opisanych w dokumencie.

Migracja danych to proces przenoszenia danych z jednej lokalizacji magazynu. Dzięki temu, dzięki czemu dokładną kopię organizacji bieżące dane z jednego urządzenia do innego urządzenia — najlepiej bez zakłócania pracy lub wyłączenie aktywnych aplikacji — a następnie przekierowanie wszystkie wejścia/wyjścia (aktywność We/Wy) do nowego urządzenia. 

StorSimple 5000 i 7000 urządzeń magazynujących serii będzie dotrzeć do końca usługi w lipcu 2019 r. Oznacza to, że Microsoft nie będzie już możliwe do obsługi sprzętu i oprogramowania dla serii StorSimple 5000/7000 po 2019 lipca. Klienci, którzy korzystają z tych urządzeń należy przeprowadzać ich danych w usłudze StorSimple na inne rozwiązania magazynu hybrydowego na platformie Azure. W tym artykule opisano migracji danych z urządzenia z serii StorSimple 5000/7000 do usługi Azure File Sync (AFS).

## <a name="intended-audience"></a>Docelowi odbiorcy

Ten artykuł jest przeznaczony dla specjalistów technologii informatycznych (IT) i wykwalifikowanych pracowników odpowiedzialnych za wdrażanie i zarządzanie nimi urządzeń z serii StorSimple 5000/7000 w centra danych. Klienci, za pomocą ich urządzeń StorSimple dla obciążeń serwera plików (z systemem Windows Server) może się okazać ta ścieżka migracji szczególnie atrakcyjne. Jeśli uważasz, że funkcje usługi Azure File Sync zadziałać dla Twojej organizacji, a następnie ten artykuł pomoże Ci zrozumieć, jak przenieść do tych rozwiązań z rozwiązania StorSimple.

## <a name="migration-considerations"></a>Zagadnienia dotyczące migracji

Ten proces działa w przypadku klientów, którzy skonfigurowali Windows udziału plików za pomocą woluminu StorSimple do magazynowania. Migrowanie danych z usługi StorSimple 5000/7000 do usługi Azure File Sync polega na konwersji lokalizacji udziału plików, aby [punkt końcowy serwera](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning) i za pomocą lokalnie dołączonych dysków jako innego punktu końcowego, który stanie się z nowej lokalizacji. 

Podczas przechodzenia do AFS, należy rozważyć następujące kwestie:

1. Usługa Azure Files obecnie ma ograniczenie 5 TB i udostępniania. To ograniczenie można rozwiązać za pomocą usługi Azure File Sync data rozkłada się na wiele udziałów plików platformy Azure. Aby uzyskać więcej informacji, zobacz [wzorzec wzrostu ilości danych do wdrożenia usługi Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-planning).
2. Ta migracja pobierze całego podstawowego zestawu danych do urządzenia w środowisku lokalnym, jak kopiowanie danych odbywa się za pomocą urządzenia w środowisku lokalnym. Upewnij się, że masz wystarczającą przepustowość do obsługi tego przeniesienia.
3. Ten proces nie zostaną zachowane migawki, które zostały już utworzone. Przeprowadza migrację tylko danych pierwotnych. Proces nie zachowują również szablony przepustowości skojarzone lub zasady tworzenia kopii zapasowych. [Użyj usługi Azure Backup](https://docs.microsoft.com/azure/backup/backup-azure-files) skonfigurować udostępnianie zasady tworzenia kopii zapasowych, po przeprowadzeniu migracji danych w systemie Azure.
4. StorSimple zapewnia pierwszy sprzętu innych firm. Jednak Azure plików/usługi Azure File Sync, której używasz własnego sprzętu lokalnego systemu Windows Server jako lokalnej pamięci podręcznej. Upewnij się, że mają wystarczającą pojemność magazynu zapewnienie zestawu danych, wybranych przez użytkownika lokalnego. Aby uzyskać więcej informacji na temat warstw i ustawienie wymaganego miejsca docelowego, zobacz instrukcje [utworzyć punkt końcowy serwera podczas wdrażania usługi Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=portal). 
5. Przegląd [cennika usługi Azure File Sync](https://azure.microsoft.com/pricing/details/storage/files/) jako różni się on z rozwiązania StorSimple. AFS nie ma potrzeby deduplikacji i kompresji, takich jak usługi StorSimple.

## <a name="migration-prerequisites"></a>Wymagania wstępne dotyczące migracji

W tym miejscu znajdziesz wymagań wstępnych migracji dla starszego urządzenia serii 5000 i 7000 do usługi Azure File Sync. Przed rozpoczęciem upewnij się, że masz:

- Dostęp do StorSimple 5000/7000 series urządzenia uruchomione oprogramowanie wersji v2.1.1.518 lub nowszej. Wcześniejsze wersje nie są obsługiwane. W prawym górnym rogu internetowego interfejsu użytkownika urządzenia StorSimple powinna być wyświetlana wersja oprogramowania, na którym jest uruchomiony.  
    Jeśli urządzenie nie jest uruchomiona v2.1.1.518, Uaktualnij system do wymaganej wersji minimalnej. Aby uzyskać szczegółowe instrukcje, zapoznaj się [uaktualnić system do v2.1.1.518](http://onlinehelp.storsimple.com/111_Appliance/6_System_Upgrade_Guides/Current_(v2.1.1)/000_Software_Patch_Upgrade_Guide_v2.1.1.518).
- Sprawdź, czy aktywne zadania tworzenia kopii zapasowej, które są uruchomione na urządzeniu źródłowym. Obejmuje to zadania na hoście konsoli ochrony danych StorSimple. Poczekaj, aż bieżące zadania do wykonania. 
- Dostęp do hosta systemu Windows Server podłączone do usługi StorSimple, urządzenie z serii 5000 – 7000. Host musi być z obsługiwaną wersją systemu Windows Server zgodnie z opisem w [współdziałania usługi Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning).
- Woluminy StorSimple są zainstalowane na hoście, a także zawierać udziały plików.
- Host ma wystarczającej ilości miejsca lokalnego do przechowywania lokalnie buforowanych danych.
- Poziom dostępu właściciela do subskrypcji platformy Azure, który będzie używany do wdrażania usługi Azure File Sync. Mogą wystąpić problemy podczas tworzenia punktu końcowego w chmurze grupy synchronizacji, jeśli nie masz właściciela lub uprawnienia na poziomie administratora.
- Dostęp do [konta magazynu ogólnego przeznaczenia v2](https://docs.microsoft.com/azure/storage/common/storage-account-overview) z udziału plików platformy Azure, który chcesz zsynchronizować z. Aby uzyskać więcej informacji, zobacz [Tworzenie konta magazynu](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account).
  - Jak [utworzyć udział plików platformy Azure](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-file-share).

## <a name="migration-process"></a>Proces migracji

Migrowanie danych z rozwiązania StorSimple 5000 – 7000 do AFS jest procesem dwuetapowym:
1.  Replikowane dane z serwera plików w środowisku lokalnym, gdzie woluminy StorSimple są instalowane do usługi Azure Files udostępniania.  Replikacja odbywa się za pośrednictwem agenta AFS instalowanej.
2.  Odłącz urządzenia StorSimple. Dyski lokalne działa jako lokalną pamięć podręczną.

### <a name="migration-steps"></a>Kroki migracji

Wykonaj poniższe kroki, aby migrować udziału plików Windows, które są skonfigurowane na woluminy StorSimple do udziału usługi Azure File Sync. 
1.  Te kroki należy wykonać na tym samym hoście systemu Windows Server, gdzie są zainstalowane woluminy StorSimple, lub użyj innego systemu. 
    - [Przygotowanie systemu Windows Server za pomocą usługi Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#prepare-windows-server-to-use-with-azure-file-sync).
    - [Zainstaluj agenta usługi Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#install-the-azure-file-sync-agent).
    - [Wdrażanie usługi synchronizacji magazynu](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#deploy-the-storage-sync-service). 
    - [Rejestrowanie systemu Windows Server za pomocą usługi synchronizacji magazynu](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#register-windows-server-with-storage-sync-service). 
    - [Tworzenie grupy synchronizacji i punktu końcowego w chmurze](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#create-a-sync-group-and-a-cloud-endpoint). Grupy synchronizacji należy podjąć, dla każdego udziału plików Windows, które muszą zostać zmigrowane z hosta.
    - [Tworzenie punktu końcowego serwera](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=portal#create-a-server-endpoint). Określ ścieżkę jako ścieżki woluminu StorSimple, która zawiera dane udziału plików. Na przykład, jeśli woluminu StorSimple jest dysk `J`, a Twoje dane znajdują się w `J:/<myafsshare>`, następnie dodaj tę ścieżkę jako punkt końcowy serwera. Pozostaw **warstw** jako **wyłączone**.
2.  Poczekaj, aż synchronizacja serwera plików jest pełny. Dla każdego serwera w grupie synchronizacji danego upewnij się, że:
    - Ostatnie są sygnatury czasowe dla ostatniej synchronizacji próba przekazywania i pobierania.
    - Stan jest zielony przekazywania i pobierania.
    - **Działanie synchronizacji** przedstawia bardzo mało lub nie plików pozostałych do synchronizacji.
    - **Nie synchronizuje pliki** ma wartość 0 dla przekazywania i pobierania.
    Aby uzyskać więcej informacji na temat po zakończeniu synchronizacji serwera, przejdź do [Rozwiązywanie problemów z usługi Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#how-do-i-know-if-my-servers-are-in-sync-with-each-other). Synchronizacja może potrwać kilka godzin, dni, w zależności od rozmiaru danych i przepustowości. Po zakończeniu synchronizacji, wszystkie dane są bezpiecznie w udziale plików platformy Azure. 
3.  Przejdź do udziałów woluminów StorSimple. Wybierz udział, kliknij prawym przyciskiem myszy i wybierz **właściwości**. Należy pamiętać, uprawnienia udziału w obszarze **zabezpieczeń**. Te uprawnienia będą musieli ręcznie można zastosować do nowego udziału w późniejszym kroku.
4.  W zależności od tego, czy używasz tego samego hosta systemu Windows Server lub innego kolejne kroki może się różnić.

    Pomiń ten krok i przejdź do następnego kroku, jeśli używasz innego hosta Windows Server. Jeśli używasz tego samego serwera plików Windows dla AFS, teraz doświadczy za kilka minut przestoju. 
    - **Przestój rozpocznie się** -Usuń punkt końcowy serwera, który został utworzony w *krok 1F*. 
    - Utwórz nowy punkt końcowy serwera przy użyciu ścieżki, gdzie mają dane znajdują się przerywaj do przodu.
    - Gdy punkt końcowy serwera znajduje się w dobrej kondycji (może to potrwać kilka minut), zobaczysz dane w tej nowej lokalizacji. Teraz można skonfigurować hosta Windows Server do obsługi plików z tej nowej lokalizacji. -  **Kończy się przestojów**.
5.  Jeśli korzystania z innego serwera plików Windows dla usługi Azure File Sync mogą nie mieć żadnych przestojów. 
    - Dodaj inny punkt końcowy serwera o ścieżce lokalnej pamięci masowej, które są przygotowywane do użycia jako pamięci podręcznej audytów urządzenia StorSimple. 
    - Można wyświetlić pliki w nowym serwerze w ciągu kilku minut. Mogą na zamianę z urządzenia StorSimple do nowej lokalizacji na hoście w dowolnym momencie.

    > [!TIP] 
    > Zaleca się skonfigurowanie tego nowego udziału plików przy użyciu tej samej nazwie i tej samej ścieżki, ponieważ zastępuje jeden it, aby zminimalizować zakłócenia. Jeśli używasz systemu plików DFS-N, może to wymagać wprowadzenia zmian w konfiguracji.
6.  Ponownie skonfiguruj uprawnienia do udostępniania, jak wspomniano w *kroku 3*.

Jeśli napotkasz jakiekolwiek problemy podczas migracji danych [skontaktuj się z pomocą techniczną firmy Microsoft](storsimple-8000-contact-microsoft-support.md). 



## <a name="next-steps"></a>Kolejne kroki

Jeśli AFS nie jest właściwe rozwiązanie dla Ciebie, Dowiedz się, jak [migracji danych z magazynu StorSimple serii 5000 – 7000 do urządzenia z serii 8000](storsimple-8000-migrate-from-5000-7000.md).

