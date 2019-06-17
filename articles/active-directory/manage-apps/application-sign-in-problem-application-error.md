---
title: Błąd na stronie aplikacji po zalogowaniu | Dokumentacja firmy Microsoft
description: Jak rozwiązywać problemy z logowaniem gdy sama aplikacja generuje błąd usługi Azure AD
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
ms.openlocfilehash: adfc96d2d7abf38c00f32a5d53615bb7c99c320e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66742384"
---
# <a name="error-on-an-applications-page-after-signing-in"></a>Błąd na stronie aplikacji po zalogowaniu

W tym scenariuszu użytkownika musi być zarejestrowany w usłudze Azure AD, ale aplikacja wyświetla błąd nie zezwala na użytkownika pomyślnie zakończyć przepływ logowania. W tym scenariuszu aplikacja nie akceptuje problem odpowiedzi przez usługę Azure AD.

Istnieją niektóre możliwe przyczyny, dlaczego ta aplikacja nie akceptuje odpowiedzi z usługi Azure AD. Jeśli ten błąd w aplikacji nie jest jasne, aby dowiedzieć się, czego brakuje w odpowiedzi, następnie:

-   Jeśli aplikacja jest galerii usługi Azure AD, sprawdź, zostały wykonane wszystkie kroki opisane w artykule [sposób debugowania opartej na SAML logowania jednokrotnego do aplikacji w usłudze Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-saml-debugging).

-   Użyj narzędzia, takiego jak [Fiddler](https://www.telerik.com/fiddler) do przechwytywania żądanie języka SAML i SAML odpowiedzi tokenu SAML.

-   Udostępnianie odpowiedzi SAML z dostawcą aplikacji, aby dowiedzieć się, czego brakuje.

## <a name="missing-attributes-in-the-saml-response"></a>Brak atrybutów w odpowiedzi SAML

Aby dodać atrybut w konfiguracji usługi Azure AD w celu ich wysłania w odpowiedzi usługa Azure AD, wykonaj następujące kroki:

1. Otwórz [ **witryny Azure portal** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego** lub **Współadministratora.**

2. Otwórz **rozszerzenia usługi Azure Active Directory** , klikając **wszystkich usług** w górnej części menu główne menu nawigacji po lewej stronie.

3. Wpisz **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz pozycję **usługi Azure Active Directory** elementu.

4. Kliknij przycisk **aplikacje dla przedsiębiorstw** menu nawigacji po lewej stronie usługi Azure Active Directory.

5. Kliknij przycisk **wszystkie aplikacje** Aby wyświetlić listę wszystkich aplikacji.

   * Jeśli nie widzisz aplikacji, chcesz, aby wyświetlić tutaj użyć **filtru** formant w górnej części **listę wszystkich aplikacji** i ustaw **Pokaż** opcję **wszystkie Aplikacje.**

6. Wybierz aplikację, którą chcesz skonfigurować logowanie jednokrotne.

7. Po załadowaniu aplikacji, kliknij przycisk **logowanie jednokrotne** menu nawigacji po lewej stronie aplikacji.

8. Kliknij przycisk **wyświetlanie i edytowanie użytkownika wszystkie inne atrybuty w obszarze** **atrybutów użytkownika** sekcji, aby edytować atrybuty do wysłania do aplikacji w tokenie SAML podczas logowania.

   Aby dodać atrybut:

   * Kliknij przycisk **Dodaj atrybut**. Wprowadź **nazwa** a następnie wybierz **wartość** z listy rozwijanej.

   * Kliknij przycisk **Zapisz.** Nowy atrybut zostanie wyświetlony w tabeli.

9. Zapisz konfigurację.

Następnym razem użytkownik loguje się do aplikacji usługi Azure AD wysyłanie nowy atrybut w odpowiedzi SAML.

## <a name="the-application-doesnt-identify-the-user"></a>Aplikacja nie identyfikacji użytkownika

Logowanie do aplikacji kończy się niepowodzeniem, ponieważ odpowiedź SAML nie ma atrybutów, takich jak role lub aplikacja oczekuje innego formatu lub wartość atrybutu EntityID.

Jeśli używasz [usługi Azure AD automatyczna aprowizacja użytkowników](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning) do tworzenia, obsługa i usuwania użytkowników w aplikacji. Następnie sprawdź, czy użytkownik pomyślnie ustanowiono do aplikacji SaaS. Aby uzyskać więcej informacji, zobacz [żadni użytkownicy są aprowizowane do aplikacji galerii usługi Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-config-problem-no-users-provisioned)

## <a name="add-an-attribute-in-the-azure-ad-application-configuration"></a>Dodaj atrybut w konfiguracji aplikacji usługi Azure AD:

Aby zmienić wartość identyfikatora użytkownika, wykonaj następujące kroki:

1. Otwórz [ **witryny Azure portal** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego** lub **Współadministratora.**

2. Otwórz **rozszerzenia usługi Azure Active Directory** , klikając **wszystkich usług** w górnej części menu główne menu nawigacji po lewej stronie.

3. Wpisz **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz pozycję **usługi Azure Active Directory** elementu.

4. Kliknij przycisk **aplikacje dla przedsiębiorstw** menu nawigacji po lewej stronie usługi Azure Active Directory.

5. Kliknij przycisk **wszystkie aplikacje** Aby wyświetlić listę wszystkich aplikacji.

   * Jeśli nie widzisz aplikacji, chcesz, aby wyświetlić tutaj użyć **filtru** formant w górnej części **listę wszystkich aplikacji** i ustaw **Pokaż** opcję **wszystkie Aplikacje.**

6. Wybierz aplikację, którą chcesz skonfigurować logowanie jednokrotne.

7. Po załadowaniu aplikacji, kliknij przycisk **logowanie jednokrotne** menu nawigacji po lewej stronie aplikacji.

8. W obszarze **atrybutów użytkownika**, wybierz Unikatowy identyfikator dla użytkowników w **identyfikator użytkownika** listy rozwijanej.

## <a name="change-entityid-user-identifier-format"></a>Zmień format EntityID (identyfikator użytkownika)

Jeśli aplikacja oczekuje innego formatu dla atrybutu EntityID. Następnie będzie mógł wybrać format EntityID (identyfikator użytkownika), który usługa Azure AD wysyła do aplikacji w odpowiedzi po uwierzytelnieniu użytkownika.

Wybrana usługa Azure AD wybierz pozycję formatu NameID atrybutu (identyfikator użytkownika) na podstawie wartości lub format żądany przez aplikację w SAML AuthRequest. Więcej informacji na ten temat można znaleźć w artykule [protokołu SAML rejestracji jednokrotnej](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) sekcji NameIDPolicy.

## <a name="the-application-expects-a-different-signature-method-for-the-saml-response"></a>Aplikacja oczekuje inny podpis metody dla odpowiedzi SAML

Aby zmienić, które części tokenu SAML są podpisane cyfrowo przez usługę Azure Active Directory. Wykonaj następujące kroki:

1. Otwórz [ **witryny Azure portal** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego** lub **Współadministratora.**

2. Otwórz **rozszerzenia usługi Azure Active Directory** , klikając **wszystkich usług** w górnej części menu główne menu nawigacji po lewej stronie.

3. Wpisz **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz pozycję **usługi Azure Active Directory** elementu.

4. Kliknij przycisk **aplikacje dla przedsiębiorstw** menu nawigacji po lewej stronie usługi Azure Active Directory.

5. Kliknij przycisk **wszystkie aplikacje** Aby wyświetlić listę wszystkich aplikacji.

   * Jeśli nie widzisz aplikacji, chcesz, aby wyświetlić tutaj użyć **filtru** formant w górnej części **listę wszystkich aplikacji** i ustaw **Pokaż** opcję **wszystkie Aplikacje.**

6. Wybierz aplikację, którą chcesz skonfigurować logowanie jednokrotne.

7. Po załadowaniu aplikacji, kliknij przycisk **logowanie jednokrotne** menu nawigacji po lewej stronie aplikacji.

8. Kliknij przycisk **Pokaż zaawansowane ustawienia podpisywania certyfikatów** w obszarze **certyfikat podpisywania SAML** sekcji.

9. Wybierz odpowiedni **opcja podpisywania** oczekiwane przez aplikację:

   * Podpisz odpowiedź SAML

   * Zaloguj się odpowiedź i potwierdzenie SAML

   * Podpisz potwierdzenie SAML

Następnym razem użytkownik loguje się do aplikacji, usługi Azure AD Utwórz część odpowiedzi SAML wybrane.

## <a name="the-application-expects-the-signing-algorithm-to-be-sha-1"></a>Aplikacja oczekuje algorytm podpisywania to SHA-1

Domyślnie usługa Azure AD podpisuje token SAML przy użyciu algorytmu większość zabezpieczeń. Zmiana algorytmu logowania na SHA-1 nie jest zalecane, chyba że wymagane przez aplikację.

Aby zmienić algorytm podpisywania, wykonaj następujące kroki:

1. Otwórz [ **witryny Azure portal** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego** lub **Współadministratora.**

2. Otwórz **rozszerzenia usługi Azure Active Directory** , klikając **wszystkich usług** w górnej części menu główne menu nawigacji po lewej stronie.

3. Wpisz **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz pozycję **usługi Azure Active Directory** elementu.

4. Kliknij przycisk **aplikacje dla przedsiębiorstw** menu nawigacji po lewej stronie usługi Azure Active Directory.

5. Kliknij przycisk **wszystkie aplikacje** Aby wyświetlić listę wszystkich aplikacji.

   * Jeśli nie widzisz aplikacji, chcesz, aby wyświetlić tutaj użyć **filtru** formant w górnej części **listę wszystkich aplikacji** i ustaw **Pokaż** opcję **wszystkie Aplikacje.**

6. Wybierz aplikację, którą chcesz skonfigurować logowanie jednokrotne.

7. Po załadowaniu aplikacji, kliknij przycisk **logowanie jednokrotne** menu nawigacji po lewej stronie aplikacji.

8. Kliknij przycisk **Pokaż zaawansowane ustawienia podpisywania certyfikatów** w obszarze **certyfikat podpisywania SAML** sekcji.

9. Wybierz algorytm SHA-1, **algorytm podpisywania**.

Następnym razem użytkownik loguje się do aplikacji usługi Azure AD Zaloguj się do tokenu SAML, przy użyciu algorytmu SHA-1.

## <a name="next-steps"></a>Kolejne kroki
[Jak debugować opartej na SAML logowania jednokrotnego do aplikacji w usłudze Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-saml-debugging)
