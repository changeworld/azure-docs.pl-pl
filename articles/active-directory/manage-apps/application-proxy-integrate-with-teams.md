---
title: Dostęp do aplikacji serwera Proxy aplikacji usługi Azure AD w usłudze Teams | Dokumentacja firmy Microsoft
description: Serwer Proxy aplikacji usługi Azure AD umożliwia dostęp do Twoich aplikacji w środowisku lokalnym za pomocą Microsoft Teams.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/05/2017
ms.author: mimart
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9e9b043b27f624a3d9f38fb2299f9e49f0ffedda
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2019
ms.locfileid: "65783422"
---
# <a name="access-your-on-premises-applications-through-microsoft-teams"></a>Dostęp do aplikacji w środowisku lokalnym za pomocą Microsoft Teams

Azure Active Directory serwera Proxy aplikacji umożliwia logowanie jednokrotne do aplikacji lokalnych, niezależnie od tego, gdzie jesteś. Microsoft Teams usprawnia współpracę wysiłków w jednym miejscu. Integracji ze sobą dwa oznacza, że użytkowników może efektywnie pracować z ich członkami zespołu w każdej sytuacji. 

Użytkownicy mogą dodawać aplikacje w chmurze do ich kanałów zespołów [przy użyciu karty](https://support.office.com/article/Video-Using-Tabs-7350a03e-017a-4a00-a6ae-1c9fe8c497b3?ui=en-US&rs=en-US&ad=US), ale co witryny programu SharePoint lub narzędzie planowania, które są hostowane w środowisku lokalnym? Serwer Proxy aplikacji to rozwiązanie. Mogą dodawać aplikacje opublikowane za pośrednictwem serwera Proxy aplikacji do swoich kanałów za pomocą tego samego zewnętrzne adresy URL zawsze używać, aby zdalnie uzyskiwać dostęp do swoich aplikacji. A ponieważ uwierzytelnia serwer Proxy aplikacji za pomocą usługi Azure Active Directory, użytkownicy pobierają funkcji logowania jednokrotnego.


## <a name="install-the-application-proxy-connector-and-publish-your-app"></a>Zainstaluj łącznik serwera Proxy aplikacji i publikowanie aplikacji

Jeśli jeszcze nie, [skonfigurować serwer Proxy aplikacji dla dzierżawy i zainstaluj łącznik](application-proxy-add-on-premises-application.md). Następnie [opublikować aplikację w środowisku lokalnym](application-proxy-add-on-premises-application.md) dostępu zdalnego. Gdy publikujesz aplikację, zanotuj zewnętrzny adres URL, ponieważ umożliwia dodawanie aplikacji do usługi Teams.

Jeśli masz już opublikowane aplikacje, ale nie pamiętasz swojego zewnętrzne adresy URL, wyszukaj je [witryny Azure portal](https://portal.azure.com). Zaloguj się, a następnie przejdź do **usługi Azure Active Directory** > **aplikacje dla przedsiębiorstw** > **wszystkie aplikacje** > Wybierz swoją aplikację >  **Serwer proxy aplikacji**.

## <a name="add-your-app-to-teams"></a>Dodawanie aplikacji do usługi Teams

Po opublikowaniu aplikacji za pośrednictwem serwera Proxy aplikacji, należy poinformować użytkowników, że mogą dodać go jako kartę bezpośrednio w swoich zespołów kanały, a następnie aplikacja jest dostępna dla wszystkich użytkowników w zespole do użycia. Poproś wykonaj trzy następujące kroki:

1. Przejdź do kanału zespołów, które chcesz dodać tę aplikację, a następnie wybierz pozycję **+** dodać kontrolkę karty.

   ![Wybierz opcję Dodaj kartę](./media/application-proxy-integrate-with-teams/add-tab.png)

2. Wybierz **witryny sieci Web** z opcji karty.

   ![Dodaj witrynę sieci Web](./media/application-proxy-integrate-with-teams/website.png)

3. Nadaj nazwę karty i Ustaw adres URL do zewnętrznego adresu URL serwera Proxy aplikacji. 

   ![Skonfiguruj nazwę karty i adres URL](./media/application-proxy-integrate-with-teams/tab-name-url.png)

Gdy jeden członek zespołu dodaje kartę, zostanie ona wyświetlona dla wszystkich użytkowników w kanale. Użytkownicy, którzy mają dostęp do aplikacji korzystać pojedynczego logowania jednokrotnego przy użyciu poświadczeń, których używają Microsoft Teams. Użytkownicy, którzy nie mają dostępu do aplikacji można zobaczyć kartę w usłudze Teams, ale są blokowane, aż możesz nadać mu uprawnienia do aplikacji lokalnych i portalu Azure opublikowanej wersji aplikacji. 

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się, jak [publikowanie lokalnych witryn programu SharePoint](application-proxy-integrate-with-sharepoint-server.md) przy użyciu serwera Proxy aplikacji.
- Konfigurowanie aplikacji do użycia [domen niestandardowych](application-proxy-configure-custom-domain.md) dla ich zewnętrznego adresu URL. 
