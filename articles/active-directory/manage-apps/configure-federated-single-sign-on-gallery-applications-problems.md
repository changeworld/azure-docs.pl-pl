---
title: Problem podczas konfigurowania federacyjnego logowania jednokrotnego dla aplikacji galerii usługi Azure AD | Dokumentacja firmy Microsoft
description: Niektóre typowe problemy, które można napotkać podczas konfigurowania federacyjnego pojedynczego logowania jednokrotnego za pośrednictwem protokołu SAML dla aplikacji, które są wyświetlane w galerii aplikacji usługi Azure AD
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 554fb73ea7bf4f866eda3e9f3d629f871cb5e582
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2019
ms.locfileid: "65781166"
---
# <a name="problem-configuring-federated-single-sign-on-for-an-azure-ad-gallery-application"></a>Problem podczas konfigurowania federacyjnego logowania jednokrotnego dla aplikacji galerii usługi Azure AD

Jeśli wystąpi problem podczas konfigurowania aplikacji. Sprawdź, czy zostały wykonane wszystkie kroki przedstawione w tym samouczku dla aplikacji. W konfiguracji aplikacji istnieje wbudowana dokumentacja na temat konfigurowania aplikacji. Ponadto, możesz uzyskać dostęp [listę samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/) szczegółowe wskazówki krok po kroku.

## <a name="cant-add-another-instance-of-the-application"></a>Nie można dodać inne wystąpienie aplikacji

Aby dodać drugiego wystąpienia aplikacji, musisz mieć możliwość:

-   Skonfiguruj Unikatowy identyfikator drugiego wystąpienia. Nie można skonfigurować ten sam identyfikator używany na potrzeby pierwszego wystąpienia.

-   Konfigurowanie certyfikatu innego niż ten używany po raz pierwszy.

Jeśli aplikacja nie obsługuje żadnego z powyższych subskrypcji. Następnie będzie mógł skonfigurować drugie wystąpienie.

## <a name="cant-add-the-identifier-or-the-reply-url"></a>Nie można dodać identyfikatora lub adres URL odpowiedzi

Jeśli nie możesz skonfigurować identyfikator lub adres URL odpowiedzi, upewnij się, że wartości identyfikatora i adres URL odpowiedzi zgodne wzorców wstępnie skonfigurowane na potrzeby aplikacji.

Aby dowiedzieć się wzorców wstępnie skonfigurowane na potrzeby aplikacji:

1. Otwórz [ **witryny Azure portal** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego** lub **Współadministratora.** Przejdź do kroku 7. Jeśli jesteś już w bloku Konfiguracja aplikacji w usłudze Azure AD.

2. Otwórz **rozszerzenia usługi Azure Active Directory** , klikając **wszystkich usług** w górnej części menu główne menu nawigacji po lewej stronie.

3. Wpisz **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz pozycję **usługi Azure Active Directory** elementu.

4. Kliknij przycisk **aplikacje dla przedsiębiorstw** menu nawigacji po lewej stronie usługi Azure Active Directory.

5. Kliknij przycisk **wszystkie aplikacje** Aby wyświetlić listę wszystkich aplikacji.

   * Jeśli nie widzisz aplikacji, chcesz, aby wyświetlić tutaj użyć **filtru** formant w górnej części **listę wszystkich aplikacji** i ustaw **Pokaż** opcję **wszystkie Aplikacje.**

6. Wybierz aplikację, którą chcesz skonfigurować logowanie jednokrotne.

7. Po załadowaniu aplikacji, kliknij przycisk **logowanie jednokrotne** menu nawigacji po lewej stronie aplikacji.

8. Wybierz **opartej na SAML logowania jednokrotnego** z **tryb** listy rozwijanej.

9. Przejdź do **identyfikator** lub **adres URL odpowiedzi** pole tekstowe, w obszarze **sekcji domena i adresy URL.**

10. Istnieją trzy sposoby, aby wiedzieć, obsługiwane wzorce dla aplikacji:

    * W polu tekstowym, obsługiwanych wzorców zostanie wyświetlony jako symbol zastępczy *przykład:* <https://contoso.com>.

    * Jeśli wzorzec nie jest obsługiwana, zobaczysz czerwony wykrzyknik, podczas próby wprowadź wartość w polu tekstowym. Jeśli wskaźnik myszy nad czerwony wykrzyknik, zostanie wyświetlony obsługiwane wzorce.

    * W tym samouczku aplikacji można także uzyskać informacje dotyczące obsługiwanych wzorców. W obszarze **usługi Azure AD Konfigurowanie logowania jednokrotnego** sekcji. Przejdź do kroku, aby skonfigurować wartości w polach **domena i adresy URL** sekcji.

Jeśli wartości nie są zgodne ze wzorcami wstępnie skonfigurowane w usłudze Azure AD. Możesz:

-   Praca z dostawcą aplikacji, aby uzyskać wartości, które pasuje do wzorca, wstępnie skonfigurowane w usłudze Azure AD

-   Alternatywnie można skontaktować się z zespołem usługi Azure AD <aadapprequest@microsoft.com> lub Dodaj komentarz w tym samouczku, aby zażądać aktualizacji obsługiwane wzorce dla aplikacji

## <a name="where-do-i-set-the-entityid-user-identifier-format"></a>Gdzie ustawić format EntityID (identyfikator użytkownika)

Nie można wybrać format EntityID (identyfikator użytkownika), który usługa Azure AD wysyła do aplikacji w odpowiedzi po uwierzytelnieniu użytkownika.

Wybrana usługa Azure AD wybierz pozycję formatu NameID atrybutu (identyfikator użytkownika) na podstawie wartości lub format żądany przez aplikację w SAML AuthRequest. Więcej informacji na ten temat można znaleźć w artykule [protokołu SAML rejestracji jednokrotnej](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) sekcji NameIDPolicy,

## <a name="cant-find-the-azure-ad-metadata-to-complete-the-configuration-with-the-application"></a>Nie można odnaleźć metadanych usługi Azure AD w celu ukończenia konfiguracji z aplikacją

Aby pobrać metadanych aplikacji lub certyfikatu z usługi Azure AD, wykonaj następujące kroki:

1. Otwórz [ **witryny Azure portal** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego** lub **Współadministratora.**

2. Otwórz **rozszerzenia usługi Azure Active Directory** , klikając **wszystkich usług** w górnej części menu główne menu nawigacji po lewej stronie.

3. Wpisz **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz pozycję **usługi Azure Active Directory** elementu.

4. Kliknij przycisk **aplikacje dla przedsiębiorstw** menu nawigacji po lewej stronie usługi Azure Active Directory.

5. Kliknij przycisk **wszystkie aplikacje** Aby wyświetlić listę wszystkich aplikacji.

   * Jeśli nie widzisz aplikacji, chcesz, aby wyświetlić tutaj użyć **filtru** formant w górnej części **listę wszystkich aplikacji** i ustaw **Pokaż** opcję **wszystkie Aplikacje.**

6. Wybierz aplikację, zostały skonfigurowane logowanie jednokrotne.

7. Po załadowaniu aplikacji, kliknij przycisk **logowanie jednokrotne** menu nawigacji po lewej stronie aplikacji.

8. Przejdź do **certyfikat podpisywania SAML** sekcji, a następnie kliknij przycisk **Pobierz** wartości w kolumnie. W zależności od tego, jakie aplikacji wymagane jest skonfigurowanie logowania jednokrotnego zobaczysz opcję Pobierz kod XML metadanych albo certyfikatu.

Usługa Azure AD nie zawiera adres URL, aby pobrać metadane. Można pobrać metadanych jako plik XML.

## <a name="dont-know-how-to-customize-saml-claims-sent-to-an-application"></a>Nie wiem, jak dostosować oświadczenia języka SAML wysyłane do aplikacji

Aby dowiedzieć się, jak dostosować oświadczeń atrybutów SAML, które są wysyłane do aplikacji, zobacz [Mapowanie oświadczeń w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping) Aby uzyskać więcej informacji.

## <a name="next-steps"></a>Kolejne kroki
[Managing Applications with Azure Active Directory (Zarządzanie aplikacjami za pomocą usługi Azure Active Directory)](what-is-application-management.md)
