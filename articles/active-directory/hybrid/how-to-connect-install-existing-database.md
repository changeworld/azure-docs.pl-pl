---
title: Instalowanie usługi Azure AD Connect przy użyciu istniejącej bazy danych usługi ADSync | Dokumenty firmy Microsoft
description: W tym temacie opisano sposób korzystania z istniejącej bazy danych usługi ADSync.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.reviewer: cychua
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/30/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4dc6993586063c9c99a287c51d799b44f921768d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60245125"
---
# <a name="install-azure-ad-connect-using-an-existing-adsync-database"></a>Instalowanie programu Azure AD Connect przy użyciu istniejącej bazy danych programu ADSync
Usługa Azure AD Connect wymaga bazy danych programu SQL Server do przechowywania danych. Można użyć domyślnej bazy danych lokalnej programu SQL Server 2012 Express LocalDB zainstalowanej w usłudze Azure AD Connect lub użyć pełnej wersji programu SQL. Wcześniej po zainstalowaniu usługi Azure AD Connect zawsze tworzono nową bazę danych o nazwie ADSync. W usłudze Azure AD Connect w wersji 1.1.613.0 (lub później) można zainstalować usługę Azure AD Connect, wskazując ją na istniejącą bazę danych usługi ADSync.

## <a name="benefits-of-using-an-existing-adsync-database"></a>Korzyści z używania istniejącej bazy danych ADSync
Wskazując istniejącą bazę danych ADSync:

- Z wyjątkiem informacji o poświadczeniach konfiguracja synchronizacji przechowywana w bazie danych ADSync (w tym niestandardowe reguły synchronizacji, łączniki, filtrowanie i konfiguracja funkcji opcjonalnych) jest automatycznie odzyskiwana i używana podczas instalacji . Poświadczenia używane przez usługę Azure AD Connect do synchronizowania zmian z lokalnymi usługami AD i usługi Azure AD są szyfrowane i są dostępne tylko dla poprzedniego serwera usługi Azure AD Connect.
- Wszystkie dane tożsamości (skojarzone ze spaniami łączników i metaverse) i pliki cookie synchronizacji przechowywane w bazie danych ADSync są również odzyskiwane. Nowo zainstalowany serwer usługi Azure AD Connect może nadal synchronizować z miejsca, w którym poprzedni serwer usługi Azure AD Connect został wyłączony, zamiast konieczności wykonywania pełnej synchronizacji.

## <a name="scenarios-where-using-an-existing-adsync-database-is-beneficial"></a>Scenariusze, w których korzystanie z istniejącej bazy danych ADSync jest korzystne
Te korzyści są przydatne w następujących scenariuszach:


- Masz istniejące wdrożenie usługi Azure AD Connect. Istniejący serwer usługi Azure AD Connect nie działa, ale serwer SQL zawierający bazę danych ADSync nadal działa. Można zainstalować nowy serwer usługi Azure AD Connect i wskazać go do istniejącej bazy danych usługi ADSync. 
- Masz istniejące wdrożenie usługi Azure AD Connect. Serwer SQL zawierający bazę danych ADSync nie działa. Jednak masz ostatnio kopii zapasowej bazy danych. Najpierw można przywrócić bazę danych ADSync do nowego serwera SQL. Po tym, można zainstalować nowy serwer usługi Azure AD Connect i wskazać go do przywróconej bazy danych ADSync.
- Masz istniejące wdrożenie usługi Azure AD Connect, które używa localdb. Ze względu na limit 10 GB narzucony przez LocalDB, chcesz przeprowadzić migrację do pełnego sql. Można wywrzeć kopię zapasową bazy danych ADSync z LocalDB i przywrócić ją do serwera SQL. Po tym czasie można ponownie zainstalować nowy serwer usługi Azure AD Connect i wskazać go do przywróconej bazy danych USŁUGI ADSync.
- Próbujesz skonfigurować serwer przejściowy i chcesz upewnić się, że jego konfiguracja jest zgodna z konfiguracją bieżącego aktywnego serwera. Można wywrzeć kopię zapasową bazy danych ADSync i przywrócić ją na innym serwerze SQL. Po tym czasie można ponownie zainstalować nowy serwer usługi Azure AD Connect i wskazać go do przywróconej bazy danych USŁUGI ADSync.

## <a name="prerequisite-information"></a>Informacje wymagane wstępnie

Ważne uwagi, które należy wziąć pod uwagę przed kontynuowaniem:

- Zapoznaj się z wymaganiami wstępnymi do zainstalowania usługi Azure AD Connect przy sprzęcie sprzętowym i wymaganiami wstępnymi oraz konta i uprawnień wymaganych do zainstalowania usługi Azure AD Connect. Uprawnienia wymagane do zainstalowania usługi Azure AD Connect przy użyciu trybu "użyj istniejącej bazy danych" jest taka sama jak instalacja "niestandardowa".
- Wdrażanie usługi Azure AD Connect w istniejącej bazie danych usługi ADSync jest obsługiwane tylko przy pełnym sql. Nie jest obsługiwany w programie SQL Express LocalDB. Jeśli masz istniejącą bazę danych ADSync w LocalDB, który chcesz użyć, należy najpierw wykonać kopię zapasową bazy danych ADSync (LocalDB) i przywrócić ją do pełnego SQL. Po tym, można wdrożyć usługi Azure AD Connect względem przywróconej bazy danych przy użyciu tej metody.
- Wersja usługi Azure AD Connect używana do instalacji musi spełniać następujące kryteria:
    - 1.1.613.0 lub powyżej, AND
    - Taka sama lub wyższa niż wersja usługi Azure AD Connect ostatnio używana z bazą danych ADSync. Jeśli wersja usługi Azure AD Connect używana do instalacji jest wyższa niż wersja ostatnio używana z bazą danych ADSync, może być wymagana pełna synchronizacja.  Pełna synchronizacja jest wymagana, jeśli istnieją zmiany reguły schematu lub synchronizacji między dwiema wersjami. 
- Używana baza danych ADSync powinna zawierać stan synchronizacji, który jest stosunkowo niedawno. Ostatnie działanie synchronizacji z istniejącą bazą danych ADSync powinno być w ciągu ostatnich trzech tygodni.
- Podczas instalowania usługi Azure AD Connect przy użyciu metody "użyj istniejącej bazy danych" metoda logowania skonfigurowana na poprzednim serwerze usługi Azure AD Connect nie jest zachowywana. Ponadto nie można skonfigurować metody logowania podczas instalacji. Metodę logowania można skonfigurować tylko po zakończeniu instalacji.
- Nie można mieć wiele serwerów usługi Azure AD Connect współużytkować tę samą bazę danych usługi ADSync. Metoda "użyj istniejącej bazy danych" umożliwia ponowne użycie istniejącej bazy danych ADSync z nowym serwerem usługi Azure AD Connect. Nie obsługuje udostępniania.

## <a name="steps-to-install-azure-ad-connect-with-use-existing-database-mode"></a>Kroki, aby zainstalować usługę Azure AD Connect w trybie "użyj istniejącej bazy danych"
1.  Pobierz instalator usługi Azure AD Connect (AzureADConnect.MSI) na serwer windows. Kliknij dwukrotnie instalator usługi Azure AD Connect, aby rozpocząć instalowanie usługi Azure AD Connect.
2.  Po zakończeniu instalacji pliku MSI kreator programu Azure AD Connect rozpocznie instalację w trybie ekspresowym. Zamknij ekran, klikając ikonę zakończenia.
![Powitanie](./media/how-to-connect-install-existing-database/db1.png)
3.  Uruchom nowy wiersz polecenia lub sesję programu PowerShell. Przejdź do folderu "C:\Program Files\Microsoft Azure Active Directory Connect". Uruchom polecenie .\AzureADConnect.exe /useexistingdatabase w celu uruchomienia kreatora programu Azure AD Connect w trybie instalacji „Użyj istniejącej bazy danych”.

> [!NOTE]
> Użyj przełącznika **/UseExistingDatabase** tylko wtedy, gdy baza danych zawiera już dane z wcześniejszej instalacji usługi Azure AD Connect. Na przykład podczas przenoszenia z lokalnej bazy danych do pełnej bazy danych programu SQL Server lub gdy serwer usługi Azure AD Connect został przebudowany i przywrócono kopię zapasową SQL bazy danych USŁUGI ADSync z wcześniejszej instalacji usługi Azure AD Connect. Jeśli baza danych jest pusta, oznacza to, że nie zawiera żadnych danych z poprzedniej instalacji usługi Azure AD Connect, pomiń ten krok.

![PowerShell](./media/how-to-connect-install-existing-database/db2.png)
1. Zobaczysz ekran powitalny programu Azure AD Connect. Gdy zaakceptujesz postanowienia licencyjne i uwagi na temat ochrony prywatności, kliknij pozycję **Kontynuuj**.
   ![Powitanie](./media/how-to-connect-install-existing-database/db3.png)
1. Na ekranie **Instalowanie wymaganych składników** włączona jest opcja **Użyj istniejącego serwera SQL Server**. Określ nazwę serwera SQL Server hostującego bazę danych programu ADSync. Jeśli wystąpienie aparatu SQL używane do hostowania bazy danych programu ADSync nie jest domyślnym wystąpieniem serwera SQL Server, musisz określić nazwę wystąpienia aparatu SQL. Ponadto jeśli nie jest włączone przeglądanie SQL, musisz też określić numer portu wystąpienia aparatu SQL. Przykład:         
   ![Powitanie](./media/how-to-connect-install-existing-database/db4.png)           

1. Na ekranie **Łączenie z usługą Azure AD** musisz podać poświadczenia administratora globalnego katalogu usługi Azure AD. Zaleca się użycie konta w domyślnej domenie onmicrosoft.com. To konto służy tylko do tworzenia konta usługi w usłudze Azure AD i nie jest używane po zakończeniu pracy kreatora.
   ![Połączenie](./media/how-to-connect-install-existing-database/db5.png)
 
1. Na ekranie **Podłączanie katalogów** istniejący las usługi AD skonfigurowany na potrzeby synchronizacji katalogów jest wyświetlany z ikoną czerwonego krzyżyka. Aby zsynchronizować zmiany z lokalnego lasu usługi AD, wymagane jest konto usługi AD DS. Kreator programu Azure AD Connect nie może uzyskać poświadczeń konta usługi AD DS przechowywanych w bazie danych programu ADSync, ponieważ poświadczenia są zaszyfrowane i mogą zostać odszyfrowane tylko przez poprzedni serwer programu Azure AD Connect. Kliknij pozycję **Zmień poświadczenia**, aby określić konto usługi AD DS dla lasu usługi AD.
   ![Katalogi](./media/how-to-connect-install-existing-database/db6.png)
 
 
1. W wyskakującym oknie dialogowym możesz albo (i) podać poświadczenia administratora przedsiębiorstwa i pozwolić programowi Azure AD Connect utworzyć konto usługi AD DS, albo (ii) samodzielnie utworzyć konto usługi AD DS i podać jego poświadczenia w programie Azure AD Connect. Po wybraniu opcji i podaniu niezbędnych poświadczeń kliknij przycisk **OK** w celu zamknięcia wyskakującego okna dialogowego.
   ![Powitanie](./media/how-to-connect-install-existing-database/db7.png)
 
 
1. Po podaniu poświadczeń ikona czerwonego krzyżyka jest zastępowana ikoną zielonego znacznika wyboru. Kliknij przycisk **alej**.
   ![Powitanie](./media/how-to-connect-install-existing-database/db8.png)
 
 
1. Na ekranie **Gotowe do skonfigurowania** kliknij pozycję **Zainstaluj**.
   ![Powitanie](./media/how-to-connect-install-existing-database/db9.png)
 
 
1. Po zakończeniu instalacji serwer programu Azure AD Connect jest automatycznie włączany dla trybu przejściowego. Przed wyłączeniem trybu przejściowego zaleca się przejrzenie konfiguracji serwera i oczekujących operacji eksportowania pod kątem nieoczekiwanych zmian. 

## <a name="post-installation-tasks"></a>Zadania poinstalacyjne
Podczas przywracania kopii zapasowej bazy danych utworzonej przez wersję usługi Azure AD Connect przed wersją 1.2.65.0 serwer przemieszczania automatycznie wybierze metodę logowania **Do Not Configure**. Podczas gdy preferencje synchronizacji skrótu hasła i hasła zostaną przywrócone, należy następnie zmienić metodę logowania, aby dopasować inne zasady obowiązujące dla aktywnego serwera synchronizacji.  Niepowodzenie wykonania tych kroków może uniemożliwić użytkownikom zalogowanie się, jeśli ten serwer stanie się aktywny.  

Skorzystaj z poniższej tabeli, aby zweryfikować wszelkie dodatkowe kroki, które są wymagane.

|Funkcja|Kroki|
|-----|-----|
|Synchronizacja skrótu hasła| Ustawienia synchronizacji skrótu haseł i zapisywania haseł są w pełni przywracane dla wersji usługi Azure AD Connect, począwszy od wersji 1.2.65.0.  W przypadku przywracania przy użyciu starszej wersji usługi Azure AD Connect, przejrzyj ustawienia opcji synchronizacji dla tych funkcji, aby upewnić się, że są one zgodne z aktywnym serwerem synchronizacji.  Żadne inne kroki konfiguracji nie powinny być konieczne.|
|Federacja z usługami AD FS|Uwierzytelnianie platformy Azure będą nadal używać zasad usług AD FS skonfigurowanych dla aktywnego serwera synchronizacji.  Jeśli używasz usługi Azure AD Connect do zarządzania farmą usług AD FS, możesz opcjonalnie zmienić metodę logowania na federację usług AD FS w ramach przygotowań do tego, że serwer rezerwowy stanie się aktywnym wystąpieniem synchronizacji.   Jeśli opcje urządzenia są włączone na aktywnym serwerze synchronizacji, skonfiguruj te opcje na tym serwerze, uruchamiając zadanie "Konfigurowanie opcji urządzenia".|
|Uwierzytelnianie przekazywane i logowanie jednokrotne na pulpicie|Zaktualizuj metodę logowania, aby dopasować konfigurację na aktywnym serwerze synchronizacji.  Jeśli nie jest to przestrzegane przed promowaniem serwera do podstawowego, uwierzytelnianie przekazywane wraz z bezproblemowym logowaniem jednokrotnym zostanie wyłączone, a dzierżawa może zostać zablokowana, jeśli nie masz synchronizacji skrótu hasła jako opcji logowania zapasowego. Należy również zauważyć, że po włączeniu uwierzytelniania przekazywanego w trybie przejściowym nowy agent uwierzytelniania zostanie zainstalowany, zarejestrowany i będzie uruchamiany jako agent o wysokiej dostępności, który będzie akceptował żądania logowania.|
|Federacja z serwerem PingFederate|Uwierzytelnianie platformy Azure będą nadal używać zasad PingFederate skonfigurowanych dla aktywnego serwera synchronizacji.  Opcjonalnie można zmienić metodę logowania na PingFederate w ramach przygotowań do serwera rezerwowego staje się aktywne wystąpienie synchronizacji.  Ten krok może zostać odroczony, dopóki nie trzeba sfederować dodatkowe domeny za pomocą pingfederate.|

## <a name="next-steps"></a>Następne kroki

- Po zainstalowaniu programu Azure AD Connect możesz [zweryfikować instalację i przypisać licencje](how-to-connect-post-installation.md).
- Dowiedz się więcej na temat funkcji włączonych w ramach instalacji: [Zapobieganie przypadkowemu usuwaniu](how-to-connect-sync-feature-prevent-accidental-deletes.md) oraz [Azure AD Connect Health](how-to-connect-health-sync.md).
- Dowiedz się więcej na te popularne tematy: [harmonogram i sposób włączania synchronizacji](how-to-connect-sync-feature-scheduler.md).
- Dowiedz się więcej na temat [integrowania tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md).
