---
title: Problemy z logowaniem się do aplikacji galerii logowania jednokrotnego federacyjnego | Dokumenty firmy Microsoft
description: Wskazówki dotyczące określonych błędów podczas logowania do aplikacji skonfigurowanej dla logowania jednokrotnego opartego na saml za pomocą usługi Azure AD
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
ms.date: 02/18/2019
ms.author: mimart
ms.reviewer: luleon, asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 874d273e26a728afc0a1dc1a16852016797067ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77367904"
---
# <a name="problems-signing-in-to-a-gallery-application-configured-for-federated-single-sign-on"></a>Problemy z logowaniem się do aplikacji galerii skonfigurowanych do logowania sfederowanego

Aby rozwiązać poniższe problemy z logowaniem, zalecamy wykonanie następujących sugestii, aby uzyskać lepszą diagnozę i zautomatyzować kroki rozwiązywania problemów:

- Zainstaluj [rozszerzenie moja aplikacja bezpieczna przeglądarka,](access-panel-extension-problem-installing.md) aby pomóc usługi Azure Active Directory (Azure AD) w zapewnianiu lepszej diagnostyki i rozpoznawania podczas korzystania z testowania w witrynie Azure portal.
- Odtwórz błąd przy użyciu środowiska testowania na stronie konfiguracji aplikacji w witrynie Azure portal. Dowiedz się więcej o [aplikacjach logowania jednookrotnego opartych na debugowaniu SAML](../azuread-dev/howto-v1-debug-saml-sso-issues.md)


## <a name="application-not-found-in-directory"></a>Nie znaleziono aplikacji w katalogu

*Błąd AADSTS70001: Nie znaleziono aplikacji\/o identyfikatorze "https: /contoso.com" w katalogu*.

**Możliwa przyczyna**

Atrybut `Issuer` wysłany z aplikacji do usługi Azure AD w żądaniu SAML nie jest zgodny z wartością identyfikatora skonfigurowaną dla aplikacji w usłudze Azure AD.

**Rozdzielczość**

Upewnij się, że `Issuer` atrybut w żądaniu SAML jest zgodny z wartością identyfikatora skonfigurowaną w usłudze Azure AD. Jeśli używasz [środowiska testowania](../azuread-dev/howto-v1-debug-saml-sso-issues.md) w witrynie Azure portal z rozszerzeniem my apps bezpiecznej przeglądarki, nie trzeba ręcznie wykonać następujące kroki.

1.  Otwórz [**witrynę Azure portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny** lub **współadministrator**.

1.  Otwórz **rozszerzenie usługi Azure Active Directory,** wybierając **wszystkie usługi** u góry głównego menu nawigacji po lewej stronie.

1.  Wpisz **"Usługa Azure Active Directory"** w polu wyszukiwania filtru i wybierz element **usługi Azure Active Directory.**

1.  Wybierz **aplikacje dla przedsiębiorstw** z menu nawigacji po lewej stronie usługi Azure Active Directory.

1.  Wybierz **pozycję Wszystkie aplikacje,** aby wyświetlić listę wszystkich aplikacji.

    Jeśli nie widzisz aplikacji, która ma być pokazana w tym miejscu, użyj kontrolki **Filtruj** u góry **listy Wszystkie aplikacje** i ustaw opcję **Pokaż** na **Wszystkie aplikacje**.

1.  Wybierz aplikację, którą chcesz skonfigurować do logowania jednokrotnego.

1.  Po załadowaniu aplikacji otwórz **podstawową konfigurację protokołu SAML**. Sprawdź, czy wartość w polu tekstowym Identyfikator jest zgodna z wartością wartości identyfikatora wyświetlaną w błędzie.



## <a name="the-reply-address-does-not-match-the-reply-addresses-configured-for-the-application"></a>Adres odpowiedzi nie jest zgodny z adresami odpowiedzi skonfigurowanym dla aplikacji

*Błąd AADSTS50011: Adres odpowiedzi "https:\//contoso.com" nie jest zgodny z adresami odpowiedzi skonfigurowanym dla aplikacji*

**Możliwa przyczyna**

Wartość `AssertionConsumerServiceURL` w żądaniu SAML nie jest zgodna z wartością adresu URL odpowiedzi lub wzorcem skonfigurowanym w usłudze Azure AD. Wartość `AssertionConsumerServiceURL` w żądaniu SAML jest adresem URL widocznym w błędzie.

**Rozdzielczość**

Upewnij się, że `AssertionConsumerServiceURL` wartość w żądaniu SAML jest zgodna z wartością adresu URL odpowiedzi skonfigurowaną w usłudze Azure AD. Jeśli używasz [środowiska testowania](../azuread-dev/howto-v1-debug-saml-sso-issues.md) w witrynie Azure portal z rozszerzeniem my apps bezpiecznej przeglądarki, nie trzeba ręcznie wykonać następujące kroki.

1.  Otwórz [**witrynę Azure portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny** lub **współadministrator**.

1.  Otwórz **rozszerzenie usługi Azure Active Directory,** wybierając **wszystkie usługi** u góry głównego menu nawigacji po lewej stronie.

1.  Wpisz **"Usługa Azure Active Directory"** w polu wyszukiwania filtru i wybierz element **usługi Azure Active Directory.**

1.  Wybierz **aplikacje dla przedsiębiorstw** z menu nawigacji po lewej stronie usługi Azure Active Directory.

1.  Wybierz **pozycję Wszystkie aplikacje,** aby wyświetlić listę wszystkich aplikacji.

    Jeśli nie widzisz aplikacji, która ma być pokazana w tym miejscu, użyj kontrolki **Filtruj** u góry **listy Wszystkie aplikacje** i ustaw opcję **Pokaż** na **Wszystkie aplikacje**.

1.  Wybierz aplikację, którą chcesz skonfigurować do logowania jednokrotnego.

1.  Po załadowaniu aplikacji otwórz **podstawową konfigurację protokołu SAML**. Sprawdź lub zaktualizuj wartość w `AssertionConsumerServiceURL` polu tekstowym Odpowiedz adres URL, aby dopasować wartość w żądaniu SAML.    
    
Po zaktualizowaniu wartości adresu URL odpowiedzi w usłudze Azure AD i jest zgodna z wartością wysłaną przez aplikację w żądaniu SAML, powinno być możliwe zalogowanie się do aplikacji.

## <a name="user-not-assigned-a-role"></a>Użytkownik nie przypisał roli

*Błąd AADSTS50105: Zalogowany użytkownik\@"brian contoso.com" nie jest przypisany do roli aplikacji*.

**Możliwa przyczyna**

Użytkownikowi nie udzielono dostępu do aplikacji w usłudze Azure AD.

**Rozdzielczość**

Aby bezpośrednio przypisać jednego lub więcej użytkowników do aplikacji, wykonaj poniższe czynności. Jeśli używasz [środowiska testowania](../azuread-dev/howto-v1-debug-saml-sso-issues.md) w witrynie Azure portal z rozszerzeniem my apps bezpiecznej przeglądarki, nie trzeba ręcznie wykonać następujące kroki.

1.  Otwórz [**witrynę Azure portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny**.

1.  Otwórz **rozszerzenie usługi Azure Active Directory,** wybierając **wszystkie usługi** u góry głównego menu nawigacji po lewej stronie.

1.  Wpisz **"Usługa Azure Active Directory"** w polu wyszukiwania filtru i wybierz element **usługi Azure Active Directory.**

1.  Wybierz **aplikacje dla przedsiębiorstw** z menu nawigacji po lewej stronie usługi Azure Active Directory.

1.  Wybierz **pozycję Wszystkie aplikacje,** aby wyświetlić listę wszystkich aplikacji.

    Jeśli nie widzisz aplikacji, która ma być pokazana w tym miejscu, użyj kontrolki **Filtruj** u góry **listy Wszystkie aplikacje** i ustaw opcję **Pokaż** na **Wszystkie aplikacje**.

1.  Z listy aplikacji wybierz tę, do której chcesz przypisać użytkownika.

1.  Po załadowaniu aplikacji wybierz **pozycję Użytkownicy i grupy** z menu nawigacji po lewej stronie aplikacji.

1.  Kliknij przycisk **Dodaj** u góry listy **Użytkownicy i grupy,** aby otworzyć okienko **Dodaj przypisanie.**

1.  Wybierz selektor **Użytkownicy i grupy** z okienka **Dodaj przypisanie.**

1. W polu wyszukiwania **Wyszukaj według nazwy lub adresu e-mail** wpisz pełną nazwę lub adres e-mail użytkownika, którego chcesz dodać.

1. Umieść wskaźnik myszy na **użytkowniku** na liście, aby wyświetlić **pole wyboru**. Kliknij pole wyboru obok zdjęcia profilowego lub logo użytkownika, aby dodać go do **wybranej** listy.

1. **Opcjonalnie:** Jeśli chcesz **dodać więcej niż jednego użytkownika,** wpisz inną pełną nazwę lub adres e-mail w polu wyszukiwania według nazwy lub adresu **e-mail,** a następnie kliknij to pole wyboru, aby dodać użytkownika do **wybranej** listy.

1. Po zakończeniu wybierania użytkowników kliknij przycisk **Wybierz,** aby dodać ich do listy użytkowników i grup, które mają być przypisane do aplikacji.

1. **Opcjonalnie:** Kliknij selektor **Wybierz rolę** w okienku **Dodaj przypisanie,** aby wybrać rolę przypisaną do wybranych użytkowników.

1. Kliknij przycisk **Przypisz,** aby przypisać aplikację do wybranych użytkowników.

Po krótkim czasie wybrani użytkownicy będą mogli uruchamiać te aplikacje przy użyciu metod opisanych w sekcji opis rozwiązania.

## <a name="not-a-valid-saml-request"></a>Nieprawidłowe żądanie SAML

*Błąd AADSTS75005: Żądanie nie jest prawidłowym komunikatem protokołu Saml2.*

**Możliwa przyczyna**

Usługa Azure AD nie obsługuje żądania SAML wysłanego przez aplikację na potrzeby logowania jednokrotnego. Typowe problemy to:

-   Brak wymaganych pól w żądaniu SAML
-   Zakodowana metoda żądania SAML

**Rozdzielczość**

1. Przechwyć żądanie SAML. Wykonaj samouczek [Jak debugować samol oparte na logowaniu jednokrotnym do aplikacji w usłudze Azure AD,](../azuread-dev/howto-v1-debug-saml-sso-issues.md) aby dowiedzieć się, jak przechwycić żądanie SAML.

1. Skontaktuj się z dostawcą aplikacji i udostępnij następujące informacje:

   -   Żądanie SAML

   -   [Wymagania protokołu SAML logowania jednokrotnego usługi Azure AD](../develop/single-sign-on-saml-protocol.md)

Dostawca aplikacji należy sprawdzić, czy obsługują implementację saml usługi Azure AD dla logowania jednokrotnego.

## <a name="misconfigured-application"></a>Nieprawidłowo skonfigurowana aplikacja

*Błąd AADSTS650056: Nieprawidłowo skonfigurowana aplikacja. Może to być spowodowane jedną z następujących czynności: Klient nie ma żadnych uprawnień w żądanych uprawnień w rejestracji aplikacji klienta. Lub administrator nie wyraził zgody w dzierżawie. Lub sprawdź identyfikator aplikacji w żądaniu, aby upewnić się, że jest zgodny z identyfikatorem skonfigurowanych aplikacji klienckich. Skontaktuj się z administratorem, aby naprawić konfigurację lub zgodę w imieniu najemcy.*.

**Możliwa przyczyna**

Atrybut `Issuer` wysłany z aplikacji do usługi Azure AD w żądaniu SAML nie jest zgodny z wartością identyfikatora skonfigurowaną dla aplikacji w usłudze Azure AD.

**Rozdzielczość**

Upewnij się, że `Issuer` atrybut w żądaniu SAML jest zgodny z wartością identyfikatora skonfigurowaną w usłudze Azure AD. Jeśli korzystasz z [środowiska testowania](../azuread-dev/howto-v1-debug-saml-sso-issues.md) w witrynie Azure portal z rozszerzeniem moja aplikacja bezpieczna przeglądarka, nie musisz ręcznie wykonać następujących kroków:

1.  Otwórz [**witrynę Azure portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny** lub **współadministrator**.

1.  Otwórz **rozszerzenie usługi Azure Active Directory,** wybierając **wszystkie usługi** u góry głównego menu nawigacji po lewej stronie.

1.  Wpisz **"Usługa Azure Active Directory"** w polu wyszukiwania filtru i wybierz element **usługi Azure Active Directory.**

1.  Wybierz **aplikacje dla przedsiębiorstw** z menu nawigacji po lewej stronie usługi Azure Active Directory.

1.  Wybierz **pozycję Wszystkie aplikacje,** aby wyświetlić listę wszystkich aplikacji.

    Jeśli nie widzisz aplikacji, która ma być pokazana w tym miejscu, użyj kontrolki **Filtruj** u góry **listy Wszystkie aplikacje** i ustaw opcję **Pokaż** na **Wszystkie aplikacje**.

1.  Wybierz aplikację, którą chcesz skonfigurować do logowania jednokrotnego.

1.  Po załadowaniu aplikacji otwórz **podstawową konfigurację protokołu SAML**. Sprawdź, czy wartość w polu tekstowym Identyfikator jest zgodna z wartością wartości identyfikatora wyświetlaną w błędzie.


## <a name="certificate-or-key-not-configured"></a>Certyfikat lub klucz nie skonfigurowany

*Błąd AADSTS50003: Brak skonfigurowanego klucza podpisywania.*

**Możliwa przyczyna**

Obiekt aplikacji jest uszkodzony, a usługa Azure AD nie rozpoznaje certyfikatu skonfigurowany dla aplikacji.

**Rozdzielczość**

Aby usunąć i utworzyć nowy certyfikat, wykonaj poniższe czynności:

1. Otwórz [**witrynę Azure portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny** lub **współadministrator**.

1. Otwórz **rozszerzenie usługi Azure Active Directory,** klikając **pozycję Wszystkie usługi** u góry głównego menu nawigacji po lewej stronie.

1. Wpisz **"Usługa Azure Active Directory"** w polu wyszukiwania filtru i wybierz element **usługi Azure Active Directory.**

1. Wybierz **aplikacje dla przedsiębiorstw** z menu nawigacji po lewej stronie usługi Azure Active Directory.

1. Wybierz **pozycję Wszystkie aplikacje,** aby wyświetlić listę wszystkich aplikacji.

    Jeśli nie widzisz aplikacji, która ma być pokazana w tym miejscu, użyj kontrolki **Filtruj** u góry **listy Wszystkie aplikacje** i ustaw opcję **Pokaż** na **Wszystkie aplikacje**.

1. Wybierz aplikację, którą chcesz skonfigurować logowanie jednokrotne

1. Po załadowaniu aplikacji kliknij **logowanie jednokrotne** z menu nawigacji po lewej stronie aplikacji.

1. Wybierz **pozycję Utwórz nowy certyfikat** w sekcji **Podpisywanie SAML Certyfikat.**

1. Wybierz pozycję Data wygaśnięcia, a następnie kliknij przycisk **Zapisz**.

1. Zaznacz **pole wyboru Uaktywność nowego certyfikatu** w celu zastąpienia aktywnego certyfikatu. Następnie kliknij przycisk **Zapisz** w górnej części okienka i zaakceptuj, aby aktywować certyfikat przerzucania.

1. W sekcji **Saml Podpisywanie certyfikatu** kliknij przycisk **Usuń,** aby usunąć **nieużyny** certyfikat.

## <a name="saml-request-not-present-in-the-request"></a>SAML Żądanie nie obecny w żądaniu

*Błąd AADSTS750054: SAMLRequest lub SAMLResponse musi być obecny jako parametry ciągu zapytania w żądaniu HTTP dla powiązania PRZEKIEROWANIE SAML.*

**Możliwa przyczyna**

Usługa Azure AD nie może zidentyfikować żądania SAML w parametrach adresu URL w żądaniu HTTP. Może się tak zdarzyć, jeśli aplikacja nie używa powiązania przekierowania HTTP podczas wysyłania żądania SAML do usługi Azure AD.

**Rozdzielczość**

Aplikacja musi wysłać żądanie SAML zakodowane w nagłówku lokalizacji przy użyciu powiązania przekierowania HTTP. Aby dowiedzieć się więcej o sposobie implementacji tego rozwiązania, zapoznaj się z sekcją powiązania przekierowania HTTP w [dokumencie specyfikacji protokołu SAML](https://docs.oasis-open.org/security/saml/v2.0/saml-bindings-2.0-os.pdf).

## <a name="azure-ad-is-sending-the-token-to-an-incorrect-endpoint"></a>Usługa Azure AD wysyła token do niepoprawnego punktu końcowego

**Możliwa przyczyna**

Podczas logowania jednokrotnego, jeśli żądanie logowania nie zawiera jawnego adresu URL odpowiedzi (adres URL usługi konsumenta oświadczeń), usługa Azure AD wybierze dowolny skonfigurowany adres URL polegania dla tej aplikacji. Nawet jeśli aplikacja ma skonfigurowany adres URL jawnej odpowiedzi, użytkownik może zostać przekierowany https://127.0.0.1:444. 

Podczas dodawania tej aplikacji jako aplikacji spoza galerii usługa Azure Active Directory utworzyła ten adres URL odpowiedzi jako wartość domyślną. To działanie zostało zmienione i usługa Azure Active Directory nie dodaje już domyślnie tego adresu URL. 

**Rozdzielczość**

Usuń nieużywane adresy URL odpowiedzi skonfigurowane dla aplikacji.

1.  Otwórz [**witrynę Azure portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny** lub **współadministrator**.

2.  Otwórz **rozszerzenie usługi Azure Active Directory,** wybierając **wszystkie usługi** u góry głównego menu nawigacji po lewej stronie.

3.  Wpisz **"Usługa Azure Active Directory"** w polu wyszukiwania filtru i wybierz element **usługi Azure Active Directory.**

4.  Wybierz **aplikacje dla przedsiębiorstw** z menu nawigacji po lewej stronie usługi Azure Active Directory.

5.  Wybierz **pozycję Wszystkie aplikacje,** aby wyświetlić listę wszystkich aplikacji.

    Jeśli nie widzisz aplikacji, która ma być pokazana w tym miejscu, użyj kontrolki **Filtruj** u góry **listy Wszystkie aplikacje** i ustaw opcję **Pokaż** na **Wszystkie aplikacje**.

6.  Wybierz aplikację, którą chcesz skonfigurować do logowania jednokrotnego.

7.  Po załadowaniu aplikacji otwórz **podstawową konfigurację protokołu SAML**. W **adresie URL odpowiedzi (adres URL usługi konsumenta potwierdzenia)** usuń nieużywane lub domyślne adresy URL odpowiedzi utworzone przez system. Na przykład `https://127.0.0.1:444/applications/default.aspx`.

## <a name="problem-when-customizing-the-saml-claims-sent-to-an-application"></a>Problem podczas dostosowywania oświadczeń SAML wysyłanych do aplikacji

Aby dowiedzieć się, jak dostosować oświadczenia atrybutów SAML wysyłane do aplikacji, zobacz [Mapowanie oświadczeń w usłudze Azure Active Directory](../develop/active-directory-claims-mapping.md).

## <a name="next-steps"></a>Następne kroki

[Jak debugować aplikacje logowania jednokrotnego oparte na saml w usłudze Azure AD](../azuread-dev/howto-v1-debug-saml-sso-issues.md)
