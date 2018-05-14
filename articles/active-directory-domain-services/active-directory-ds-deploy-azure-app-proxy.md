---
title: 'Usługi domenowe w usłudze Azure Active Directory: Wdrażanie serwera Proxy aplikacji usługi Azure Active Directory | Dokumentacja firmy Microsoft'
description: Użyj serwera Proxy aplikacji usługi Azure AD w domenach zarządzanych usług domenowych Azure Active Directory
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: maheshu
ms.openlocfilehash: eee20271c27f596ee6aefd0b7280c7862c53b289
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/11/2018
---
# <a name="deploy-azure-ad-application-proxy-on-an-azure-ad-domain-services-managed-domain"></a>Wdrażanie aplikacji serwera Proxy Azure AD w domenie zarządzanej usług domenowych Azure AD
Serwer Proxy aplikacji usługi Azure Active Directory (AD) pomaga obsługuje pracowników zdalnych przez publikowania lokalnych aplikacji można uzyskać dostęp za pośrednictwem Internetu. Z usług domenowych Azure AD możesz teraz przyrostu i shift starsze aplikacje uruchomione w siedzibie firmy do usługi infrastruktury platformy Azure. Następnie można opublikować te aplikacje przy użyciu usługi Azure AD serwera Proxy aplikacji, co zapewnia bezpieczny dostęp zdalny do użytkowników w organizacji.

Jeśli jesteś nowym użytkownikiem serwer Proxy aplikacji usługi Azure AD, więcej informacji na temat tej funkcji w programie artykule: [jak zapewnić bezpieczny zdalny dostęp do aplikacji lokalnych](../active-directory/manage-apps/application-proxy.md).


## <a name="before-you-begin"></a>Przed rozpoczęciem
Aby wykonać zadania opisane w tym artykule, należy:

1. Prawidłowy **subskrypcji platformy Azure**.
2. **Katalog usługi Azure AD** -albo synchronizowane z katalogu lokalnego lub w katalogu tylko w chmurze.
3. **Licencji Azure AD podstawowa lub Premium** musisz wybrać serwer Proxy aplikacji usługi Azure AD.
4. **Usługi domenowe Azure AD** musi być włączona dla katalogu usługi Azure AD. Jeśli nie zostało to jeszcze zrobione, należy wykonać wszystkie zadania opisane w temacie [Przewodnik wprowadzający](active-directory-ds-getting-started.md).

<br>

## <a name="task-1---enable-azure-ad-application-proxy-for-your-azure-ad-directory"></a>Zadanie 1 - serwer Proxy aplikacji włączania usługi Azure AD dla katalogu usługi Azure AD
Wykonaj poniższe kroki, aby włączyć serwer Proxy aplikacji usługi AD platformy Azure dla katalogu usługi Azure AD.

1. Zaloguj się jako administrator w [portalu Azure](http://portal.azure.com).

2. Kliknij przycisk **usługi Azure Active Directory** można wyświetlić informacje o katalogu. Kliknij przycisk **aplikacje dla przedsiębiorstw**.

    ![Wybierz katalog usługi Azure AD](./media/app-proxy/app-proxy-enable-start.png)
3. Kliknij przycisk **serwera proxy aplikacji**. Jeśli nie masz subskrypcji usługi Azure AD podstawowa lub Azure AD Premium, zobaczysz opcję, aby włączyć korzystania z wersji próbnej. Przełącz **Włączanie serwera Proxy aplikacji?** do **włączyć** i kliknij przycisk **zapisać**.

    ![Włącz serwer Proxy aplikacji](./media/app-proxy/app-proxy-enable-proxy-blade.png)
4. Aby pobrać łącznik, kliknij przycisk **łącznik** przycisku.

    ![Pobierz łącznik](./media/app-proxy/app-proxy-enabled-download-connector.png)
5. Na stronie pobierania, zaakceptuj postanowienia licencyjne i zasady ochrony prywatności umowy i kliknij przycisk **Pobierz** przycisku.

    ![Potwierdź pobierania](./media/app-proxy/app-proxy-enabled-confirm-download.png)


## <a name="task-2---provision-domain-joined-windows-servers-to-deploy-the-azure-ad-application-proxy-connector"></a>Zadanie 2 - Provision przyłączonych do domeny serwery z systemem Windows do wdrożenia serwera Proxy aplikacji usługi Azure AD connector
Wymagane są przyłączone do domeny systemu Windows Server maszyny wirtualne na których można zainstalować łącznik serwera Proxy aplikacji usługi Azure AD. Dla pewnych aplikacji możesz udostępnić wielu serwerów, na których jest zainstalowany łącznik. Ta opcja wdrażania zapewnia większą dostępność i pomaga obsługi większych obciążeń uwierzytelniania.

Udostępnić serwery łącznika w tej samej sieci wirtualnej (lub połączony/połączyć za pomocą sieci wirtualnej), w których włączono domeny zarządzanej usług domenowych Azure AD. Podobnie serwery obsługujące aplikacje, które można opublikować za pośrednictwem serwera Proxy aplikacji muszą być zainstalowane w tej samej sieci wirtualnej platformy Azure.

Aby udostępnić serwery łącznika, należy wykonać zadania opisane w artykule zatytułowany [Dołącz maszynę wirtualną systemu Windows do domeny zarządzanej](active-directory-ds-admin-guide-join-windows-vm.md).


## <a name="task-3---install-and-register-the-azure-ad-application-proxy-connector"></a>Zadanie 3 — Instalowanie i rejestrowanie łącznika serwera Proxy aplikacji Azure AD
Poprzednio udostępnione maszynę wirtualną systemu Windows Server i do domeny zarządzanej. To zadanie należy zainstalować łącznik serwera Proxy aplikacji usługi Azure AD na tej maszynie wirtualnej.

1. Skopiuj pakiet instalacyjny łącznika do maszyny Wirtualnej, na którym jest instalowany łącznik serwera Proxy aplikacji sieci Web platformy Azure AD.

2. Uruchom **AADApplicationProxyConnectorInstaller.exe** na maszynie wirtualnej. Zaakceptuj postanowienia licencyjne dotyczące oprogramowania.

    ![Zaakceptuj postanowienia instalacji](./media/app-proxy/app-proxy-install-connector-terms.png)
3. Podczas instalacji wyświetlany jest monit o zarejestrowanie łącznika serwera proxy aplikacji z katalogu usługi Azure AD.
    * Podaj Twojej **poświadczenia administratora globalnego usługi Azure AD**. Administrator globalny dzierżawy może mieć inne poświadczenia platformy Microsoft Azure niż Twoje.
    * Konto administratora używane do rejestrowania łącznik muszą należeć do tego samego katalogu, w którym włączono usługę serwera Proxy aplikacji. Na przykład, jeśli domena dzierżawy to contoso.com, administrator powinien mieć admin@contoso.com lub dowolny inny alias prawidłowe w tej domenie.
    * Jeśli Konfiguracja zwiększonych zabezpieczeń programu Internet Explorer jest włączona dla serwera którym jest instalowany łącznik, ekran rejestracji może zostać zablokowany. Aby zezwolić na dostęp, postępuj zgodnie z instrukcjami w komunikacie o błędzie. Upewnij się, że pozycja Konfiguracja zwiększonych zabezpieczeń programu Internet Explorer jest wyłączona.
    * Jeśli rejestracja łącznika nie powiedzie się, zobacz [Troubleshoot Application Proxy](../active-directory/active-directory-application-proxy-troubleshoot.md) (Rozwiązywanie problemów z serwerem proxy aplikacji).

    ![Zainstalowanego łącznika](./media/app-proxy/app-proxy-connector-installed.png)
4. Aby zapewnić łącznik działa prawidłowo, uruchomić rozwiązywania łącznika serwera Proxy aplikacji w usłudze Azure AD. Pomyślne raportu powinna zostać wyświetlona po uruchomieniu narzędzia do rozwiązywania problemów.

    ![Powodzenie narzędzia do rozwiązywania problemów](./media/app-proxy/app-proxy-connector-troubleshooter.png)
5. Powinny pojawić się nowo zainstalowany łącznik na liście na stronie serwer proxy aplikacji w katalogu usługi Azure AD.

    ![](./media/app-proxy/app-proxy-connector-page.png)

> [!NOTE]
> Użytkownik może wybrać opcję instalacji łączników na wielu serwerach w celu zapewnienia wysokiej dostępności w celu uwierzytelniania aplikacji opublikowanych przez serwer Proxy aplikacji usługi Azure AD. Wykonaj te same kroki wymienione powyżej, aby zainstalować łącznik na inne serwery przyłączone do domeny zarządzanej.
>
>

## <a name="next-steps"></a>Następne kroki
Masz skonfigurować serwer Proxy aplikacji usługi Azure AD i zintegrować ją z domeny zarządzanej usług domenowych Azure AD.

* **Migracji aplikacji do maszyn wirtualnych platformy Azure:** można przyrostu i shift aplikacji z serwerów lokalnych maszyn wirtualnych platformy Azure jest przyłączony do domeny zarządzanej. Pomoże pozbyć się kosztów infrastruktury działających serwerów lokalnych.

* **Publikowanie aplikacji przy użyciu serwera Proxy aplikacji usługi Azure AD:** publikowania aplikacji działających na maszynach wirtualnych platformy Azure przy użyciu serwera Proxy aplikacji usługi AD platformy Azure. Aby uzyskać więcej informacji, zobacz [publikowanie aplikacji przy użyciu serwera Proxy aplikacji usługi Azure AD](../active-directory/manage-apps/application-proxy-publish-azure-portal.md)


## <a name="deployment-note---publish-iwa-integrated-windows-authentication-applications-using-azure-ad-application-proxy"></a>Uwaga wdrożenia — aplikacje publikowania IWA (zintegrowane uwierzytelnianie systemu Windows) przy użyciu serwera Proxy aplikacji usługi Azure AD
Włącz logowanie jednokrotne do aplikacji przy użyciu zintegrowanego uwierzytelniania systemu Windows (IWA) przez udzielanie uprawnień łączniki serwera Proxy aplikacji personifikować użytkowników i wysyłać i odbierać tokeny w ich imieniu. Skonfiguruj ograniczone delegowanie protokołu Kerberos (KCD) dla łącznika można udzielić wymaganych uprawnień dostępu do zasobów z domeny zarządzanej. Aby zwiększyć bezpieczeństwo, użyj mechanizmu KCD oparte na zasobach w domenach zarządzanych.


### <a name="enable-resource-based-kerberos-constrained-delegation-for-the-azure-ad-application-proxy-connector"></a>Włącz oparte na zasobach ograniczonego delegowania protokołu Kerberos dla łącznika serwera Proxy aplikacji usługi Azure AD
Łącznik serwera Proxy aplikacji Azure powinien być skonfigurowany ograniczonego delegowania protokołu Kerberos (KCD), dlatego może on personifikować użytkowników dla domeny zarządzanej. W domenie zarządzanej usług domenowych Azure AD nie masz uprawnienia administratora domeny. W związku z tym **tradycyjnych KCD poziomie konta nie można skonfigurować w domenie zarządzanej**.

Użyj KCD oparte na zasobach, zgodnie z opisem w tym [artykułu](active-directory-ds-enable-kcd.md).

> [!NOTE]
> Musisz być członkiem grupy "Administratorzy kontrolera domeny usługi AAD", do administrowania domeny zarządzanej przy użyciu poleceń cmdlet programu PowerShell usługi AD.
>
>

Użyj polecenia cmdlet programu PowerShell Get-ADComputer można pobrać ustawień dla komputera, na którym zainstalowano łącznik serwera Proxy aplikacji usługi Azure AD.
```powershell
$ConnectorComputerAccount = Get-ADComputer -Identity contoso100-proxy.contoso100.com
```

Później za pomocą polecenia cmdlet Set-ADComputer Konfigurowanie oparte na zasobach KCD dla serwera zasobu.
```powershell
Set-ADComputer contoso100-resource.contoso100.com -PrincipalsAllowedToDelegateToAccount $ConnectorComputerAccount
```

Jeśli wiele łączników serwera Proxy aplikacji zostały wdrożone na domeny zarządzanej, należy skonfigurować oparte na zasobach KCD dla każdego wystąpienia łącznika.


## <a name="related-content"></a>Powiązana zawartość
* [Usługi domenowe AD Azure - Przewodnik wprowadzający](active-directory-ds-getting-started.md)
* [Skonfiguruj ograniczone delegowanie protokołu Kerberos w domenie zarządzanej](active-directory-ds-enable-kcd.md)
* [Omówienie delegowania ograniczonego protokołu Kerberos](https://technet.microsoft.com/library/jj553400.aspx)
