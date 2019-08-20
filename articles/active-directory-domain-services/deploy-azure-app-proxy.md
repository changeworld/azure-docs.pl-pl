---
title: 'Azure Active Directory Domain Services: Wdrażanie usługi Azure serwer proxy aplikacji usługi Azure AD | Microsoft Docs'
description: Korzystanie z usługi Azure serwer proxy aplikacji usługi Azure AD w Azure Active Directory Domain Services domenach zarządzanych
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
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
ms.author: iainfou
ms.openlocfilehash: 21693926bae681cf15d31dca06344dfa5d865e3b
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2019
ms.locfileid: "69613024"
---
# <a name="deploy-azure-ad-application-proxy-on-an-azure-ad-domain-services-managed-domain"></a>Wdrażanie usługi Azure serwer proxy aplikacji usługi Azure AD w domenie zarządzanej Azure AD Domain Services
Serwer proxy aplikacji Azure Active Directory (AD) pomaga zapewnić obsługę zdalnych procesów roboczych przez publikowanie aplikacji lokalnych w celu uzyskania dostępu przez Internet. Dzięki Azure AD Domain Services możesz teraz podnieśać i przeshift starsze aplikacje działające lokalnie do usług infrastruktury platformy Azure. Następnie można opublikować te aplikacje za pomocą serwer proxy aplikacji usługi Azure AD platformy Azure, aby zapewnić bezpieczny dostęp zdalny użytkownikom w organizacji.

Jeśli jesteś nowym elementem serwer proxy aplikacji usługi Azure AD platformy Azure, Dowiedz się więcej o tej funkcji w następującym artykule: [Jak zapewnić bezpieczny dostęp zdalny do aplikacji lokalnych](../active-directory/manage-apps/application-proxy.md).

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Przed rozpoczęciem
Aby wykonać zadania wymienione w tym artykule, potrzebne są:

1. Prawidłowa **subskrypcja platformy Azure**.
2. **Katalog usługi Azure AD** — zsynchronizowany z katalogiem lokalnym lub katalogiem w chmurze.
3. Do korzystania z usługi Azure serwer proxy aplikacji usługi Azure AD jest wymagana **licencja Azure AD — wersja Premium** .
4. Należy włączyć **Azure AD Domain Services** dla katalogu usługi Azure AD. Jeśli nie zostało to zrobione, postępuj zgodnie ze wszystkimi zadaniami opisanymi w [przewodniku wprowadzenie](tutorial-create-instance.md).

<br>

## <a name="task-1---enable-azure-ad-application-proxy-for-your-azure-ad-directory"></a>Zadanie 1 — Włączanie usługi Azure serwer proxy aplikacji usługi Azure AD dla katalogu usługi Azure AD
Wykonaj następujące kroki, aby włączyć serwer proxy aplikacji usługi Azure AD platformy Azure dla katalogu usługi Azure AD.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) jako administrator.

2. Kliknij **Azure Active Directory** , aby wyświetlić przegląd katalogu. Kliknij pozycję **aplikacje dla przedsiębiorstw**.

3. Kliknij pozycję **serwer proxy aplikacji**.

4. Aby pobrać łącznik, kliknij przycisk **Łącznik** .

5. Na stronie pobierania zaakceptuj postanowienia licencyjne i umowę o ochronie prywatności, a następnie kliknij przycisk **Pobierz** .

    ![Potwierdź pobieranie](./media/app-proxy/app-proxy-enabled-confirm-download.png)


## <a name="task-2---provision-domain-joined-windows-servers-to-deploy-the-azure-ad-application-proxy-connector"></a>Zadanie 2 — Inicjowanie obsługi administracyjnej serwerów z systemem Windows przyłączonych do domeny w celu wdrożenia łącznika usługi Azure serwer proxy aplikacji usługi Azure AD
Potrzebne są przyłączone do domeny maszyny wirtualne z systemem Windows Server, na których można zainstalować łącznik usługi Azure serwer proxy aplikacji usługi Azure AD. W przypadku niektórych aplikacji można zainicjować obsługę administracyjną wielu serwerów, na których jest zainstalowany łącznik. Ta opcja wdrażania zapewnia większą dostępność i pomaga w obsłudze cięższych obciążeń uwierzytelniania.

Udostępnianie serwerów łączników w tej samej sieci wirtualnej (lub połączonej/równorzędnej sieci wirtualnej), w której włączono domenę zarządzaną Azure AD Domain Services. Podobnie serwery obsługujące aplikacje publikowane za pośrednictwem serwera proxy aplikacji muszą być zainstalowane w tej samej sieci wirtualnej platformy Azure.

Aby udostępnić serwery łączników, wykonaj czynności opisane w artykule zatytułowanym [Dołącz maszynę wirtualną z systemem Windows do domeny zarządzanej](active-directory-ds-admin-guide-join-windows-vm.md).


## <a name="task-3---install-and-register-the-azure-ad-application-proxy-connector"></a>Zadanie 3 — Instalowanie i rejestrowanie łącznika usługi Azure serwer proxy aplikacji usługi Azure AD
Wcześniej zainicjowano już maszynę wirtualną z systemem Windows Server i przyłączono ją do domeny zarządzanej. To zadanie służy do instalowania łącznika usługi Azure serwer proxy aplikacji usługi Azure AD na tej maszynie wirtualnej.

1. Skopiuj pakiet instalacyjny łącznika do maszyny wirtualnej, na której jest instalowany łącznik serwera proxy aplikacji sieci Web usługi Azure AD.

2. Uruchom **plik aadapplicationproxyconnectorinstaller. exe** na maszynie wirtualnej. Zaakceptuj postanowienia licencyjne dotyczące oprogramowania.

    ![Zaakceptuj warunki instalacji](./media/app-proxy/app-proxy-install-connector-terms.png)
3. Podczas instalacji zostanie wyświetlony monit o zarejestrowanie łącznika przy użyciu serwera proxy aplikacji w katalogu usługi Azure AD.
   * Podaj **poświadczenia administratora aplikacji usługi Azure AD**. Dzierżawa administratora aplikacji może się różnić od poświadczeń Microsoft Azure.
   * Konto administratora użyte do zarejestrowania łącznika musi należeć do tego samego katalogu, w którym włączono usługę serwera proxy aplikacji. Na przykład jeśli domena dzierżawy to contoso.com, administrator powinien mieć lub mieć admin@contoso.com dowolny prawidłowy alias w tej domenie.
   * Jeśli konfiguracja zwiększonych zabezpieczeń programu Internet Explorer jest włączona dla serwera, na którym jest instalowany łącznik, ekran rejestracji może zostać zablokowany. Aby zezwolić na dostęp, postępuj zgodnie z instrukcjami wyświetlanymi w komunikacie o błędzie. Upewnij się, że pozycja Konfiguracja zwiększonych zabezpieczeń programu Internet Explorer jest wyłączona.
   * Jeśli rejestracja łącznika nie powiedzie się, zobacz [Troubleshoot Application Proxy](../active-directory/manage-apps/application-proxy-troubleshoot.md) (Rozwiązywanie problemów z serwerem proxy aplikacji).

     ![Łącznik został zainstalowany](./media/app-proxy/app-proxy-connector-installed.png)
4. Aby upewnić się, że łącznik działa prawidłowo, uruchom narzędzie do rozwiązywania problemów z łącznikiem serwer proxy aplikacji usługi Azure AD platformy Azure. Po uruchomieniu narzędzia do rozwiązywania problemów powinien zostać wyświetlony raport o powodzeniu.

    ![Powodzenie narzędzia do rozwiązywania problemów](./media/app-proxy/app-proxy-connector-troubleshooter.png)
5. Nowo zainstalowany łącznik powinien być widoczny na liście na stronie serwer proxy aplikacji w katalogu usługi Azure AD.

    ![W Azure Portal zainstalowany łącznik jest wyświetlany jako dostępny](./media/app-proxy/app-proxy-connector-page.png)

> [!NOTE]
> Można zainstalować łączniki na wielu serwerach w celu zagwarantowania wysokiej dostępności aplikacji publikowanych za pomocą serwer proxy aplikacji usługi Azure AD platformy Azure. Wykonaj te same kroki, aby zainstalować łącznik na innych serwerach przyłączonych do domeny zarządzanej.
>
>

## <a name="next-steps"></a>Następne kroki
Skonfigurowano serwer proxy aplikacji usługi Azure AD platformy Azure i zintegrowano ją z domeną zarządzaną Azure AD Domain Services.

* **Migrowanie aplikacji do usługi Azure Virtual Machines:** Można podłączać i przełączać aplikacje z serwerów lokalnych do maszyn wirtualnych platformy Azure dołączonych do domeny zarządzanej. Dzięki temu możesz pozbyć się kosztów związanych z infrastrukturą uruchomionych serwerów lokalnie.

* **Publikowanie aplikacji przy użyciu usługi Azure serwer proxy aplikacji usługi Azure AD:** Publikowanie aplikacji uruchomionych na maszynach wirtualnych platformy Azure przy użyciu usługi Azure serwer proxy aplikacji usługi Azure AD. Aby uzyskać więcej informacji, zobacz [publikowanie aplikacji przy użyciu usługi Azure serwer proxy aplikacji usługi Azure AD](../active-directory/manage-apps/application-proxy-publish-azure-portal.md)


## <a name="deployment-note---publish-iwa-integrated-windows-authentication-applications-using-azure-ad-application-proxy"></a>Uwagi dotyczące wdrażania — publikowanie aplikacji IWA (zintegrowane uwierzytelnianie systemu Windows) przy użyciu usługi Azure serwer proxy aplikacji usługi Azure AD
Włącz logowanie jednokrotne do aplikacji przy użyciu zintegrowanego uwierzytelniania systemu Windows (IWA) przez przyznanie łączników serwera proxy aplikacji uprawnienia do personifikacji użytkowników oraz wysyłania i odbierania tokenów w ich imieniu. Skonfiguruj ograniczone delegowanie protokołu Kerberos (KCD) dla łącznika, aby przyznać wymagane uprawnienia dostępu do zasobów w domenie zarządzanej. Użyj mechanizmu KCD opartego na zasobach w domenach zarządzanych, aby zwiększyć bezpieczeństwo.


### <a name="enable-resource-based-kerberos-constrained-delegation-for-the-azure-ad-application-proxy-connector"></a>Włączanie ograniczonego delegowania protokołu Kerberos na podstawie zasobów dla łącznika usługi Azure serwer proxy aplikacji usługi Azure AD
Łącznik serwera proxy aplikacji platformy Azure powinien zostać skonfigurowany do ograniczonego delegowania protokołu Kerberos (KCD), dzięki czemu może personifikować użytkowników w domenie zarządzanej. W domenie zarządzanej Azure AD Domain Services nie masz uprawnień administratora domeny. W związku z tym **tradycyjne KCD na poziomie konta nie można skonfigurować w domenie zarządzanej**.

Użyj KCD opartego na zasobach zgodnie z opisem w tym [artykule](deploy-kcd.md).

> [!NOTE]
> Aby administrować domeną zarządzaną za pomocą poleceń cmdlet programu PowerShell, musisz być członkiem grupy administratorów domeny usługi AAD.
>
>

Użyj polecenia cmdlet programu PowerShell Get-ADComputer, aby pobrać ustawienia dla komputera, na którym zainstalowano łącznik usługi Azure serwer proxy aplikacji usługi Azure AD.
```powershell
$ConnectorComputerAccount = Get-ADComputer -Identity contoso-proxy.contoso.com
```

Następnie użyj polecenia cmdlet Set-ADComputer w celu skonfigurowania KCD opartego na zasobach dla serwera zasobów.
```powershell
Set-ADComputer contoso-resource.contoso.com -PrincipalsAllowedToDelegateToAccount $ConnectorComputerAccount
```

Jeśli wdrożono wiele łączników serwera proxy aplikacji w domenie zarządzanej, należy skonfigurować KCD oparte na zasobach dla każdego takiego wystąpienia łącznika.


## <a name="related-content"></a>Powiązana zawartość
* [Przewodnik po Wprowadzenie Azure AD Domain Services](tutorial-create-instance.md)
* [Konfigurowanie ograniczonego delegowania protokołu Kerberos w domenie zarządzanej](deploy-kcd.md)
* [Omówienie ograniczonego delegowania protokołu Kerberos](https://technet.microsoft.com/library/jj553400.aspx)
