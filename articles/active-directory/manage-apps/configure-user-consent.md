---
title: Konfigurowanie zgody użytkownika do aplikacji — Azure Active Directory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zarządzać w sposób, w jaki użytkownicy wyrazić zgodę na uprawnienia aplikacji. Aby uprościć środowisko użytkownika, możesz udzielania zgody administratora. Metody te mają zastosowanie do wszystkich użytkowników końcowych w dzierżawie usługi Azure Active Directory (Azure AD).
services: active-directory
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 10/22/2018
ms.author: celested
ms.reviewer: arvindh
ms.collection: M365-identity-device-management
ms.openlocfilehash: d35f8b440fe748f91c9e01003fe83a3a5343c8df
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60291589"
---
# <a name="configure-the-way-end-users-consent-to-an-application-in-azure-active-directory"></a>Skonfiguruj sposób, w jaki użytkownicy końcowi wyrażenia zgody na aplikację w usłudze Azure Active Directory
Dowiedz się, jak skonfigurować sposób, w jaki użytkownicy wyrazić zgodę na uprawnienia aplikacji. Aby uprościć środowisko użytkownika, możesz udzielania zgody administratora. Ten artykuł zawiera różne sposoby, które można skonfigurować zgody użytkownika. Te metody mają zastosowanie do wszystkich użytkowników końcowych w dzierżawie usługi Azure Active Directory (Azure AD). 

Aby uzyskać więcej informacji na temat wyrażanie zgody na aplikacje, zobacz [platformy wyrażania zgody w usłudze Azure Active Directory](../develop/consent-framework.md).

## <a name="prerequisites"></a>Wymagania wstępne

Udzielania zgody administratora, należy zalogować się jako administrator globalny, administrator aplikacji lub administrator aplikacji w chmurze.

Aby ograniczyć dostęp do aplikacji, musisz wymaganie przypisania użytkownika, a następnie Przypisz użytkowników lub grup do aplikacji.  Aby uzyskać więcej informacji, zobacz [metody przypisywania użytkowników i grup](methods-for-assigning-users-and-groups.md).

## <a name="grant-admin-consent-to-enterprise-apps-in-the-azure-portal"></a>Udzielić zgody administratora do aplikacji dla przedsiębiorstw w witrynie Azure portal

Aby udzielić zgody administratora do aplikacji przedsiębiorstwa:

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) jako administrator globalny, administrator aplikacji lub administrator aplikacji w chmurze.
2. Kliknij przycisk **wszystkich usług** w górnej części menu nawigacji po lewej stronie. **Rozszerzenia usługi Azure Active Directory** zostanie otwarty.
3. W polu filtru wyszukiwania wpisz **"Azure Active Directory"** i wybierz **usługi Azure Active Directory** elementu.
4. W menu nawigacyjnym kliknij **aplikacje dla przedsiębiorstw**.
5. Kliknij przycisk **zgody administratora Grant**. Zostanie wyświetlony monit logować się do administrowanie aplikacji.
6. Zaloguj się przy użyciu konta, które ma uprawnienia do udzielania zgody administratora dla aplikacji. 
7. Wyrazić zgodę na uprawnienia dostępu do aplikacji.

Tej opcji tylko wtedy, gdy aplikacja jest: 

- Zarejestrowana w Twojej dzierżawie lub
- Zarejestrowany w innej dzierżawie usługi Azure AD, a które wyraził zgodę, przez co najmniej jednego użytkownika końcowego. Po użytkownik końcowy wyraził zgody na aplikację, usługi Azure AD zawiera listę aplikacji w ramach **aplikacje dla przedsiębiorstw** w witrynie Azure portal.

## <a name="grant-admin-consent-when-registering-an-app-in-the-azure-portal"></a>Udzielić zgody administratora, podczas rejestrowania aplikacji w witrynie Azure portal

Aby udzielić zgody administratora, podczas rejestrowania aplikacji: 

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com) jako administrator globalny.
2. Przejdź do **rejestracje aplikacji** bloku.
3. Wybierz aplikację, o ich zgodę.
4. Wybierz **wymagane uprawnienia**.
5. Kliknij przycisk **Udziel uprawnień** w górnej części bloku.


## <a name="grant-admin-consent-through-a-url-request"></a>Udzielić zgody administratora, za pośrednictwem adresu URL żądania

Aby udzielić zgody administratora, za pośrednictwem adresu URL żądania:

1. Konstruowania żądania *login.microsoftonline.com* z konfiguracji aplikacji i Dołącz na `&prompt=admin_consent`. 
2. Po zarejestrowaniu się przy użyciu poświadczeń administratora, aplikację udzielono zgody dla wszystkich użytkowników.


## <a name="force-user-consent-through-a-url-request"></a>Wymuś zgody użytkownika za pośrednictwem adresu URL żądania

Aby wymagać od użytkowników końcowych do wyrażenia zgody na aplikację każdorazowo podczas uwierzytelniania, należy dołączyć `&prompt=consent` adres URL żądania uwierzytelnienia.

## <a name="next-steps"></a>Kolejne kroki

[Integrowanie aplikacji do usługi Azure AD i zgody](../develop/quickstart-v1-integrate-apps-with-azure-ad.md)

[Wyrażania zgody i udzielania do nich uprawnień dla usługi Azure AD v2.0 zbieżne aplikacje](../develop/active-directory-v2-scopes.md)

[AzureAD StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)
