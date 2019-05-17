---
title: Publikowanie usług pulpitu zdalnego z serwera Proxy aplikacji usługi Azure AD | Dokumentacja firmy Microsoft
description: Zawiera podstawowe informacje dotyczące łączników serwera Proxy aplikacji usługi Azure AD.
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
ms.date: 06/27/2018
ms.author: mimart
ms.custom: it-pro
ms.reviewer: harshja
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2ecd55cbb3a8eefc150db731901458561d90f033
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2019
ms.locfileid: "65783403"
---
# <a name="publish-remote-desktop-with-azure-ad-application-proxy"></a>Publikowanie usług pulpitu zdalnego z serwerem Proxy aplikacji usługi Azure AD

Usługi pulpitu zdalnego i serwer Proxy aplikacji usługi Azure AD działają razem, aby zwiększyć produktywność pracowników, którzy są poza siecią firmową. 

Odbiorców, w tym artykule przedstawiono:
- Bieżący serwer Proxy aplikacji klientów, którzy chcą oferować większej liczby aplikacji użytkownikom końcowym, publikując aplikacje w środowisku lokalnym za pomocą usług pulpitu zdalnego.
- Obecni klienci usług pulpitu zdalnego, którzy chcą, aby zmniejszyć obszar ataków, ich wdrożenia przy użyciu serwera Proxy aplikacji usługi Azure AD. W tym scenariuszu zapewnia ograniczony zestaw weryfikacji dwuetapowej i kontroli dostępu warunkowego do RDS.

## <a name="how-application-proxy-fits-in-the-standard-rds-deployment"></a>Jak serwer Proxy aplikacji są dopasowane do standardowego wdrożenia usług pulpitu zdalnego

Standardowe wdrożenie usług pulpitu zdalnego obejmuje różnych usług ról usług pulpitu zdalnego w systemie Windows Server. Patrząc [architektury usług pulpitu zdalnego](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/desktop-hosting-logical-architecture), istnieje wiele dostępnych opcji wdrażania. W przeciwieństwie do innych opcji wdrażania usług pulpitu zdalnego [wdrożenia usług pulpitu zdalnego z serwera Proxy aplikacji usługi Azure AD](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/desktop-hosting-logical-architecture) (pokazane na poniższym diagramie) ma stały połączenie wychodzące z serwerem z uruchomioną usługą łącznika. Inne wdrożenia, pozostaw otwarte połączenia przychodzące za pośrednictwem modułu równoważenia obciążenia.

![Serwer Proxy aplikacji znajduje się pomiędzy maszyn wirtualnych usług pulpitu zdalnego i publiczną siecią internet](./media/application-proxy-integrate-with-remote-desktop-services/rds-with-app-proxy.png)

We wdrożeniu usług pulpitu zdalnego roli usług pulpitu zdalnego w sieci Web i roli bramy usług pulpitu zdalnego uruchamiane na maszynach dostępnego z Internetu. Te punkty końcowe są dostępne w następujących sytuacjach:
- Publiczny punkt końcowy do logowania i wyświetlić różne aplikacje lokalne i komputery stacjonarne, które mogą uzyskiwać dostęp do zapewnia użytkownikowi dostęp w sieci Web usług pulpitu zdalnego. Po wybraniu zasobu, z połączeniem RDP jest tworzony za pomocą aplikacji natywnej w systemie operacyjnym.
- Brama usług pulpitu zdalnego jest dostarczany do obrazu po użytkownik uruchamia połączenie RDP. Brama usług pulpitu zdalnego obsługuje zaszyfrowany ruch RDP, dostępne za pośrednictwem Internetu i tłumaczy je na lokalnym serwerze, który użytkownik nawiązuje połączenie z. W tym scenariuszu Brama usług pulpitu zdalnego odbiera ruch pochodzi z serwera Proxy aplikacji usługi AD systemu Azure.

>[!TIP]
>Jeśli nie zostały wdrożone RDS przed lub chcesz uzyskać więcej informacji, zanim rozpoczniesz, Dowiedz się, jak [bezproblemowo wdrożenia usług pulpitu zdalnego za pomocą usługi Azure Resource Manager i portalu Azure Marketplace](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/rds-in-azure).

## <a name="requirements"></a>Wymagania

- Za pomocą klienta inne niż klient sieci web usług pulpitu zdalnego, ponieważ klient sieci web nie obsługuje serwera Proxy aplikacji.

- Punkty końcowe sieci Web usług pulpitu zdalnego i bramy usług pulpitu zdalnego muszą znajdować się na na tym samym komputerze, a także z typowy katalog główny. Sieci Web usług pulpitu zdalnego i bramy usług pulpitu zdalnego są publikowane jako pojedynczą aplikacją przy użyciu serwera Proxy aplikacji, dzięki czemu mają funkcji logowania jednokrotnego między dwiema aplikacjami.

- Konto powinno mieć już [wdrożonych usług pulpitu zdalnego](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/rds-in-azure), i [włączono serwer Proxy aplikacji](application-proxy-add-on-premises-application.md).

- W tym scenariuszu przyjmuje go użytkownikom końcowym za pomocą programu Internet Explorer na komputerach stacjonarnych Windows 7 lub Windows 10, łączących się za pośrednictwem strony sieci Web usług pulpitu zdalnego. Jeśli potrzebujesz do obsługi innych systemów operacyjnych, zobacz [Obsługa innych konfiguracji klienta](#support-for-other-client-configurations).

- Podczas publikowania usług pulpitu zdalnego w sieci Web, zaleca się używać tej samej nazwy FQDN wewnętrznych i zewnętrznych. Różnią się nazwy FQDN wewnętrznych i zewnętrznych należy wyłączyć żądania tłumaczenia nagłówka, aby uniknąć klienta odbieranie nieprawidłowe linki. 

- W programie Internet Explorer należy włączyć dodatek ActiveX usług pulpitu zdalnego.

## <a name="deploy-the-joint-rds-and-application-proxy-scenario"></a>Wdrażanie wspólnego scenariusz usług pulpitu zdalnego i serwer Proxy aplikacji

Po skonfigurowaniu usług pulpitu zdalnego i aplikacji serwera Proxy Azure AD dla danego środowiska, postępuj zgodnie z instrukcjami, aby połączyć dwa rozwiązania. Te kroki przedstawiają publikowania dwa przeznaczonych dla sieci web usług pulpitu zdalnego punkty końcowe (sieci Web usług pulpitu zdalnego i bramy usług pulpitu zdalnego) jako aplikacje, a następnie kierowanie ruchu na usługi RDS za pośrednictwem serwera Proxy aplikacji.

### <a name="publish-the-rd-host-endpoint"></a>Publikowanie usług pulpitu zdalnego punktu końcowego hosta

1. [Opublikuj nową aplikację serwera Proxy aplikacji](application-proxy-add-on-premises-application.md) z następującymi wartościami:
   - Wewnętrzny adres URL: `https://\<rdhost\>.com/`, gdzie `\<rdhost\>` jest typowy katalog główny, które współdzielą sieci Web usług pulpitu zdalnego i bramy usług pulpitu zdalnego.
   - Zewnętrzny adres URL: To pole jest wypełniane automatycznie na podstawie nazwy aplikacji, ale można go zmodyfikować. Użytkownicy zaczną się do tego adresu URL, przy uzyskiwaniu dostępu RDS.
   - Metoda uwierzytelniania wstępnego: Usługa Azure Active Directory
   - Tłumaczenie nagłówki URL: Nie
2. Przypisywanie użytkowników do opublikowanej aplikacji usług pulpitu zdalnego. Upewnij się, że dostęp do usług pulpitu zdalnego, wszystkie one mają zbyt.
3. Pozostaw pojedynczej metody logowania jednokrotnego dla aplikacji jako **usługi Azure AD logowanie jednokrotne wyłączone**. Użytkownicy są proszeni o uwierzytelniania do usługi Azure AD i w sieci Web usług pulpitu zdalnego, ale występuje logowanie jednokrotne do bramy usług pulpitu zdalnego.
4. Przejdź do **usługi Azure Active Directory** > **rejestracje aplikacji** > *aplikacji* > **ustawienia**.
5. Wybierz **właściwości** i zaktualizuj **adres URL strony głównej** pola, aby wskazać na punkt końcowy usługi sieci Web usług pulpitu zdalnego (takich jak `https://\<rdhost\>.com/RDWeb`).

### <a name="direct-rds-traffic-to-application-proxy"></a>Bezpośrednie kierowanie ruchu usług pulpitu zdalnego serwera proxy aplikacji

Do wdrożenia usług pulpitu zdalnego z uprawnieniami administracyjnymi i Zmień nazwę serwera bramy usług pulpitu zdalnego dla wdrożenia. Ta konfiguracja gwarantuje, że połączenia przechodzą przez usługę serwera Proxy aplikacji usługi Azure AD.

1. Połącz na serwer usług pulpitu zdalnego, na którym działa rola brokera połączeń usług pulpitu zdalnego.
2. Uruchom **Menedżera serwera**.
3. Wybierz **usług pulpitu zdalnego** w okienku po lewej stronie.
4. Wybierz pozycję **Przegląd**.
5. W sekcji Przegląd wdrożenia wybierz opcję menu rozwijane i wybierz polecenie **Edytuj właściwości wdrożenia**.
6. Na karcie bramy usług pulpitu zdalnego, należy zmienić **nazwy serwera** pole zewnętrzny adres URL dla usług pulpitu zdalnego hosta punktu końcowego na serwerze Proxy aplikacji.
7. Zmiana **logowania metoda** pole **uwierzytelniania za pomocą hasła**.

   ![Właściwości ekranu wdrożenia usług pulpitu zdalnego](./media/application-proxy-integrate-with-remote-desktop-services/rds-deployment-properties.png)

8. Uruchom to polecenie dla każdej kolekcji. Zastąp *\<yourcollectionname\>* i *\<proxyfrontendurl\>* odpowiednimi informacjami. To polecenie umożliwia logowanie jednokrotne między wersjami Web usług pulpitu zdalnego i bramy usług pulpitu zdalnego oraz optymalizację wydajności:

   ```
   Set-RDSessionCollectionConfiguration -CollectionName "<yourcollectionname>" -CustomRdpProperty "pre-authentication server address:s:<proxyfrontendurl>`nrequire pre-authentication:i:1"
   ```

   **Na przykład:**
   ```
   Set-RDSessionCollectionConfiguration -CollectionName "QuickSessionCollection" -CustomRdpProperty "pre-authentication server address:s:https://remotedesktoptest-aadapdemo.msappproxy.net/`nrequire pre-authentication:i:1"
   ```
   >[!NOTE]
   >Powyższe polecenie używa początkowych w "" nrequire ".

9. Sprawdź modyfikacja właściwości niestandardowe RDP jak wyświetlić zawartość pliku RDP, które zostaną pobrane z RDWeb dla tej kolekcji, uruchom następujące polecenie:
    ```
    (get-wmiobject -Namespace root\cimv2\terminalservices -Class Win32_RDCentralPublishedRemoteDesktop).RDPFileContents
    ```

Teraz, gdy skonfigurowano pulpitu zdalnego, serwer Proxy aplikacji usługi AD Azure przejmuje jako składnik internetowy RDS. Możesz usunąć pozostałe publicznego Internetu punkty końcowe na maszynach sieci Web usług pulpitu zdalnego i bramy usług pulpitu zdalnego.

## <a name="test-the-scenario"></a>Testowanie scenariusza

Testowanie scenariusza za pomocą programu Internet Explorer na komputerze 10 lub Windows 7.

1. Przejdź do zewnętrznego adresu URL, możesz skonfigurować lub Znajdź swoją aplikację w [panelu MyApps](https://myapps.microsoft.com).
2. Zostanie wyświetlony monit uwierzytelnienia w usłudze Azure Active Directory. Użyj konta, które są przypisane do aplikacji.
3. Prośba do uwierzytelniania w sieci Web usług pulpitu zdalnego.
4. Po pomyślnym uwierzytelniania usług pulpitu zdalnego, można wybrać pulpitu lub aplikacji, które mają i rozpocząć pracę.

## <a name="support-for-other-client-configurations"></a>Obsługa innych konfiguracji klienta

Konfiguracja opisane w tym artykule jest przeznaczona dla użytkowników na Windows 7 lub 10, za pomocą programu Internet Explorer, a także dodatek ActiveX usług pulpitu zdalnego. Jeśli zachodzi potrzeba, jednak można obsługiwać innych systemów operacyjnych lub przeglądarek. Różnica polega na w metodzie uwierzytelniania, którego używasz.

| Metoda uwierzytelniania | Konfiguracja klienta obsługiwanych |
| --------------------- | ------------------------------ |
| Uwierzytelnianie wstępne    | Windows 7/10 przy użyciu programu Internet Explorer + dodatek ActiveX usług pulpitu zdalnego |
| Przekazywanie | Inny system operacyjny, który obsługuje aplikację Pulpit zdalny firmy Microsoft |

Przepływ uwierzytelniania wstępnego oferuje więcej korzyści w zakresie zabezpieczeń niż przepływ przekazującego. Przy użyciu wstępnego uwierzytelniania można użyć funkcji uwierzytelniania usługi Azure AD, takich jak logowanie jednokrotne dostępu warunkowego i weryfikację dwuetapową dla zasobów w środowisku lokalnym. Musisz także upewnić się, tylko uwierzytelnianego przypada ruchu w sieci.

Aby użyć uwierzytelniania przekazującego, istnieją tylko dwa modyfikacji kroków opisanych w tym artykule:
1. W [publikowania usług pulpitu zdalnego punktu końcowego hosta](#publish-the-rd-host-endpoint) kroku 1, ustaw metoda uwierzytelniania wstępnego **przekazywanie**.
2. W [ruch bezpośredni usług pulpitu zdalnego do serwera Proxy aplikacji](#direct-rds-traffic-to-application-proxy), całkowicie pominąć krok 8.

## <a name="next-steps"></a>Kolejne kroki

[Zdalny dostęp do programu SharePoint przy użyciu serwera Proxy aplikacji usługi Azure AD](application-proxy-integrate-with-sharepoint-server.md)  
[Zagadnienia dotyczące zabezpieczeń do uzyskiwania dostępu do aplikacji zdalnie przy użyciu serwera Proxy aplikacji usługi Azure AD](application-proxy-security.md)
