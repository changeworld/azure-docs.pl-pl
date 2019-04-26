---
title: Zainstaluj program Azure AD Connect przy użyciu istniejącej bazy danych ADSync | Dokumentacja firmy Microsoft
description: W tym temacie opisano sposób użycia istniejącej bazy danych ADSync.
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
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60245125"
---
# <a name="install-azure-ad-connect-using-an-existing-adsync-database"></a>Zainstaluj program Azure AD Connect przy użyciu istniejącej bazy danych ADSync
Program Azure AD Connect wymaga bazy danych programu SQL Server do przechowywania danych. Możesz korzystać z domyślnego programu SQL Server 2012 Express LocalDB zainstalowane za pomocą usługi Azure AD Connect lub użyć pełnej wersji programu SQL Server. Wcześniej Jeśli zainstalowano program Azure AD Connect, nową bazę danych o nazwie ADSync zawsze został utworzony. Za pomocą usługi Azure AD Connect w wersji 1.1.613.0 (lub po) masz możliwość zainstalowania Azure AD Connect, wskazując je do istniejącej bazy danych ADSync.

## <a name="benefits-of-using-an-existing-adsync-database"></a>Korzyści z używania istniejącej bazy danych ADSync
Wskazując istniejącej bazy danych ADSync:

- Z wyjątkiem informacje o poświadczeniach Konfiguracja synchronizacji w bazie danych ADSync (w tym reguły synchronizacji niestandardowych, łączniki, filtrowanie i Konfiguracja funkcji opcjonalnych) jest automatycznie odzyskane i używany podczas instalacji . Poświadczenia używane przez program Azure AD Connect, aby zsynchronizować zmiany z lokalnej usługi AD i Azure AD są szyfrowane, a może zostać oceniony jedynie przez poprzedni serwer usługi Azure AD Connect.
- Wszystkie dane tożsamości (skojarzone z łączników i metaverse) i pliki cookie synchronizacji, przechowywane w bazie danych ADSync również zostaną odzyskane. Nowo zainstalowanego programu Azure AD Connect serwera nadal synchronizowane z tam, gdzie adres serwera usługi Azure AD Connect przerwaliśmy, zamiast konieczności przeprowadzić pełną synchronizację.

## <a name="scenarios-where-using-an-existing-adsync-database-is-beneficial"></a>Scenariusze, w których jest korzystne przy użyciu istniejącej bazy danych ADSync
Korzyści te są przydatne w następujących scenariuszach:


- Masz istniejące wdrożenie usługi Azure AD Connect. Istniejącego serwera Azure AD Connect jest już nie działa, ale nadal działa programu SQL server zawierającego bazy danych ADSync. Można zainstalować serwer usługi Azure AD Connect i wskaż istniejącej bazy danych ADSync. 
- Masz istniejące wdrożenie usługi Azure AD Connect. Program SQL server zawierającego bazy danych ADSync przestanie działać. Jednak masz ostatnie Utwórz kopię zapasową bazy danych. Najpierw możesz przywrócić bazy danych ADSync na nowy serwer SQL. Po upływie którego można zainstalować serwer usługi Azure AD Connect i wskaż przywróconej bazy danych ADSync.
- Masz istniejące wdrożenie usługi Azure AD Connect, który jest używany program LocalDB. Z powodu limitu 10 GB, nałożonych przez LocalDB należy przeprowadzić migrację do pełnego rozwiązania SQL. Można utworzyć kopię zapasową bazy danych ADSync z lokalnej bazy danych i przywróć ją z programem SQL server. Po upływie którego można ponownie zainstalować serwer usługi Azure AD Connect i wskaż przywróconej bazy danych ADSync.
- Próbujesz skonfigurować serwer przejściowy i chce mieć pewność, że jego konfiguracja jest zgodny z typem bieżącego aktywnego serwera. Można utworzyć kopię zapasową bazy danych ADSync i przywróć ją na inny program SQL server. Po upływie którego można ponownie zainstalować serwer usługi Azure AD Connect i wskaż przywróconej bazy danych ADSync.

## <a name="prerequisite-information"></a>Informacje o wymaganiach wstępnych

Ważne uwagi do wykonania należy pamiętać o przed kontynuowaniem:

- Upewnij się przejrzeć wymagania wstępne dotyczące instalowania usługi Azure AD Connect na sprzęt i wymagania wstępne oraz konta i uprawnienia wymagane do zainstalowania programu Azure AD Connect. Uprawnienia wymagane do zainstalowania programu Azure AD Connect przy użyciu trybu "przy użyciu istniejącej bazy danych" jest taka sama jak "niestandardowy" instalacji.
- Wdrażanie usługi Azure AD Connect przed ADSync istniejące bazy danych jest obsługiwana tylko przy użyciu pełnego programu SQL. Nie jest obsługiwana przy użyciu programu SQL Express LocalDB. Mając LocalDB, który chcesz użyć istniejącej bazy danych ADSync, możesz utworzyć kopię zapasową bazy danych ADSync (LocalDB) i przywróć ją do pełnego rozwiązania SQL. Po upływie którego możesz wdrożyć program Azure AD Connect w przywróconej bazie danych przy użyciu tej metody.
- Wersja programu Azure AD Connect, używane do instalacji muszą spełniać następujące kryteria:
    - 1.1.613.0 lub nowszy, i
    - Tym samym lub wyższa niż wersja programu Azure AD Connect, ostatnio używane z bazy danych ADSync. Jeśli używane do instalacji wersji Azure AD Connect jest wyższa niż wersja, ostatnio używane z bazy danych ADSync, Pełna synchronizacja może być wymagane.  Pełna synchronizacja jest wymagana w przypadku zmiany reguł schematem lub synchronizacji między dwoma wersjami. 
- Bazy danych ADSync używanej powinna zawierać stan synchronizacji, która jest stosunkowo nowych. Ostatnie działanie synchronizacji przy użyciu istniejącej bazy danych ADSync powinna być w ciągu ostatnich trzech tygodni.
- Podczas instalowania programu Azure AD Connect przy użyciu "przy użyciu istniejącej bazy danych" metody należy skonfigurowane na serwerze usługi Azure AD Connect poprzedniej metody logowania nie są zachowywane. Ponadto nie można skonfigurować metodę logowania podczas instalacji. Metoda logowania można skonfigurować tylko po zakończeniu instalacji.
- Nie może mieć wiele serwerów usługi Azure AD Connect, korzystać z tej samej bazy danych ADSync. "Użyj istniejącej bazy danych" metody umożliwia ponowne użycie istniejącej bazy danych ADSync nowy serwer usługi Azure AD Connect. Nie obsługuje udostępniania.

## <a name="steps-to-install-azure-ad-connect-with-use-existing-database-mode"></a>Kroki, aby zainstalować program Azure AD Connect z trybem "przy użyciu istniejącej bazy danych"
1.  Pobierz Instalator programu Azure AD Connect (AzureADConnect.MSI) do serwera Windows. Kliknij dwukrotnie, Instalator programu Azure AD Connect w taki sposób, aby rozpocząć instalowanie program Azure AD Connect.
2.  Po zakończeniu instalacji pliku MSI kreator programu Azure AD Connect rozpocznie instalację w trybie ekspresowym. Zamknij ekran, klikając ikonę zakończenia.
![Powitanie](./media/how-to-connect-install-existing-database/db1.png)
3.  Uruchom nowy wiersz polecenia lub sesję programu PowerShell. Przejdź do folderu "C:\Program Files\Microsoft Azure Active Directory Connect". Uruchom polecenie .\AzureADConnect.exe /useexistingdatabase w celu uruchomienia kreatora programu Azure AD Connect w trybie instalacji „Użyj istniejącej bazy danych”.

> [!NOTE]
> Użyj przełącznika **/useexistingdatabase** tylko kiedy baza danych już zawiera dane z poprzedniej instalacji program Azure AD Connect. Na przykład gdy przenosisz z lokalnej bazy danych do pełnego bazy danych programu SQL Server lub kiedy został odbudowany serwer programu Azure AD Connect i przywrócić kopię zapasową SQL bazy danych ADSync z poprzedniej instalacji programu Azure AD Connect. Jeśli baza danych jest pusty, oznacza to, go nie zawiera żadnych danych z poprzedniej instalacji program Azure AD Connect, Pomiń ten krok.

![PowerShell](./media/how-to-connect-install-existing-database/db2.png)
1. Zobaczysz ekran powitalny programu Azure AD Connect. Gdy zaakceptujesz postanowienia licencyjne i uwagi na temat ochrony prywatności, kliknij pozycję **Kontynuuj**.
   ![Powitanie](./media/how-to-connect-install-existing-database/db3.png)
1. Na ekranie **Instalowanie wymaganych składników** włączona jest opcja **Użyj istniejącego serwera SQL Server**. Określ nazwę serwera SQL Server hostującego bazę danych programu ADSync. Jeśli wystąpienie aparatu SQL używane do hostowania bazy danych programu ADSync nie jest domyślnym wystąpieniem serwera SQL Server, musisz określić nazwę wystąpienia aparatu SQL. Ponadto jeśli nie jest włączone przeglądanie SQL, musisz też określić numer portu wystąpienia aparatu SQL. Na przykład:         
   ![Powitanie](./media/how-to-connect-install-existing-database/db4.png)           

1. Na ekranie **Łączenie z usługą Azure AD** musisz podać poświadczenia administratora globalnego katalogu usługi Azure AD. Zaleca się użycie konta w domyślnej domenie onmicrosoft.com. To konto służy tylko do tworzenia konta usługi w usłudze Azure AD i nie jest używane po zakończeniu pracy kreatora.
   ![Połączenie](./media/how-to-connect-install-existing-database/db5.png)
 
1. Na ekranie **Podłączanie katalogów** istniejący las usługi AD skonfigurowany na potrzeby synchronizacji katalogów jest wyświetlany z ikoną czerwonego krzyżyka. Aby zsynchronizować zmiany z lokalnego lasu usługi AD, wymagane jest konto usługi AD DS. Kreator programu Azure AD Connect nie może uzyskać poświadczeń konta usługi AD DS przechowywanych w bazie danych programu ADSync, ponieważ poświadczenia są zaszyfrowane i mogą zostać odszyfrowane tylko przez poprzedni serwer programu Azure AD Connect. Kliknij pozycję **Zmień poświadczenia**, aby określić konto usługi AD DS dla lasu usługi AD.
   ![Katalogi](./media/how-to-connect-install-existing-database/db6.png)
 
 
1. W wyskakującym oknie dialogowym możesz albo (i) podać poświadczenia administratora przedsiębiorstwa i pozwolić programowi Azure AD Connect utworzyć konto usługi AD DS, albo (ii) samodzielnie utworzyć konto usługi AD DS i podać jego poświadczenia w programie Azure AD Connect. Po wybraniu opcji i podaniu niezbędnych poświadczeń kliknij przycisk **OK** w celu zamknięcia wyskakującego okna dialogowego.
   ![Powitanie](./media/how-to-connect-install-existing-database/db7.png)
 
 
1. Po podaniu poświadczeń ikona czerwonego krzyżyka jest zastępowana ikoną zielonego znacznika wyboru. Kliknij przycisk **Dalej**.
   ![Powitanie](./media/how-to-connect-install-existing-database/db8.png)
 
 
1. Na ekranie **Wszystko gotowe do skonfigurowania** kliknij pozycję **Zainstaluj**.
   ![Powitanie](./media/how-to-connect-install-existing-database/db9.png)
 
 
1. Po zakończeniu instalacji serwer programu Azure AD Connect jest automatycznie włączany dla trybu przejściowego. Przed wyłączeniem trybu przejściowego zaleca się przejrzenie konfiguracji serwera i oczekujących operacji eksportowania pod kątem nieoczekiwanych zmian. 

## <a name="post-installation-tasks"></a>Zadania poinstalacyjne
Po przywróceniu kopii zapasowej bazy danych utworzone przy użyciu wersji programu Azure AD Connect przed 1.2.65.0, serwer przejściowy będzie automatycznie wybierać metodę logowania z **nie należy konfigurować**. Podczas, gdy zostaną przywrócone z synchronizacji skrótów haseł i preferencje dotyczące funkcji zapisywania zwrotnego haseł, możesz później zmienić metody logowania, aby dopasować inne zasady dla serwera synchronizacji usługi active.  Nie można wykonać te kroki mogą uniemożliwić użytkownikom rejestrowanie w należy ten serwer stanie się aktywny.  

Użyj poniższej tabeli, aby sprawdzić dodatkowe czynności, które są wymagane.

|Cecha|Kroki|
|-----|-----|
|Synchronizacja skrótów haseł| Synchronizacja skrótów haseł i ustawienia funkcji zapisywania zwrotnego haseł zostaną w pełni przywrócone dla wersji programu Azure AD Connect począwszy od 1.2.65.0.  Jeśli przywracanie przy użyciu starszej wersji programu Azure AD Connect, należy przejrzeć ustawienia opcji synchronizacji dla tych funkcji, aby upewnić się, że pasują do serwera synchronizacji usługi active.  Nie inne czynności konfiguracyjne, powinno być konieczne.|
|Federacja z usługami AD FS|Uwierzytelnianie usługi Azure, będą w dalszym ciągu korzystać z zasad usług AD FS skonfigurowane dla serwera synchronizacji usługi active.  Program Azure AD Connect umożliwia zarządzanie farmie usług AD FS, opcjonalnie możesz zmienić metodę logowania do Federacji usług AD FS w ramach przygotowania do wstrzymania serwera staje się wystąpienia synchronizacji usługi active.   Włączenie opcji urządzeń na serwerze synchronizacji usługi active należy skonfigurować te opcje na tym serwerze, uruchamiając zadanie "Konfiguruj opcje urządzenia".|
|Uwierzytelnianie przekazywane i pulpitu logowania jednokrotnego|Zaktualizuj logowania w metodzie zgodnie z konfiguracją na serwerze synchronizacji usługi active.  Jeśli to nie jest zakończony przed podwyższania poziomu serwera do uwierzytelniania podstawowego, przekazywane wraz z bezproblemowego logowania jednokrotnego na zostanie wyłączone, a następnie dzierżawy może być zablokowane, jeśli nie masz synchronizacji skrótów haseł jako kopii zapasowej Zaloguj się w opcji. Należy również zauważyć, że po włączeniu uwierzytelnianie przekazywane w trybie przejściowym, zostanie zainstalowany nowy agent uwierzytelniania, zarejestrowany i będzie działał jako agent wysokiej dostępności, który będzie akceptować logowania dla żądania.|
|Federacja z serwerem PingFederate|Uwierzytelnianie usługi Azure, będą w dalszym ciągu korzystać z zasad serwera PingFederate skonfigurowane dla serwera synchronizacji usługi active.  Opcjonalnie możesz zmienić metodę logowania do serwera PingFederate w ramach przygotowania do wstrzymania serwera staje się wystąpienia synchronizacji usługi active.  Może zostać przesunięty w tym kroku, dopóki nie należy sfederować dodatkowe domeny z serwerem PingFederate.|

## <a name="next-steps"></a>Kolejne kroki

- Po zainstalowaniu programu Azure AD Connect możesz [zweryfikować instalację i przypisać licencje](how-to-connect-post-installation.md).
- Dowiedz się więcej na temat funkcji, które zostały włączone w ramach instalacji: [Zapobieganie przypadkowemu usuwaniu](how-to-connect-sync-feature-prevent-accidental-deletes.md) i [Azure AD Connect Health](how-to-connect-health-sync.md).
- Dowiedz się więcej na te popularne tematy: [harmonogram i sposób włączania synchronizacji](how-to-connect-sync-feature-scheduler.md).
- Dowiedz się więcej na temat [integrowania tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md).
