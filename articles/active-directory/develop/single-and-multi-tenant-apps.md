---
title: Aplikacje dla jednej i wielu dzierżawców w usłudze Azure AD
titleSuffix: Microsoft identity platform
description: Dowiedz się więcej o funkcjach i różnicach między aplikacjami z jedną i wieloma dzierżawcami w usłudze Azure AD.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: justhu
ms.custom: aaddev
ms.openlocfilehash: ec59383f9ca2b71ec9f4b6df3ab2e24c6b52473b
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80881369"
---
# <a name="tenancy-in-azure-active-directory"></a>Dzierżawa w usłudze Azure Active Directory

Usługa Azure Active Directory (Azure AD) organizuje obiekty, takie jak użytkownicy i aplikacje, w grupy zwane *dzierżawcami.* Dzierżawcy umożliwiają administratorowi ustawianie zasad dotyczących użytkowników w organizacji i aplikacji, które jest właścicielem organizacji, aby spełnić ich zasady zabezpieczeń i operacyjne. 

## <a name="who-can-sign-in-to-your-app"></a>Kto może zalogować się do aplikacji?

Jeśli chodzi o tworzenie aplikacji, deweloperzy mogą skonfigurować swoją aplikację jako jednodostępną lub wielodostępną podczas rejestracji aplikacji w [witrynie Azure portal.](https://portal.azure.com)
* Aplikacje z jedną dzierżawą są dostępne tylko w dzierżawie, w których zostały zarejestrowane, znane również jako ich dzierżawa domowa.
* Aplikacje dla wielu dzierżawców są dostępne dla użytkowników zarówno w dzierżawie domowej, jak i w innych dzierżawach.

W witrynie Azure portal można skonfigurować aplikację jako pojedynczej dzierżawy lub wielu dzierżawców, ustawiając odbiorców w następujący sposób.

| Grupy odbiorców | Pojedynczy/wielodostępny | Kto może się zalogować | 
|----------|--------| ---------|
| Tylko konta w tym katalogu | Pojedyncza dzierżawa | Wszystkie konta użytkowników i gości w katalogu można użyć aplikacji lub interfejsu API.<br>*Użyj tej opcji, jeśli grupa docelowa jest wewnętrzna w Organizacji.* |
| Konta w dowolnym katalogu usługi Azure AD | Wiele dzierżaw | Wszyscy użytkownicy i goście posiadający konto służbowe firmy Microsoft mogą korzystać z aplikacji lub interfejsu API. Dotyczy to szkół i firm korzystających z usługi Office 365.<br>*Użyj tej opcji, jeśli grupą docelową są klienci biznesowi lub edukacyjni.* |
| Konta w dowolnym katalogu usługi Azure AD i osobistych kontach Microsoft (takich jak Skype, Xbox, Outlook.com) | Wiele dzierżaw | Wszyscy użytkownicy z pracą lub szkołą lub osobistym kontem Microsoft mogą korzystać z aplikacji lub interfejsu API. Obejmuje szkoły i firmy korzystające z usługi Office 365, a także konta osobiste, które są używane do logowania się do usług, takich jak Xbox i Skype.<br>*Ta opcja służy do kierowania na najszerszy zestaw kont Microsoft.* | 

## <a name="best-practices-for-multi-tenant-apps"></a>Najważniejsze wskazówki dotyczące aplikacji z wieloma dzierżawami

Tworzenie doskonałych aplikacji z wieloma dzierżawcami może być trudne ze względu na liczbę różnych zasad, które administratorzy IT mogą ustawić w swoich dzierżawach. Jeśli zdecydujesz się utworzyć aplikację z wieloma dzierżawami, postępuj zgodnie z tymi najlepszymi rozwiązaniami:

* Przetestuj aplikację w dzierżawie, która skonfigurowała [zasady dostępu warunkowego](../azuread-dev/conditional-access-dev-guide.md).
* Postępuj zgodnie z zasadą najmniejszego dostępu użytkowników, aby upewnić się, że aplikacja żąda tylko uprawnień, których faktycznie potrzebuje. Unikaj żądania uprawnień, które wymagają zgody administratora, ponieważ może to uniemożliwić użytkownikom pobieranie aplikacji w ogóle w niektórych organizacjach. 
* Podaj odpowiednie nazwy i opisy dla wszelkich uprawnień, które udostępniasz w ramach aplikacji. Dzięki temu użytkownicy i administratorzy wiedzą, na co się zgadzają podczas próby użycia interfejsów API aplikacji. Aby uzyskać więcej informacji, zobacz sekcję najważniejsze wskazówki w [przewodniku po uprawnieniach](v2-permissions-and-consent.md).

## <a name="next-steps"></a>Następne kroki

* [Jak przekonwertować aplikację na wielodostępną](howto-convert-app-to-be-multi-tenant.md)
