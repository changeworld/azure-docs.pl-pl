---
title: Zainstaluj usługi Azure AD Connect przy użyciu istniejącej bazy danych ADSync | Dokumentacja firmy Microsoft
description: W tym temacie opisano sposób użycia istniejącej bazy danych ADSync.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.reviewer: cychua
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/30/2017
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: b28d4cc6b10b0fd47a67cfefa7cf1022921a5d96
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34591773"
---
# <a name="install-azure-ad-connect-using-an-existing-adsync-database"></a>Zainstalować program Azure AD Connect przy użyciu istniejącej bazy danych ADSync
Azure AD Connect wymaga bazy danych programu SQL Server do przechowywania danych. Można użyć domyślnego programu SQL Server 2012 Express LocalDB zainstalowane z programem Azure AD Connect lub używać pełnej wersji programu SQL Server. Wcześniej po zainstalowaniu usługi Azure AD Connect, nową bazę danych o nazwie ADSync zawsze został utworzony. Z programem Azure AD Connect wersji 1.1.613.0 (lub po) masz możliwość zainstalowania Azure AD Connect, wskazując go istniejąca baza danych ADSync.

## <a name="benefits-of-using-an-existing-adsync-database"></a>Korzyści wynikające z korzystania z istniejącej bazy danych ADSync
Wskazując istniejąca baza danych ADSync:

- Oprócz informacji poświadczeń Konfiguracja synchronizacji w bazie danych ADSync (w tym reguły synchronizacji niestandardowych, łączniki, filtrowania i konfiguracji funkcji opcjonalnych) jest automatycznie odzyskać i używane podczas instalacji . Poświadczenia używane przez program Azure AD Connect do synchronizowania zmian z lokalnymi AD i Azure AD są szyfrowane i można uzyskać tylko przez poprzedni serwer Azure AD Connect.
- Wszystkie dane tożsamości (skojarzone z obszary łączników i metaverse) i pliki cookie synchronizacji w bazie danych ADSync również są odzyskiwane. Nowo zainstalowany Azure AD Connect nadal synchronizowane z którym poprzedni serwer Azure AD Connect przerwał pracę, zamiast należy przeprowadzić pełną synchronizację serwera.

## <a name="scenarios-where-using-an-existing-adsync-database-is-beneficial"></a>Scenariusze, w których jest korzystne przy użyciu istniejącej bazy danych ADSync
Korzyści te są przydatne w następujących scenariuszach:


- Masz istniejące wdrożenie usługi Azure AD Connect. Już nie działa z istniejącego serwera Azure AD Connect, ale nadal działa programu SQL server zawierającego bazę danych ADSync. Można zainstalować nowy serwer Azure AD Connect, a następnie wskaż istniejąca baza danych ADSync. 
- Masz istniejące wdrożenie usługi Azure AD Connect. Program SQL server zawierającego bazę danych ADSync nie działa. Jednak masz ostatnią kopię zapasową bazy danych. Można przywrócić bazy danych ADSync najpierw na nowy serwer SQL. Po upływie którego można zainstalować nowy serwer Azure AD Connect i wskaż przywrócona baza danych ADSync.
- Masz istniejące wdrożenie usługi Azure AD Connect, korzystających z bazy danych LocalDB. Z powodu limitu 10 GB narzuconego przez LocalDB należy przeprowadzić migrację do pełnej bazy danych SQL. Można utworzyć kopię zapasową bazy danych ADSync z LocalDB i przywróć ją z programem SQL server. Po upływie którego można ponownie zainstalować nowy serwer Azure AD Connect i wskaż przywrócona baza danych ADSync.
- Próbujesz skonfigurować serwer przemieszczania i chce upewnij się, że jego konfiguracji zgodny z bieżącym aktywnego serwera. Można utworzyć kopię zapasową bazy danych programu ADSync i przywrócić go do innego serwera SQL. Po upływie którego można ponownie zainstalować nowy serwer Azure AD Connect i wskaż przywrócona baza danych ADSync.

## <a name="prerequisite-information"></a>Informacje o wymaganiach wstępnych

Ważne uwagi do wykonania należy pamiętać o przed kontynuowaniem:

- Upewnij się sprawdzić wymagania wstępne dotyczące instalowania usługi Azure AD Connect na sprzęt i wymagania wstępne oraz konta i uprawnienia wymagane do zainstalowania programu Azure AD Connect. Uprawnienia wymagane do zainstalowania programu Azure AD Connect przy użyciu trybu "Użyj istniejącej bazy danych" jest taka sama jak instalacja "niestandardowa".
- Wdrażanie usługi Azure AD Connect przed ADSync istniejącej bazy danych jest obsługiwana tylko pełna SQL. Nie jest obsługiwana z SQL Express LocalDB. Jeśli masz istniejącą bazę danych ADSync w LocalDB, który ma zostać użyty, najpierw musisz kopii zapasowej bazy danych ADSync (LocalDB) i przywrócić go do pełnego SQL. Po upływie którego można wdrożyć Azure AD Connect w przywróconej bazie danych przy użyciu tej metody.
- Wersja programu Azure AD Connect używane do instalacji muszą spełniać następujące kryteria:
    - 1.1.613.0 lub nowszej, AND
    - Takie same lub wyższe niż wersja programu Azure AD Connect ostatnio używane z bazą danych ADSync. Jeśli używane do instalacji wersji Azure AD Connect jest wyższa niż wersja ostatnio używane z bazą danych programu ADSync, Pełna synchronizacja może być wymagany.  Pełna synchronizacja jest wymagana, gdy istnieją zmiany reguły schematu lub synchronizacji między dwoma wersjami. 
- Baza danych ADSync używane powinien zawierać stan synchronizacji, która jest względnie nowe. Ostatnie działanie synchronizacji z istniejącej bazy danych ADSync powinna być w ciągu ostatnich trzech tygodni.
- Po zainstalowaniu programu Azure AD Connect przy użyciu metody "Użyj istniejącej bazy danych", skonfigurowane na serwerze usługi Azure AD Connect poprzedniej metody logowania nie są zachowywane. Ponadto metoda logowania nie można skonfigurować podczas instalacji. Metoda logowania można skonfigurować tylko po zakończeniu instalacji.
- Nie może mieć wielu serwerów usługi Azure AD Connect, korzystać z tej samej bazy danych ADSync. Metoda "Użyj istniejącej bazy danych" służy do ponownego użycia istniejącej bazy danych ADSync z nowego serwera Azure AD Connect. Nie obsługuje udostępniania.

## <a name="steps-to-install-azure-ad-connect-with-use-existing-database-mode"></a>Kroki, aby zainstalować usługi Azure AD Connect z trybem "Użyj istniejącej bazy danych"
1.  Pobierz Instalator Azure AD Connect (AzureADConnect.MSI) w systemie Windows Server. Kliknij dwukrotnie Instalator Azure AD Connect, aby rozpocząć instalowanie Azure AD Connect.
2.  Po zakończeniu instalacji MSI Kreatora programu Azure AD Connect rozpoczyna się od instalacji w trybie Express. Zamknąć ten ekran, klikając ikonę zakończenia.
![Witamy](media/active-directory-aadconnect-existing-database/db1.png)
3.  Uruchom nowy wiersz polecenia lub w sesji programu PowerShell. Przejdź do folderu <drive>\program files\Microsoft Azure AD Connect. Uruchom polecenie.\AzureADConnect.exe /useexistingdatabase, aby uruchomić Kreatora programu Azure AD Connect w trybie "Użyj istniejącej bazy danych".
![Program PowerShell](media/active-directory-aadconnect-existing-database/db2.png)
4.  Możesz są greeted o wprowadzenie do programu Azure AD Connect ekranu. Po akceptujesz postanowienia licencyjne i zasady zachowania poufności informacji, kliknij przycisk **Kontynuuj**.
![Witamy](media/active-directory-aadconnect-existing-database/db3.png)
5.  Na **zainstalowanie wymaganych składników** ekranu, **korzystało z istniejącego serwera SQL** opcja jest włączona. Określ nazwę programu SQL server, który jest hostem bazy danych ADSync. Jeśli wystąpienie aparatu SQL używana do hostowania bazy danych ADSync nie jest domyślne wystąpienie programu SQL Server, należy określić nazwę wystąpienia aparatu SQL. Ponadto jeśli SQL nie włączono funkcji przeglądania, należy również określić numer portu wystąpienia aparatu SQL. Na przykład:         
![Witamy](media/active-directory-aadconnect-existing-database/db4.png)           

6.  Na **nawiązywanie połączenia z usługą Azure AD** ekranu, należy podać poświadczenia administratora globalnego katalogu Azure AD. Zaleca się użycie konta w domyślnej domeny onmicrosoft.com. To konto służy tylko do tworzenia konta usługi w usłudze Azure AD i nie jest używane po zakończeniu pracy kreatora.
![Połączenie](media/active-directory-aadconnect-existing-database/db5.png)
 
7.  Na **Podłączanie katalogów** ekranu skonfigurowany na potrzeby synchronizacji katalogów jest oznaczone ikoną czerwony krzyżyk obok siebie z istniejącego lasu usługi AD. Aby zsynchronizować zmiany z lokalnego lasu usługi AD, konta usług AD DS jest wymagana. Kreator Azure AD Connect nie może pobrać poświadczenia konta usług AD DS, przechowywane w bazie danych ADSync, ponieważ poświadczenia są szyfrowane i mogą być odszyfrowane tylko przez poprzedni serwer Azure AD Connect. Kliknij przycisk **zmiany poświadczeń** umożliwia określenie konta usług AD DS dla lasu usługi AD.
![Katalogi](media/active-directory-aadconnect-existing-database/db6.png)
 
 
8.  W wyskakującym oknie dialogowym możesz albo (i), podaj poświadczenia administratora przedsiębiorstwa i pozwól Azure AD Connect tworzenia konta usług AD DS lub (ii) samodzielnie utworzyć konto usługi AD DS i podać jego poświadczenia do usługi Azure AD Connect. Gdy wybrana jest opcja i podaj niezbędne poświadczenia, kliknij przycisk **OK** aby zamknąć okno podręczne.
![Witamy](media/active-directory-aadconnect-existing-database/db7.png)
 
 
9.  Gdy podano poświadczeń, ikona czerwony krzyżyk jest zastępowany ikona zielony znacznik. Kliknij przycisk **Dalej**.
![Witamy](media/active-directory-aadconnect-existing-database/db8.png)
 
 
10. Na **wszystko gotowe do skonfigurowania** kliknij **zainstalować**.
![Witamy](media/active-directory-aadconnect-existing-database/db9.png)
 
 
11. Po zakończeniu instalacji serwera usługi Azure AD Connect jest automatycznie włączona dla trybu przejściowego. Zalecane jest, aby przejrzeć konfigurację serwera i oczekujące operacje eksportu dla nieoczekiwane zmiany przed wyłączeniem trybu przejściowego. 

## <a name="next-steps"></a>Kolejne kroki

- Po zainstalowaniu programu Azure AD Connect możesz [zweryfikować instalację i przypisać licencje](active-directory-aadconnect-whats-next.md).
- Dowiedz się więcej na temat funkcji włączonych w ramach instalacji: [Zapobieganie przypadkowemu usuwaniu](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md) oraz [Azure AD Connect Health](../connect-health/active-directory-aadconnect-health-sync.md).
- Dowiedz się więcej na te popularne tematy: [harmonogram i sposób włączania synchronizacji](active-directory-aadconnectsync-feature-scheduler.md).
- Dowiedz się więcej na temat [integrowania tożsamości lokalnych z usługą Azure Active Directory](active-directory-aadconnect.md).
