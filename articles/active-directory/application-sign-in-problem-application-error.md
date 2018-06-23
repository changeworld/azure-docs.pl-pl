---
title: Błąd na stronie aplikacji po zalogowaniu się | Dokumentacja firmy Microsoft
description: Jak rozwiązać problemy z logowaniem usługi Azure AD po zgłoszeniu emituje błąd
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: ce37ab8ac3324226d0d50491b334fe837b77c49d
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "36330309"
---
# <a name="error-on-an-applications-page-after-signing-in"></a>Błąd na stronie aplikacji po zalogowaniu

W tym scenariuszu usługi Azure AD podpisała użytkownika w, ale aplikacja jest wyświetlany błąd nie zezwala na użytkownika do pomyślnego zakończenia przepływu logowania. W tym scenariuszu aplikacja nie akceptuje problem odpowiedzi przez usługę Azure AD.

Istnieje kilka możliwych przyczyn Dlaczego ta aplikacja nie akceptuje odpowiedzi z usługi Azure AD. Jeśli ten błąd w aplikacji nie jest jasne, aby dowiedzieć się, czego brakuje w odpowiedzi, następnie:

-   Jeśli aplikacja jest w galerii Azure AD, sprawdź zostały wykonane wszystkie kroki opisane w artykule [debugowanie na języku SAML logowanie jednokrotne do aplikacji w usłudze Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-saml-debugging).

-   Użyj narzędzia, takiego jak [Fiddler](http://www.telerik.com/fiddler) przechwytywania SAML żądania, odpowiedzi SAML i tokenu SAML.

-   Udostępnianie odpowiedzi SAML z dostawcą aplikacji, aby dowiedzieć się, czego brakuje.

## <a name="missing-attributes-in-the-saml-response"></a>Brak atrybutów w odpowiedzi SAML

Aby dodać atrybut w konfiguracji usługi Azure AD do wysłania w odpowiedzi usługi Azure AD, wykonaj następujące kroki:

1.  Otwórz [ **portalu Azure** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego** lub **ko-administratora.**

2.  Otwórz **rozszerzenia usług Azure Active Directory** klikając **wszystkie usługi** w górnej części menu nawigacji po lewej stronie głównej.

3.  Wpisz w **"Azure Active Directory**" w polu wyszukiwania filtr a wybierz **usługi Azure Active Directory** elementu.

4.  Kliknij przycisk **aplikacje dla przedsiębiorstw** z menu nawigacji po lewej stronie usługi Azure Active Directory.

5.  Kliknij przycisk **wszystkie aplikacje** Aby wyświetlić listę wszystkich aplikacji.

   * Jeśli nie ma aplikacji ma tutaj będą wyświetlane, użyj **filtru** kontroli nad **listę wszystkich aplikacji** i ustaw **Pokaż** opcji w celu **wszystkich Aplikacje.**

6.  Wybierz aplikację, aby skonfigurować logowanie jednokrotne.

7.  Po załadowaniu aplikacji, kliknij przycisk **logowanie jednokrotne** z menu nawigacji po lewej stronie aplikacji.

8.  Kliknij przycisk **wyświetlanie i edytowanie użytkownika wszystkie inne atrybuty w obszarze** **atrybuty użytkownika** sekcji atrybuty przesyłany do aplikacji w tokenie SAML podczas logowania.

   Aby dodać atrybutu:

   * Kliknij przycisk **Dodaj atrybut**. Wprowadź **nazwa** i wybierz **wartość** z listy rozwijanej.

   * Kliknij przycisk **zapisać.** Zostanie wyświetlony nowy atrybut w tabeli.

9.  Zapisz konfigurację.

Następnym razem, gdy użytkownik loguje się do aplikacji usługi Azure AD wysyłanie nowy atrybut w odpowiedzi SAML.

## <a name="the-application-expects-a-different-user-identifier-value-or-format"></a>Aplikacja oczekuje na format lub inną wartość identyfikatora użytkownika

Logowanie do aplikacji kończy się niepowodzeniem, ponieważ brak odpowiedzi SAML atrybutów, takich jak role lub aplikacja oczekuje innego formatu dla atrybutu EntityID.

## <a name="add-an-attribute-in-the-azure-ad-application-configuration"></a>Dodaj atrybut w konfiguracji aplikacji usługi Azure AD:

Aby zmienić wartość identyfikatora użytkownika, wykonaj następujące kroki:

1.  Otwórz [ **portalu Azure** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego** lub **ko-administratora.**

2.  Otwórz **rozszerzenia usług Azure Active Directory** klikając **wszystkie usługi** w górnej części menu nawigacji po lewej stronie głównej.

3.  Wpisz w **"Azure Active Directory**" w polu wyszukiwania filtr a wybierz **usługi Azure Active Directory** elementu.

4.  Kliknij przycisk **aplikacje dla przedsiębiorstw** z menu nawigacji po lewej stronie usługi Azure Active Directory.

5.  Kliknij przycisk **wszystkie aplikacje** Aby wyświetlić listę wszystkich aplikacji.

   * Jeśli nie ma aplikacji ma tutaj będą wyświetlane, użyj **filtru** kontroli nad **listę wszystkich aplikacji** i ustaw **Pokaż** opcji w celu **wszystkich Aplikacje.**

6.  Wybierz aplikację, aby skonfigurować logowanie jednokrotne.

7.  Po załadowaniu aplikacji, kliknij przycisk **logowanie jednokrotne** z menu nawigacji po lewej stronie aplikacji.

8.  W obszarze **atrybuty użytkownika**, wybierz Unikatowy identyfikator dla użytkowników w **identyfikator użytkownika** listy rozwijanej.

## <a name="change-entityid-user-identifier-format"></a>Zmiana formatu EntityID (identyfikator użytkownika)

Jeśli aplikacja oczekuje innego formatu dla atrybutu EntityID. Następnie nie będzie mógł wybrać format EntityID (identyfikator użytkownika), który wysyła usługi Azure AD do aplikacji w odpowiedzi po uwierzytelnieniu użytkownika.

Wybrano Azure wybierz AD format dla atrybutu NameID (identyfikator użytkownika) na podstawie wartości lub format żądany przez aplikację w SAML AuthRequest. Aby uzyskać więcej informacji można znaleźć w artykule [protokołu SAML rejestracji jednokrotnej](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) w sekcji NameIDPolicy.

## <a name="the-application-expects-a-different-signature-method-for-the-saml-response"></a>Aplikacja oczekuje metody inny podpis dla odpowiedzi SAML

Aby zmienić, które części tokenu SAML są podpisane cyfrowo przez usługę Azure Active Directory. Wykonaj następujące kroki:

1.  Otwórz [ **portalu Azure** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego** lub **ko-administratora.**

2.  Otwórz **rozszerzenia usług Azure Active Directory** klikając **wszystkie usługi** w górnej części menu nawigacji po lewej stronie głównej.

3.  Wpisz w **"Azure Active Directory**" w polu wyszukiwania filtr a wybierz **usługi Azure Active Directory** elementu.

4.  Kliknij przycisk **aplikacje dla przedsiębiorstw** z menu nawigacji po lewej stronie usługi Azure Active Directory.

5.  Kliknij przycisk **wszystkie aplikacje** Aby wyświetlić listę wszystkich aplikacji.

  * Jeśli nie ma aplikacji ma tutaj będą wyświetlane, użyj **filtru** kontroli nad **listę wszystkich aplikacji** i ustaw **Pokaż** opcji w celu **wszystkich Aplikacje.**

6.  Wybierz aplikację, aby skonfigurować logowanie jednokrotne.

7.  Po załadowaniu aplikacji, kliknij przycisk **logowanie jednokrotne** z menu nawigacji po lewej stronie aplikacji.

8.  Kliknij przycisk **Pokaż zaawansowane ustawienia podpisywania certyfikatu** w obszarze **certyfikat podpisywania SAML** sekcji.

9.  Wybierz odpowiednie **opcja podpisywania** oczekiwany przez aplikację:

  * Podpisz odpowiedź SAML

  * Podpisz odpowiedź i potwierdzenie SAML

  * Podpisz potwierdzenie SAML

Następnym razem, gdy użytkownik loguje się do aplikacji usługi Azure AD podpisać części odpowiedzi SAML wybrane.

## <a name="the-application-expects-the-signing-algorithm-to-be-sha-1"></a>Aplikacja oczekuje podpisywania algorytmu SHA-1

Domyślnie usługi Azure AD zaloguje się przy użyciu algorytmu większości zabezpieczeń tokenu SAML. Zmiana logowania algorytmu SHA-1 nie jest zalecane, chyba że wymagane przez aplikację.

Aby zmienić algorytm podpisywania, wykonaj następujące kroki:

1.  Otwórz [ **portalu Azure** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego** lub **ko-administratora.**

2.  Otwórz **rozszerzenia usług Azure Active Directory** klikając **wszystkie usługi** w górnej części menu nawigacji po lewej stronie głównej.

3.  Wpisz w **"Azure Active Directory**" w polu wyszukiwania filtr a wybierz **usługi Azure Active Directory** elementu.

4.  Kliknij przycisk **aplikacje dla przedsiębiorstw** z menu nawigacji po lewej stronie usługi Azure Active Directory.

5.  Kliknij przycisk **wszystkie aplikacje** Aby wyświetlić listę wszystkich aplikacji.

   * Jeśli nie ma aplikacji ma tutaj będą wyświetlane, użyj **filtru** kontroli nad **listę wszystkich aplikacji** i ustaw **Pokaż** opcji w celu **wszystkich Aplikacje.**

6.  Wybierz aplikację, aby skonfigurować logowanie jednokrotne.

7.  Po załadowaniu aplikacji, kliknij przycisk **logowanie jednokrotne** z menu nawigacji po lewej stronie aplikacji.

8.  Kliknij przycisk **Pokaż zaawansowane ustawienia podpisywania certyfikatu** w obszarze **certyfikat podpisywania SAML** sekcji.

9.  Wybierz algorytm SHA-1, **algorytm podpisywania**.

Następnym razem, gdy użytkownik loguje się do aplikacji usługi Azure AD Zaloguj się przy użyciu algorytmu SHA-1 tokenu SAML.

## <a name="next-steps"></a>Kolejne kroki
[Debugowanie na języku SAML logowanie jednokrotne do aplikacji w usłudze Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-saml-debugging)
