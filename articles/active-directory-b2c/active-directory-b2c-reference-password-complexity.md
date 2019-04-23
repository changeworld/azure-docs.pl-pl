---
title: Złożoność hasła — Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Jak skonfigurować wymagania dotyczące złożoności haseł dostarczonych przez klientów w usłudze Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 1d7874d7c8d04e3d3565cdfe2e52e49c538b3091
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59795818"
---
# <a name="configure-complexity-requirements-for-passwords-in-azure-active-directory-b2c"></a>Konfigurowanie wymagań dotyczących złożoności haseł w usłudze Azure Active Directory B2C

Usługa Azure Active Directory (Azure AD) B2C obsługuje zmieniające się wymagania dotyczące złożoności haseł podane przez użytkownika końcowego podczas tworzenia konta. Domyślnie program Azure AD B2C używa `Strong` hasła. Usługa Azure AD B2C obsługuje również opcji konfiguracji służących do kontrolowania złożoności haseł, których klienci mogą używać.

## <a name="password-rule-enforcement"></a>Wymuszanie reguły hasła

Podczas tworzenia konta lub resetowania hasła użytkownika końcowego należy podać hasło, który spełnia reguł złożoności. Reguł złożoności haseł są wymuszane na przepływ użytkownika. Użytkownik może mieć jeden przepływ użytkownika wymagają 4 cyfrowy numer pin, podczas rejestracji podczas inny przepływ użytkownika wymaga ciągu osiem znaków podczas tworzenia konta. Może na przykład użyć przepływu użytkownika za pomocą innego hasła, złożoność dla dorosłych niż dla dzieci.

Złożoność hasła nigdy nie jest wymuszana podczas logowania. Użytkownicy nigdy nie są monitowani podczas logowania do zmiany hasła, ponieważ nie spełnia bieżące wymagania co do złożoności.

Złożoność hasła można skonfigurować w następujących typach przepływów użytkownika:

- Przepływ użytkownika rejestracji lub logowania
- Przepływ użytkownika resetowania hasła

Jeśli używane są zasady niestandardowe, możesz to zrobić ([skonfigurowania złożoności hasła jako w zasadach niestandardowych](active-directory-b2c-reference-password-complexity-custom.md)).

## <a name="configure-password-complexity"></a>Konfigurowanie złożoność hasła

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Upewnij się, że używasz katalogu zawierającego Twoją dzierżawę usługi Azure AD B2C, klikając pozycję **Filtr katalogu i subskrypcji** w górnym menu i wybierając katalog zawierający Twoją dzierżawę.
3. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
4. Wybierz **przepływy użytkownika**.
2. Wybierz przepływ użytkownika, a następnie kliknij przycisk **właściwości**.
3. W obszarze **złożoność hasła jako**, złożoność hasła dla tego przepływu użytkownika, aby zmienić **proste**, **silne**, lub **niestandardowe**.

### <a name="comparison-chart"></a>Wykres porównawczy

| Złożoność | Opis |
| --- | --- |
| Proste | Hasło składające się z co najmniej 8 do 64 znaków. |
| Silna | Hasło składające się z co najmniej 8 do 64 znaków. Wymaga to 3 z 4, małe litery, wielkie litery, cyfry i symbole. |
| Niestandardowy | Ta opcja zapewnia największą kontrolę nad reguł złożoności haseł.  Umożliwia konfigurowanie niestandardowych długości.  Umożliwia także akceptuje tylko liczby haseł (PIN). |

## <a name="custom-options"></a>Niestandardowe opcje

### <a name="character-set"></a>Zestaw znaków

Umożliwia akceptowanie tylko cyfr (PIN) lub pełny zestaw znaków.

- **Tylko numery** umożliwia cyfr tylko (0 – 9), podczas wprowadzania hasła.
- **Wszystkie** umożliwia dowolnym literą, liczbą lub symbol.

### <a name="length"></a>Długość

Umożliwia kontrolowanie wymagania dotyczące długości hasła.

- **Minimalna długość** musi mieć co najmniej 4.
- **Maksymalna długość** musi być większa lub równa minimalnej długości i może być co najwyżej 64 znaków.

### <a name="character-classes"></a>Klasy znaków

Pozwala na kontrolowanie typów znaków użyte w haśle.

- **2 z 4: Małej litery, wielkie litery znaków, liczba (0 – 9), Symbol** zapewnia hasło zawiera co najmniej dwa typy znaków. Na przykład liczbę i małą literę.
- **3 z 4: Małej litery, wielkie litery znaków, liczba (0 – 9), Symbol** zapewnia hasło zawiera co najmniej dwa typy znaków. Na przykład numer małą literę i Wielkiej litery.
- **4 z 4: Małej litery, wielkie litery znaków, liczba (0 – 9), Symbol** zapewnia hasło zawiera wszystkie dla typów znakowych.

    > [!NOTE]
    > Wymaganie **4 z 4** może spowodować Rozczarowanie przez użytkownika końcowego. Niektóre badania wykazały, to wymaganie nie poprawi entropii hasła. Zobacz [wytyczne dotyczące haseł NIST](https://pages.nist.gov/800-63-3/sp800-63b.html#appA)
