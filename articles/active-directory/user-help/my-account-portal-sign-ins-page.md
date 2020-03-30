---
title: Wyświetlanie i wyszukiwanie ostatniej aktywności logowania na stronie Moje logowanie (wersja zapoznawcza) — Usługa Azure Active Directory | Dokumenty firmy Microsoft
description: Szczegółowe informacje na temat wyświetlania i wyszukiwania ostatniej aktywności związanej z logowaniem można znaleźć na stronie Moje logowania w portalu Moje konto.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: rhicock
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 10/28/2019
ms.author: curtand
ms.openlocfilehash: b68e7b517ddaa9b2aaef00cf87d5b6e63871654b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77064023"
---
# <a name="view-and-search-your-recent-sign-in-activity-from-the-my-sign-ins-preview-page"></a>Wyświetlanie i wyszukiwanie ostatniej aktywności logowania na stronie Moje logowania (podgląd)

Wszystkie ostatnie działania w zakresie logowania do konta lub konta szkolnego można wyświetlić na stronie **Moje logowania w** portalu Moje **konto.** Przeglądanie historii logowania pomaga sprawdzić nietypową aktywność, pomagając ci zobaczyć:

- Jeśli ktoś próbuje odgadnąć Twoje hasło.

- Jeśli osoba atakująca pomyślnie zalogowała się na Twoje konto i z jakiej lokalizacji.

- Do jakich aplikacji osoba atakująca próbowała uzyskać dostęp.

## <a name="view-your-recent-sign-in-activity"></a>Wyświetlanie ostatniej aktywności logowania

1. Zaloguj się na swoje konto służbowe, https://myprofile.microsoft.com/ a następnie przejdź do strony.

2. Z lewego okienka nawigacji wybierz **pozycję Moje logowania (podgląd)** lub wybierz łącze **Przejrzyj ostatnie działania** z bloku Moje logowania **(podgląd).**

    ![Strona Moje konto z wyróżnionymi linkami Do ostatniej aktywności](media/my-account-portal/my-account-portal-sign-ins.png)

3. Rozwiń i przejrzyj każdy z elementów logowania, upewniając się, że rozpoznajesz każdy z nich. Jeśli znajdziesz element logowania, który nie wygląda znajomo, zalecamy zmianę hasła, aby chronić swoje konto, jeśli zostało naruszone.

    ![Strona ostatniej aktywności z rozszerzonymi szczegółami logowania](media/my-account-portal/my-account-portal-sign-ins-page.png)

### <a name="if-you-see-a-successful-sign-in"></a>Jeśli widzisz udane logowanie

Powinieneś rozpoznać swoją własną aktywność jako normalną. Jeśli jednak zauważysz pomyślne zalogowanie się z dziwnej lokalizacji, przeglądarki lub systemu operacyjnego, może to oznaczać, że osoba atakująca uzyskała dostęp do Twojego konta. W tej sytuacji zalecamy natychmiastową zmianę hasła, a następnie przejście do strony [Informacje o zabezpieczeniach,](https://mysignins.microsoft.com/security-info) aby zaktualizować ustawienia zabezpieczeń.

Zanim stwierdzisz, że coś jest nieprawidłowe, upewnij się, że nie widzisz fałszywie dodatniego (gdzie element wygląda wątpliwie, ale jest w porządku). Na przykład określamy twoją przybliżoną lokalizację i mapę na podstawie Twojego adresu IP. Sieci komórkowe są szczególnie trudne do określenia, ponieważ czasami kierują ruch przez odległe miejsca. Jeśli więc zalogujesz się przy użyciu urządzenia mobilnego w stanie Waszyngton, lokalizacja może wyświetlać logowanie pochodzące z Kalifornii. Z tego powodu zdecydowanie zalecamy sprawdzenie więcej szczegółów, poza samą lokalizacją. Należy również upewnić się, że system operacyjny, przeglądarka i aplikacja mają również sens.

### <a name="if-you-see-an-unsuccessful-sign-in"></a>Jeśli widzisz nieudane logowanie

Nieudane logowanie bez aktywności w sesji oznacza, że podstawowa metoda weryfikacji (nazwa użytkownika/hasło) nie powiodła się. Może to oznaczać, że błędnie wpisałeś swoją nazwę użytkownika lub hasło, ale może to również oznaczać, że osoba atakująca próbowała odgadnąć Twoje hasło. Jeśli uważasz, że osoba atakująca próbowała bezskutecznie odgadnąć hasło, nie musisz zmieniać hasła, ale zdecydowanie zalecamy zarejestrowanie się w usłudze Azure Multi-Factor Authentication (MFA). Dzięki uwierzytelniania MFA, nawet jeśli haker w końcu odgadnie Twoje hasło, nie wystarczy, aby uzyskać dostęp do Twojego konta.

Jeśli zostanie wyświetlony nieudane logowanie, z notatką w obszarze Działanie sesji, która mówi, **Dodatkowa weryfikacja nie powiodła się, nieprawidłowy kod**, oznacza to, że uwierzytelnianie podstawowe (nazwa użytkownika/hasło) powiodło się, ale uwierzytelnianie wieloskładnikowe nie powiodło się. Jeśli był to osoba atakująca, poprawnie odgadli hasło, ale nadal nie mogli przejść wyzwania usługi MFA. W takim przypadku zalecamy zmianę hasła, ponieważ osoba atakująca prawo tej części, a następnie przejdź do strony [Informacje o zabezpieczeniach,](https://mysignins.microsoft.com/security-info) aby zaktualizować ustawienia zabezpieczeń.

## <a name="search-for-specific-sign-in-activity"></a>Wyszukiwanie określonych działań logowania

Możesz wyszukać swoją ostatnią aktywność logowania, korzystając z dowolnych dostępnych informacji. Możesz na przykład wyszukać ostatnią aktywność logowania według systemu operacyjnego, lokalizacji, aplikacji itd.

1. Na stronie **Przeglądanie ostatniej aktywności** wpisz informacje, które chcesz wyszukać, na **pasku wyszukiwania.** Na przykład `My Account` wpisz, aby wyszukać wszystkie działania zebrane przez aplikację Moje konto.

2. Wybierz przycisk **Wyszukaj,** aby rozpocząć wyszukiwanie.

    ![Strona Ostatnia aktywność z wyróżnionym paskiem wyszukiwania, przyciskiem wyszukiwania i wynikami](media/my-account-portal/my-account-portal-sign-ins-page-search.png)

## <a name="next-steps"></a>Następne kroki

Po wyświetlenie ostatniej aktywności logowania możesz:

- Wyświetlanie informacji [zabezpieczających](user-help-security-info-overview.md)lub zarządzanie nimi .

- Wyświetlanie [podłączonych urządzeń](my-account-portal-devices-page.md)lub zarządzanie nimi .

- Wyświetlanie [organizacji](my-account-portal-organizations-page.md)lub zarządzanie nimi .

- Zobacz, jak organizacja [wykorzystuje dane związane z prywatnością](my-account-portal-privacy-page.md).
