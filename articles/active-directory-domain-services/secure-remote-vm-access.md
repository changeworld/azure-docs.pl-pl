---
title: Bezpieczny zdalny dostęp do maszyny Wirtualnej w usługach domenowych usługi Azure AD | Dokumenty firmy Microsoft
description: Dowiedz się, jak zabezpieczyć zdalny dostęp do maszyn wirtualnych przy użyciu serwera zasad sieciowych (NPS) i uwierzytelniania wieloskładnikowego platformy Azure za pomocą wdrożenia usług pulpitu zdalnego w domenie zarządzanej usług domenowych Active Directory platformy Azure.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: iainfou
ms.openlocfilehash: f2a222c6a382fa2c316211e293547780a8778177
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80423147"
---
# <a name="secure-remote-access-to-virtual-machines-in-azure-active-directory-domain-services"></a>Bezpieczny dostęp zdalny do maszyn wirtualnych w usługach domenowych Active Directory platformy Azure

Aby zabezpieczyć zdalny dostęp do maszyn wirtualnych działających w domenie zarządzanej usług domenowych Usługi active directory platformy Azure (Usługi Azure AD DS), można użyć usług pulpitu zdalnego (RDS) i serwera zasad sieciowych (NPS). Usługi Azure AD DS uwierzytelnia użytkowników, którzy żądają dostępu za pośrednictwem środowiska RDS. Aby zwiększyć bezpieczeństwo, można zintegrować uwierzytelnianie wieloskładnikowe platformy Azure, aby zapewnić dodatkowy monit o uwierzytelnienie podczas zdarzeń logowania. Uwierzytelnianie wieloskładnikowe platformy Azure używa rozszerzenia dla serwera NPS, aby zapewnić tę funkcję.

> [!IMPORTANT]
> Zalecanym sposobem bezpiecznego łączenia się z maszynami wirtualnymi w domenie zarządzanej usług Azure AD DS jest użycie usługi Azure Bastion, w pełni zarządzanej przez platformę usługi PaaS, którą aprowizujesz w sieci wirtualnej. Host bastionu zapewnia bezpieczną i bezproblemową łączność protokołu RDP (Remote Desktop Protocol) z maszynami wirtualnymi bezpośrednio w witrynie Azure portal za pomocą protokołu SSL. Po nawiązaniu połączenia za pośrednictwem hosta bastionu maszyny wirtualne nie potrzebują publicznego adresu IP i nie trzeba używać sieciowych grup zabezpieczeń do udostępnienia dostępu do protokołu RDP na porcie TCP 3389.
>
> Zdecydowanie zaleca się używanie usługi Azure Bastion we wszystkich regionach, w których jest obsługiwana. W regionach bez dostępności bastionu platformy Azure wykonaj kroki opisane w tym artykule, dopóki usługa Azure Bastion nie będzie dostępna. Należy się zająć przy przypisywaniu publicznych adresów IP do maszyn wirtualnych przyłączonych do usług Azure AD DS, gdzie dozwolony jest cały przychodzący ruch RDP.
>
> Aby uzyskać więcej informacji, zobacz [Co to jest Bastion platformy Azure?][bastion-overview].

W tym artykule pokazano, jak skonfigurować usługę RDS w usługach Azure AD DS i opcjonalnie użyć rozszerzenia nps uwierzytelniania wieloskładnikowego platformy Azure.

![Omówienie usług pulpitu zdalnego (RDS)](./media/enable-network-policy-server/remote-desktop-services-overview.png)

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten artykuł, potrzebne są następujące zasoby:

* Aktywna subskrypcja platformy Azure.
    * Jeśli nie masz subskrypcji platformy Azure, [utwórz konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Dzierżawa usługi Azure Active Directory skojarzona z subskrypcją, zsynchronizowana z katalogiem lokalnym lub katalogiem tylko w chmurze.
    * W razie potrzeby [utwórz dzierżawę usługi Azure Active Directory][create-azure-ad-tenant] lub [skojarz subskrypcję platformy Azure z kontem.][associate-azure-ad-tenant]
* Domena zarządzana usługami domenowymi Usługi Active Directory platformy Azure włączona i skonfigurowana w dzierżawie usługi Azure AD.
    * W razie potrzeby [utwórz i skonfiguruj wystąpienie usług domenowych Active Directory platformy Azure][create-azure-ad-ds-instance].
* *Podsieć obciążeń* utworzona w sieci wirtualnej usług domenowych Active Directory azure.
    * W razie potrzeby [skonfiguruj sieć wirtualną dla domeny zarządzanej usług domenowych Usługi domenowe Active Directory platformy Azure][configure-azureadds-vnet].
* Konto użytkownika, które jest członkiem grupy *administratorów usługi Azure AD DC* w dzierżawie usługi Azure AD.

## <a name="deploy-and-configure-the-remote-desktop-environment"></a>Wdrażanie i konfigurowanie środowiska pulpitu zdalnego

Aby rozpocząć, utwórz co najmniej dwie maszyny wirtualne platformy Azure z systemem Windows Server 2016 lub Windows Server 2019. Aby zwiększyć nadmiarowość i wysoką dostępność środowiska pulpitu zdalnego (RD), można później dodać i załadować dodatkowe hosty.

Sugerowane wdrożenie usług pulpitu zdalnego obejmuje następujące dwie maszyny wirtualne:

* *RDGVM01* — uruchamia serwer brokera połączeń usług pulpitu zdalnego, serwer dostępu w sieci Web usług pulpitu zdalnego i serwer bramy usług pulpitu zdalnego.
* *RDSHVM01* - Uruchamia serwer hosta sesji usług pulpitu zdalnego.

Upewnij się, że maszyny wirtualne są wdrażane w *podsieci obciążeń* sieci wirtualnej usług Ad DS, a następnie dołącz do maszyn wirtualnych do domeny zarządzanej usługi Azure AD DS. Aby uzyskać więcej informacji, zobacz jak [utworzyć maszynę wirtualną systemu Windows Server i dołączyć ją do domeny zarządzanej usług Azure AD DS][tutorial-create-join-vm].

Wdrożenie środowiska usług pulpitu zdalnego zawiera kilka kroków. Istniejący przewodnik wdrażania usług pulpitu zdalnego może być używany bez żadnych konkretnych zmian do użycia w domenie zarządzanej usług Azure AD DS:

1. Zaloguj się do maszyn wirtualnych utworzonych dla środowiska usług pulpitu zdalnego za pomocą konta należącego do grupy *Administratorzy kontrolera domeny usługi Azure AD,* takiej jak *contosoadmin*.
1. Aby utworzyć i skonfigurować usługi RDS, należy użyć przewodnika [wdrażania istniejącego środowiska pulpitu zdalnego][deploy-remote-desktop]. Rozłóż składniki serwera usług pulpitu zdalnego na maszynach wirtualnych platformy Azure zgodnie z potrzebami.
1. Jeśli chcesz zapewnić dostęp za pomocą przeglądarki internetowej, [skonfiguruj klienta sieci Web pulpitu zdalnego dla użytkowników][rd-web-client].

W przypadku wdrażania usług pulpitu zdalnego w domenie zarządzanej usług Azure AD DS można zarządzać usługą i korzystać z niej w następujący sposób w przypadku lokalnej domeny usług AD DS.

## <a name="deploy-and-configure-nps-and-the-azure-mfa-nps-extension"></a>Wdrażanie i konfigurowanie serwera NPS i rozszerzenia serwera NPS usługi Azure MFA

Jeśli chcesz zwiększyć bezpieczeństwo środowiska logowania użytkownika, możesz opcjonalnie zintegrować środowisko usług pulpitu zdalnego z uwierzytelnianiem wieloskładnikowym platformy Azure. W tej konfiguracji użytkownicy otrzymują dodatkowy monit podczas logowania, aby potwierdzić swoją tożsamość.

Aby zapewnić tę możliwość, w twoim środowisku jest zainstalowany dodatkowy serwer zasad sieciowych (NPS) wraz z rozszerzeniem serwera NPS uwierzytelniania wieloskładnikowego platformy Azure. To rozszerzenie integruje się z usługą Azure AD, aby zażądać i zwrócić stan monitów uwierzytelniania wieloskładnikowego.

Użytkownicy muszą być [zarejestrowani, aby korzystać z uwierzytelniania wieloskładnikowego platformy Azure,][user-mfa-registration]co może wymagać dodatkowych licencji usługi Azure AD.

Aby zintegrować uwierzytelnianie wieloskładnikowe platformy Azure z środowiskiem pulpitu zdalnego usługi Azure AD DS, utwórz serwer NPS i zainstaluj rozszerzenie:

1. Utwórz dodatkową maszynę wirtualną systemu Windows Server 2016 lub 2019, taką jak *NPSVM01*, która jest połączona z *podsiecią obciążeń* w sieci wirtualnej usług Ad DS. Dołącz do maszyny Wirtualnej do domeny zarządzanej usług Azure AD DS.
1. Zaloguj się do maszyny Wirtualnej SERWERA NPS jako konto należące do grupy *Administratorzy kontrolera domeny usługi Azure AD,* takie jak *contosoadmin*.
1. Z **Menedżera serwera**wybierz pozycję Dodaj role i **funkcje**, a następnie zainstaluj rolę *Zasady sieciowe i Usługi dostępu.*
1. Użyj istniejącego artykułu infiguracyjnego, aby [zainstalować i skonfigurować rozszerzenie nps usługi Azure MFA][nps-extension].

Po zainstalowaniu serwera NPS i rozszerzenia nps uwierzytelniania wieloskładnikowego platformy Azure należy wykonać następną sekcję, aby skonfigurować ją do użytku ze środowiskiem usług pulpitu zdalnego.

## <a name="integrate-remote-desktop-gateway-and-azure-multi-factor-authentication"></a>Integracja bramy usług pulpitu zdalnego i uwierzytelniania wieloskładnikowego platformy Azure

Aby zintegrować rozszerzenie usługi Azure Multi-Factor Authentication NPS, użyj istniejącego artykułu insycyjnie, aby [zintegrować infrastrukturę bramy usług pulpitu zdalnego przy użyciu rozszerzenia serwera zasad sieciowych (NPS) i usługi Azure AD.][azure-mfa-nps-integration]

Następujące dodatkowe opcje konfiguracji są potrzebne do integracji z domeną zarządzaną usługą Azure AD DS:

1. Nie [rejestruj serwera NPS w usłudze Active Directory][register-nps-ad]. Ten krok kończy się niepowodzeniem w domenie zarządzanej usług Ad DS platformy Azure.
1. W [kroku 4, aby skonfigurować zasady sieciowe,][create-nps-policy]zaznacz również pole **wyboru Ignoruj właściwości telefonowania konta użytkownika**.
1. Jeśli używasz systemu Windows Server 2019 dla serwera NPS i rozszerzenia nps uwierzytelniania wieloskładnikowego platformy Azure, uruchom następujące polecenie, aby zaktualizować bezpieczny kanał, aby umożliwić serwerowi NPS poprawne komunikowanie się:

    ```powershell
    sc sidtype IAS unrestricted
    ```

Użytkownicy są teraz monitowani o dodatkowy czynnik uwierzytelniania podczas logowania, na przykład wiadomość tekstową lub monit w aplikacji Microsoft Authenticator.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat zwiększania odporności wdrożenia, zobacz [Usługi pulpitu zdalnego — wysoka dostępność][rds-high-availability].

Aby uzyskać więcej informacji na temat zabezpieczania logowania użytkownika, zobacz [Jak to działa: Uwierzytelnianie wieloskładnikowe platformy Azure][concepts-mfa].

<!-- INTERNAL LINKS -->
[bastion-overview]: ../bastion/bastion-overview.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[configure-azureadds-vnet]: tutorial-configure-networking.md
[tutorial-create-join-vm]: join-windows-vm.md
[user-mfa-registration]: ../active-directory/authentication/howto-mfa-nps-extension.md#register-users-for-mfa
[nps-extension]: ../active-directory/authentication/howto-mfa-nps-extension.md
[azure-mfa-nps-integration]: ../active-directory/authentication/howto-mfa-nps-extension-rdg.md
[register-nps-ad]:../active-directory/authentication/howto-mfa-nps-extension-rdg.md#register-server-in-active-directory
[create-nps-policy]: ../active-directory/authentication/howto-mfa-nps-extension-rdg.md#configure-network-policy
[concepts-mfa]: ../active-directory/authentication/concept-mfa-howitworks.md

<!-- EXTERNAL LINKS -->
[deploy-remote-desktop]: https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure
[rd-web-client]: https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-web-client-admin
[rds-high-availability]: https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-plan-high-availability
