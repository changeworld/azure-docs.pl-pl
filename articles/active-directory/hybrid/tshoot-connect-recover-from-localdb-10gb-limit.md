---
title: 'Usługa Azure AD Connect: jak odzyskać dane z powodu problemu z limitem localdb 10 GB | Dokumenty firmy Microsoft'
description: W tym temacie opisano sposób odzyskiwania usługi synchronizacji usługi Azure AD Connect, gdy napotka problem z limitem LocalDB 10 GB.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 41d081af-ed89-4e17-be34-14f7e80ae358
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4d420c64c5834f7d3cb11d2f5f59e3ed85a54891
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60386928"
---
# <a name="azure-ad-connect-how-to-recover-from-localdb-10-gb-limit"></a>Azure AD Connect: jak usunąć limit 10 GB dla lokalnej bazy danych
Program Azure AD Connect wymaga bazy danych programu SQL Server do przechowywania danych tożsamości. Możesz korzystać z domyślnego programu SQL Server 2012 Express LocalDB zainstalowanego z programem Azure AD Connect lub użyć własnego pełnego programu SQL. Program SQL Server Express narzuca limit rozmiaru w wysokości 10 GB. Jeśli jest używany program LocalDB i limit zostanie osiągnięty, usługa synchronizacji programu Azure AD Connect nie będzie mogła uruchomić się ani prawidłowo wykonywać synchronizacji. Ten artykuł zawiera kroki odzyskiwania.

## <a name="symptoms"></a>Objawy
Istnieją dwa typowe objawy:

* Usługa synchronizacji usługi Azure AD Connect **jest uruchomiona,** ale nie można zsynchronizować z błędem *"stopped-database-disk-full".*

* **Nie można uruchomić**usługi synchronizacji usługi Azure AD Connect . Podczas próby uruchomienia usługi, kończy się niepowodzeniem ze zdarzeniem 6323 i komunikat o błędzie *"Serwer napotkał błąd, ponieważ SQL Server jest na dysku."*

## <a name="short-term-recovery-steps"></a>Krótkoterminowe kroki odzyskiwania
Ta sekcja zawiera kroki, aby odzyskać miejsce w db wymagane dla usługi Synchronizacji usługi Azure AD Connect, aby wznowić działanie. Kroki obejmują:
1. [Określanie stanu usługi synchronizacji](#determine-the-synchronization-service-status)
2. [Zmniejszanie bazy danych](#shrink-the-database)
3. [Usuwanie danych historii uruchamiania](#delete-run-history-data)
4. [Skróć okres przechowywania danych historii uruchamiania](#shorten-retention-period-for-run-history-data)

### <a name="determine-the-synchronization-service-status"></a>Określanie stanu usługi synchronizacji
Najpierw określ, czy usługa synchronizacji jest nadal uruchomiona, czy nie:

1. Zaloguj się do serwera usługi Azure AD Connect jako administrator.

2. Przejdź do **Menedżera sterowania usługami**.

3. Sprawdź stan usługi **Microsoft Azure AD Sync**.


4. Jeśli jest uruchomiona, nie należy zatrzymywać ani ponownie uruchamiać usługi. Pomiń [krok Zmniejszanie bazy danych](#shrink-the-database) i przejdź do kroku Usuń dane historii [uruchamiania.](#delete-run-history-data)

5. Jeśli nie jest uruchomiona, spróbuj uruchomić usługę. Jeśli usługa zostanie pomyślnie uruchomiony, pomiń krok [Zmniejsz bazę danych](#shrink-the-database) i przejdź do kroku [Usuń dane historii uruchamiania.](#delete-run-history-data) W przeciwnym razie kontynuuj z [krokiem Zmniejszanie bazy danych.](#shrink-the-database)

### <a name="shrink-the-database"></a>Zmniejszanie bazy danych
Użyj operacji Shrink, aby zwolnić wystarczającą ilość miejsca w db, aby uruchomić usługę synchronizacji. Zwalnia miejsce w bazie danych, usuwając odstępy w bazie danych. Ten krok jest najlepszym wysiłkiem, ponieważ nie jest gwarantowana, że zawsze można odzyskać miejsce. Aby dowiedzieć się więcej o operacji Zmniejszanie, przeczytaj ten artykuł [Zmniejszanie bazy danych](https://msdn.microsoft.com/library/ms189035.aspx).

> [!IMPORTANT]
> Pomiń ten krok, jeśli można uruchomić usługę synchronizacji. Nie zaleca się zmniejszania bazy danych SQL, ponieważ może to prowadzić do niskiej wydajności ze względu na zwiększone fragmentację.

Nazwa bazy danych utworzonej dla usługi Azure AD Connect to **ADSync**. Aby wykonać operację Shrink, należy zalogować się jako sysadmin lub DBO bazy danych. Podczas instalacji usługi Azure AD Connect następujące konta są przyznawane prawa sysadmin:
* Administratorzy lokalni
* Konto użytkownika, które zostało użyte do uruchomienia instalacji usługi Azure AD Connect.
* Konto usługi synchronizacji, które jest używane jako kontekst operacyjny usługi synchronizacji usługi Azure AD Connect.
* Lokalna grupa ADSyncAdmins, który został utworzony podczas instalacji.

1. Skopiuj kopię zapasową bazy danych, kopiując pliki **ADSync.mdf** i **ADSync_log.ldf** znajdujące się w `%ProgramFiles%\Microsoft Azure AD Sync\Data` bezpiecznej lokalizacji.

2. Rozpocznij nową sesję programu PowerShell.

3. Przejdź do `%ProgramFiles%\Microsoft SQL Server\110\Tools\Binn`folderu .

4. Uruchom narzędzie **sqlcmd,** `./SQLCMD.EXE -S "(localdb)\.\ADSync" -U <Username> -P <Password>`uruchamiając polecenie , używając poświadczenia sysadmin lub dbo bazy danych.

5. Aby zmniejszyć bazę danych, w wierszu sqlcmd `DBCC Shrinkdatabase(ADSync,1);`(1 `GO`>), wprowadź , a następnie w następnym wierszu.

6. Jeśli operacja zakończy się pomyślnie, spróbuj ponownie uruchomić usługę synchronizacji. Jeśli można uruchomić usługę synchronizacji, przejdź do [kroku Usuń dane historii uruchamiania.](#delete-run-history-data) Jeśli nie, skontaktuj się z pomocą techniczną.

### <a name="delete-run-history-data"></a>Usuwanie danych historii uruchamiania
Domyślnie usługa Azure AD Connect zachowuje do siedmiu dni wartości danych historii uruchamiania. W tym kroku usuwamy dane historii uruchamiania, aby odzyskać miejsce w db, dzięki czemu usługa synchronizacji usługi Azure AD Connect może ponownie rozpocząć synchronizację.

1. Uruchom **Menedżera usług synchronizacji,** przechodząc do START → Usługa synchronizacji.

2. Przejdź do karty **Operacje.**

3. W obszarze **Akcje**wybierz **pozycję Wyczyść przebiegi**...

4. Możesz wybrać **wyczyść wszystkie przebiegi** lub **Wyczyść przebiegi przed... data \<>** opcja. Zaleca się, aby rozpocząć od wyczyszczenie danych historii uruchamiania, które są starsze niż dwa dni. Jeśli nadal występuje problem z rozmiarem bazy danych, wybierz opcję **Wyczyść wszystkie uruchomienia.**

### <a name="shorten-retention-period-for-run-history-data"></a>Skróć okres przechowywania danych historii uruchamiania
Ten krok ma na celu zmniejszenie prawdopodobieństwa uruchomienia w 10 GB problem limit po wielu cyklach synchronizacji.

1. Otwórz nową sesję programu PowerShell.

2. Uruchom `Get-ADSyncScheduler` i zanotuj właściwość PurgeRunHistoryInterval, która określa bieżący okres przechowywania.

3. Uruchom, `Set-ADSyncScheduler -PurgeRunHistoryInterval 2.00:00:00` aby ustawić okres przechowywania na dwa dni. Odpowiednio dostosuj okres przechowywania.

## <a name="long-term-solution--migrate-to-full-sql"></a>Rozwiązanie długoterminowe — migracja do pełnego SQL
Ogólnie rzecz biorąc problem wskazuje, że rozmiar bazy danych 10 GB nie jest już wystarczający, aby usługa Azure AD Connect zsynchronizować lokalną usługę Active Directory z usługą Azure AD. Zaleca się przełączenie do korzystania z pełnej wersji serwera SQL. Nie można bezpośrednio zastąpić programu LocalDB istniejącego wdrożenia programu Azure AD Connect za pomocą pełnej wersji programu SQL. Zamiast tego należy wdrożyć nowy serwer programu Azure AD Connect z pełną wersją programu SQL. Zalecana jest migracja typu swing, gdzie nowy serwer programu Azure AD Connect (z bazą danych w programie SQL) jest wdrażany jako serwer przejściowy obok istniejącego serwera programu Azure AD Connect (z programem LocalDB). 
* Instrukcje dotyczące konfigurowania zdalnego programu SQL pod kątem programu Azure AD Connect znajdują się w artykule [Niestandardowa instalacja programu Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-custom).
* Instrukcje dotyczące migracji typu swing w ramach uaktualniania programu Azure AD Connect znajdują się w artykule [Azure AD Connect: Upgrade from a previous version to the latest](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-upgrade-previous-version#swing-migration) (Azure AD Connect: Uaktualnianie z poprzedniej wersji do najnowszej).

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej na temat [integrowania tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md).
