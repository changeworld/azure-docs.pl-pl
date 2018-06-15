---
title: Problem podczas konfiguracji federacyjne logowanie jednokrotne dla aplikacji z systemem innym niż galerii | Dokumentacja firmy Microsoft
description: Rozwiązania niektórych typowych problemów, które mogą wystąpić podczas konfigurowania federacyjne logowanie jednokrotne do aplikacji SAML niestandardowego, który nie znajduje się w galerii aplikacji usługi Azure AD
services: active-directory
documentationcenter: ''
author: ajamess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 1990f6595be86d7f3a50f6c764a855aedf2cbcbf
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/11/2018
ms.locfileid: "34052562"
---
# <a name="problem-configuring-federated-single-sign-on-for-a-non-gallery-application"></a>Problem podczas konfiguracji federacyjne logowanie jednokrotne dla aplikacji z systemem innym niż galerii

Jeśli napotkasz problem podczas konfigurowania aplikacji. Sprawdź zostały wykonane wszystkie kroki opisane w artykule [konfigurowania rejestracji jednokrotnej do aplikacji, które nie znajdują się w galerii aplikacji usługi Azure Active Directory.](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-federated-sso-non-gallery)

## <a name="cant-add-another-instance-of-the-application"></a>Nie można dodać inne wystąpienie aplikacji

Aby dodać drugie wystąpienie aplikacji, musisz mieć możliwość:

-   Skonfiguruj Unikatowy identyfikator dla drugiego wystąpienia. Nie można skonfigurować ten sam identyfikator używany po raz pierwszy.

-   Skonfiguruj inny certyfikat niż w pierwszym wystąpieniu.

Jeśli aplikacja nie obsługuje żadnego z poprzednim, nie można skonfigurować drugie wystąpienie.

## <a name="where-do-i-set-the-entityid-user-identifier-format"></a>Gdy ustawienie formatu EntityID (identyfikator użytkownika)

Nie można wybrać format EntityID (identyfikator użytkownika), który wysyła usługi Azure AD do aplikacji w odpowiedzi po uwierzytelnieniu użytkownika.

Usługi Azure AD wybiera formatu żądany przez aplikację w SAML AuthRequest lub format dla atrybutu NameID (identyfikator użytkownika) oparte na wybranej wartości. Aby uzyskać więcej informacji można znaleźć w artykule [protokołu SAML rejestracji jednokrotnej](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) w sekcji NameIDPolicy,

## <a name="where-do-i-get-the-application-metadata-or-certificate-from-azure-ad"></a>Gdzie uzyskać metadanych aplikacji lub certyfikatu z usługi Azure AD

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
