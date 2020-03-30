---
title: Tworzenie & publikowanie dokumentacji logowania jednokrotnego dla aplikacji
description: Wskazówki dotyczące integracji z usługą Azure Active Directory dla niezależnych dostawców oprogramowania
services: active-directory
author: barbaraselden
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 05/22/2019
ms.author: baselden
ms.reviewer: jeeds
ms.collection: M365-identity-device-management
ms.openlocfilehash: cb223ec8ab7b5c053136c78d3b4ca30ad4da4e18
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74232281"
---
# <a name="create-and-publish-single-sign-on-documentation-for-your-application"></a>Tworzenie i publikowanie dokumentacji logowania jednokrotnego dla aplikacji   

## <a name="documentation-on-your-site"></a>Dokumentacja na twojej stronie

Łatwość wdrażania jest istotnym czynnikiem w podejmowaniu decyzji dotyczących oprogramowania dla przedsiębiorstw. Przejrzysta, łatwa do naśladowania dokumentacja wspiera klientów w ich podróży adopcyjnej i zmniejsza koszty pomocy technicznej. Współpracując z tysiącami dostawców oprogramowania, Microsoft widział, co działa.

Zaleca się, aby dokumentacja w witrynie zawierała co najmniej następujące elementy.

* Wprowadzenie do funkcji SSO

  * Obsługiwane protokoły

  * Wersja i jednostka SKU

  * Lista obsługiwanych dostawców tożsamości z łączami dokumentacji

* Informacje o licencjonowaniu aplikacji

* Kontrola dostępu oparta na rolach do konfigurowania usługi SSO

* Kroki konfiguracji szosyfikacyjne

  * Elementy konfiguracji interfejsu użytkownika dla SAML z oczekiwanymi wartościami od dostawcy

  * Informacje o dostawcy usług, które mają być przekazywane dostawcom tożsamości

* Jeśli OIDC/OAuth

  * Lista uprawnień wymaganych do uzyskania zgody z uzasadnieniami biznesowymi

* Etapy testowania dla użytkowników pilotażowych

* Informacje dotyczące rozwiązywania problemów, w tym kody błędów i komunikaty

* Mechanizmy wsparcia dla klientów

## <a name="documentation-on-the-microsoft-site"></a>Dokumentacja w witrynie firmy Microsoft

Po wyświetleniu listy aplikacji z galerią aplikacji usługi Azure Active Directory, która również publikuje aplikację w portalu Azure Marketplace, firma Microsoft wygeneruje dokumentację dla naszych wspólnych klientów wyjaśniającą proces krok po kroku. Możesz zobaczyć przykład [tutaj](https://aka.ms/appstutorial). Ta dokumentacja jest tworzona na podstawie przesłania do galerii i można ją łatwo zaktualizować, jeśli wprowadzasz zmiany w aplikacji przy użyciu konta GitHub.

## <a name="next-steps"></a>Następne kroki

[Wyświetlanie listy aplikacji w galerii aplikacji usługi Azure AD](https://microsoft.sharepoint.com/teams/apponboarding/Apps/SitePages/Default.aspx)