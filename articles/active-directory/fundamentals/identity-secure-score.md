---
title: Co to jest bezpieczny wynik tożsamości? - Usługa Azure Active Directory
description: Jak można użyć bezpiecznego wyniku tożsamości, aby poprawić poziom bezpieczeństwa katalogu
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: tilarso
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0f75dea2cffbe710bf2778ceab5eacc91ffcca9c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77523116"
---
# <a name="what-is-the-identity-secure-score-in-azure-active-directory"></a>Co to jest wskaźnik bezpieczeństwa tożsamości w usłudze Azure Active Directory?

Jak bezpieczna jest Twoja dzierżawa usługi Azure AD? Jeśli nie wiesz, jak odpowiedzieć na to pytanie, w tym artykule wyjaśniono, w jaki sposób wynik bezpiecznego tożsamości pomaga monitorować i poprawiać stan bezpieczeństwa tożsamości.

## <a name="what-is-an-identity-secure-score"></a>Co to jest wskaźnik bezpieczeństwa tożsamości?

Wynik bezpiecznego tożsamości to liczba z 1 do 223, która działa jako wskaźnik dostosowania do najlepszych rozwiązań firmy Microsoft dotyczących zabezpieczeń. Każda akcja poprawy w identyfikacji bezpiecznego wyniku jest dostosowana do konkretnej konfiguracji.  

![Wskaźnik bezpieczeństwa](./media/identity-secure-score/identity-secure-score-overview.png)

Ten wskaźnik ułatwi Ci:

- Obiektywne mierzenie poziomu bezpieczeństwa tożsamości
- Planowanie poprawy bezpieczeństwa tożsamości
- Sprawdzanie powodzenia wprowadzonych udoskonaleń

Dostęp do wskaźnika i powiązanych informacji można uzyskać na pulpicie nawigacyjnym wskaźnika bezpieczeństwa tożsamości. Informacje prezentowane na tym pulpicie nawigacyjnym to:

- Twoja tożsamość bezpieczny wynik
- Wykres porównawczy pokazujący porównanie wyników tożsamości w porównaniu z innymi dzierżawcami w tej samej branży i podobnej wielkości
- Wykres trendu pokazujący, jak zmieniał się twój wynik w zakresie bezpiecznej tożsamości w czasie
- Lista możliwych ulepszeń

Poprzez wykonanie akcji wprowadzania ulepszeń możliwe jest:

- Popraw swoją postawę bezpieczeństwa i wynik
- Korzystaj z funkcji dostępnych dla twojej organizacji w ramach inwestycji w tożsamość

## <a name="how-do-i-get-my-secure-score"></a>Jak mogę uzyskać swój wskaźnik bezpieczeństwa?

Wynik bezpiecznego tożsamości jest dostępny we wszystkich wersjach usługi Azure AD. Organizacje mogą uzyskać dostęp do swojego bezpiecznego wyniku tożsamości z wyniku bezpiecznego tożsamości **usługi Azure portal** > **Azure Active Directory** > **Security** > **Identity Secure Score**.

## <a name="how-does-it-work"></a>Jak to działa?

Co 48 godzin platforma Azure analizuje Twoją konfigurację zabezpieczeń i porównuje Twoje ustawienia z rekomendowanymi najlepszymi rozwiązaniami. Na podstawie wyniku tej oceny dla katalogu jest obliczana nowa ocena. Jest możliwe, że konfiguracja zabezpieczeń nie jest w pełni zgodna ze wskazówkami dotyczącymi najlepszych rozwiązań, a akcje poprawy są tylko częściowo spełnione. W tych scenariuszach zostanie przyznana tylko część maksymalnego wyniku dostępnego dla formantu.

Każda rekomendacja jest mierzona w oparciu o konfigurację usługi Azure AD. Jeśli używasz produktów innych firm, aby włączyć zalecenia dotyczące najlepszych praktyk, możesz wskazać tę konfigurację w ustawieniach akcji poprawy. Istnieje również możliwość ustawienia zaleceń, które mają być ignorowane, jeśli nie mają zastosowania do środowiska. Zignorowana rekomendacja nie wpływa na obliczenia Twojego wskaźnika.

![Ignorowanie lub oznaczanie akcji jako objętej ubezpieczeniem strony trzeciej](./media/identity-secure-score/identity-secure-score-ignore-or-third-party-reccomendations.png)

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

### <a name="how-are-controls-scored"></a>W jaki sposób są oceniane kontrole?

Formanty można punktować na dwa sposoby. Niektóre są oceniane w sposób binarny - otrzymasz 100% wyniku, jeśli masz funkcję lub ustawienie skonfigurowane na podstawie naszej rekomendacji. Inne wyniki są obliczane jako procent całkowitej konfiguracji. Na przykład, jeśli zalecenie poprawy stwierdza, że otrzymasz 30 punktów, jeśli ochronisz wszystkich użytkowników za pomocą usługi MFA i masz tylko 5 ze 100 zabezpieczonych użytkowników, otrzymasz częściowy wynik około 2 punktów (5 chronionych / 100 całkowitych * 30 max pkt = 2 pkt częściowy wynik).

### <a name="what-does-not-scored-mean"></a>Co oznacza informacja [Nieoceniane]?

Akcje oznaczone jako [Nieoceniane] to takie, które możesz wykonywać w swojej organizacji, ale które nie będą oceniane, ponieważ nie są uwzględnione w narzędziu (jeszcze!). Ciągle możesz więc zwiększyć swój poziom bezpieczeństwa, ale w tej chwil nie otrzymasz za to punktów.

### <a name="how-often-is-my-score-updated"></a>Jak często mój wskaźnik jest aktualizowany?

Wskaźnik jest obliczany raz dziennie (około 1:00 czasu PST). W przypadku wprowadzania zmiany w mierzonej akcji wskaźnik zostanie automatycznie zaktualizowany następnego dnia. Zanim zmiana zostanie odzwierciedlona w Twoim wskaźniku, może minąć do 48 godzin.

### <a name="my-score-changed-how-do-i-figure-out-why"></a>Mój wskaźnik uległ zmianie. Jak ustalić przyczynę tego?

Udaj się do [centrum zabezpieczeń Microsoft 365,](https://security.microsoft.com/)gdzie znajdziesz pełny bezpieczny wynik firmy Microsoft. Możesz łatwo zobaczyć wszystkie zmiany bezpiecznego wyniku, przeglądając szczegółowe zmiany na karcie historia.

### <a name="does-the-secure-score-measure-my-risk-of-getting-breached"></a>Czy bezpieczny wynik mierzy ryzyko naruszenia mojego wyniku?

W skrócie: nie. Bezpieczny wynik nie wyraża absolutnej miary prawdopodobieństwo, że dojdziesz do naruszenia. Wyraża on zakres, w jakim zostały przez Ciebie wdrożone funkcje odsuwające ryzyko naruszenia bezpieczeństwa. Żadna usługa nie może zagwarantować, że nie zostaniesz naruszony, a bezpieczny wynik nie powinien być interpretowany jako gwarancja w jakikolwiek sposób.

### <a name="how-should-i-interpret-my-score"></a>Jak mam zinterpretować swój wskaźnik?

Punkty otrzymujesz za skonfigurowanie rekomendowanych funkcji zabezpieczeń lub wykonanie zadań związanych z zabezpieczeniami (na przykład przeczytanie raportów). Niektóre akcje są oceniane za częściowe ukończenie. Należy do nich na przykład włączenie uwierzytelniania wieloskładnikowego (MFA) dla użytkowników. Twój bezpieczny wynik jest bezpośrednio reprezentatywny dla używanych usług zabezpieczeń firmy Microsoft. Pamiętaj, że bezpieczeństwo musi być zrównoważone użytecznością. Wszystkie kontrole bezpieczeństwa wpływają na użytkowników. Kontrole o niskim wpływie na użytkowników powinny mieć niewielkie przełożenie na codzienne operacje użytkowników lub nie mieć go wcale.

Aby wyświetlić historię wyników, przejdź do [centrum zabezpieczeń usługi Microsoft 365](https://security.microsoft.com/) i przejrzyj ogólny bezpieczny wynik firmy Microsoft. Możesz przejrzeć zmiany w ogólnym bezpiecznym wyniku, klikając historię wyświetlenia. Wybierz konkretną datę, aby zobaczyć, które kontrole były włączone w tym dniu i jakie punkty zostały Ci przyznane za każdą z nich.

### <a name="how-does-the-identity-secure-score-relate-to-the-office-365-secure-score"></a>Jaki jest związek między wskaźnikiem bezpieczeństwa tożsamości a wskaźnikiem bezpieczeństwa usługi Office 365?

[Bezpieczny wynik firmy Microsoft](https://docs.microsoft.com/office365/securitycompliance/microsoft-secure-score) zawiera pięć odrębnych kategorii kontroli i punktów:

- Tożsamość
- Dane
- Urządzenia
- Infrastruktura
- Aplikacje

Wynik bezpiecznego tożsamości reprezentuje część tożsamości bezpiecznego wyniku firmy Microsoft. To nakładanie oznacza, że zalecenia dotyczące bezpiecznego wyniku tożsamości i wyniku tożsamości w firmie Microsoft są takie same.

## <a name="next-steps"></a>Następne kroki

[Dowiedz się więcej o bezpiecznym wyniku firmy Microsoft](https://docs.microsoft.com/office365/securitycompliance/microsoft-secure-score)
