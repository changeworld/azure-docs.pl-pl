---
title: Saml logowanie jednokrotne — aplikacje inne niż galeria - Platforma tożsamości firmy Microsoft | Dokumenty firmy Microsoft
description: Konfigurowanie logowania jednokrotnego (Logowanie jednokrotne) do aplikacji innych niż galeria na platformie tożsamości firmy Microsoft (Azure AD)
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: article
ms.workload: identity
ms.date: 07/19/2019
ms.author: celested
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: ad27ad5e34d9f44fe7d7be80e05e33dd6fb5e7b1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244214"
---
# <a name="configure-saml-based-single-sign-on-to-non-gallery-applications"></a>Konfigurowanie logowania jednokrotnego opartego na saml do aplikacji innych niż galeria

Po [dodaniu aplikacji galerii](add-gallery-app.md) lub [aplikacji sieci web spoza galerii](add-non-gallery-app.md) do aplikacji azure AD Enterprise, jedną z dostępnych opcji logowania jednokrotnego jest [logowanie jednokrotne oparte na SAML](what-is-single-sign-on.md#saml-sso). Wybierz SAML, gdy jest to możliwe dla aplikacji uwierzytelnianych przy użyciu jednego z protokołów SAML. W przypadku logowania jednokrotnego SAML usługa Azure AD uwierzytelnia się w aplikacji przy użyciu konta usługi Azure AD użytkownika. Usługa Azure AD przekazuje informacje logowania do aplikacji za pośrednictwem protokołu połączenia. Użytkownicy można mapować do określonych ról aplikacji na podstawie reguł zdefiniowanych w oświadczeń SAML. W tym artykule opisano sposób konfigurowania logowania jednokrotnego opartego na saml dla aplikacji spoza galerii. 

> [!NOTE]
> Dodajesz aplikację z galerii? Instrukcje konfiguracji krok po kroku znajdują się na [liście samouczków aplikacji SaaS](../saas-apps/tutorial-list.md)

Aby skonfigurować saml logowania jednokrotnego dla aplikacji spoza galerii bez pisania kodu, należy mieć subskrypcję wraz z licencją usługi Azure AD Premium i aplikacja musi obsługiwać SAML 2.0. Aby uzyskać więcej informacji na temat wersji usługi Azure AD, odwiedź stronę [Cennik usługi Azure AD](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="before-you-begin"></a>Przed rozpoczęciem

Jeśli aplikacja nie została dodana do dzierżawy usługi Azure AD, zobacz [Dodawanie aplikacji spoza galerii.](add-non-gallery-app.md)

## <a name="step-1-edit-the-basic-saml-configuration"></a>Krok 1. Edytowanie podstawowej konfiguracji SAML

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) jako administrator aplikacji w chmurze lub administrator aplikacji dla dzierżawy usługi Azure AD.

2. Przejdź do aplikacji **Usługi Azure Active Directory** > **Enterprise** i wybierz aplikację z listy. 
   
   - Aby wyszukać aplikację, w menu **Typ aplikacji** wybierz polecenie **Wszystkie aplikacje**, a następnie wybierz pozycję **Zastosuj**. Wprowadź nazwę aplikacji w polu wyszukiwania, a następnie wybierz aplikację z wyników.

3. W sekcji **Zarządzanie** wybierz pozycję **Logowanie jednokrotne**. 

4. Wybierz **SAML**. Zostanie wyświetlona strona **Konfigurowanie logowania jednokrotnego za pomocą strony SAML — podgląd.**

   ![Krok 1 Edytuj podstawową konfigurację SAML](media/configure-single-sign-on-non-gallery-applications/step-one-basic-saml-config.png)

5. Aby edytować podstawowe opcje konfiguracji SAML, wybierz ikonę **Edytuj** (ołówek) w prawym górnym rogu sekcji **Podstawowa konfiguracja SAML.**

1. Wprowadź następujące ustawienia. Wartości powinny być ujmne od dostawcy aplikacji. Można ręcznie wprowadzić wartości lub przekazać plik metadanych, aby wyodrębnić wartość pól.

    | Podstawowe ustawienie konfiguracji SAML | Zainicjowane przez dostawcę usługi | Zainicjowane przez dostawcę tożsamości | Opis |
    |:--|:--|:--|:--|
    | **Identyfikator (identyfikator jednostki)** | Wymagane w przypadku niektórych aplikacji | Wymagane w przypadku niektórych aplikacji | Jednoznacznie identyfikuje aplikację. Usługa Azure AD wysyła identyfikator do aplikacji jako parametr odbiorców tokenu SAML. Aplikacja powinna go zweryfikować. Ta wartość jest widoczna również jako identyfikator jednostki w dowolnych metadanych SAML udostępnianych przez aplikację. Wprowadź adres URL, który używa następującego<subdomain>wzorca: "https:// .contoso.com" *Możesz znaleźć tę wartość jako element **wystawcy** w **żądaniu AuthnRequest** (SAML) wysłanym przez aplikację.* |
    | **Adres URL odpowiedzi** | Wymagany | Wymagany | Określa miejsce, w którym aplikacja oczekuje otrzymać token języka SAML. Adres URL odpowiedzi jest również nazywany adresem URL usługi Assertion Consumer Service (ACS). Dodatkowe pola adresu URL odpowiedzi można użyć do określenia wielu adresów URL odpowiedzi. Na przykład może być potrzebne dodatkowe adresy URL odpowiedzi dla wielu poddomen. Do celów testowych można też jednocześnie określić wiele adresów URL odpowiedzi (hosta lokalnego i publicznych adresów URL). |
    | **Adres URL logowania** | Wymagany | Nie podawaj | Gdy użytkownik otwiera ten adres URL, dostawca usługi przekierowuje go do usługi Azure AD w celu uwierzytelnienia i zalogowania. Usługa Azure AD używa adresu URL do uruchomienia aplikacji z usługi Office 365 lub panelu dostępu usługi Azure AD. Gdy usługa Azure AD jest pusta, wykonuje logowanie inicjowane przez idp, gdy użytkownik uruchamia aplikację z usługi Office 365, panelu dostępu usługi Azure AD lub adresu URL logowania jednokrotnego usługi Azure AD.|
    | **Stan przekaźnika** | Optional (Opcjonalność) | Optional (Opcjonalność) | Określa aplikacji, dokąd przekierować użytkownika po zakończeniu uwierzytelniania. Zazwyczaj wartość jest prawidłowym adresem URL aplikacji. Jednak niektóre aplikacje używają tego pola inaczej. Aby uzyskać więcej informacji, skontaktuj się z dostawcą aplikacji.
    | **Adres URL wylogowywania** | Optional (Opcjonalność) | Optional (Opcjonalność) | Służy do wysyłania odpowiedzi wylogowywać SAML z powrotem do aplikacji.

Aby uzyskać więcej informacji, zobacz [Protokół SAML jednokrotnego logowania](../develop/single-sign-on-saml-protocol.md).

## <a name="step-2-configure-user-attributes-and-claims"></a>Krok 2. Konfigurowanie atrybutów i oświadczeń użytkownika 

Gdy użytkownik uwierzytelnia się w aplikacji, usługa Azure AD wystawia aplikacji token SAML z informacjami (lub oświadczeń) o użytkowniku, który jednoznacznie identyfikuje je. Domyślnie informacje te obejmują nazwę użytkownika, adres e-mail, imię i nazwisko. Może być konieczne dostosowanie tych oświadczeń, jeśli na przykład aplikacja wymaga określonych wartości oświadczeń lub formatu **Name** innego niż nazwa użytkownika. Wymagania dotyczące aplikacji galerii są opisane w [samouczkach specyficznych dla aplikacji](../saas-apps/tutorial-list.md)lub można zwrócić się do dostawcy aplikacji. Ogólne kroki konfigurowania atrybutów i oświadczeń użytkownika są opisane poniżej.

1. W sekcji **Atrybuty i oświadczenia użytkownika** wybierz ikonę **Edytuj** (ołówek) w prawym górnym rogu.

   ![Krok 2 Konfigurowanie atrybutów i oświadczeń użytkownika](media/configure-single-sign-on-non-gallery-applications/step-two-user-attributes-claims.png)

2. Sprawdź **wartość identyfikatora nazwy**. Wartością domyślną jest *nazwa użytkownika.principal.* Identyfikator użytkownika jednoznacznie identyfikuje każdego użytkownika w aplikacji. Jeśli na przykład adresem e-mail jest zarówno nazwa użytkownika, jak i unikatowy identyfikator, ustaw wartość *user.mail*.

3. Aby zmodyfikować **wartość identyfikatora nazwy,** wybierz ikonę **Edytuj** (ołówek) dla pola **Wartość identyfikatora nazwy.** W razie potrzeby należy wprowadzić odpowiednie zmiany w formacie identyfikatora i źródle. Aby uzyskać szczegółowe informacje, zobacz [Edytowanie nameid](../develop/active-directory-saml-claims-customization.md#editing-nameid). Zapisz zmiany po zakończeniu. 
 
4. Aby skonfigurować oświadczenia grupy, wybierz ikonę **Edytuj** dla **grupy zwrócone w** polu oświadczeń. Aby uzyskać szczegółowe informacje, zobacz [Konfigurowanie oświadczeń grupy](../hybrid/how-to-connect-fed-group-claims.md).

5. Aby dodać oświadczenie, wybierz **pozycję Dodaj nowe roszczenie** u góry strony. Wprowadź **nazwę** i wybierz odpowiednie źródło. Jeśli **wybierzesz źródło atrybutu,** musisz wybrać **atrybut Źródło,** którego chcesz użyć. Jeśli wybierzesz źródło **tłumaczenia,** musisz wybrać **transformację** i **parametr 1,** którego chcesz użyć. Aby uzyskać szczegółowe informacje, zobacz [Dodawanie oświadczeń specyficznych dla aplikacji](../develop/active-directory-saml-claims-customization.md#adding-application-specific-claims). Zapisz zmiany po zakończeniu. 

6. Wybierz **pozycję Zapisz**. Nowe oświadczenie pojawi się w tabeli.

   > [!NOTE]
   > Aby uzyskać dodatkowe sposoby dostosowywania tokenu SAML z usługi Azure AD do aplikacji, zobacz następujące zasoby.
   >- Aby utworzyć role niestandardowe za pośrednictwem witryny Azure Portal, zobacz [Konfigurowanie oświadczeń ról](../develop/active-directory-enterprise-app-role-management.md).
   >- Aby dostosować oświadczenia za pośrednictwem programu PowerShell, zobacz [Dostosowywanie oświadczeń — PowerShell](../develop/active-directory-claims-mapping.md).
   >- Aby zmodyfikować manifest aplikacji w celu skonfigurowania opcjonalnych oświadczeń dla aplikacji, zobacz [Konfigurowanie oświadczeń opcjonalnych](../develop/active-directory-optional-claims.md).
   >- Aby ustawić zasady okresu istnienia tokenu dla tokenów odświeżania, tokenów dostępu, tokenów sesji i tokenów identyfikatorów, zobacz [Konfigurowanie okresów istnienia tokenu.](../develop/active-directory-configurable-token-lifetimes.md) Aby ograniczyć sesje uwierzytelniania za pośrednictwem usługi Azure AD, zobacz [możliwości zarządzania sesjami uwierzytelniania](https://go.microsoft.com/fwlink/?linkid=2083106).

## <a name="step-3-manage-the-saml-signing-certificate"></a>Krok 3. Zarządzanie certyfikatem podpisywania SAML

Usługa Azure AD używa certyfikatu do podpisywania tokenów SAML wysyłanych do aplikacji. Ten certyfikat jest potrzebny do skonfigurowania zaufania między usługą Azure AD a aplikacją. Szczegółowe informacje na temat formatu certyfikatu można znaleźć w dokumentacji saml aplikacji. Aby uzyskać więcej informacji, zobacz [Zarządzanie certyfikatami dla federacyjnych opcji logowania jednokrotnego](manage-certificates-for-federated-single-sign-on.md) i [zaawansowanego podpisywania certyfikatów w tokenie SAML](certificate-signing-options.md).

Z usługi Azure AD możesz pobrać aktywny certyfikat w formacie Base64 lub Raw bezpośrednio ze strony głównej **Konfigurowanie logowania jednokrotnego za pomocą strony SAML.** Alternatywnie można uzyskać aktywny certyfikat, pobierając plik XML metadanych aplikacji lub przy użyciu adresu URL metadanych federacji aplikacji. Aby wyświetlić, utworzyć lub pobrać certyfikaty (aktywne lub nieaktywne), wykonaj następujące kroki.

1. Przejdź do sekcji **Certyfikat podpisywania SAML.** 

   ![Krok 3 Zarządzanie certyfikatem podpisywania SAML](./media/configure-single-sign-on-non-gallery-applications/step-three-certificate.png)

2. Sprawdź, czy certyfikat ma:

   - *Żądana data wygaśnięcia.* Datę wygaśnięcia można skonfigurować na okres do trzech lat w przyszłości.
   - *Stan aktywny dla żądanego certyfikatu.* Jeśli stan jest **nieaktywny,** zmień stan na **Aktywny**. Aby zmienić stan, kliknij prawym przyciskiem myszy wiersz żądanego certyfikatu i wybierz polecenie **Uaktywność certyfikatu**.
   - *Poprawna opcja podpisywania i algorytm.*
   - *Prawidłowy adres e-mail z powiadomieniem(y).* Gdy aktywny certyfikat zbliża się do daty wygaśnięcia, usługa Azure AD wysyła powiadomienie na adres e-mail skonfigurowany w tym polu.

2. Aby pobrać certyfikat, wybierz jedną z opcji formatu Base64, formatu RAW lub protokołu XML metadanych federacji. Usługa Azure AD udostępnia również **adres URL metadanych federacji aplikacji,** `https://login.microsoftonline.com/<Directory ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<Application ID>`w którym można uzyskać dostęp do metadanych specyficznych dla aplikacji w formacie .

3. Aby zarządzać certyfikatem, tworzyć lub importować go, wybierz ikonę **Edytuj** (ołówek) w prawym górnym rogu sekcji **Certyfikat podpisywania SAML.**

   ![Certyfikat podpisywania SAML](./media/configure-single-sign-on-non-gallery-applications/saml-signing-certificate.png)


   Podejmij dowolną z następujących czynności:

   - Aby utworzyć nowy certyfikat, wybierz pozycję **Nowy certyfikat**, wybierz **datę wygaśnięcia**, a następnie wybierz pozycję **Zapisz**. Aby aktywować certyfikat, wybierz menu kontekstowe (**...**) i wybierz pozycję **Uatywn certyfikat**.
   - Aby przekazać certyfikat z kluczem prywatnym i poświadczeniami pfx, wybierz pozycję **Importuj certyfikat** i przejdź do certyfikatu. Wprowadź **hasło PFX**, a następnie wybierz pozycję **Dodaj**.  
   - Aby skonfigurować zaawansowane opcje podpisywania certyfikatów, użyj następujących opcji. Opisy tych opcji można znaleźć w artykule [Opcje podpisywania certyfikatów zaawansowanych.](certificate-signing-options.md)
      - Na liście rozwijanej **Opcja podpisywania** wybierz pozycję **Podpisz odpowiedź SAML**, **Podpisz twierdzenie SAML**lub **Podpisz odpowiedź i asercja SAML**.
      - Z listy rozwijanej **Algorytm podpisywania** wybierz pozycję **SHA-1** lub **SHA-256**.
   - Aby powiadomić dodatkowe osoby, gdy aktywny certyfikat zbliża się do daty wygaśnięcia, wprowadź adresy e-mail w **polach Wiadomości e-mail powiadomień.**

4. Jeśli wprowadzono zmiany, wybierz **pozycję Zapisz** u góry sekcji **Certyfikat podpisywania SAML.** 

## <a name="step-4-set-up-the-application-to-use-azure-ad"></a>Krok 4. Konfigurowanie aplikacji do korzystania z usługi Azure AD

Sekcja **Set \<up applicationName>** zawiera listę wartości, które muszą być skonfigurowane w aplikacji, aby używać usługi Azure AD jako dostawcy tożsamości SAML. Wymagane wartości różnią się w zależności od aplikacji. Aby uzyskać szczegółowe informacje, zobacz dokumentację SAML aplikacji. Aby znaleźć dokumentację, przejdź do nagłówka **Konfigurowanie nazwy \<aplikacji>** i wybierz pozycję Wyświetl **instrukcje krok po kroku**. Dokumentacja pojawi się na stronie **Konfigurowanie logowania.** Ta strona prowadzi cię do wypełniania **adresów URL logowania,** **identyfikatora usługi Azure AD**i wartości **adresu URL wylogowywanie** w nagłówku **Konfigurowanie nazwy \<aplikacji>.**

1. Przewiń w dół do sekcji **Ustaw \<aplikacjęName>.** 
   
   ![Krok 4 Konfigurowanie aplikacji](media/configure-single-sign-on-non-gallery-applications/step-four-app-config.png)

1. W razie potrzeby skopiuj wartość z każdego wiersza w tej sekcji i postępuj zgodnie z instrukcjami dotyczącymi aplikacji, aby dodać wartość do aplikacji. W przypadku aplikacji galerii można wyświetlić dokumentację, wybierając **pozycję Wyświetl instrukcje krok po kroku**. 
   - Wartości **adresu URL logowania** i adresu URL **wylogowania** zarówno rozpoznać do tego samego punktu końcowego, który jest punktem końcowym obsługi żądania SAML dla wystąpienia usługi Azure AD. 
   - Identyfikator **usługi Azure AD** jest wartością **wystawcy** w tokenie SAML wystawionym do aplikacji.
2. Po wklejeniu wszystkich wartości do odpowiednich pól wybierz pozycję **Zapisz**.

## <a name="step-5-validate-single-sign-on"></a>Krok 5. Sprawdzanie poprawności logowania jednokrotnego

Po skonfigurowaniu aplikacji do używania usługi Azure AD jako dostawcy tożsamości opartego na saml, można przetestować ustawienia, aby sprawdzić, czy logowanie jednokrotne działa dla Twojego konta. 

2. Przewiń do **sekcji Sprawdzanie <applicationName> poprawności logowania jednokrotnego z sekcją.**

   ![Krok 5 Sprawdzanie poprawności logowania jednokrotnego](media/configure-single-sign-on-non-gallery-applications/step-five-validate.png)

3. Wybierz przycisk **Weryfikuj**. Zostaną wyświetlone opcje testowania.

4. Wybierz pozycję **Zaloguj się jako bieżący użytkownik**. 

Jeśli logowanie zakończy się pomyślnie, możesz przystąpić do przypisywania użytkowników i grup do aplikacji SAML.
Jeśli pojawi się komunikat o błędzie, wykonaj następujące czynności:

1. Skopiuj szczegóły i wklej je w polu **Jak wygląda błąd?**.

    ![Uzyskiwanie wskazówek dotyczących rozwiązywania](media/configure-single-sign-on-non-gallery-applications/error-guidance.png)

2. Wybierz **pozycję Uzyskaj wskazówki dotyczące rozdzielczości**. Zostaną wyświetlone główna przyczyna i wskazówki dotyczące rozwiązywania.  W tym przykładzie użytkownik nie został przypisany do aplikacji.

3. Przeczytaj wskazówki dotyczące rozwiązania, a następnie, jeśli to możliwe, rozwiązać problem.

4. Uruchamiaj test ponownie, dopóki nie zostanie pomyślnie zakończony.

Aby uzyskać więcej informacji, zobacz [Debugowanie aplikacji jednokrotnego logowania opartych na SAML w usłudze Azure Active Directory](../azuread-dev/howto-v1-debug-saml-sso-issues.md).

## <a name="next-steps"></a>Następne kroki

- [Przypisywanie użytkowników lub grup do aplikacji](methods-for-assigning-users-and-groups.md)
- [Konfigurowanie automatycznego inicjowania obsługi administracyjnej konta użytkownika](../app-provisioning/configure-automatic-user-provisioning-portal.md)
