---
title: Często zadawane pytania — Azure Active Directory Domain Services | Microsoft Docs
description: Często zadawane pytania dotyczące Azure Active Directory Domain Services
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
ms.openlocfilehash: 80bc5ef78a73430dea23bdd644767d9fa9b3c9a8
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70124677"
---
# <a name="azure-active-directory-domain-services-frequently-asked-questions-faqs"></a>Azure Active Directory Domain Services: Często zadawane pytania
Ta strona zawiera odpowiedzi na często zadawane pytania dotyczące Azure Active Directory Domain Services. Kontynuuj sprawdzanie aktualizacji.

## <a name="troubleshooting-guide"></a>Przewodnik rozwiązywania problemów
Zapoznaj się z [przewodnikiem rozwiązywania problemów](troubleshoot.md) , aby zapoznać się z typowymi problemami dotyczącymi konfigurowania i administrowania Azure AD Domain Services.

## <a name="configuration"></a>Konfigurowanie
### <a name="can-i-create-multiple-managed-domains-for-a-single-azure-ad-directory"></a>Czy mogę utworzyć wiele domen zarządzanych dla jednego katalogu usługi Azure AD?
Nie. Można utworzyć tylko jedną domenę zarządzaną, Azure AD Domain Services dla jednego katalogu usługi Azure AD.  

### <a name="can-i-enable-azure-ad-domain-services-in-a-classic-virtual-network"></a>Czy mogę włączyć Azure AD Domain Services w klasycznej sieci wirtualnej?
Klasyczne sieci wirtualne nie są obsługiwane w przypadku nowych wdrożeń. Istniejące domeny zarządzane wdrożone w klasycznych sieciach wirtualnych są nadal obsługiwane.

### <a name="can-i-enable-azure-ad-domain-services-in-an-azure-resource-manager-virtual-network"></a>Czy mogę włączyć Azure AD Domain Services w sieci wirtualnej Azure Resource Manager?
Tak. Azure AD Domain Services można włączyć w Azure Resource Manager sieci wirtualnej. Klasyczne sieci wirtualne platformy Azure nie są już obsługiwane na potrzeby tworzenia nowych domen zarządzanych.

### <a name="can-i-migrate-my-existing-managed-domain-from-a-classic-virtual-network-to-a-resource-manager-virtual-network"></a>Czy mogę migrować istniejącą domenę zarządzaną z klasycznej sieci wirtualnej do sieci wirtualnej Menedżer zasobów?
Obecnie nie. Firma Microsoft dostarczy mechanizm migracji istniejącej domeny zarządzanej z klasycznej sieci wirtualnej do Menedżer zasobów sieci wirtualnej w przyszłości.

### <a name="can-i-enable-azure-ad-domain-services-in-an-azure-csp-cloud-solution-provider-subscription"></a>Czy mogę włączyć Azure AD Domain Services w ramach subskrypcji dostawcy rozwiązań w chmurze (CSP) platformy Azure?
Tak. Zobacz, jak można włączyć [Azure AD Domain Services w subskrypcjach CSP platformy Azure](csp.md).

### <a name="can-i-enable-azure-ad-domain-services-in-a-federated-azure-ad-directory-i-do-not-synchronize-password-hashes-to-azure-ad-can-i-enable-azure-ad-domain-services-for-this-directory"></a>Czy mogę włączyć Azure AD Domain Services w federacyjnym katalogu usługi Azure AD? Nie synchronizuję skrótów haseł z usługą Azure AD. Czy mogę włączyć Azure AD Domain Services dla tego katalogu?
Nie. Azure AD Domain Services musi mieć dostęp do skrótów haseł kont użytkowników, aby uwierzytelniać użytkowników za pośrednictwem protokołu NTLM lub Kerberos. W katalogu federacyjnym skróty haseł nie są przechowywane w katalogu usługi Azure AD. W związku z tym Azure AD Domain Services nie działa z takimi katalogami usługi Azure AD.

### <a name="can-i-make-azure-ad-domain-services-available-in-multiple-virtual-networks-within-my-subscription"></a>Czy mogę udostępniać Azure AD Domain Services w wielu sieciach wirtualnych w ramach mojej subskrypcji?
Sama usługa nie obsługuje bezpośrednio tego scenariusza. Twoja domena zarządzana jest dostępna tylko w jednej sieci wirtualnej naraz. Istnieje jednak możliwość skonfigurowania łączności między wieloma sieciami wirtualnymi w celu udostępnienia Azure AD Domain Services innym sieciom wirtualnym. Zobacz, jak [połączyć sieci wirtualne na platformie Azure](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md).

### <a name="can-i-enable-azure-ad-domain-services-using-powershell"></a>Czy mogę włączyć Azure AD Domain Services przy użyciu programu PowerShell?
Tak. Zobacz [jak włączyć Azure AD Domain Services przy użyciu programu PowerShell](powershell-create-instance.md).

### <a name="can-i-enable-azure-ad-domain-services-using-a-resource-manager-template"></a>Czy mogę włączyć Azure AD Domain Services przy użyciu szablonu Menedżer zasobów?
Nie, obecnie nie jest możliwe Włączanie Azure AD Domain Services przy użyciu szablonu. Zamiast tego należy użyć programu PowerShell, zobacz [jak włączyć Azure AD Domain Services przy użyciu programu PowerShell](powershell-create-instance.md).

### <a name="can-i-add-domain-controllers-to-an-azure-ad-domain-services-managed-domain"></a>Czy można dodać kontrolery domeny do Azure AD Domain Services domeny zarządzanej?
Nie. Domena udostępniona przez Azure AD Domain Services jest domeną zarządzaną. Nie ma potrzeby udostępniania, konfigurowania ani zarządzania kontrolerami domeny dla tej domeny — te działania związane z zarządzaniem są udostępniane jako usługa przez firmę Microsoft. W związku z tym nie można dodać dodatkowych kontrolerów domeny (do odczytu i zapisu lub tylko do odczytu) dla domeny zarządzanej.

### <a name="can-guest-users-invited-to-my-directory-use-azure-ad-domain-services"></a>Czy użytkownicy-Goście zaproszeni do mojego katalogu używają Azure AD Domain Services?
Nie. Użytkownicy-Goście zaproszeni do katalogu usługi Azure AD przy użyciu procesu zapraszania [B2B usługi Azure AD](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md) są synchronizowane w Azure AD Domain Services domenie zarządzanej. Jednak hasła dla tych użytkowników nie są przechowywane w katalogu usługi Azure AD. W związku z tym Azure AD Domain Services nie ma możliwości synchronizacji skrótów NTLM i Kerberos dla tych użytkowników w domenie zarządzanej. W związku z tym użytkownicy nie mogą zalogować się do domeny zarządzanej ani przyłączyć komputerów do domeny zarządzanej.

### <a name="can-i-move-an-existing-azure-ad-domain-services-managed-domain-to-a-different-subscription-resource-group-region-or-virtual-network"></a>Czy można przenieść istniejącą Azure AD Domain Servicesą domenę zarządzaną do innej subskrypcji, grupy zasobów, regionu lub sieci wirtualnej?
Nie. Po utworzeniu domeny zarządzanej Azure AD Domain Services nie można przenieść wystąpienia do innej grupy zasobów, sieci wirtualnej, subskrypcji itd. Podczas wdrażania wystąpienia usługi Azure AD DS należy zadbać o wybranie najbardziej odpowiedniej subskrypcji, grupy zasobów, regionu i sieci wirtualnej.

## <a name="administration-and-operations"></a>Administracja i operacje
### <a name="can-i-connect-to-the-domain-controller-for-my-managed-domain-using-remote-desktop"></a>Czy można połączyć się z kontrolerem domeny dla mojej domeny zarządzanej przy użyciu Pulpit zdalny?
Nie. Nie masz uprawnień do łączenia się z kontrolerami domeny dla domeny zarządzanej za pośrednictwem Pulpit zdalny. Członkowie grupy "Administratorzy usługi AAD DC" mogą administrować zarządzaną domeną przy użyciu narzędzi administracyjnych usług AD, takich jak Active Directory Administration Center (usługach ADAC) lub AD PowerShell. Te narzędzia są instalowane przy użyciu funkcji "Narzędzia administracji zdalnej serwera" na serwerze z systemem Windows przyłączonym do domeny zarządzanej.

### <a name="ive-enabled-azure-ad-domain-services-what-user-account-do-i-use-to-domain-join-machines-to-this-domain"></a>Włączono Azure AD Domain Services. Jakie konto użytkownika jest używane do przyłączania do domeny maszyn do tej domeny?
Członkowie grupy administracyjnej "Administratorzy usługi AAD DC" mogą przyłączać do domeny maszyny. Ponadto członkowie tej grupy mają przyznany dostęp pulpitu zdalnego do komputerów, które zostały przyłączone do domeny.

### <a name="do-i-have-domain-administrator-privileges-for-the-managed-domain-provided-by-azure-ad-domain-services"></a>Czy masz uprawnienia administratora domeny dla domeny zarządzanej dostarczonej przez Azure AD Domain Services?
Nie. Nie przyznano uprawnień administracyjnych w domenie zarządzanej. Przywileje "Administrator domeny" i "Administrator przedsiębiorstwa" nie są dostępne do użycia w ramach domeny. Członkowie grupy Administratorzy domeny lub Administratorzy przedsiębiorstwa w Active Directory lokalnym nie mają również uprawnień administratora domeny/przedsiębiorstwa w domenie zarządzanej.

### <a name="can-i-modify-group-memberships-using-ldap-or-other-ad-administrative-tools-on-managed-domains"></a>Czy mogę modyfikować członkostwa w grupach przy użyciu protokołu LDAP lub innych narzędzi administracyjnych usługi AD w domenach zarządzanych?
Nie. Członkostwa w grupach nie można modyfikować w domenach objętych usługą Azure AD Domain Services. To samo dotyczy atrybutów użytkownika. Można jednak zmienić członkostwa w grupach lub atrybuty użytkownika w usłudze Azure AD lub w domenie lokalnej. Takie zmiany są automatycznie synchronizowane z Azure AD Domain Services.

### <a name="how-long-does-it-take-for-changes-i-make-to-my-azure-ad-directory-to-be-visible-in-my-managed-domain"></a>Jak długo trwa wprowadzanie zmian wprowadzonych w katalogu usługi Azure AD, aby były widoczne w mojej domenie zarządzanej?
Zmiany wprowadzone w katalogu usługi Azure AD za pomocą interfejsu użytkownika usługi Azure AD lub programu PowerShell są synchronizowane z domeną zarządzaną. Ten proces synchronizacji jest uruchamiany w tle. Po zakończeniu synchronizacji początkowej zwykle trwa około 20 minut, aby zmiany wprowadzone w usłudze Azure AD zostały odzwierciedlone w domenie zarządzanej.

### <a name="can-i-extend-the-schema-of-the-managed-domain-provided-by-azure-ad-domain-services"></a>Czy można rozciągnąć schemat domeny zarządzanej udostępnionej przez Azure AD Domain Services?
Nie. Schemat jest zarządzany przez firmę Microsoft dla domeny zarządzanej. Rozszerzenia schematu nie są obsługiwane przez Azure AD Domain Services.

### <a name="can-i-modify-or-add-dns-records-in-my-managed-domain"></a>Czy mogę modyfikować lub dodawać rekordy DNS w mojej domenie zarządzanej?
Tak. Członkowie grupy "Administratorzy usługi AAD DC" otrzymują uprawnienia administratora "DNS", aby modyfikować rekordy DNS w domenie zarządzanej. Mogą oni korzystać z konsoli Menedżera DNS na komputerze z systemem Windows Server przyłączonym do domeny zarządzanej, aby zarządzać systemem DNS. Aby użyć konsoli Menedżera DNS, należy zainstalować "narzędzia serwera DNS", które jest częścią funkcji opcjonalnej "Narzędzia administracji zdalnej serwera" na serwerze. Więcej informacji na temat [narzędzi do administrowania, monitorowania i rozwiązywania problemów z usługą DNS](https://technet.microsoft.com/library/cc753579.aspx) jest dostępnych w witrynie TechNet.

### <a name="what-is-the-password-lifetime-policy-on-a-managed-domain"></a>Jakie są zasady okresu istnienia hasła w domenie zarządzanej?
Domyślny okres istnienia hasła w domenie zarządzanej Azure AD Domain Services to 90 dni. Okres istnienia hasła nie jest zsynchronizowany z okresem istnienia hasła skonfigurowanym w usłudze Azure AD. W związku z tym może wystąpić sytuacja, w której hasła użytkowników wygasną w domenie zarządzanej, ale są nadal ważne w usłudze Azure AD. W takich scenariuszach użytkownicy muszą zmienić swoje hasło w usłudze Azure AD, a nowe hasło zostanie zsynchronizowane z domeną zarządzaną. Ponadto atrybuty "hasło-nie wygasa" i "użytkownik-zmiana hasła przy następnym logowaniu" dla kont użytkowników nie są synchronizowane z domeną zarządzaną.

### <a name="does-azure-ad-domain-services-provide-ad-account-lockout-protection"></a>Czy Azure AD Domain Services zapewnić ochronę blokady konta usługi AD?
Tak. Pięć nieudanych prób wprowadzenia hasła w ciągu 2 minut w domenie zarządzanej powoduje, że konto użytkownika zostanie zablokowane przez 30 minut. Po 30 minutach konto użytkownika zostanie automatycznie odblokowane. Nieprawidłowe próby hasła w domenie zarządzanej nie Zablokuj konta użytkownika w usłudze Azure AD. Konto użytkownika jest blokowane tylko w ramach domeny zarządzanej Azure AD Domain Services.

## <a name="billing-and-availability"></a>Rozliczenia i dostępność
### <a name="is-azure-ad-domain-services-a-paid-service"></a>Czy Azure AD Domain Services płatną usługę?
Tak. Aby uzyskać więcej informacji, odwiedź [stronę cennika](https://azure.microsoft.com/pricing/details/active-directory-ds/).

### <a name="is-there-a-free-trial-for-the-service"></a>Czy istnieje bezpłatna wersja próbna usługi?
Ta usługa jest oferowana w bezpłatnej wersji próbnej platformy Azure. Możesz zarejestrować się w celu uzyskania [bezpłatnej miesięcznej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).

### <a name="can-i-pause-an-azure-ad-domain-services-managed-domain"></a>Czy można wstrzymać Azure AD Domain Servicesą domenę zarządzaną? 
Nie. Po włączeniu domeny zarządzanej Azure AD Domain Services usługa będzie dostępna w wybranej sieci wirtualnej do momentu wyłączenia/usunięcia domeny zarządzanej. Nie ma możliwości wstrzymania usługi. Rozliczanie jest kontynuowane co godzinę, dopóki nie usuniesz domeny zarządzanej.

### <a name="can-i-failover-azure-ad-domain-services-to-another-region-for-a-dr-event"></a>Czy można przeAzure AD Domain Servicesć w tryb failover do innego regionu dla zdarzenia DR?
Nie.  Azure AD Domain Services obecnie nie udostępnia modelu wdrażania geograficznie nadmiarowego. Jest ograniczona do pojedynczej sieci wirtualnej w regionie świadczenia usługi Azure. Jeśli chcesz korzystać z wielu regionów platformy Azure, musisz uruchomić kontrolery domena usługi Active Directory na maszynach wirtualnych usługi Azure IaaS.  Wskazówki dotyczące architektury można znaleźć [tutaj](https://docs.microsoft.com/azure/architecture/reference-architectures/identity/adds-extend-domain).

### <a name="can-i-get-azure-ad-domain-services-as-part-of-enterprise-mobility-suite-ems-do-i-need-azure-ad-premium-to-use-azure-ad-domain-services"></a>Czy mogę uzyskać Azure AD Domain Services w ramach pakietu Enterprise Mobility Suite (EMS)? Czy muszę Azure AD — wersja Premium używać Azure AD Domain Services?
Nie. Azure AD Domain Services to usługa platformy Azure z płatność zgodnie z rzeczywistym użyciem i nie jest częścią pakietu EMS. Azure AD Domain Services można używać ze wszystkimi wersjami usługi Azure AD (bezpłatna i Premium). Opłaty są naliczane godzinowo, w zależności od użycia.

### <a name="what-azure-regions-is-the-service-available-in"></a>W jakich regionach platformy Azure jest dostępna usługa?
Zapoznaj się ze stroną [usług platformy Azure według regionów](https://azure.microsoft.com/regions/#services/) , aby wyświetlić listę regionów świadczenia usługi Azure, w których Azure AD Domain Services jest dostępna.
