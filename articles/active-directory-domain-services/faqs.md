---
title: Często zadawane pytania — usługa Azure Active Directory Domain Services | Dokumentacja firmy Microsoft
description: Często zadawane pytania dotyczące usługi Azure Active Directory Domain Services
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 48731820-9e8c-4ec2-95e8-83dba1e58775
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: iainfou
ms.openlocfilehash: 10131ad306a8a24cb5835e55a02f1b502b20bea4
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2019
ms.locfileid: "67473369"
---
# <a name="azure-active-directory-domain-services-frequently-asked-questions-faqs"></a>Azure Active Directory Domain Services: Często zadawane pytania
Ta strona odpowiedzi na często zadawane pytania dotyczące usługi Azure Active Directory Domain Services. Wracaj aktualizacje.

## <a name="troubleshooting-guide"></a>Przewodnik rozwiązywania problemów
Zapoznaj się [przewodnik rozwiązywania problemów](troubleshoot.md) dla rozwiązania typowych problemów z Konfigurowanie lub administrowania usługami domenowymi Azure AD.

## <a name="configuration"></a>Konfigurowanie
### <a name="can-i-create-multiple-managed-domains-for-a-single-azure-ad-directory"></a>Można utworzyć wiele domen zarządzanych dla pojedynczego katalogu usługi Azure AD?
Nie. Można tworzyć tylko jednej domeny zarządzanej, obsługiwany przez usług domenowych Azure AD dla pojedynczego katalogu usługi Azure AD.  

### <a name="can-i-enable-azure-ad-domain-services-in-an-azure-resource-manager-virtual-network"></a>Można włączyć usługi domenowe Azure AD w sieci wirtualnej usługi Azure Resource Manager?
Tak. Usługi domenowe Azure AD można włączyć w sieci wirtualnej usługi Azure Resource Manager. Klasyczne sieci wirtualne platformy Azure nie są już obsługiwane do tworzenia nowych domen zarządzanych.

### <a name="can-i-migrate-my-existing-managed-domain-from-a-classic-virtual-network-to-a-resource-manager-virtual-network"></a>Czy mogę przenieść mój istniejąca domena zarządzana z klasyczną siecią wirtualną z siecią wirtualną usługi Resource Manager
Obecnie nie. Microsoft będzie dostarczać mechanizm do migracji istniejąca domena zarządzana z klasyczną siecią wirtualną z siecią wirtualną Menedżera zasobów w przyszłości.

### <a name="can-i-enable-azure-ad-domain-services-in-an-azure-csp-cloud-solution-provider-subscription"></a>Można włączyć usługi domenowe Azure AD w ramach subskrypcji Azure CSP (dostawca rozwiązań w chmurze)?
Tak. Zobacz, jak umożliwić [usług domenowych Azure AD w ramach subskrypcji Azure CSP](csp.md).

### <a name="can-i-enable-azure-ad-domain-services-in-a-federated-azure-ad-directory-i-do-not-synchronize-password-hashes-to-azure-ad-can-i-enable-azure-ad-domain-services-for-this-directory"></a>Można włączyć usługi domenowe Azure AD w federacyjnych Azure AD directory? Czy mogę synchronizuje skrótów haseł z usługą Azure AD. Można włączyć usługi domenowe Azure AD dla tego katalogu?
Nie. Usługi domenowe Azure AD wymaga dostępu do skróty haseł kont użytkowników w celu uwierzytelniania użytkowników za pomocą protokołu NTLM lub Kerberos. W katalogu federacyjnego skrótów haseł nie są przechowywane w katalogu usługi Azure AD. W związku z tym usług domenowych Azure AD nie działa z tych katalogów usługi Azure AD.

### <a name="can-i-make-azure-ad-domain-services-available-in-multiple-virtual-networks-within-my-subscription"></a>Czy można utworzyć usługi Azure AD Domain Services dostępna w wielu sieci wirtualnych w ramach mojej subskrypcji?
Sama usługa nie obsługuje bezpośrednio w tym scenariuszu. Twoja domena zarządzana jest dostępna w tylko jednej sieci wirtualnej w danym momencie. Można jednak skonfigurować łączność między wieloma sieciami wirtualnymi w celu udostępnienia usług domenowych Azure AD z innymi sieciami wirtualnymi. Zobacz, jak można [łączenie sieci wirtualnych na platformie Azure](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md).

### <a name="can-i-enable-azure-ad-domain-services-using-powershell"></a>Można włączyć usługi domenowe Azure AD przy użyciu programu PowerShell?
Tak. Zobacz [jak włączyć usługę Azure AD Domain Services przy użyciu programu PowerShell](powershell-create-instance.md).

### <a name="can-i-enable-azure-ad-domain-services-using-a-resource-manager-template"></a>Można włączyć usługi domenowe Azure AD przy użyciu szablonu usługi Resource Manager?
Nie, nie jest obecnie można włączyć usługi domenowe Azure AD przy użyciu szablonu. Zamiast tego użyj programu PowerShell, zobacz [jak włączyć usługę Azure AD Domain Services przy użyciu programu PowerShell](powershell-create-instance.md).

### <a name="can-i-add-domain-controllers-to-an-azure-ad-domain-services-managed-domain"></a>Czy mogę dodać kontrolerów domeny do domeny zarządzanej usług domenowych Azure AD?
Nie. Domeny oferowanymi przez usługi Azure AD Domain Services jest domeną zarządzaną. Nie trzeba aprowizować, konfigurując lub inny sposób zarządzania kontrolerami domeny dla tej domeny — tych działań związanych z zarządzaniem są dostarczane jako usługa przez firmę Microsoft. W związku z tym nie można dodać dodatkowe kontrolery domeny (odczytu i zapisu lub tylko do odczytu) dla domeny zarządzanej.

### <a name="can-guest-users-invited-to-my-directory-use-azure-ad-domain-services"></a>Użytkownicy-goście zaproszenie do katalogu, można użyć usług domenowych Azure AD?
Nie. Użytkownicy-goście zaproszenie do katalogu usługi Azure AD za pomocą [usługi Azure AD B2B](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md) procesu zaproszenia są synchronizowane Twojej domeny zarządzanej usług domenowych Azure AD. Hasła dla tych użytkowników nie są przechowywane w katalogu usługi Azure AD. W związku z tym usługi domenowe Azure AD nie ma możliwości synchronizacji NTLM i Kerberos wyznacza wartość skrótu dla tych użytkowników do domeny zarządzanej. W wyniku tych użytkowników nie można zalogować się do domeny zarządzanej lub dołączania komputerów do domeny zarządzanej.

## <a name="administration-and-operations"></a>Administracja i operacje
### <a name="can-i-connect-to-the-domain-controller-for-my-managed-domain-using-remote-desktop"></a>Można podłączyć do kontrolera domeny dla mojej domeny zarządzanej przy użyciu pulpitu zdalnego?
Nie. Nie masz uprawnień do łączenia się z kontrolerami domeny dla domeny zarządzanej za pośrednictwem pulpitu zdalnego. Członkowie grupy "Administratorzy usługi AAD DC" mogą administrować domeny zarządzanej za pomocą narzędzi administracji usługi AD, takich jak Centrum administracji usługi Active Directory (ADAC) lub programu PowerShell usługi AD. Te narzędzia są zainstalowane na serwerze Windows przyłączone do domeny zarządzanej przy użyciu funkcji "Narzędzia administracji zdalnej serwera".

### <a name="ive-enabled-azure-ad-domain-services-what-user-account-do-i-use-to-domain-join-machines-to-this-domain"></a>Czy włączono usługi domenowe Azure AD. Które konto użytkownika należy używać do domeny dołączania maszyn do tej domeny?
Członkowie grupy administracyjnej "Administratorzy usługi AAD DC" można maszyn przyłączania do domeny. Ponadto Członkowie tej grupy uzyskują dostęp zdalny do pulpitu na maszynach, które zostały dołączone do domeny.

### <a name="do-i-have-domain-administrator-privileges-for-the-managed-domain-provided-by-azure-ad-domain-services"></a>Czy masz uprawnienia administratora domeny dla domeny zarządzanej, udostępniane przez usługi domenowe Azure AD?
Nie. Nie udzielono uprawnień administracyjnych w domenie zarządzanej. "Administrator domeny" i "Administrator przedsiębiorstwa" uprawnienia nie są dostępne do użycia w domenie. Również elementy członkowskie administrator domeny lub grupy administrator przedsiębiorstwa w usłudze Active Directory w środowisku lokalnym nie są przyznawane uprawnienia administratora przedsiębiorstwa/domeny w domenie zarządzanej.

### <a name="can-i-modify-group-memberships-using-ldap-or-other-ad-administrative-tools-on-managed-domains"></a>Czy można modyfikować członkostwa w grupie przy użyciu protokołu LDAP lub innych narzędzi administracyjnych usługi AD w domenach zarządzanych
Nie. Nie można modyfikować członkostwa w grupach w przypadku domen obsługiwanych przez usługi domenowe Azure AD. To samo dotyczy atrybutów użytkownika. Możesz jednak zmienić atrybuty użytkownika lub członkostwa w grupach w usłudze Azure AD lub w domenie lokalnej. Takie zmiany są automatycznie synchronizowane w usłudze Azure AD Domain Services.

### <a name="how-long-does-it-take-for-changes-i-make-to-my-azure-ad-directory-to-be-visible-in-my-managed-domain"></a>Jak długo trwa zmiany można wprowadzić do katalogu usługi Azure AD mają być wyświetlane w mojej domeny zarządzanej?
Zmiany wprowadzone w katalogu usługi Azure AD przy użyciu interfejsu użytkownika usługi Azure AD lub programu PowerShell są synchronizowane z domeną zarządzaną. Ten proces — synchronizacja jest uruchamiana w tle. Po ukończeniu synchronizacji początkowej zwykle trwa około 20 minut, aby zmiany wprowadzone w usłudze Azure AD zostaną odzwierciedlone w Twojej domeny zarządzanej.

### <a name="can-i-extend-the-schema-of-the-managed-domain-provided-by-azure-ad-domain-services"></a>Czy można rozszerzyć schemat domeny zarządzanej, udostępniane przez usługi domenowe Azure AD?
Nie. Schemat jest zarządzane przez firmę Microsoft dla domeny zarządzanej. Rozszerzenia schematu nie są obsługiwane przez usługi domenowe Azure AD.

### <a name="can-i-modify-or-add-dns-records-in-my-managed-domain"></a>Można zmodyfikować lub dodać rekordy DNS w mojej domeny zarządzanej?
Tak. Członkowie grupy "Administratorzy usługi AAD DC" zostaną przyznane uprawnienia "Administrator DNS", aby zmodyfikować rekordy DNS w domenie zarządzanej. One umożliwia konsolę Menedżera DNS na komputerze z systemem Windows Server, przyłączone do domeny zarządzanej, zarządzać usługą DNS. Aby korzystać z konsoli Menedżera DNS, należy zainstalować "Narzędzia serwera DNS", który jest częścią funkcji opcjonalnych "Narzędzia administracji zdalnej serwera" na serwerze. Więcej informacji na temat [narzędzia do zarządzania, monitorowania i rozwiązywania problemów DNS](https://technet.microsoft.com/library/cc753579.aspx) jest dostępna w witrynie TechNet.

### <a name="what-is-the-password-lifetime-policy-on-a-managed-domain"></a>Co to jest okres istnienia hasła w domenie zarządzanej?
Domyślny okres istnienia hasła w usłudze Azure AD Domain Services domeny zarządzanej wynosi 90 dni. Okres istnienia tego hasła nie jest zsynchronizowany z okresem istnienia hasło skonfigurowane w usłudze Azure AD. W związku z tym może wystąpić sytuacja, gdy wygaśnie po upływie domenę zarządzaną haseł użytkowników, ale są nadal ważne w usłudze Azure AD. W takich przypadkach użytkownicy muszą zmienić swoje hasło w usłudze Azure AD i synchronizują nowe hasło do domeny zarządzanej. Ponadto "hasło — ma not wygasa" i "user-must-change-password-at-next-logon" atrybuty dla kont użytkowników nie są zsynchronizowane z domeną zarządzaną.

### <a name="does-azure-ad-domain-services-provide-ad-account-lockout-protection"></a>Usług domenowych Azure AD zapewnia ochronę blokady konta usługi AD?
Tak. Pięć próby podania hasła w ciągu 2 minut w domenie zarządzanej spowodować, że konto użytkownika zostało zablokowane przez 30 minut. Po 30 minutach konto użytkownika zostanie automatycznie odblokowana. Próby podania hasła w domenie zarządzanej nie zablokowania konta użytkownika w usłudze Azure AD. Konto użytkownika jest zablokowane tylko w ramach domeny usług domenowych Azure AD zarządzane.

## <a name="billing-and-availability"></a>Pomoc dotycząca rozliczeń i dostępności
### <a name="is-azure-ad-domain-services-a-paid-service"></a>Czy usługi Azure AD Domain Services płatnej usługi?
Tak. Aby uzyskać więcej informacji, odwiedź [stronę cennika](https://azure.microsoft.com/pricing/details/active-directory-ds/).

### <a name="is-there-a-free-trial-for-the-service"></a>Czy istnieje bezpłatna wersja próbna usługi?
Ta usługa znajduje się w bezpłatnej wersji próbnej platformy Azure. Możesz zarejestrować się w celu [bezpłatna miesięczna wersja próbna platformy Azure](https://azure.microsoft.com/pricing/free-trial/).

### <a name="can-i-pause-an-azure-ad-domain-services-managed-domain"></a>Czy można wstrzymać domeny zarządzanej usług domenowych Azure AD? 
Nie. Po włączeniu domeny zarządzanej usług domenowych Azure AD, usługa jest dostępne w wybranej sieci wirtualnej, dopóki nie zostanie zdezaktywuj lub Usuń domeny zarządzanej. Nie ma możliwości wstrzymania usługi. W systemie godzinowym nadal rozliczeń, dopóki nie usuniesz domeny zarządzanej.

### <a name="can-i-failover-azure-ad-domain-services-to-another-region-for-a-dr-event"></a>Czy mogę trybu failover w usługach domenowych Azure AD do innego regionu dla zdarzenia odzyskiwania po awarii?
Nie.  Azure AD Domain Services nie jest aktualnie dostępny model wdrożenia geograficznie nadmiarowy. Jest ograniczona do jednej sieci wirtualnej w regionie platformy Azure. Korzystanie z wielu regionach platformy Azure, musisz uruchomić kontrolerów domeny usługi Active Directory na maszynach wirtualnych IaaS platformy Azure.  Znajdują się wskazówki dotyczące architektury [tutaj](https://docs.microsoft.com/azure/architecture/reference-architectures/identity/adds-extend-domain).

### <a name="can-i-get-azure-ad-domain-services-as-part-of-enterprise-mobility-suite-ems-do-i-need-azure-ad-premium-to-use-azure-ad-domain-services"></a>Czy można uzyskać usług domenowych Azure AD w ramach Enterprise Mobility Suite (EMS)? Czy muszę mieć usługi Azure AD Premium do korzystania z usług domenowych Azure AD?
Nie. Azure AD Domain Services jest rozliczana według bieżącego użycia usług platformy Azure i nie jest częścią pakietu EMS. Usługi domenowe Azure AD mogą być używane z wszystkie wersje usługi Azure AD (bezpłatna, podstawowa i, Premium). Opłaty są naliczane godzinowo, w zależności od użycia.

### <a name="what-azure-regions-is-the-service-available-in"></a>Jakie regiony platformy Azure jest dostępne w usłudze?
Zapoznaj się [usług platformy Azure według regionu](https://azure.microsoft.com/regions/#services/) strony, aby wyświetlić listę regionów świadczenia usługi Azure, których usługi domenowe Azure AD jest dostępna.
