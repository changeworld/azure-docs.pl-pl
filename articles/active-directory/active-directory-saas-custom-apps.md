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
ms.openlocfilehash: 8b76809e615174e7c4e118c6043c8f3fbef3ee94
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
---
# <a name="configuring-single-sign-on-to-applications-that-are-not-in-the-azure-active-directory-application-gallery"></a>Konfigurowanie logowania jednokrotnego do aplikacji, które nie znajdują się w galerii aplikacji Azure Active Directory
Ten artykuł dotyczy funkcja, która umożliwia administratorom na konfigurowanie rejestracji jednokrotnej do aplikacji nie znajduje się w galerii aplikacji usługi Azure Active Directory *bez pisania kodu*. Ta funkcja została wydana z wersji zapoznawczej technical preview 18 listopada 2015 i znajduje się w [Azure Active Directory Premium](active-directory-whatis.md). Jeśli zamiast tego Wyszukaj wskazówki dla deweloperów na temat integracji z usługą Azure AD przez kod aplikacji niestandardowych, zobacz [scenariusze uwierzytelniania dla usługi Azure AD](active-directory-authentication-scenarios.md).

Galerii aplikacji usługi Azure Active Directory zawiera listę aplikacji, które są znane do obsługi formularza rejestracji jednokrotnej z usługą Azure Active Directory, zgodnie z opisem w [w tym artykule](active-directory-appssoaccess-whatis.md). Po (jako IT specjalistyczne lub system integratora w organizacji) znalezieniu aplikacji, którą chcesz się połączyć, możesz rozpocząć pracę zgodnie z instrukcjami krok po kroku przedstawiony w portalu Azure, aby włączyć logowanie jednokrotne.

Klienci z [Azure Active Directory Premium](active-directory-whatis.md) licencji również uzyskać te dodatkowe możliwości:

* Samoobsługowe integracji z dowolnej aplikacji, która obsługuje dostawców tożsamości SAML 2.0 (zainicjował SP lub inicjowane IdP)
* Samoobsługowe integracji z dowolnej aplikacji sieci web, która ma oparty na języku HTML strony logowania przy użyciu [opartego na hasłach logowania jednokrotnego](active-directory-appssoaccess-whatis.md#password-based-single-sign-on)
* Połączenie aplikacji korzystających z protokołu SCIM do inicjowania obsługi użytkowników samoobsługi ([opisanych tutaj](active-directory-scim-provisioning.md))
* Możliwość dodawania łącza do aplikacji w [uruchamiania aplikacji usługi Office 365](https://blogs.office.com/2014/10/16/organize-office-365-new-app-launcher-2/) lub [panel dostępu usługi Azure AD](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users)

To nie tylko aplikacji SaaS, które używają, ale ma nie jeszcze został na dodawanej do galerii aplikacji Azure AD, ale aplikacji sieci web innych firm, które organizacja został wdrożony na serwerach, które możesz kontrolować, w chmurze lub lokalnie.

Te możliwości, nazywany również *szablony integracji aplikacji*, podaj punkty połączenia oparte na standardach aplikacji, które obsługują SAML, SCIM lub uwierzytelnianie oparte na formularzach i obejmują elastyczne opcje i ustawienia dla zgodności z szerokim liczbę aplikacji. 

## <a name="adding-an-unlisted-application"></a>Dodawanie nieznajdujące się na liście aplikacji
Aby połączyć aplikację przy użyciu szablonu usługi integracji aplikacji, zaloguj się do portalu Azure przy użyciu konta administratora usługi Azure Active Directory, a następnie przejdź do **usługi Active Directory > aplikacje dla przedsiębiorstw > nowej aplikacji > Inne niż galerii aplikacji** zaznacz **Dodaj**, a następnie **dodać aplikację z galerii**.

  ![][1]

Nieznajdujące się na liście aplikacji w galerii aplikacji można dodawać przez zaznaczenie **Non galerii aplikacji** kafelka, który jest wyświetlany w wynikach wyszukiwania, jeśli nie można znaleźć żądanej aplikacji. Po wprowadzeniu nazwy aplikacji, można skonfigurować opcje rejestracji jednokrotnej i zachowania. 

**Szybkie porady**: najlepszym rozwiązaniem, użyj funkcji wyszukiwania, aby sprawdzić, czy aplikacja już istnieje w galerii aplikacji. Czy aplikacja zostanie znaleziony i podaje opis "Logowanie jednokrotne", aplikacja jest już obsługiwane w przypadku federacyjnego logowania jednokrotnego.

  ![][2]

Dodawanie aplikacji w ten sposób zapewnia bardzo podobne do dostępne dla wstępnie zintegrowanych aplikacji. Aby rozpocząć, wybierz **skonfigurować logowanie jednokrotne**. Następnym ekranie przedstawia poniższe trzy opcje dotyczące konfigurowania rejestracji jednokrotnej, które zostały opisane w poniższych sekcjach.

  ![][3]

## <a name="saml-based-sign-on"></a>SAML na podstawie logowania jednokrotnego
Wybierz tę opcję, aby skonfigurować uwierzytelnianie oparte na protokole SAML dla aplikacji. Wymaga to, że obsługa aplikacji SAML 2.0, a zbierały informacje na temat sposobu korzystania z funkcji SAML aplikacji przed kontynuowaniem. Po wybraniu **dalej**, pojawi się monit o wprowadzenie trzech różnych adresów URL odpowiadającego SAML punktów końcowych dla aplikacji.

  ![][4]

Są to:

* **Zaloguj się na adres URL (SP inicjowane tylko)** — w przypadku, gdy użytkownik przechodzi do logowania się w tej aplikacji. Jeśli aplikacja jest skonfigurowany do wykonywania usługi zainicjował dostawcy logowania jednokrotnego, gdy użytkownik przechodzi do tego adresu URL, dostawcy usług wykonuje niezbędne przekierowanie do usługi Azure AD do uwierzytelniania i logowania użytkownika w. Jeśli to pole zostanie wypełnione, usługi Azure AD będzie używać tego adresu URL do uruchamiania aplikacji z usługi Office 365 i panelu dostępu usługi Azure AD. Jeśli to pole zostanie pominięte, a następnie usługi Azure AD, zamiast tego zostanie przeprowadzone dostawcy tożsamości-zainicjował logowania jednokrotnego, gdy aplikacja jest uruchamiana z usługi Office 365, Panel dostępu usługi Azure AD lub Azure AD pojedynczy adres URL logowania (copyable z karty Pulpit nawigacyjny).
* **Adres URL wystawcy** — adres URL wystawcy musi jednoznacznie wskazywać aplikacji, dla którego rejestracji jednokrotnej jest konfigurowany. Jest to wartość, która wysyła usługi Azure AD z powrotem do aplikacji jako **odbiorców** parametr tokenu SAML i aplikacji powinien go zweryfikować. Ta wartość jest także wyświetlany jako **identyfikator jednostki** w dowolnym metadanych SAML udostępniany przez aplikację. W dokumentacji aplikacji SAML szczegółowe informacje na temat jest jego wartość Identyfikatora jednostki lub odbiorców. Poniżej przedstawiono przykładowy sposób wyświetlania adresu URL odbiorców tokenu SAML zwrócony do aplikacji:
  
  ```
  <Subject>
    <NameID Format="urn:oasis:names:tc:SAML:2.0:nameid-format:unspecificed">chad.smith@example.com</NameID>
        <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer" />
      </Subject>
      <Conditions NotBefore="2014-12-19T01:03:14.278Z" NotOnOrAfter="2014-12-19T02:03:14.278Z">
        <AudienceRestriction>
          <Audience>https://tenant.example.com</Audience>
        </AudienceRestriction>
      </Conditions>
    ```

* **Adres URL odpowiedzi** — adres URL odpowiedzi jest, gdy aplikacja oczekuje odebrać tokenu SAML. To jest również nazywany **adres URL usługi konsumenta potwierdzenia (ACS)**. Sprawdź dokumentacji SAML aplikacji, aby uzyskać więcej informacji na jego odpowiedzi tokenu SAML adres URL lub adres URL usług ACS jest.
  Po te zostały wprowadzone, kliknij przycisk **dalej** do przejścia do następnego ekranu. Ten ekran informuje o co musi zostać skonfigurowane na stronie aplikacji do akceptowania tokenu SAML z usługi Azure AD.

  * Polecenie **skonfigurować Litware**.
  
    ![][5]

Wartości, które są wymagane będą się różnić w zależności od aplikacji, więc spróbuj dokumentacji SAML aplikacji, aby uzyskać szczegółowe informacje. **Logowania jednokrotnego** i **wylogowania** URL oba rozwiązania do tego samego punktu końcowego, który jest punktem końcowym obsługi żądań SAML dla swojego wystąpienia usługi Azure AD usługi. Adres URL wystawcy jest wartość, która jest wyświetlana jako "Wystawca" w tokenie SAML wystawiony dla aplikacji. 

Po skonfigurowaniu aplikacji kliknij **dalej** przycisk, a następnie **Complete** aby zamknąć okno dialogowe. 

## <a name="assigning-users-and-groups-to-your-saml-application"></a>Przypisywanie użytkowników i grup do aplikacji SAML
Po skonfigurowaniu aplikacji przy użyciu usługi Azure AD jako dostawca tożsamości SAML na podstawie jest już prawie gotowe do testów. Jako formant zabezpieczeń usługi Azure AD nie będzie wystawiać token, dzięki czemu mogą zalogować się do aplikacji, chyba że przyznano im dostęp przy użyciu usługi Azure AD. Użytkownicy mogą otrzymać dostęp bezpośrednio lub za pośrednictwem grupy, które są członkami. 

Aby przypisać użytkownika lub grupę do aplikacji, kliknij przycisk **Przypisz użytkowników** przycisku. Wybierz użytkownika lub grupy, które chcesz przypisać, a następnie wybierz **przypisać** przycisku.

  ![][6]

Przypisanie użytkownika umożliwi usługi Azure AD można wystawić token dla użytkownika, a także powoduje kafelka aplikacji są wyświetlane w panelu dostępu użytkownika. Kafelek aplikacji również będą wyświetlane w uruchamiający aplikację usługi Office 365, jeśli użytkownik korzysta z usługi Office 365. 

Możesz przekazać logo kafelka aplikacji przy użyciu **przekazać Logo** znajdującego się na **Konfiguruj** kartę dla aplikacji. 

### <a name="customizing-the-claims-issued-in-the-saml-token"></a>Dostosowywanie oświadczeń wydanych w tokenie SAML
Podczas uwierzytelniania użytkownika do aplikacji, usługi Azure AD wystawia SAML token aplikacja, która zawiera informacje (lub oświadczenia) dotyczące użytkownika, który unikatowo identyfikuje je. Domyślnie w tym nazwę użytkownika, adres e-mail, imię i nazwisko użytkownika. 

Umożliwia wyświetlenie i edytowanie oświadczenia wysyłane w tokenie SAML do aplikacji w obszarze **atrybuty** kartę.

  ![][7]

Istnieją dwie możliwe przyczyny, dlaczego konieczne może być Edycja oświadczeń wydanych w tokenie SAML: •zakres aplikacji została zapisana wymagają innego zestawu oświadczeń identyfikatorów URI lub wartości oświadczeń •Your aplikacja została wdrożona w taki sposób, który wymaga NameIdentifier oświadczeń inny niż nazwa użytkownika (nazwy głównej użytkownika AKA) przechowywanych w usłudze Azure Active Directory. 

Aby uzyskać informacje o tym, jak dodawać i edytować oświadczeń dotyczących następujących scenariuszy, zapoznaj się z tym [artykuł na temat dostosowywania oświadczeń](active-directory-saml-claims-customization.md). 

### <a name="testing-the-saml-application"></a>Testowanie aplikacji SAML
Po SAML adresy URL i certyfikatu zostały skonfigurowane w usłudze Azure AD i w aplikacji, użytkowników lub grup z przypisanym do aplikacji na platformie Azure, oświadczenia zostały sprawdzone i edytować, jeśli to konieczne, a następnie użytkownik jest gotowy do logowania się do aplikacji. 

Aby przetestować, po prostu logowania do usługi Azure AD panelu dostępu pod adresem https://myapps.microsoft.com przy użyciu konta użytkownika przypisany do aplikacji, a następnie kliknij Kafelek dla aplikacji, aby rozpocząć poza pojedynczy proces logowania jednokrotnego. Alternatywnie możesz przeglądać bezpośrednio adres URL logowania jednokrotnego dla aplikacji i zaloguj się z tego miejsca. 

Debugowanie wskazówki, zobacz [artykuł na temat debugowania na języku SAML logowanie jednokrotne do aplikacji](active-directory-saml-debugging.md) 

## <a name="password-single-sign-on"></a>Hasło logowania jednokrotnego
Wybierz tę opcję, aby skonfigurować [opartego na hasłach rejestracji jednokrotnej](active-directory-appssoaccess-whatis.md) dla aplikacji sieci web, z HTML strony logowania. Logowanie Jednokrotne opartego na hasłach, także określane jako hasło vaulting, umożliwia zarządzanie dostępem użytkowników i hasła do aplikacji sieci web, które nie obsługują federacji tożsamości. Jest również przydatne w przypadku scenariuszy, w którym musi kilku użytkowników do udostępnienia jednego konta, takich jak do kont aplikacji mediów społecznościowych w organizacji. 

Po wybraniu **dalej**, pojawi się monit o wprowadzenie adresu URL aplikacji sieci web stronę logowania w. Należy pamiętać, że musi być to strona, która zawiera nazwę użytkownika i hasło pól wejściowych. Po podaniu usługi Azure AD uruchamia proces przeanalizować strony logowania, wprowadź nazwę użytkownika i hasło, wprowadź. Jeśli proces zakończy się niepowodzeniem, następnie kolejno alternatywny proces instalowania rozszerzenia przeglądarki (wymaga programu Internet Explorer, Chrome lub Firefox), co umożliwi ręcznie przechwytywania pola.

Po przechwyceniu strony logowania, można przypisać użytkowników i grup i poświadczeń zasady można ustawić tak jak zwykły [hasło logowania jednokrotnego aplikacji](active-directory-appssoaccess-whatis.md).

Uwaga: Możesz przekazać logo kafelka aplikacji przy użyciu **przekazać Logo** znajdującego się na **Konfiguruj** kartę dla aplikacji. 

## <a name="existing-single-sign-on"></a>Istniejące rejestracji jednokrotnej
Wybierz tę opcję, aby dodać łącze do aplikacji portal firmy Panel dostępu usługi Azure AD lub usługi Office 365. Służy to można dodać łącza do aplikacji sieci web niestandardowe, które obecnie używają usługi Azure Active Directory Federation Services (lub innej usługi federacyjnej) zamiast Azure AD na potrzeby uwierzytelniania. Alternatywnie można dodać linków bezpośrednich do określonych stron SharePoint lub innych stron sieci web, które mają być wyświetlane na panele dostępu użytkownika. 

Po wybraniu **dalej**, pojawi się monit o wprowadzenie adresu URL aplikacji, aby utworzyć łącze do. Po ukończeniu użytkowników i grup można przypisać do aplikacji, co powoduje, że są wyświetlane w aplikacji [uruchamiania aplikacji usługi Office 365](https://blogs.office.com/2014/10/16/organize-office-365-new-app-launcher-2/) lub [panel dostępu usługi Azure AD](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users) dla tych użytkowników.

Uwaga: Możesz przekazać logo kafelka aplikacji przy użyciu **przekazać Logo** znajdującego się na **Konfiguruj** kartę dla aplikacji.

## <a name="related-articles"></a>Pokrewne artykuły
* [Indeks artykułów dotyczących zarządzania aplikacjami w usłudze Azure Active Directory](active-directory-apps-index.md)
* [Dostosowywanie oświadczeń wydanych w tokenie SAML dla wstępnie zintegrowanych aplikacji](active-directory-saml-claims-customization.md)
* [Rozwiązywanie problemów z systemem SAML logowania jednokrotnego](active-directory-saml-debugging.md)

<!--Image references-->
[1]: ./media/active-directory-saas-custom-apps/customapp1.png
[2]: ./media/active-directory-saas-custom-apps/customapp2.png
[3]: ./media/active-directory-saas-custom-apps/customapp3.png
[4]: ./media/active-directory-saas-custom-apps/customapp4.png
[5]: ./media/active-directory-saas-custom-apps/customapp5.png
[6]: ./media/active-directory-saas-custom-apps/customapp6.png
[7]: ./media/active-directory-saas-custom-apps/customapp7.png