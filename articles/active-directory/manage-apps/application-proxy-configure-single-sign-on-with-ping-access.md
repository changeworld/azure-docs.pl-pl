---
title: Uwierzytelnianie oparte na nagłówka z usługą PingAccess dla serwera Proxy aplikacji usługi Azure AD | Dokumentacja firmy Microsoft
description: Publikowanie aplikacji z usługą PingAccess i serwera Proxy aplikacji obsługują uwierzytelnianie oparte na nagłówka.
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
ms.date: 05/08/2019
ms.author: celested
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 365f017fe7d71500c17d0a9ccd9c5a0a26a78b75
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/21/2019
ms.locfileid: "65989565"
---
# <a name="header-based-authentication-for-single-sign-on-with-application-proxy-and-pingaccess"></a>Na podstawie nagłówka uwierzytelniania dla logowania jednokrotnego przy użyciu serwera Proxy aplikacji i PingAccess

Serwer Proxy aplikacji usługi Azure Active Directory (Azure AD) nawiązała współpracę z usługą PingAccess tak, aby usługi Azure AD klientom dostęp do kilku aplikacji. Rozwija PingAccess [istniejące oferty serwera Proxy aplikacji](application-proxy.md) obejmujący dostęp pojedynczego logowania jednokrotnego do aplikacji, które używające nagłówków na potrzeby uwierzytelniania.

## <a name="whats-pingaccess-for-azure-ad"></a>Co to jest oprogramowanie PingAccess dla usługi Azure AD?

Z usługą PingAccess dla usługi Azure AD możesz udzielić użytkownikom dostępu i logowania jednokrotnego (SSO) do aplikacji, które używające nagłówków na potrzeby uwierzytelniania. Serwer Proxy aplikacji traktuje te aplikacje, takie jak każdy inny, za pomocą usługi Azure AD do uwierzytelniania dostępu i następnie przekazywanie ruchu za pośrednictwem usługi łącznika. Usługa PingAccess znajduje się przed aplikacje i przekształca token dostępu z usługi Azure AD w nagłówku. Następnie aplikacja odbiera uwierzytelniania w formacie, który może odczytać.

Użytkownicy nie będą zauważyć nic innego po zalogowaniu się do użycia aplikacje firmowe. One nadal pracować z dowolnego miejsca na dowolnym urządzeniu. Łączniki serwera Proxy aplikacji bezpośrednie zdalny ruch do wszystkich aplikacji, bez względu na ich typ uwierzytelniania, więc one będzie nadal równoważyć obciążenia automatycznie.

## <a name="how-do-i-get-access"></a>Jak uzyskać dostęp?

Ponieważ w tym scenariuszu pochodzi z partnerstwa między usługi Azure Active Directory i PingAccess, potrzebujesz licencji dla obu usług. Jednak subskrypcje usługi Azure Active Directory — wersja Premium obejmują licencję PingAccess podstawowe, który obejmuje maksymalnie 20 aplikacji. Jeśli trzeba opublikować więcej niż 20 aplikacji opartej na nagłówkach, możesz kupić dodatkową licencję z usługą PingAccess.

Aby uzyskać więcej informacji, zobacz [Wersje usługi Azure Active Directory](../fundamentals/active-directory-whatis.md).

## <a name="publish-your-application-in-azure"></a>Publikuj swoje aplikacje na platformie Azure

Ten artykuł jest przeznaczony dla użytkowników opublikować aplikację w tym scenariuszu po raz pierwszy. Oprócz szczegółowych informacji na temat publikowania kroki, prowadzi użytkownika w środowisku wprowadzenie zarówno serwer Proxy aplikacji, jak i rozwiązanie PingAccess. Jeśli skonfigurowano już obie te usługi, ale przypomnienia informacji na temat publikowania kroków, przejdź do [Dodaj aplikację do usługi Azure AD przy użyciu serwera Proxy aplikacji](#add-your-application-to-azure-ad-with-application-proxy) sekcji.

> [!NOTE]
> Ponieważ ten scenariusz jest partnerstwa między usługą Azure AD i PingAccess, istnieją niektóre instrukcje w witrynie Ping Identity.

### <a name="install-an-application-proxy-connector"></a>Zainstaluj łącznik serwera Proxy aplikacji

Jeśli włączono serwer Proxy aplikacji włączony i już zainstalowano łącznik, możesz pominąć tę sekcję i przejdź do [Dodaj aplikację do usługi Azure AD przy użyciu serwera Proxy aplikacji](#add-your-application-to-azure-ad-with-application-proxy).

Łącznik serwera Proxy aplikacji jest usługę systemu Windows Server, która kieruje ruch z pracownicy zdalni do opublikowanej aplikacji. Aby uzyskać szczegółowe instrukcje dotyczące instalacji, zobacz [samouczka: Dodawanie aplikacji w środowisku lokalnym dostępu zdalnego za pośrednictwem serwera Proxy aplikacji w usłudze Azure Active Directory](application-proxy-add-on-premises-application.md).

1. Zaloguj się do [portalu Azure Active Directory](https://aad.portal.azure.com/) jako administrator aplikacji. **Centrum administracyjne usługi Azure Active Directory** zostanie wyświetlona strona.
2. Wybierz **usługi Azure Active Directory** > **serwera proxy aplikacji** > **Pobierz usługę łącznika**. **Pobieranie łącznika serwera Proxy aplikacji** zostanie wyświetlona strona.

   ![Pobieranie łącznika serwera proxy aplikacji](./media/application-proxy-configure-single-sign-on-with-ping-access/application-proxy-connector-download.png)
3. Postępuj zgodnie z instrukcjami instalacji.

Pobieranie łącznika automatycznie należy włączyć serwer Proxy aplikacji dla katalogu, ale jeśli nie, możesz wybrać **Włączanie serwera Proxy aplikacji**.

### <a name="add-your-application-to-azure-ad-with-application-proxy"></a>Dodawanie aplikacji do usługi Azure AD przy użyciu serwera Proxy aplikacji

Istnieją dwie akcje, które należy wykonać w witrynie Azure portal. Najpierw musisz opublikować aplikację przy użyciu serwera Proxy aplikacji. Następnie należy zebrać pewne informacje o aplikacji, która można użyć podczas czynności PingAccess.

#### <a name="publish-your-application"></a>Publikowanie aplikacji

Najpierw należy opublikować aplikację. Ta akcja wymaga:

- Dodawanie aplikacji lokalnych do usługi Azure AD
- Przypisanie użytkownika do testowania aplikacji i wybierając opartej na nagłówkach logowania jednokrotnego
- Konfigurowanie adresu URL przekierowania aplikacji
- Udzielanie uprawnień dla użytkowników i innych aplikacji na używanie aplikacji w środowisku lokalnym

Aby opublikować aplikację w środowisku lokalnym:

1. Jeśli nie jest w ostatniej sekcji, zaloguj się do [portalu Azure Active Directory](https://aad.portal.azure.com/) jako administrator aplikacji.
2. Wybierz **aplikacje dla przedsiębiorstw** > **nową aplikację** > **aplikacje lokalne**. **Dodaj własną aplikację w środowisku lokalnym** zostanie wyświetlona strona.

   ![Dodaj własną aplikację lokalną](./media/application-proxy-configure-single-sign-on-with-ping-access/add-your-own-on-premises-application.png)
3. Wypełnij wymagane pola informacji o nowej aplikacji. Użyj wskazówek poniższych ustawień.

   > [!NOTE]
   > Aby uzyskać bardziej szczegółowy przewodnik dotyczący tego kroku, zobacz [Dodawanie aplikacji lokalnych do usługi Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad).

   1. **Wewnętrzny adres URL**: Zwykle należy podać adres URL, który umożliwia przejście do strony logowania w aplikacji podczas korzystania z sieci firmowej. W tym scenariuszu łącznika musi traktować PingAccess proxy jako pierwszej strony aplikacji. Użyj tego formatu: `https://<host name of your PingAccess server>:<port>`. Numer portu to 3000 domyślnie, ale można go skonfigurować w PingAccess.

      > [!WARNING]
      > Dla tego typu logowania jednokrotnego, wewnętrzny adres URL musi używać `https` i nie można użyć `http`.

   2. **Metoda wstępnego uwierzytelnienia**: Choose **Azure Active Directory**.
   3. **Przetłumacz URL w nagłówkach**: Wybierz **nie**.

   > [!NOTE]
   > Jeśli jest to swoją pierwszą aplikację, należy użyć portu 3000 do uruchamiania i wróć, aby zaktualizować to ustawienie, jeśli zmienisz konfigurację PingAccess. Dla kolejnych aplikacji port będą musiały być zgodne odbiornika skonfigurowaniu PingAccess. Dowiedz się więcej o [słuchaczy w PingAccess](https://documentation.pingidentity.com/pingaccess/pa31/index.shtml#Listeners.html).
4. Wybierz pozycję **Dodaj**. Zostanie wyświetlona strona omówienie nowej aplikacji.

Teraz przypisać użytkownika do testowania aplikacji i wybierz opartej na nagłówkach logowania jednokrotnego:

1. Na pasku bocznym aplikacji wybierz **użytkowników i grup** > **Dodaj użytkownika** > **użytkowników i grup (\<numer > wybrany)** . Można wybrać z zostanie wyświetlona lista użytkowników i grup.

   ![Użytkownicy i grupy](./media/application-proxy-configure-single-sign-on-with-ping-access/users-and-groups.png)
2. Wybierz użytkownika do testowania aplikacji, a następnie wybierz pozycję **wybierz**. Upewnij się, że to konto testu ma dostęp do aplikacji w środowisku lokalnym.
3. Wybierz opcję **Przypisz**.
4. Na pasku bocznym aplikacji wybierz **logowanie jednokrotne** > **opartej na nagłówkach**.

   > [!TIP]
   > Jeśli po raz pierwszy przy użyciu opartej na nagłówkach logowania jednokrotnego, musisz zainstalować oprogramowanie PingAccess. Aby upewnić się, że Twoja subskrypcja platformy Azure jest automatycznie kojarzony z instalacji oprogramowanie PingAccess, użyj łącza w tym — strona logowanie jednokrotne można pobrać oprogramowanie PingAccess. Możesz teraz otworzyć witryny pobierania lub późniejszy powrót do tej strony.

   ![Logowanie na podstawie nagłówka](./media/application-proxy-configure-single-sign-on-with-ping-access/sso-header.png)
5. Wybierz pozycję **Zapisz**.

Następnie upewnij się, że Twojego przekierowania, który jest ustawiony adres URL do zewnętrznego adresu URL:

1. Z **Centrum administracyjne usługi Azure Active Directory** bocznym wybierz opcję **usługi Azure Active Directory** > **rejestracje aplikacji**. Zostanie wyświetlona lista aplikacji.

   ![Rejestracje aplikacji](./media/application-proxy-configure-single-sign-on-with-ping-access/app-registrations.png)
2. Wybierz swoją aplikację.
3. Wybierz łącze obok **identyfikatory URI przekierowań**, którym wyświetlana jest liczba przekierowania URI zdefiniowana w sieci web i klientów publicznych.  **\<Nazwa aplikacji >-uwierzytelniania** zostanie wyświetlona strona.
4. Sprawdź, czy zewnętrzny adres URL, przypisana do starszej aplikacji jest **identyfikatory URI przekierowań** listy. Jeśli nie, Dodaj zewnętrznego adresu URL teraz, przy użyciu przekierowania URI **Web**i wybierz **Zapisz**.

Na koniec należy skonfigurować aplikację w środowisku lokalnym, aby użytkownicy mają dostęp do odczytu i inne aplikacje mają dostęp do odczytu/zapisu:

1. Z **rejestracje aplikacji** paska bocznego dla aplikacji, wybierz **uprawnienia do interfejsu API** > **Dodaj uprawnienia**  >   **Interfejsy API firmy Microsoft** > **programu Microsoft Graph**. **Uprawnienia do żądania interfejsu API** stronie **programu Microsoft Graph** pojawi się, który zawiera interfejsy API dla Windows Azure Active Directory.

   ![Żądanie uprawnień interfejsu API](./media/application-proxy-configure-single-sign-on-with-ping-access/required-permissions.png)
2. Wybierz **delegowane uprawnienia** > **użytkownika** > **User.Read**.
3. Wybierz **uprawnienia aplikacji** > **aplikacji** > **Application.ReadWrite.All**.
4. Wybierz **Dodaj uprawnienia**.
5. W **uprawnienia do interfejsu API** wybierz opcję **udzielić zgody administratora, aby uzyskać \<swoją nazwę katalogu >**.

#### <a name="collect-information-for-the-pingaccess-steps"></a>Gromadzenie informacji o krokach PingAccess

Należy zebrać następujące trzy informacje (wszystkie identyfikatory GUID) do skonfigurowania aplikacji z usługą PingAccess:

| Nazwa pola w usłudze Azure AD | Nazwa pola PingAccess | Format danych |
| --- | --- | --- |
| **Identyfikator aplikacji (klienta)** | **Identyfikator klienta** | Identyfikator GUID |
| **Identyfikator katalogu (dzierżawcy)** | **Wystawcy** | Identyfikator GUID |
| `PingAccess key` | **Client Secret (Wpis tajny klienta)** | Losowy ciąg |

Do zebrania tych informacji:

1. Z **Centrum administracyjne usługi Azure Active Directory** bocznym wybierz opcję **usługi Azure Active Directory** > **rejestracje aplikacji**. Zostanie wyświetlona lista aplikacji.
2. Wybierz swoją aplikację. **Rejestracje aplikacji** aplikacja pojawi się strona.

   ![Omówienie usługi Rejestracja aplikacji](./media/application-proxy-configure-single-sign-on-with-ping-access/registration-overview-for-an-application.png)
3. Obok pozycji **identyfikator aplikacji (klienta)** wartości, wybierz opcję **Kopiuj do Schowka** ikonę, a następnie skopiuj i zapisz go. Później określić tę wartość jako identyfikator PingAccess firmy klienta.
4. Następny **identyfikator katalogu (dzierżawcy)** wartość, należy również wybrać **Kopiuj do Schowka**, a następnie skopiuj i zapisz go. Tę wartość można określić później jako wystawca PingAccess firmy.
5. Z paska bocznego z **rejestracje aplikacji** dla aplikacji, wybierz **certyfikaty i klucze tajne** > **nowy wpis tajny klienta**. **Dodać wpis tajny klienta** zostanie wyświetlona strona.

   ![Dodaj klucz tajny klienta](./media/application-proxy-configure-single-sign-on-with-ping-access/add-a-client-secret.png)
6. W **opis**, typ `PingAccess key`.
7. W obszarze **Expires**, wybierz sposób ustawiania klucza PingAccess: **Za 1 rok**, **za 2 lata**, lub **nigdy**.
8. Wybierz pozycję **Dodaj**. PingAccess klucz pojawia się w tabeli klienta wpisów tajnych oraz ich z losową ciągu tego autofills w **wartość** pola.
9. Obok klucza PingAccess **wartość** pól, zaznacz **Kopiuj do Schowka** ikonę, a następnie skopiuj i zapisz go. Tę wartość można określić później jako klucz tajny klienta PingAccess firmy.

### <a name="update-graphapi-to-send-custom-fields-optional"></a>Aktualizuj GraphAPI do wysyłania niestandardowych pól (opcjonalnie)

Aby uzyskać listę tokeny zabezpieczające, które wysyła usługi Azure AD w celu uwierzytelniania, zobacz [tokeny Identyfikatora platforma tożsamości firmy Microsoft](../develop/id-tokens.md). Jeśli potrzebujesz niestandardowych oświadczenie, które wysyła innych tokenów, należy ustawić `acceptMappedClaims` pole aplikacji do `True`. Można jednak używać Eksploratora programu Graph lub manifest aplikacji portalu usługi Azure AD, aby wprowadzić tę zmianę.

W tym przykładzie użyto Graph Explorer:

```
PATCH https://graph.windows.net/myorganization/applications/<object_id_GUID_of_your_application>

{
  "acceptMappedClaims":true
}
```

W tym przykładzie użyto [portalu Azure Active Directory](https://aad.portal.azure.com/) można zaktualizować `acceptMappedClaims` pola:

1. Zaloguj się do [portalu Azure Active Directory](https://aad.portal.azure.com/) jako administrator aplikacji.
2. Wybierz **usługi Azure Active Directory** > **rejestracje aplikacji**. Zostanie wyświetlona lista aplikacji.
3. Wybierz swoją aplikację.
4. Z paska bocznego z **rejestracje aplikacji** strona aplikacji, wybierz pozycję **manifestu**. Zostanie wyświetlony kod JSON manifestu dla rejestracji aplikacji.
5. Wyszukaj `acceptMappedClaims` pola, a następnie zmień wartość na `True`.
6. Wybierz pozycję **Zapisz**.

### <a name="use-a-custom-claim-optional"></a>Użyj oświadczenia niestandardowego (opcjonalnie)

Aby aplikacja oświadczenia niestandardowego i zawiera dodatkowe pola, upewnij się, masz także [utworzone oświadczenia niestandardowe mapowanie zasad i przypisać je do aplikacji](../develop/active-directory-claims-mapping.md#claims-mapping-policy-assignment).

> [!NOTE]
> Aby użyć oświadczenia niestandardowego, musi również mieć zasad niestandardowych, zdefiniowanych i przypisane do aplikacji. Ta zasada powinna zawierać wszystkie wymagane atrybutów niestandardowych.
>
> Możesz tworzyć definicję zasad i przypisywania za pomocą programu PowerShell, usługi Azure AD Graph Explorer lub Microsoft Graph. Jeśli wykonujesz je w programie PowerShell, należy najpierw użyć `New-AzureADPolicy` i przypisz je do aplikacji przy użyciu `Add-AzureADServicePrincipalPolicy`. Aby uzyskać więcej informacji, zobacz [oświadczeń przypisania zasad mapowania](../develop/active-directory-claims-mapping.md#claims-mapping-policy-assignment).

## <a name="download-pingaccess-and-configure-your-application"></a>Pobierz oprogramowanie PingAccess i konfigurowanie aplikacji

Teraz, że ukończono wszystkie kroki konfiguracji usługi Azure Active Directory, możesz przejść do konfigurowania PingAccess.

Szczegółowe kroki PingAccess częścią tego scenariusza jest nadal w dokumentacji Ping Identity. Postępuj zgodnie z instrukcjami w [skonfigurować oprogramowanie PingAccess dla usługi Azure AD w ochronie aplikacji opublikowanych przy użyciu serwera Proxy aplikacji programu Microsoft Azure AD](https://docs.pingidentity.com/bundle/paaad_m_ConfigurePAforMSAzureADSolution_paaad43/page/pa_c_PAAzureSolutionOverview.html) w witrynie sieci web Ping Identity.

Te kroki pomogą Ci zainstalować oprogramowanie PingAccess i skonfigurować konto PingAccess (Jeśli nie masz jeszcze jeden). Następnie, aby utworzyć połączenie usługi Azure AD OpenID Connect (OIDC), należy skonfigurować dostawcę tokenów z **identyfikator katalogu (dzierżawcy)** wartości, który został skopiowany z portalu usługi Azure AD. Następnie, aby utworzyć sesję sieci web na PingAccess, należy użyć **identyfikator aplikacji (klienta)** i `PingAccess key` wartości. Po tym można Konfigurowanie mapowania tożsamości i utworzyć hostów wirtualnych, witryn i aplikacji.

### <a name="test-your-application"></a>Testowanie aplikacji

Po zakończeniu wszystkie te kroki, aplikacja powinna być uruchomiona. Aby przetestować go, otwórz przeglądarkę i przejdź do zewnętrznego adresu URL, które zostało utworzone podczas publikowania aplikacji na platformie Azure. Zaloguj się przy użyciu konta testowego, która została przypisana do aplikacji.

## <a name="next-steps"></a>Kolejne kroki

- [Skonfigurować oprogramowanie PingAccess dla usługi Azure AD w ochronie aplikacji opublikowanych przy użyciu serwera Proxy aplikacji programu Microsoft Azure AD](https://docs.pingidentity.com/bundle/paaad_m_ConfigurePAforMSAzureADSolution_paaad43/page/pa_c_PAAzureSolutionOverview.html)
- [Single sign-on to applications in Azure Active Directory (Logowanie jednokrotne do aplikacji w usłudze Azure Active Directory)](what-is-single-sign-on.md)
- [Rozwiązywanie problemów z serwera Proxy aplikacji i komunikaty o błędach](application-proxy-troubleshoot.md)
