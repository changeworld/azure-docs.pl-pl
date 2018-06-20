---
title: Często zadawane pytania — Azure Active Directory Domain Services | Dokumentacja firmy Microsoft
description: Często zadawane pytania dotyczące usług domenowych Azure Active Directory
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 48731820-9e8c-4ec2-95e8-83dba1e58775
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/30/2018
ms.author: maheshu
ms.openlocfilehash: 41e5d515dcea4a4bc2ebfec8493d44de877a263f
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36214733"
---
# <a name="azure-active-directory-domain-services-frequently-asked-questions-faqs"></a>Azure Active Directory Domain Services: Często zadawane pytania (FAQ)
Ta strona zawiera odpowiedzi na często zadawane pytania dotyczące usługi Azure Active Directory Domain Services. Sprawdzanie wstecz do aktualizacji.

## <a name="troubleshooting-guide"></a>Przewodnik rozwiązywania problemów
Zapoznaj się [przewodnik rozwiązywania problemów](active-directory-ds-troubleshooting.md) dla rozwiązania typowych problemów z administrowaniem w usługach domenowych Azure AD lub konfigurowanie.

## <a name="configuration"></a>Konfigurowanie
### <a name="can-i-create-multiple-managed-domains-for-a-single-azure-ad-directory"></a>Można utworzyć wiele domen zarządzanych w jednym katalogu usługi Azure AD?
Nie. Można tworzyć tylko jednej domeny zarządzanej, obsługiwanych przez usługi domenowe Azure AD z jednym katalogu usługi Azure AD.  

### <a name="can-i-enable-azure-ad-domain-services-in-an-azure-resource-manager-virtual-network"></a>Można włączyć usługi domenowe Azure AD w sieci wirtualnej platformy Azure Resource Manager?
Tak. Można włączyć usługi domenowe Azure AD w sieci wirtualnej platformy Azure Resource Manager. Klasyczne sieci wirtualnych platformy Azure nie są już obsługiwane do tworzenia nowych domen zarządzanych.

### <a name="can-i-migrate-my-existing-managed-domain-from-a-classic-virtual-network-to-a-resource-manager-virtual-network"></a>Można przeprowadzić migrację mojej istniejącej domeny zarządzanej z klasycznych sieci wirtualnych Menedżera zasobów sieci wirtualnej?
Nie można obecnie. Firma Microsoft będzie dostarczać mechanizm do migracji istniejącej domeny zarządzanej z klasycznej sieci wirtualnej do sieci wirtualnej Resource Manager w przyszłości.

### <a name="can-i-enable-azure-ad-domain-services-in-an-azure-csp-cloud-solution-provider-subscription"></a>Można włączyć usługi domenowe Azure AD w subskrypcji platformy Azure dostawcy usług Kryptograficznych (Cloud Solution Provider)?
Tak. Zobacz, jak można włączyć [usług domenowych Azure AD w ramach subskrypcji platformy Azure dostawcy usług Kryptograficznych](active-directory-ds-csp.md).

### <a name="can-i-enable-azure-ad-domain-services-in-a-federated-azure-ad-directory-i-do-not-synchronize-password-hashes-to-azure-ad-can-i-enable-azure-ad-domain-services-for-this-directory"></a>Można włączyć usługi domenowe Azure AD w federacyjnych Azure AD directory? Nie synchronizacji skrótów haseł do usługi Azure AD. Można włączyć usługi domenowe Azure AD dla katalogu?
Nie. Usługi domenowe Azure AD musi mieć dostęp do wartości skrótów haseł kont użytkowników, do uwierzytelniania użytkowników za pomocą protokołu NTLM lub Kerberos. W katalogu federacyjnych skrótów haseł nie są przechowywane w katalogu usługi Azure AD. W związku z tym usługi domenowe Azure AD nie działa z tych katalogów usługi Azure AD.

### <a name="can-i-make-azure-ad-domain-services-available-in-multiple-virtual-networks-within-my-subscription"></a>Czy można utworzyć usługi domenowe Azure AD dostępne w wielu sieci wirtualnych w mojej subskrypcji?
Sama usługa nie obsługuje bezpośrednio w tym scenariuszu. Domeny zarządzanej, jest dostępna w tylko jedną sieć wirtualną w czasie. Jednak należy skonfigurować połączenie między wieloma sieciami wirtualnymi do udostępnienia usług domenowych Azure AD do innych sieci wirtualnych. Zobacz, jak można [połączyć sieci wirtualnych na platformie Azure](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md).

### <a name="can-i-enable-azure-ad-domain-services-using-powershell"></a>Można włączyć usługi domenowe Azure AD przy użyciu programu PowerShell?
Tak. Zobacz [sposób, aby włączyć domenowych Azure AD usług przy użyciu programu PowerShell](active-directory-ds-enable-using-powershell.md).

### <a name="can-i-enable-azure-ad-domain-services-using-a-resource-manager-template"></a>Można włączyć usługi domenowe Azure AD przy użyciu szablonu usługi Resource Manager?
Tak. Zobacz [sposób, aby włączyć domenowych Azure AD usług przy użyciu programu PowerShell](active-directory-ds-enable-using-powershell.md).

### <a name="can-i-add-domain-controllers-to-an-azure-ad-domain-services-managed-domain"></a>Kontrolery domeny można dodać do domeny zarządzanej usług domenowych Azure AD?
Nie. Domena udostępniane przez usługi domenowe Azure AD jest domeną zarządzaną. Nie trzeba było możliwe, konfigurowania i inaczej Zarządzanie kontrolerami domeny dla tej domeny — te działania zarządzania są obsługiwane jako usługa przez firmę Microsoft. W związku z tym nie można dodać dodatkowe kontrolery domeny (odczytu i zapisu lub tylko do odczytu) dla domeny zarządzanej.

### <a name="can-guest-users-invited-to-my-directory-use-azure-ad-domain-services"></a>Goście zaproszenie do katalogu Moje można użyć usług domenowych Azure AD?
Nie. Goście zaproszenie do używania usługi Azure AD directory [B2B usługi Azure AD](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md) zsynchronizowaniu procesu zaproszenie do domeny zarządzanej usług domenowych Azure AD. Hasła dla tych użytkowników nie są przechowywane w katalogu usługi Azure AD. W związku z tym usługi domenowe Azure AD nie ma możliwości synchronizacji NTLM i Kerberos, tworzy skrót dla tych użytkowników do domeny zarządzanej. W związku z tym takich użytkowników nie może zalogować się do domeny zarządzanej lub dołączania komputerów do domeny zarządzanej.

## <a name="administration-and-operations"></a>Administracja i operacje
### <a name="can-i-connect-to-the-domain-controller-for-my-managed-domain-using-remote-desktop"></a>Można podłączyć do kontrolera domeny dla mojej domeny zarządzanej przy użyciu pulpitu zdalnego?
Nie. Nie masz uprawnień do łączenia się z kontrolerami domeny dla domeny zarządzanej za pośrednictwem pulpitu zdalnego. Członkowie grupy "Administratorzy kontrolera domeny usługi AAD" mogą administrować domeny zarządzanej przy użyciu narzędzi administracyjnych AD, takie jak Centrum administracji usługi Active Directory (ADAC) lub AD PowerShell. Te narzędzia są zainstalowane na serwerze systemu Windows przyłączone do domeny zarządzanej przy użyciu funkcji "Narzędzia administracji zdalnej serwera".

### <a name="ive-enabled-azure-ad-domain-services-what-user-account-do-i-use-to-domain-join-machines-to-this-domain"></a>Czy włączono usługi domenowe Azure AD. Które konto użytkownika należy używać do domeny sprzężenia maszyn do tej domeny?
Elementy członkowskie grupy administracyjnej "Administratorzy kontrolera domeny usługi AAD" można maszyny przyłączania do domeny. Ponadto Członkowie tej grupy są przyznawane pulpitu dostęp zdalny do komputerów, na których został przyłączony do domeny.

### <a name="do-i-have-domain-administrator-privileges-for-the-managed-domain-provided-by-azure-ad-domain-services"></a>Czy mają uprawnienia administratora domeny dla domeny zarządzanej udostępniane przez usługi domenowe Azure AD?
Nie. Nie udzielono uprawnienia administracyjne do domeny zarządzanej. Zarówno domeny administratora, jak i administratora przedsiębiorstwa uprawnienia nie są dostępne do użycia w ramach domeny. Również członkowie administrator domeny lub grup administratorów przedsiębiorstwa w usłudze Active Directory lokalnych nie są przyznawane uprawnienia administratora domeny/enterprise na domeny zarządzanej.

### <a name="can-i-modify-group-memberships-using-ldap-or-other-ad-administrative-tools-on-managed-domains"></a>Można zmodyfikować członkostwa grupy przy użyciu protokołu LDAP lub innych narzędzi administracyjnych AD w domenach zarządzanych?
Nie. Nie można zmodyfikować członkostwa w grupach w domenach obsługiwanych przez usługi domenowe Azure AD. To samo dotyczy atrybuty użytkownika. Można jednak zmienić atrybuty użytkownika lub członkostwa w grupach w usłudze Azure AD lub w domenie lokalnej. Takie zmiany są synchronizowane automatycznie w usługach domenowych Azure AD.

### <a name="how-long-does-it-take-for-changes-i-make-to-my-azure-ad-directory-to-be-visible-in-my-managed-domain"></a>Jak długo trwa zmiany można wprowadzić w katalogu Moje usługi Azure AD mają być wyświetlane w mojej domeny zarządzanej?
Zmiany wprowadzone w katalogu usługi Azure AD przy użyciu interfejsu użytkownika programu Azure AD lub programu PowerShell są synchronizowane z domeny zarządzanej. Ten proces synchronizacji działa w tle. Po ukończeniu synchronizacji początkowej zwykle trwa około 20 minut, aby zmiany wprowadzone w usłudze Azure AD zostaną odzwierciedlone w domeny zarządzanej.

### <a name="can-i-extend-the-schema-of-the-managed-domain-provided-by-azure-ad-domain-services"></a>Czy można rozszerzyć schemat domeny zarządzanej udostępniane przez usługi domenowe Azure AD?
Nie. Schemat jest zarządzany przez firmę Microsoft do domeny zarządzanej. Rozszerzenia schematu nie są obsługiwane przez usługi domenowe Azure AD.

### <a name="can-i-modify-or-add-dns-records-in-my-managed-domain"></a>Można zmodyfikować lub dodać rekordy DNS w mojej domeny zarządzanej?
Tak. Członków grupy "Administratorzy kontrolera domeny usługi AAD" są przyznawane uprawnienia administratora DNS, do modyfikowania rekordów DNS w domenie zarządzanej. Do zarządzania DNS użyciem konsoli Menedżera DNS na komputerze z systemem Windows Server przyłączony do domeny zarządzanej. Przy użyciu konsoli Menedżera DNS, należy zainstalować "Narzędzia serwera DNS", który jest częścią funkcji opcjonalnych "Narzędzia administracji zdalnej serwera" na serwerze. Więcej informacji na temat [narzędzia do zarządzania, monitorowania i rozwiązywania problemów DNS](https://technet.microsoft.com/library/cc753579.aspx) jest dostępna w witrynie TechNet.

### <a name="what-is-the-password-lifetime-policy-on-a-managed-domain"></a>Co to jest okres istnienia zasad haseł w domenie zarządzanej?
Domyślny okres istnienia hasła w domenie AD Azure domeny zarządzanej usług to 90 dni. Okres istnienia tego hasła nie jest zsynchronizowany z okresu istnienia hasło skonfigurowane w usłudze Azure AD. W związku z tym może być sytuacja, gdy wygaśnie po upływie domeny zarządzanej hasła użytkowników, ale są nadal ważne w usłudze Azure AD. W takich sytuacjach użytkownicy będą musieli zmienić swoje hasło w usłudze Azure AD i nowe hasło będzie synchronizować do domeny zarządzanej. Ponadto 'hasło — czy — nie wygasa' i "user-must-change-password-at-next-logon" atrybuty dla kont użytkowników nie są zsynchronizowane do domeny zarządzanej.

### <a name="does-azure-ad-domain-services-provide-ad-account-lockout-protection"></a>Usługi domenowe Azure AD zapewnia ochronę blokady konta AD?
Tak. Pięć nieudanych próbach wpisania hasła w ciągu 2 minut na domeny zarządzanej, że konto użytkownika zostało zablokowane przez 30 minut. Po 30 minutach konto użytkownika jest automatycznie odblokowane. Nieudanych próbach wpisania hasła na domeny zarządzanej nie zablokowania konta użytkownika w usłudze Azure AD. Konto użytkownika jest zablokowane tylko w swojej domenie zarządzanych usług domenowych Azure AD.

## <a name="billing-and-availability"></a>Rozliczeń i dostępności
### <a name="is-azure-ad-domain-services-a-paid-service"></a>Jest usługą płatną usług domenowych Azure AD?
Tak. Aby uzyskać więcej informacji, odwiedź [stronę cennika](https://azure.microsoft.com/pricing/details/active-directory-ds/).

### <a name="is-there-a-free-trial-for-the-service"></a>Czy istnieje bezpłatna wersja próbna usługi?
Ta usługa znajduje się w bezpłatnej wersji próbnej platformy Azure. Możesz zarejestrować się w celu [w warstwie bezpłatna miesięczna wersja próbna systemu Azure](https://azure.microsoft.com/pricing/free-trial/).

### <a name="can-i-pause-an-azure-ad-domain-services-managed-domain"></a>Czy można wstrzymać domeny zarządzanej usług domenowych Azure AD? 
Nie. Po włączeniu domeny zarządzanej usług domenowych Azure AD, usługa jest dostępne w wybranej sieci wirtualnej, dopóki nie zostanie zdezaktywuj lub Usuń domeny zarządzanej. Nie istnieje sposób wstrzymania usługi. Karta nadal co godzinę, do momentu usunięcia domeny zarządzanej.

### <a name="can-i-get-azure-ad-domain-services-as-part-of-enterprise-mobility-suite-ems-do-i-need-azure-ad-premium-to-use-azure-ad-domain-services"></a>W ramach Enterprise Mobility Suite (EMS) można uzyskać usług domenowych Azure AD? Należy Azure AD Premium do użycia usług domenowych Azure AD?
Nie. Usługi domenowe Azure AD jest płatność za rzeczywiste użycie usługi Azure i nie jest częścią pakietu EMS. Usługi domenowe Azure AD może być używany z wszystkie wersje usługi Azure AD (bezpłatne, podstawowe i, Premium). Opłaty są naliczane co godzinę, w zależności od użycia.

### <a name="what-azure-regions-is-the-service-available-in"></a>Jakie regiony platformy Azure jest dostępna w usłudze?
Zapoznaj się [regionów platformy Azure](https://azure.microsoft.com/regions/#services/) stronę, aby wyświetlić listę regionów platformy Azure, gdy są dostępne usługi domenowe Azure AD.
