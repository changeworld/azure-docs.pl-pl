---
title: Konfigurowanie wymagań dotyczących złożoności hasła
titleSuffix: Azure AD B2C
description: Jak skonfigurować wymagania dotyczące złożoności haseł dostarczanych przez konsumentów w usłudze Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c5ef550af0c7e19531ea19093ea937880f7dcf14
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78185645"
---
# <a name="configure-complexity-requirements-for-passwords-in-azure-active-directory-b2c"></a>Konfigurowanie wymagań dotyczących złożoności haseł w usłudze Azure Active Directory B2C

Usługa Azure Active Directory B2C (Azure AD B2C) obsługuje zmianę wymagań dotyczących złożoności haseł dostarczanych przez użytkownika końcowego podczas tworzenia konta. Domyślnie usługa Azure AD B2C używa `Strong` haseł. Usługa Azure AD B2C obsługuje również opcje konfiguracji, aby kontrolować złożoność haseł, których klienci mogą używać.

## <a name="password-rule-enforcement"></a>Wymuszanie reguł haseł

Podczas rejestracji lub resetowania hasła użytkownik końcowy musi podać hasło spełniające reguły złożoności. Reguły złożoności haseł są wymuszane na przepływ użytkownika. Możliwe jest, że jeden przepływ użytkownika wymaga czterocyfrowego kodu PIN podczas rejestracji, podczas gdy inny przepływ użytkownika wymaga ciągu ośmiu znaków podczas rejestracji. Na przykład można użyć przepływu użytkownika z inną złożonością hasła dla dorosłych niż dla dzieci.

Złożoność hasła nigdy nie jest wymuszana podczas logowania. Użytkownicy nigdy nie są monitowani podczas logowania, aby zmienić swoje hasło, ponieważ nie spełnia bieżącego wymagania złożoności.

Złożoność hasła można skonfigurować w następujących typach przepływów użytkownika:

- Przepływ użytkownika rejestracji lub logowania
- Przepływ użytkownika resetowania hasła

Jeśli używasz zasad niestandardowych, możesz ([skonfiguruj złożoność hasła w zasadach niestandardowych](custom-policy-password-complexity.md)).

## <a name="configure-password-complexity"></a>Konfigurowanie złożoności hasła

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
2. Wybierz ikonę **Katalog + Subskrypcja** na pasku narzędzi portalu, a następnie wybierz katalog zawierający dzierżawę usługi Azure AD B2C.
3. W witrynie Azure portal wyszukaj i wybierz **pozycję Azure AD B2C**.
4. Wybierz **przepływy użytkownika (zasady)**.
2. Wybierz przepływ użytkownika i kliknij pozycję **Właściwości**.
3. W obszarze **Złożoność hasła**zmień złożoność hasła dla tego przepływu użytkownika na **Prosty,** **Silny**lub **Niestandardowy**.

### <a name="comparison-chart"></a>Porównanie

| Złożoność | Opis |
| --- | --- |
| Proste | Hasło o masie co najmniej od 8 do 64 znaków. |
| Silna | Hasło o masie co najmniej od 8 do 64 znaków. Wymaga 3 z 4 małych liter, wielkich, liczb lub symboli. |
| Niestandardowy | Ta opcja zapewnia największą kontrolę nad regułami złożoności hasła.  Umożliwia skonfigurowanie niestandardowej długości.  Umożliwia również akceptowanie haseł tylko numerów (pinów). |

## <a name="custom-options"></a>Opcje niestandardowe

### <a name="character-set"></a>Zestaw znaków

Umożliwia akceptowanie tylko cyfr (pinów) lub pełnego zestawu znaków.

- **Liczby zezwalają tylko** na cyfry (0-9) podczas wprowadzania hasła.
- **Wszystko** pozwala na dowolną literę, cyfrę lub symbol.

### <a name="length"></a>Długość

Umożliwia kontrolowanie wymagań dotyczących długości hasła.

- **Minimalna długość** musi wynosić co najmniej 4.
- **Maksymalna długość** musi być większa lub równa minimalnej długości i co najwyżej może wynosić 64 znaki.

### <a name="character-classes"></a>Klasy znaków

Umożliwia sterowanie różnymi typami znaków używanymi w haśle.

- **2 z 4: Znak małych liter, Znak wielkich liter, Liczba (0-9), Symbol zapewnia,** że hasło zawiera co najmniej dwa typy znaków. Na przykład liczba i małe litery.
- **3 z 4: Znak małych liter, Znak wielkich liter, Liczba (0-9), Symbol zapewnia,** że hasło zawiera co najmniej trzy typy znaków. Na przykład liczba, mały znak i znak wielkim.
- **4 z 4: Znak małych liter, Znak wielkich liter, Liczba (0-9), Symbol** zapewnia, że hasło zawiera wszystkie typy znaków.

    > [!NOTE]
    > Wymaganie **4 z 4** może spowodować frustrację użytkownika końcowego. Niektóre badania wykazały, że wymóg ten nie poprawia entropii haseł. Zobacz [Wskazówki dotyczące haseł NIST](https://pages.nist.gov/800-63-3/sp800-63b.html#appA)
