---
title: Debugowanie na języku SAML logowania jednokrotnego — Azure Active Directory | Dokumentacja firmy Microsoft
description: Debugowania na języku SAML logowania jednokrotnego do aplikacji w usłudze Azure Active Directory.
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
ms.openlocfilehash: d0c006b21e00693fe6c8b35237d4ce55f67c0f75
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/22/2018
ms.locfileid: "36320597"
---
# <a name="debug-saml-based-single-sign-on-to-applications-in-azure-active-directory"></a>Debugowania na języku SAML logowania jednokrotnego do aplikacji w usłudze Azure Active Directory

Dowiedz się, jak znaleźć i rozwiązać [logowanie jednokrotne](../manage-apps/what-is-single-sign-on.md) problemy dotyczące aplikacji w usłudze Azure Active Directory (Azure AD), które obsługują [Security Assertion Markup Language (SAML) 2.0](https://en.wikipedia.org/wiki/Security_Assertion_Markup_Language). 

## <a name="before-you-begin"></a>Przed rozpoczęciem
Zaleca się zainstalowanie [Moje aplikacje bezpiecznego logowania rozszerzenia](../active-directory-saas-access-panel-user-help.md#i-am-having-trouble-installing-the-my-apps-secure-sign-in-extension). To rozszerzenie przeglądarki ułatwia zebrać żądanie SAML i informacje o odpowiedzi SAML potrzebnym do rozwiązywania problemów związanych z logowania jednokrotnego. W przypadku, gdy nie można zainstalować rozszerzenia, w tym artykule przedstawiono sposób rozwiązywania problemów, zarówno z i bez rozszerzenia zainstalowane.

Aby pobrać i zainstalować Moje aplikacje bezpiecznego logowania rozszerzenie, użyj jednej z poniższych łączy.

- [Chrome](https://go.microsoft.com/fwlink/?linkid=866367)
- [Krawędzi](https://go.microsoft.com/fwlink/?linkid=845176)
- [Firefox](https://go.microsoft.com/fwlink/?linkid=866366)


## <a name="test-saml-based-single-sign-on"></a>Test na języku SAML logowania jednokrotnego

Aby przetestować na języku SAML logowania jednokrotnego między AAD i aplikacji docelowej:

1.  Zaloguj się do [portalu Azure](https://portal.azure.com) jako administrator globalny lub inny administrator, który jest upoważniony do zarządzania aplikacjami.
2.  W lewym bloku, kliknij **usługi Azure Active Directory**, a następnie kliknij przycisk **aplikacje dla przedsiębiorstw**. 
3.  Z listy aplikacji przedsiębiorstwa, kliknij aplikację, dla której ma zostać przetestowana logowania jednokrotnego, a następnie opcje po lewej stronie kliknij **logowanie jednokrotne**.
4.  Aby otworzyć SAML na podstawie pojedynczego logowania jednokrotnego testowania środowisko, w **domeny i adres URL** kliknij sekcję **ustawienia testu SAML**. Przycisk Test ustawienie SAML jest nieaktywna, musisz najpierw Wypełnij i Zapisz wymaganych atrybutów.
5.  W **Test rejestracji jednokrotnej** bloku, użyj swoich poświadczeń firmowych logować się do aplikacji docelowej. Można zalogować się jako bieżącego użytkownika lub innego użytkownika. Jeśli logujesz się jako inny użytkownik, monit zostanie wyświetlony monit o uwierzytelniania.

Jeśli pomyślnie zalogowano, testu są spełnione. W takim przypadku usługi Azure AD wystawiony token odpowiedzi SAML dla aplikacji. Aplikacja używa tokenu SAML pomyślnie Cię zalogować.

Jeśli masz błąd na stronę logowania firmy lub aplikacji, użyj jednej z kolejnych sekcjach Aby rozwiązać problem.


## <a name="resolve-a-sign-in-error-on-your-company-sign-in-page"></a>Napraw błąd logowania na stronie logowania firmy

Gdy podejmą próbę zalogowania napotkać błąd na stronie logowania firmy. 

![Błąd logowania](media/active-directory-saml-debugging/error.png)

Aby debugować ten błąd, należy komunikat o błędzie i żądania SAML. Moje aplikacje bezpiecznego logowania rozszerzenia automatycznie zbiera te informacje i zawiera wskazówki dotyczące rozwiązania w usłudze Azure AD. 

Aby usunąć błąd logowania z MyApps Secure logowania rozszerzenie zainstalowane:

1.  Po wystąpieniu błędu rozszerzenia przekieruje Cię do usługi Azure Ad **Test rejestracji jednokrotnej** bloku. 
2.  Na **Test rejestracji jednokrotnej** bloku, kliknij przycisk **Pobierz żądanie SAML**. 
3.  Powinny pojawić się określonej rozdzielczości wskazówki na podstawie błąd i wartości w żądaniu SAML. Przejrzyj wskazówki.

Aby usunąć błąd bez instalowania MyApps bezpiecznego logowania rozszerzenia:

1. Skopiuj komunikat o błędzie w prawym dolnym rogu strony. Zawiera komunikat o błędzie:
    - CorrelationID i sygnatura czasowa. Te wartości są ważne podczas tworzenia sprawy pomocy technicznej z firmą Microsoft, ponieważ pomagają engineers, aby zidentyfikować problem i uzyskać dokładne rozwiązania problemu.
    - Oświadczenie głównej przyczyny problemu.
2.  Wróć do usługi Azure AD i Znajdź **Test rejestracji jednokrotnej** bloku.
3.  W powyższym polu tekstowym **uzyskać wskazówki dotyczące rozwiązania**, Wklej komunikat o błędzie.
3.  Kliknij przycisk **uzyskać wskazówki dotyczące rozwiązania** można wyświetlić kroki rozwiązania tego problemu. Wskazówki może wymagać informacji z SAML żądania lub odpowiedzi SAML. Jeśli nie używasz MyApps bezpiecznego logowania rozszerzenia, narzędzie może być konieczne takich jak [Fiddler](http://www.telerik.com/fiddler) można pobrać SAML żądań i odpowiedzi.
4.  Sprawdź, czy docelowego w żądaniu SAML odpowiada SAML pojedynczy znak na adres URL usługi uzyskane z usługi Azure Active Directory
5.  Sprawdź, czy wystawca w żądaniu SAML jest taki sam identyfikator, które zostały skonfigurowane dla aplikacji w usłudze Azure Active Directory. Usługi Azure AD używa wystawca do znajdowania aplikacji w katalogu.
6.  Sprawdź, czy jest AssertionConsumerServiceURL, gdy aplikacja oczekuje odebrać tokenu SAML z usługi Azure Active Directory. Tę wartość można skonfigurować w usłudze Azure Active Directory, ale nie jest to konieczne, jeśli jest on częścią żądania SAML.


## <a name="resolve-a-sign-in-error-on-the-application-page"></a>Napraw błąd logowania na stronie aplikacji

Może zalogować pomyślnym, a następnie zobacz błąd na stronie aplikacji. Dzieje się tak, gdy usługi Azure AD wystawiony token do aplikacji, ale aplikacja nie akceptuje odpowiedzi.   

Aby rozwiązać ten błąd:

1. W przypadku aplikacji w galerii Azure AD, sprawdź, czy zostały wykonane wszystkie kroki integracji aplikacji z usługą Azure AD. Aby uzyskać instrukcje dotyczące integracji aplikacji, zobacz [lista samouczków integracji aplikacji SaaS](../saas-apps/tutorial-list.md).
2. Pobrać odpowiedzi SAML.
    - Po zainstalowaniu rozszerzenia Moje aplikacje bezpiecznego logowania z **Test rejestracji jednokrotnej** bloku, kliknij przycisk **pobrać odpowiedzi SAML**.
    - Jeśli nie zainstalowano rozszerzenia, użyj narzędzia takiego jak [Fiddler](http://www.telerik.com/fiddler) można pobrać odpowiedzi SAML. 
3. Zwróć uwagę tych elementów w tokenie SAML odpowiedzi:
    - Unikatowy identyfikator użytkownika NameID wartości i formatu
    - Oświadczeń wydanych w tokenie
    - Certyfikat używany do podpisywania tokenu. Informacje dotyczące sposobu przeglądania odpowiedzi SAML, sekcji [protokołu SAML rejestracji jednokrotnej](active-directory-single-sign-on-protocol-reference.md).
4. Aby uzyskać więcej informacji na odpowiedzi SAML, zobacz [protokołu SAML rejestracji jednokrotnej](active-directory-single-sign-on-protocol-reference.md).
5. Teraz, że użytkownik przejrzał odpowiedzi SAML, zobacz [błąd na stronie aplikacji po zalogowaniu się](../application-sign-in-problem-application-error.md) wskazówki dotyczące rozwiązywania problemów. 
6. Jeśli nadal nie możesz zalogować się pomyślnie, należy poprosić dostawcę aplikacji brakujących z odpowiedzi SAML.


## <a name="next-steps"></a>Kolejne kroki
Teraz, gdy rejestracji jednokrotnej jest praca do aplikacji, można [zautomatyzować użytkownika alokowania i anulowania alokowania do aplikacji SaaS](../active-directory-saas-app-provisioning.md), lub [wprowadzenie dostępu warunkowego](../active-directory-conditional-access-azure-portal-get-started.md).


