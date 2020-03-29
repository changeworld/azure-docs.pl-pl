---
title: Typowe scenariusze wdrażania usług domenowych usługi Azure AD | Dokumenty firmy Microsoft
description: Dowiedz się więcej o niektórych typowych scenariuszach i przypadkach użycia usług domenowych Active Directory platformy Azure w celu zapewnienia wartości i zaspokojenia potrzeb biznesowych.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: c5216ec9-4c4f-4b7e-830b-9d70cf176b20
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: iainfou
ms.openlocfilehash: ac67ef64ca4850c6e805b5314ace856114d889a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77917234"
---
# <a name="common-use-cases-and-scenarios-for-azure-active-directory-domain-services"></a>Typowe przypadki użycia i scenariusze usług domenowych Active Directory azure

Usługi domenowe Usługi active directory platformy Azure (Usługi Azure AD DS) zapewniają usługi domeny zarządzanej, takie jak dołączanie do domeny, zasady grupy, protokół LDAP (Lightweight Directory Access Protocol) i uwierzytelnianie Kerberos / NTLM. Usługi Azure AD DS integruje się z istniejącą dzierżawą usługi Azure AD, co umożliwia użytkownikom logowanie się przy użyciu istniejących poświadczeń. Te usługi domeny są używane bez konieczności wdrażania, zarządzania i instalowania poprawek kontrolerów domeny w chmurze, co zapewnia płynniejsze przenoszenie i przenoszenie zasobów lokalnych na platformę Azure.

W tym artykule opisano niektóre typowe scenariusze biznesowe, w których usługi Azure AD DS zapewnia wartość i spełnia te potrzeby.

## <a name="secure-administration-of-azure-virtual-machines"></a>Bezpieczne administrowanie maszynami wirtualnymi platformy Azure

Aby umożliwić korzystanie z jednego zestawu poświadczeń usługi AD, maszyny wirtualne platformy Azure (maszyny wirtualne) można przyłączyć do domeny zarządzanej usług Azure AD DS. Takie podejście zmniejsza problemy z zarządzaniem poświadczeniami, takie jak obsługa kont administratora lokalnego na każdej maszynie wirtualnej lub oddzielnych kont i haseł między środowiskami.

Maszyny wirtualne, które są przyłączone do domeny zarządzanej usług Azure AD DS mogą być również zarządzane i zabezpieczone przy użyciu zasad grupy. Wymagane linie bazowe zabezpieczeń mogą być stosowane do maszyn wirtualnych, aby zablokować je zgodnie z wytycznymi dotyczącymi zabezpieczeń firmy. Na przykład można użyć możliwości zarządzania zasadami grupy, aby ograniczyć typy aplikacji, które mogą być uruchamiane na maszynie Wirtualnej.

![Usprawniona administracja maszynami wirtualnymi platformy Azure](./media/active-directory-domain-services-scenarios/streamlined-vm-administration.png)

Przyjrzyjmy się typowym scenariuszem przykładowym. Ponieważ serwery i inna infrastruktura osiągną koniec reszty ekwikcjowej wersji końcowej, firma Contoso chce przenieść aplikacje obecnie hostowane lokalnie do chmury. Ich obecny standard IT upoważnia, że serwery obsługujące aplikacje firmowe muszą być przyłączone do domeny i zarządzane przy użyciu zasad grupy. Administrator IT firmy Contoso wolałby dołączać do maszyn wirtualnych do domeny wdrożonych na platformie Azure, aby ułatwić administrowanie, ponieważ użytkownicy mogą następnie logować się przy użyciu poświadczeń firmowych. Po przyłączeniu do domeny maszyny wirtualne można również skonfigurować tak, aby były zgodne z wymaganymi liniami bazowymi zabezpieczeń przy użyciu obiektów zasad grupy (OBIEKTÓW ZASAD GRUPY). Contoso wolałby nie wdrażać, monitorować i zarządzać własnymi kontrolerami domeny na platformie Azure.

Usługi Azure AD DS doskonale nadają się do tego przypadku użycia. Domena zarządzana usługą Azure AD DS umożliwia dołączanie do maszyn wirtualnych w domenie, używanie jednego zestawu poświadczeń i stosowanie zasad grupy. Jako domena zarządzana nie musisz samodzielnie konfigurować i obsługiwać kontrolerów domeny.

### <a name="deployment-notes"></a>Uwagi dotyczące wdrażania

Następujące zagadnienia dotyczące wdrażania dotyczą tego przykładowego przypadku użycia:

* Domeny zarządzane usługi Azure AD DS domyślnie używają jednej, płaskiej struktury jednostki organizacyjnej (OU). Wszystkie maszyny wirtualne przyłączone do domeny znajdują się w jednej jednostce organizacyjnej. W razie potrzeby można utworzyć niestandardowe instalacje organizacyjne.
* Usługi Azure AD DS używa wbudowanego obiektu zasad grupy dla użytkowników i kontenerów komputerów. Aby uzyskać dodatkową kontrolę, można utworzyć niestandardowe obiekty zasad grupy i kierować je do niestandardowych jednostek organizacyjnych.
* Usługi Azure AD DS obsługuje podstawowy schemat obiektu komputera usługi AD. Nie można rozszerzyć schematu obiektu komputera.

## <a name="lift-and-shift-on-premises-applications-that-use-ldap-bind-authentication"></a>Aplikacje lokalne typu lift-and-shift korzystające z uwierzytelniania LDAP

W przykładowym scenariuszu contoso ma aplikację lokalną, która została zakupiona od niezależnego przedsiębiorstwa wiele lat temu. Aplikacja jest obecnie w trybie konserwacji przez isv i żądanie zmian w aplikacji jest zbyt drogie. Ta aplikacja ma fronton oparty na sieci Web, który zbiera poświadczenia użytkownika przy użyciu formularza sieci web, a następnie uwierzytelnia użytkowników, wykonując powiązanie LDAP z lokalnym środowiskiem usług AD DS.

![Powiązanie LDAP](./media/active-directory-domain-services-scenarios/ldap-bind.png)

Contoso chce przeprowadzić migrację tej aplikacji na platformę Azure. Aplikacja powinna nadal działać w stanie, w jakim jest, bez konieczności zmiany. Ponadto użytkownicy powinni mieć możliwość uwierzytelniania przy użyciu istniejących poświadczeń firmowych i bez dodatkowego szkolenia. Powinna być przejrzysta dla użytkowników końcowych, w których aplikacja jest uruchomiona.

W tym scenariuszu usługi Azure AD DS umożliwia aplikacjom wykonywanie powiązań LDAP w ramach procesu uwierzytelniania. Starsze aplikacje lokalne mogą przenosić i przenosić się na platformę Azure i nadal bezproblemowo uwierzytelniać użytkowników bez żadnych zmian w konfiguracji lub środowisku użytkownika.

### <a name="deployment-notes"></a>Uwagi dotyczące wdrażania

Następujące zagadnienia dotyczące wdrażania dotyczą tego przykładowego przypadku użycia:

* Upewnij się, że aplikacja nie musi modyfikować/zapisywać w katalogu. Dostęp do zapisu LDAP do domeny zarządzanej usług Azure AD DS nie jest obsługiwany.
* Nie można zmienić haseł bezpośrednio w domenie zarządzanej usług Azure AD DS. Użytkownicy końcowi mogą zmieniać swoje hasło przy użyciu samoobsługowego mechanizmu zmiany hasła usługi Azure AD lub w katalogu lokalnym. Zmiany te są następnie automatycznie synchronizowane i dostępne w domenie zarządzanej usług Azure AD DS.

## <a name="lift-and-shift-on-premises-applications-that-use-ldap-read-to-access-the-directory"></a>Aplikacje lokalne lift-and-shift, które korzystają z odczytu LDAP w celu uzyskania dostępu do katalogu

Podobnie jak w poprzednim scenariuszu przykładu załóżmy, że contoso ma lokalną aplikację biznesową (LOB), która została opracowana prawie dziesięć lat temu. Ta aplikacja jest obsługujących katalogi i został zaprojektowany do używania LDAP do odczytu informacji/atrybutów o użytkownikach z usług AD DS. Aplikacja nie modyfikuje atrybutów ani w inny sposób nie zapisuje w katalogu.

Contoso chce przeprowadzić migrację tej aplikacji na platformę Azure i wycofać starzejący się sprzęt lokalny aktualnie obsługujący tę aplikację. Nie można przepisać aplikacji do używania nowoczesnych interfejsów API katalogu, takich jak interfejs API programu Microsoft Graph oparty na rest. Wymagana jest opcja lift-and-shift, w której można przeprowadzić migrację aplikacji w celu uruchomienia w chmurze, bez modyfikowania kodu lub przepisywania aplikacji.

Aby pomóc w tym scenariuszu, usługi Azure AD DS umożliwia aplikacjom wykonywanie odczytów LDAP względem domeny zarządzanej, aby uzyskać informacje o atrybutach, które są potrzebne. Aplikacja nie musi być przepisany, więc lift-and-shift na platformie Azure pozwala użytkownikom nadal korzystać z aplikacji, nie zdając sobie sprawy, że jest zmiana w którym działa.

### <a name="deployment-notes"></a>Uwagi dotyczące wdrażania

Następujące zagadnienia dotyczące wdrażania dotyczą tego przykładowego przypadku użycia:

* Upewnij się, że aplikacja nie musi modyfikować/zapisywać w katalogu. Dostęp do zapisu LDAP do domeny zarządzanej usług Azure AD DS nie jest obsługiwany.
* Upewnij się, że aplikacja nie potrzebuje niestandardowego/rozszerzonego schematu usługi Active Directory. Rozszerzenia schematu nie są obsługiwane w usługach Azure AD DS.

## <a name="migrate-an-on-premises-service-or-daemon-application-to-azure"></a>Migrowanie lokalnej usługi lub aplikacji demona na platformę Azure

Niektóre aplikacje zawierają wiele warstw, w których jedna z warstw musi wykonywać uwierzytelnione wywołania do warstwy wewnętrznej bazy danych, takiej jak baza danych. Konta usług AD są często używane w tych scenariuszach. Po podniesieniu i przeniesieniu aplikacji do platformy Azure usługi Azure AD DS umożliwia dalsze korzystanie z kont usług w ten sam sposób. Można użyć tego samego konta usługi, które jest synchronizowane z katalogu lokalnego do usługi Azure AD lub utworzyć niestandardową jednostkę organizacyjną, a następnie utworzyć oddzielne konto usługi w tej jednostki organizacyjnej. Przy każdym podejściu aplikacje nadal działają w ten sam sposób, aby wykonać uwierzytelnione wywołania do innych warstw i usług.

![Konto usługi przy użyciu usługi WIA](./media/active-directory-domain-services-scenarios/wia-service-account.png)

W tym przykładzie contoso ma niestandardową aplikację magazynu oprogramowania, która zawiera frontonomię sieci web, serwer SQL i serwer FTP wewnętrznej bazy danych. Uwierzytelnianie zintegrowane z systemem Windows przy użyciu kont usług uwierzytelnia fronton sieci Web na serwerze FTP. Frontołówka sieci Web jest skonfigurowana do uruchamiania jako konto usługi. Serwer wewnętrznej bazy danych jest skonfigurowany do autoryzowania dostępu z konta usługi dla frontonia sieci Web. Contoso nie chce wdrażać i zarządzać własnymi maszynami wirtualnymi kontrolera domeny w chmurze, aby przenieść tę aplikację na platformę Azure.

W tym scenariuszu serwery hostujące frontołów sieci web, serwer SQL i serwer FTP mogą być migrowane do maszyn wirtualnych platformy Azure i przyłączane do domeny zarządzanej usług Azure AD DS. Maszyny wirtualne mogą następnie używać tego samego konta usługi w katalogu lokalnym na potrzeby uwierzytelniania aplikacji, który jest synchronizowany za pośrednictwem usługi Azure AD przy użyciu usługi Azure AD Connect.

### <a name="deployment-notes"></a>Uwagi dotyczące wdrażania

Następujące zagadnienia dotyczące wdrażania dotyczą tego przykładowego przypadku użycia:

* Upewnij się, że aplikacje używają nazwy użytkownika i hasła do uwierzytelniania. Uwierzytelnianie oparte na certyfikatach lub kartach inteligentnych nie jest obsługiwane przez usługi Azure AD DS.
* Nie można zmienić haseł bezpośrednio w domenie zarządzanej usług Azure AD DS. Użytkownicy końcowi mogą zmieniać swoje hasło przy użyciu samoobsługowego mechanizmu zmiany hasła usługi Azure AD lub w katalogu lokalnym. Zmiany te są następnie automatycznie synchronizowane i dostępne w domenie zarządzanej usług Azure AD DS.

## <a name="windows-server-remote-desktop-services-deployments-in-azure"></a>Wdrożenia usług pulpitu zdalnego systemu Windows Server na platformie Azure

Usługi Azure AD DS umożliwiają świadczenie usług domeny zarządzanej na serwerach pulpitu zdalnego wdrożonych na platformie Azure. Aby uzyskać więcej informacji na temat tego scenariusza wdrażania, zobacz [jak zintegrować usługi domenowe usługi Azure AD domain services z wdrożeniem usług pulpitu zdalnego.][windows-rds]

## <a name="domain-joined-hdinsight-clusters"></a>Klastry HDInsight przyłączone do domeny

Można skonfigurować klaster usługi Azure HDInsight, który jest przyłączony do domeny zarządzanej usługi Azure AD DS z włączoną apache Ranger. Zasady hive można tworzyć i stosować za pośrednictwem aplikacji Apache Ranger oraz zezwalać użytkownikom, takim jak analityki danych, na łączenie się z gałęzią przy użyciu narzędzi opartych na odbc, takich jak Excel lub Tableau. Kontynuujemy prace nad dodawaniem innych obciążeń, takich jak HBase, Spark i Storm do usługi HDInsight przyłączonych do domeny.

Aby uzyskać więcej informacji na temat tego scenariusza wdrażania, zobacz [jak skonfigurować klastry HDInsight przyłączone do domeny][hdinsight]

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć, [utwórz i skonfiguruj wystąpienie usług domenowych Usługi domenowe Active Directory platformy Azure][tutorial-create-instance]

<!-- INTERNAL LINKS -->
[hdinsight]: ../hdinsight/domain-joined/apache-domain-joined-configure.md
[tutorial-create-instance]: tutorial-create-instance.md

<!-- EXTERNAL LINKS -->
[windows-rds]: /windows-server/remote/remote-desktop-services/rds-azure-adds
