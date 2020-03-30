---
title: Integracja z serwerem proxy aplikacji usługi AD na serwerze NDES
titleSuffix: Azure Active Directory
description: Wskazówki dotyczące wdrażania serwera proxy aplikacji usługi Azure Active Directory w celu ochrony serwera NDES.
services: active-directory
author: CelesteDG
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/17/2020
ms.author: baselden
ms.reviewer: mimart
ms.openlocfilehash: 4ccd8834671725ace72497391090f81eb197ad6a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77032259"
---
# <a name="integrate-with-azure-ad-application-proxy-on-a-network-device-enrollment-service-ndes-server"></a>Integracja z serwerem proxy aplikacji usługi Azure AD na serwerze usługi rejestracji urządzeń sieciowych (NDES)

Serwer proxy aplikacji usługi Azure Active Directory (AD) umożliwia publikowanie aplikacji w sieci. Te aplikacje są takie jak witryny programu SharePoint, microsoft outlook web app i innych aplikacji sieci web. Zapewnia również bezpieczny dostęp do użytkowników spoza sieci za pośrednictwem platformy Azure.

Jeśli jesteś nowym użytkownikiem serwera proxy aplikacji usługi Azure AD i chcesz dowiedzieć się więcej, zobacz [Dostęp zdalny do aplikacji lokalnych za pośrednictwem serwera proxy aplikacji usługi Azure AD](application-proxy.md).

Usługa Azure AD Application Proxy jest oparta na platformie Azure. Zapewnia ogromną przepustowość sieci i infrastrukturę serwera, co zapewnia lepszą ochronę przed rozproszonymi atakami typu "odmowa usługi" (DDOS) i doskonałą dostępność. Ponadto nie ma potrzeby otwierania zewnętrznych portów zapory w sieci lokalnej i nie jest wymagany żaden serwer DMZ. Cały ruch jest pochodzi z ruchu przychodzącego. Aby uzyskać pełną listę portów wychodzących, zobacz [Samouczek: Dodawanie aplikacji lokalnej do zdalnego dostępu za pośrednictwem serwera proxy aplikacji w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#prepare-your-on-premises-environment).

> Usługa Azure AD Application Proxy to funkcja, która jest dostępna tylko wtedy, gdy używasz wersji Premium lub Basic usługi Azure Active Directory. Aby uzyskać więcej informacji, zobacz [Cennik usługi Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/). 
> Jeśli masz licencje Enterprise Mobility Suite (EMS), możesz korzystać z tego rozwiązania.
> Łącznik serwera proxy aplikacji usługi Azure AD jest instalowany tylko w systemie Windows Server 2012 R2 lub nowszym. Jest to również wymagane przez serwer NDES.

## <a name="install-and-register-the-connector-on-the-ndes-server"></a>Instalowanie i rejestrowanie łącznika na serwerze NDES

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com/) jako administrator aplikacji w katalogu, w którym jest używany serwer proxy aplikacji. Na przykład jeśli domena dzierżawy to contoso.com, administratorem powinien być admin@contoso.com lub dowolny alias administratora w tej domenie.
1. Wybierz swoją nazwę użytkownika w prawym górnym rogu. Sprawdź, czy zalogowano się do katalogu korzystającego z serwera proxy aplikacji. Jeśli chcesz zmienić katalogi, wybierz **pozycję Przełącz katalog** i wybierz katalog, w który korzysta z serwera proxy aplikacji.
1. W lewym panelu nawigacyjnym wybierz pozycję **Azure Active Directory**.
1. W obszarze **Zarządzanie**wybierz pozycję **Serwer proxy aplikacji**.
1. Wybierz **pozycję Pobierz usługę łącznika**.

    ![Pobierz usługę łącznika, aby zapoznać się z Warunkami korzystania z usługi](./media/active-directory-app-proxy-protect-ndes/application-proxy-download-connector-service.png)

1. Zapoznaj się z warunkami użytkowania usługi. Gdy będziesz gotowy, wybierz **pozycję Akceptuj warunki & Pobierz**.
1. Skopiuj plik instalacyjny łącznika serwera proxy aplikacji usługi Azure AD na serwer NDES. 
   > Łącznik można zainstalować na dowolnym serwerze w sieci firmowej z dostępem do ndes. Nie musisz go instalować na samym serwerze NDES.
1. Uruchom plik instalacyjny, taki jak *AADApplicationProxyConnectorInstaller.exe*. Zaakceptuj postanowienia licencyjne dotyczące oprogramowania.
1. Podczas instalacji zostanie wyświetlony monit o zarejestrowanie łącznika za pomocą serwera proxy aplikacji w katalogu usługi Azure AD.
   * Podaj poświadczenia dla administratora globalnego lub aplikacji w katalogu usługi Azure AD. Poświadczenia administratora usługi Azure AD lub administratora aplikacji mogą się różnić od poświadczeń platformy Azure w portalu.

        > [!NOTE]
        > Konto administratora aplikacji używane do rejestrowania łącznika musi należeć do tego samego katalogu, w którym jest włączana usługa Proxy aplikacji.
        >
        > Na przykład jeśli domena usługi Azure AD jest *contoso.com*, `admin@contoso.com` administratorem aplikacji globalnej/aplikacji powinien być lub inny prawidłowy alias w tej domenie.

   * Jeśli konfiguracja zwiększonych zabezpieczeń programu Internet Explorer jest włączona dla serwera, na którym jest instalowany łącznik, ekran rejestracji może zostać zablokowany. Aby zezwolić na dostęp, postępuj zgodnie z instrukcjami zawartymi w komunikacie o błędzie lub wyłącz rozszerzone zabezpieczenia programu Internet Explorer podczas procesu instalacji.
   * Jeśli rejestracja łącznika nie powiedzie się, zobacz [Rozwiązywanie problemów z serwerem proxy aplikacji](application-proxy-troubleshoot.md).
1. Po zakończeniu konfiguracji jest wyświetlana notatka dla środowisk z wychodzącym serwerem proxy. Aby skonfigurować łącznik serwera proxy aplikacji usługi Azure AD do pracy za `C:\Program Files\Microsoft AAD App Proxy connector\ConfigureOutBoundProxy.ps1`pośrednictwem wychodzącego serwera proxy, uruchom dostarczony skrypt, taki jak .
1. Na stronie serwera proxy aplikacji w witrynie Azure portal nowy łącznik jest wyświetlany ze stanem *Active*, jak pokazano w poniższym przykładzie:

    ![Nowy łącznik serwera proxy aplikacji usługi Azure AD wyświetlany jako aktywny w witrynie Azure portal](./media/active-directory-app-proxy-protect-ndes/connected-app-proxy.png)

    > [!NOTE]
    > Aby zapewnić wysoką dostępność aplikacji uwierzytelniających się za pośrednictwem serwera proxy aplikacji usługi Azure AD, można zainstalować łączniki na wielu maszynach wirtualnych. Powtórz te same kroki wymienione w poprzedniej sekcji, aby zainstalować łącznik na innych serwerach przyłączonych do domeny zarządzanej usług Azure AD DS.

1. Po pomyślnej instalacji wróć do witryny Azure portal.

1. Wybierz **aplikacje enterprise**.

   ![upewnij się, że angażujesz właściwe zainteresowane strony](./media/active-directory-app-proxy-protect-ndes/azure-active-directory-enterprise-applications.png)

1. Wybierz **+Nowa aplikacja**, a następnie wybierz pozycję **Aplikacja lokalna**. 

1. W aplikacji **Dodaj własną aplikację lokalną**skonfiguruj następujące pola:

   * **Nazwa**: Wprowadź nazwę aplikacji.
   * **Wewnętrzny adres URL:** Wprowadź wewnętrzny adres URL/FQDN serwera NDES, na którym zainstalowano łącznik.
   * **Uwierzytelnianie wstępne**: Wybierz **opcję Przekazywanie**. Nie można użyć żadnej formy uwierzytelniania wstępnego. Protokół używany dla żądań certyfikatów (SCEP) nie zapewnia takiej opcji.
   * Skopiuj podany **zewnętrzny adres URL** do schowka.

1. Wybierz **+Dodaj,** aby zapisać aplikację.

1. Sprawdź, czy możesz uzyskać dostęp do serwera NDES za pośrednictwem serwera proxy aplikacji usługi Azure AD, wklejając łącze skopiowane w kroku 10 do przeglądarki. Powinna zostać wyświetlona domyślna strona powitalna dla programów IIS.

1. Jako test końcowy dodaj ścieżkę *mscep.dll* do istniejącego adresu URL wklejony w poprzednim kroku:

   https://scep-test93635307549127448334.msappproxy.net/certsrv/mscep/mscep.dll

1. Powinien zostać wyświetlony **błąd HTTP 403 — zabroniona** odpowiedź.

1. Zmień adres URL ndes podany (za pośrednictwem usługi Microsoft Intune) na urządzenia, ta zmiana może być w Centrum konfiguracji punktu końcowego firmy Microsoft lub w usłudze Intune Cloud.

   * W przypadku Centrum konfiguracji przejdź do punktu rejestracji certyfikatu (CRP) i dostosuj adres URL. Ten adres URL jest tym, do czego urządzenia wyzywają i stanowią wyzwanie.
   * Tylko w przypadku usługi Intune Cloud, znanej również jako Autonomiczna usługa Intune, edytuj lub utwórz nową zasadę SCEP i dodaj nowy adres URL.

## <a name="next-steps"></a>Następne kroki

Dzięki zintegrowanemu z usługą Azure AD Application Proxy z usługą NDES publikuj aplikacje dla użytkowników, aby uzyskać do nich dostęp. Aby uzyskać więcej informacji, zobacz [publikowanie aplikacji przy użyciu serwera proxy aplikacji usługi Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application).
