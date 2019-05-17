---
title: Nieoczekiwany błąd podczas wyrażania zgody dla aplikacji | Dokumentacja firmy Microsoft
description: W tym artykule omówiono błędy, które mogą wystąpić w trakcie procesu, który integruję aplikację i co można zrobić na ich temat
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
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2019
ms.locfileid: "65824943"
---
# <a name="unexpected-error-when-performing-consent-to-an-application"></a>Nieoczekiwany błąd podczas wyrażania zgody dla aplikacji

W tym artykule omówiono błędy, które mogą wystąpić w trakcie procesu wyrażanie zgody na aplikację. Jeśli rozwiązujesz monity nieoczekiwany zgody, które nie zawierają komunikaty o błędach, zobacz [scenariusze uwierzytelniania dla usługi Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-scenarios).

Wiele aplikacji, które integrują się z usługą Azure Active Directory wymaga uprawnień do dostępu do innych zasobów, aby funkcjonować. Podczas tych zasobów są także zintegrowana z usługą Azure Active Directory, uprawnienia dostępu do nich jest często żądana za pomocą wspólnego platformy wyrażania zgody. Zostanie wyświetlony monit zgody, która zwykle pojawia się po raz pierwszy aplikacji jest używany, ale może również wystąpić na późniejsze użycie aplikacji.

Określone warunki muszą być spełnione, użytkownik może wyrazić zgodę na uprawnienia, których wymaga aplikacja. Jeśli te warunki nie są spełnione, mogą wystąpić następujące błędy.

## <a name="requesting-not-authorized-permissions-error"></a>Błąd uprawnień nie Autoryzowano żądania
* **AADSTS90093:** &lt;clientAppDisplayName&gt; żąda co najmniej jednego uprawnienia, które są nie uprawnień do udzielania. Skontaktuj się z administratorem, który mogą wyrazić zgodę na tę aplikację w Twoim imieniu.

Ten błąd występuje, gdy użytkownik, który nie jest administratorem firmy podejmują próbę użycia aplikacji, która żąda uprawnienia, których może udzielić tylko administrator. Ten błąd można rozwiązać, administrator udzielanie dostępu do aplikacji w imieniu swojej organizacji.

## <a name="policy-prevents-granting-permissions-error"></a>Zasady uniemożliwiają, udzielanie uprawnień błąd
* **AADSTS90093:** Administrator &lt;tenantDisplayName&gt; ustawił zasadę, która uniemożliwia udzielanie &lt;Nazwa aplikacji&gt; uprawnień żąda. Skontaktuj się z administratorem &lt;tenantDisplayName&gt;, kto może nadać uprawnienia do tej aplikacji w Twoim imieniu.

Ten błąd występuje, gdy administrator firmy wyłącza możliwości użytkownikom na wyrażanie zgody dla aplikacji, a następnie próbuje użyć aplikacji, która wymaga zgody użytkownika bez uprawnień administratora. Ten błąd można rozwiązać, administrator udzielanie dostępu do aplikacji w imieniu swojej organizacji.

## <a name="intermittent-problem-error"></a>Błąd sporadyczny problem
* **AADSTS90090:** Wygląda na proces logowania napotkał sporadyczny problem rejestrowanie uprawnienia próbujesz udzielić &lt;clientAppDisplayName&gt;. Spróbuj ponownie później.

Ten błąd wskazuje, że wystąpił problem po stronie usługi sporadyczne. Można go rozwiązać, próbując do wyrażenia zgody na aplikację ponownie.

## <a name="resource-not-available-error"></a>Zasób błąd nie jest dostępna
* **AADSTS65005:** Aplikacja &lt;clientAppDisplayName&gt; żądane uprawnienia dostępu do zasobu &lt;resourceAppDisplayName&gt; które nie są dostępne. 

Skontaktuj się z deweloperem aplikacji.

##  <a name="resource-not-available-in-tenant-error"></a>Zasób nie jest dostępny w wyniku błędu dzierżawy
* **AADSTS65005:** &lt;clientAppDisplayName&gt; żąda dostępu do zasobu &lt;resourceAppDisplayName&gt; które nie są dostępne w Twojej organizacji &lt;tenantDisplayName &gt;. 

Upewnij się, że ten zasób jest dostępny, lub skontaktuj się z administratorem &lt;tenantDisplayName&gt;.

## <a name="permissions-mismatch-error"></a>Błąd niezgodności uprawnień
* **AADSTS65005:** Aplikacja wymagane zgody na dostęp do zasobów &lt;resourceAppDisplayName&gt;. To żądanie nie powiodło się, ponieważ nie jest zgodny, jak aplikacja została wstępnie skonfigurowane podczas rejestracji aplikacji. Skontaktuj się z aplikacji vendor.* *

Te błędy, które wszystkie występują, gdy aplikacji użytkownik próbuje się do wyrażenia zgody na żąda uprawnienia dostępu do aplikacji zasobu, którego nie można znaleźć w katalogu organizacji (dzierżawa). Taka sytuacja może wystąpić z kilku powodów:

-   Deweloper aplikacji klienta ma ich stosowania nieprawidłowo skonfigurowane, powodowania umożliwia zażądanie dostępu do nieprawidłowych zasobów. W tym przypadku Deweloper aplikacji musi zaktualizować konfigurację aplikacji klienta, aby rozwiązać ten problem.

-   Jednostki usługi, reprezentujący zasób aplikacji docelowej nie istnieje w danej organizacji lub istniał w przeszłości, ale została usunięta. Aby rozwiązać ten problem, jednostkę usługi, dla której aplikacja zasobu musi być obsługiwana w organizacji, dzięki czemu aplikacja kliencka może zażądać uprawnień do niego. Nazwa główna usługi mogą być udostępniane na wiele sposobów, w zależności od typu aplikacji, w tym:

    -   Uzyskiwanie subskrypcji dla zasobów aplikacji (Microsoft opublikowane aplikacje)

    -   Wyrażanie zgody na aplikacji zasobów.

    -   Udzielanie uprawnień aplikacji w witrynie Azure portal

    -   Dodawanie aplikacji z galerii aplikacji usługi Azure AD

## <a name="next-steps"></a>Kolejne kroki 

[Aplikacje, uprawnienia i zgody w usłudze Azure Active Directory (punkt końcowy v1)](https://docs.microsoft.com/azure/active-directory/active-directory-apps-permissions-consent)<br>

[Zakresy, uprawnienia i zgody w usłudze Azure Active Directory (punktu końcowego v2.0)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)


