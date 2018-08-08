---
title: Debugowanie opartej na SAML logowania jednokrotnego — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Debugowanie opartej na SAML logowania jednokrotnego do aplikacji w usłudze Azure Active Directory.
services: active-directory
author: CelesteDG
documentationcenter: na
manager: mtillman
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/15/2018
ms.author: celested
ms.custom: aaddev
ms.reviewer: hirsin, dastrock, smalser
ms.openlocfilehash: 5895e918fbd3b67074069ccc81bd794a75725c9a
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2018
ms.locfileid: "39620940"
---
# <a name="debug-saml-based-single-sign-on-to-applications-in-azure-active-directory"></a>Debugowanie opartej na SAML logowania jednokrotnego do aplikacji w usłudze Azure Active Directory

Dowiedz się, jak Znajdowanie i eliminowanie [logowanie jednokrotne](../manage-apps/what-is-single-sign-on.md) problemów dotyczących aplikacji w usłudze Azure Active Directory (Azure AD), które obsługują [Assertion Markup języka SAML (Security) w wersji 2.0](https://en.wikipedia.org/wiki/Security_Assertion_Markup_Language). 

## <a name="before-you-begin"></a>Przed rozpoczęciem
Zaleca się zainstalowanie [Moje zabezpieczenia aplikacji logowania rozszerzenia](../user-help/active-directory-saas-access-panel-user-help.md#i-am-having-trouble-installing-the-my-apps-secure-sign-in-extension). To rozszerzenie przeglądarki ułatwia zbieranie żądanie języka SAML i SAML odpowiedzi informacje potrzebne do rozwiązywania problemów związanych z logowania jednokrotnego. W przypadku, gdy nie można zainstalować rozszerzenia, w tym artykule pokazano, jak rozwiązać problemy z usługą i bez zainstalowane rozszerzenie.

Aby pobrać i zainstalować Moje zabezpieczenia aplikacji logowania rozszerzenia, użyj jednej z poniższych linków.

- [Chrome](https://go.microsoft.com/fwlink/?linkid=866367)
- [Krawędź](https://go.microsoft.com/fwlink/?linkid=845176)
- [Firefox](https://go.microsoft.com/fwlink/?linkid=866366)


## <a name="test-saml-based-single-sign-on"></a>Testowanie opartej na SAML logowania jednokrotnego

Aby przetestować opartej na SAML logowania jednokrotnego między usługą AAD i aplikacja docelowa:

1.  Zaloguj się do [witryny Azure portal](https://portal.azure.com) jako administrator globalny lub innych administratorów, która dysponuje autoryzacją do zarządzania aplikacjami.
2.  W lewym bloku kliknij **usługi Azure Active Directory**, a następnie kliknij przycisk **aplikacje dla przedsiębiorstw**. 
3.  Z listy aplikacji dla przedsiębiorstw, kliknij żądaną aplikację, dla której chcesz przetestować logowanie jednokrotne, a następnie z listy opcji po lewej stronie kliknij pozycję **logowanie jednokrotne**.
4.  Aby otworzyć opartej na SAML logowania testowania jednokrotne, w **domena i adresy URL** kliknij sekcję **ustawienie SAML testu**. Przycisk Ustawienia języka SAML testu są wyszarzone, musisz najpierw Wypełnij i Zapisz wymaganych atrybutów.
5.  W **Test rejestracji jednokrotnej** bloku, użyj firmowych poświadczeń do logowania do aplikacji docelowej. Można zalogować się jako bieżący użytkownik lub innego użytkownika. Jeśli logujesz się jako inny użytkownik monit wyświetli monit do uwierzytelnienia.

    ![Test SAML strony](./media/howto-v1-debug-saml-sso-issues/testing.png)


Jeśli pomyślnie zalogowano w test został przekazany. W tym przypadku usługi Azure AD wystawiony token odpowiedzi SAML do aplikacji. Aplikacja używa tokenu SAML pomyślnie zalogować się.

Jeśli błąd na stronie rejestracji w firmie lub strony aplikacji, użyj jednej z następnych sekcjach, aby naprawić błąd.


## <a name="resolve-a-sign-in-error-on-your-company-sign-in-page"></a>Napraw błąd logowania na stronie logowania firmy

Podczas próby Zaloguj się na stronę logowania Twojej firmy mogą pojawiać się błąd. 

![Błąd logowania](./media/howto-v1-debug-saml-sso-issues/error.png)

Aby debugować ten błąd, konieczne jest komunikat o błędzie i żądanie języka SAML. Zabezpieczanie aplikacji My logowania rozszerzenie automatycznie zbiera te informacje i zawiera wskazówki dotyczące rozwiązania w usłudze Azure AD. 

Aby naprawić błąd logowania za pomocą MyApps Secure zainstalowane rozszerzenia logowania:

1.  Gdy wystąpi błąd, rozszerzenie przekieruje Cię do usługi Azure Ad **Test rejestracji jednokrotnej** bloku. 
2.  Na **Test rejestracji jednokrotnej** bloku kliknij **pobrać żądanie języka SAML**. 
3.  Powinien zostać wyświetlony określonym rozwiązania wskazówki opierają się na błąd i wartościami w żądaniu języka SAML. Zapoznaj się ze wskazówkami.

Aby rozwiązać problem bez konieczności instalowania MyApps bezpiecznego logowania rozszerzenia:

1. Kopiuj komunikat o błędzie w prawym dolnym rogu strony. Zawiera komunikat o błędzie:
    - Identyfikator korelacji i sygnaturę czasową. Te wartości są ważne podczas tworzenia zgłoszenia do pomocy technicznej firmy Microsoft, ponieważ one pomóc inżynierom, aby zidentyfikować problem i uzyskać dokładne rozwiązania problemu.
    - Oświadczenie identyfikuje główną przyczynę problemu.
2.  Wróć do usługi Azure AD i Znajdź **Test rejestracji jednokrotnej** bloku.
3.  W polu tekstowym powyżej **Uzyskaj wskazówki dotyczące rozwiązywania**, Wklej komunikat o błędzie.
3.  Kliknij przycisk **zapoznaj się ze wskazówkami rozpoznawania** do wyświetlenia kroki dotyczące rozwiązania tego problemu. Wytyczne mogą wymagać informacji z żądania SAML lub odpowiedzi SAML. Jeśli nie używasz MyApps bezpiecznego logowania rozszerzenia, możesz potrzebować narzędzia takie jak [Fiddler](http://www.telerik.com/fiddler) można pobrać żądanie języka SAML i odpowiedzi.
4.  Sprawdź, czy miejsce docelowe w żądaniu języka SAML odpowiada SAML pojedynczego logowania jednokrotnego adresu URL usługi uzyskany z usługi Azure Active Directory
5.  Sprawdź, czy wystawcy żądania języka SAML jest taki sam identyfikator, które zostały skonfigurowane dla aplikacji w usłudze Azure Active Directory. Usługa Azure AD używa wystawca do znajdowania aplikacji w Twoim katalogu.
6.  Sprawdź, czy AssertionConsumerServiceURL jest, gdy aplikacja oczekuje otrzymać SAML token z usługi Azure Active Directory. Wartość tę można skonfigurować w usłudze Azure Active Directory, ale nie jest to konieczne, jeśli jest on częścią żądania języka SAML.


## <a name="resolve-a-sign-in-error-on-the-application-page"></a>Napraw błąd logowania na stronie aplikacji

Może zalogować się pomyślnie, a następnie zobacz błąd, na stronie aplikacji. Dzieje się tak, gdy usługi Azure AD wystawiony token do aplikacji, ale aplikacja nie akceptuje odpowiedzi.   

Aby naprawić błąd:

1. Jeśli aplikacja znajduje się w galerii usługi Azure AD, sprawdź, czy zostały wykonane wszystkie kroki integracji aplikacji z usługą Azure AD. Aby uzyskać instrukcje dotyczące integracji aplikacji, zobacz [listę samouczki integracji aplikacji SaaS](../saas-apps/tutorial-list.md).
2. Pobrać odpowiedzi SAML.
    - Jeśli zainstalowano rozszerzenie Secure Moje aplikacje logowania, z **Test rejestracji jednokrotnej** bloku kliknij **pobrać odpowiedzi SAML**.
    - Jeśli rozszerzenie nie jest zainstalowany, użyj narzędzia takiego jak [Fiddler](http://www.telerik.com/fiddler) można pobrać odpowiedzi SAML. 
3. Zwróć uwagę, te elementy w tokenie języka SAML odpowiedzi:
    - Unikatowy identyfikator użytkownika, wartość NameID i format
    - Oświadczeń wystawionych w tokenie
    - Certyfikat używany do podpisywania tokenu. Aby uzyskać informacji na temat sposobu przeglądania odpowiedzi SAML, zobacz [protokołu SAML rejestracji jednokrotnej](single-sign-on-saml-protocol.md).
4. Aby uzyskać więcej informacji na temat odpowiedzi SAML, zobacz [protokołu SAML rejestracji jednokrotnej](single-sign-on-saml-protocol.md).
5. Po przejrzeniu odpowiedzi SAML, zapoznaj się [błąd na stronie aplikacji po zalogowaniu się](../application-sign-in-problem-application-error.md) wskazówki dotyczące rozwiązywania problemu. 
6. Jeśli nadal nie możesz zalogować się pomyślnie, możesz poprosić z dostawcą aplikacji co to jest brak odpowiedzi SAML.


## <a name="next-steps"></a>Kolejne kroki
Teraz, logowania jednokrotnego działa do aplikacji, można wykonać następujące akcje [Automatyzowanie aprowizacji użytkowników i anulowania obsługi do aplikacji SaaS](../active-directory-saas-app-provisioning.md), lub [Rozpoczynanie pracy przy użyciu dostępu warunkowego](../conditional-access/app-based-conditional-access.md).


