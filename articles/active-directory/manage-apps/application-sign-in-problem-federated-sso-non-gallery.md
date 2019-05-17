---
title: Problemy z logowaniem do aplikacji spoza galerii, skonfigurowanej dla federacyjnego logowania jednokrotnego | Dokumentacja firmy Microsoft
description: Wskazówki dotyczące określonych problemów, które mogą występować podczas logowania się do aplikacji, skonfigurowanej do obsługi opartej na SAML federacyjne logowanie jednokrotne z usługą Azure AD
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 38decf98707231c21427f7a22dd4d12adb41852b
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2019
ms.locfileid: "65825435"
---
# <a name="problems-signing-in-to-a-non-gallery-application-configured-for-federated-single-sign-on"></a>Problemy z logowaniem do aplikacji spoza galerii, skonfigurowanej do obsługi federacyjnego logowania jednokrotnego

Aby rozwiązać problemy dotyczące logowania poniżej, zaleca się, że postępuj zgodnie z tych sugestii, aby uzyskać lepsze diagnostyki i automatyzować czynności rozwiązujących problem:

- Zainstaluj [Moje aplikacje Secure rozszerzenia przeglądarki do obsługi](access-panel-extension-problem-installing.md) ułatwiające usługi Azure Active Directory (Azure AD), aby zapewnić lepszą diagnostykę i rozwiązania, korzystając z testowania środowisko w witrynie Azure portal.
- Odtwórz błąd przy użyciu środowiska testowania na stronie konfiguracji aplikacji w witrynie Azure portal. Dowiedz się więcej o [SAML debugowania aplikacji opartych na pojedynczego logowania jednokrotnego](../develop/howto-v1-debug-saml-sso-issues.md)

## <a name="application-not-found-in-directory"></a>Nie można odnaleźć w katalogu aplikacji

*Błąd AADSTS70001: Aplikacja o identyfikatorze `https://contoso.com` nie został znaleziony w katalogu*.

**Możliwa przyczyna**

Wystawcę, którego atrybut wysyła z aplikacji do usługi Azure AD w żądaniu języka SAML nie jest zgodna wartość identyfikatora skonfigurowaną w aplikacji usługi Azure AD.

**Rozdzielczość**

Upewnij się, że `Issuer` pasuje do atrybutu w żądaniu języka SAML wartość identyfikatora skonfigurowaną w usłudze Azure AD. Jeśli używasz [testowania środowisko](../develop/howto-v1-debug-saml-sso-issues.md) w witrynie Azure portal przy użyciu rozszerzenia przeglądarki Moje zabezpieczenia aplikacji, nie trzeba ręcznie, wykonaj następujące kroki.

1. Otwórz [ **witryny Azure portal** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego** lub **Współadministratora.**

2. Otwórz **rozszerzenia usługi Azure Active Directory** , klikając **wszystkich usług** w górnej części menu główne menu nawigacji po lewej stronie.

3. Wpisz **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz pozycję **usługi Azure Active Directory** elementu.

4. Kliknij przycisk **aplikacje dla przedsiębiorstw** menu nawigacji po lewej stronie usługi Azure Active Directory.

5. Kliknij przycisk **wszystkie aplikacje** Aby wyświetlić listę wszystkich aplikacji.

   * Jeśli nie widzisz aplikacji, chcesz, aby wyświetlić tutaj użyć **filtru** formant w górnej części **listę wszystkich aplikacji** i ustaw **Pokaż** opcję **wszystkie Aplikacje.**

6. Wybierz aplikację, którą chcesz skonfigurować logowanie jednokrotne.

7. Po załadowaniu aplikacji, kliknij przycisk **logowanie jednokrotne** menu nawigacji po lewej stronie aplikacji.

8. Po załadowaniu aplikacji otwórz **podstawową konfigurację protokołu SAML**. Sprawdź, czy wartość w polu tekstowym Identyfikator zgodna wartość identyfikatora wyświetlane w błędzie.

## <a name="the-reply-address-does-not-match-the-reply-addresses-configured-for-the-application"></a>Adres, który jest niezgodny z adresy zwrotne skonfigurowane dla aplikacji. 

*Błąd AADSTS50011: Adres zwrotny `https://contoso.com` jest niezgodna z adresy zwrotne skonfigurowane dla aplikacji* 

**Możliwa przyczyna** 

Wartość AssertionConsumerServiceURL w żądaniu języka SAML nie jest zgodna wartość adresu URL odpowiedzi lub wzorzec skonfigurowane w usłudze Azure AD. Wartość AssertionConsumerServiceURL w żądaniu języka SAML jest adres URL, zostanie wyświetlony w błędzie. 

**Rozdzielczość** 

Upewnij się, że `Issuer` pasuje do atrybutu w żądaniu języka SAML wartość identyfikatora skonfigurowaną w usłudze Azure AD. Jeśli używasz [testowania środowisko](../develop/howto-v1-debug-saml-sso-issues.md) w witrynie Azure portal przy użyciu rozszerzenia przeglądarki Moje zabezpieczenia aplikacji, nie trzeba ręcznie, wykonaj następujące kroki.
 
1. Otwórz [ **witryny Azure portal** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego** lub **Współadministratora.** 

2. Otwórz **rozszerzenia usługi Azure Active Directory** , klikając **wszystkich usług** w górnej części menu główne menu nawigacji po lewej stronie. 

3. Wpisz **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz pozycję **usługi Azure Active Directory** elementu. 

4. Kliknij przycisk **aplikacje dla przedsiębiorstw** menu nawigacji po lewej stronie usługi Azure Active Directory. 

5. Kliknij przycisk **wszystkie aplikacje** Aby wyświetlić listę wszystkich aplikacji. 

   * Jeśli nie widzisz aplikacji, chcesz, aby wyświetlić tutaj użyć **filtru** formant w górnej części **listę wszystkich aplikacji** i ustaw **Pokaż** opcję **wszystkie Aplikacje.**
  
6. Wybierz aplikację, którą chcesz skonfigurować logowanie jednokrotne

7. Po załadowaniu aplikacji, kliknij przycisk **logowanie jednokrotne** menu nawigacji po lewej stronie aplikacji.

8. Po załadowaniu aplikacji otwórz **podstawową konfigurację protokołu SAML**. Sprawdź lub zaktualizuj tę wartość w polu tekstowym adres URL odpowiedzi, aby dopasować `AssertionConsumerServiceURL` wartość w żądaniu języka SAML.    
    
Po zaktualizowaniu wartość adresu URL odpowiedzi w usłudze Azure AD i odpowiada wartości wysyłane przez aplikację w żądaniu języka SAML, należy zalogować się do aplikacji.

## <a name="user-not-assigned-a-role"></a>Nie przypisaną rolę użytkownika

*Błąd AADSTS50105: Zalogowany użytkownik `brian\@contoso.com` nie jest przypisany do roli dla aplikacji*

**Możliwa przyczyna**

Użytkownikowi nie udzielono dostępu do aplikacji w usłudze Azure AD.

**Rozdzielczość**

Aby przypisać co najmniej jednego użytkownika do aplikacji bezpośrednio, wykonaj poniższe kroki. Jeśli używasz [testowania środowisko](../develop/howto-v1-debug-saml-sso-issues.md) w witrynie Azure portal przy użyciu rozszerzenia przeglądarki Moje zabezpieczenia aplikacji, nie trzeba ręcznie, wykonaj następujące kroki.

1. Otwórz [ **witryny Azure portal** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego.**

2. Otwórz **rozszerzenia usługi Azure Active Directory** , klikając **wszystkich usług** w górnej części menu główne menu nawigacji po lewej stronie.

3. Wpisz **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz pozycję **usługi Azure Active Directory** elementu.

4. Kliknij przycisk **aplikacje dla przedsiębiorstw** menu nawigacji po lewej stronie usługi Azure Active Directory.

5. Kliknij przycisk **wszystkie aplikacje** Aby wyświetlić listę wszystkich aplikacji.

   * Jeśli nie widzisz aplikacji, chcesz, aby wyświetlić tutaj użyć **filtru** formant w górnej części **listę wszystkich aplikacji** i ustaw **Pokaż** opcję **wszystkie Aplikacje.**

6. Wybierz aplikację, którą chcesz przypisać do użytkownika z listy.

7. Po załadowaniu aplikacji, kliknij przycisk **użytkowników i grup** menu nawigacji po lewej stronie aplikacji.

8. Kliknij przycisk **Dodaj** przycisk w górnej części **użytkowników i grup** liście, aby otworzyć **Dodaj przydziału** okienka.

9. Kliknij przycisk **użytkowników i grup** selektor z **Dodaj przydziału** okienka.

10. Wpisz **Pełna nazwa** lub **adres e-mail** użytkownika, jesteś zainteresowany przypisywania do **wyszukiwanie według nazwy lub adresu e-mail** pola wyszukiwania.

11. Umieść kursor nad **użytkownika** na liście, aby wyświetlić **wyboru**. Kliknij pole wyboru obok logo, aby dodać użytkownika, aby lub zdjęcie w profilu użytkownika **wybrane** listy.

12. **Opcjonalnie:** Jeśli chcesz **dodać więcej niż jednego użytkownika**, typ w innym **Pełna nazwa** lub **adres e-mail** do **wyszukiwanie według nazwy lub adresu e-mail** pole wyszukiwania, a następnie kliknij pole wyboru, aby dodać użytkownika do **wybrane** listy.

13. Gdy to zrobisz, Wybieranie użytkowników, kliknij przycisk **wybierz** przycisk, aby dodać je do listy użytkowników i grup do przypisania do aplikacji.

14. **Opcjonalnie:** kliknij **wybierz rolę** selektorze **Dodaj przydziału** okienku wybierz rolę, aby przypisać użytkownikom wybrania.

15. Kliknij przycisk **przypisać** przycisk, aby przypisać aplikację do wybranych użytkowników.

Po krótkim czasie użytkowników, dla których wybrano mogli uruchamiać te aplikacje za pomocą metod opisanych w sekcji Opis rozwiązania.

## <a name="not-a-valid-saml-request"></a>Nie prawidłowe żądania języka SAML

*Błąd AADSTS75005: Żądanie nie jest prawidłowy komunikat protokołu Saml2.*

**Możliwa przyczyna**

Usługa Azure AD nie obsługuje żądania SAML wysłanego przez aplikację na potrzeby logowania jednokrotnego. Niektóre typowe problemy to:

-   Brak wymaganych pól w żądaniu języka SAML

-   Zakodowana metoda żądania SAML

**Rozdzielczość**

1.  Przechwyć żądanie języka SAML. Postępuj zgodnie z samouczka w [sposób debugowania opartej na SAML logowania jednokrotnego do aplikacji w usłudze Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging) Aby dowiedzieć się, jak przechwytywać żądania języka SAML.

2.  Skontaktuj się z dostawcą aplikacji i udziału:

    -   Żądanie języka SAML

    -   [Wymagania protokołu usługi Azure AD pojedynczego logowania jednokrotnego SAML](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference)

Dostawca aplikacji należy zweryfikować, czy obsługują one wdrożenia usługi Azure AD SAML dla logowania jednokrotnego.

## <a name="misconfigured-application"></a>Nieprawidłowej konfiguracji aplikacji

*Błąd AADSTS650056: Aplikacja nieprawidłowo skonfigurowane. Może to być spowodowane jedną z następujących przyczyn: Klient nie ma na liście jakichkolwiek uprawnień "AAD Graph" żądanych uprawnień w rejestracji aplikacji klienta. Lub, w dzierżawie nie wyraził zgody administratora. Lub Sprawdź identyfikator aplikacji, aby upewnić się, że pasuje do identyfikatora aplikacji klienta skonfigurowanego w żądaniu. Skontaktuj się z administratorem, aby naprawić konfigurację, lub zgody w imieniu dzierżawy.* .

**Możliwa przyczyna**

`Issuer` Atrybut wysyłane z aplikacji do usługi Azure AD w żądaniu języka SAML nie jest zgodna wartość identyfikatora skonfigurowaną dla aplikacji w usłudze Azure AD.

**Rozdzielczość**

Upewnij się, że `Issuer` pasuje do atrybutu w żądaniu języka SAML wartość identyfikatora skonfigurowaną w usłudze Azure AD. Jeśli używasz [testowania środowisko](../develop/howto-v1-debug-saml-sso-issues.md) w witrynie Azure portal przy użyciu rozszerzenia przeglądarki Moje zabezpieczenia aplikacji, nie trzeba ręcznie, wykonaj następujące kroki:

1.  Otwórz [ **witryny Azure portal** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego** lub **współadministrator**.

1.  Otwórz **rozszerzenia usługi Azure Active Directory** , wybierając **wszystkich usług** w górnej części menu główne menu nawigacji po lewej stronie.

1.  Typ **"Azure Active Directory"** w filtru pole wyszukiwania i wybierz pozycję **usługi Azure Active Directory** elementu.

1.  Wybierz **aplikacje dla przedsiębiorstw** menu nawigacji po lewej stronie usługi Azure Active Directory.

1.  Wybierz **wszystkie aplikacje** Aby wyświetlić listę wszystkich aplikacji.

    Jeśli nie widzisz aplikacji, chcesz, aby wyświetlić tutaj użyć **filtru** formant w górnej części **listę wszystkich aplikacji** i ustaw **Pokaż** opcję **wszystkie Aplikacje**.

1.  Wybierz aplikację, którą chcesz skonfigurować dla logowania jednokrotnego.

1.  Po załadowaniu aplikacji otwórz **podstawową konfigurację protokołu SAML**. Sprawdź, czy wartość w polu tekstowym Identyfikator zgodna wartość identyfikatora wyświetlane w błędzie.

## <a name="certificate-or-key-not-configured"></a>Certyfikatu lub klucza nieskonfigurowane

Błąd AADSTS50003: Nie skonfigurowano klucza podpisywania.

**Możliwa przyczyna**

Obiekt aplikacji jest uszkodzony, i usługi Azure AD nie rozpoznaje certyfikatu skonfigurowanego dla aplikacji.

**Rozdzielczość**

Aby usunąć i utworzyć nowy certyfikat, wykonaj następujące czynności:

1. Otwórz [ **witryny Azure portal** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego** lub **Współadministratora.**

2. Otwórz **rozszerzenia usługi Azure Active Directory** , klikając **wszystkich usług** w górnej części menu główne menu nawigacji po lewej stronie.

3. Wpisz **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz pozycję **usługi Azure Active Directory** elementu.

4. Kliknij przycisk **aplikacje dla przedsiębiorstw** menu nawigacji po lewej stronie usługi Azure Active Directory.

5. Kliknij przycisk **wszystkie aplikacje** Aby wyświetlić listę wszystkich aplikacji.

   * Jeśli nie widzisz aplikacji, chcesz, aby wyświetlić tutaj użyć **filtru** formant w górnej części **listę wszystkich aplikacji** i ustaw **Pokaż** opcję **wszystkie Aplikacje.**

6. Wybierz aplikację, którą chcesz skonfigurować logowanie jednokrotne.

7. Po załadowaniu aplikacji, kliknij przycisk **logowanie jednokrotne** menu nawigacji po lewej stronie aplikacji.

8. Kliknij przycisk **Utwórz nowy certyfikat** w obszarze **certyfikat podpisywania SAML** sekcji.

9. Wybierz datę wygaśnięcia. Następnie kliknij przycisk **Zapisz.**

10. Sprawdź **Ustaw nowy certyfikat jako aktywny** przesłonić aktywny certyfikat. Następnie kliknij przycisk **Zapisz** w górnej części okienka i zaakceptuj, aby aktywować certyfikat przerzucania.

11. W obszarze **certyfikat podpisywania SAML** kliknij **Usuń** do usunięcia **nieużywane** certyfikatu.

## <a name="saml-request-not-present-in-the-request"></a>Żądanie języka SAML nie znajduje się w żądaniu

*Błąd AADSTS750054: SAMLRequest lub SAMLResponse musi być obecny, ponieważ parametry ciągu w żądaniu HTTP do przekierowania protokołu SAML powiązania zapytania.*

**Możliwa przyczyna**

Usługa Azure AD nie był w stanie zidentyfikować żądanie języka SAML, w ramach parametrów adresu URL w żądaniu HTTP. Może to nastąpić, jeśli aplikacja nie używa przekierowania HTTP powiązanie podczas wysyłania żądania SAML do usługi Azure AD.

**Rozdzielczość**

Aplikacja musi wysyłać żądania języka SAML, kodowane do Nagłówek lokalizacji przy użyciu protokołu HTTP przekierowanie powiązania. Aby dowiedzieć się więcej o sposobie implementacji tego rozwiązania, zapoznaj się z sekcją powiązania przekierowania HTTP w [dokumencie specyfikacji protokołu SAML](https://docs.oasis-open.org/security/saml/v2.0/saml-bindings-2.0-os.pdf).

## <a name="azure-ad-is-sending-the-token-to-an-incorrect-endpoint"></a>Usługa Azure AD wysyła ten token nieprawidłowy punkt końcowy

**Możliwa przyczyna**

Podczas rejestracji jednokrotnej Jeśli żądanie logowania nie zawiera adresu URL odpowiedzi jawne (adres URL usługi Assertion konsumenta), a następnie wybierze usługi Azure AD, wszystkich skonfigurowanych polegać adresy URL dla tej aplikacji. Nawet wtedy, gdy aplikacja ma skonfigurowano adresu URL odpowiedzi jawne, użytkownik może być przekierowany https://127.0.0.1:444. 

Podczas dodawania tej aplikacji jako aplikacji spoza galerii usługa Azure Active Directory utworzyła ten adres URL odpowiedzi jako wartość domyślną. To działanie zostało zmienione i usługa Azure Active Directory nie dodaje już domyślnie tego adresu URL. 

**Rozdzielczość**

Usuń adresy URL odpowiedzi nieużywane, skonfigurowane dla aplikacji.

1.  Otwórz [ **witryny Azure portal** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego** lub **współadministrator**.

2.  Otwórz **rozszerzenia usługi Azure Active Directory** , wybierając **wszystkich usług** w górnej części menu główne menu nawigacji po lewej stronie.

3.  Typ **"Azure Active Directory"** w filtru pole wyszukiwania i wybierz pozycję **usługi Azure Active Directory** elementu.

4.  Wybierz **aplikacje dla przedsiębiorstw** menu nawigacji po lewej stronie usługi Azure Active Directory.

5.  Wybierz **wszystkie aplikacje** Aby wyświetlić listę wszystkich aplikacji.

    Jeśli nie widzisz aplikacji, chcesz, aby wyświetlić tutaj użyć **filtru** formant w górnej części **listę wszystkich aplikacji** i ustaw **Pokaż** opcję **wszystkie Aplikacje**.

6.  Wybierz aplikację, którą chcesz skonfigurować dla logowania jednokrotnego.

7.  Po załadowaniu aplikacji otwórz **podstawową konfigurację protokołu SAML**. W **adres URL odpowiedzi (adres URL usługi Assertion konsumenta)**, Usuń nieużywany lub adresy URL odpowiedzi domyślnej utworzonych przez system. Na przykład `https://127.0.0.1:444/applications/default.aspx`.



## <a name="problem-when-customizing-the-saml-claims-sent-to-an-application"></a>Problem podczas dostosowywania oświadczenia języka SAML wysyłane do aplikacji

Aby dowiedzieć się, jak dostosować oświadczeń atrybutów SAML, które są wysyłane do aplikacji, zobacz [Mapowanie oświadczeń w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping) Aby uzyskać więcej informacji.

## <a name="next-steps"></a>Kolejne kroki
[Wymagania protokołu usługi Azure AD pojedynczego logowania jednokrotnego SAML](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference)
