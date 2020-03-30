---
title: Komunikat o błędzie pojawia się na stronie aplikacji po zalogowaniu | Dokumenty firmy Microsoft
description: Jak rozwiązać problemy z logowaniem usługi Azure AD, gdy aplikacja zwraca komunikat o błędzie.
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
ms.openlocfilehash: 9b8d20b31e96973a492355f0515d0532deea0ac9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77185492"
---
# <a name="an-app-page-shows-an-error-message-after-the-user-signs-in"></a>Strona aplikacji wyświetla komunikat o błędzie po zalogowaniu się użytkownika

W tym scenariuszu usługa Azure Active Directory (Azure AD) loguje użytkownika. Ale aplikacja wyświetla komunikat o błędzie i nie pozwala użytkownikowi zakończyć przepływ logowania. Problem polega na tym, że aplikacja nie zaakceptowała odpowiedzi wystawionej przez usługę Azure AD.

Istnieje kilka możliwych przyczyn, dla których aplikacja nie zaakceptowała odpowiedzi z usługi Azure AD. Jeśli komunikat o błędzie nie identyfikuje wyraźnie, czego brakuje w odpowiedzi, spróbuj wykonać następujące czynności:

-   Jeśli aplikacja jest galerią usługi Azure AD, sprawdź, czy postępujesz do kroków w [jak debugować aplikacje logowania jednokrotnego oparte na SAML w usłudze Azure AD.](https://azure.microsoft.com/documentation/articles/active-directory-saml-debugging)

-   Użyj narzędzia, takiego jak [Fiddler,](https://www.telerik.com/fiddler) aby przechwycić żądanie SAML, odpowiedź i token.

-   Wyślij odpowiedź SAML do dostawcy aplikacji i zapytaj go, czego brakuje.

## <a name="attributes-are-missing-from-the-saml-response"></a>W odpowiedzi SAML brakuje atrybutów

Aby dodać atrybut w konfiguracji usługi Azure AD, który zostanie wysłany w odpowiedzi usługi Azure AD, wykonaj następujące kroki:

1. Otwórz [**witrynę Azure portal**](https://portal.azure.com/) i zaloguj się jako administrator globalny lub współadministrator.

2. W górnej części okienka nawigacji po lewej stronie wybierz pozycję **Wszystkie usługi,** aby otworzyć rozszerzenie usługi Azure AD.

3. Wpisz **usługę Azure Active Directory** w polu wyszukiwania filtru, a następnie wybierz pozycję Azure Active **Directory**.

4. Wybierz **aplikacje dla przedsiębiorstw** w okienku nawigacji usługi Azure AD.

5. Wybierz **pozycję Wszystkie aplikacje,** aby wyświetlić listę aplikacji.

   > [!NOTE]
   > Jeśli nie widzisz żądanej aplikacji, użyj kontrolki **Filtruj** u góry **listy Wszystkie aplikacje**. Ustaw opcję **Pokaż** na "Wszystkie aplikacje".

6. Wybierz aplikację, którą chcesz skonfigurować do logowania jednokrotnego.

7. Po załadowaniu aplikacji wybierz **pozycję Logowanie jednokrotne** w okienku nawigacji.

8. W sekcji **Atrybuty użytkownika** wybierz pozycję **Wyświetl i edytuj wszystkie inne atrybuty użytkownika**. W tym miejscu można zmienić atrybuty, które mają być wysyłane do aplikacji w tokenie SAML, gdy użytkownicy się logują.

   Aby dodać atrybut:

   1. Wybierz **pozycję Dodaj atrybut**. Wprowadź **nazwę**i wybierz **wartość** z listy rozwijanej.

   1.  Wybierz **pozycję Zapisz**. Nowy atrybut zostanie wyświetlony w tabeli.

9. Zapisz konfigurację.

   Następnym razem, gdy użytkownik zaloguje się do aplikacji, usługa Azure AD wyśle nowy atrybut w odpowiedzi SAML.

## <a name="the-app-doesnt-identify-the-user"></a>Aplikacja nie identyfikuje użytkownika

Logowanie się do aplikacji kończy się niepowodzeniem, ponieważ w odpowiedzi SAML brakuje atrybutu, takiego jak rola. Lub kończy się niepowodzeniem, ponieważ aplikacja oczekuje innego formatu lub wartości dla **atrybutu NameID** (User Identifier).

Jeśli używasz [automatycznego inicjowania obsługi administracyjnej usługi Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning) do tworzenia, obsługi i usuwania użytkowników w aplikacji, sprawdź, czy użytkownik został aprowizny do aplikacji SaaS. Aby uzyskać więcej informacji, zobacz [Żaden użytkownik nie jest aprowidyzowany w aplikacji usługi Azure AD Gallery.](../app-provisioning/application-provisioning-config-problem-no-users-provisioned.md)

## <a name="add-an-attribute-to-the-azure-ad-app-configuration"></a>Dodawanie atrybutu do konfiguracji aplikacji usługi Azure AD

Aby zmienić wartość identyfikatora użytkownika, wykonaj następujące czynności:

1. Otwórz [**witrynę Azure portal**](https://portal.azure.com/) i zaloguj się jako administrator globalny lub współadministrator.

2. Wybierz **wszystkie usługi** w górnej części okienka nawigacji po lewej stronie, aby otworzyć rozszerzenie usługi Azure AD.

3. Wpisz **usługę Azure Active Directory** w polu wyszukiwania filtru, a następnie wybierz pozycję Azure Active **Directory**.

4. Wybierz **aplikacje dla przedsiębiorstw** w okienku nawigacji usługi Azure AD.

5. Wybierz **pozycję Wszystkie aplikacje,** aby wyświetlić listę aplikacji.

   > [!NOTE]
   > Jeśli nie widzisz żądanej aplikacji, użyj kontrolki **Filtruj** u góry **listy Wszystkie aplikacje**. Ustaw opcję **Pokaż** na "Wszystkie aplikacje".

6. Wybierz aplikację, którą chcesz skonfigurować dla aplikacji SSO.

7. Po załadowaniu aplikacji wybierz **pozycję Logowanie jednokrotne** w okienku nawigacji.

8. W obszarze **Atrybuty użytkownika**wybierz unikatowy identyfikator użytkownika z listy rozwijanej **Identyfikator użytkownika.**

## <a name="change-the-nameid-format"></a>Zmienianie formatu NameID

Jeśli aplikacja oczekuje innego formatu atrybutu **NameID** (User Identifier), zobacz [Edytowanie nameID,](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization#editing-nameid) aby zmienić format NameID.

Usługa Azure AD wybiera format atrybutu **NameID** (identyfikator użytkownika) na podstawie wybranej wartości lub formatu żądanego przez aplikację w 2018 roku. Aby uzyskać więcej informacji, zobacz sekcję "NameIDPolicy" protokołu [SAML logowania jednokrotnego.](https://docs.microsoft.com/azure/active-directory/develop/single-sign-on-saml-protocol#nameidpolicy)

## <a name="the-app-expects-a-different-signature-method-for-the-saml-response"></a>Aplikacja oczekuje innej metody podpisu dla odpowiedzi SAML

Aby zmienić, które części tokenu SAML są podpisane cyfrowo przez usługę Azure AD, wykonaj następujące kroki:

1. Otwórz [witrynę Azure portal](https://portal.azure.com/) i zaloguj się jako administrator globalny lub współadministrator.

2. Wybierz **wszystkie usługi** w górnej części okienka nawigacji po lewej stronie, aby otworzyć rozszerzenie usługi Azure AD.

3. Wpisz **usługę Azure Active Directory** w polu wyszukiwania filtru, a następnie wybierz pozycję Azure Active **Directory**.

4. Wybierz **aplikacje dla przedsiębiorstw** w okienku nawigacji usługi Azure AD.

5. Wybierz **pozycję Wszystkie aplikacje,** aby wyświetlić listę aplikacji.

   > [!NOTE]
   > Jeśli nie widzisz żądanej aplikacji, użyj formantu **Filtru** u góry **listy Wszystkie aplikacje**. Ustaw opcję **Pokaż** na "Wszystkie aplikacje".

6. Wybierz aplikację, którą chcesz skonfigurować do logowania jednokrotnego.

7. Po załadowaniu aplikacji wybierz **opcję Logowanie jednokrotne** w okienku nawigacji.

8. W obszarze **Certyfikat podpisywania SAML**wybierz pozycję **Pokaż zaawansowane ustawienia podpisywania certyfikatów**.

9. Wybierz **opcję podpisywania,** od których aplikacja oczekuje spośród następujących opcji:

   * **Podpisz odpowiedź SAML**
   * **Podpisz odpowiedź i asercja SAML**
   * **Podpisz twierdzenie SAML**

   Następnym razem, gdy użytkownik zaloguje się do aplikacji, usługa Azure AD podpisze część odpowiedzi SAML, która została wybrana.

## <a name="the-app-expects-the-sha-1-signing-algorithm"></a>Aplikacja oczekuje algorytmu podpisywania SHA-1

Domyślnie usługa Azure AD podpisuje token SAML przy użyciu najbardziej bezpiecznego algorytmu. Zaleca się, aby nie zmieniać algorytmu podpisywania na *SHA-1,* chyba że aplikacja wymaga sha-1.

Aby zmienić algorytm podpisywania, wykonaj następujące kroki:

1. Otwórz [witrynę Azure portal](https://portal.azure.com/) i zaloguj się jako administrator globalny lub współadministrator.

2. Wybierz **wszystkie usługi** w górnej części okienka nawigacji po lewej stronie, aby otworzyć rozszerzenie usługi Azure AD.

3. Wpisz **usługę Azure Active Directory** w polu wyszukiwania filtru, a następnie wybierz pozycję Azure Active **Directory**.

4. Wybierz **aplikacje dla przedsiębiorstw** w okienku nawigacji usługi Azure AD.

5. Wybierz **pozycję Wszystkie aplikacje,** aby wyświetlić listę aplikacji.

   > [!NOTE]
   > Jeśli nie widzisz żądanej aplikacji, użyj formantu **Filtru** u góry **listy Wszystkie aplikacje**. Ustaw opcję **Pokaż** na "Wszystkie aplikacje".

6. Wybierz aplikację, którą chcesz skonfigurować do logowania jednokrotnego.

7. Po załadowaniu aplikacji wybierz **opcję Logowanie jednokrotne** z okienka nawigacji po lewej stronie aplikacji.

8. W obszarze **Certyfikat podpisywania SAML**wybierz pozycję **Pokaż zaawansowane ustawienia podpisywania certyfikatów**.

9. Wybierz **SHA-1** jako **algorytm podpisywania**.

   Następnym razem, gdy użytkownik zaloguje się do aplikacji, usługa Azure AD podpisze token SAML przy użyciu algorytmu SHA-1.

## <a name="next-steps"></a>Następne kroki
[Jak debugować oparte na SAML logowanie jednokrotne do aplikacji w usłudze Azure AD](https://azure.microsoft.com/documentation/articles/active-directory-saml-debugging).
