---
title: Wystąpił nieoczekiwany błąd podczas wykonywania zgody do aplikacji | Dokumentacja firmy Microsoft
description: W tym artykule omówiono błędy, które mogą wystąpić w trakcie procesu zgodę, aplikacja i co można zrobić o nich
services: active-directory
documentationcenter: ''
author: ajamess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: bbc0cee8a44773c025c6174eaf7eccaba81b8d1b
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
ms.locfileid: "26617080"
---
# <a name="unexpected-error-when-performing-consent-to-an-application"></a>Wystąpił nieoczekiwany błąd podczas wykonywania zgody do aplikacji

W tym artykule omówiono błędy, które mogą wystąpić w trakcie procesu zgodę aplikacji. Jeśli jesteś rozwiązywanie nieoczekiwany zgody monity nie zawiera komunikaty o błędach, zobacz [scenariusze uwierzytelniania dla usługi Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-scenarios).

Wiele aplikacji, które integrują się z usługą Azure Active Directory wymaga uprawnienia dostępu do innych zasobów prawidłowego działania. Jeśli te zasoby również są zintegrowane z usługą Azure Active Directory, uprawnienia dostępu do nich jest często żądana za pomocą wspólnej struktury zgody. 

Powoduje to monit o zgodę będzie wyświetlany, którego działanie ma zazwyczaj miejsce po raz pierwszy aplikacja jest używana, ale może również wystąpić w kolejnych za pomocą aplikacji.

Określone warunki musi mieć wartość true dla użytkownika o zgodę na uprawnienia wymagane przez aplikację. Jeśli te warunki nie są spełnione, mogą wystąpić różne błędy. Należą do nich:

## <a name="requesting-not-authorized-permissions-error"></a>Błąd uprawnień nie Autoryzowano żądania
* **AADSTS90093:** &lt;clientAppDisplayName&gt; żąda jedno lub więcej uprawnień, które nie autoryzacji do przyznawania. Skontaktuj się z administratorem, który można wyrazić zgodę na tę aplikację w Twoim imieniu.

Ten błąd występuje, gdy użytkownik, który nie jest administratorem firmy próbuje użyć aplikacji, która żąda uprawnienia, których może udzielić tylko administrator. Ten błąd można rozwiązać przez administratora udzielanie dostępu do aplikacji w imieniu swojej organizacji.

## <a name="policy-prevents-granting-permissions-error"></a>Zasady uniemożliwiają udzielanie uprawnień błąd
* **AADSTS90093:** administrator &lt;tenantDisplayName&gt; ustawił zasadę, która uniemożliwia udzielanie &lt;Nazwa aplikacji&gt; żąda uprawnienia. Skontaktuj się z administratorem &lt;tenantDisplayName&gt;, który można przyznać uprawnień dostępu do tej aplikacji w Twoim imieniu.

Ten błąd występuje, gdy administrator firmy wyłącza przez użytkowników wyrazić zgodę na aplikacje, a następnie użytkownik bez uprawnień administratora próbuje użyć aplikacji, która wymaga zgody. Ten błąd można rozwiązać przez administratora udzielanie dostępu do aplikacji w imieniu swojej organizacji.

## <a name="intermittent-problem-error"></a>Problem tymczasowy błąd
* **AADSTS90090:** prawdopodobnie wystąpił problem tymczasowy rejestrowania próbował udzielać uprawnienia &lt;clientAppDisplayName&gt;. Spróbuj ponownie później.

Ten błąd wskazuje, że wystąpił problem po stronie usługi tymczasowymi. Można można rozwiązać przez próby wyrażenia zgody na ponownie aplikację.

## <a name="resource-not-available-error"></a>Zasób nie jest dostępny błąd
* **AADSTS65005:** aplikacji &lt;clientAppDisplayName&gt; wymagane uprawnienia dostępu do zasobu &lt;resourceAppDisplayName&gt; jest niedostępny. 

Skontaktuj się z deweloperem aplikacji.

##  <a name="resource-not-available-in-tenant-error"></a>Zasób nie jest dostępna w błąd dzierżawy
* **AADSTS65005:** &lt;clientAppDisplayName&gt; żąda dostępu do zasobu &lt;resourceAppDisplayName&gt; które nie są dostępne w Twojej organizacji &lt;tenantDisplayName &gt;. 

Upewnij się, że ten zasób jest dostępny, lub skontaktuj się z administratorem &lt;tenantDisplayName&gt;.

## <a name="permissions-mismatch-error"></a>Błąd niezgodności uprawnień
* **AADSTS65005:** aplikacji wymagane zgody na dostęp do zasobów &lt;resourceAppDisplayName&gt;. To żądanie nie powiodło się, ponieważ nie jest zgodny, jak aplikacja została wstępnie skonfigurowane podczas rejestracji aplikacji. Skontaktuj się z aplikacji vendor.* *

Te błędy, wszystkie wystąpić, gdy aplikacja użytkownik próbuje wyrażenia zgody na żąda uprawnienia dostępu do aplikacji zasobu, którego nie można znaleźć w katalogu organizacji (dzierżawcy). Taka sytuacja może wystąpić z kilku powodów:

-   Deweloper aplikacji klienta ma ich stosowania nieprawidłowo skonfigurowane, powodowania umożliwia zażądanie dostępu do nieprawidłowy zasób. W takim przypadku Deweloper aplikacji musi zaktualizować konfigurację aplikacji klienta, aby rozwiązać ten problem.

-   Nazwy głównej usługi reprezentujących zasobu aplikacji docelowej nie istnieje w organizacji, lub istniał w przeszłości, ale została usunięta. Aby rozwiązać ten problem, nazwy głównej usługi dla aplikacji zasobów należy udostępnić w organizacji, więc aplikacja kliencka może zażądać uprawnień do niego. Może to wystąpić w różne sposoby, w zależności od typu aplikacji, w tym:

    -   Uzyskiwanie subskrypcji dla aplikacji zasobów (Firma Microsoft opublikowała aplikacji)

    -   Zgodę aplikacji zasobów.

    -   Udzielanie uprawnień aplikacji za pośrednictwem portalu Azure

    -   Dodawanie aplikacji z poziomu galerii aplikacji Azure AD

## <a name="next-steps"></a>Kolejne kroki 

[Aplikacje, uprawnienia i zgody w usłudze Azure Active Directory (punkt końcowy v1)](https://docs.microsoft.com/azure/active-directory/active-directory-apps-permissions-consent)<br>

[Zakresy, uprawnienia i zgody w usłudze Azure Active Directory (punktu końcowego v2.0)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)


