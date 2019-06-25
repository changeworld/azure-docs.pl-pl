---
title: Problem podczas konfigurowania federacyjnego logowania jednokrotnego dla aplikacji spoza galerii | Dokumentacja firmy Microsoft
description: Niektóre typowe problemy, które można napotkać podczas konfigurowania federacyjnego logowania jednokrotnego do aplikacji SAML niestandardowego, który nie znajduje się w galerii aplikacji usługi Azure AD
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
ms.openlocfilehash: 87c70990f003a39def551171b8637615129379b4
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67190306"
---
# <a name="problem-configuring-federated-single-sign-on-for-a-non-gallery-application"></a>Problem podczas konfigurowania federacyjnego logowania jednokrotnego dla aplikacji spoza galerii

Jeśli wystąpi problem podczas konfigurowania aplikacji. Sprawdź, że zostały wykonane wszystkie kroki w artykule [Konfigurowanie logowania jednokrotnego do aplikacji, które nie znajdują się w galerii aplikacji usługi Azure Active Directory.](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-federated-sso-non-gallery)

## <a name="cant-add-another-instance-of-the-application"></a>Nie można dodać inne wystąpienie aplikacji

Aby dodać drugiego wystąpienia aplikacji, musisz mieć możliwość:

-   Skonfiguruj Unikatowy identyfikator drugiego wystąpienia. Nie można skonfigurować ten sam identyfikator używany na potrzeby pierwszego wystąpienia.

-   Konfigurowanie certyfikatu innego niż ten używany po raz pierwszy.

Jeśli aplikacja nie obsługuje żadnego z poprzednim, nie można skonfigurować drugie wystąpienie.

## <a name="where-do-i-set-the-entityid-user-identifier-format"></a>Gdzie ustawić format EntityID (identyfikator użytkownika)

Nie można wybrać format EntityID (identyfikator użytkownika), który usługa Azure AD wysyła do aplikacji w odpowiedzi po uwierzytelnieniu użytkownika.

Usługa Azure AD wybiera formacie dla atrybutu NameID (identyfikator użytkownika) na podstawie wartości wybrane lub żądany przez aplikację w SAML AuthRequest. Więcej informacji na ten temat można znaleźć w artykule [protokołu SAML rejestracji jednokrotnej](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) sekcji NameIDPolicy,

## <a name="where-do-i-get-the-application-metadata-or-certificate-from-azure-ad"></a>Gdzie uzyskać metadanych aplikacji lub certyfikatu z usługi Azure AD

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
