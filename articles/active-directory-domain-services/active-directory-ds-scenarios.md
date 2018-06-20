---
title: 'Usługi Azure Active Directory Domain Services: Scenariusze wdrażania | Dokumentacja firmy Microsoft'
description: Scenariusze wdrażania usług domenowych Azure AD
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: c5216ec9-4c4f-4b7e-830b-9d70cf176b20
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/21/2017
ms.author: maheshu
ms.openlocfilehash: be8d565ea16849302b743e5619afd3f21703bd1e
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36218692"
---
# <a name="deployment-scenarios-and-use-cases"></a>Scenariusze wdrażania i przypadki użycia
W tej sekcji przyjrzymy się kilka scenariuszy i przypadków użycia, które korzystają z usług domenowych w usłudze Azure Active Directory (AD).

## <a name="secure-easy-administration-of-azure-virtual-machines"></a>Bezpieczne i łatwe zarządzanie maszyn wirtualnych platformy Azure
Azure Active Directory Domain Services służy do zarządzania maszynach wirtualnych Azure w sposób prostsze. Maszyny wirtualne platformy Azure mogą należeć do domeny zarządzanej, dzięki czemu można używać do logowania się w swoje poświadczenia firmowe, usługi AD. To rozwiązanie pomaga uniknąć problemu zarządzania poświadczeń, takich jak obsługa kont administratora lokalnego na wszystkich maszynach wirtualnych platformy Azure.

Maszyny wirtualne serwera, które są przyłączone do domeny zarządzanej można również zarządzane i chronione przy użyciu zasad grupy. Można zastosować plany bazowe zabezpieczeń wymagane na maszynach wirtualnych platformy Azure i zablokowanie zgodnie z zasadami bezpieczeństwa firmowych. Na przykład można użyć funkcji zarządzania zasadami grupy do ograniczania typów aplikacji, które mogą zostać uruchomione na tych maszynach wirtualnych.

![Usprawnione zarządzanie maszyn wirtualnych platformy Azure](./media/active-directory-domain-services-scenarios/streamlined-vm-administration.png)

Serwery i pozostałą infrastrukturą osiągnie z eksploatacji, Contoso jest przenoszona wiele aplikacji, w obecnie hostowana na lokalnych do chmury. Bieżący standardowego IT ma, że serwery hostujące aplikacje firmowe musi być przyłączony do domeny i zarządzane przy użyciu zasad grupy. Firmy Contoso administratora IT chce maszyn wirtualnych sprzężenia domeny wdrożona na platformie Azure, aby ułatwić administracji. W związku z tym administratorzy i użytkownicy mogą Zaloguj się za pomocą poświadczeń firmowych. W tym samym czasie można skonfigurować maszyny do wykonania linie bazowe zabezpieczeń za pomocą zasad grupy. Contoso nie chce mieć wdrażania, monitorowania i zarządzania kontrolerami domeny w systemie Azure, aby zabezpieczyć maszyn wirtualnych platformy Azure. W związku z tym usługi domenowe Azure AD jest doskonałym rozwiązaniem dla przypadek użycia.

**Uwagi dotyczące wdrażania**

Należy wziąć pod uwagę następujące istotne kwestie dotyczące tego scenariusza wdrażania:

* Domen zarządzanych udostępniane przez usługi domenowe Azure AD Podaj pojedynczy płaska struktura OU (jednostki organizacyjnej), domyślnie. Wszystkie komputery przyłączone do domeny znajdują się w jednej jednostce Organizacyjnej płaskim. Można jednak utworzyć niestandardowe jednostek organizacyjnych.
* Usługi domenowe Azure AD obsługuje prosty zasad grupy w formie wbudowanego obiektu zasad grupy Każdy dla użytkowników i komputerów kontenerów. Można tworzyć niestandardowe obiekty zasad grupy i adresować je do niestandardowych jednostek organizacyjnych.
* Usługi domenowe Azure AD obsługuje schematu podstawowego obiektu komputera usługi AD. Nie można rozszerzyć schematu obiektu komputera.

## <a name="lift-and-shift-an-on-premises-application-that-uses-ldap-bind-authentication-to-azure-infrastructure-services"></a>Przyrostu i shift aplikacji lokalnej, która używa uwierzytelniania wiązania LDAP do usługi infrastruktury platformy Azure
![Powiązanie z protokołem LDAP](./media/active-directory-domain-services-scenarios/ldap-bind.png)

Firma Contoso ma aplikacji lokalnych, które zostało zakupione od niezależnego dostawcy oprogramowania wielu lat temu. Aplikacja jest obecnie w trybie konserwacji przez niezależnego dostawcy oprogramowania i żądania zmiany do aplikacji jest zbyt duży dla Contoso. Ta aplikacja ma frontonu sieci web, służąca do zbierania poświadczeń użytkownika za pomocą formularza sieci web i następnie uwierzytelnia użytkowników, wykonując wiązanie LDAP do firmowej usługi Active Directory. Contoso przeprowadzić migrację z tej aplikacji do usługi infrastruktury platformy Azure. Jest to pożądane, czy aplikacja działa tak jak, bez konieczności wprowadzania żadnych zmian. Ponadto użytkownicy powinni mieć do uwierzytelniania przy użyciu istniejących poświadczeń firmowych i bez konieczności retrain użytkownikom wykonywanie czynności inaczej. Innymi słowy użytkownicy końcowi powinny być oblivious, z którym jest uruchomiona aplikacja, i migracji powinny być przezroczyste do nich.

**Uwagi dotyczące wdrażania**

Należy wziąć pod uwagę następujące istotne kwestie dotyczące tego scenariusza wdrażania:

* Upewnij się, że aplikacja nie trzeba zmodyfikować i zapisu do katalogu. LDAP do zapisu w domenach zarządzanych udostępniane przez usługi domenowe Azure AD nie jest obsługiwane.
* Nie można zmienić hasła bezpośrednio przed domeny zarządzanej. Użytkownicy końcowi mogą zmieniać swoje hasła albo przy użyciu mechanizmu zmiany hasła samoobsługi usługi Azure AD lub względem katalogu lokalnego. Te zmiany są automatycznie synchronizowane i dostępne w domeny zarządzanej.

## <a name="lift-and-shift-an-on-premises-application-that-uses-ldap-read-to-access-the-directory-to-azure-infrastructure-services"></a>Przyrostu i shift aplikacji lokalnej, która używa protokołu LDAP do odczytu do uzyskiwania dostępu do katalogu do usługi infrastruktury platformy Azure
Firma Contoso ma lokalnego aplikacji — biznesowych (LOB), która został opracowany niemal dekadę temu. Ta aplikacja jest katalogiem aware i został zaprojektowany do pracy z systemem Windows Server AD. Aplikacja używa protokołu LDAP (Lightweight Directory Access Protocol), aby odczytać informacje/atrybuty o użytkownikach z usługi Active Directory. Aplikacja nie Modyfikuj atrybuty lub w przeciwnym razie zapis w katalogu. Contoso chce migrować tę aplikację do usługi infrastruktury platformy Azure i wycofywania urządzeń lokalnych przedawnienia aktualnie obsługujący tej aplikacji. Aplikacja nie ulegną używać katalogu nowoczesne interfejsy API, takich jak opartego na interfejsie REST usługi Azure AD interfejsu API programu Graph. W związku z tym opcja przyrostu shift jest pożądane, według którego aplikacji można poddać migracji do uruchamiania w chmurze, bez modyfikowania kodu lub ponowne zapisywanie aplikacji.

**Uwagi dotyczące wdrażania**

Należy wziąć pod uwagę następujące istotne kwestie dotyczące tego scenariusza wdrażania:

* Upewnij się, że aplikacja nie trzeba zmodyfikować i zapisu do katalogu. LDAP do zapisu w domenach zarządzanych udostępniane przez usługi domenowe Azure AD nie jest obsługiwane.
* Upewnij się, aplikacja nie wymaga niestandardowych/rozszerzony schemat usługi Active Directory. Rozszerzenia schematu nie są obsługiwane w usługach domenowych Azure AD.

## <a name="migrate-an-on-premises-service-or-daemon-application-to-azure-infrastructure-services"></a>Migracja lokalnych aplikacji usługi lub demon usługi infrastruktury platformy Azure
Niektóre aplikacje składają się z wielu warstw, gdy jeden z poziomów musi wykonać uwierzytelnionego połączenia z warstwą wewnętrznej bazy danych, takich jak warstwy bazy danych. Konta usługi Active Directory są często używane dla tych przypadków użycia. Można przyrostu shift aplikacjom usługi infrastruktury platformy Azure i używania usługi domenowe Azure AD na potrzeby tożsamości tych aplikacji. Można używać tego samego konta usługi, który jest synchronizowany z katalogu lokalnego do usługi Azure AD. Alternatywnie można najpierw utworzyć niestandardowy jednostkę Organizacyjną, a następnie utworzyć osobnego konta usługowego w tej jednostce Organizacyjnej, do wdrażania tych aplikacji.

![Konto usługi za pomocą WIA](./media/active-directory-domain-services-scenarios/wia-service-account.png)

Firma Contoso ma niestandardowej magazynu aplikacji frontonu sieci web, programu SQL server i serwera wewnętrznej bazy danych FTP. Uwierzytelnianie zintegrowane systemu Windows kont usługi służy do uwierzytelniania frontonu sieci web do serwera FTP. Fronton sieci web został skonfigurowany do uruchamiania jako konto usługi. Serwer wewnętrznej bazy danych jest skonfigurowany do autoryzowania dostępu z konta usługi dla frontonu sieci web. Contoso preferuje nie ma potrzeby wdrażania maszyny wirtualnej kontrolera domeny w chmurze, aby przenieść tę aplikację do usługi infrastruktury platformy Azure. Firmy Contoso IT administrator może wdrożyć serwerów obsługujących frontonu sieci web, programu SQL server i serwer FTP do maszyn wirtualnych platformy Azure. Te komputery są następnie przyłączone do domeny zarządzanej usług domenowych Azure AD. Następnie można używają tego samego konta usługi w ich katalogu lokalnego na potrzeby uwierzytelniania aplikacji. To konto usługi jest synchronizowane do domeny zarządzanej usług domenowych Azure AD i jest dostępny do użytku.

**Uwagi dotyczące wdrażania**

Należy wziąć pod uwagę następujące istotne kwestie dotyczące tego scenariusza wdrażania:

* Upewnij się, że aplikacja używa nazwy użytkownika i hasła do uwierzytelnienia. Uwierzytelnianie certyfikatu/karty inteligentnej na podstawie nie jest obsługiwane przez usługi domenowe Azure AD.
* Nie można zmienić hasła bezpośrednio przed domeny zarządzanej. Użytkownicy końcowi mogą zmieniać swoje hasła albo przy użyciu mechanizmu zmiany hasła samoobsługi usługi Azure AD lub względem katalogu lokalnego. Te zmiany są automatycznie synchronizowane i dostępne w domeny zarządzanej.

## <a name="windows-server-remote-desktop-services-deployments-in-azure"></a>Wdrożenia na platformie Azure usług pulpitu zdalnego serwera systemu Windows
Usługi domenowe Azure AD służy do nadania zarządzanej usług domenowych AD do serwerów usług pulpitu zdalnego wdrożona na platformie Azure.

Aby uzyskać więcej informacji na temat tego scenariusza wdrażania, zobacz temat jak [Integrowanie usług domenowych Azure AD z wdrożeniem usług pulpitu zdalnego](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-azure-adds).


## <a name="domain-joined-hdinsight-clusters-preview"></a>Klastry HDInsight przyłączonych do domeny (wersja zapoznawcza)
Można skonfigurować klaster Azure HDInsight, który jest przyłączony do domeny zarządzanej usług domenowych Azure AD z włączoną zakres Apache. Tworzenie i stosowanie zasad Hive za pośrednictwem zakres Apache oraz użytkownicy (na przykład analityków danych) do nawiązania połączenia Hive za pomocą narzędzia oparte na ODBC, na przykład programu Excel, Tableau itp. Firma Microsoft pracuje Dodawanie innych obciążeń, takich jak bazy danych HBase, Spark i Storm, do domeny w usłudze HDInsight wkrótce.

Aby uzyskać więcej informacji na temat tego scenariusza wdrażania, zobacz temat jak [skonfigurować przyłączonych do domeny w usłudze hdinsight](../hdinsight/domain-joined/apache-domain-joined-configure.md)
