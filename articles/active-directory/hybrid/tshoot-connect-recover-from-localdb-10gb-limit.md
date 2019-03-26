---
title: 'Program Azure AD Connect: Jak odzyskać z lokalnej bazy danych problem wywołany limitem 10 GB | Dokumentacja firmy Microsoft'
description: W tym temacie opisano sposób odzyskiwania usługi Azure AD Connect synchronizacji po napotkaniu LocalDB 10GB ograniczyć wydania.
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
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2019
ms.locfileid: "58435603"
---
# <a name="azure-ad-connect-how-to-recover-from-localdb-10-gb-limit"></a>Program Azure AD Connect: Jak usunąć limit 10 GB dla lokalnej bazy danych
Program Azure AD Connect wymaga bazy danych programu SQL Server do przechowywania danych tożsamości. Możesz korzystać z domyślnego programu SQL Server 2012 Express LocalDB zainstalowanego z programem Azure AD Connect lub użyć własnego pełnego programu SQL. Program SQL Server Express narzuca limit rozmiaru w wysokości 10 GB. Jeśli jest używany program LocalDB i limit zostanie osiągnięty, usługa synchronizacji programu Azure AD Connect nie będzie mogła uruchomić się ani prawidłowo wykonywać synchronizacji. Ten artykuł zawiera kroki odzyskiwania.

## <a name="symptoms"></a>Objawy
Istnieją dwie typowe objawy:

* Usługa Azure AD Connect synchronizacji **działa** , ale nie powiedzie się zapewnić synchronizację z *"zatrzymane database pełnego dysku"* błędu.

* Usługa Azure AD Connect synchronizacji **nie może uruchomić**. Przy próbie uruchomienia usługi pojawia się błąd komunikat o błędzie i zdarzenia 6323 *"Serwer napotkał błąd, ponieważ serwer SQL jest mało miejsca na dysku."*

## <a name="short-term-recovery-steps"></a>Procedura odzyskiwania krótkoterminowego
Ta sekcja zawiera instrukcje dotyczące odzyskania miejsca w bazie danych wymaganych do usługi Azure AD Connect synchronizacji można wznowić operacji. Kroki obejmują:
1. [Sprawdź stan usługi synchronizacji](#determine-the-synchronization-service-status)
2. [Zmniejszanie bazy danych](#shrink-the-database)
3. [Usuwanie danych historii uruchamiania](#delete-run-history-data)
4. [Skróć okres przechowywania danych historii uruchamiania](#shorten-retention-period-for-run-history-data)

### <a name="determine-the-synchronization-service-status"></a>Sprawdź stan usługi synchronizacji
Najpierw określić, czy usługa synchronizacji jest nadal uruchomiona, czy nie:

1. Zaloguj się do serwera Azure AD Connect, jako administrator.

2. Przejdź do **Menedżera sterowania usługami**.

3. Sprawdź stan **Microsoft Azure AD Sync**.


4. Jeśli jest uruchomiona, Zatrzymaj lub nie ponownie uruchomić usługę. Pomiń [bazy danych zmniejszyć](#shrink-the-database) krok i przejdź do [usuwania danych historii uruchamiania](#delete-run-history-data) kroku.

5. Jeśli nie jest uruchomiona, spróbuj uruchomić usługę. Jeśli usługa zostanie uruchomiony pomyślnie, Pomiń [bazy danych zmniejszyć](#shrink-the-database) krok i przejdź do [usuwania danych historii uruchamiania](#delete-run-history-data) kroku. W przeciwnym razie kontynuuj [bazy danych zmniejszyć](#shrink-the-database) kroku.

### <a name="shrink-the-database"></a>Zmniejszanie bazy danych
Za pomocą operacji zmniejszania zwolnienia wystarczającej ilości miejsca w bazie danych można uruchomić usługi synchronizacji. Umożliwiliśmy miejsca w bazie danych, usuwając białych znaków w bazie danych. Ten krok jest optymalny, ponieważ nie gwarantuje, że zawsze przeprowadza odzyskiwanie miejsca. Aby dowiedzieć się więcej na temat operacji zmniejszania, przeczytaj ten artykuł [baza danych](https://msdn.microsoft.com/library/ms189035.aspx).

> [!IMPORTANT]
> Pomiń ten krok, jeśli możesz zainstalować usługę synchronizacji do uruchomienia. Nie zaleca się zmniejszyć bazy danych SQL, ponieważ może to prowadzić do niskiej wydajności ze względu na większą fragmentacji.

Nazwa bazy danych utworzone dla programu Azure AD Connect jest **ADSync**. Aby wykonać operację zmniejszania, musisz zalogować się jako administratora systemu lub DBO bazy danych. Podczas instalacji programu Azure AD Connect następujące konta mają prawa administratora systemu:
* Administratorzy lokalni
* Konto użytkownika, który został użyty do uruchamiania instalacji programu Azure AD Connect.
* Konto usługi synchronizacji, która jest używana jako kontekst operacyjny z usługi Azure AD Connect synchronizacji.
* Lokalne grupy ADSyncAdmins, która została utworzona podczas instalacji.

1. Utwórz kopię zapasową bazy danych przez skopiowanie **ADSync.mdf** i **ADSync_log.ldf** plików znajdujących się w obszarze `%ProgramFiles%\Microsoft Azure AD Sync\Data` do bezpiecznej lokalizacji.

2. Rozpocznij nową sesję programu PowerShell.

3. Przejdź do folderu `%ProgramFiles%\Microsoft SQL Server\110\Tools\Binn`.

4. Rozpocznij **sqlcmd** narzędzie, uruchamiając polecenie `./SQLCMD.EXE -S "(localdb)\.\ADSync" -U <Username> -P <Password>`, przy użyciu poświadczeń administratora systemu lub bazy danych DBO.

5. Aby zmniejszyć bazy danych, w tym celu w wierszu sqlcmd (1 >), wprowadź `DBCC Shrinkdatabase(ADSync,1);`, a następnie `GO` w następnym wierszu.

6. Jeśli operacja się powiedzie, spróbuj ponownie uruchomić usługi synchronizacji. Jeśli można uruchomić usługi synchronizacji, przejdź do strony [usuwania danych historii uruchamiania](#delete-run-history-data) kroku. W przeciwnym razie skontaktuj się z pomocą techniczną.

### <a name="delete-run-history-data"></a>Usuwanie danych historii uruchamiania
Domyślnie program Azure AD Connect przechowuje siedem dni, przez które dane historii uruchamiania. W tym kroku usuniemy dane historii uruchamiania w celu odzyskania miejsca w bazie danych, tak, aby uruchomić usługi Azure AD Connect synchronizacji ponowić synchronizację.

1. Rozpocznij **Menedżera usługi synchronizacji** , przechodząc do rozpoczęcia → usługi synchronizacji.

2. Przejdź do **operacji** kartę.

3. W obszarze **akcje**, wybierz opcję **uruchomień Clear**...

4. Można wybrać **wyczyść wszystkie przebiegi** lub **wyczyść jest uruchamiany przed... \<daty >** opcji. Zaleca się uruchamiania przez wyczyszczenie danych historii, które są starsze niż dwa dni uruchamiania. Jeśli będziesz kontynuować napotkać problem rozmiar bazy danych, wybierz **wyczyść wszystkie przebiegi** opcji.

### <a name="shorten-retention-period-for-run-history-data"></a>Skróć okres przechowywania danych historii uruchamiania
Ten krok jest zmniejszenie prawdopodobieństwa przekroczone problem limitu 10 GB po wiele cykli synchronizacji.

1. Otwórz nową sesję programu PowerShell.

2. Uruchom `Get-ADSyncScheduler` i zwróć uwagę na właściwość PurgeRunHistoryInterval, która określa bieżący okres przechowywania.

3. Uruchom `Set-ADSyncScheduler -PurgeRunHistoryInterval 2.00:00:00` można ustawić okresu przechowywania do dwóch dni. Ustaw okres przechowywania, zgodnie z potrzebami.

## <a name="long-term-solution--migrate-to-full-sql"></a>Rozwiązaniem długoterminowym — migracja do pełnego rozwiązania SQL
Ogólnie rzecz biorąc problem polega na wskazywać, że rozmiar bazy danych 10 GB nie jest już wystarczająca dla usługi Azure AD Connect, aby zsynchronizować z lokalnej usługi Active Directory do usługi Azure AD. Zaleca się przejście na pełną wersję programu SQL server. Nie można bezpośrednio zastąpić programu LocalDB istniejącego wdrożenia programu Azure AD Connect za pomocą pełnej wersji programu SQL. Zamiast tego należy wdrożyć nowy serwer programu Azure AD Connect z pełną wersją programu SQL. Zalecana jest migracja typu swing, gdzie nowy serwer programu Azure AD Connect (z bazą danych w programie SQL) jest wdrażany jako serwer przejściowy obok istniejącego serwera programu Azure AD Connect (z programem LocalDB). 
* Instrukcje dotyczące konfigurowania zdalnego programu SQL pod kątem programu Azure AD Connect znajdują się w artykule [Niestandardowa instalacja programu Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-custom).
* Instrukcje dotyczące migracji typu swing w ramach uaktualniania programu Azure AD Connect znajdują się w artykule [Azure AD Connect: Uaktualnianie z poprzedniej wersji do najnowszej](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-upgrade-previous-version#swing-migration).

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej na temat [integrowania tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md).
