---
title: Zdalny dostęp do programu SharePoint przy użyciu serwera Proxy aplikacji usługi Azure AD | Dokumentacja firmy Microsoft
description: Zawiera podstawowe informacje dotyczące sposobu integrowania programu SharePoint server w środowisku lokalnym dzięki serwerowi Proxy aplikacji usługi Azure AD.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/28/2019
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1265341ecfdb7f418ea89bb0ec848a20c6b430cd
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70127681"
---
# <a name="enable-remote-access-to-sharepoint-with-azure-ad-application-proxy"></a>Zdalny dostęp do programu SharePoint przy użyciu serwera Proxy aplikacji usługi Azure AD

W tym artykule omówiono sposób integrowania programu SharePoint server w środowisku lokalnym dzięki serwerowi Proxy aplikacji usługi Azure Active Directory (Azure AD).

Aby włączyć dostęp zdalny do programu SharePoint przy użyciu serwera Proxy aplikacji usługi Azure AD, wykonaj sekcje w tym artykule krok po kroku.

## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule założono, że masz już program SharePoint 2013 lub nowszym w danym środowisku. Ponadto należy wziąć pod uwagę następujące wymagania wstępne:

* Program SharePoint obsługuje natywnego protokołu Kerberos. W związku z tym użytkownicy, którzy uzyskują dostęp do wewnętrznych witryn firmowych zdalnie za pośrednictwem serwera Proxy aplikacji usługi Azure AD, można założyć na środowisko logowania jednokrotnego (SSO).
* Ten scenariusz obejmuje zmiany w konfiguracji serwera programu SharePoint. Firma Microsoft zaleca korzystanie w środowisku przejściowym. Dzięki temu można dokonać aktualizacji serwera przejściowego najpierw i następnie ułatwienia cyklu testowania przed przejściem do środowiska produkcyjnego.
* Firma Microsoft wymaga protokołu SSL w adresie URL publikacji. Protokół SSL jest również wymagany w wewnętrznym adresie URL, aby upewnić się, że linki są poprawnie wysyłane/mapowane.

> [!NOTE]
> Najlepszym rozwiązaniem jest użycie domen niestandardowych wszędzie tam, gdzie to możliwe. W przypadku domeny niestandardowej można skonfigurować ten sam adres URL dla wewnętrznego adresu URL i zewnętrznego adresu URL. Następnie można użyć tego samego linku w celu uzyskania dostępu do aplikacji z poziomu lub spoza sieci. Ta konfiguracja optymalizuje środowisko dla użytkowników i innych aplikacji, które muszą uzyskać dostęp do aplikacji. Dowiedz się więcej [na temat pracy z domenami niestandardowymi w usłudze Azure serwer proxy aplikacji usługi Azure AD](application-proxy-configure-custom-domain.md).

## <a name="step-1-configure-kerberos-constrained-delegation-kcd"></a>Krok 1: Konfigurowanie ograniczonego delegowania protokołu Kerberos (KCD)

Aplikacje lokalne, które korzystają z uwierzytelniania Windows można osiągnąć logowania jednokrotnego (SSO) przy użyciu protokołu uwierzytelniania Kerberos i funkcję ograniczonego delegowania protokołu Kerberos (KCD). Ograniczonego delegowania protokołu Kerberos, po skonfigurowaniu umożliwia łącznik serwera Proxy aplikacji w celu uzyskania tokenu Windows użytkownika, nawet wtedy, gdy użytkownik nie zarejestrował się usłudze Windows bezpośrednio. Aby dowiedzieć się więcej na temat ograniczonego delegowania protokołu Kerberos, zobacz [omówienie delegowania ograniczonego protokołu Kerberos](https://technet.microsoft.com/library/jj553400.aspx).

Do skonfigurowania ograniczonego delegowania protokołu Kerberos dla programu SharePoint server, korzystając z procedur w kolejnych sekcjach:

### <a name="ensure-that-sharepoint-web-application-is-running-under-a-domain-account"></a>Upewnij się, że aplikacja sieci Web programu SharePoint jest uruchomiona na koncie domeny

Najpierw upewnij się, że aplikacja sieci Web programu SharePoint jest uruchomiona na koncie domeny — nie system lokalny, Usługa lokalna lub usługa sieciowa. Zrób to, aby można było dołączyć główne nazwy usług (SPN) do tego konta. Nazwy SPN są, jak protokół Kerberos identyfikuje różnych usług. I musisz mieć konto później, aby skonfigurować ograniczonego delegowania protokołu Kerberos.

> [!NOTE]
> Musisz mieć wcześniej utworzone konto usługi Azure AD dla usługi. Sugerujemy, umożliwiający automatyczną zmianę hasła. Aby uzyskać więcej informacji na temat pełnego zestawu kroków i rozwiązywania problemów, zobacz [Konfigurowanie automatycznej zmiany hasła w programie SharePoint](https://technet.microsoft.com/library/ff724280.aspx).

Aby upewnić się, że lokacje są uruchomione na koncie usługi zdefiniowane, wykonaj następujące czynności:

1. Otwórz witrynę **administracji centralnej programu SharePoint** .
1. Przejdź do **zabezpieczeń** i wybierz **Konfigurowanie kont usług**.
1. Wybierz **- SharePoint - 80. pula aplikacji sieci Web**. Opcje mogą być nieco inne na podstawie nazwy puli w sieci web, lub jeśli puli sieci web domyślnie używa protokołu SSL.

   ![Opcje dotyczące konfigurowania konta usługi](./media/application-proxy-integrate-with-sharepoint-server/service-web-application.png)

1. Jeśli **wybierz konto dla tego składnika** pole jest ustawione na **Usługa lokalna** lub **Usługa sieciowa**, musisz utworzyć konto. W przeciwnym razie jesteś gotowy i przejściem do następnej sekcji.
1. Wybierz **zarejestrować nowego zarządzanego konta**. Po utworzeniu konta należy ustawić **puli aplikacji sieci Web** zanim będzie można użyć tego konta.

### <a name="set-a-service-principal-name-for-the-sharepoint-service-account"></a>Ustaw główną nazwę usługi dla konta usługi programu SharePoint

Przed skonfigurowaniem programu KCD należy:

* Zidentyfikuj konto domeny z uruchomioną aplikacją sieci Web programu SharePoint, która będzie uwidaczniać serwer proxy usługi Azure AD.
* Wybierz wewnętrzny adres URL, który zostanie skonfigurowany zarówno na serwerze proxy usługi Azure AD, jak i w programie SharePoint. Wewnętrzny adres URL nie może być już używany w aplikacji sieci Web i nigdy nie będzie wyświetlany w przeglądarce sieci Web.

Przy założeniu, że adres <https://sharepoint>URL został wybrany wewnętrznie, Nazwa SPN to:

```
HTTP/SharePoint
```

> [!NOTE]
> Postępuj zgodnie z następującymi zaleceniami dotyczącymi wewnętrznego adresu URL:
> * Użyj protokołu HTTPS
> * Nie używaj portów niestandardowych
> * W systemie DNS Utwórz hosta (A), aby wskazać SharePoint WFE (lub moduł równoważenia obciążenia), a nie alias (CName).

Aby zarejestrować tę nazwę SPN, uruchom następujące polecenie w wierszu polecenia jako administrator domeny:

```
setspn -S HTTP/SharePoint demo\spAppPoolAccount
```

To polecenie ustawia nazwę SPN _http/SharePoint_ dla konta puli aplikacji programu SharePoint _demo\spAppPoolAccount_.

Zastąp ciąg _http/SharePoint_ nazwą SPN wewnętrznego adresu URL i _demo\spAppPoolAccount_ z kontem puli aplikacji w Twoim środowisku. Polecenia Setspn wyszukuje nazwy SPN, przed dodaniem go. W tym już istnieje, zostanie wyświetlony zduplikowany błąd **wartości SPN** . W takim przypadku należy rozważyć usunięcie istniejącej nazwy SPN, jeśli nie jest ona ustawiona na poprawnym koncie puli aplikacji.

Aby sprawdzić, czy nazwa SPN została dodana, można uruchomić polecenie Setspn z opcją-L. Aby dowiedzieć się więcej na temat tego polecenia, zobacz [Setspn](https://technet.microsoft.com/library/cc731241.aspx).

### <a name="ensure-that-the-connector-is-trusted-for-delegation-to-the-spn-added-to-the-sharepoint-application-pool-account"></a>Upewnij się, że łącznik jest zaufany do delegowania do nazwy SPN dodanej do konta puli aplikacji SharePoint

Skonfiguruj KCD tak, aby usługa Azure serwer proxy aplikacji usługi Azure AD mogła delegować tożsamości użytkowników do konta puli aplikacji programu SharePoint. Konfigurowanie ograniczonego delegowania protokołu Kerberos, włączając łącznik serwera Proxy aplikacji pobrać bilety protokołu Kerberos dla użytkowników, którzy zostali uwierzytelnieni w usłudze Azure AD. Następnie ten serwer przekazuje kontekst do aplikacji docelowej lub programu SharePoint w tym przypadku.

Aby skonfigurować ograniczonego delegowania protokołu Kerberos, wykonaj następujące czynności dla każdej maszyny łącznika:

1. Zaloguj się jako administrator domeny do kontrolera domeny, a następnie otwórz **użytkownicy usługi Active Directory i komputery**.
1. Znajdź komputera, na którym jest zasilany z łącznika. W tym przykładzie jest tym samym serwerze programu SharePoint.
1. Kliknij dwukrotnie komputer, a następnie kliknij przycisk **delegowania** kartę.
1. Upewnij się, że ustawienia delegowania są ustawione na **Ufaj temu komputerowi w delegowaniu tylko do określonych usług**. Następnie wybierz **Użyj dowolnego protokołu uwierzytelniania**.
1. Kliknij przycisk **Dodaj** , kliknij pozycję **Użytkownicy lub komputery**, a następnie Znajdź konto puli aplikacji SharePoint, na przykład _demo\spAppPoolAccount_.
1. Na liście nazw SPN wybierz jedną, która została wcześniej utworzona dla konta usługi.
1. Kliknij przycisk **OK**. Kliknij przycisk **OK** ponownie, aby zapisać zmiany.
  
   ![Ustawienia delegowania](./media/application-proxy-integrate-with-sharepoint-server/delegation-box2.png)

## <a name="step-2-configure-azure-ad-proxy"></a>Krok 2: Konfigurowanie serwera proxy usługi Azure AD

Teraz, po skonfigurowaniu KCD, możesz przystąpić do konfigurowania serwer proxy aplikacji usługi Azure AD platformy Azure.

1. Publikowanie witryny programu SharePoint z następującymi ustawieniami. Aby uzyskać instrukcje krok po kroku, zobacz [publikowanie aplikacji przy użyciu serwera Proxy aplikacji usługi Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad).
   * **Wewnętrzny adres URL**: Wewnętrzny adres URL programu SharePoint, który został wybrany wcześniej **<https://SharePoint/>** , taki jak.
   * **Metoda uwierzytelniania wstępnego**: Usługa Azure Active Directory
   * **Tłumaczenie adresu URL w nagłówkach**: NO

   > [!TIP]
   > Program SharePoint używa _nagłówek hosta_ wartość do wyszukania w witrynie. Polecenie to generuje także łączy na podstawie tej wartości. Efektem sieciowym jest to dowolny link, który generuje programie SharePoint opublikowanego adresu URL, który jest prawidłowo skonfigurowany do używania zewnętrznego adresu URL. Ustawienie wartości **tak** umożliwia również łącznik aby przekazywać żądania do aplikacji zaplecza. Jednakże, ustawiając wartość na **nie** oznacza, że łącznik nie będzie wysyłać nazwę hosta wewnętrznego. Zamiast tego łącznika wysyła nagłówek hosta jako adres URL opublikowanej aplikacji zaplecza.

   ![Publikowanie programu SharePoint jako aplikację](./media/application-proxy-integrate-with-sharepoint-server/publish-app.png)

1. Po opublikowaniu aplikacji pojedynczego ustawień logowania jednokrotnego należy skonfigurować następujące czynności:

   1. Na stronie aplikacji w portalu wybierz **logowanie jednokrotne**.
   1. Pojedynczego logowania jednokrotnego, wybierz tryb **zintegrowane uwierzytelnianie Windows**.
   1. Główna nazwa usługi aplikacji wewnętrznej należy ustawić wartość, które zostały ustawione wcześniej. Przykładem może być **http/SharePoint**.
   1. W obszarze "delegowana tożsamość logowania" Wybierz najbardziej odpowiednią opcję konfiguracji lasu Active Directory. Jeśli na przykład masz pojedynczą domenę usługi AD w lesie, wybierz pozycję **lokalna nazwa konta sam** (jak pokazano poniżej), ale jeśli użytkownicy nie znajdują się w tej samej domenie co program SharePoint, a serwery łącznika serwera proxy aplikacji, wybierz **lokalną główną nazwę użytkownika** (niepokazywany).

   ![Konfigurowanie uwierzytelniania Windows zintegrowanego logowania jednokrotnego](./media/application-proxy-integrate-with-sharepoint-server/configure-iwa.png)

1. Aby zakończyć konfigurowanie aplikacji, przejdź do **użytkowników i grup** sekcji, a następnie przypisać użytkownikom na dostęp do tej aplikacji. 

## <a name="step-3-configure-sharepoint-to-use-kerberos-and-azure-ad-proxy-urls"></a>Krok 3: Konfigurowanie programu SharePoint do używania adresów URL serwera proxy Kerberos i usługi Azure AD

Następnym krokiem jest przełączenie aplikacji sieci Web programu SharePoint do nowej strefy skonfigurowanej przy użyciu protokołu Kerberos i odpowiedniego mapowania dostępu alternatywnego, aby program SharePoint mógł obsługiwać przychodzące żądania wysyłane do wewnętrznego adresu URL i odpowiadać na linki skompilowane dla zewnętrznego adresu URL.

1. Uruchom **powłokę zarządzania programu SharePoint**.
1. Uruchom następujący skrypt, aby zwiększyć aplikację sieci Web do strefy ekstranetu i włączyć uwierzytelnianie Kerberos:

   ```powershell
   # Replace "http://spsites/" with the URL of your web application
   # Replace "https://sharepoint-f128.msappproxy.net/" with the External URL in your Azure AD proxy application
   $winAp = New-SPAuthenticationProvider -UseWindowsIntegratedAuthentication -DisableKerberos:$false
   Get-SPWebApplication "http://spsites/" | New-SPWebApplicationExtension -Name "SharePoint - AAD Proxy" -SecureSocketsLayer -Zone "Extranet" -Url "https://sharepoint-f128.msappproxy.net/" -AuthenticationProvider $winAp
   ```

1. Otwórz witrynę **administracji centralnej programu SharePoint** .
1. W obszarze **ustawień systemowych**, wybierz opcję **Konfigurowanie alternatywnych mapowań dostępu**. Zostanie otwarte okno mapowania dostępu alternatywnego.
1. Wybierz swoją witrynę, na przykład **SharePoint-80**. W tej chwili strefa ekstranetu nie ma jeszcze prawidłowo ustawionego wewnętrznego adresu URL:

   ![Pokazuje alternatywne mapowanie dostępu](./media/application-proxy-integrate-with-sharepoint-server/alternate-access1.png)

1. Kliknij pozycję **Dodaj wewnętrzne adresy URL**.
1. W polu tekstowym **Protokół adresu URL hosta i portu** wpisz **wewnętrzny adres URL** SKONFIGUROWANY w usłudze Azure AD proxy, na <https://SharePoint/>przykład.
1. Na liście rozwijanej wybierz pozycję **Ekstranet** strefy.
1. Kliknij polecenie **Zapisz**.
1. Alternatywne mapowania dostępu powinny teraz wyglądać następująco:

    ![Popraw alternatywne mapowania dostępu](./media/application-proxy-integrate-with-sharepoint-server/alternate-access3.png)

## <a name="step-4-ensure-that-an-https-certificate-is-configured-for-the-iis-site-of-the-extranet-zone"></a>Krok 4: Upewnij się, że certyfikat HTTPS jest skonfigurowany dla witryny usług IIS strefy ekstranetu

Konfiguracja programu SharePoint została zakończona, ale ponieważ wewnętrzny adres URL strefy ekstranetu to <https://SharePoint/>, należy ustawić certyfikat dla tej lokacji.

1. Otwórz konsolę programu Windows PowerShell.
1. Uruchom następujący skrypt, aby wygenerować certyfikat z podpisem własnym i dodać go do komputera mój magazyn:

   ```powershell
   # Replace "SharePoint" with the actual hostname of the Internal URL of your Azure AD proxy application
   New-SelfSignedCertificate -DnsName "SharePoint" -CertStoreLocation "cert:\LocalMachine\My"
   ```

   > [!NOTE]
   > Certyfikaty z podpisem własnym są odpowiednie tylko do celów testowych. W środowiskach produkcyjnych zdecydowanie zaleca się używanie certyfikatów wystawionych przez urząd certyfikacji.

1. Otwórz konsolę "Internet Information Services Manager".
1. Rozwiń serwer w widoku drzewa, rozwiń węzeł "Lokacje", wybierz lokację "SharePoint-AAD proxy" i kliknij pozycję **powiązania**.
1. Wybierz pozycję powiązanie https, a następnie kliknij pozycję **Edytuj.** ...
1. W polu certyfikat protokołu SSL wybierz pozycję certyfikat **programu SharePoint** , a następnie kliknij przycisk OK.

Możesz teraz uzyskać dostęp do witryny programu SharePoint zewnętrznie za pośrednictwem serwera Proxy aplikacji usługi Azure AD.

## <a name="next-steps"></a>Kolejne kroki

* [Praca z domenami niestandardowymi na serwerze Proxy aplikacji usługi Azure AD](application-proxy-configure-custom-domain.md)
* [Omówienie łączników serwera Proxy aplikacji usługi Azure AD](application-proxy-connectors.md)
