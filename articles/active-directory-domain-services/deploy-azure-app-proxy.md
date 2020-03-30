---
title: Wdrażanie serwera proxy aplikacji usługi Azure AD dla usług domenowych usługi Azure AD | Dokumenty firmy Microsoft
description: Dowiedz się, jak zapewnić bezpieczny dostęp do aplikacji wewnętrznych dla pracowników zdalnych, wdrażając i konfigurując serwer proxy aplikacji usługi Azure Active Directory w domenie zarządzanej usług domenowych Usługi domenowe Active Directory azure
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 11/6/2019
ms.author: iainfou
ms.openlocfilehash: c6e4e6a45fbbeab64184d8ae4b0684ba055d7735
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77613988"
---
# <a name="deploy-azure-ad-application-proxy-for-secure-access-to-internal-applications-in-an-azure-ad-domain-services-managed-domain"></a>Wdrażanie serwera proxy aplikacji usługi Azure AD w celu bezpiecznego dostępu do aplikacji wewnętrznych w domenie zarządzanej usług ad.in.

Dzięki usługom domeny usługi Azure AD (Azure AD DS) można przenosić starsze aplikacje działające lokalnie na platformę Azure. Następnie serwer proxy aplikacji usługi Azure Active Directory (AD) pomaga obsługiwać pracowników zdalnych, bezpiecznie publikując te aplikacje wewnętrzne w domenie zarządzanej usług Azure AD DS, aby były dostępne przez Internet.

Jeśli jesteś nowym serwerem proxy aplikacji usługi Azure AD i chcesz dowiedzieć się więcej, zobacz [Jak zapewnić bezpieczny dostęp zdalny do aplikacji wewnętrznych.](../active-directory/manage-apps/application-proxy.md)

W tym artykule pokazano, jak utworzyć i skonfigurować łącznik serwera proxy aplikacji usługi Azure AD, aby zapewnić bezpieczny dostęp do aplikacji w domenie zarządzanej usług Azure AD DS.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Przed rozpoczęciem

Aby ukończyć ten artykuł, potrzebne są następujące zasoby i uprawnienia:

* Aktywna subskrypcja platformy Azure.
    * Jeśli nie masz subskrypcji platformy Azure, [utwórz konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Dzierżawa usługi Azure Active Directory skojarzona z subskrypcją, zsynchronizowana z katalogiem lokalnym lub katalogiem tylko w chmurze.
    * W razie potrzeby [utwórz dzierżawę usługi Azure Active Directory][create-azure-ad-tenant] lub [skojarz subskrypcję platformy Azure z kontem.][associate-azure-ad-tenant]
    * Licencja **usługi Azure AD Premium** jest wymagana do korzystania z serwera proxy aplikacji usługi Azure AD.
* Domena zarządzana usługami domenowymi Usługi Active Directory platformy Azure włączona i skonfigurowana w dzierżawie usługi Azure AD.
    * W razie potrzeby [utwórz i skonfiguruj wystąpienie usług domenowych Active Directory platformy Azure][create-azure-ad-ds-instance].

## <a name="create-a-domain-joined-windows-vm"></a>Tworzenie maszyny Wirtualnej systemu Windows przyłączonych do domeny

Aby kierować ruch do aplikacji uruchomionych w twoim środowisku, należy zainstalować składnik łącznika serwera proxy aplikacji usługi Azure AD. Ten łącznik serwera proxy aplikacji usługi Azure AD musi być zainstalowany na maszynach wirtualnych systemu Windows Server, które są przyłączone do domeny zarządzanej usług Azure AD DS. W przypadku niektórych aplikacji można wdrożyć wiele serwerów, z których każdy ma zainstalowany łącznik. Ta opcja wdrażania zapewnia większą dostępność i pomaga obsłużyć obciążenia uwierzytelniania.

Maszyna wirtualna, która uruchamia łącznik serwera proxy aplikacji usługi Azure AD musi być na tej samej lub równorzędnej sieci wirtualnej, w której włączono usługi Azure AD DS. Maszyny wirtualne, które następnie hostują aplikacje, które publikujesz przy użyciu serwera proxy aplikacji, muszą być również wdrożone w tej samej sieci wirtualnej platformy Azure.

Aby utworzyć maszynę wirtualną dla łącznika serwera proxy aplikacji usługi Azure AD, wykonaj następujące kroki:

1. [Utwórz niestandardową instalację organizacyjną](create-ou.md). Uprawnienia do zarządzania tą niestandardową jednostką organizacyjną można delegować użytkownikom w domenie zarządzanej usług Azure AD DS. Maszyny wirtualne dla serwera proxy aplikacji usługi Azure AD i które uruchamiają aplikacje, muszą być częścią niestandardowej instalacji organizacyjnej, a nie domyślną ojówą *organizacyjną komputerów kontrolera domeny usługi AAD.*
1. [Dołącz maszyny wirtualne][create-join-windows-vm]do domeny , zarówno ten, który uruchamia łącznik serwera proxy aplikacji usługi Azure AD, jak i te, które uruchamiają aplikacje, do domeny zarządzanej usługi Azure AD DS. Utwórz te konta komputera w niestandardowej instalacji organizacyjnej z poprzedniego kroku.

## <a name="download-the-azure-ad-application-proxy-connector"></a>Pobieranie łącznika serwera proxy aplikacji usługi Azure AD

Wykonaj następujące kroki, aby pobrać łącznik serwera proxy aplikacji usługi Azure AD. Pobrany plik instalacyjny zostanie skopiowany do maszyny Wirtualnej serwera proxy aplikacji w następnej sekcji.

1. Zaloguj się do [witryny Azure Portal](https://portal.azure.com) przy za pomocą konta użytkownika, które ma uprawnienia *administratora przedsiębiorstwa* w usłudze Azure AD.
1. Wyszukaj i wybierz **pozycję Azure Active Directory** u góry portalu, a następnie wybierz pozycję **Aplikacje przedsiębiorstwa**.
1. Wybierz **pozycję Proxy aplikacji** z menu po lewej stronie. Aby utworzyć pierwszy łącznik i włączyć serwer proxy aplikacji, wybierz **łącze,** aby pobrać łącznik .
1. Na stronie pobierania zaakceptuj postanowienia licencyjne i umowy o ochronie prywatności, a następnie wybierz pozycję **Akceptuj warunki & Pobierz**.

    ![Pobieranie łącznika serwera proxy aplikacji usługi Azure AD](./media/app-proxy/download-app-proxy-connector.png)

## <a name="install-and-register-the-azure-ad-application-proxy-connector"></a>Instalowanie i rejestrowanie łącznika serwera proxy aplikacji usługi Azure AD

Z maszyną wirtualną gotowy do użycia jako łącznika serwera proxy aplikacji usługi Azure AD, teraz skopiować i uruchomić plik instalacyjny pobrany z witryny Azure portal.

1. Skopiuj plik instalacyjny łącznika serwera proxy aplikacji usługi Azure AD na maszynę wirtualną.
1. Uruchom plik instalacyjny, taki jak *AADApplicationProxyConnectorInstaller.exe*. Zaakceptuj postanowienia licencyjne dotyczące oprogramowania.
1. Podczas instalacji zostanie wyświetlony monit o zarejestrowanie łącznika za pomocą serwera proxy aplikacji w katalogu usługi Azure AD.
   * Podaj poświadczenia dla administratora globalnego w katalogu usługi Azure AD. Poświadczenia administratora globalnego usługi Azure AD mogą różnić się od poświadczeń platformy Azure w portalu

        > [!NOTE]
        > Konto administratora globalnego używane do rejestrowania łącznika musi należeć do tego samego katalogu, w którym jest włączana usługa Proxy aplikacji.
        >
        > Na przykład jeśli domena *aaddscontoso.com*usługi Azure AD jest aaddscontoso.com `admin@aaddscontoso.com` , administrator globalny powinien być lub inny prawidłowy alias w tej domenie.

   * Jeśli konfiguracja zwiększonych zabezpieczeń programu Internet Explorer jest włączona dla maszyny Wirtualnej, na której zainstalowano łącznik, ekran rejestracji może zostać zablokowany. Aby zezwolić na dostęp, postępuj zgodnie z instrukcjami zawartymi w komunikacie o błędzie lub wyłącz rozszerzone zabezpieczenia programu Internet Explorer podczas procesu instalacji.
   * Jeśli rejestracja łącznika nie powiedzie się, zobacz [Rozwiązywanie problemów z serwerem proxy aplikacji](../active-directory/manage-apps/application-proxy-troubleshoot.md).
1. Po zakończeniu konfiguracji jest wyświetlana notatka dla środowisk z wychodzącym serwerem proxy. Aby skonfigurować łącznik serwera proxy aplikacji usługi Azure AD do pracy za `C:\Program Files\Microsoft AAD App Proxy connector\ConfigureOutBoundProxy.ps1`pośrednictwem wychodzącego serwera proxy, uruchom dostarczony skrypt, taki jak .
1. Na stronie serwera proxy aplikacji w witrynie Azure portal nowy łącznik jest wyświetlany ze stanem *Active*, jak pokazano w poniższym przykładzie:

    ![Nowy łącznik serwera proxy aplikacji usługi Azure AD wyświetlany jako aktywny w witrynie Azure portal](./media/app-proxy/connected-app-proxy.png)

> [!NOTE]
> Aby zapewnić wysoką dostępność aplikacji uwierzytelniających się za pośrednictwem serwera proxy aplikacji usługi Azure AD, można zainstalować łączniki na wielu maszynach wirtualnych. Powtórz te same kroki wymienione w poprzedniej sekcji, aby zainstalować łącznik na innych serwerach przyłączonych do domeny zarządzanej usług Azure AD DS.

## <a name="enable-resource-based-kerberos-constrained-delegation"></a>Włączanie delegowania ograniczonego protokołu Kerberos opartego na zasobach

Jeśli chcesz używać logowania jednokrotnego do aplikacji przy użyciu zintegrowanego uwierzytelniania systemu Windows (IWA), przyznaj łącznikom serwera proxy aplikacji usługi Azure AD uprawnienia do personifikacji użytkowników i wysyłania i odbierania tokenów w ich imieniu. Aby udzielić tych uprawnień, należy skonfigurować delegowanie ograniczone kerberos (KCD) dla łącznika, aby uzyskać dostęp do zasobów w domenie zarządzanej usług Azure AD DS. Ponieważ nie masz uprawnień administratora domeny w domenie zarządzanej usług Azure AD DS, nie można skonfigurować tradycyjnego kcd na poziomie konta w domenie zarządzanej. Zamiast tego należy użyć kcd opartych na zasobach.

Aby uzyskać więcej informacji, zobacz [Konfigurowanie delegowania ograniczonego protokołu Kerberos (KCD) w Usługach domenowych Active Directory platformy Azure](deploy-kcd.md).

> [!NOTE]
> Aby uruchomić następujące polecenia cmdlet programu PowerShell, musisz zalogować się do konta użytkownika, które jest członkiem grupy *administratorów usługi Azure AD DC* w dzierżawie usługi Azure AD.
>
> Konta komputera dla łącznika łącznika serwera proxy aplikacji i maszyn wirtualnych aplikacji muszą znajdować się w niestandardowej instalacji organizacyjnej, w której masz uprawnienia do konfigurowania kcd opartego na zasobach. Nie można skonfigurować kcd opartych na zasobach dla konta komputera we wbudowanym kontenerze *komputerów kontrolera domeny usługi AAD.*

Użyj [Get-ADComputer,][Get-ADComputer] aby pobrać ustawienia dla komputera, na którym jest zainstalowany łącznik serwera proxy aplikacji usługi Azure AD. Na maszynie wirtualnej do zarządzania przyłączony do domeny i zalogowany jako konto użytkownika, który jest członkiem grupy *administratorów usługi Azure AD DC,* uruchom następujące polecenia cmdlet.

W poniższym przykładzie pobiera się informacje o koncie komputera o nazwie *appproxy.aaddscontoso.com*. Podaj własną nazwę komputera dla maszyny Wirtualnej serwera proxy aplikacji usługi Azure AD skonfigurowany w poprzednich krokach.

```powershell
$ImpersonatingAccount = Get-ADComputer -Identity appproxy.aaddscontoso.com
```

Dla każdego serwera aplikacji, który uruchamia aplikacje za usługi Azure AD Application Proxy użyj polecenia cmdlet Programu PowerShell [set-ADComputer][Set-ADComputer] do konfigurowania kcd opartych na zasobach. W poniższym przykładzie łącznik serwera proxy aplikacji usługi Azure AD ma uprawnienia do używania *appserver.aaddscontoso.com* komputera:

```powershell
Set-ADComputer appserver.aaddscontoso.com -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
```

W przypadku wdrażania wielu łączników serwera proxy aplikacji usługi Azure AD należy skonfigurować kcd oparte na zasobach dla każdego wystąpienia łącznika.

## <a name="next-steps"></a>Następne kroki

Dzięki serwerowi proxy aplikacji usługi Azure AD zintegrowanemu z usługą Azure AD DS publikuj aplikacje dla użytkowników, aby uzyskać do nich dostęp. Aby uzyskać więcej informacji, zobacz [publikowanie aplikacji przy użyciu serwera proxy aplikacji usługi Azure AD](../active-directory/manage-apps/application-proxy-publish-azure-portal.md).

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[azure-bastion]: ../bastion/bastion-create-host-portal.md
[Get-ADComputer]: /powershell/module/addsadministration/get-adcomputer
[Set-ADComputer]: /powershell/module/addsadministration/set-adcomputer
