---
title: 'Azure Active Directory Domain Services: Wdrażanie serwera Proxy aplikacji usługi Azure Active Directory | Dokumentacja firmy Microsoft'
description: Użyj serwera Proxy aplikacji usługi Azure AD w domenach zarządzanych usługi Azure Active Directory Domain Services
services: active-directory-ds
documentationcenter: ''
author: MikeStephens-MS
manager: daveba
editor: curtand
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/14/2019
ms.author: mstephen
ms.openlocfilehash: 202d33c95742f951b57e68f491f139aef068767c
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2019
ms.locfileid: "66246182"
---
# <a name="deploy-azure-ad-application-proxy-on-an-azure-ad-domain-services-managed-domain"></a>Wdrażanie aplikacji serwera Proxy Azure AD w domenie zarządzanej usług domenowych Azure AD
Serwer Proxy aplikacji usługi Azure Active Directory (AD) pomaga wspierać pracowników zdalnych przez publikowanie lokalnych aplikacji można uzyskać dostęp za pośrednictwem Internetu. Korzystając z usług domenowych Azure AD możesz teraz lift-and-shift starszych aplikacji lokalnych do usług infrastruktury platformy Azure. Następnie można opublikować te aplikacje przy użyciu usługi Azure AD serwera Proxy aplikacji, co zapewnia bezpieczny dostęp zdalny do użytkowników w Twojej organizacji.

Jeśli jesteś nowym użytkownikiem serwera Proxy aplikacji usługi AD systemu Azure, Dowiedz się więcej na temat tej funkcji z następującym artykułem: [Jak zapewnić bezpieczny dostęp zdalny do aplikacji lokalnych](../active-directory/manage-apps/application-proxy.md).

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Przed rozpoczęciem
Aby wykonać zadania opisane w tym artykule, potrzebne są:

1. Nieprawidłowy **subskrypcji platformy Azure**.
2. **Katalog usługi Azure AD** — albo synchronizowane z katalogu lokalnego lub w katalogu tylko w chmurze.
3. **Licencji usługi Azure AD podstawowa lub Premium** jest wymagana do używania serwera Proxy aplikacji usługi AD systemu Azure.
4. **Usługi domenowe Azure AD** musi być włączona dla katalogu usługi Azure AD. Jeśli nie zostało to zrobione, wykonaj wszystkie zadania, które są opisane w temacie [Wprowadzenie — przewodnik](create-instance.md).

<br>

## <a name="task-1---enable-azure-ad-application-proxy-for-your-azure-ad-directory"></a>Zadanie 1. serwer Proxy aplikacji Włączanie usługi Azure AD dla katalogu usługi Azure AD
Wykonaj poniższe kroki, aby umożliwić aplikacji serwera Proxy Azure AD dla katalogu usługi Azure AD.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) jako administrator.

2. Kliknij przycisk **usługi Azure Active Directory** Aby wyświetlić przegląd katalogu. Kliknij przycisk **aplikacje dla przedsiębiorstw**.

    ![Wybierz katalog usługi Azure AD](./media/app-proxy/app-proxy-enable-start.png)
3. Kliknij przycisk **serwera proxy aplikacji**. Jeśli nie masz subskrypcji usługi Azure AD w wersji podstawowa lub Azure AD Premium, zobaczysz opcję, aby włączyć korzystania z wersji próbnej. Przełącz **Włączanie serwera Proxy aplikacji?** do **Włącz** i kliknij przycisk **Zapisz**.

    ![Włączanie serwera proxy aplikacji](./media/app-proxy/app-proxy-enable-proxy-blade.png)
4. Aby pobrać łącznik, kliknij **łącznika** przycisku.

    ![Pobierz łącznik](./media/app-proxy/app-proxy-enabled-download-connector.png)
5. Na stronie pobierania, zaakceptuj postanowienia licencyjne i ochrony prywatności umowę, a następnie kliknij przycisk **Pobierz** przycisku.

    ![Upewnij się, pobierania](./media/app-proxy/app-proxy-enabled-confirm-download.png)


## <a name="task-2---provision-domain-joined-windows-servers-to-deploy-the-azure-ad-application-proxy-connector"></a>Zadanie 2 — Aprowizowanie przyłączone do domeny serwerów Windows dotyczące wdrażania łącznika serwera Proxy aplikacji usługi Azure AD
Konieczne jest przyłączony do domeny systemu Windows Server maszyn wirtualnych na których można zainstalować łącznik serwera Proxy aplikacji usługi Azure AD. W przypadku niektórych aplikacji można aprowizować wiele serwerów, na których zainstalowano łącznik. Ta opcja wdrażania zapewnia większą dostępność i ułatwia obsługę większych obciążeń uwierzytelniania.

Aprowizować serwery łącznika na tej samej sieci wirtualnej (lub połączone równorzędnie sieci wirtualnej), w których włączono Twojej domeny zarządzanej usług domenowych Azure AD. Podobnie serwerów obsługujących aplikacji, którą publikujesz za pośrednictwem serwera Proxy aplikacji muszą być zainstalowane na tej samej sieci wirtualnej platformy Azure.

Aby zainicjować obsługę serwerów łącznika, należy wykonać zadania opisane w artykule o nazwie [Dołącz maszynę wirtualną Windows do domeny zarządzanej](active-directory-ds-admin-guide-join-windows-vm.md).


## <a name="task-3---install-and-register-the-azure-ad-application-proxy-connector"></a>Zadanie 3 — Instalowanie i rejestrowanie łącznik serwera Proxy aplikacji usługi Azure AD
Wcześniej zainicjowano obsługę administracyjną maszyny wirtualnej systemu Windows Server i jego przyłączone do domeny zarządzanej. W tym zadaniu można zainstalować łącznik serwera Proxy aplikacji usługi Azure AD na tej maszynie wirtualnej.

1. Skopiuj pakiet instalacyjny łącznika do maszyny Wirtualnej, na którym jest instalowany łącznik serwera Proxy aplikacji sieci Web programu Azure AD.

2. Uruchom **AADApplicationProxyConnectorInstaller.exe** na maszynie wirtualnej. Zaakceptuj postanowienia licencyjne dotyczące oprogramowania.

    ![Zaakceptuj postanowienia dotyczące instalacji](./media/app-proxy/app-proxy-install-connector-terms.png)
3. Podczas instalacji wyświetlany jest monit o zarejestrowanie łącznika serwera Proxy aplikacji z katalogu usługi Azure AD.
   * Podaj swoje **poświadczenia administratora globalnego usługi Azure AD**. Administrator globalny dzierżawy może mieć inne poświadczenia platformy Microsoft Azure niż Twoje.
   * Konto administratora używane do rejestrowania łącznika musi należeć do tego samego katalogu, w którym włączono usługę serwera Proxy aplikacji. Na przykład, jeśli domena dzierżawy to contoso.com, administratorem powinien być admin@contoso.com lub dowolny prawidłowy alias w tej domenie.
   * Jeśli Konfiguracja zwiększonych zabezpieczeń programu Internet Explorer jest włączona dla serwera gdzie jest instalowany łącznik, ekran rejestracji może zostać zablokowany. Aby zezwolić na dostęp, postępuj zgodnie z instrukcjami w komunikacie o błędzie. Upewnij się, że pozycja Konfiguracja zwiększonych zabezpieczeń programu Internet Explorer jest wyłączona.
   * Jeśli rejestracja łącznika nie powiedzie się, zobacz [Troubleshoot Application Proxy](../active-directory/manage-apps/application-proxy-troubleshoot.md) (Rozwiązywanie problemów z serwerem proxy aplikacji).

     ![Zainstalowanego łącznika](./media/app-proxy/app-proxy-connector-installed.png)
4. Aby upewnić się, łącznik działa prawidłowo, uruchom rozwiązywania łącznika serwera Proxy aplikacji w usłudze Azure AD. Pomyślne raport powinien być widoczny po uruchomieniu narzędzia do rozwiązywania problemów.

    ![Powodzenie narzędzia do rozwiązywania problemów](./media/app-proxy/app-proxy-connector-troubleshooter.png)
5. Powinien zostać wyświetlony nowo zainstalowanego łącznika, na liście na stronie serwer proxy aplikacji w katalogu usługi Azure AD.

    ![](./media/app-proxy/app-proxy-connector-page.png)

> [!NOTE]
> Można zainstalować łączniki na wielu serwerach w celu zapewnienia wysokiej dostępności w celu uwierzytelniania aplikacji opublikowanych przy użyciu serwera Proxy aplikacji usługi AD systemu Azure. Wykonaj te same czynności wymienionych powyżej, aby zainstalować łącznik na inne serwery przyłączone do domeny zarządzanej.
>
>

## <a name="next-steps"></a>Następne kroki
Masz skonfigurować serwer Proxy aplikacji usługi AD systemu Azure i zintegrować je z Twoją domeną zarządzaną usług domenowych Azure AD.

* **Migrowanie aplikacji na maszynach wirtualnych platformy Azure:** Możesz to zrobić lift-and-shift działanie aplikacji na serwerach lokalnych maszyn wirtualnych platformy Azure przyłączone do domeny zarządzanej. Pomoże pozbyć się koszty infrastruktury obsługi serwerów w środowisku lokalnym.

* **Publikowanie aplikacji przy użyciu serwera Proxy aplikacji usługi Azure AD:** Publikowanie aplikacji uruchamianych na maszynach wirtualnych platformy Azure przy użyciu serwera Proxy aplikacji usługi AD systemu Azure. Aby uzyskać więcej informacji, zobacz [publikowania aplikacji przy użyciu serwera Proxy aplikacji usługi Azure AD](../active-directory/manage-apps/application-proxy-publish-azure-portal.md)


## <a name="deployment-note---publish-iwa-integrated-windows-authentication-applications-using-azure-ad-application-proxy"></a>Uwaga wdrożenia — aplikacje publikowania IWA (zintegrowane uwierzytelnianie Windows) przy użyciu serwera Proxy aplikacji usługi Azure AD
Włącz logowanie jednokrotne do aplikacji przy użyciu zintegrowanego Windows Authentication (Zintegrowane), przyznając uprawnienia łączników serwera Proxy aplikacji personifikować użytkowników oraz wysyłać i odbierać tokeny w ich imieniu. Skonfiguruj ograniczone delegowanie protokołu Kerberos (KCD) dla łącznika do udzielenia wymaganych uprawnień dostępu do zasobów w domenie zarządzanej. Użyj mechanizmu opartego na zasobach ograniczonego delegowania protokołu Kerberos w domenach zarządzanych, aby zwiększyć bezpieczeństwo.


### <a name="enable-resource-based-kerberos-constrained-delegation-for-the-azure-ad-application-proxy-connector"></a>Włącz opartego na zasobach ograniczonego delegowania protokołu Kerberos dla łącznika serwera Proxy aplikacji usługi Azure AD
Łącznik serwera Proxy aplikacji platformy Azure należy skonfigurować ograniczonego delegowania protokołu Kerberos (KCD), dzięki czemu może on personifikować użytkowników w domenie zarządzanej. W domenie zarządzanej usług domenowych Azure AD nie masz uprawnienia administratora domeny. W związku z tym **tradycyjnych na poziomie konta ograniczonego delegowania protokołu Kerberos, nie można skonfigurować w domenie zarządzanej**.

Użyj opartego na zasobach ograniczonego delegowania protokołu Kerberos, zgodnie z opisem w tym [artykułu](deploy-kcd.md).

> [!NOTE]
> Musisz być członkiem grupy "Administratorzy usługi AAD DC" administrowanie domeną zarządzaną przy użyciu poleceń cmdlet programu PowerShell usługi AD.
>
>

Użyj polecenia cmdlet programu PowerShell Get-ADComputer można pobrać ustawień dla komputera, na którym zainstalowano łącznik serwera Proxy aplikacji usługi Azure AD.
```powershell
$ConnectorComputerAccount = Get-ADComputer -Identity contoso100-proxy.contoso100.com
```

Użyj polecenia cmdlet Set-ADComputer skonfigurować opartego na zasobach ograniczonego delegowania protokołu Kerberos dla zasobów serwera.
```powershell
Set-ADComputer contoso100-resource.contoso100.com -PrincipalsAllowedToDelegateToAccount $ConnectorComputerAccount
```

Jeśli wdrożono wiele łączników serwera Proxy aplikacji usługi w domenie zarządzanej, należy skonfigurować opartego na zasobach ograniczonego delegowania protokołu Kerberos dla każdego wystąpienia łącznika.


## <a name="related-content"></a>Powiązana zawartość
* [Azure AD Domain Services — wprowadzenie — przewodnik](create-instance.md)
* [Konfigurowanie ograniczonego delegowania protokołu Kerberos w domenie zarządzanej](deploy-kcd.md)
* [Omówienie delegowanie ograniczone protokołu Kerberos](https://technet.microsoft.com/library/jj553400.aspx)
