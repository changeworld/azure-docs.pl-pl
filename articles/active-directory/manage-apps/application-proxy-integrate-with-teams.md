---
title: Dostęp do aplikacji serwera Proxy aplikacji usługi Azure AD w zespołach | Dokumentacja firmy Microsoft
description: Użyj serwera Proxy aplikacji usługi Azure AD na dostęp do aplikacji lokalnych poprzez Teams firmy Microsoft.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/05/2017
ms.author: barbkess
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: b2d240e6339902ce24061b5db3ad1b8c2915eb59
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35303600"
---
# <a name="access-your-on-premises-applications-through-microsoft-teams"></a>Dostęp do aplikacji lokalnych poprzez Teams firmy Microsoft

Azure Active Directory serwera Proxy aplikacji umożliwia logowanie jednokrotne do aplikacji lokalnych niezależnie od tego, gdzie się znajdujesz. Microsoft Teams usprawnia współpracy wysiłków w jednym miejscu. Integracja ze sobą dwa oznacza, że użytkownicy może być wykorzystują ich członków zespołu w każdej sytuacji. 

Użytkownicy mogą dodawać aplikacje w chmurze do ich kanałów zespoły [przy użyciu karty](https://support.office.com/article/Video-Using-Tabs-7350a03e-017a-4a00-a6ae-1c9fe8c497b3?ui=en-US&rs=en-US&ad=US), ale jak witryny programu SharePoint lub narzędzie do planowania, które są hostowane lokalnie? Serwer Proxy aplikacji jest rozwiązanie. Można dodać aplikacji opublikowanych przy użyciu serwera Proxy aplikacji do ich kanałów przy użyciu tego samego zewnętrzne adresy URL zawsze używać, aby zdalnie uzyskiwać dostęp do swoich aplikacji. I ponieważ uwierzytelnia serwer Proxy aplikacji za pomocą usługi Azure Active Directory, użytkownicy pobierają pojedynczego środowisko logowania.


## <a name="install-the-application-proxy-connector-and-publish-your-app"></a>Instalowanie łącznika serwera Proxy aplikacji i publikowanie aplikacji

Jeśli nie jest jeszcze, [skonfigurować serwer Proxy aplikacji dzierżawy i zainstalować łącznik](application-proxy-enable.md). Następnie [opublikować aplikację lokalną](application-proxy-publish-azure-portal.md) dostępu zdalnego. W przypadku publikowania aplikacji, zwróć uwagę na zewnętrzny adres URL, ponieważ jest on Dodaj aplikację do zespołów.

Jeśli już masz aplikacje opublikowane, ale nie zapamiętuj ich zewnętrzne adresy URL, wyszukaj je [portalu Azure](https://portal.azure.com). Zaloguj się, a następnie przejdź do **usługi Azure Active Directory** > **aplikacje dla przedsiębiorstw** > **wszystkie aplikacje** > Wybierz aplikację > **serwera proxy aplikacji**.

## <a name="add-your-app-to-teams"></a>Dodaj aplikację do zespołów

Po opublikowaniu aplikacji za pośrednictwem serwera Proxy aplikacji, należy poinformować użytkowników, że można go dodać na karcie bezpośrednio w ich kanały zespołów, a następnie aplikacja jest dostępna dla wszystkich członków zespołu do użycia. Niech wykonaj następujące trzy kroki:

1. Przejdź do kanału zespołów, które chcesz dodać tę aplikację i wybierz **+** można dodać na karcie.

   ![Wybierz opcję Dodaj kartę](./media/application-proxy-integrate-with-teams/add-tab.png)

2. Wybierz **witryny sieci Web** opcje kartę.

   ![Dodaj witrynę sieci Web](./media/application-proxy-integrate-with-teams/website.png)

3. Nadaj nazwę kartę i ustawić adres URL do zewnętrznego adresu URL serwera Proxy aplikacji. 

   ![Skonfiguruj kartę nazwy i adresu URL](./media/application-proxy-integrate-with-teams/tab-name-url.png)

Po jednego członka do zespołu dodaje kartę, jest wyświetlane dla wszystkich użytkowników w kanale. Wszyscy użytkownicy, którzy mają dostęp do aplikacji korzystać pojedynczego logowania jednokrotnego przy użyciu poświadczeń używanych dla Teams firmy Microsoft. Wszyscy użytkownicy, którzy nie mają dostępu do aplikacji można zobaczyć karty w zespołach, ale są zablokowane, dopóki nie można nadać mu uprawnienia do aplikacji lokalnych i portalu Azure opublikowanej wersji aplikacji. 

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się, jak [publikowanie witryn programu SharePoint lokalnymi](application-proxy-integrate-with-sharepoint-server.md) z serwerem Proxy aplikacji.
- Konfigurowanie aplikacji do użycia [domen niestandardowych](application-proxy-configure-custom-domain.md) dla ich zewnętrznego adresu URL. 
