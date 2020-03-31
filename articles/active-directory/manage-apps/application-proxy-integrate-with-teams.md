---
title: Uzyskiwanie dostępu do aplikacji proxy aplikacji usługi Azure AD w zespołach | Dokumenty firmy Microsoft
description: Użyj serwera proxy aplikacji usługi Azure AD, aby uzyskać dostęp do aplikacji lokalnej za pośrednictwem usługi Microsoft Teams.
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
ms.openlocfilehash: 037e005993a54e525560571a6d893197af99b6a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67807773"
---
# <a name="access-your-on-premises-applications-through-microsoft-teams"></a>Uzyskiwanie dostępu do aplikacji lokalnych za pośrednictwem usługi Microsoft Teams

Usługa Azure Active Directory Application Proxy zapewnia logowanie jednokrotne do aplikacji lokalnych bez względu na to, gdzie jesteś. Usługa Microsoft Teams usprawnia współpracę w jednym miejscu. Integracja tych dwóch oznacza, że użytkownicy mogą być produktywni z członkami zespołu w każdej sytuacji.

Użytkownicy mogą dodawać aplikacje w chmurze do swoich kanałów usługi Teams [za pomocą kart,](https://support.office.com/article/Video-Using-Tabs-7350a03e-017a-4a00-a6ae-1c9fe8c497b3?ui=en-US&rs=en-US&ad=US)ale co z witrynami programu SharePoint lub narzędziem planowania hostowanymi lokalnie? Serwer proxy aplikacji jest rozwiązaniem. Mogą dodawać aplikacje publikowane za pośrednictwem serwera proxy aplikacji do swoich kanałów przy użyciu tych samych zewnętrznych adresów URL, których zawsze używają do zdalnego uzyskiwania dostępu do swoich aplikacji. A ponieważ serwer proxy aplikacji uwierzytelnia się za pośrednictwem usługi Azure Active Directory, użytkownicy otrzymują środowisko logowania jednokrotnego.

## <a name="install-the-application-proxy-connector-and-publish-your-app"></a>Instalowanie łącznika serwera proxy aplikacji i publikowanie aplikacji

Jeśli jeszcze tego nie zrobiłeś, [skonfiguruj serwer proxy aplikacji dla dzierżawy i zainstaluj łącznik](application-proxy-add-on-premises-application.md). Następnie [opublikuj lokalną aplikację](application-proxy-add-on-premises-application.md) dostępu zdalnego. Podczas publikowania aplikacji zanotuj zewnętrzny adres URL, ponieważ jest on używany do dodawania aplikacji do aplikacji Teams.

Jeśli aplikacje zostały już opublikowane, ale nie pamiętasz ich zewnętrznych adresów URL, poszukaj ich w [witrynie Azure portal](https://portal.azure.com). Zaloguj się, a następnie przejdź do**aplikacji** >  **Azure Active Directory** > Enterprise**Wszystkie aplikacje** > wybrać aplikację > **proxy aplikacji**.

## <a name="add-your-app-to-teams"></a>Dodawanie aplikacji do aplikacji Teams

Po opublikowaniu aplikacji za pośrednictwem serwera proxy aplikacji poinformuj użytkowników, że mogą dodać ją jako kartę bezpośrednio w swoich kanałach usługi Teams, a następnie aplikacja jest dostępna dla wszystkich w zespole do użycia. Poproś ich, aby postępowali zgodnie z tymi trzema krokami:

1. Przejdź do kanału Zespoły, w którym **+** chcesz dodać tę aplikację, a następnie wybierz, aby dodać kartę.

   ![Wybierz + , aby dodać kartę w aplikacji Teams](./media/application-proxy-integrate-with-teams/add-tab.png)

1. Wybierz **pozycję Witryna sieci Web** z opcji karty.

   ![Wybieranie witryny sieci Web na ekranie karty Dodawanie](./media/application-proxy-integrate-with-teams/website.png)

1. Nadaj karcie nazwę i ustaw adres URL zewnętrznego adresu URL serwera proxy aplikacji.

   ![Nadaj karcie nazwę i dodaj zewnętrzny adres URL](./media/application-proxy-integrate-with-teams/tab-name-url.png)

Gdy jeden z członków zespołu doda kartę, jest ona wyświetlana wszystkim w kanale. Wszyscy użytkownicy, którzy mają dostęp do aplikacji, uzyskują dostęp do logowania jednokrotnego z poświadczeniami używanymi przez nich w usłudze Microsoft Teams. Wszyscy użytkownicy, którzy nie mają dostępu do aplikacji, mogą zobaczyć kartę w aplikacji Teams, ale są blokowani, dopóki nie przyznasz im uprawnień do aplikacji lokalnej i opublikowanej wersji aplikacji w portalu Azure.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [publikować lokalne witryny programu SharePoint](application-proxy-integrate-with-sharepoint-server.md) za pomocą serwera proxy aplikacji.
- Skonfiguruj aplikacje tak, aby [używały domen niestandardowych](application-proxy-configure-custom-domain.md) dla ich zewnętrznego adresu URL.
