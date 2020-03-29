---
title: Włączanie logowania jednokrotnego dla aplikacji wielodostępnej
description: Wskazówki dla niezależnych dostawców oprogramowania dotyczące integracji z usługą Azure active Directory
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
ms.openlocfilehash: 4c89a83ade6305579e700afb86f0b9e3aca2695e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67795178"
---
# <a name="enable-single-sign-on-for-your-multi-tenant-application"></a>Włączanie logowania jednokrotnego dla aplikacji wielodostępnych  

Jeśli oferujesz aplikację do użytku przez inne firmy za pośrednictwem zakupu lub subskrypcji, można udostępnić aplikację klientom w ramach ich własnych dzierżaw platformy Azure. Jest to nazywane tworzeniem aplikacji wielodostępnych. Aby zapoznać się z omówieniem tej koncepcji, zobacz [Aplikacje wielodostępne na platformie Azure](https://docs.microsoft.com/azure/dotnet-develop-multitenant-applications) i [najem w usłudze Azure Active Directory](../develop/single-and-multi-tenant-apps.md).

## <a name="what-is-single-sign-on"></a>Co to jest logowanie jednokrotne

Logowanie jednokrotne (Logowanie jednokrotne) zwiększa bezpieczeństwo i wygodę, gdy użytkownicy logują się do aplikacji przy użyciu usługi Azure Active Directory i innych tożsamości. Gdy aplikacja jest włączona jako logowania do logowania, użytkownicy nie trzeba wprowadzać oddzielnych poświadczeń, aby uzyskać dostęp do tej aplikacji. Aby uzyskać pełne wyjaśnienie logowania jednokrotnego. [Zobacz: Logowanie jednokrotne w usłudze Azure Active Directory](what-is-single-sign-on.md).

## <a name="why-enable-single-sign-on-in-your-application"></a>Dlaczego warto włączyć logowanie jednokrotne w aplikacji?

Istnieje wiele zalet włączania sso w aplikacji wielodostępnych. Po włączeniu syt lubi...

* Aplikacja może być wyświetlana w portalu Azure Marketplace, gdzie aplikacja jest wykrywalna przez miliony organizacji korzystających z usługi Azure Active Directory.
  * Umożliwia klientom szybkie konfigurowanie aplikacji za pomocą usługi Azure AD.

* Aplikację można odnajdować w Galerii aplikacji usługi Office 365, programie Office 365 App Launcher oraz w usłudze Microsoft Search w Office.com

* Aplikacja może używać interfejsu API REST programu Microsoft Graph, aby uzyskać dostęp do danych, które zwiększają produktywność użytkownika, która jest dostępna w programie Microsoft Graph.

* Zmniejszasz koszty pomocy technicznej, ułatwiając klientom.
  * Dokumentacja specyficzna dla aplikacji współtworzyła z zespołem usługi Azure AD dla naszych wspólnych klientów ułatwia przyjęcie.
  * Jeśli jedno kliknięcie jest włączone, administratorzy IT klientów nie muszą uczyć się konfigurowania aplikacji do użytku w organizacji.

* Zapewniasz swoim klientom możliwość całkowitego zarządzania uwierzytelnianiem i autoryzacją tożsamości pracowników i gości.

  * Umieszczenie wszystkich odpowiedzialność za zarządzanie kontem i zgodność z klientem tych tożsamości.

  * Zapewnienie możliwości włączania lub wyłączania funkcji SSO dla określonych dostawców tożsamości, grup lub użytkowników w celu zaspokojenia ich potrzeb biznesowych.

* Zwiększasz zbywalność i adoewalność. Wiele dużych organizacji wymaga, aby ich pracownicy mieli bezproblemowe środowisko logowania jednokrotnego we wszystkich aplikacjach. Ułatwienie obsługi SSO jest ważne.

* Zmniejszasz tarcie użytkowników końcowych, co może zwiększyć użycie użytkowników końcowych i zwiększyć przychody.

## <a name="how-to-enable-single-sign-on-in-your-published-application"></a>Jak włączyć logowanie jednokrotne w opublikowanej aplikacji

1. [Wybierz odpowiedni protokół federacyjny dla aplikacji wielodostępnej](isv-choose-multi-tenant-federation.md).
1. Implementowanie sytuowania sytuacyjnego w aplikacji
   - Zobacz [wskazówki dotyczące wzorców uwierzytelniania](../develop/v2-app-types.md)
   - Zobacz [przykłady kodu usługi Azure active Directory](../develop/sample-v2-code.md) dla protokołów OIDC i OAuth
1. [Tworzenie dzierżawy platformy Azure](isv-tenant-multi-tenant-app.md) i testowanie aplikacji
1. [Tworzenie i publikowanie dokumentacji SSO w witrynie](isv-create-sso-documentation.md).
1. [Prześlij listę aplikacji](https://microsoft.sharepoint.com/teams/apponboarding/Apps/SitePages/Default.aspx) i skontaktuj się z firmą Microsoft, aby utworzyć dokumentację w witrynie firmy Microsoft.
1. [Dołącz do sieci partnerów firmy Microsoft (bezpłatnie) i utwórz plan rynkowy.](https://partner.microsoft.com/en-us/explore/commercial#gtm)
