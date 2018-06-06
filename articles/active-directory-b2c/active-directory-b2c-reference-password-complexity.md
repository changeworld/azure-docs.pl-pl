---
title: Złożoność hasła w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Jak skonfigurować wymagania dotyczące złożoności haseł dostarczonych przez użytkowników w usłudze Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 08/16/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 617385bec29e2c09ae18bde3a7878624c0b2461f
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34709839"
---
# <a name="azure-ad-b2c-configure-complexity-requirements-for-passwords"></a>Usługa Azure AD B2C: Konfigurowanie wymagań dotyczących złożoności haseł

> [!NOTE]
> **Ta funkcja jest dostępna w publicznej wersji zapoznawczej.**

Usługa Azure Active Directory B2C (Azure AD B2C) obsługuje zmienianie wymagania dotyczące złożoności haseł dostarczone przez użytkownika końcowego podczas tworzenia konta.  Domyślnie używa usługi Azure AD B2C `Strong` hasła.  Usługa Azure AD B2C obsługuje również opcje kontroli złożoności haseł, których klienci mogą używać konfiguracji.

## <a name="when-password-rules-are-enforced"></a>Wymuszenie zasad haseł

Podczas tworzenia konta lub resetowania hasła, użytkownik końcowy musi podać hasło, które spełnia reguł złożoności.  Reguł złożoności haseł są wymuszane dla poszczególnych zasad.  Istnieje możliwość jedne zasady wymagają czterocyfrowy kod pin podczas tworzenia konta podczas inne zasady wymaga ciąg osiem znaków podczas tworzenia konta.  Na przykład może użyć zasad z złożoności inne hasło dla dorosłych niż dla dzieci.

Złożoność hasła nigdy nie są wymuszane podczas logowania.  Użytkownicy nigdy nie są monitowani podczas logowania do zmiany hasła, ponieważ nie spełnia bieżące wymagania co do złożoności.

Poniżej przedstawiono typy zasad, którym można skonfigurować złożoności hasła:

* Zasady rejestracji i logowania
* Zasady resetowania hasła
* Zasady niestandardowe ([skonfigurowania złożoności hasła w zasadach niestandardowych](active-directory-b2c-reference-password-complexity-custom.md))

## <a name="how-to-configure-password-complexity"></a>Jak skonfigurować złożoność hasła

1. Wykonaj następujące kroki, aby [przejdź do ustawień usługi Azure AD B2C](active-directory-b2c-app-registration.md#navigate-to-b2c-settings).
1. Otwórz **rejestracji i logowania zasady**.
1. Wybierz zasady, a następnie kliknij przycisk **Edytuj**.
1. Otwórz **złożoności hasła**.
1. Złożoność hasła dla tych zasad, aby zmienić **proste**, **silne**, lub **niestandardowy**.

### <a name="comparison-chart"></a>Wykres porównawczy

| Złożoność | Opis |
| --- | --- |
| Proste | Hasło składające się z co najmniej 8 do 64 znaków. |
| Silna | Hasło składające się z co najmniej 8 do 64 znaków. Wymaga to 3 z 4 małe litery, wielkie litery, cyfry i symbole. |
| Niestandardowy | Ta opcja zapewnia najwyższy poziom kontroli nad reguł złożoności haseł.  Umożliwia konfigurowanie niestandardowych długości.  Umożliwia także akceptuje tylko liczby hasła (PIN). |

## <a name="options-available-under-custom"></a>Opcje dostępne w obszarze niestandardowe

### <a name="character-set"></a>Zestaw znaków

Umożliwia akceptuje tylko cyfry (PIN) lub pełny zestaw znaków.

* **Tylko numery** umożliwia cyfr tylko (0 – 9) podczas wprowadzania hasła.
* **Wszystkie** umożliwia żadnych literą, cyfrą lub symbol.

### <a name="length"></a>Długość

Umożliwia określenie wymagań dotyczących długości hasła.

* **Minimalna długość** musi być co najmniej 4.
* **Maksymalna długość** musi być większa lub równa minimalnej długości i może być co najwyżej 64 znaków.

### <a name="character-classes"></a>Klasy znaków

Umożliwia określenie typów znaków używany w haśle.

* **2 z 4: małą literę, wielką literę, numer (0-9), Symbol** zapewnia hasło zawiera co najmniej dwa typy znaków. Na przykład numer i małą literę.
* **3, 4: małą literę, wielką literę, numer (0-9), Symbol** zapewnia hasło zawiera co najmniej dwa typy znaków. Na przykład numer, małą literę i wielką literę.
* **4 z 4: małą literę, wielką literę, numer (0-9), Symbol** zapewnia hasło zawiera wszystkie dla typów znakowych.

    > [!NOTE]
    > Wymaganie **4 z 4** może spowodować frustracji spowodowanej przez użytkownika końcowego. Niektóre badania wykazały, że to wymaganie nie poprawia entropii hasła. Zobacz [NIST wskazówki dotyczące hasła](https://pages.nist.gov/800-63-3/sp800-63b.html#appA)
