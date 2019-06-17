---
title: Debugowanie opartej na SAML logowania jednokrotnego — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Debugowanie opartej na SAML logowania jednokrotnego do aplikacji w usłudze Azure Active Directory.
services: active-directory
author: rwike77
documentationcenter: na
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/18/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: luleon, hirsin, smalser
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0273a0d35d2b4d69f74b1acd8bc2b1d7174810cb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67111482"
---
# <a name="debug-saml-based-single-sign-on-to-applications-in-azure-active-directory"></a>Debugowanie opartej na SAML logowania jednokrotnego do aplikacji w usłudze Azure Active Directory

Dowiedz się, jak Znajdowanie i eliminowanie [logowanie jednokrotne](../manage-apps/what-is-single-sign-on.md) problemów dotyczących aplikacji w usłudze Azure Active Directory (Azure AD), które obsługują [Assertion Markup języka SAML (Security) w wersji 2.0](https://en.wikipedia.org/wiki/Security_Assertion_Markup_Language). 

## <a name="before-you-begin"></a>Przed rozpoczęciem

Zaleca się zainstalowanie [Moje zabezpieczenia aplikacji logowania rozszerzenia](../user-help/my-apps-portal-end-user-troubleshoot.md#im-having-trouble-installing-the-my-apps-secure-sign-in-extension). To rozszerzenie przeglądarki ułatwia zbieranie żądanie języka SAML i SAML odpowiedzi informacje potrzebne do rozwiązywania problemów związanych z logowania jednokrotnego. W przypadku, gdy nie można zainstalować rozszerzenia, w tym artykule pokazano, jak rozwiązać problemy z usługą i bez zainstalowane rozszerzenie.

Aby pobrać i zainstalować Moje zabezpieczenia aplikacji logowania rozszerzenia, użyj jednej z poniższych linków.

- [Chrome](https://go.microsoft.com/fwlink/?linkid=866367)
- [Microsoft Edge](https://go.microsoft.com/fwlink/?linkid=845176)
- [Firefox](https://go.microsoft.com/fwlink/?linkid=866366)


## <a name="test-saml-based-single-sign-on"></a>Testowanie opartej na SAML logowania jednokrotnego

Aby przetestować opartej na SAML logowania jednokrotnego między usługą Azure AD i aplikacja docelowa:

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) jako administrator globalny lub innych administratorów, która dysponuje autoryzacją do zarządzania aplikacjami.
1. W bloku po lewej stronie wybierz **usługi Azure Active Directory**, a następnie wybierz pozycję **aplikacje dla przedsiębiorstw**. 
1. Z listy aplikacji dla przedsiębiorstw, wybierz aplikację, dla której chcesz przetestować logowanie jednokrotne, a następnie z listy opcji po lewej stronie wybierz **logowanie jednokrotne**.
1. Aby otworzyć opartej na SAML logowania testowania jednokrotne, przejdź do **Test rejestracji jednokrotnej** (krok 5). Jeśli **testu** przycisk jest nieaktywna, musisz wypełnić i Zapisz wymaganych atrybutów najpierw **podstawową konfigurację protokołu SAML** sekcji.
1. W **Test rejestracji jednokrotnej** bloku, użyj firmowych poświadczeń do logowania do aplikacji docelowej. Można zalogować się jako bieżący użytkownik lub innego użytkownika. Jeśli logujesz się jako inny użytkownik monit wyświetli monit do uwierzytelnienia.

    ![Test SAML strony](./media/howto-v1-debug-saml-sso-issues/test-single-sign-on.png)


Jeśli pomyślnie zalogowano w test został przekazany. W tym przypadku usługi Azure AD wystawiony token odpowiedzi SAML do aplikacji. Aplikacja używa tokenu SAML pomyślnie zalogować się.

Jeśli błąd na stronie rejestracji w firmie lub strony aplikacji, użyj jednej z następnych sekcjach, aby naprawić błąd.


## <a name="resolve-a-sign-in-error-on-your-company-sign-in-page"></a>Napraw błąd logowania na stronie logowania firmy

Podczas próby Zaloguj się na Twojej firmy strony logowania podobną do poniższego przykładu mogą pojawiać się błąd.

![Błąd logowania](./media/howto-v1-debug-saml-sso-issues/error.png)

Aby debugować ten błąd, konieczne jest komunikat o błędzie i żądanie języka SAML. Zabezpieczanie aplikacji My logowania rozszerzenie automatycznie zbiera te informacje i zawiera wskazówki dotyczące rozwiązania w usłudze Azure AD. 

### <a name="to-resolve-the-sign-in-error-with-the-my-apps-secure-sign-in-extension-installed"></a>Aby rozwiązać błąd logowania za pomocą Secure Moje aplikacje logowania rozszerzenia zainstalowane

1. Gdy wystąpi błąd, rozszerzenie przekieruje Cię do usługi Azure AD **Test rejestracji jednokrotnej** bloku. 
1. Na **Test rejestracji jednokrotnej** bloku wybierz **pobrać żądanie języka SAML**. 
1. Powinien zostać wyświetlony określonym rozwiązania wskazówki opierają się na błąd i wartościami w żądaniu języka SAML.
1. Zostanie wyświetlony **go naprawić** przycisku do automatycznego aktualizowania konfiguracji w usłudze Azure AD, aby rozwiązać ten problem. Jeśli nie widzisz tego przycisku, następnie problem logowania nie jest z powodu błędnej konfiguracji w usłudze Azure AD.

Jeśli rozwiązanie jest dostępne w błędu logowania, zaleca się, użyj pola tekstowego opinii na firmę Microsoft.

### <a name="to-resolve-the-error-without-installing-the-my-apps-secure-sign-in-extension"></a>Aby rozwiązać problem bez konieczności instalowania Moje zabezpieczenia aplikacji logowania rozszerzenia

1. Kopiuj komunikat o błędzie w prawym dolnym rogu strony. Zawiera komunikat o błędzie:
    - Identyfikator korelacji i sygnaturę czasową. Te wartości są ważne podczas tworzenia zgłoszenia do pomocy technicznej firmy Microsoft, ponieważ one pomóc inżynierom, aby zidentyfikować problem i uzyskać dokładne rozwiązania problemu.
    - Oświadczenie identyfikuje główną przyczynę problemu.
1. Wróć do usługi Azure AD i Znajdź **Test rejestracji jednokrotnej** bloku.
1. W polu tekstowym powyżej **Uzyskaj wskazówki dotyczące rozwiązywania**, Wklej komunikat o błędzie.
1. Kliknij przycisk **zapoznaj się ze wskazówkami rozpoznawania** do wyświetlenia kroki dotyczące rozwiązania tego problemu. Wytyczne mogą wymagać informacji z żądania SAML lub odpowiedzi SAML. Jeśli nie używasz Moje zabezpieczenia aplikacji logowania rozszerzenia, to narzędzie może być konieczne takich jak [Fiddler](https://www.telerik.com/fiddler) można pobrać żądanie języka SAML i odpowiedzi.
1. Sprawdź, czy miejsce docelowe w żądaniu języka SAML odpowiada SAML pojedynczego logowania jednokrotnego adresu URL usługi uzyskany z usługi Azure AD.
1. Sprawdź, czy wystawcy żądania języka SAML jest taki sam identyfikator, które zostały skonfigurowane dla aplikacji w usłudze Azure AD. Usługa Azure AD używa wystawca do znajdowania aplikacji w Twoim katalogu.
1. Sprawdź, czy AssertionConsumerServiceURL jest, gdy aplikacja oczekuje otrzymać SAML token z usługi Azure AD. Wartość tę można skonfigurować w usłudze Azure AD, ale nie jest to konieczne, jeśli jest on częścią żądania języka SAML.


## <a name="resolve-a-sign-in-error-on-the-application-page"></a>Napraw błąd logowania na stronie aplikacji

Może zalogować się pomyślnie, a następnie zobacz błąd, na stronie aplikacji. Dzieje się tak, gdy usługi Azure AD wystawiony token do aplikacji, ale aplikacja nie akceptuje odpowiedzi.   

Aby naprawić błąd, wykonaj następujące kroki:

1. Jeśli aplikacja znajduje się w galerii usługi Azure AD, sprawdź, czy wykonano wszystkie kroki integracji aplikacji z usługą Azure AD. Aby uzyskać instrukcje dotyczące integracji aplikacji, zobacz [listę samouczki integracji aplikacji SaaS](../saas-apps/tutorial-list.md).
1. Pobrać odpowiedzi SAML.
    - Jeśli zainstalowano rozszerzenie Secure Moje aplikacje logowania, z **Test rejestracji jednokrotnej** bloku kliknij **pobrać odpowiedzi SAML**.
    - Jeśli rozszerzenie nie jest zainstalowany, użyj narzędzia takiego jak [Fiddler](https://www.telerik.com/fiddler) można pobrać odpowiedzi SAML. 
1. Zwróć uwagę, te elementy w tokenie języka SAML odpowiedzi:
   - Unikatowy identyfikator użytkownika, wartość NameID i format
   - Oświadczeń wystawionych w tokenie
   - Certyfikat używany do podpisywania tokenu. 

     Aby uzyskać więcej informacji na temat odpowiedzi SAML, zobacz [protokołu SAML rejestracji jednokrotnej](single-sign-on-saml-protocol.md).

1. Po przejrzeniu odpowiedzi SAML, zapoznaj się [błąd na stronie aplikacji po zalogowaniu się](../manage-apps/application-sign-in-problem-application-error.md) wskazówki na temat sposobu rozwiązania problemu. 
1. Jeśli nadal nie możesz zalogować się pomyślnie, możesz poprosić z dostawcą aplikacji co to jest brak odpowiedzi SAML.


## <a name="next-steps"></a>Kolejne kroki

Teraz, logowania jednokrotnego działa do aplikacji, można wykonać następujące akcje [Automatyzowanie aprowizacji użytkowników i anulowania obsługi do aplikacji SaaS](../manage-apps/user-provisioning.md) lub [Rozpoczynanie pracy przy użyciu dostępu warunkowego](../conditional-access/app-based-conditional-access.md).
