---
title: 'Azure Active Directory Domain Services: Scenariusze wdrażania | Dokumentacja firmy Microsoft'
description: Scenariusze wdrażania usług domenowych Azure AD
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: c5216ec9-4c4f-4b7e-830b-9d70cf176b20
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/21/2017
ms.author: ergreenl
ms.openlocfilehash: 0659586512b36c51c5058271fa5e1bdb46efbc3b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60416997"
---
# <a name="deployment-scenarios-and-use-cases"></a>Scenariusze wdrażania i przypadki użycia
W tej sekcji przyjrzymy się kilka scenariuszy i przypadków użycia, które korzystają z usług domenowych w usłudze Azure Active Directory (AD).

## <a name="secure-easy-administration-of-azure-virtual-machines"></a>Bezpieczne i łatwe zarządzanie maszyn wirtualnych platformy Azure
Za pomocą usługi Azure Active Directory Domain Services do zarządzania usługi Azure virtual machines w sposób zoptymalizowany. Maszyny wirtualne platformy Azure mogą być przyłączone do domeny zarządzanej, co umożliwia Zaloguj się za pomocą firmowych poświadczeń usługi AD. Takie podejście pomaga uniknąć problemów zarządzania poświadczeń, takich jak obsługa kont administratora lokalnego na wszystkich maszynach wirtualnych platformy Azure.

Maszyny wirtualne serwera, które są przyłączone do domeny zarządzanej można również zarządzane i chronione przy użyciu zasad grupy. Możesz zastosować linie bazowe zabezpieczeń na maszynach wirtualnych platformy Azure i blokowania zgodnie z wytycznymi dotyczącymi zabezpieczeń firmy. Na przykład można użyć możliwości zarządzania zasadami grupy, aby ograniczyć typy aplikacji, które można uruchamiać na tych maszynach wirtualnych.

![Usprawnione zarządzanie maszyn wirtualnych platformy Azure](./media/active-directory-domain-services-scenarios/streamlined-vm-administration.png)

Serwery i inne infrastrukturę osiągnie wycofanych z eksploatacji, firmy Contoso przechodzi wiele aplikacji, które aktualnie jest zainstalowana lokalnie, w chmurze. Ich bieżące normy IT określającemu, że serwery hostujące aplikacje firmowe musi być przyłączony do domeny i zarządzane przy użyciu zasad grupy. Firmy Contoso administratora IT preferuje do maszyn wirtualnych przyłączanie do domeny wdrożonych na platformie Azure, w celu ułatwienia administracji. W rezultacie administratorzy i użytkownicy zalogować się przy użyciu swoich poświadczeń firmowych. W tym samym czasie można skonfigurować maszyny do wykonania linie bazowe zabezpieczeń za pomocą zasad grupy. Contoso nie chce mieć wdrażanie, monitorowanie i Zarządzanie kontrolerami domeny na platformie Azure w celu zabezpieczenia maszyn wirtualnych platformy Azure. W związku z tym usługi Azure AD Domain Services to doskonałe rozwiązanie dla tego przypadku użycia.

**Uwagi dotyczące wdrażania**

Należy wziąć pod uwagę następujące ważne punkty, w tym scenariuszu wdrażania:

* Domeny zarządzane oferowanymi przez usługi Azure AD Domain Services zapewniają jednej płaskiej strukturze OU (jednostki organizacyjnej) domyślnie. Wszystkie komputery przyłączone do domeny znajdują się w jednej jednostce Organizacyjnej prostego. Można jednak utworzyć niestandardowe jednostek organizacyjnych.
* Usługi domenowe Azure AD obsługuje proste zasady grupy w formie wbudowanego obiektu zasad grupy Każdy dla użytkowników i komputerów kontenerów. Można tworzyć niestandardowe obiekty zasad grupy i określania elementów docelowych widoków z jednostkami organizacyjnymi niestandardowych.
* Usługi domenowe Azure AD obsługuje schematu obiektu podstawowego komputera usługi AD. Nie można rozszerzyć schematu obiektu komputera.

## <a name="lift-and-shift-an-on-premises-application-that-uses-ldap-bind-authentication-to-azure-infrastructure-services"></a>Lift-and-shift aplikacji w środowisku lokalnym, która używa uwierzytelniania powiązania LDAP na usługach infrastruktury platformy Azure
![Powiązanie z protokołem LDAP](./media/active-directory-domain-services-scenarios/ldap-bind.png)

Firma Contoso ma aplikację lokalną, które zostało zakupione w ramach z niezależnym dostawcą oprogramowania i wielu lat temu. Aplikacja jest obecnie w trybie konserwacji przez niezależnych dostawców oprogramowania i żądania zmiany w aplikacji jest zbyt duży dla firmy Contoso. Ta aplikacja ma opartego na sieci web frontonu, który gromadzi informacje o poświadczenia użytkownika przy użyciu formularza sieci web, a następnie uwierzytelnia użytkowników, wykonując powiązania LDAP z firmową usługą Active Directory. Contoso przeprowadzić migrację tej aplikacji w usługach infrastruktury platformy Azure. Jest pożądane, czy aplikacja działa podobnie jak, bez konieczności wprowadzania zmian. Ponadto użytkownicy należy przeprowadzić uwierzytelnienie przy użyciu dotychczasowych poświadczeń firmowych, bez konieczności Ponowne szkolenie użytkowników, aby wykonywały inaczej. Innymi słowy użytkownicy końcowi powinny być oblivious, z którym aplikacja jest uruchomiona i migracji powinny być przezroczyste dla nich.

**Uwagi dotyczące wdrażania**

Należy wziąć pod uwagę następujące ważne punkty, w tym scenariuszu wdrażania:

* Upewnij się, że aplikacja nie trzeba zmodyfikować/zapisu do katalogu. Domeny zarządzane udostępniane przez usługi domenowe Azure AD do zapisu LDAP nie jest obsługiwana.
* Nie można zmienić hasła bezpośrednio w odniesieniu do domeny zarządzanej. Użytkownicy końcowi mogą zmieniać swoje hasła albo przy użyciu mechanizmu zmiany haseł usługi Azure AD lub względem katalogu lokalnego. Te zmiany są automatycznie synchronizowane i dostępne w domenie zarządzanej.

## <a name="lift-and-shift-an-on-premises-application-that-uses-ldap-read-to-access-the-directory-to-azure-infrastructure-services"></a>Lift-and-shift aplikacji w środowisku lokalnym, która korzysta z protokołu LDAP do odczytu do uzyskiwania dostępu do katalogu na usługach infrastruktury platformy Azure
Firma Contoso ma lokalnej aplikacji line-of-business (LOB), który został opracowany prawie lat temu. Ta aplikacja jest katalogiem pamiętać i był projektowany do pracy z systemem Windows Server AD. Aplikacja używa protokołu LDAP (Lightweight Directory Access Protocol) do odczytywania informacji i atrybuty o użytkownikach z usługi Active Directory. Aplikacja nie Modyfikuj atrybuty lub w przeciwnym razie zapis w katalogu. Firma Contoso chce zmigrowania tej aplikacji w usługach infrastruktury platformy Azure i wycofania sprzętu lokalnego przestarzałej obecnie hostującej tę aplikację. Aplikacja nie może zostać przepisane, aby używać katalogu nowoczesnych interfejsów API, takich jak interfejs API programu Graph usługi Azure AD oparty na protokole REST. W związku z tym opcja lift-and-shift jest pożądane, zgodnie z którymi można poddać migracji aplikacji do uruchamiania w chmurze, bez konieczności modyfikowania kodu lub ponownego tworzenia aplikacji.

**Uwagi dotyczące wdrażania**

Należy wziąć pod uwagę następujące ważne punkty, w tym scenariuszu wdrażania:

* Upewnij się, że aplikacja nie trzeba zmodyfikować/zapisu do katalogu. Domeny zarządzane udostępniane przez usługi domenowe Azure AD do zapisu LDAP nie jest obsługiwana.
* Upewnij się, że aplikacja nie musi niestandardowe/rozszerzony schemat usługi Active Directory. Rozszerzenia schematu nie są obsługiwane w usługach domenowych Azure AD.

## <a name="migrate-an-on-premises-service-or-daemon-application-to-azure-infrastructure-services"></a>Migrowanie lokalnych aplikacji usługi lub demona usługi infrastruktury platformy Azure
Niektóre aplikacje składają się z wielu warstw, w którym jednej z warstw musi wykonać uwierzytelnionych wywołań zawierającą warstwę zaplecza, np. Warstwa bazy danych. Konta usługi Active Directory są często używane dla tych przypadków użycia. Możesz lift-and-shift takich aplikacji, usług infrastruktury platformy Azure i używania usługi Azure AD Domain Services na potrzeby tożsamości te aplikacje. Można użyć tego samego konta usługi, które są synchronizowane z katalogu lokalnego do usługi Azure AD. Alternatywnie można najpierw utwórz niestandardowe jednostki Organizacyjnej, a następnie utworzyć osobnego konta usługowego w tej jednostce Organizacyjnej, do wdrożenia tych aplikacji.

![Konto usługi przy użyciu WIA](./media/active-directory-domain-services-scenarios/wia-service-account.png)

Firma Contoso ma niestandardowej magazynu aplikacji, które obejmuje fronton sieci web, programu SQL server i serwera wewnętrznej bazy danych FTP. Windows integrated authentication kont usług jest używany do uwierzytelniania fronton sieci web do serwera FTP. Fronton sieci web jest skonfigurowany do uruchamiania jako konto usługi. Serwer wewnętrznej bazy danych jest skonfigurowany do autoryzowania dostępu z konta usługi dla frontonu sieci web. Contoso preferuje nie ma potrzeby wdrażania maszyny wirtualnej kontrolera domeny w chmurze, aby przenieść tej aplikacji w usługach infrastruktury platformy Azure. Firmy Contoso IT administrator może wdrożyć serwerów obsługujących fronton sieci web, programu SQL server i serwerowi FTP na maszynach wirtualnych platformy Azure. Te maszyny, następnie są przyłączone do domeny zarządzanej usług domenowych Azure AD. Następnie można używają tego samego konta usługi w katalogu ich w środowisku lokalnym na potrzeby uwierzytelniania w aplikacji. To konto usługi jest zsynchronizowany z domeną zarządzaną usług domenowych Azure AD i jest dostępny do użytku.

**Uwagi dotyczące wdrażania**

Należy wziąć pod uwagę następujące ważne punkty, w tym scenariuszu wdrażania:

* Upewnij się, że aplikacja używa nazwy użytkownika i hasła do uwierzytelniania. Uwierzytelnianie na podstawie certyfikatu/Smartcard nie jest obsługiwane przez usługi domenowe Azure AD.
* Nie można zmienić hasła bezpośrednio w odniesieniu do domeny zarządzanej. Użytkownicy końcowi mogą zmieniać swoje hasła albo przy użyciu mechanizmu zmiany haseł usługi Azure AD lub względem katalogu lokalnego. Te zmiany są automatycznie synchronizowane i dostępne w domenie zarządzanej.

## <a name="windows-server-remote-desktop-services-deployments-in-azure"></a>Wdrożenia na platformie Azure usług pulpitu zdalnego serwera systemu Windows
Usług domenowych Azure AD umożliwia zapewnienie zarządzane usługi domenowe AD serwerów usług pulpitu zdalnego z wdrożonymi na platformie Azure.

Aby uzyskać więcej informacji na temat tego scenariusza wdrażania, zobacz instrukcje [integracji usługi Azure AD Domain Services z wdrożeniem usług RDS](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-azure-adds).


## <a name="domain-joined-hdinsight-clusters-preview"></a>Przyłączone do domeny klastrów HDInsight (wersja zapoznawcza)
Można skonfigurować klastra usługi Azure HDInsight, który jest przyłączony do domeny zarządzanej usług domenowych Azure AD przy użyciu struktury Apache Ranger włączone. Tworzenie i stosowanie zasad usługi Hive przy użyciu struktury Apache Ranger oraz umożliwiają użytkownikom (na przykład, analitykom danych), połączyć się z programu Hive przy użyciu narzędzi opartych na ODBC, na przykład programu Excel, Tableau itp. Firma Microsoft pracuje nad dodaniem innych obciążeń, takich jak bazy danych HBase i Spark, Storm, można szybko HDInsight z przyłączonym do domeny.

Aby uzyskać więcej informacji na temat tego scenariusza wdrażania, zobacz instrukcje [Konfigurowanie przyłączonych do domeny klastrów HDInsight](../hdinsight/domain-joined/apache-domain-joined-configure.md)
