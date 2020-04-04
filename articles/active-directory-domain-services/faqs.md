---
title: Często zadawane pytania dotyczące usług domenowych usługi Azure AD | Dokumenty firmy Microsoft
description: Przeczytaj i zrozum niektóre z często zadawanych pytań dotyczących konfiguracji, administrowania i dostępności usług domenowych Active Directory platformy Azure
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 48731820-9e8c-4ec2-95e8-83dba1e58775
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 03/09/2020
ms.author: iainfou
ms.openlocfilehash: 86b68b794928900717bea25623e7eb833c23e86c
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655348"
---
# <a name="frequently-asked-questions-faqs"></a>Często zadawane pytania (często zadawane pytania)

Ta strona zawiera odpowiedzi na często zadawane pytania dotyczące usług domenowych Active Directory platformy Azure.

## <a name="configuration"></a>Konfigurowanie

* [Czy można utworzyć wiele domen zarządzanych dla jednego katalogu usługi Azure AD?](#can-i-create-multiple-managed-domains-for-a-single-azure-ad-directory)
* [Czy mogę włączyć usługi domenowe usługi ad azure w klasycznej sieci wirtualnej?](#can-i-enable-azure-ad-domain-services-in-a-classic-virtual-network)
* [Czy mogę włączyć usługi domenowe usługi ad azure w sieci wirtualnej usługi Azure Resource Manager?](#can-i-enable-azure-ad-domain-services-in-an-azure-resource-manager-virtual-network)
* [Czy mogę przeprowadzić migrację istniejącej domeny zarządzanej z klasycznej sieci wirtualnej do sieci wirtualnej Menedżera zasobów?](#can-i-migrate-my-existing-managed-domain-from-a-classic-virtual-network-to-a-resource-manager-virtual-network)
* [Czy mogę włączyć usługi domenowe usługi azure ad w subskrypcji dostawcy usług CSP platformy Azure (dostawca rozwiązań w chmurze)?](#can-i-enable-azure-ad-domain-services-in-an-azure-csp-cloud-solution-provider-subscription)
* [Czy mogę włączyć usługi domenowe usługi ad azure w sfederowanym katalogu usługi Azure AD? Nie synchronizuję skrótów haseł z usługą Azure AD. Czy mogę włączyć usługi domenowe usługi azure ad dla tego katalogu?](#can-i-enable-azure-ad-domain-services-in-a-federated-azure-ad-directory-i-do-not-synchronize-password-hashes-to-azure-ad-can-i-enable-azure-ad-domain-services-for-this-directory)
* [Czy mogę udostępnić usługi domenowe usługi Azure AD w wielu sieciach wirtualnych w ramach mojej subskrypcji?](#can-i-make-azure-ad-domain-services-available-in-multiple-virtual-networks-within-my-subscription)
* [Czy mogę włączyć usługi domenowe usługi azure ad przy użyciu programu PowerShell?](#can-i-enable-azure-ad-domain-services-using-powershell)
* [Czy mogę włączyć usługi domenowe usługi azure ad przy użyciu szablonu Menedżera zasobów?](#can-i-enable-azure-ad-domain-services-using-a-resource-manager-template)
* [Czy mogę dodać kontrolery domeny do domeny zarządzanej usług ad-domenowych usługi Azure?](#can-i-add-domain-controllers-to-an-azure-ad-domain-services-managed-domain)
* [Czy użytkownicy-goście zaproszeni do mojego katalogu mogą korzystać z usług domenowych usługi Azure AD?](#can-guest-users-invited-to-my-directory-use-azure-ad-domain-services)
* [Czy mogę przenieść istniejącą domenę zarządzaną usług ad ad azure do innej subskrypcji, grupy zasobów, regionu lub sieci wirtualnej?](#can-i-move-an-existing-azure-ad-domain-services-managed-domain-to-a-different-subscription-resource-group-region-or-virtual-network)
* [Czy usługi domenowe usługi Azure AD zawierają opcje wysokiej dostępności?](#does-azure-ad-domain-services-include-high-availability-options)

### <a name="can-i-create-multiple-managed-domains-for-a-single-azure-ad-directory"></a>Czy można utworzyć wiele domen zarządzanych dla jednego katalogu usługi Azure AD?
Nie. Można utworzyć tylko jedną domenę zarządzaną obsługiwaną przez usługi domenowe usługi azure ad dla jednego katalogu usługi Azure AD.

### <a name="can-i-enable-azure-ad-domain-services-in-a-classic-virtual-network"></a>Czy mogę włączyć usługi domenowe usługi ad azure w klasycznej sieci wirtualnej?
Klasyczne sieci wirtualne nie są obsługiwane w przypadku nowych wdrożeń. Istniejące domeny zarządzane wdrożone w klasycznych sieciach wirtualnych są nadal obsługiwane do momentu wycofania ich 1 marca 2023 r. W przypadku istniejących wdrożeń można [migrować usługi domenowe usługi Azure AD DOMAIN Services z klasycznego modelu sieci wirtualnej do Menedżera zasobów](migrate-from-classic-vnet.md).

Aby uzyskać więcej informacji, zobacz [oficjalne zawiadomienie o umoridach.](https://azure.microsoft.com/updates/we-are-retiring-azure-ad-domain-services-classic-vnet-support-on-march-1-2023/)

### <a name="can-i-enable-azure-ad-domain-services-in-an-azure-resource-manager-virtual-network"></a>Czy mogę włączyć usługi domenowe usługi ad azure w sieci wirtualnej usługi Azure Resource Manager?
Tak. Usługi domenowe usługi Azure AD mogą być włączone w sieci wirtualnej usługi Azure Resource Manager. Klasyczne sieci wirtualne platformy Azure nie są już dostępne podczas tworzenia domeny zarządzanej.

### <a name="can-i-migrate-my-existing-managed-domain-from-a-classic-virtual-network-to-a-resource-manager-virtual-network"></a>Czy mogę przeprowadzić migrację istniejącej domeny zarządzanej z klasycznej sieci wirtualnej do sieci wirtualnej Menedżera zasobów?
Tak. Aby uzyskać więcej informacji, zobacz [Migrowanie usług domenowych usługi Azure AD z klasycznego modelu sieci wirtualnej do Menedżera zasobów](migrate-from-classic-vnet.md).

### <a name="can-i-enable-azure-ad-domain-services-in-an-azure-csp-cloud-solution-provider-subscription"></a>Czy mogę włączyć usługi domenowe usługi azure ad w subskrypcji dostawcy usług CSP platformy Azure (dostawca rozwiązań w chmurze)?
Tak. Aby uzyskać więcej informacji, zobacz [jak włączyć usługi domenowe usługi azure ad w subskrypcjach usługi CSP platformy Azure](csp.md).

### <a name="can-i-enable-azure-ad-domain-services-in-a-federated-azure-ad-directory-i-do-not-synchronize-password-hashes-to-azure-ad-can-i-enable-azure-ad-domain-services-for-this-directory"></a>Czy mogę włączyć usługi domenowe usługi ad azure w sfederowanym katalogu usługi Azure AD? Nie synchronizuję skrótów haseł z usługą Azure AD. Czy mogę włączyć usługi domenowe usługi azure ad dla tego katalogu?
Nie. Aby uwierzytelnić użytkowników za pośrednictwem usługi NTLM lub Protokołu Kerberos, usługi domenowe usługi azure ad muszą mieć dostęp do skrótów haseł kont użytkowników. W katalogu federatywnym skróty haseł nie są przechowywane w katalogu usługi Azure AD. W związku z tym usługi domenowe usługi Azure AD nie działa z takich katalogów usługi Azure AD.

### <a name="can-i-make-azure-ad-domain-services-available-in-multiple-virtual-networks-within-my-subscription"></a>Czy mogę udostępnić usługi domenowe usługi Azure AD w wielu sieciach wirtualnych w ramach mojej subskrypcji?
Sama usługa nie obsługuje bezpośrednio tego scenariusza. Domena zarządzana jest dostępna tylko w jednej sieci wirtualnej naraz. Można jednak skonfigurować łączność między wieloma sieciami wirtualnymi, aby udostępnić usługi domenowe usługi Azure AD na inne sieci wirtualne. Aby uzyskać więcej informacji, zobacz [jak połączyć sieci wirtualne na platformie Azure przy użyciu bram sieci VPN](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md) lub komunikacji [równorzędnej sieci wirtualnej](../virtual-network/virtual-network-peering-overview.md).

### <a name="can-i-enable-azure-ad-domain-services-using-powershell"></a>Czy mogę włączyć usługi domenowe usługi azure ad przy użyciu programu PowerShell?
Tak. Aby uzyskać więcej informacji, zobacz [jak włączyć usługi domenowe usługi Azure AD przy użyciu programu PowerShell](powershell-create-instance.md).

### <a name="can-i-enable-azure-ad-domain-services-using-a-resource-manager-template"></a>Czy mogę włączyć usługi domenowe usługi azure ad przy użyciu szablonu Menedżera zasobów?
Tak, można utworzyć domenę zarządzaną usług domenowych usługi AD azure przy użyciu szablonu Menedżera zasobów. Podmiot usługi i grupa usługi Azure AD do administrowania muszą zostać utworzone przy użyciu witryny Azure portal lub programu Azure PowerShell przed wdrożeniem szablonu. Aby uzyskać więcej informacji, zobacz [Tworzenie domeny zarządzanej usług Azure AD DS przy użyciu szablonu usługi Azure Resource Manager](template-create-instance.md). Podczas tworzenia domeny zarządzanej usług domenowych usługi AD azure w witrynie Azure portal, istnieje również opcja, aby wyeksportować szablon do użycia z dodatkowych wdrożeń.

### <a name="can-i-add-domain-controllers-to-an-azure-ad-domain-services-managed-domain"></a>Czy mogę dodać kontrolery domeny do domeny zarządzanej usług ad-domenowych usługi Azure?
Nie. Domena udostępniana przez usługi domenowe usługi azure ad jest domeną zarządzaną. Nie trzeba aprowizować, konfigurować ani w inny sposób zarządzać kontrolerami domeny dla tej domeny. Te działania związane z zarządzaniem są świadczone jako usługa przez firmę Microsoft. W związku z tym nie można dodać dodatkowych kontrolerów domeny (do odczytu i zapisu lub tylko do odczytu) dla domeny zarządzanej.

### <a name="can-guest-users-invited-to-my-directory-use-azure-ad-domain-services"></a>Czy użytkownicy-goście zaproszeni do mojego katalogu mogą korzystać z usług domenowych usługi Azure AD?
Nie. Użytkownicy-goście zaproszeni do katalogu usługi Azure AD przy użyciu procesu zapraszania [usługi Azure AD B2B](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md) są synchronizowani z domeną zarządzana usługami domenowymi usługi Azure AD. Jednak hasła dla tych użytkowników nie są przechowywane w katalogu usługi Azure AD. W związku z tym usługi domenowe usługi azure ad nie ma możliwości synchronizowania skrótów NTLM i Kerberos dla tych użytkowników do domeny zarządzanej. Tacy użytkownicy nie mogą zalogować się ani dołączyć komputerów do domeny zarządzanej.

### <a name="can-i-move-an-existing-azure-ad-domain-services-managed-domain-to-a-different-subscription-resource-group-region-or-virtual-network"></a>Czy mogę przenieść istniejącą domenę zarządzaną usług ad ad azure do innej subskrypcji, grupy zasobów, regionu lub sieci wirtualnej?
Nie. Po utworzeniu domeny zarządzanej usług domenowych usługi AD azure nie można przenieść wystąpienia do innej grupy zasobów, sieci wirtualnej, subskrypcji itp. Podczas wdrażania wystąpienia usług Azure AD DS należy dopilnować, aby wybrać najbardziej odpowiednią subskrypcję, grupę zasobów, region i sieć wirtualną.

### <a name="does-azure-ad-domain-services-include-high-availability-options"></a>Czy usługi domenowe usługi Azure AD zawierają opcje wysokiej dostępności?

Tak. Każda domena zarządzana usługami domenowymi usługi Azure AD zawiera dwa kontrolery domeny. Nie zarządzasz tymi kontrolerami domeny ani nie łączysz się z nimi, są one częścią usługi zarządzanej. Jeśli wdrożysz usługi domenowe usługi azure ad w regionie, który obsługuje strefy dostępności, kontrolery domeny są rozproszone między strefami. W regionach, które nie obsługują stref dostępności, kontrolery domeny są dystrybuowane między zestawami dostępności. Nie masz żadnych opcji konfiguracji ani kontroli zarządzania nad tą dystrybucją. Aby uzyskać więcej informacji, zobacz [Opcje dostępności maszyn wirtualnych na platformie Azure](../virtual-machines/windows/availability.md).

## <a name="administration-and-operations"></a>Administracja i operacje

* [Czy mogę połączyć się z kontrolerem domeny dla mojej domeny zarządzanej za pomocą pulpitu zdalnego?](#can-i-connect-to-the-domain-controller-for-my-managed-domain-using-remote-desktop)
* [Włączyłem usługi domenowe usługi Azure AD Domain Services. Jakiego konta użytkownika używam do dołączania do komputerów do tej domeny?](#ive-enabled-azure-ad-domain-services-what-user-account-do-i-use-to-domain-join-machines-to-this-domain)
* [Czy mam uprawnienia administratora domeny dla domeny zarządzanej udostępniane przez usługi domenowe usługi Azure AD?](#do-i-have-domain-administrator-privileges-for-the-managed-domain-provided-by-azure-ad-domain-services)
* [Czy można modyfikować członkostwo w grupach przy użyciu protokołu LDAP lub innych narzędzi administracyjnych usługi AD w domenach zarządzanych?](#can-i-modify-group-memberships-using-ldap-or-other-ad-administrative-tools-on-managed-domains)
* [Jak długo trwa zmiana wprowadzone w moim katalogu usługi Azure AD, aby były widoczne w mojej domenie zarządzanej?](#how-long-does-it-take-for-changes-i-make-to-my-azure-ad-directory-to-be-visible-in-my-managed-domain)
* [Czy można rozszerzyć schemat domeny zarządzanej udostępnianej przez usługi domenowe usługi Azure AD?](#can-i-extend-the-schema-of-the-managed-domain-provided-by-azure-ad-domain-services)
* [Czy mogę modyfikować lub dodawać rekordy DNS w domenie zarządzanej?](#can-i-modify-or-add-dns-records-in-my-managed-domain)
* [Co to jest zasada okresu istnienia hasła w domenie zarządzanej?](#what-is-the-password-lifetime-policy-on-a-managed-domain)
* [Czy usługi domenowe usługi Azure AD zapewniają ochronę blokady konta USŁUGI AD?](#does-azure-ad-domain-services-provide-ad-account-lockout-protection)

### <a name="can-i-connect-to-the-domain-controller-for-my-managed-domain-using-remote-desktop"></a>Czy mogę połączyć się z kontrolerem domeny dla mojej domeny zarządzanej za pomocą pulpitu zdalnego?
Nie. Nie masz uprawnień do łączenia się z kontrolerami domeny dla domeny zarządzanej za pomocą pulpitu zdalnego. Członkowie grupy *Administratorzy kontrolera domeny kontrolera domeny usługi AAD* mogą administrować domeną zarządzaną za pomocą narzędzi administracyjnych usługi AD, takich jak Centrum administracyjne usługi Active Directory (ADAC) lub Program AD PowerShell. Narzędzia te są instalowane przy użyciu funkcji *Narzędzia administracji zdalnej serwera* na serwerze systemu Windows przyłączonym do domeny zarządzanej. Aby uzyskać więcej informacji, zobacz [Tworzenie maszyny Wirtualnej zarządzania w celu konfigurowania domeny zarządzanej usług ad-domenowych usługi Azure AD i administrowania nimi.](tutorial-create-management-vm.md)

### <a name="ive-enabled-azure-ad-domain-services-what-user-account-do-i-use-to-domain-join-machines-to-this-domain"></a>Włączyłem usługi domenowe usługi Azure AD Domain Services. Jakiego konta użytkownika używam do dołączania do komputerów do tej domeny?
Każde konto użytkownika, które jest częścią domeny zarządzanej usługi Azure AD DS, może dołączyć do maszyny Wirtualnej. Członkowie grupy *Administratorzy kontrolera domeny usługi AAD* otrzymują dostęp do pulpitu zdalnego do komputerów, które zostały przyłączone do domeny zarządzanej.

### <a name="do-i-have-domain-administrator-privileges-for-the-managed-domain-provided-by-azure-ad-domain-services"></a>Czy mam uprawnienia administratora domeny dla domeny zarządzanej udostępniane przez usługi domenowe usługi Azure AD?
Nie. Nie przyznano uprawnień administracyjnych w domenie zarządzanej. *Uprawnienia Administratora domeny* i *administratora przedsiębiorstwa* nie są dostępne do użycia w domenie. Członkom administratora domeny lub grup administratorów przedsiębiorstwa w lokalnej usłudze Active Directory nie są również przyznawane uprawnienia administratora domeny/przedsiębiorstwa w domenie zarządzanej.

### <a name="can-i-modify-group-memberships-using-ldap-or-other-ad-administrative-tools-on-managed-domains"></a>Czy można modyfikować członkostwo w grupach przy użyciu protokołu LDAP lub innych narzędzi administracyjnych usługi AD w domenach zarządzanych?
Nie można zmodyfikować użytkowników i grup zsynchronizowanych z usługi Azure Active Directory z usługami domenowymi usługi Azure AD, ponieważ ich źródłem jest usługa Azure Active Directory. Każdy użytkownik lub grupa pochodząca z domeny zarządzanej może zostać zmodyfikowana.

### <a name="how-long-does-it-take-for-changes-i-make-to-my-azure-ad-directory-to-be-visible-in-my-managed-domain"></a>Jak długo trwa zmiana wprowadzone w moim katalogu usługi Azure AD, aby były widoczne w mojej domenie zarządzanej?
Zmiany wprowadzone w katalogu usługi Azure AD przy użyciu interfejsu użytkownika usługi Azure AD lub programu PowerShell są automatycznie synchronizowane z domeną zarządza zarządza zarządzalącą. Ten proces synchronizacji jest uruchamiany w tle. Nie ma zdefiniowanego okresu dla tej synchronizacji, aby zakończyć wszystkie zmiany obiektu.

### <a name="can-i-extend-the-schema-of-the-managed-domain-provided-by-azure-ad-domain-services"></a>Czy można rozszerzyć schemat domeny zarządzanej udostępnianej przez usługi domenowe usługi Azure AD?
Nie. Schemat jest administrowany przez firmę Microsoft dla domeny zarządzanej. Rozszerzenia schematu nie są obsługiwane przez usługi domenowe usługi Azure AD Domain Services.

### <a name="can-i-modify-or-add-dns-records-in-my-managed-domain"></a>Czy mogę modyfikować lub dodawać rekordy DNS w domenie zarządzanej?
Tak. Członkowie grupy *Administratorzy kontrolera domeny usługi AAD* mają uprawnienia *administratora DNS* do modyfikowania rekordów DNS w domenie zarządzanej. Ci użytkownicy mogą używać konsoli Menedżera DNS na komputerze z systemem Windows Server przyłączonym do domeny zarządzanej w celu zarządzania systemem DNS. Aby użyć konsoli Menedżera DNS, należy zainstalować *narzędzie DNS Server Tools*, które jest częścią opcjonalnej funkcji Narzędzia *administracji zdalnej serwera* na serwerze. Aby uzyskać więcej informacji, zobacz [Administrowanie systemem DNS w domenie zarządzanej usług domenowych usługi Azure AD](manage-dns.md).

### <a name="what-is-the-password-lifetime-policy-on-a-managed-domain"></a>Co to jest zasada okresu istnienia hasła w domenie zarządzanej?
Domyślny okres istnienia hasła w domenie zarządzanej usług domenowych usługi ad azure wynosi 90 dni. Ten okres istnienia hasła nie jest synchronizowany z okresem istnienia hasła skonfigurowanym w usłudze Azure AD. W związku z tym może mieć sytuację, w której hasła użytkowników wygasają w domenie zarządzanej, ale są nadal ważne w usłudze Azure AD. W takich scenariuszach użytkownicy muszą zmienić swoje hasło w usłudze Azure AD, a nowe hasło zostanie zsynchronizowane z domeną zarządzaną. Ponadto *atrybuty password-does-not-expire* i *user-must-change-password-at-next-logon* dla kont użytkowników nie są synchronizowane z domeną zarządzaną.

### <a name="does-azure-ad-domain-services-provide-ad-account-lockout-protection"></a>Czy usługi domenowe usługi Azure AD zapewniają ochronę blokady konta USŁUGI AD?
Tak. Pięć prób nieprawidłowego hasła w ciągu 2 minut w domenie zarządzanej powoduje zablokowanie konta użytkownika na 30 minut. Po 30 minutach konto użytkownika zostanie automatycznie odblokowane. Próby nieprawidłowego hasła w domenie zarządzanej nie blokują konta użytkownika w usłudze Azure AD. Konto użytkownika jest zablokowane tylko w domenie zarządzanej usług domenowych usługi Azure AD. Aby uzyskać więcej informacji, zobacz [Zasady blokowania haseł i kont w domenach zarządzanych](password-policy.md).

## <a name="billing-and-availability"></a>Rozliczenia i dostępność

* [Czy usługi domenowe usługi Azure AD są usługą płatną?](#is-azure-ad-domain-services-a-paid-service)
* [Czy istnieje bezpłatna wersja próbna dla usługi?](#is-there-a-free-trial-for-the-service)
* [Czy mogę wstrzymać domenę zarządzaną usług domenowych usługi AD azure?](#can-i-pause-an-azure-ad-domain-services-managed-domain)
* [Czy mogę przewinąć w błąd usługi domenowe usługi azure ad do innego regionu dla zdarzenia odzyskiwania po awarii?](#can-i-pause-an-azure-ad-domain-services-managed-domain)
* [Czy mogę uzyskać usługi domenowe usługi Azure AD w ramach pakietu Enterprise Mobility Suite (EMS)? Czy do korzystania z usług domenowych usługi Azure AD DOMAIN jest potrzebna usługa Azure AD Premium?](#can-i-failover-azure-ad-domain-services-to-another-region-for-a-dr-event)
* [W jakich regionach platformy Azure jest dostępna usługa?](#can-i-get-azure-ad-domain-services-as-part-of-enterprise-mobility-suite-ems-do-i-need-azure-ad-premium-to-use-azure-ad-domain-services)

### <a name="is-azure-ad-domain-services-a-paid-service"></a>Czy usługi domenowe usługi Azure AD są usługą płatną?
Tak. Aby uzyskać więcej informacji, odwiedź [stronę cennika](https://azure.microsoft.com/pricing/details/active-directory-ds/).

### <a name="is-there-a-free-trial-for-the-service"></a>Czy istnieje bezpłatna wersja próbna dla usługi?
Usługi domenowe usługi Azure AD są uwzględnione w bezpłatnej wersji próbnej platformy Azure. Możesz zarejestrować się, aby uzyskać [bezpłatną miesięczną wersję próbną platformy Azure.](https://azure.microsoft.com/pricing/free-trial/)

### <a name="can-i-pause-an-azure-ad-domain-services-managed-domain"></a>Czy mogę wstrzymać domenę zarządzaną usług domenowych usługi AD azure?
Nie. Po włączeniu domeny zarządzanej usług AD AD usługa Azure, usługa jest dostępna w wybranej sieci wirtualnej, dopóki nie usuniesz domeny zarządzanej. Nie ma sposobu, aby wstrzymać usługę. Rozliczenia są kontynuowane co godzinę do momentu usunięcia domeny zarządzanej.

### <a name="can-i-failover-azure-ad-domain-services-to-another-region-for-a-dr-event"></a>Czy mogę przewinąć w błąd usługi domenowe usługi azure ad do innego regionu dla zdarzenia odzyskiwania po awarii?
Nie. Usługi domenowe usługi Azure AD nie zapewniają obecnie modelu wdrażania geograficznie nadmiarowego. Jest ograniczona do jednej sieci wirtualnej w regionie platformy Azure. Jeśli chcesz korzystać z wielu regionów platformy Azure, musisz uruchomić kontrolery domeny usługi Active Directory na maszynach wirtualnych usługi Azure IaaS. Aby uzyskać wskazówki dotyczące architektury, zobacz [Rozszerzanie lokalnej domeny usługi Active Directory na platformę Azure](https://docs.microsoft.com/azure/architecture/reference-architectures/identity/adds-extend-domain).

### <a name="can-i-get-azure-ad-domain-services-as-part-of-enterprise-mobility-suite-ems-do-i-need-azure-ad-premium-to-use-azure-ad-domain-services"></a>Czy mogę uzyskać usługi domenowe usługi Azure AD w ramach pakietu Enterprise Mobility Suite (EMS)? Czy do korzystania z usług domenowych usługi Azure AD DOMAIN jest potrzebna usługa Azure AD Premium?
Nie. Usługi domenowe usługi Azure AD jest płatną usługą platformy Azure i nie jest częścią usługi EMS. Usługi domenowe usługi Azure AD mogą być używane ze wszystkimi wersjami usługi Azure AD (bezpłatne i premium). Opłaty są naliczane co godzinę, w zależności od użycia.

### <a name="what-azure-regions-is-the-service-available-in"></a>W jakich regionach platformy Azure jest dostępna usługa?
Zapoznaj się ze stroną [Usługi platformy Azure według regionów,](https://azure.microsoft.com/regions/#services/) aby wyświetlić listę regionów platformy Azure, w których są dostępne usługi domenowe usługi Azure AD.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Zapoznaj się z [przewodnikiem rozwiązywania problemów,](troubleshoot.md) aby dorównać typowym problemom związanym z konfigurowaniem usług domenowych usługi Azure AD i administrowaniem nimi.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o usługach domenowych usługi Azure AD, zobacz [Co to są Usługi domenowe Usługi domenowe Active Directory platformy Azure?](overview.md).

Aby rozpocząć, zobacz [Tworzenie i konfigurowanie wystąpienia usług domenowych Active Directory platformy Azure](tutorial-create-instance.md).
