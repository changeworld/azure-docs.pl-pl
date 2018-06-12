---
title: Publikowanie pulpitu zdalnego z serwera Proxy aplikacji usługi Azure AD | Dokumentacja firmy Microsoft
description: Zawiera podstawowe informacje dotyczące serwera Proxy aplikacji usługi Azure AD łączników.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/11/2018
ms.author: barbkess
ms.custom: it-pro
ms.reviewer: harshja
ms.openlocfilehash: 5c6d6b9580b123dafb03af2acc885d2416ca2a56
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35292545"
---
# <a name="publish-remote-desktop-with-azure-ad-application-proxy"></a>Publikowanie pulpitu zdalnego z serwerem Proxy aplikacji usługi Azure AD

Usługi pulpitu zdalnego i serwera Proxy aplikacji usługi Azure AD współdziałają ze sobą, aby zwiększyć produktywność pracowników, którzy są poza siecią firmową. 

Docelowa grupa odbiorców tego artykułu jest:
- Bieżący serwer Proxy aplikacji klientów, którzy chcą oferować więcej aplikacji dla użytkowników końcowych przez publikowanie aplikacji lokalnych za pomocą usług pulpitu zdalnego.
- Bieżącego klientów usług pulpitu zdalnego, którzy chcą zmniejszyć obszar ataków, ich wdrożenia przy użyciu serwera Proxy aplikacji usługi Azure AD. W tym scenariuszu zapewnia ograniczony zestaw weryfikacji dwuetapowej i kontroli dostępu warunkowego do usług pulpitu zdalnego.

## <a name="how-application-proxy-fits-in-the-standard-rds-deployment"></a>Jak serwer Proxy aplikacji mieści się w standardowe wdrożenie usług pulpitu zdalnego

Standardowe wdrożenie usług pulpitu zdalnego obejmuje różnych usług ról usług pulpitu zdalnego w systemie Windows Server. Spojrzenie na [architektury usług pulpitu zdalnego](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/desktop-hosting-logical-architecture), istnieje wiele opcji wdrażania. W przeciwieństwie do innych opcji wdrażania usług pulpitu zdalnego [wdrożenie usług pulpitu zdalnego z serwera Proxy aplikacji usługi Azure AD](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/desktop-hosting-logical-architecture) (przedstawione na poniższym diagramie) ma stałego połączenia wychodzącego z serwera z uruchomioną usługą łącznika. Inne wdrożenia, pozostaw otwarte połączenia przychodzące za pośrednictwem usługi równoważenia obciążenia.

![Serwer Proxy aplikacji znajduje się pomiędzy maszyny Wirtualnej usług pulpitu zdalnego i publicznej sieci internet](./media/application-proxy-integrate-with-remote-desktop-services/rds-with-app-proxy.png)

We wdrożeniu usług pulpitu zdalnego roli usług pulpitu zdalnego w sieci Web i roli bramy usług pulpitu zdalnego uruchamiania na komputerach połączonych z Internetem. Te punkty końcowe są dostępne w następujących sytuacjach:
- Sieci Web usług pulpitu zdalnego udostępnia użytkownikowi publiczny punkt końcowy logować się i wyświetlić aplikacji lokalnych i komputery stacjonarne, które mogą uzyskiwać dostęp do różnych. Po wybraniu zasobu, połączenie RDP jest tworzony za pomocą aplikacji natywnej na system operacyjny.
- Bramy usług pulpitu zdalnego jest dostarczany do obrazu, gdy użytkownik uruchamia połączenie RDP. Brama usług pulpitu zdalnego obsługuje zaszyfrowanych RDP ruch w Internecie i tłumaczy je na serwerze lokalnym, które użytkownik nawiązuje połączenie z. W tym scenariuszu ruchu, który odbiera bramy usług pulpitu zdalnego, pochodzi z serwera Proxy aplikacji usługi AD platformy Azure.

>[!TIP]
>Dowiedz się, jeśli nie zostały wdrożone RDS przed lub chcesz uzyskać więcej informacji, aby rozpocząć, jak [bezproblemowo wdrożenia usług pulpitu zdalnego z usługi Azure Resource Manager i portalu Azure Marketplace](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/rds-in-azure).

## <a name="requirements"></a>Wymagania

- Punktów końcowych sieci Web usług pulpitu zdalnego i Brama usług pulpitu zdalnego musi znajdować się na na tym samym komputerze, a także z typowy katalog główny. Sieci Web usług pulpitu zdalnego i Brama usług pulpitu zdalnego są publikowane jako pojedynczą aplikacją przy użyciu serwera Proxy aplikacji, dzięki czemu może mieć pojedynczy jednokrotnego między dwiema aplikacjami.

- Ma już [wdrożone RDS](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/rds-in-azure), i [włączono serwer Proxy aplikacji](application-proxy-enable.md).

- W tym scenariuszu przyjęto go użytkownikom końcowym za pomocą programu Internet Explorer na pulpitach systemu Windows 7 lub Windows 10, które łączenie się za pośrednictwem strony sieci Web usług pulpitu zdalnego. Jeśli potrzebujesz do obsługi innych systemów operacyjnych, zobacz [obsługę innych konfiguracji klienta](#support-for-other-client-configurations).

- Podczas publikowania usług pulpitu zdalnego w sieci Web, zaleca się używania taką samą nazwą FQDN wewnętrznych i zewnętrznych. Różnią się nazwy FQDN wewnętrznych i zewnętrznych należy wyłączyć żądania tłumaczenie nagłówka, aby uniknąć klienta odbieranie nieprawidłowe linki. 

- W programie Internet Explorer należy włączyć dodatek ActiveX usług pulpitu zdalnego.

## <a name="deploy-the-joint-rds-and-application-proxy-scenario"></a>Wdrażanie wspólnego scenariusza usług pulpitu zdalnego i serwera Proxy aplikacji

Po skonfigurowaniu usługi pulpitu zdalnego i aplikacji serwera Proxy Azure AD w danym środowisku, wykonaj kroki, aby połączyć dwa rozwiązania. Te kroki przeprowadzenie publikowania dwa udostępnianych w sieci web usług pulpitu zdalnego punkty końcowe (sieci Web usług pulpitu zdalnego i Brama usług pulpitu zdalnego) jako aplikacje, a następnie kierowanie ruchu w sieci RDS przechodzić przez serwer Proxy aplikacji.

### <a name="publish-the-rd-host-endpoint"></a>Publikowanie usług pulpitu zdalnego punktu końcowego hosta

1. [Opublikuj nową aplikację serwera Proxy aplikacji](application-proxy-publish-azure-portal.md) z następującymi wartościami:
   - Wewnętrzny adres URL: https://\<rdhost\>.com /, gdzie \<rdhost\> jest typowy katalog główny, który sieci Web usług pulpitu zdalnego i Brama usług pulpitu zdalnego udziału.
   - Zewnętrzny adres URL: To pole jest wypełniane automatycznie na podstawie nazwy aplikacji, ale można go zmodyfikować. Użytkownicy przechodzą do tego adresu URL przy uzyskiwaniu dostępu do usług pulpitu zdalnego.
   - Metoda uwierzytelniania wstępnego: Azure Active Directory
   - Tłumaczenie nagłówków adresu URL: nie
2. Przypisywanie użytkowników w opublikowanej aplikacji usług pulpitu zdalnego. Upewnij się, że wszystkie mają dostęp do usług pulpitu zdalnego, zbyt.
3. Pozostaw jednej metody logowania jednokrotnego dla aplikacji jako **usługi Azure AD rejestracji jednokrotnej wyłączone**. Użytkownicy są proszeni o uwierzytelniania do usługi Azure AD i usług pulpitu zdalnego w sieci Web, ale ma rejestracji jednokrotnej do bramy usług pulpitu zdalnego.
4. Przejdź do **usługi Azure Active Directory** > **rejestracji aplikacji** > *aplikacji* > **ustawienia**.
5. Wybierz **właściwości** i zaktualizuj **adres URL strony głównej** pola, aby wskazywał punktu końcowego sieci Web usług pulpitu zdalnego (takich jak https://\<rdhost\>.com/RDWeb).

### <a name="direct-rds-traffic-to-application-proxy"></a>Bezpośrednie ruch usługi pulpitu zdalnego do serwera Proxy aplikacji

Połącz do wdrożenia usług pulpitu zdalnego jako administrator i Zmień nazwę serwera bramy usług pulpitu zdalnego dla wdrożenia. Ta konfiguracja zapewnia, że połączenia przechodzą przez usługę serwera Proxy aplikacji usługi Azure AD.

1. Połączenie do serwera usług pulpitu zdalnego z roli Broker połączeń usług pulpitu zdalnego.
2. Uruchom **Menedżera serwera**.
3. Wybierz **usług pulpitu zdalnego** w okienku po lewej stronie.
4. Wybierz pozycję **Przegląd**.
5. W sekcji Przegląd wdrożenia, wybierz menu rozwijane i wybierz polecenie **Edytuj właściwości wdrożenia**.
6. Na karcie bramy usług pulpitu zdalnego, należy zmienić **nazwy serwera** do zewnętrznego adresu URL dla punktu końcowego hosta usług pulpitu zdalnego w serwer Proxy aplikacji.
7. Zmiany **logowania metody** do **uwierzytelniania hasła**.

  ![Ekran właściwości wdrożenia na usługi pulpitu zdalnego](./media/application-proxy-integrate-with-remote-desktop-services/rds-deployment-properties.png)

8. Uruchom to polecenie dla każdej kolekcji. Zastąp *\<yourcollectionname\>* i *\<proxyfrontendurl\>* odpowiednimi informacjami. To polecenie umożliwia logowanie jednokrotne między sieci Web usług pulpitu zdalnego i Brama usług pulpitu zdalnego oraz optymalizację wydajności:

   ```
   Set-RDSessionCollectionConfiguration -CollectionName "<yourcollectionname>" -CustomRdpProperty "pre-authentication server address:s:<proxyfrontendurl>`nrequire pre-authentication:i:1"
   ```

   **Na przykład:**
   ```
   Set-RDSessionCollectionConfiguration -CollectionName "QuickSessionCollection" -CustomRdpProperty "pre-authentication server address:s:https://remotedesktoptest-aadapdemo.msappproxy.net/`nrequire pre-authentication:i:1"
   ```

9. Aby zweryfikować zmiany właściwości niestandardowe RDP, a także wyświetlać zawartość pliku RDP, które zostaną pobrane z RDWeb dla tej kolekcji, uruchom następujące polecenie:
    ```
    (get-wmiobject -Namespace root\cimv2\terminalservices -Class Win32_RDCentralPublishedRemoteDesktop).RDPFileContents
    ```

Teraz, gdy skonfigurowano pulpitu zdalnego, serwer Proxy aplikacji usługi Azure AD przejmuje jako składnik internetowy usług pulpitu zdalnego. Należy usunąć pozostałe publiczny internetowy punkty końcowe na maszynach w sieci Web usług pulpitu zdalnego i Brama usług pulpitu zdalnego.

## <a name="test-the-scenario"></a>Przetestowania tego scenariusza

Przetestowania tego scenariusza z programu Internet Explorer na komputerze 10 lub Windows 7.

1. Przejdź do zewnętrznego adresu URL, można skonfigurować lub znajdowania aplikacji w [panelu MyApps](https://myapps.microsoft.com).
2. Prośba do uwierzytelniania usługi Azure Active Directory. Użyj konta, które są przypisane do aplikacji.
3. Prośba do uwierzytelniania w sieci Web usług pulpitu zdalnego.
4. Po pomyślnym uwierzytelniania usług pulpitu zdalnego, można wybrać pulpitu lub aplikacji, które mają i rozpocząć pracę.

## <a name="support-for-other-client-configurations"></a>Obsługę innych konfiguracji klienta

Konfiguracja opisane w tym artykule jest przeznaczony dla użytkowników w systemie Windows 7 lub 10, w przypadku programu Internet Explorer oraz dodatek ActiveX usług pulpitu zdalnego. Jeśli potrzebujesz, jednak można obsługiwać innych systemów operacyjnych i przeglądarek. Jest to różnica w metodzie uwierzytelniania, którego używasz.

| Metoda uwierzytelniania | Obsługiwany klient konfiguracji |
| --------------------- | ------------------------------ |
| Uwierzytelnianie wstępne    | Windows 7/10 przy użyciu programu Internet Explorer + dodatek ActiveX usług pulpitu zdalnego |
| Przekazywanie | Dowolny inny system operacyjny obsługujący aplikacji pulpitu zdalnego firmy Microsoft |

Przepływ uwierzytelniania wstępnego oferuje więcej korzyści w zakresie zabezpieczeń niż przepływu passthrough. Przy użyciu wstępnego uwierzytelniania można użyć funkcji uwierzytelniania usługi Azure AD, takich jak logowanie jednokrotne dostępu warunkowego i weryfikacji dwuetapowej dla zasobów lokalnych. Można również upewnij się, że tylko uwierzytelnianego osiągnie ruchu w sieci.

Aby używać uwierzytelniania passthrough, istnieją tylko dwie zmiany, aby wszystkie czynności opisane w tym artykule:
1. W [publikowania usług pulpitu zdalnego punktu końcowego hosta](#publish-the-rd-host-endpoint) kroku 1, ustaw metoda uwierzytelniania wstępnego **Passthrough**.
2. W [ruch RDS bezpośrednio do serwera Proxy aplikacji](#direct-rds-traffic-to-application-proxy), całkowicie pominąć krok 8.

## <a name="next-steps"></a>Kolejne kroki

[Włączenie dostępu zdalnego do programu SharePoint przy użyciu serwera Proxy aplikacji usługi Azure AD](application-proxy-integrate-with-sharepoint-server.md)  
[Zagadnienia dotyczące zabezpieczeń do uzyskiwania dostępu do aplikacji zdalnie przy użyciu serwera Proxy aplikacji usługi Azure AD](application-proxy-security.md)
