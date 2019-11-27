---
title: Konfigurowanie zgody użytkownika na Azure Active Directory aplikacji
description: Dowiedz się, jak zarządzać sposobem wyrażania zgody użytkowników na uprawnienia aplikacji. Możesz uprościć środowisko użytkownika, udzielając zgody administratora. Te metody mają zastosowanie do wszystkich użytkowników końcowych w dzierżawie usługi Azure Active Directory (Azure AD).
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 10/22/2018
ms.author: mimart
ms.reviewer: arvindh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4a187ea9d22efa40c482ea8930be0271d84a899a
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74273986"
---
# <a name="configure-the-way-end-users-consent-to-an-application-in-azure-active-directory"></a>Konfigurowanie sposobu, w jaki użytkownicy końcowi wyrażają zgodę na aplikację w Azure Active Directory
Dowiedz się, jak skonfigurować sposób, w jaki użytkownicy wyrażają zgodę na uprawnienia aplikacji. Możesz uprościć środowisko użytkownika, udzielając zgody administratora. W tym artykule przedstawiono różne sposoby konfigurowania wyrażania zgody użytkownika. Metody dotyczą wszystkich użytkowników końcowych w dzierżawie usługi Azure Active Directory (Azure AD). 

Aby uzyskać więcej informacji na temat wyrażania zgody na aplikacje, zobacz [Azure Active Directory](../develop/consent-framework.md).

## <a name="prerequisites"></a>Wymagania wstępne

Przyznanie zgody administratora wymaga zalogowania się jako Administrator globalny, administrator aplikacji lub administrator aplikacji w chmurze.

Aby ograniczyć dostęp do aplikacji, należy wymagać przypisania użytkownika, a następnie przypisania użytkowników lub grup do aplikacji.  Aby uzyskać więcej informacji, zobacz [metody przypisywania użytkowników i grup](methods-for-assigning-users-and-groups.md).

## <a name="grant-admin-consent-to-enterprise-apps-in-the-azure-portal"></a>Udziel zgody administratora na aplikacje dla przedsiębiorstw w Azure Portal

Aby udzielić zgody administratora na aplikację dla przedsiębiorstw:

1. Zaloguj się do [Azure Portal](https://portal.azure.com) jako Administrator globalny, administrator aplikacji lub administrator aplikacji w chmurze.
2. Kliknij pozycję **wszystkie usługi** w górnej części menu nawigacji po lewej stronie. Zostanie otwarte **rozszerzenie Azure Active Directory** .
3. W polu wyszukiwania filtru wpisz **"Azure Active Directory"** i wybierz element **Azure Active Directory** .
4. W menu nawigacji kliknij pozycję **aplikacje dla przedsiębiorstw**.
5. Wybierz aplikację do zgody.
6. Wybierz pozycję **uprawnienia** , a następnie kliknij pozycję **Udziel zgody administratora**. Zostanie wyświetlony monit o zalogowanie się w celu administrowania aplikacją.
7. Zaloguj się przy użyciu konta, które ma uprawnienia do udzielania zgody administratora aplikacji. 
8. Wyrażanie zgody na uprawnienia aplikacji.

Ta opcja działa tylko wtedy, gdy aplikacja jest: 

- Zarejestrowane w dzierżawie lub
- Zarejestrowane w innej dzierżawie usługi Azure AD i wysłane przez co najmniej jednego użytkownika końcowego. Gdy użytkownik końcowy wyraził zgodę na aplikację, usługa Azure AD wyświetla listę aplikacji w obszarze **aplikacje dla przedsiębiorstw** w Azure Portal.

## <a name="grant-admin-consent-when-registering-an-app-in-the-azure-portal"></a>Udziel zgody administratora podczas rejestrowania aplikacji w Azure Portal

Aby udzielić zgody administratora podczas rejestrowania aplikacji: 

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com) jako administrator globalny.
2. Przejdź do bloku **rejestracje aplikacji** .
3. Wybierz aplikację, aby wyrazić zgodę.
4. Wybierz pozycję **uprawnienia interfejsu API**.
5. Kliknij pozycję **Udziel zgody administratora**.


## <a name="grant-admin-consent-through-a-url-request"></a>Udzielanie zgody administratora przez żądanie adresu URL

Aby udzielić zgody administratora przez żądanie adresu URL:

1. Utwórz żądanie *login.microsoftonline.com* z konfiguracją aplikacji i dołącz do `&prompt=admin_consent`. Ten adres URL będzie wyglądać następująco: `https://login.microsoftonline.com/<tenant-id>/oauth2/authorize?client_id=<client id>&response_type=code&redirect_uri=<Your-Redirect-URI-Https-Encoded>&nonce=1234&resource=<your-resource-Https-encoded>&prompt=admin_consent`
2. Po zalogowaniu się przy użyciu poświadczeń administratora aplikacja uzyskała zgodę dla wszystkich użytkowników.


## <a name="force-user-consent-through-a-url-request"></a>Wymuś zgodę użytkownika za pomocą żądania adresu URL

Aby użytkownicy końcowi musieli wyrazić zgodę na aplikację przy każdym uwierzytelnianiu, należy dołączyć `&prompt=consent` do adresu URL żądania uwierzytelnienia.
Ten adres URL będzie wyglądać następująco: `https://login.microsoftonline.com/<tenant-id>/oauth2/authorize?client_id=<client id>&response_type=code&redirect_uri=<Your-Redirect-URI-Https-Encoded>&nonce=1234&resource=<your-resource-Https-encoded>&prompt=consent`

## <a name="next-steps"></a>Następne kroki

[Wyrażanie zgody i Integrowanie aplikacji z usługą AzureAD](../develop/quickstart-v1-integrate-apps-with-azure-ad.md)

[Wyrażanie zgody i uprawnień dla aplikacji AzureAD v 2.0 z zbieżnością](../develop/active-directory-v2-scopes.md)

[AzureAD StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)
