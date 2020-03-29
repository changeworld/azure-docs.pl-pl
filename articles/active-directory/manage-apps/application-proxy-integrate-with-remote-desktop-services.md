---
title: Publikowanie pulpitu zdalnego za pomocą serwera proxy aplikacji usługi Azure AD | Dokumenty firmy Microsoft
description: Obejmuje podstawowe informacje o łącznikach serwera proxy aplikacji usługi Azure AD.
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
ms.date: 05/23/2019
ms.author: mimart
ms.custom: it-pro
ms.reviewer: harshja
ms.collection: M365-identity-device-management
ms.openlocfilehash: d6ca64e2de5734c567173fc735776074f4c87fbc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67108466"
---
# <a name="publish-remote-desktop-with-azure-ad-application-proxy"></a>Publikowanie pulpitu zdalnego za pomocą serwera proxy aplikacji usługi Azure AD

Usługa pulpitu zdalnego i serwer proxy aplikacji usługi Azure AD współpracują ze sobą, aby zwiększyć produktywność pracowników, którzy są poza siecią firmową. 

Odbiorcami przeznaczonymi dla tego artykułu są:
- Klienci bieżącego serwera proxy aplikacji, którzy chcą oferować użytkownikom końcowym więcej aplikacji, publikując aplikacje lokalne za pośrednictwem usług pulpitu zdalnego.
- Obecni klienci usług pulpitu zdalnego, którzy chcą zmniejszyć obszar ataku ich wdrożenia przy użyciu serwera proxy aplikacji usługi Azure AD. W tym scenariuszu udostępnia ograniczony zestaw kontroli weryfikacji dwuetapowej i dostępu warunkowego do rds.

## <a name="how-application-proxy-fits-in-the-standard-rds-deployment"></a>Jak serwer proxy aplikacji pasuje do standardowego wdrożenia usług pulpitu zdalnego

Standardowe wdrożenie usług pulpitu zdalnego obejmuje różne usługi roli pulpitu zdalnego uruchomione w systemie Windows Server. Patrząc na [architekturę Usług pulpitu zdalnego,](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/desktop-hosting-logical-architecture)istnieje wiele opcji wdrażania. W przeciwieństwie do innych opcji wdrażania usług pulpitu zdalnego [wdrożenie usługi RDS za pomocą serwera proxy aplikacji usługi Azure AD](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/desktop-hosting-logical-architecture) (pokazanego na poniższym diagramie) ma stałe połączenie wychodzące z serwera z uruchomiona usługą łącznika. Inne wdrożenia pozostawiają otwarte połączenia przychodzące za pośrednictwem modułu równoważenia obciążenia.

![Serwer proxy aplikacji znajduje się między maszyną wirtualną RDS a publicznym internetem](./media/application-proxy-integrate-with-remote-desktop-services/rds-with-app-proxy.png)

We wdrożeniu usług pulpitu zdalnego rola sieci Web usług pulpitu zdalnego i rola bramy usług pulpitu zdalnego są uruchamiane na komputerach z dostępem do Internetu. Te punkty końcowe są widoczne z następujących powodów:
- Usługa RD Web udostępnia użytkownikowi publiczny punkt końcowy do logowania się i wyświetlania różnych lokalnych aplikacji i pulpitów, do których mogą uzyskać dostęp. Po wybraniu zasobu połączenie RDP jest tworzone przy użyciu aplikacji macierzystej w os.
- Brama usług pulpitu zdalnego pojawia się na zdjęciu, gdy użytkownik uruchamia połączenie RDP. Brama usług pulpitu zdalnego obsługuje zaszyfrowany ruch RDP przychodzący przez Internet i tłumaczy go na serwer lokalny, z którego użytkownik się łączy. W tym scenariuszu ruch odbierany przez bramę usług pulpitu zdalnego pochodzi z serwera proxy aplikacji usługi Azure AD.

>[!TIP]
>Jeśli usługi RDS nie zostały wcześniej wdrożone lub chcesz uzyskać więcej informacji przed rozpoczęciem, dowiedz się, jak [bezproblemowo wdrażać usługi RDS za pomocą usługi Azure Resource Manager i azure marketplace.](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/rds-in-azure)

## <a name="requirements"></a>Wymagania

- Użyj klienta innego niż klient sieci Web pulpitu zdalnego, ponieważ klient sieci web nie obsługuje serwera proxy aplikacji.

- Punkty końcowe sieci Web usług pulpitu zdalnego i bramy usług pulpitu zdalnego muszą znajdować się na tym samym komputerze i ze wspólnym katalogiem głównym. Bramy sieci Web i usług pulpitu zdalnego są publikowane jako pojedyncza aplikacja z serwerem proxy aplikacji, dzięki czemu można mieć środowisko logowania jednokrotnego między dwiema aplikacjami.

- Serwer proxy aplikacji powinien już zostać [wdrożony](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/rds-in-azure)i [włączony](application-proxy-add-on-premises-application.md).

- W tym scenariuszu założono, że użytkownicy końcowi przechodzą przez program Internet Explorer na pulpitach systemu Windows 7 lub Windows 10, które łączą się za pośrednictwem strony sieci Web usług pulpitu zdalnego. Jeśli chcesz obsługiwać inne systemy operacyjne, zobacz [Obsługa innych konfiguracji klienta](#support-for-other-client-configurations).

- Podczas publikowania sieci Web usług pulpitu zdalnego zaleca się używanie tej samej wewnętrznej i zewnętrznej sieci FQDN. Jeśli wewnętrzne i zewnętrzne nazwy FQDN są różne, należy wyłączyć tłumaczenie nagłówka żądania, aby uniknąć odbierania nieprawidłowych łączy przez klienta. 

- W programie Internet Explorer włącz dodatek RDS ActiveX.

- W przypadku przepływu wstępnego uwierzytelniania usługi Azure AD użytkownicy mogą łączyć się tylko z zasobami opublikowanymi w okienku **RemoteApp i Pulpity.** Użytkownicy nie mogą połączyć się z pulpitem za pomocą okienka **Połącz ze zdalnym komputerem.**

## <a name="deploy-the-joint-rds-and-application-proxy-scenario"></a>Wdrażanie wspólnego scenariusza usług rds i serwera proxy aplikacji

Po skonfigurowaniu usług RDS i usługi Azure AD Application Proxy dla środowiska, wykonaj kroki, aby połączyć dwa rozwiązania. W tych krokach można przejść do publikowania dwóch punktów końcowych usług pulpitu zdalnego (rd Web i rd Gateway) jako aplikacji, a następnie kierowania ruchem na usługi RDS w celu przejścia przez serwer proxy aplikacji.

### <a name="publish-the-rd-host-endpoint"></a>Publikowanie punktu końcowego hosta usług pulpitu zdalnego

1. [Opublikuj nową aplikację serwera proxy aplikacji](application-proxy-add-on-premises-application.md) z następującymi wartościami:
   - Wewnętrzny adres `https://\<rdhost\>.com/`URL: , gdzie `\<rdhost\>` jest wspólny katalog główny współużytkuje sieć Web usług pulpitu zdalnego i bramę usług pulpitu zdalnego.
   - Zewnętrzny adres URL: to pole jest wypełniane automatycznie na podstawie nazwy aplikacji, ale można je zmodyfikować. Użytkownicy przejdą do tego adresu URL podczas uzyskiwania dostępu do usług pulpitu zdalnego.
   - Metoda wstępnej uwierzytelniania: usługa Azure Active Directory
   - Tłumaczenie nagłówków adresów URL: Nie
2. Przypisz użytkowników do opublikowanej aplikacji RD. Upewnij się, że wszyscy mają dostęp do RDS, zbyt.
3. Pozostaw metodę logowania jednokrotnego dla aplikacji jako **azure ad logowania jednokrotnego wyłączone**. Użytkownicy są proszeni o uwierzytelnienie raz w usłudze Azure AD i raz w sieci Web usług pulpitu zdalnego, ale mają logowanie jednokrotne do bramy usług pulpitu zdalnego.
4. Wybierz **pozycję Azure Active Directory**, a następnie **rejestracje aplikacji**. Wybierz aplikację z listy.
5. W obszarze **Zarządzaj**wybierz pozycję **Znakowanie**.
6. Zaktualizuj pole **ADRESU URL strony głównej,** aby wskazywało punkt końcowy sieci Web usług pulpitu zdalnego (np. `https://\<rdhost\>.com/RDWeb`

### <a name="direct-rds-traffic-to-application-proxy"></a>Bezpośredni ruch RDS do serwera proxy aplikacji

Połącz się z wdrożeniem usług pulpitu zdalnego jako administrator i zmień nazwę serwera bramy usług pulpitu zdalnego dla wdrożenia. Ta konfiguracja gwarantuje, że połączenia przechodzą przez usługę serwera proxy aplikacji usługi Azure AD.

1. Połącz się z serwerem usług PULPITU zdalnego z rolą brokera połączeń usług pulpitu zdalnego.
2. Uruchom **Menedżera serwera**.
3. Wybierz **pozycję Usługi pulpitu zdalnego** z okienka po lewej stronie.
4. Wybierz pozycję **Przegląd**.
5. W sekcji Omówienie wdrażania wybierz menu rozwijane i wybierz polecenie **Edytuj właściwości wdrażania**.
6. Na karcie Brama usług pulpitu zdalnego zmień pole **Nazwa serwera** na zewnętrzny adres URL ustawiony dla punktu końcowego hosta usług pulpitu zdalnego w serwerze proxy aplikacji.
7. Zmień pole **Metody logowania** na **Uwierzytelnianie hasłem**.

   ![Ekran Właściwości wdrażania na usługach RDS](./media/application-proxy-integrate-with-remote-desktop-services/rds-deployment-properties.png)

8. Uruchom to polecenie dla każdej kolekcji. Wymień * \<swoją swojąukę i\> * * \<proxyfrontendurl\> * własnymi informacjami. To polecenie umożliwia logowanie jednokrotne między usługą RD Web i bramą usług pulpitu zdalnego i optymalizuje wydajność:

   ```
   Set-RDSessionCollectionConfiguration -CollectionName "<yourcollectionname>" -CustomRdpProperty "pre-authentication server address:s:<proxyfrontendurl>`nrequire pre-authentication:i:1"
   ```

   **Na przykład:**
   ```
   Set-RDSessionCollectionConfiguration -CollectionName "QuickSessionCollection" -CustomRdpProperty "pre-authentication server address:s:https://remotedesktoptest-aadapdemo.msappproxy.net/`nrequire pre-authentication:i:1"
   ```
   >[!NOTE]
   >Powyższe polecenie używa backtick w "'nrequire".

9. Aby zweryfikować modyfikację niestandardowych właściwości RDP, a także wyświetlić zawartość pliku RDP, który zostanie pobrany z rdweb dla tej kolekcji, uruchom następujące polecenie:
    ```
    (get-wmiobject -Namespace root\cimv2\terminalservices -Class Win32_RDCentralPublishedRemoteDesktop).RDPFileContents
    ```

Teraz, po skonfigurowaniu pulpitu zdalnego, usługi Azure AD Application Proxy przejął jako składnik usług PULPITU ZDALNEGO. Można usunąć inne publiczne punkty końcowe skierowane do Internetu na komputerach z siecią Rd Web i bramą usług pulpitu zdalnego.

## <a name="test-the-scenario"></a>Testowanie scenariusza

Przetestuj scenariusz za pomocą programu Internet Explorer na komputerze z systemem Windows 7 lub 10.

1. Przejdź do skonfigurowany zewnętrzny adres URL lub znajdź aplikację w [panelu MyApps](https://myapps.microsoft.com).
2. Zostaniesz poproszony o uwierzytelnienie w usłudze Azure Active Directory. Użyj konta przypisanego do aplikacji.
3. Zostaniesz poproszony o uwierzytelnienie w sieci Web usług pulpitu zdalnego.
4. Po pomyślnym zakończeniu uwierzytelniania RDS można wybrać odpowiedni pulpit lub aplikację i rozpocząć pracę.

## <a name="support-for-other-client-configurations"></a>Obsługa innych konfiguracji klienta

Konfiguracja opisana w tym artykule jest dla użytkowników w systemie Windows 7 lub 10, z programem Internet Explorer oraz dodatkiem RDS ActiveX. Jeśli jednak zajdzie taka potrzeba, możesz obsługiwać inne systemy operacyjne lub przeglądarki. Różnica polega na metodzie uwierzytelniania, której używasz.

| Metoda uwierzytelniania | Obsługiwana konfiguracja klienta |
| --------------------- | ------------------------------ |
| Wstępne uwierzytelnianie    | Windows 7/10 przy użyciu dodatku Internet Explorer + RDS ActiveX |
| Przekazywanie | Każdy inny system operacyjny obsługujący aplikację Pulpit zdalny firmy Microsoft |

Przepływ uwierzytelniania wstępnego oferuje więcej korzyści zabezpieczeń niż przepływ przekazywania. Za pomocą uwierzytelniania wstępnego można używać funkcji uwierzytelniania usługi Azure AD, takich jak logowanie jednokrotne, dostęp warunkowy i weryfikacja dwuetapowa dla zasobów lokalnych. Upewnij się również, że tylko uwierzytelniony ruch dociera do twojej sieci.

Aby użyć uwierzytelniania przekazywanego, istnieją tylko dwie modyfikacje kroków wymienionych w tym artykule:
1. W [ujmowanie punktu końcowego hosta usług pulpitu zdalnego](#publish-the-rd-host-endpoint) krok 1 ustaw metodę preauthentication na **Przekazywanie**.
2. W [bezpośrednim ruchu RDS do serwera proxy aplikacji](#direct-rds-traffic-to-application-proxy)całkowicie pomiń krok 8.

## <a name="next-steps"></a>Następne kroki

[Włączanie dostępu zdalnego do programu SharePoint przy użyciu serwera proxy aplikacji usługi Azure AD](application-proxy-integrate-with-sharepoint-server.md)  
[Zagadnienia dotyczące zabezpieczeń dotyczące zdalnego uzyskiwania dostępu do aplikacji przy użyciu serwera proxy aplikacji usługi Azure AD](application-proxy-security.md)
