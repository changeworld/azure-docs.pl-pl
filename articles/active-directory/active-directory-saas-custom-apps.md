---
title: Konfigurowanie usługi Azure AD z logowania jednokrotnego dla aplikacji | Dokumentacja firmy Microsoft
description: Dowiedz się, jak do samoobsługi połączyć aplikacje w usłudze Azure Active Directory przy użyciu SAML i Usługa rejestracji Jednokrotnej opartego na hasłach
services: active-directory
author: asmalser-msft
documentationcenter: na
manager: mtillman
ms.assetid: 0d42eb0c-6d3f-4557-9030-e88e86709a19
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/20/2018
ms.author: asmalser
ms.reviewer: luleon
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: aeb67a73a69684b89609c6b04160357b244f62b7
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/25/2018
ms.locfileid: "36754512"
---
# <a name="configure-single-sign-on-to-applications-that-are-not-in-the-azure-active-directory-application-gallery"></a>Konfigurowanie rejestracji jednokrotnej do aplikacji, które nie znajdują się w galerii aplikacji usługi Azure Active Directory

Ten artykuł dotyczy funkcja, która umożliwia administratorom na konfigurowanie rejestracji jednokrotnej do aplikacji nie znajduje się w galerii aplikacji usługi Azure Active Directory *bez pisania kodu*. Ta funkcja została wydana z wersji zapoznawczej technical preview 18 listopada 2015 i znajduje się w [Azure Active Directory Premium](fundamentals/active-directory-whatis.md). Jeśli zamiast tego Wyszukaj wskazówki dla deweloperów na temat integracji z usługą Azure AD przez kod aplikacji niestandardowych, zobacz [scenariusze uwierzytelniania dla usługi Azure AD](active-directory-authentication-scenarios.md).

Galerii aplikacji usługi Azure Active Directory zawiera listę aplikacji, które są znane do obsługi formularza rejestracji jednokrotnej z usługą Azure Active Directory, zgodnie z opisem w [w tym artykule](manage-apps/what-is-single-sign-on.md). Po (jako IT specjalistyczne lub system integratora w organizacji) znalezieniu aplikacji, którą chcesz się połączyć, możesz rozpocząć pracę zgodnie z instrukcjami krok po kroku przedstawiony w portalu Azure, aby włączyć logowanie jednokrotne.

Klienci z [Azure Active Directory Premium](fundamentals/active-directory-whatis.md) licencji również uzyskać te dodatkowe możliwości:

* Samoobsługowe integracji z dowolnej aplikacji, która obsługuje dostawców tożsamości SAML 2.0 (zainicjował SP lub inicjowane IdP)
* Samoobsługowe integracji z dowolnej aplikacji sieci web, która ma oparty na języku HTML strony logowania przy użyciu [opartego na hasłach logowania jednokrotnego](manage-apps/what-is-single-sign-on.md#password-based-single-sign-on)
* Połączenie aplikacji korzystających z protokołu SCIM do inicjowania obsługi użytkowników samoobsługi ([opisanych tutaj](manage-apps/use-scim-to-provision-users-and-groups.md))
* Możliwość dodawania łącza do aplikacji w [uruchamiania aplikacji usługi Office 365](https://blogs.office.com/2014/10/16/organize-office-365-new-app-launcher-2/) lub [panel dostępu usługi Azure AD](manage-apps/what-is-single-sign-on.md#deploying-azure-ad-integrated-applications-to-users)

To nie tylko aplikacji SaaS, które używają, ale ma nie jeszcze został na dodawanej do galerii aplikacji Azure AD, ale aplikacji sieci web innych firm, które organizacja został wdrożony na serwerach, które możesz kontrolować, w chmurze lub lokalnie.

Te możliwości, nazywany również *szablony integracji aplikacji*, podaj punkty połączenia oparte na standardach aplikacji, które obsługują SAML, SCIM lub uwierzytelnianie oparte na formularzach i elastyczne opcje i ustawienia zgodność z szerokim liczbę aplikacji. 

## <a name="adding-an-unlisted-application"></a>Dodawanie nieznajdujące się na liście aplikacji
Aby połączyć aplikację przy użyciu szablonu usługi integracji aplikacji, zaloguj się do portalu Azure przy użyciu konta administratora usługi Azure Active Directory. Przejdź do **usługi Active Directory > aplikacje dla przedsiębiorstw > nowej aplikacji > Non galerii aplikacji** zaznacz **Dodaj**, a następnie **dodać aplikację z galerii** .

  ![](./media/active-directory-saas-custom-apps/customapp1.png)

Nieznajdujące się na liście aplikacji w galerii aplikacji można dodawać przez zaznaczenie **Non galerii aplikacji** kafelka, który jest wyświetlany w wynikach wyszukiwania, jeśli nie można znaleźć żądanej aplikacji. Po wprowadzeniu nazwy aplikacji, można skonfigurować opcje rejestracji jednokrotnej i zachowania. 

**Szybkie porady**: najlepszym rozwiązaniem, użyj funkcji wyszukiwania, aby sprawdzić, czy aplikacja już istnieje w galerii aplikacji. Czy aplikacja zostanie znaleziony i jego opis uwagi rejestracji jednokrotnej, aplikacji jest już obsługiwane w przypadku federacyjnego logowania jednokrotnego.

  ![](./media/active-directory-saas-custom-apps/customapp2.png)

Dodawanie aplikacji w ten sposób zapewnia podobne do dostępne dla wstępnie zintegrowanych aplikacji. Aby rozpocząć, wybierz **skonfigurować logowanie jednokrotne** lub kliknij **logowanie jednokrotne** z menu nawigacji po lewej stronie aplikacji. Następnego ekranu przedstawia opcje dotyczące konfigurowania rejestracji jednokrotnej. Opcje zostały opisane w kolejnych sekcjach niniejszego artykułu.
  
![](./media/active-directory-saas-custom-apps/customapp3.png)

## <a name="saml-based-single-sign-on"></a>Logowanie jednokrotne oparte na języku SAML
Wybierz tę opcję, aby skonfigurować uwierzytelnianie oparte na protokole SAML dla aplikacji. Wymaga to, że aplikacja obsługuje SAML 2.0. Należy zebrać informacje na temat sposobu korzystania z funkcji SAML aplikacji przed kontynuowaniem. Wykonaj następujące sekcje umożliwiające konfigurowanie logowania jednokrotnego między aplikacją a usługą Azure AD.

### <a name="enter-basic-saml-configuration"></a>Wprowadź podstawową konfigurację SAML

Aby skonfigurować usługi Azure AD, wprowadź podstawową konfigurację SAML. Ręcznie wprowadź wartości lub Przekaż plik metadanych można wyodrębnić wartość pola.

  ![Adresy URL i litware domeny](./media/active-directory-saas-custom-apps/customapp4.png)

- **Zaloguj się na adres URL (SP inicjowane tylko)** — w przypadku, gdy użytkownik przechodzi do logowania się w tej aplikacji. Jeśli aplikacja jest skonfigurowany do wykonywania usługi zainicjował dostawcy logowania jednokrotnego, gdy użytkownik przechodzi do tego adresu URL, dostawcy usług wykonuje niezbędne przekierowanie do usługi Azure AD do uwierzytelniania i logowania użytkownika w. Jeśli to pole zostanie wypełnione, usługi Azure AD będzie używać tego adresu URL do uruchamiania aplikacji z usługi Office 365 i panelu dostępu usługi Azure AD. Jeśli to pole zostanie pominięte, a następnie usługi Azure AD, zamiast tego zostanie przeprowadzone dostawcy tożsamości-zainicjował logowania jednokrotnego, gdy aplikacja jest uruchamiana z usługi Office 365, Panel dostępu usługi Azure AD lub Azure AD pojedynczy adres URL logowania (copyable z karty Pulpit nawigacyjny).
- **Identyfikator** -musi jednoznacznie wskazywać aplikacji, dla którego rejestracji jednokrotnej jest konfigurowany. Można znaleźć tę wartość jako element wystawcy AuthRequest (żądanie SAML) wysyłane przez aplikację. Ta wartość jest także wyświetlany jako **identyfikator jednostki** w dowolnym metadanych SAML udostępniany przez aplikację. W dokumentacji aplikacji SAML szczegółowe informacje na temat jest jego wartość Identyfikatora jednostki lub odbiorców. 

    Poniżej przedstawiono przykład sposobu wyświetlania identyfikatora lub wystawcy w żądaniu SAML wysyłane przez aplikację do usługi Azure AD:

    ```
    <samlp:AuthnRequest
    xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
    ID="id6c1c178c166d486687be4aaf5e482730"
    Version="2.0" IssueInstant="2013-03-18T03:28:54.1839884Z"
    xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
    <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.contoso.com</Issuer>
    </samlp:AuthnRequest>
    ```

- **Adres URL odpowiedzi** — adres URL odpowiedzi jest, gdy aplikacja oczekuje odebrać tokenu SAML. Jest to także określane jako adres URL usługi konsumenta potwierdzenia (ACS). Sprawdź dokumentacji SAML aplikacji, aby uzyskać więcej informacji na jego odpowiedzi tokenu SAML adres URL lub adres URL usług ACS jest. 

    Aby skonfigurować wiele replyURLs służy poniższy skrypt programu PowerShell.

    ```PowerShell
    $sp = Get-AzureADServicePrincipal -SearchString "<Exact App  name>"
    $app = Get-AzureADApplication -SearchString "<Exact app name>"
    Set-AzureADApplication -ObjectId $app.ObjectId -ReplyUrls "<ReplyURLs>"
    Set-AzureADServicePrincipal -ObjectId $sp.ObjectId -ReplyUrls "<ReplyURLs>"
    ```

Aby uzyskać więcej informacji, zobacz [SAML 2.0 uwierzytelniania żądań i odpowiedzi, które obsługuje usługi Azure Active Directory (Azure AD)](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference?/?WT.mc_id=DOC_AAD_How_to_Debug_SAML)


### <a name="review-or-customize-the-claims-issued-in-the-saml-token"></a>Przeglądanie lub dostosowywanie oświadczeń wydanych w tokenie SAML

Podczas uwierzytelniania użytkownika do aplikacji, usługi Azure AD wystawia SAML token aplikacja, która zawiera informacje (lub oświadczenia) dotyczące użytkownika, który unikatowo identyfikuje je. Domyślnie w tym nazwę użytkownika, adres e-mail, imię i nazwisko użytkownika. 

Umożliwia wyświetlenie i edytowanie oświadczenia wysyłane w tokenie SAML do aplikacji w obszarze **atrybuty** kartę.

  ![](./media/active-directory-saas-custom-apps/customapp7.png)

Istnieją dwie przyczyny, dlaczego konieczne może być Edycja oświadczeń wydanych w tokenie SAML:

- Aplikacja została zapisana wymagają innego zestawu oświadczeń identyfikatorów URI lub wartości oświadczeń.
- Aplikacja została wdrożona w taki sposób, który wymaga oświadczeń NameIdentifier inny niż nazwa użytkownika (nazwy głównej użytkownika AKA) przechowywanych w usłudze Azure Active Directory. 

Aby uzyskać więcej informacji, zobacz [Dostosowywanie oświadczeń wydanych w tokenie SAML dla przedsiębiorstw](./develop/active-directory-saml-claims-customization.md). 



### <a name="review-certificate-expiration-data-status-and-email-notification"></a>Data wygaśnięcia certyfikatu przeglądu, stanu i powiadomień e-mail

Po utworzeniu galerii lub inne niż galerii aplikacji usługi Azure AD utworzy certyfikat specyficzne dla aplikacji z datą wygaśnięcia w ciągu 3 lat od daty utworzenia. Potrzebujesz tego certyfikatu do skonfigurowania zaufania między usługą Azure AD i aplikacji. Aby uzyskać więcej informacji na temat formatu certyfikatu zobacz dokumentację SAML aplikacji. 

Z usługi Azure AD możesz pobrać certyfikatu w formacie Base64 lub Raw format. Ponadto można uzyskać certyfikatu przez pobranie pliku XML metadanych aplikacji lub za pomocą adres URL metadanych federacji.

  ![Certyfikat](./media/active-directory-saas-custom-apps/certificate.png)

Sprawdź, czy certyfikat ma:

- Data wygaśnięcia żądany. Można skonfigurować datę wygaśnięcia, przez co najwyżej 3 lata.
- Stan aktywny. Jeśli stan jest nieaktywny, Zmień stan na aktywny. Aby zmienić stan, sprawdź **Active** , a następnie Zapisz konfigurację. 
- Poprawne powiadomień e-mail. Gdy aktywnego certyfikatu zbliża się data wygaśnięcia, usługi Azure AD wyśle powiadomienie na adres e-mail skonfigurowany w tym polu.  

Aby uzyskać więcej informacji, zobacz [zarządzać certyfikatami dla federacyjnego logowania jednokrotnego w usłudze Azure Active Directory](manage-apps/manage-certificates-for-federated-single-sign-on.md).

### <a name="set-up-target-application"></a>Konfigurowanie aplikacji docelowej

Aby skonfigurować aplikację do logowania jednokrotnego, zlokalizuj dokumentacji aplikacji. Aby znaleźć dokumentację, przewiń do końca strony konfiguracji opartej na SAML logowania, a następnie kliknij polecenie **Konfiguruj <application name>** . 

Wymagane wartości różny w zależności od aplikacji. Aby uzyskać więcej informacji zobacz dokumentację SAML aplikacji. Adres URL wylogowania usługi logowania jednokrotnego i oba rozwiązania do tego samego punktu końcowego, który jest punktem końcowym obsługi żądań SAML dla swojego wystąpienia usługi Azure AD. Identyfikator jednostki SAML jest wartość, która jest wyświetlana jako wystawca w tokenie SAML wystawiony do aplikacji.


### <a name="assign-users-and-groups-to-your-saml-application"></a>Przypisywanie użytkowników i grup do aplikacji SAML

Po skonfigurowaniu aplikacji przy użyciu usługi Azure AD jako dostawca tożsamości SAML na podstawie jest już prawie gotowe do testów. Jako formant zabezpieczeń usługi Azure AD nie będzie wystawiać token użytkownika do logowania się do aplikacji, chyba że usługi Azure AD udzielił użytkownikowi dostępu. Użytkownicy mogą otrzymać dostęp bezpośrednio lub za pośrednictwem członkostwa w grupie. 

Aby przypisać użytkownika lub grupę do aplikacji, kliknij przycisk **Przypisz użytkowników** przycisku. Wybierz użytkownika lub grupy, które chcesz przypisać, a następnie wybierz **przypisać** przycisku.

  ![](./media/active-directory-saas-custom-apps/customapp6.png)

Przypisanie użytkownika umożliwi usługi Azure AD można wystawić token dla użytkownika. Powoduje także kafelka aplikacji są wyświetlane w panelu dostępu użytkownika. Kafelek aplikacji również będą wyświetlane w uruchamiający aplikację usługi Office 365, jeśli użytkownik korzysta z usługi Office 365. 

> [!NOTE] 
> Możesz przekazać logo kafelka aplikacji przy użyciu **przekazać Logo** znajdującego się na **Konfiguruj** kartę dla aplikacji. 


### <a name="test-the-saml-application"></a>Testowanie aplikacji SAML

Przed testowaniem aplikacji SAML, musi mieć skonfigurowaniu aplikacji z usługą Azure AD i przypisany do aplikacji użytkowników lub grup. Aby przetestować aplikację SAML, zobacz [debugowanie na języku SAML logowanie jednokrotne do aplikacji w usłudze Azure Active Directory](develop/active-directory-saml-debugging.md).

## <a name="password-single-sign-on"></a>Hasło logowania jednokrotnego

Wybierz tę opcję, aby skonfigurować [opartego na hasłach rejestracji jednokrotnej](manage-apps/what-is-single-sign-on.md) dla aplikacji sieci web, z HTML strony logowania. Logowanie Jednokrotne opartego na hasłach, także określane jako hasło vaulting, umożliwia zarządzanie dostępem użytkowników i hasła do aplikacji sieci web, które nie obsługują federacji tożsamości. Jest również przydatne w przypadku scenariuszy, w którym musi kilku użytkowników do udostępnienia jednego konta, takich jak do kont aplikacji mediów społecznościowych w organizacji. 

Po wybraniu **dalej**, pojawi się monit o wprowadzenie adresu URL aplikacji sieci web stronę logowania w. Należy pamiętać, że musi być to strona, która zawiera nazwę użytkownika i hasło pól wejściowych. Po podaniu usługi Azure AD uruchamia proces przeanalizować strony logowania, wprowadź nazwę użytkownika i hasło, wprowadź. Jeśli proces zakończy się niepowodzeniem, następnie kolejno alternatywny proces instalowania rozszerzenia przeglądarki (wymaga programu Internet Explorer, Chrome lub Firefox), co umożliwi ręcznie przechwytywania pola.

Po przechwyceniu strony logowania, można przypisać użytkowników i grup i poświadczeń zasady można ustawić tak jak zwykły [hasło logowania jednokrotnego aplikacji](manage-apps/what-is-single-sign-on.md).

> [!NOTE] 
> Możesz przekazać logo kafelka aplikacji przy użyciu **przekazać Logo** znajdującego się na **Konfiguruj** kartę dla aplikacji. 
>

## <a name="existing-single-sign-on"></a>Istniejące rejestracji jednokrotnej
Wybierz tę opcję, aby dodać łącze do aplikacji portal firmy Panel dostępu usługi Azure AD lub usługi Office 365. Służy to można dodać łącza do aplikacji sieci web niestandardowe, które obecnie używają usługi Azure Active Directory Federation Services (lub innej usługi federacyjnej) zamiast Azure AD na potrzeby uwierzytelniania. Alternatywnie można dodać linków bezpośrednich do określonych stron SharePoint lub innych stron sieci web, które mają być wyświetlane na panele dostępu użytkownika. 

Po wybraniu **dalej**, pojawi się monit o wprowadzenie adresu URL aplikacji, aby utworzyć łącze do. Po ukończeniu użytkowników i grup można przypisać do aplikacji, co powoduje, że są wyświetlane w aplikacji [uruchamiania aplikacji usługi Office 365](https://blogs.office.com/2014/10/16/organize-office-365-new-app-launcher-2/) lub [panel dostępu usługi Azure AD](manage-apps/what-is-single-sign-on.md#deploying-azure-ad-integrated-applications-to-users) dla tych użytkowników.

> [!NOTE] 
> Możesz przekazać logo kafelka aplikacji przy użyciu **przekazać Logo** znajdującego się na **Konfiguruj** kartę dla aplikacji. 
>

## <a name="related-articles"></a>Pokrewne artykuły

- [Indeks artykułów dotyczących zarządzania aplikacjami w usłudze Azure Active Directory](active-directory-apps-index.md)
- [Dostosowywanie oświadczeń wydanych w tokenie SAML dla wstępnie zintegrowanych aplikacji](active-directory-saml-claims-customization.md)
- [Rozwiązywanie problemów z systemem SAML logowania jednokrotnego](active-directory-saml-debugging.md)

