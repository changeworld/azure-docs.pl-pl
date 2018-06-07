---
title: 'Azure AD Connect: Odzyskanie bazy danych LocalDB 10 GB limit problem jak | Dokumentacja firmy Microsoft'
description: W tym temacie opisano sposób odzyskiwania usługi Azure AD Connect usługi synchronizacji po napotkaniu LocalDB 10GB ograniczyć problem.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: 41d081af-ed89-4e17-be34-14f7e80ae358
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 3ba491902444c9c05f997f854353206d78f2957d
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34591276"
---
# <a name="azure-ad-connect-how-to-recover-from-localdb-10-gb-limit"></a>Azure AD Connect: Jak odzyskać limitu 10 GB LocalDB
Program Azure AD Connect wymaga bazy danych programu SQL Server do przechowywania danych tożsamości. Możesz korzystać z domyślnego programu SQL Server 2012 Express LocalDB zainstalowanego z programem Azure AD Connect lub użyć własnego pełnego programu SQL. Program SQL Server Express narzuca limit rozmiaru w wysokości 10 GB. Jeśli jest używany program LocalDB i limit zostanie osiągnięty, usługa synchronizacji programu Azure AD Connect nie będzie mogła uruchomić się ani prawidłowo wykonywać synchronizacji. Ten artykuł zawiera kroki odzyskiwania.

## <a name="symptoms"></a>Objawy
Istnieją dwa wspólne symptomy:

* Azure AD Connect usługi synchronizacji **działa** , ale nie powiedzie się synchronizować z *"zatrzymana-bazy danych dysk pełny"* błędu.

* Azure AD Connect usługi synchronizacji **nie można uruchomić**. Podczas próby uruchomienie usługi pojawia się błąd zdarzeń 6323 i komunikat o błędzie *"Serwer napotkał błąd, ponieważ serwer SQL jest mało miejsca na dysku."*

## <a name="short-term-recovery-steps"></a>Procedura odzyskiwania krótkoterminowego
Ta sekcja zawiera kroki, aby odzyskać miejsce DB wymagane do usługa Azure AD Connect synchronizacji można wznowić operacji. Kroki obejmują:
1. [Sprawdź stan usługi synchronizacji](#determine-the-synchronization-service-status)
2. [Zmniejszyć rozmiar bazy danych](#shrink-the-database)
3. [Usuwanie danych w historii](#delete-run-history-data)
4. [Skrócić okres przechowywania danych historii wykonywania](#shorten-retention-period-for-run-history-data)

### <a name="determine-the-synchronization-service-status"></a>Sprawdź stan usługi synchronizacji
Po pierwsze należy ustalić, czy usługi synchronizacji jest nadal uruchomiona lub nie:

1. Zaloguj się do serwera usługi Azure AD Connect jako administrator.

2. Przejdź do **Menedżera sterowania usługami**.

3. Sprawdź stan **Microsoft Azure AD Sync**.


4. Jeśli została uruchomiona, nie zatrzymać lub uruchomić ponownie usługę. Pomiń [zmniejszyć rozmiar bazy danych](#shrink-the-database) krok i przejdź do [Delete Uruchom dane historii](#delete-run-history-data) kroku.

5. Jeśli nie jest uruchomiona, spróbuj uruchomić usługę. Jeśli usługa zostanie uruchomiony pomyślnie, Pomiń [zmniejszyć rozmiar bazy danych](#shrink-the-database) krok i przejdź do [Delete Uruchom dane historii](#delete-run-history-data) kroku. W przeciwnym razie kontynuuj [zmniejszyć rozmiar bazy danych](#shrink-the-database) kroku.

### <a name="shrink-the-database"></a>Zmniejszyć rozmiar bazy danych
Za pomocą operacji zmniejszania rozmiaru w celu zwolnienia miejsca bazy danych można uruchomić usługi synchronizacji. Zwalnia jego miejsce DB przez usunięcie odstępy w bazie danych. Ten krok jest optymalnych, ponieważ nie gwarantuje, że zawsze przeprowadza odzyskiwanie miejsca. Aby dowiedzieć się więcej na temat operacji zmniejszania, przeczytaj ten artykuł [baza danych](https://msdn.microsoft.com/library/ms189035.aspx).

> [!IMPORTANT]
> Pomiń ten krok, jeśli zostanie wyświetlony do uruchamiania usługi synchronizacji. Nie zaleca się zmniejszanie bazy danych SQL, ponieważ może to prowadzić do niskiej wydajności z powodu fragmentacji zwiększona.

Nazwa bazy danych, utworzonych dla usługi Azure AD Connect jest **ADSync**. Aby wykonać operację zmniejszania, należy zalogować się jako administratora systemu lub właściciela bazy danych. Podczas instalacji usługi Azure AD Connect następujące konta mają prawa administratora systemu:
* Administratorzy lokalni
* Konto użytkownika, którego użyto do uruchomienia instalacji Azure AD Connect.
* Konto usługi synchronizacji, które jest używane jako kontekst operacyjny usługi Azure AD Connect synchronizacji.
* Lokalne grupy ADSyncAdmins, który został utworzony podczas instalacji.

1. Utwórz kopię zapasową bazy danych przez skopiowanie **ADSync.mdf** i **ADSync_log.ldf** pliki znajdujące się w obszarze `%ProgramFiles%\Microsoft Azure AD Sync\Data` do bezpiecznej lokalizacji.

2. Rozpocznij nową sesję programu PowerShell.

3. Przejdź do folderu `%ProgramFiles%\Microsoft SQL Server\110\Tools\Binn`.

4. Uruchom **sqlcmd** narzędzia, uruchamiając polecenie `./SQLCMD.EXE -S “(localdb)\.\ADSync” -U <Username> -P <Password>`, przy użyciu poświadczeń administratora systemu lub bazy danych DBO.

5. Aby zmniejszyć rozmiar bazy danych, w wierszu polecenia sqlcmd (1 >), wprowadź `DBCC Shrinkdatabase(ADSync,1);`, a następnie `GO` w następnym wierszu.

6. Jeśli operacja się powiodła, spróbuj uruchomić ponownie usługi synchronizacji. Jeśli można uruchomić usługi synchronizacji, przejdź do [Delete Uruchom dane historii](#delete-run-history-data) kroku. Jeśli nie, skontaktuj się z pomocą techniczną.

### <a name="delete-run-history-data"></a>Usuwanie danych w historii
Domyślnie program Azure AD Connect przechowuje siedem dni, przez które Historia uruchomień danych. W tym kroku usunąć dane Historia uruchomień, aby odzyskać miejsce bazy danych, dzięki czemu usługi Azure AD Connect usługi synchronizacji można uruchomić ponownie przeprowadzić synchronizację.

1.  Uruchom **Menedżera usługi synchronizacji** , przechodząc do rozpoczęcia → usługi synchronizacji.

2.  Przejdź do **operacji** kartę.

3.  W obszarze **akcje**, wybierz pozycję **uruchamia wyczyść**...

4.  Możesz wybrać **wyczyść wszystkie elementy** lub **wyczyść jest uruchamiany przed... <date>**  opcji. Zaleca się uruchamiania czyszcząc Uruchom historyczne dane, które są starsze niż dwa dni. Jeśli będziesz kontynuować funkcjonowaniem problem rozmiar bazy danych, należy wybrać **wyczyść wszystkie elementy** opcji.

### <a name="shorten-retention-period-for-run-history-data"></a>Skrócić okres przechowywania danych historii wykonywania
Ten krok jest aby zmniejszyć prawdopodobieństwo powodowania problem limitu 10 GB po wielu cykli synchronizacji.

1. Otwórz nową sesję programu PowerShell.

2. Uruchom `Get-ADSyncScheduler` i zwróć właściwość PurgeRunHistoryInterval, która określa bieżącego okresu przechowywania.

3. Uruchom `Set-ADSyncScheduler -PurgeRunHistoryInterval 2.00:00:00` można ustawić okresu przechowywania do dwóch dni. Dostosuj okres przechowywania, zależnie od potrzeb.

## <a name="long-term-solution--migrate-to-full-sql"></a>Długoterminowe rozwiązanie — migracja do pełnego SQL
Ogólnie rzecz biorąc problem jest wskaźnikiem, że rozmiar 10 GB bazy danych nie jest już wystarczające dla usługi Azure AD Connect do synchronizowania z lokalnej usługi Active Directory do usługi Azure AD. Zalecane jest, aby przełączyć się do pełnej wersji programu SQL server. Nie można bezpośrednio zastąpić programu LocalDB istniejącego wdrożenia programu Azure AD Connect za pomocą pełnej wersji programu SQL. Zamiast tego należy wdrożyć nowy serwer programu Azure AD Connect z pełną wersją programu SQL. Zalecana jest migracja typu swing, gdzie nowy serwer programu Azure AD Connect (z bazą danych w programie SQL) jest wdrażany jako serwer przejściowy obok istniejącego serwera programu Azure AD Connect (z programem LocalDB). 
* Instrukcje dotyczące konfigurowania zdalnego programu SQL pod kątem programu Azure AD Connect znajdują się w artykule [Niestandardowa instalacja programu Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-custom).
* Instrukcje dotyczące migracji typu swing w ramach uaktualniania programu Azure AD Connect znajdują się w artykule [Azure AD Connect: Upgrade from a previous version to the latest](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-upgrade-previous-version#swing-migration) (Azure AD Connect: Uaktualnianie z poprzedniej wersji do najnowszej).

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej na temat [integrowania tożsamości lokalnych z usługą Azure Active Directory](active-directory-aadconnect.md).
