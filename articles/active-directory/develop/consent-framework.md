---
title: Platformy wyrażania zgody w usłudze Azure Active Directory
description: Więcej informacji na temat platformy wyrażania zgody w usłudze Azure Active Directory i jak ją można łatwo tworzyć wielu dzierżawców w sieci web i natywne aplikacje klienckie.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/30/2018
ms.author: ryanwi
ms.reviewer: zachowd, lenalepa, jesakowi
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 93adedc5c1343df1eee05b653b60cfd7e810044c
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/11/2019
ms.locfileid: "65540428"
---
# <a name="azure-active-directory-consent-framework"></a>Platformy wyrażania zgody w usłudze Azure Active Directory

Platformy wyrażania zgody usługi Azure Active Directory (Azure AD) można łatwo tworzyć wielu dzierżawców w sieci web i natywne aplikacje klienckie. Te aplikacje Zezwalaj na logowanie za pomocą kont użytkownika z dzierżawy usługi Azure AD, który jest inny niż ten, na którym aplikacja jest zarejestrowany. Mogą one również potrzebować dostępu do sieci web, interfejsów API, np. interfejsu API programu Microsoft Graph (w celu dostępu do usługi Azure AD, Intune i usługi w usłudze Office 365) i innych usług firmy Microsoft, oprócz własnych interfejsów API sieci web.

Struktura opiera się na użytkownika lub administratora wyrażenia zgody na aplikację, która prosi do zarejestrowania się w jego katalogu, który może obejmować dostęp do danych katalogu. Na przykład jeśli wymaga aplikacji klienta sieci web na odczytywanie kalendarza informacje o użytkowniku z usługi Office 365, tego użytkownika jest wymagane do wyrażenia zgody aplikacji klienckiej. Po zgoda zostanie podany, aplikacja kliencka będzie do wywołania interfejsu API programu Microsoft Graph w imieniu użytkownika, skorzystaj z informacji kalendarza, zgodnie z potrzebami. [Interfejsu API Microsoft Graph](https://developer.microsoft.com/graph) zapewnia dostęp do danych w usłudze Office 365 (np. kalendarzy i komunikaty z programu Exchange, witrynom i listom z programu SharePoint, dokumentów z poziomu usługi OneDrive, notesów z programu OneNote, zadania w aplikacji Planner i skoroszyty z W programie Excel), a także użytkownicy i grupy z usługi Azure AD i innych obiektów danych z więcej usług w chmurze firmy Microsoft.

Platformy wyrażania zgody jest oparta na protokołu OAuth 2.0 i jego różnych przepływów, takie jak udzielić kod autoryzacji poświadczenia przydział i klienta, przy użyciu publicznego lub poufnych klientów. Za pomocą protokołu OAuth 2.0, usługi Azure AD umożliwia tworzenie wiele różnych typów aplikacji klienckich — takie jak w telefonie, tablecie, serwera lub aplikacji sieci web — i uzyskiwania dostępu do wymaganych zasobów.

Aby uzyskać więcej informacji o korzystaniu z platformy wyrażania zgody z przydziałów autoryzacji OAuth 2.0, zobacz [Autoryzowanie dostępu do aplikacji sieci web przy użyciu protokołu OAuth 2.0 i usługi Azure AD](v1-protocols-oauth-code.md) i [scenariusze uwierzytelniania dla usługi Azure AD](authentication-scenarios.md). Aby uzyskać informacje na temat pobierania autoryzowanego dostępu do usługi Office 365 za pomocą programu Microsoft Graph, zobacz [uwierzytelniania aplikacji za pomocą programu Microsoft Graph](https://developer.microsoft.com/graph/docs/authorization/auth_overview).

## <a name="consent-experience---an-example"></a>Obsługa zgody — przykład

Poniższe kroki pokazują, jak środowisko działa zgody dla deweloperów aplikacji i użytkownika.

1. Załóżmy, że masz klienta aplikacji sieci web, który wymaga, aby żądać określonych uprawnień dostępu do zasobów/interfejsu API. Dowiesz się, jak przeprowadzić tę konfigurację w następnej sekcji, ale zasadniczo witryny Azure portal jest używane do deklarowania żądań dotyczących uprawnień podczas konfiguracji. Podobnie jak inne ustawienia konfiguracji, staną się one częścią rejestracji aplikacji w usłudze Azure AD:

    ![Uprawnienia do innych aplikacji](./media/quickstart-v1-integrate-apps-with-azure-ad/requiredpermissions.png)

1. Należy wziąć pod uwagę uprawnienia aplikacji zostały zaktualizowane, aplikacja jest uruchomiona i użytkownik ma użyć go po raz pierwszy. Po pierwsze, aplikacja musi uzyskać kod autoryzacji z usługi Azure AD `/authorize` punktu końcowego. Może wtedy użyć kodu autoryzacji do uzyskania nowego tokenu dostępu i odświeżania.

1. Jeśli użytkownik nie jest już uwierzytelniony, Azure AD `/authorize` punktu końcowego monituje użytkownika do logowania.

    ![Użytkownik lub administrator, zaloguj się do usługi Azure AD](./media/quickstart-v1-integrate-apps-with-azure-ad/usersignin.png)

1. Po użytkownik zalogował się w usłudze Azure AD określi, jeśli użytkownik powinien być wyświetlony strona zgody użytkownika. Jest to uzależnione do tego, czy użytkownik (lub administrator w jego organizacji) już wyraził zgodę na aplikację. Jeśli zgody nie ma już przyznane, usługi Azure AD monituje użytkownika o zgodę i wyświetli wymagane uprawnienia niezbędne do działania. Zestaw uprawnień, które są wyświetlane w oknie dialogowym wyrażania zgody są zgodne z typami, które wybrano w **delegowane uprawnienia** w witrynie Azure portal.

    ![Środowisko zgody użytkownika](./media/quickstart-v1-integrate-apps-with-azure-ad/consent.png)

1. Po użytkownik udziela zgody, Kod autoryzacji jest zwracana do aplikacji jest zrealizowany uzyskiwanie tokenu dostępu i token odświeżania. Aby uzyskać więcej informacji na temat tego przepływu, zobacz [typ aplikacji interfejsu API sieci Web](web-api.md).

1. Jako administrator możesz także wyrazić zgodę na uprawnienia delegowane aplikacji w imieniu wszystkich użytkowników w dzierżawie. Zgody administratora, okno dialogowe ze zgodą zapobiega wyświetlane dla każdego użytkownika w dzierżawie i może odbywać się [witryny Azure portal](https://portal.azure.com) przez użytkowników z roli administratora. Aby dowiedzieć się, który administrator ról mogą wyrazić zgodę na delegowane uprawnienia, zobacz [uprawnienia roli administratora w usłudze Azure AD](../users-groups-roles/directory-assign-admin-roles.md).

    **Do wyrażenia zgody na aplikację na delegowane uprawnienia**

   1. Przejdź do **ustawienia** strony aplikacji
   1. Wybierz **wymagane uprawnienia**.
   1. Kliknij pozycję **udzielić uprawnień** przycisku.

      ![Przyznawanie uprawnień dla zgody administratora jawne](./media/quickstart-v1-integrate-apps-with-azure-ad/grantpermissions.png)

   > [!IMPORTANT]
   > Udzielanie jawnej zgody, za pomocą **udzielić uprawnień** przycisk jest obecnie wymagane dla aplikacji jednostronicowej (SPA), które używają ADAL.js. W przeciwnym przypadku wystąpi błąd aplikacji przy żądaniu tokenu dostępu.

## <a name="next-steps"></a>Kolejne kroki

* Zobacz [sposób konwertowania aplikacji wielodostępnych](howto-convert-app-to-be-multi-tenant.md)
* Aby uzyskać bardziej szczegółowe informacje, Dowiedz się, [jak zgody jest obsługiwana na poziomie warstwy protokołu OAuth 2.0 w przepływie przyznawania kodu autoryzacji.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code#request-an-authorization-code)
