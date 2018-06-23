---
title: Problem podczas konfiguracji federacyjne logowanie jednokrotne dla aplikacji w galerii Azure AD | Dokumentacja firmy Microsoft
description: Niektóre typowe problemy mogą wystąpić podczas konfigurowania federacyjnych pojedynczy adres logowania jednokrotnego dla aplikacji, które są wymienione w galerii aplikacji usługi Azure AD za pomocą protokołu SAML
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
ms.openlocfilehash: 7fcc8c24214465bc1e601ced69ee67df2c48274f
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "36334262"
---
# <a name="problem-configuring-federated-single-sign-on-for-an-azure-ad-gallery-application"></a>Problem podczas konfiguracji federacyjne logowanie jednokrotne dla aplikacji w galerii Azure AD

Jeśli napotkasz problem podczas konfigurowania aplikacji. Sprawdź, czy zostały wykonane wszystkie kroki samouczka dla aplikacji. W konfiguracji aplikacji masz wbudowanego dokumentacji na temat konfigurowania aplikacji. Ponadto można uzyskać dostęp [lista samouczków dotyczących sposobów integracji aplikacji SaaS w usłudze Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/) szczegółowe wskazówki krok po kroku.

## <a name="cant-add-another-instance-of-the-application"></a>Nie można dodać inne wystąpienie aplikacji

Aby dodać drugie wystąpienie aplikacji, musisz mieć możliwość:

-   Skonfiguruj Unikatowy identyfikator dla drugiego wystąpienia. Nie można skonfigurować w taki sam identyfikator używany po raz pierwszy.

-   Skonfiguruj inny certyfikat niż w pierwszym wystąpieniu.

Jeśli aplikacja nie obsługuje dowolne z powyższych. Następnie, nie będzie można skonfigurować drugie wystąpienie.

## <a name="cant-add-the-identifier-or-the-reply-url"></a>Nie można dodać identyfikatora lub adresu URL odpowiedzi

Jeśli nie możesz skonfigurować identyfikatora lub adresu URL odpowiedzi, należy potwierdzić, że wartości identyfikatora i adres URL odpowiedzi zgodne wzorce wstępnie skonfigurowane dla aplikacji.

Znajomość wzorce wstępnie skonfigurowane dla aplikacji:

1.  Otwórz [ **portalu Azure** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego** lub **ko-administratora.** Przejdź do kroku 7. Jeśli jesteś już w bloku konfiguracji aplikacji w usłudze Azure AD.

2.  Otwórz **rozszerzenia usług Azure Active Directory** klikając **wszystkie usługi** w górnej części menu nawigacji po lewej stronie głównej.

3.  Wpisz w **"Azure Active Directory**" w polu wyszukiwania filtr a wybierz **usługi Azure Active Directory** elementu.

4.  Kliknij przycisk **aplikacje dla przedsiębiorstw** z menu nawigacji po lewej stronie usługi Azure Active Directory.

5.  Kliknij przycisk **wszystkie aplikacje** Aby wyświetlić listę wszystkich aplikacji.

   * Jeśli nie ma aplikacji ma tutaj będą wyświetlane, użyj **filtru** kontroli nad **listę wszystkich aplikacji** i ustaw **Pokaż** opcji w celu **wszystkich Aplikacje.**

6.  Wybierz aplikację, aby skonfigurować logowanie jednokrotne.

7.  Po załadowaniu aplikacji, kliknij przycisk **logowanie jednokrotne** z menu nawigacji po lewej stronie aplikacji.

8.  Wybierz **na języku SAML logowania jednokrotnego** z **tryb** listy rozwijanej.

9.  Przejdź do **identyfikator** lub **adres URL odpowiedzi** pole tekstowe, w obszarze **sekcji domeny i adres URL.**

10. Istnieją trzy sposoby znać obsługiwane wzorce dla aplikacji:

   * W polu tekstowym, zobacz ze wzorcem obsługiwane jako symbol zastępczy *przykład:* <https://contoso.com>.

   * Jeśli wzorzec nie jest obsługiwana, zostanie wyświetlony czerwony wykrzyknik, podczas próby wprowadź wartość w polu tekstowym. Umieść kursor nad czerwony wykrzyknik, zobaczysz obsługiwane wzorce.

   * W samouczku dla aplikacji można także uzyskać informacje o obsługiwanych wzorce. W obszarze **Konfigurowanie usługi Azure AD rejestracji jednokrotnej** sekcji. Przejdź do kroku dla skonfigurować wartości w polach **domeny i adres URL** sekcji.

Jeśli wartości nie są zgodne z wzorców wstępnie skonfigurowane w usłudze Azure AD. Możesz:

-   Praca z dostawcą aplikacji, aby uzyskać wartości, które jest zgodny z wzorcem wstępnie skonfigurowane w usłudze Azure AD

-   Można skontaktować się zespół usługi Azure AD <aadapprequest@microsoft.com> lub zostaw komentarz w samouczku, aby zażądać aktualizacji obsługiwane wzorce dla aplikacji

## <a name="where-do-i-set-the-entityid-user-identifier-format"></a>Gdy ustawienie formatu EntityID (identyfikator użytkownika)

Nie można wybrać format EntityID (identyfikator użytkownika) usługi Azure AD wysyła do aplikacji w odpowiedzi po uwierzytelnieniu użytkownika.

Wybrano Azure wybierz AD format dla atrybutu NameID (identyfikator użytkownika) na podstawie wartości lub format żądany przez aplikację w SAML AuthRequest. Aby uzyskać więcej informacji można znaleźć w artykule [protokołu SAML rejestracji jednokrotnej](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) w sekcji NameIDPolicy,

## <a name="cant-find-the-azure-ad-metadata-to-complete-the-configuration-with-the-application"></a>Nie można odnaleźć metadanych usługi Azure AD w celu ukończenia konfiguracji z aplikacją

Aby pobrać metadanych aplikacji lub certyfikatu z usługi Azure AD, wykonaj następujące kroki:

1.  Otwórz [ **portalu Azure** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego** lub **ko-administratora.**

2.  Otwórz **rozszerzenia usług Azure Active Directory** klikając **wszystkie usługi** w górnej części menu nawigacji po lewej stronie głównej.

3.  Wpisz w **"Azure Active Directory**" w polu wyszukiwania filtr a wybierz **usługi Azure Active Directory** elementu.

4.  Kliknij przycisk **aplikacje dla przedsiębiorstw** z menu nawigacji po lewej stronie usługi Azure Active Directory.

5.  Kliknij przycisk **wszystkie aplikacje** Aby wyświetlić listę wszystkich aplikacji.

   * Jeśli nie ma aplikacji ma tutaj będą wyświetlane, użyj **filtru** kontroli nad **listę wszystkich aplikacji** i ustaw **Pokaż** opcji w celu **wszystkich Aplikacje.**

6.  Wybierz aplikację, skonfigurowaniu logowania jednokrotnego.

7.  Po załadowaniu aplikacji, kliknij przycisk **logowanie jednokrotne** z menu nawigacji po lewej stronie aplikacji.

8.  Przejdź do **certyfikat podpisywania SAML** sekcji, a następnie kliknij przycisk **Pobierz** wartość w kolumnie. W zależności od tego, jakie aplikacji wymaga Konfigurowanie logowania jednokrotnego zobaczysz opcję, aby pobrać metadane XML albo certyfikatu.

Usługi Azure AD nie udostępnia adresu URL można pobrać metadanych. Można pobrać metadanych jako plik XML.

## <a name="dont-know-how-to-customize-saml-claims-sent-to-an-application"></a>Nie wiadomo, jak dostosować SAML oświadczenia wysyłane do aplikacji

Aby dowiedzieć się, jak dostosować oświadczeń atrybutów SAML wysyłanych do aplikacji, zobacz [oświadczeń mapowanie w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping) Aby uzyskać więcej informacji.

## <a name="next-steps"></a>Kolejne kroki
[Managing Applications with Azure Active Directory (Zarządzanie aplikacjami za pomocą usługi Azure Active Directory)](manage-apps/what-is-application-management.md)
