---
title: Platforma Azure AD consent framework
titleSuffix: Microsoft identity platform
description: Dowiedz się więcej o platformie zgody w usłudze Azure Active Directory i o tym, jak łatwo jest tworzyć wielodostępne aplikacje sieci web i natywne aplikacje klienckie.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/30/2018
ms.author: ryanwi
ms.reviewer: zachowd, lenalepa, jesakowi
ms.custom: aaddev
ms.openlocfilehash: cb9441e6ce19094ff72e902cdeea151041ceb963
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77161146"
---
# <a name="azure-active-directory-consent-framework"></a>Platforma Azure Active Directory ( platforma Azure Active Directory consent framework)

Struktura zgody usługi Azure Active Directory (Azure AD) ułatwia tworzenie wielodostępnych aplikacji sieci web i natywnych aplikacji klienckich. Te aplikacje umożliwiają logowanie przez konta użytkowników z dzierżawy usługi Azure AD, która różni się od tej, w której aplikacja jest zarejestrowana. Może również być konieczne uzyskiwać dostęp do interfejsów API sieci Web, takich jak interfejs API programu Microsoft Graph (aby uzyskać dostęp do usługi Azure AD, usługi Intune i usług w usłudze Office 365) i interfejsów API innych usług firmy Microsoft, oprócz własnych interfejsów API sieci Web.

Struktura jest oparta na użytkowniku lub administratorze wyrażającym zgodę na aplikację, która prosi o zarejestrowanie się w ich katalogu, co może obejmować dostęp do danych katalogu. Na przykład jeśli aplikacja klienta sieci web musi odczytać informacje kalendarza o użytkowniku z usługi Office 365, ten użytkownik jest zobowiązany do wyrażenia zgody na aplikację kliencką w pierwszej kolejności. Po udzieleniu zgody aplikacja kliencka będzie mogła wywołać interfejs API programu Microsoft Graph w imieniu użytkownika i w razie potrzeby użyć informacji kalendarza. [Interfejs API programu Microsoft Graph](https://developer.microsoft.com/graph) zapewnia dostęp do danych w usłudze Office 365 (takich jak kalendarze i wiadomości z programu Exchange, witryn i list z programu SharePoint, dokumenty z usługi OneDrive, notesy z programu OneNote, zadania z programu Planner i skoroszyty z programu Excel), a także użytkowników i grup z usługi Azure AD i innych obiektów danych z większej liczby usług w chmurze firmy Microsoft.

Struktura zgody jest oparta na OAuth 2.0 i jego różnych przepływów, takich jak udzielanie kodu autoryzacji i udzielanie poświadczeń klienta, przy użyciu klientów publicznych lub poufnych. Za pomocą OAuth 2.0, usługa Azure AD umożliwia tworzenie wielu różnych typów aplikacji klienckich — takich jak na telefonie, tablecie, serwerze lub aplikacji sieci web — i uzyskać dostęp do wymaganych zasobów.

Aby uzyskać więcej informacji na temat korzystania z struktury zgody z dotacjami autoryzacji OAuth2.0, zobacz [Autoryzowanie dostępu do aplikacji sieci Web przy użyciu scenariuszy OAuth 2.0 i Azure AD](v2-oauth2-auth-code-flow.md) i [uwierzytelniania dla usługi Azure AD](authentication-scenarios.md). Aby uzyskać informacje o uzyskiwaniu autoryzowanego dostępu do usługi Office 365 za pośrednictwem programu Microsoft Graph, zobacz [Uwierzytelnianie aplikacji za pomocą programu Microsoft Graph](https://developer.microsoft.com/graph/docs/authorization/auth_overview).

## <a name="consent-experience---an-example"></a>Doświadczenie zgody - przykład

Poniższe kroki pokazują, jak środowisko zgody działa zarówno dla dewelopera aplikacji, jak i dla użytkownika.

1. Załóżmy, że masz aplikację klienta sieci web, która musi zażądać określonych uprawnień, aby uzyskać dostęp do zasobu/interfejsu API. Dowiesz się, jak wykonać tę konfigurację w następnej sekcji, ale zasadniczo witryna Azure portal jest używana do deklarowania żądań uprawnień w czasie konfiguracji. Podobnie jak inne ustawienia konfiguracji, staną się one częścią rejestracji aplikacji w usłudze Azure AD:

    ![Uprawnienia do innych aplikacji](./media/consent-framework/permissions.png)

1. Należy wziąć pod uwagę, że uprawnienia aplikacji zostały zaktualizowane, aplikacja jest uruchomiona, a użytkownik ma zamiar używać go po raz pierwszy. Najpierw aplikacja musi uzyskać kod autoryzacji z `/authorize` punktu końcowego usługi Azure AD. Może wtedy użyć kodu autoryzacji do uzyskania nowego tokenu dostępu i odświeżania.

1. Jeśli użytkownik nie jest jeszcze uwierzytelniony, `/authorize` punkt końcowy usługi Azure AD monituje użytkownika o zalogowanie się.

    ![Użytkownik lub administrator logują się do usługi Azure AD](./media/consent-framework/usersignin.png)

1. Po zalogowaniu się użytkownik usługi Azure AD określi, czy użytkownik musi mieć wyświetlaną stronę zgody. Jest to uzależnione do tego, czy użytkownik (lub administrator w jego organizacji) już wyraził zgodę na aplikację. Jeśli zgoda nie została jeszcze udzielona, usługa Azure AD monituje użytkownika o zgodę i wyświetla wymagane uprawnienia, które muszą działać. Zestaw uprawnień wyświetlanych w oknie dialogowym zgody jest zgodny z uprawnieniami wybranymi w **uprawnieniach delegowanych** w witrynie Azure portal.

    ![Pokazuje przykład uprawnień wyświetlanych w oknie dialogowym zgody](./media/consent-framework/consent.png)

1. Po udzieleniu zgody przez użytkownika kod autoryzacji jest zwracany do aplikacji, która jest realizowana w celu uzyskania tokenu dostępu i odświeżenia tokenu. Aby uzyskać więcej informacji na temat tego przepływu, zobacz [Przepływ kodu autoryzacji OAuth 2.0](v2-oauth2-auth-code-flow.md).

1. Jako administrator możesz także wyrazić zgodę na uprawnienia delegowane aplikacji w imieniu wszystkich użytkowników w dzierżawie. Zgoda administracyjna uniemożliwia wyświetlenie okna dialogowego zgody dla każdego użytkownika w dzierżawie i może być wykonywana w [witrynie Azure portal](https://portal.azure.com) przez użytkowników z rolą administratora. Aby dowiedzieć się, które role administratora mogą wyrazić zgodę na uprawnienia delegowane, zobacz [Uprawnienia roli administratora w usłudze Azure AD](../users-groups-roles/directory-assign-admin-roles.md).

    **Aby wyrazić zgodę na delegowane uprawnienia aplikacji**

   1. Przejdź do strony **uprawnień interfejsu API** dla aplikacji
   1. Kliknij przycisk **Zgoda administratora grantu.**

      ![Udziel uprawnień dla wyraźnej zgody administratora](./media/consent-framework/grant-consent.png)

   > [!IMPORTANT]
   > Udzielanie wyraźnej zgody przy użyciu przycisku **Udzielaj uprawnień** jest obecnie wymagane dla aplikacji jednostronicowych (SPA), które używają pliku ADAL.js. W przeciwnym przypadku wystąpi błąd aplikacji przy żądaniu tokenu dostępu.

## <a name="next-steps"></a>Następne kroki

* Zobacz, [jak konwertować aplikację na wielodostępną](howto-convert-app-to-be-multi-tenant.md)
* Aby uzyskać więcej informacji, dowiedz się, [jak zgoda jest obsługiwana w warstwie protokołu OAuth 2.0 podczas przepływu udzielania uprawnień kodu autoryzacji.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code#request-an-authorization-code)
