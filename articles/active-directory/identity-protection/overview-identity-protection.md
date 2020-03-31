---
title: Co to jest usługa Azure Active Directory Identity Protection?
description: Wykrywanie, korygowanie, badanie i analizowanie ryzyka za pomocą usługi Azure AD Identity Protection
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: overview
ms.date: 03/17/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: d2b1d9748b243dcc2104ce7b8e0e8735a7b7276f
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79497674"
---
# <a name="what-is-azure-active-directory-identity-protection"></a>Co to jest usługa Azure Active Directory Identity Protection?

Ochrona tożsamości to narzędzie, które umożliwia organizacjom wykonywanie trzech kluczowych zadań:

- Automatyzacja wykrywania i korygowania ryzyka opartego na tożsamości.
- Zbadaj zagrożenia przy użyciu danych w portalu.
- Eksportuj dane wykrywania ryzyka do narzędzi innych firm w celu dalszej analizy.

Usługa Identity Protection korzysta z doświadczeń firmy Microsoft uzyskanych z ich pozycji w organizacjach korzystających z usługi Azure AD, przestrzeni konsumenckiej z kontami Microsoft oraz w grach z konsolą Xbox w celu ochrony użytkowników. Firma Microsoft analizuje 6,5 biliona sygnałów dziennie w celu identyfikacji i ochrony klientów przed zagrożeniami.

Sygnały generowane przez i przekazywane do ochrony tożsamości, mogą być dalej wprowadzane do narzędzi, takich jak dostęp warunkowy do podejmowania decyzji o dostępie, lub przekazywane z powrotem do narzędzia do zarządzania informacjami o zabezpieczeniach i zdarzeniami (SIEM) w celu dalszego dochodzenia w oparciu o egzekwowanych polityk.

## <a name="why-is-automation-important"></a>Dlaczego automatyzacja jest ważna?

W swoim [wpisie na blogu w październiku 2018](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Eight-essentials-for-hybrid-identity-3-Securing-your-identity/ba-p/275843) r. Alex Weinert, który kieruje zespołem microsoftu identity security and protection, wyjaśnia, dlaczego automatyzacja jest tak ważna w kontaktach z ilością zdarzeń:

> Każdego dnia nasze systemy uczenia maszynowego i heurystyki zapewniają ocenę ryzyka dla 18 miliardów prób logowania dla ponad 800 milionów różnych kont, z których 300 milionów jest wyraźnie wykonywanych przez przeciwników (podmioty takie jak: podmioty przestępcze, hakerzy).
>
> W zeszłym roku na Ignite mówiłem o 3 najlepszych atakach na nasze systemy tożsamości. Oto ostatnia liczba tych ataków
>   
>   - **Powtórka naruszenia**: 4,6 MLD ataków wykrytych w maju 2018 r.
>   - **Spray hasło**: 350k w kwietniu 2018
>   - **Phishing**: Jest to trudne do oszacowania dokładnie, ale widzieliśmy 23M zdarzeń ryzyka w marcu 2018, z których wiele jest phish związane

## <a name="risk-detection-and-remediation"></a>Wykrywanie i korygowanie ryzyka

Ochrona tożsamości identyfikuje zagrożenia w następujących klasyfikacjach:

| Typ wykrywania ryzyka | Opis |
| --- | --- |
| Nietypowe podróże | Zaloguj się z nietypowej lokalizacji na podstawie ostatnich logów użytkownika. |
| Anonimowy adres IP | Zaloguj się z anonimowego adresu IP (na przykład: przeglądarka Tora, anonimizator VPN). |
| Nieznane właściwości logowania | Zaloguj się przy przy tym przy tym, że nie widzieliśmy ostatnio danego użytkownika. |
| Połączony adres IP ze złośliwym oprogramowaniem | Logowanie się z adresu IP połączonego ze złośliwym oprogramowaniem |
| Wyciekające poświadczenia | To wykrywanie ryzyka wskazuje, że prawidłowe poświadczenia użytkownika zostały ujawnione |
| Analiza zagrożeń usługi Azure AD | Wewnętrzne i zewnętrzne źródła analizy zagrożeń firmy Microsoft zidentyfikowały znany wzorzec ataku |

Więcej szczegółów na temat tych zagrożeń i jak / kiedy są obliczane można znaleźć w artykule, [Co to jest ryzyko](concept-identity-protection-risks.md).

Sygnały ryzyka mogą powodować działania naprawcze, takie jak wymaganie od użytkowników: wykonywania uwierzytelniania wieloskładnikowego platformy Azure, resetowania hasła przy użyciu samoobsługowego resetowania hasła lub blokowania, dopóki administrator nie podejmie działań.

## <a name="risk-investigation"></a>Badanie ryzyka

Administratorzy mogą przeglądać wykrywania i w razie potrzeby podejmować ręczne działania na ich temat. Istnieją trzy kluczowe raporty używane przez administratorów do badania w ochronie tożsamości:

- Ryzykowni użytkownicy
- Ryzykowne logowania
- Wykrycia ryzyka

Więcej informacji można znaleźć w artykule, [Jak: Zbadać ryzyko](howto-identity-protection-investigate-risk.md).

## <a name="exporting-risk-data"></a>Eksportowanie danych o ryzyku

Dane z usługi Identity Protection można eksportować do innych narzędzi do archiwizacji i dalszego badania i relacji współużytkowych. Interfejsy API oparte na programie Microsoft Graph umożliwiają organizacjom zbieranie tych danych w celu dalszego przetwarzania w narzędziu, takim jak ich SIEM. Informacje dotyczące uzyskiwania dostępu do interfejsu API ochrony tożsamości można znaleźć w artykule Wprowadzenie [do usługi Azure Active Directory Identity Protection i Microsoft Graph](howto-identity-protection-graph-api.md)

Informacje na temat integrowania informacji o ochronie tożsamości z usługą Azure Sentinel można znaleźć w artykule [Połącz dane z usługi Azure AD Identity Protection](../../sentinel/connect-azure-ad-identity-protection.md).

## <a name="permissions"></a>Uprawnienia

Ochrona tożsamości wymaga, aby użytkownicy stanowili czytnik zabezpieczeń, operator zabezpieczeń, administrator zabezpieczeń, czytnik globalny lub administrator globalny w celu uzyskania dostępu.

| Rola | Może to zrobić | Nie można tego zrobić |
| --- | --- | --- |
| Administrator globalny | Pełny dostęp do ochrony tożsamości |   |
| Administrator zabezpieczeń | Pełny dostęp do ochrony tożsamości | Resetowanie hasła dla użytkownika |
| Operator zabezpieczeń | Wyświetl wszystkie raporty ochrony tożsamości i blok Przegląd <br><br> Odrzuć ryzyko użytkownika, potwierdź bezpieczne logowanie, potwierdź kompromis | Konfigurowanie lub zmienianie zasad <br><br> Resetowanie hasła dla użytkownika <br><br> Konfigurowanie alertów |
| Czytelnik zabezpieczeń | Wyświetl wszystkie raporty ochrony tożsamości i blok Przegląd | Konfigurowanie lub zmienianie zasad <br><br> Resetowanie hasła dla użytkownika <br><br> Konfigurowanie alertów <br><br> Przekazywanie opinii na temat wykrywania |

Administratorzy dostępu warunkowego mogą również tworzyć zasady, które uwzględniają ryzyko logowania jako warunek, znaleźć więcej informacji w artykule [Dostęp warunkowy: Warunki](../conditional-access/concept-conditional-access-conditions.md#sign-in-risk).

## <a name="license-requirements"></a>Wymagania licencyjne

[!INCLUDE [Active Directory P2 license](../../../includes/active-directory-p2-license.md)]

| Możliwości | Szczegóły | Usługa Azure AD — warstwa Premium P2 | Usługa Azure AD — warstwa Premium P1 | Usługa Azure AD Basic/Bezpłatna |
| --- | --- | --- | --- | --- |
| Polityka ryzyka | Zasady dotyczące ryzyka użytkownika (za pośrednictwem ochrony tożsamości) | Tak | Nie | Nie |
| Polityka ryzyka | Zasady ryzyka logowania (za pośrednictwem ochrony tożsamości lub dostępu warunkowego) | Tak | Nie | Nie |
| Raporty dotyczące zabezpieczeń | Omówienie | Tak | Nie | Nie |
| Raporty dotyczące zabezpieczeń | Ryzykowni użytkownicy | Dostęp pełny | Informacje ograniczone | Informacje ograniczone |
| Raporty dotyczące zabezpieczeń | Ryzykowne logowania | Dostęp pełny | Informacje ograniczone | Informacje ograniczone |
| Raporty dotyczące zabezpieczeń | Wykrycia ryzyka | Dostęp pełny | Informacje ograniczone | Nie |
| Powiadomienia | Użytkownicy z ryzykiem wykryli alerty | Tak | Nie | Nie |
| Powiadomienia | Podsumowanie tygodniowe | Tak | Nie | Nie |
| | Zasady rejestracji usługi MFA | Tak | Nie | Nie |

## <a name="next-steps"></a>Następne kroki

- [Omówienie zabezpieczeń](concept-identity-protection-security-overview.md)

- [Co to jest ryzyko](concept-identity-protection-risks.md)

- [Dostępne zasady ograniczania ryzyka](concept-identity-protection-policies.md)
