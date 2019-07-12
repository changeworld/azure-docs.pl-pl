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
ms.date: 12/10/2018
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4f213acea71f22815d8b26b6c4c6cb54f64b8b34
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807800"
---
# <a name="enable-remote-access-to-sharepoint-with-azure-ad-application-proxy"></a>Zdalny dostęp do programu SharePoint przy użyciu serwera Proxy aplikacji usługi Azure AD

W tym artykule omówiono sposób integrowania programu SharePoint server w środowisku lokalnym dzięki serwerowi Proxy aplikacji usługi Azure Active Directory (Azure AD).

Aby włączyć dostęp zdalny do programu SharePoint przy użyciu serwera Proxy aplikacji usługi Azure AD, wykonaj sekcje w tym artykule krok po kroku.

## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule założono, że masz już program SharePoint 2013 lub nowszym w danym środowisku. Ponadto należy wziąć pod uwagę następujące wymagania wstępne:

* Program SharePoint obsługuje natywnego protokołu Kerberos. W związku z tym użytkownicy, którzy uzyskują dostęp do wewnętrznych witryn firmowych zdalnie za pośrednictwem serwera Proxy aplikacji usługi Azure AD, można założyć na środowisko logowania jednokrotnego (SSO).
* Ten scenariusz obejmuje zmiany w konfiguracji serwera programu SharePoint. Firma Microsoft zaleca korzystanie w środowisku przejściowym. Dzięki temu można dokonać aktualizacji serwera przejściowego najpierw i następnie ułatwienia cyklu testowania przed przejściem do środowiska produkcyjnego.
* Firma Microsoft wymaga protokołu SSL w adresie URL publikacji. Aby upewnić się, czy łącza są wysyłane mapowane poprawnie na wewnętrzny adres URL również wymagany jest protokół SSL.

## <a name="step-1-configure-kerberos-constrained-delegation-kcd"></a>Krok 1: Konfigurowanie protokołu Kerberos ograniczone delegowanie (KCD)

Aplikacje lokalne, które korzystają z uwierzytelniania Windows można osiągnąć logowania jednokrotnego (SSO) przy użyciu protokołu uwierzytelniania Kerberos i funkcję ograniczonego delegowania protokołu Kerberos (KCD). Ograniczonego delegowania protokołu Kerberos, po skonfigurowaniu umożliwia łącznik serwera Proxy aplikacji w celu uzyskania tokenu Windows użytkownika, nawet wtedy, gdy użytkownik nie zarejestrował się usłudze Windows bezpośrednio. Aby dowiedzieć się więcej na temat ograniczonego delegowania protokołu Kerberos, zobacz [omówienie delegowania ograniczonego protokołu Kerberos](https://technet.microsoft.com/library/jj553400.aspx).

Do skonfigurowania ograniczonego delegowania protokołu Kerberos dla programu SharePoint server, korzystając z procedur w kolejnych sekcjach:

### <a name="ensure-that-sharepoint-web-application-is-running-under-a-domain-account"></a>Upewnij się, że aplikacja sieci web programu SharePoint jest uruchomiona w ramach konta domeny

Najpierw upewnij się, że aplikacja sieci web programu SharePoint jest uruchomiona w ramach konta domeny — system lokalny, Usługa lokalna lub usługi sieciowej. W tym dołączenie głównych nazw usług (SPN) tego konta. Nazwy SPN są, jak protokół Kerberos identyfikuje różnych usług. I musisz mieć konto później, aby skonfigurować ograniczonego delegowania protokołu Kerberos.

> [!NOTE]
> Musisz mieć wcześniej utworzone konto usługi Azure AD dla usługi. Sugerujemy, umożliwiający automatyczną zmianę hasła. Aby uzyskać więcej informacji na temat pełnego zestawu kroków i rozwiązywanie problemów, zobacz [skonfigurować automatyczną zmianę hasła w programie SharePoint](https://technet.microsoft.com/library/ff724280.aspx).

Aby upewnić się, że lokacje są uruchomione na koncie usługi zdefiniowane, wykonaj następujące czynności:

1. Otwórz **administracji centralnej programu SharePoint** lokacji.
1. Przejdź do **zabezpieczeń** i wybierz **Konfigurowanie kont usług**.
1. Wybierz **- SharePoint - 80. pula aplikacji sieci Web**. Opcje mogą być nieco inne na podstawie nazwy puli w sieci web, lub jeśli puli sieci web domyślnie używa protokołu SSL.

   ![Opcje dotyczące konfigurowania konta usługi](./media/application-proxy-integrate-with-sharepoint-server/service-web-application.png)

1. Jeśli **wybierz konto dla tego składnika** pole jest ustawione na **Usługa lokalna** lub **Usługa sieciowa**, musisz utworzyć konto. W przeciwnym razie jesteś gotowy i przejściem do następnej sekcji.
1. Wybierz **zarejestrować nowego zarządzanego konta**. Po utworzeniu konta należy ustawić **puli aplikacji sieci Web** zanim będzie można użyć tego konta.

### <a name="set-a-service-principal-name-for-the-sharepoint-service-account"></a>Ustaw główną nazwę usługi dla konta usługi programu SharePoint

Przed rozpoczęciem konfigurowania ograniczonego delegowania protokołu Kerberos, należy:

* Określ konto domeny, uruchamiania aplikacji sieci web programu SharePoint, która udostępni serwera Proxy usługi Azure AD.
* Wybierz wewnętrzny adres URL, który zostanie skonfigurowany zarówno w przypadku serwera Proxy usługi Azure AD, jak i programu SharePoint. To wewnętrzny adres URL nie może już być używany w aplikacji sieci web i nigdy nie będzie wyświetlany w przeglądarce sieci web.

Zakładając, że wewnętrzny adres URL, wybierany jest <https://sharepoint>, to główna nazwa usługi:

```
HTTP/SharePoint
```

> [!NOTE]
> Wykonaj te zalecenia dotyczące wewnętrznego adresu URL:
> * Za pośrednictwem protokołu HTTPS
> * Nie należy używać niestandardowych portów
> * W systemie DNS należy utworzyć hosta (A) do punktu na frontonie WFE programu SharePoint (lub modułu równoważenia obciążenia), a nie do aliasu (CName)

Aby zarejestrować tę nazwę SPN, uruchom następujące polecenie w wierszu polecenia jako administrator domeny:

```
setspn -S HTTP/SharePoint demo\spAppPoolAccount
```

To polecenie ustawia nazwę SPN _HTTP/SharePoint_ dla konta puli aplikacji programu SharePoint _demo\spAppPoolAccount_.

Zastąp _HTTP/SharePoint_ przy użyciu nazwy SPN dla wewnętrznego adresu URL i _demo\spAppPoolAccount_ przy użyciu konta puli aplikacji w danym środowisku. Polecenia Setspn wyszukuje nazwy SPN, przed dodaniem go. W już istnieje, zostanie wyświetlony **zduplikowana wartość SPN** błędu. W takim przypadku należy wziąć pod uwagę do usunięcia istniejącej głównej nazwy usługi, jeśli nie jest ustawiona na koncie puli właściwej aplikacji.

Aby sprawdzić, czy nazwa SPN została dodana przy użyciu polecenia Setspn z opcją -L. Aby dowiedzieć się więcej na temat tego polecenia, zobacz [Setspn](https://technet.microsoft.com/library/cc731241.aspx).

### <a name="ensure-that-the-connector-is-trusted-for-delegation-to-the-spn-added-to-the-sharepoint-application-pool-account"></a>Upewnij się, że łącznik jest zaufany dla celów delegacji do nazwy SPN dodany do konta puli aplikacji programu SharePoint

Konfigurowanie ograniczonego delegowania protokołu Kerberos, aby usługa serwera Proxy aplikacji usługi Azure AD mogą delegować uprawnienia do tożsamości użytkowników do konta puli aplikacji programu SharePoint. Konfigurowanie ograniczonego delegowania protokołu Kerberos, włączając łącznik serwera Proxy aplikacji pobrać bilety protokołu Kerberos dla użytkowników, którzy zostali uwierzytelnieni w usłudze Azure AD. Następnie ten serwer przekazuje kontekst do aplikacji docelowej lub programu SharePoint w tym przypadku.

Aby skonfigurować ograniczonego delegowania protokołu Kerberos, wykonaj następujące czynności dla każdej maszyny łącznika:

1. Zaloguj się jako administrator domeny do kontrolera domeny, a następnie otwórz **użytkownicy usługi Active Directory i komputery**.
1. Znajdź komputera, na którym jest zasilany z łącznika. W tym przykładzie jest tym samym serwerze programu SharePoint.
1. Kliknij dwukrotnie komputer, a następnie kliknij przycisk **delegowania** kartę.
1. Upewnij się, że ustawienia delegowania są ustawione na **Ufaj temu komputerowi w delegowaniu tylko do określonych usług**. Następnie wybierz **Użyj dowolnego protokołu uwierzytelniania**.
1. Kliknij przycisk **Dodaj** przycisku, kliknij przycisk **użytkowników lub komputerów**, a następnie zlokalizuj konto puli aplikacji programu SharePoint, na przykład _demo\spAppPoolAccount_.
1. Na liście nazw SPN wybierz jedną, która została wcześniej utworzona dla konta usługi.
1. Kliknij przycisk **OK**. Kliknij przycisk **OK** ponownie, aby zapisać zmiany.
  
   ![Ustawienia delegowania](./media/application-proxy-integrate-with-sharepoint-server/delegation-box2.png)

## <a name="step-2-configure-azure-ad-proxy"></a>Krok 2: Konfigurowanie serwera Proxy usługi Azure AD

Teraz, gdy skonfigurowano ograniczonego delegowania protokołu Kerberos, możesz przystąpić do konfigurowania serwera Proxy aplikacji usługi Azure AD.

1. Publikowanie witryny programu SharePoint z następującymi ustawieniami. Aby uzyskać instrukcje krok po kroku, zobacz [publikowanie aplikacji przy użyciu serwera Proxy aplikacji usługi Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad).
   * **Wewnętrzny adres URL**: Program SharePoint wewnętrzny adres URL, który wybrano wcześniej, takich jak **<https://SharePoint/>** .
   * **Metoda wstępnego uwierzytelnienia**: Usługa Azure Active Directory
   * **Przetłumacz URL w nagłówkach**: NO

   > [!TIP]
   > Program SharePoint używa _nagłówek hosta_ wartość do wyszukania w witrynie. Polecenie to generuje także łączy na podstawie tej wartości. Efektem sieciowym jest to dowolny link, który generuje programie SharePoint opublikowanego adresu URL, który jest prawidłowo skonfigurowany do używania zewnętrznego adresu URL. Ustawienie wartości **tak** umożliwia również łącznik aby przekazywać żądania do aplikacji zaplecza. Jednakże, ustawiając wartość na **nie** oznacza, że łącznik nie będzie wysyłać nazwę hosta wewnętrznego. Zamiast tego łącznika wysyła nagłówek hosta jako adres URL opublikowanej aplikacji zaplecza.

   ![Publikowanie programu SharePoint jako aplikację](./media/application-proxy-integrate-with-sharepoint-server/publish-app.png)

1. Po opublikowaniu aplikacji pojedynczego ustawień logowania jednokrotnego należy skonfigurować następujące czynności:

   1. Na stronie aplikacji w portalu wybierz **logowanie jednokrotne**.
   1. Pojedynczego logowania jednokrotnego, wybierz tryb **zintegrowane uwierzytelnianie Windows**.
   1. Główna nazwa usługi aplikacji wewnętrznej należy ustawić wartość, które zostały ustawione wcześniej. W tym przykładzie wyniesie **HTTP/SharePoint**.
   1. W "Delegowana tożsamość logowania" Wybierz opcję najbardziej odpowiedni dla danej konfiguracji lasu usługi Active Directory. Na przykład w przypadku pojedynczej domeny usługi AD w lesie, wybierz opcję **nazwy konta SAM On-premises** (jak pokazano poniżej), ale w przypadku, gdy użytkownicy nie są w tej samej domenie, w programie SharePoint, a następnie wybierz pozycję serwery łącznika serwera Proxy aplikacji  **Główna nazwa użytkownika w środowisku lokalnym** (niewyświetlany).

   ![Konfigurowanie uwierzytelniania Windows zintegrowanego logowania jednokrotnego](./media/application-proxy-integrate-with-sharepoint-server/configure-iwa.png)

1. Aby zakończyć konfigurowanie aplikacji, przejdź do **użytkowników i grup** sekcji, a następnie przypisać użytkownikom na dostęp do tej aplikacji. 

## <a name="step-3-configure-sharepoint-to-use-kerberos-and-azure-ad-proxy-urls"></a>Krok 3: Konfigurowanie programu SharePoint do używania protokołu Kerberos i adresów URL serwera Proxy usługi Azure AD

Następnym krokiem jest rozszerzenie aplikacji sieci web programu SharePoint do nowej strefy, skonfigurowane przy użyciu protokołu Kerberos i mapowania dostępu alternatywnego odpowiednie, aby zezwolić programowi SharePoint do obsługi przychodzących żądań wysyłanych do wewnętrznego adresu URL i odpowiedzi wraz z łączami stworzona z myślą o zewnętrznego adresu URL.

1. Rozpocznij **powłokę zarządzania programu SharePoint**.
1. Uruchom następujący skrypt, aby rozszerzyć aplikację sieci web do strefy ekstranetu i włączyć uwierzytelnianie Kerberos:

   ```powershell
   # Replace "http://spsites/" with the URL of your web application
   # Replace "https://sharepoint-f128.msappproxy.net/" with the External URL in your Azure AD proxy application
   $winAp = New-SPAuthenticationProvider -UseWindowsIntegratedAuthentication -DisableKerberos:$false
   Get-SPWebApplication "http://spsites/" | New-SPWebApplicationExtension -Name "SharePoint - AAD Proxy" -SecureSocketsLayer -Zone "Extranet" -Url "https://sharepoint-f128.msappproxy.net/" -AuthenticationProvider $winAp
   ```

1. Otwórz **administracji centralnej programu SharePoint** lokacji.
1. W obszarze **ustawień systemowych**, wybierz opcję **Konfigurowanie alternatywnych mapowań dostępu**. Zostanie otwarte okno mapowania dostępu alternatywnego.
1. Wybierz lokację, na przykład **SharePoint — 80**. Na chwilę ekstranetu strefy nie ma wewnętrzny poprawnie ustawiony adres URL:

   ![Pokazuje okno mapowania dostępu alternatywnego](./media/application-proxy-integrate-with-sharepoint-server/alternate-access1.png)

1. Kliknij przycisk **Dodaj wewnętrzne adresy URL**.
1. W **protokół adresu URL, hosta i port** polu tekstowym wpisz **wewnętrzny adres URL** skonfigurowany serwer proxy usługi Azure AD, na przykład <https://SharePoint/>.
1. Wybierz strefę **ekstranet** na liście rozwijanej.
1. Kliknij polecenie **Zapisz**.
1. Mapowania dostępu alternatywnego powinna teraz wyglądać następująco:

    ![Popraw mapowania dostępu alternatywnego](./media/application-proxy-integrate-with-sharepoint-server/alternate-access3.png)

## <a name="step-4-ensure-that-an-https-certificate-is-configured-for-the-iis-site-of-the-extranet-zone"></a>Krok 4: Upewnij się, że certyfikat HTTPS jest skonfigurowany dla witryny usług IIS ekstranetu strefy

Konfiguracja programu SharePoint jest teraz zakończona, ale ponieważ jest wewnętrzny adres URL w ekstranecie strefy <https://SharePoint/>, certyfikat musi być ustawiona dla tej lokacji.

1. Otwórz konsolę programu Windows PowerShell.
1. Uruchom następujący skrypt, aby wygenerować certyfikat z podpisem własnym i dodaj go do komputera za Sklepu:

   ```powershell
   # Replace "SharePoint" with the actual hostname of the Internal URL of your Azure AD proxy application
   New-SelfSignedCertificate -DnsName "SharePoint" -CertStoreLocation "cert:\LocalMachine\My"
   ```

   > [!NOTE]
   > Certyfikaty z podpisem własnym nadają się tylko do celów testowych. W środowiskach produkcyjnych zdecydowanie zaleca się używać certyfikatów wystawionych przez urząd certyfikacji, zamiast tego.

1. Otwórz konsolę "Menedżera internetowych usług informacyjnych".
1. Rozwiń węzeł serwera w widoku drzewa, rozwiń węzeł "Witryny", wybierz lokację "Proxy usługi AAD — SharePoint" i kliknąć **powiązania**.
1. Wybierz powiązanie https, a następnie kliknij przycisk **Edytuj...** .
1. W polu certyfikat protokołu SSL, wybierz **SharePoint** certyfikatu, a następnie kliknij przycisk OK.

Możesz teraz uzyskać dostęp do witryny programu SharePoint zewnętrznie za pośrednictwem serwera Proxy aplikacji usługi Azure AD.

## <a name="next-steps"></a>Kolejne kroki

* [Praca z domenami niestandardowymi na serwerze Proxy aplikacji usługi Azure AD](application-proxy-configure-custom-domain.md)
* [Omówienie łączników serwera Proxy aplikacji usługi Azure AD](application-proxy-connectors.md)
