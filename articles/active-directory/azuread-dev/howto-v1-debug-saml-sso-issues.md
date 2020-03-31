---
title: Logowanie jednookrotne oparte na debugowaniu SAML — usługa Azure Active Directory | Dokumenty firmy Microsoft
description: Debugowanie aplikacji logowania jednokrotnego opartego na debugowaniu SAML w usłudze Azure Active Directory.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.topic: conceptual
ms.workload: identity
ms.date: 02/18/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: luleon, hirsin, paulgarn
ROBOTS: NOINDEX
ms.openlocfilehash: 5d92b43b47a20a75d2c8b6becb69cfee5829e80f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154852"
---
# <a name="debug-saml-based-single-sign-on-to-applications-in-azure-active-directory"></a>Debugowanie aplikacji logowania jednokrotnego opartego na technologii SAML w usłudze Azure Active Directory

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Dowiedz się, jak znaleźć i rozwiązać problemy z [logowaniem pojedynczym](../manage-apps/what-is-single-sign-on.md) dla aplikacji w usłudze Azure Active Directory (Azure AD), które obsługują [język znaczników oświadczeń zabezpieczeń (SAML) 2.0](https://en.wikipedia.org/wiki/Security_Assertion_Markup_Language). 

## <a name="before-you-begin"></a>Przed rozpoczęciem

Zalecamy zainstalowanie [rozszerzenia logowania my apps secure sign-in](../user-help/my-apps-portal-end-user-troubleshoot.md#im-having-trouble-installing-the-my-apps-secure-sign-in-extension). To rozszerzenie przeglądarki ułatwia zbieranie informacji o żądaniu SAML i odpowiedzi SAML, które są potrzebne do rozwiązywania problemów z logowaniem jednokrotnym. W przypadku, gdy nie można zainstalować rozszerzenia, w tym artykule pokazano, jak rozwiązać problemy zarówno z zainstalowanym rozszerzeniem, jak i bez niego.

Aby pobrać i zainstalować rozszerzenie logowania moje aplikacje bezpieczne, użyj jednego z poniższych łączy.

- [Chrome](https://go.microsoft.com/fwlink/?linkid=866367)
- [Microsoft Edge](https://go.microsoft.com/fwlink/?linkid=845176)
- [Firefox](https://go.microsoft.com/fwlink/?linkid=866366)

## <a name="test-saml-based-single-sign-on"></a>Testowanie logowania jednokrotnego opartego na SAML

Aby przetestować logowanie jednoocełowe oparte na saml między usługą Azure AD a aplikacją docelową:

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) jako administrator globalny lub inny administrator, który jest upoważniony do zarządzania aplikacjami.
1. W lewym bloku wybierz pozycję **Azure Active Directory**, a następnie wybierz pozycję **Aplikacje przedsiębiorstwa**. 
1. Z listy aplikacji dla przedsiębiorstw wybierz aplikację, dla której chcesz przetestować logowanie jednokrotne, a następnie z opcji po lewej stronie wybierz opcję **Logowanie jednokrotne**.
1. Aby otworzyć środowisko testowania logowania jednokrotnego oparte na SAML, przejdź do **testowania logowania jednokrotnego** (krok 5). Jeśli przycisk **Test** jest wyszarzony, należy najpierw wypełnić i zapisać wymagane atrybuty w sekcji **Podstawowa konfiguracja SAML.**
1. W **bloku logowania jednokrotnego testu** użyj poświadczeń firmowych, aby zalogować się do aplikacji docelowej. Możesz zalogować się jako bieżący użytkownik lub jako inny użytkownik. Jeśli zalogujesz się jako inny użytkownik, zostanie wyświetlony monit o uwierzytelnienie.

    ![Zrzut ekranu przedstawiający testową stronę SSO SAML](./media/howto-v1-debug-saml-sso-issues/test-single-sign-on.png)

Jeśli użytkownik zostanie pomyślnie zalogowany, test został pomyślnie ukończony. W takim przypadku usługa Azure AD wystawiła token odpowiedzi SAML do aplikacji. Aplikacja użyła tokenu SAML, aby pomyślnie zalogować się.

Jeśli masz błąd na stronie logowania firmy lub na stronie aplikacji, użyj jednej z następnych sekcji, aby rozwiązać ten błąd.

## <a name="resolve-a-sign-in-error-on-your-company-sign-in-page"></a>Rozwiązywanie problemu z błędem logowania na stronie logowania firmy

Podczas próby zalogowania się może pojawić się błąd na stronie logowania firmy, który jest podobny do poniższego przykładu.

![Przykład przedstawiający błąd na stronie logowania firmy](./media/howto-v1-debug-saml-sso-issues/error.png)

Aby debugować ten błąd, potrzebny jest komunikat o błędzie i żądanie SAML. Rozszerzenie bezpieczne logowanie my apps automatycznie zbiera te informacje i wyświetla wskazówki dotyczące rozpoznawania w usłudze Azure AD.

### <a name="to-resolve-the-sign-in-error-with-the-my-apps-secure-sign-in-extension-installed"></a>Aby rozwiązać problem błędu logowania z zainstalowanym rozszerzeniem moje aplikacje bezpieczne logowanie

1. W przypadku wystąpienia błędu rozszerzenie przekierowuje cię z powrotem do bloku **logowania jednokrotnego testu** usługi Azure AD.
1. Na **bloku Test logowania jednokrotnego** wybierz opcję **Pobierz żądanie SAML**.
1. Powinny być widoczne wskazówki dotyczące rozwiązania na podstawie błędu i wartości w żądaniu SAML.
1. Zostanie wyświetlony przycisk **Napraw to,** aby automatycznie zaktualizować konfigurację w usłudze Azure AD, aby rozwiązać problem. Jeśli ten przycisk nie jest widoczny, problem z logowaniem nie jest spowodowany błędną konfiguracją usługi Azure AD.

Jeśli nie zostanie podana żadna rozdzielczość błędu logowania, zalecamy użycie pola tekstowego opinii, aby nas poinformować.

### <a name="to-resolve-the-error-without-installing-the-my-apps-secure-sign-in-extension"></a>Aby rozwiązać ten problem bez instalowania rozszerzenia logowania my apps secure sign-in

1. Skopiuj komunikat o błędzie w prawym dolnym rogu strony. Komunikat o błędzie zawiera:
    - A CorrelationID i sygnatura czasowa. Te wartości są ważne podczas tworzenia przypadku pomocy technicznej z firmą Microsoft, ponieważ pomagają inżynierom zidentyfikować problem i zapewnić dokładne rozwiązanie problemu.
    - Instrukcja identyfikujący główną przyczynę problemu.
1. Wróć do usługi Azure AD i znajdź test logowania **jednokrotnego** bloku.
1. W polu tekstowym powyżej **Uzyskaj wskazówki dotyczące rozdzielczości,** wklej komunikat o błędzie.
1. Kliknij **pozycję Uzyskaj wskazówki dotyczące rozwiązywania problemów,** aby wyświetlić kroki rozwiązywania problemu. Wskazówki mogą wymagać informacji z żądania SAML lub odpowiedzi SAML. Jeśli nie używasz rozszerzenia logowania my apps secure sign-in, może być potrzebne narzędzie, takie jak [Fiddler,](https://www.telerik.com/fiddler) aby pobrać żądanie SAML i odpowiedź.
1. Sprawdź, czy miejsce docelowe w żądaniu SAML odpowiada adresowi URL usługi logowania jednokrotnego SAML uzyskanym z usługi Azure AD.
1. Sprawdź, czy wystawca w żądaniu SAML jest tym samym identyfikatorem skonfigurowanym dla aplikacji w usłudze Azure AD. Usługa Azure AD używa wystawcy do znalezienia aplikacji w katalogu.
1. Sprawdź AssertionConsumerServiceURL jest, gdzie aplikacja oczekuje, aby otrzymać token SAML z usługi Azure AD. Tę wartość można skonfigurować w usłudze Azure AD, ale nie jest to obowiązkowe, jeśli jest częścią żądania SAML.


## <a name="resolve-a-sign-in-error-on-the-application-page"></a>Rozwiązywanie problemu z błędem logowania na stronie aplikacji

Możesz zalogować się pomyślnie, a następnie wyświetlić błąd na stronie aplikacji. Dzieje się tak, gdy usługa Azure AD wystawiła token do aplikacji, ale aplikacja nie akceptuje odpowiedzi.

Aby rozwiązać ten problem, wykonaj następujące kroki:

1. Jeśli aplikacja znajduje się w galerii usługi Azure AD, sprawdź, czy należy wykonać wszystkie kroki dotyczące integracji aplikacji z usługą Azure AD. Aby znaleźć instrukcje integracji dla aplikacji, zobacz [listę samouczków integracji aplikacji SaaS](../saas-apps/tutorial-list.md).
1. Pobierz odpowiedź SAML.
    - Jeśli jest zainstalowane rozszerzenie Moje aplikacje bezpieczne logowanie, z bloku **Test logowania jednokrotnego** kliknij przycisk **Pobierz odpowiedź SAML**.
    - Jeśli rozszerzenie nie jest zainstalowane, użyj narzędzia, takiego jak [Fiddler,](https://www.telerik.com/fiddler) aby pobrać odpowiedź SAML.
1. Zwróć uwagę na te elementy w tokenie odpowiedzi SAML:
   - Unikatowy identyfikator użytkownika wartości i formatu NameID
   - Roszczenia wystawione w tokenie
   - Certyfikat używany do podpisywania tokenu.

     Aby uzyskać więcej informacji na temat odpowiedzi SAML, zobacz [protokół SAML logowania jednokrotnego](../develop/single-sign-on-saml-protocol.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

1. Teraz, po przejrzeniu odpowiedzi SAML, zobacz [Błąd na stronie aplikacji po zalogowaniu się,](../manage-apps/application-sign-in-problem-application-error.md) aby uzyskać wskazówki dotyczące rozwiązywania problemu. 
1. Jeśli nadal nie możesz zalogować się pomyślnie, możesz zapytać dostawcę aplikacji, czego brakuje w odpowiedzi SAML.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy logowanie jednokrotne działa z aplikacją, można [zautomatyzować inicjowanie obsługi administracyjnej i usuwanie obsługi administracyjnej aplikacji SaaS](../manage-apps/user-provisioning.md) lub [rozpocząć pracę z dostępem warunkowym](../conditional-access/app-based-conditional-access.md).
