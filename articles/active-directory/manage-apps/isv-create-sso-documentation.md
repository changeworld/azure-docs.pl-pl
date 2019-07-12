---
title: Tworzenie i publikowanie dokumentacji pojedynczego logowania jednokrotnego dla aplikacji
description: Wskazówki dla niezależnych dostawców oprogramowania na integrację z usługą Azure Active Directory
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
ms.openlocfilehash: 53221343ac606b6076cc9cc3cff6e0f96c1a3ac3
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/08/2019
ms.locfileid: "67659595"
---
# <a name="create-and-publish-single-sign-on-documentation-for-your-application"></a>Tworzenie i publikowanie dokumentacji pojedynczego logowania jednokrotnego dla aplikacji   

## <a name="documentation-on-your-site"></a>Dokumentacja w witrynie

Łatwość wdrażania jest ważnym czynnikiem decyzje dotyczące oprogramowania w przedsiębiorstwie. Dokumentacja wyczyść łatwe do wykonania obsługuje klientów w ich przebiegu wdrożenia i obniża koszty pomocy technicznej. Praca z tysiącami dostawców oprogramowania, Microsoft obserwowała, jak to, co działa.

Zaleca się, że do dokumentacji w witrynie co najmniej obejmują następujące elementy.

* Wprowadzenie do funkcji usługi logowania jednokrotnego

  * Obsługiwane protokoły

  * Wersja i jednostki SKU

  * Listy obsługiwanych dostawców tożsamości za pomocą linków do dokumentacji

* Informacje o licencjonowaniu dla aplikacji

* Kontrola dostępu oparta na rolach do konfigurowania logowania jednokrotnego

* Kroki konfiguracji logowania jednokrotnego

  * Elementy konfiguracji interfejsu użytkownika dla języka SAML z oczekiwanych wartości od dostawcy

  * Informacje o dostawcy usług mają być przekazywane do dostawcy tożsamości

* Jeśli OIDC/OAuth

  * Lista uprawnień wymaganych do wyrażania zgody o uzasadnienie biznesowe

* Kroki dla użytkowników pilotażowych testowania

* Informacje dotyczące rozwiązywania problemów, w tym kody błędów i komunikaty

* Mechanizmy obsługi dla klientów

## <a name="documentation-on-the-microsoft-site"></a>Dokumentacja w witrynie firmy Microsoft

Po wyświetleniu listy aplikacji za pomocą usługi Azure Active Directory aplikacji galerii, która aplikacji są również publikowane w witrynie Azure Marketplace, Microsoft wygeneruje dokumentację dla naszych wspólnych klientów wyjaśniające, krok po kroku procesu. Przykładowy wynik można obejrzeć [tutaj](https://aka.ms/appstutorial). Ta dokumentacja jest oparta na swoje zgłoszenie do galerii i można łatwo zaktualizować go w przypadku wprowadzenia zmian do aplikacji przy użyciu konta usługi GitHub.

## <a name="next-steps"></a>Następne kroki

[Umieść swoją aplikację w galerii aplikacji usługi Azure AD](https://microsoft.sharepoint.com/teams/apponboarding/Apps/SitePages/Default.aspx)