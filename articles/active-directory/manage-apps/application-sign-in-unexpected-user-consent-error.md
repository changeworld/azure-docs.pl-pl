---
title: Nieoczekiwany błąd podczas wykonywania zgody na aplikację | Dokumenty firmy Microsoft
description: W tym artykule omówiono błędy, które mogą wystąpić podczas procesu wyrażania zgody na aplikację i co można z nimi zrobić
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6dff3be9a9bc7fd897f340e5fe6a4775a4914810
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "65824943"
---
# <a name="unexpected-error-when-performing-consent-to-an-application"></a>Nieoczekiwany błąd podczas wykonywania zgody na aplikację

W tym artykule omówiono błędy, które mogą wystąpić podczas procesu wyrażania zgody na aplikację. Jeśli rozwiązujesz problemy z nieoczekiwanymi monitami o zgodę, które nie zawierają żadnych komunikatów o [błędach, zobacz Scenariusze uwierzytelniania dla usługi Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-scenarios).

Wiele aplikacji, które integrują się z usługą Azure Active Directory wymagają uprawnień dostępu do innych zasobów w celu działania. Gdy te zasoby są również zintegrowane z usługą Azure Active Directory, uprawnienia dostępu do nich jest często wymagane przy użyciu wspólnej struktury zgody. Wyświetlany jest monit o zgodę, który zazwyczaj występuje przy pierwszym użyciu aplikacji, ale może również wystąpić przy kolejnym użyciu aplikacji.

Niektóre warunki muszą być spełnione dla użytkownika do wyrażenia zgody na uprawnienia aplikacji wymaga. Jeśli te warunki nie są spełnione, mogą wystąpić następujące błędy.

## <a name="requesting-not-authorized-permissions-error"></a>Żądanie błędu nieautoryzowanych uprawnień
* **AADSTS90093:** &lt;clientAppDisplayName&gt; żąda co najmniej jednego uprawnienia, które nie są autoryzowane do przyznania. Skontaktuj się z administratorem, który może wyrazić zgodę na tę aplikację w Twoim imieniu.

Ten błąd występuje, gdy użytkownik, który nie jest administratorem firmy próbuje użyć aplikacji, która żąda uprawnień, które tylko administrator może udzielić. Ten błąd może zostać rozwiązany przez administratora udzielającego dostępu do aplikacji w imieniu swojej organizacji.

## <a name="policy-prevents-granting-permissions-error"></a>Zasady zapobiegają udzielaniu błędów uprawnień
* **AADSTS90093:** Administrator &lt;tenantDisplayName&gt; ustawił zasadę, która uniemożliwia &lt;przyznanie nazwy&gt; aplikacji, której żąda. Skontaktuj się &lt;z administratorem&gt;tenantDisplayName , który może udzielić uprawnień do tej aplikacji w Twoim imieniu.

Ten błąd występuje, gdy administrator firmy wyłącza możliwość wyrażania zgody użytkowników na aplikacje, a następnie użytkownik niebędący administratorem próbuje użyć aplikacji, która wymaga zgody. Ten błąd może zostać rozwiązany przez administratora udzielającego dostępu do aplikacji w imieniu swojej organizacji.

## <a name="intermittent-problem-error"></a>Przerywany błąd problemu
* **AADSTS90090:** Wygląda na to, że proces logowania napotkał sporadyczny problem rejestrowania uprawnień, które próbowano przyznać &lt;clientAppDisplayName&gt;. spróbuj ponownie później.

Ten błąd wskazuje, że wystąpił sporadyczny problem po stronie usługi. Można go rozwiązać, próbując ponownie wyrazić zgodę na aplikację.

## <a name="resource-not-available-error"></a>Błąd niedostępny zasób
* **AADSTS65005:** ClientappAppDisplayName &lt;&gt; żądanie uprawnień dostępu do &lt;zasobuAppDisplayName,&gt; który jest niedostępny. 

Skontaktuj się z deweloperem aplikacji.

##  <a name="resource-not-available-in-tenant-error"></a>Zasób niedostępny w błędzie dzierżawy
* **AADSTS65005:** &lt;&gt; clientAppDisplayName żąda dostępu do &lt;zasobu ZasobówAppDisplayName,&gt; który &lt;nie jest&gt;dostępny w organizacji tenantDisplayName . 

Upewnij się, że ten zasób jest dostępny lub skontaktuj się z &lt;administratorem tenantDisplayName&gt;.

## <a name="permissions-mismatch-error"></a>Błąd niezgodności uprawnień
* **AADSTS65005:** Aplikacja zażądała zgody na &lt;dostęp do zasobuAppSprzestępczego Programu&gt;. To żądanie nie powiodło się, ponieważ nie jest zgodne, jak aplikacja została wstępnie skonfigurowana podczas rejestracji aplikacji. Skontaktuj się z dostawcą aplikacji.**

Wszystkie te błędy występują, gdy aplikacja, na którą użytkownik próbuje wyrazić zgodę, żąda uprawnień dostępu do aplikacji zasobów, których nie można znaleźć w katalogu organizacji (dzierżawie). Taka sytuacja może wystąpić z kilku powodów:

-   Deweloper aplikacji klienckiej niepoprawnie skonfigurował swoją aplikację, powodując żądanie dostępu do nieprawidłowego zasobu. W takim przypadku deweloper aplikacji musi zaktualizować konfigurację aplikacji klienckiej, aby rozwiązać ten problem.

-   Jednostka usługi reprezentująca aplikację zasobu docelowego nie istnieje w organizacji lub istniała w przeszłości, ale została usunięta. Aby rozwiązać ten problem, jednostka usługi dla aplikacji zasobów musi być aprowizowana w organizacji, aby aplikacja kliencka mogła żądać uprawnień do niej. Podmiot usługi mogą być aprowizowana na wiele sposobów, w zależności od typu aplikacji, w tym:

    -   Pobieranie subskrypcji dla aplikacji zasobów (opublikowane przez firmę Microsoft aplikacje)

    -   Wyrażanie zgody na aplikację zasobów

    -   Udzielanie uprawnień aplikacji za pośrednictwem witryny Azure portal

    -   Dodawanie aplikacji z galerii aplikacji usługi Azure AD

## <a name="next-steps"></a>Następne kroki 

[Aplikacje, uprawnienia i zgoda w usłudze Azure Active Directory (punkt końcowy w wersji 1)](https://docs.microsoft.com/azure/active-directory/active-directory-apps-permissions-consent)<br>

[Zakresy, uprawnienia i zgody w usłudze Azure Active Directory (punkt końcowy w wersji 2.0)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)


