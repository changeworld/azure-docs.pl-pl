---
title: Włączanie logowania jednokrotnego dla aplikacji wielodostępnych
description: Wskazówki dla niezależnych dostawców oprogramowania na integrację z usługą Azure active Directory
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
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67795178"
---
# <a name="enable-single-sign-on-for-your-multi-tenant-application"></a>Włącz logowanie jednokrotne dla aplikacji wielodostępnych  

Gdy możesz zaoferować swoją aplikację do użytku przez inne firmy za pośrednictwem zakupu lub subskrypcji, możesz udostępnić aplikację dla klientów w ich własnych dzierżaw usługi Azure. Jest to nazywane tworzenia aplikacji z wieloma dzierżawami. Aby zapoznać się z omówieniem tego pojęcia, zobacz [wielodostępnej aplikacji na platformie Azure](https://docs.microsoft.com/azure/dotnet-develop-multitenant-applications) i [dzierżawy usługi Azure Active Directory](../develop/single-and-multi-tenant-apps.md).

## <a name="what-is-single-sign-on"></a>Co to jest logowanie jednokrotne

Logowanie jednokrotne (SSO) dodaje zabezpieczenia i wygodę, gdy użytkownicy zalogować się przy użyciu usługi Azure Active Directory i innych tożsamości do aplikacji. Gdy aplikacja jest włączone logowanie Jednokrotne, użytkownicy nie musieli wprowadzać oddzielnych poświadczeń w celu dostępu do tej aplikacji. Aby uzyskać pełne wyjaśnienie logowania jednokrotnego. [Zobacz logowanie jednokrotne do aplikacji w usłudze Azure Active Directory](what-is-single-sign-on.md).

## <a name="why-enable-single-sign-on-in-your-application"></a>Dlaczego warto włączyć logowanie jednokrotne w aplikacji?

Ma wiele zalet i włączanie logowania jednokrotnego w aplikacji wielodostępnej. Po włączeniu logowania jednokrotnego dla aplikacji:

* Aplikacja może się na liście w portalu Azure Marketplace, w którym Twoja aplikacja jest wykrywane przez miliony organizacji za pomocą usługi Azure Active Directory.
  * Umożliwia klientom szybko skonfigurować aplikację z usługą Azure AD.

* Aplikacja może być wykrywalny, w galerii aplikacji Office 365, uruchamianie aplikacji usługi Office 365 oraz w ramach Microsoft Search w witrynie Office.com

* Aplikacja może używać interfejsu API programu Microsoft Graph REST dostępu do danych wpływającymi na produktywność użytkownika, który jest dostępny z programu Microsoft Graph.

* Można zmniejszyć koszty pomocy technicznej, ułatwiając dla swoich klientów.
  * Dokumentacja specyficzne dla aplikacji coproduced z zespołem usługi Azure AD dla naszych wspólnych klientów ułatwia wdrożenie.
  * Jeśli usługa rejestracji Jednokrotnej w jednym kliknięciem jest włączona, Administratorzy systemów informatycznych klientów nie trzeba Dowiedz się, jak skonfigurować aplikację do użytku w organizacji.

* Udostępnianie klientom możliwość zarządzania uwierzytelnianie i autoryzacja w swoich pracowników i gości tożsamości.

  * Wprowadzenie do wszystkich kont zarządzania i zgodności odpowiedzialność z właścicielem odbiorcy tych tożsamości.

  * Zapewniając możliwość Włączanie lub wyłączanie logowania jednokrotnego dla dostawców tożsamości określonej, grupy lub użytkowników w celu zaspokojenia swoich potrzeb biznesowych.

* Można zwiększyć swoje zbywalności i adoptability. W wielu dużych organizacjach wymagają, aby (lub Oczekuj) ich pracownicy mają bezproblemowego logowania jednokrotnego środowiska we wszystkich aplikacjach. Dzięki rejestracji Jednokrotnej można łatwo jest ważne.

* Można zmniejszyć liczbę problemów użytkowników końcowych, co może zwiększyć użycie przez użytkownika końcowego i zwiększyć swoje przychody.

## <a name="how-to-enable-single-sign-on-in-your-published-application"></a>Jak włączyć logowanie jednokrotne w opublikowanej aplikacji

1. [Wybierz protokół Federacji odpowiednie dla twojej aplikacji wielodostępnej](isv-choose-multi-tenant-federation.md).
1. Implementowanie logowania jednokrotnego w aplikacji
   - Zobacz [wskazówki na temat wzorców uwierzytelniania](../develop/v2-app-types.md)
   - Zobacz [active Directory przykłady kodu usługi Azure](../develop/sample-v2-code.md) protokołów OIDC i OAuth
1. [Tworzenie dzierżawy usługi Azure](isv-tenant-multi-tenant-app.md) i testować swoją aplikację
1. [Tworzenie i publikowanie logowania jednokrotnego dokumentacji w witrynie](isv-create-sso-documentation.md).
1. [Prześlij swoje listy aplikacji](https://microsoft.sharepoint.com/teams/apponboarding/Apps/SitePages/Default.aspx) i jej partnerów z firmą Microsoft, aby tworzyć dokumentację w witrynie firmy Microsoft.
1. [Dołącz do programu Microsoft Partner Network (wersja bezpłatna) i Utwórz swoje go na rynek plan](https://partner.microsoft.com/en-us/explore/commercial#gtm).
