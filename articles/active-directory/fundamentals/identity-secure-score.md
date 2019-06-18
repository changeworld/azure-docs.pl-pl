---
title: Co to jest ocena bezpiecznego tożsamości? — Usługa azure Active Directory
description: Jak można użyć wyniku bezpiecznego tożsamości, aby zwiększyć poziom bezpieczeństwa w katalogu
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: tilarso
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6cdff2305914ca6e4144f7784d1a60026a1d27c0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65988681"
---
# <a name="what-is-the-identity-secure-score-in-azure-active-directory"></a>Co to jest wskaźnik bezpieczeństwa tożsamości w usłudze Azure Active Directory?

Jak bezpieczna jest Twoja dzierżawa usługi Azure AD? Jeśli nie wiesz, jak odpowiedzieć na to pytanie, w tym artykule wyjaśniono, jak wynik bezpiecznego tożsamości pomaga monitorować i zwiększyć poziom bezpieczeństwa tożsamości.

## <a name="what-is-an-identity-secure-score"></a>Co to jest wskaźnik bezpieczeństwa tożsamości?

Wynik bezpiecznego tożsamości jest liczba od 1 do 223, który działa jako wskaźnik określający sposób wyrównany są z rozwiązaniem firmy Microsoft zaleceniach dotyczących zabezpieczeń. Każda akcja poprawy jakości obsługi, w wyniku bezpiecznego tożsamości jest dostosowane do określonej konfiguracji.  

![Wskaźnik bezpieczeństwa](./media/identity-secure-score/identity-secure-score-overview.png)

Ten wskaźnik ułatwi Ci:

- Obiektywne mierzenie poziomu bezpieczeństwa tożsamości
- Planowanie poprawy bezpieczeństwa tożsamości
- Sprawdzanie powodzenia wprowadzonych udoskonaleń

Dostęp do wskaźnika i powiązanych informacji można uzyskać na pulpicie nawigacyjnym wskaźnika bezpieczeństwa tożsamości. Informacje prezentowane na tym pulpicie nawigacyjnym to:

- Bezpieczne ocenę tożsamości
- Porównanie wykres porównanie ocenę bezpiecznego tożsamość do innych dzierżaw w tej samej branży i podobnej wielkości
- Wykres trendu pokazujący, jak bezpieczne ocenę tożsamości zmieniła się wraz z upływem czasu
- Lista możliwych ulepszeń

Poprzez wykonanie akcji wprowadzania ulepszeń możliwe jest:

- Zwiększenia poziomu bezpieczeństwa i ocenę
- Wykorzystać funkcje dostępne dla Twojej organizacji jako część Twoich inwestycji tożsamości

## <a name="how-do-i-get-my-secure-score"></a>Jak mogę uzyskać swój wskaźnik bezpieczeństwa?

Wynik bezpiecznego tożsamości jest dostępna we wszystkich wersjach programu Azure AD. Aby uzyskać dostęp do tego wskaźnika, przejdź na [pulpit nawigacyjny przeglądu w usłudze Azure AD](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/IdentitySecureScore).

## <a name="how-does-it-work"></a>Jak to działa?

Co 48 godzin platforma Azure analizuje Twoją konfigurację zabezpieczeń i porównuje Twoje ustawienia z rekomendowanymi najlepszymi rozwiązaniami. W oparciu o wyniki tej oceny nową ocenę jest obliczana dla katalogu. Istnieje możliwość, że konfiguracji zabezpieczeń nie jest w pełni dostosowane do najlepszych rozwiązań i spełnione są tylko częściowo akcje poprawy jakości obsługi. W tych scenariuszach można będzie tylko można przyznać część maksymalna dostępna dla kontrolki oceny.

Każda rekomendacja jest mierzona w oparciu o konfigurację usługi Azure AD. Korzystania z produktów innych firm umożliwiające najlepsze zalecenie praktyki można wskazać tej konfiguracji w ustawieniach akcję poprawy jakości obsługi. Istnieje również możliwość ustawienia zaleceń, które można zignorować, jeśli nie można ich stosować do danego środowiska. Zignorowana rekomendacja nie wpływa na obliczenia Twojego wskaźnika.

![Zignoruj lub oznaczyć akcji, których dotyczy innych firm](./media/identity-secure-score/identity-secure-score-ignore-or-third-party-reccomendations.png)

## <a name="how-does-it-help-me"></a>Jak to może mi pomóc?

Wskaźnik bezpieczeństwa jest pomocny w następujących kwestiach:

- Obiektywne mierzenie poziomu bezpieczeństwa tożsamości
- Planowanie poprawy bezpieczeństwa tożsamości
- Sprawdzanie powodzenia wprowadzonych udoskonaleń

## <a name="what-you-should-know"></a>Co należy wiedzieć

### <a name="who-can-use-the-identity-secure-score"></a>Kto może używać wskaźnika bezpieczeństwa tożsamości?

Wskaźnik bezpieczeństwa tożsamości może być używany przez następujące role:

- Administrator globalny
- Administrator zabezpieczeń
- Czytelnicy zabezpieczeń

### <a name="how-are-controls-scored"></a>Jak są klasyfikowane formantów

Formanty mogą zostać ocenione na dwa sposoby. Niektóre są oceniane w sposób binarny — możesz otrzymywać 100% wynik, jeśli skonfigurowano ustawienie oparta na nasze zalecenie, lub funkcję. Inne wyniki są obliczane jako wartość procentowa łączna liczba konfiguracji. Na przykład, jeśli zalecenie dotyczące poprawy stwierdza, otrzymasz 30 punktów ochronę wszystkich użytkowników z usługą MFA, jeśli masz tylko 5 100 całkowitej liczby użytkowników chronione, możesz zapewnianą częściowe wynik około 2 punkty (5 chronione / całkowita 100 * 30 pkt max = 2 wynik częściowe pkt) .

### <a name="what-does-not-scored-mean"></a>Co oznacza informacja [Nieoceniane]?

Akcje oznaczone jako [Nieoceniane] to takie, które możesz wykonywać w swojej organizacji, ale które nie będą oceniane, ponieważ nie są uwzględnione w narzędziu (jeszcze!). Ciągle możesz więc zwiększyć swój poziom bezpieczeństwa, ale w tej chwil nie otrzymasz za to punktów.

### <a name="how-often-is-my-score-updated"></a>Jak często mój wskaźnik jest aktualizowany?

Wskaźnik jest obliczany raz dziennie (około 1:00 czasu PST). W przypadku wprowadzania zmiany w mierzonej akcji wskaźnik zostanie automatycznie zaktualizowany następnego dnia. Zanim zmiana zostanie odzwierciedlona w Twoim wskaźniku, może minąć do 48 godzin.

### <a name="my-score-changed-how-do-i-figure-out-why"></a>Mój wskaźnik uległ zmianie. Jak ustalić przyczynę tego?

Przejdź do [Centrum zabezpieczeń firmy Microsoft 365](https://security.microsoft.com/), znajdziesz tutaj pełną ocenę bezpiecznego firmy Microsoft. Łatwo widać wszystkie zmiany do bezpiecznego ocenę, przeglądając szczegółowe zmiany na karcie Historia.

### <a name="does-the-secure-score-measure-my-risk-of-getting-breached"></a>Bezpieczny wynik pomiaru Moje ryzyko związane z naruszeniem wprowadzenie?

W skrócie: nie. Bezpieczne wynik nie oznacza bezwzględne miary jak prawdopodobnie jesteś można uzyskać włamania. Wyraża on zakres, w jakim zostały przez Ciebie wdrożone funkcje odsuwające ryzyko naruszenia bezpieczeństwa. Żadna usługa nie może zagwarantować, że użytkownik nie zostanie naruszony, a bezpieczne wynik nie powinny być interpretowane jako gwarancja w dowolny sposób.

### <a name="how-should-i-interpret-my-score"></a>Jak mam zinterpretować swój wskaźnik?

Punkty otrzymujesz za skonfigurowanie rekomendowanych funkcji zabezpieczeń lub wykonanie zadań związanych z zabezpieczeniami (na przykład przeczytanie raportów). Niektóre akcje są oceniane za częściowe ukończenie. Należy do nich na przykład włączenie uwierzytelniania wieloskładnikowego (MFA) dla użytkowników. Twój bezpieczny wynik to bezpośrednio usług zabezpieczeń firmy Microsoft, którego używasz. Należy pamiętać, że zabezpieczeń musi być równoważony z użytecznością. Wszystkie kontrole bezpieczeństwa wpływają na użytkowników. Kontrole o niskim wpływie na użytkowników powinny mieć niewielkie przełożenie na codzienne operacje użytkowników lub nie mieć go wcale.

Aby wyświetlić historię wynik, przejdź do [Centrum zabezpieczeń firmy Microsoft 365](https://security.microsoft.com/) i przejrzyj ogólny wynik bezpiecznego firmy Microsoft. Możesz przejrzeć zmiany do całkowitego bezpiecznego wynik jest kliknięcie Wyświetl historię. Wybierz konkretną datę, aby zobaczyć, które kontrole były włączone w tym dniu i jakie punkty zostały Ci przyznane za każdą z nich.

### <a name="how-does-the-identity-secure-score-relate-to-the-office-365-secure-score"></a>Jaki jest związek między wskaźnikiem bezpieczeństwa tożsamości a wskaźnikiem bezpieczeństwa usługi Office 365?

[Microsoft secure wynik](https://docs.microsoft.com/office365/securitycompliance/microsoft-secure-score) zawiera pięć różnych kontroli i kategorie wynik:

- Tożsamość
- Dane
- Urządzenia
- Infrastruktura
- Aplikacje

Wynik bezpiecznego tożsamości reprezentuje część tożsamości wynik bezpiecznego firmy Microsoft. To nakładanie się oznacza, że zalecenia dla tożsamości secure wynik oceny tożsamości firmy Microsoft są takie same.

## <a name="next-steps"></a>Kolejne kroki

[Dowiedz się więcej na temat bezpiecznego oceny Microsoft](https://docs.microsoft.com/office365/securitycompliance/microsoft-secure-score)
