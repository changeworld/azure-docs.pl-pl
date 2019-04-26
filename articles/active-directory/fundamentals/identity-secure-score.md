---
title: Co to jest wskaźnik bezpieczeństwa tożsamości? — Usługa Azure Active Directory (publiczna wersja zapoznawcza) | Microsoft Docs
description: Instrukcje dotyczące korzystania ze wskaźnika bezpieczeństwa tożsamości w celu zwiększenia poziomu bezpieczeństwa Twojej dzierżawy usługi Azure AD.
services: active-directory
keywords: wskaźnik bezpieczeństwa tożsamości, Azure AD, bezpieczny dostęp do zasobów firmy
documentationcenter: ''
author: MarkusVi
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: fundamentals
ms.topic: overview
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/19/2018
ms.author: markvi
ms.reviewer: nigu
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8617442c811dc2fe8f961038f35ebcf218a58302
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60247479"
---
# <a name="what-is-the-identity-secure-score-in-azure-active-directory-public-preview"></a>Co to jest wskaźnik bezpieczeństwa tożsamości w usłudze Azure Active Directory? (publiczna wersja zapoznawcza)

Jak bezpieczna jest Twoja dzierżawa usługi Azure AD? Jeśli nie wiesz, jak odpowiedzieć na to pytanie, przeczytaj ten artykuł, aby dowiedzieć się, jak wskaźnik bezpieczeństwa tożsamości pomaga w monitorowaniu i podnoszeniu poziomu bezpieczeństwa tożsamości. 

## <a name="what-is-an-identity-secure-score"></a>Co to jest wskaźnik bezpieczeństwa tożsamości?

Wskaźnik bezpieczeństwa tożsamości jest liczbą z zakresu od 1 do 248, która pokazuje, jaki jest Twój poziom zgodności z rekomendacjami dotyczącymi najlepszych rozwiązań firmy Microsoft.


![Wskaźnik bezpieczeństwa](./media/identity-secure-score/01.png)



Ten wskaźnik ułatwi Ci:

- Obiektywne mierzenie poziomu bezpieczeństwa tożsamości

- Planowanie poprawy bezpieczeństwa tożsamości

- Sprawdzanie powodzenia wprowadzonych udoskonaleń 


Dostęp do wskaźnika i powiązanych informacji można uzyskać na pulpicie nawigacyjnym wskaźnika bezpieczeństwa tożsamości. Informacje prezentowane na tym pulpicie nawigacyjnym to:

- Twój wskaźnik

    ![Wskaźnik bezpieczeństwa](./media/identity-secure-score/02.png)

- Wykres porównawczy

    ![Wskaźnik bezpieczeństwa](./media/identity-secure-score/03.png)

- Wykres trendu

    ![Wskaźnik bezpieczeństwa](./media/identity-secure-score/04.png)

- Lista najlepszych rozwiązań z zakresu zabezpieczeń tożsamości. 

    ![Wskaźnik bezpieczeństwa](./media/identity-secure-score/05.png)


Poprzez wykonanie akcji wprowadzania ulepszeń możliwe jest:

- Zwiększenie poziomu bezpieczeństwa i Twojego wskaźnika.
 
- Korzystanie z funkcji obsługi tożsamości firmy Microsoft. 



## <a name="how-do-i-get-my-secure-score"></a>Jak mogę uzyskać swój wskaźnik bezpieczeństwa?

Wskaźnik bezpieczeństwa tożsamości jest dostępny we wszystkich wersjach usługi Azure AD.

Aby uzyskać dostęp do tego wskaźnika, przejdź na [pulpit nawigacyjny przeglądu w usłudze Azure AD](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/IdentitySecureScore).



## <a name="how-does-it-work"></a>Jak to działa?

Co 48 godzin platforma Azure analizuje Twoją konfigurację zabezpieczeń i porównuje Twoje ustawienia z rekomendowanymi najlepszymi rozwiązaniami. W oparciu o wyniki tej oceny dla Twojej dzierżawy obliczany jest nowy wskaźnik. Oznacza to, że może minąć nawet 48 godzin, zanim wprowadzona zmiana konfiguracji znajdzie odzwierciedlenie w Twoim wskaźniku. 

Każda rekomendacja jest mierzona w oparciu o konfigurację usługi Azure AD. Jeśli do umożliwiania rekomendowania najlepszych rozwiązań używasz produktów innych firm, możesz to zaznaczyć w ustawieniach akcji wprowadzania ulepszeń.

![Wskaźnik bezpieczeństwa](./media/identity-secure-score/07.png)


Dodatkowo masz również możliwość ustawienia rekomendacji, które mają być ignorowane, jeśli nie mają zastosowania do Twojego środowiska. Zignorowana rekomendacja nie wpływa na obliczenia Twojego wskaźnika. 
 
![Wskaźnik bezpieczeństwa](./media/identity-secure-score/06.png)



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

### <a name="what-does-not-scored-mean"></a>Co oznacza informacja [Nieoceniane]?

Akcje oznaczone jako [Nieoceniane] to takie, które możesz wykonywać w swojej organizacji, ale które nie będą oceniane, ponieważ nie są uwzględnione w narzędziu (jeszcze!). Ciągle możesz więc zwiększyć swój poziom bezpieczeństwa, ale w tej chwil nie otrzymasz za to punktów.

### <a name="how-often-is-my-score-updated"></a>Jak często mój wskaźnik jest aktualizowany?

Wskaźnik jest obliczany raz dziennie (około 1:00 czasu PST). W przypadku wprowadzania zmiany w mierzonej akcji wskaźnik zostanie automatycznie zaktualizowany następnego dnia. Zanim zmiana zostanie odzwierciedlona w Twoim wskaźniku, może minąć do 48 godzin.


### <a name="my-score-changed-how-do-i-figure-out-why"></a>Mój wskaźnik uległ zmianie. Jak ustalić przyczynę tego?

Na stronie analizatora wskaźnika w [portalu wskaźnika bezpieczeństwa](https://securescore.microsoft.com/#!/score) kliknij punkt danych dla konkretnego dnia, a następnie przewiń ekran w dół, aby zobaczyć ukończone i nieukończone akcje dla tego dnia oraz sprawdzić, co się zmieniło.

### <a name="does-the-secure-score-measure-my-risk-of-getting-breached"></a>Czy wskaźnik bezpieczeństwa mierzy ryzyko naruszenia bezpieczeństwa?

W skrócie: nie. Wskaźnik bezpieczeństwa nie wyraża żadnej bezwzględnej miary tego, jak prawdopodobne jest naruszenie Twojego bezpieczeństwa. Wyraża on zakres, w jakim zostały przez Ciebie wdrożone funkcje odsuwające ryzyko naruszenia bezpieczeństwa. Żadna usługa nie może zagwarantować, że Twoje bezpieczeństwo nie zostanie naruszone, a wskaźnik bezpieczeństwa nie powinien być w żaden sposób rozumiany jako gwarancja bezpieczeństwa.

### <a name="how-should-i-interpret-my-score"></a>Jak mam zinterpretować swój wskaźnik?

Punkty otrzymujesz za skonfigurowanie rekomendowanych funkcji zabezpieczeń lub wykonanie zadań związanych z zabezpieczeniami (na przykład przeczytanie raportów). Niektóre akcje są oceniane za częściowe ukończenie. Należy do nich na przykład włączenie uwierzytelniania wieloskładnikowego (MFA) dla użytkowników. Wskaźnik bezpieczeństwa to bezpośrednie odzwierciedlenie usług zabezpieczeń firmy Microsoft, których używasz. Pamiętaj, że bezpieczeństwo i użyteczność powinny być zrównoważone. Wszystkie kontrole bezpieczeństwa wpływają na użytkowników. Kontrole o niskim wpływie na użytkowników powinny mieć niewielkie przełożenie na codzienne operacje użytkowników lub nie mieć go wcale.

Aby wyświetlić historię wskaźnika, przejdź na stronę analizatora wskaźnika w [portalu wskaźnika bezpieczeństwa](https://securescore.microsoft.com/#!/score). Wybierz konkretną datę, aby zobaczyć, które kontrole były włączone w tym dniu i jakie punkty zostały Ci przyznane za każdą z nich.


### <a name="how-does-the-identity-secure-score-relate-to-the-office-365-secure-score"></a>Jaki jest związek między wskaźnikiem bezpieczeństwa tożsamości a wskaźnikiem bezpieczeństwa usługi Office 365? 

[Wskaźnik bezpieczeństwa usługi Office 365 ](https://docs.microsoft.com/office365/securitycompliance/office-365-secure-score) ma zostać niedługo poddany migracji do agregacji pięciu różnych wskaźników:

- Tożsamość

- Dane

- Urządzenia

- Infrastruktura

- Aplikacje

Wskaźnik bezpieczeństwa tożsamości reprezentuje część „Tożsamość” wskaźnika bezpieczeństwa usługi Office 365. Oznacza to, że rekomendacje dotyczące wskaźnika bezpieczeństwa tożsamości i wskaźnika tożsamości w usłudze Office 365 są takie same. 


## <a name="next-steps"></a>Kolejne kroki

Jeśli chcesz obejrzeć wideo na temat wskaźnika bezpieczeństwa usługi Office 365, kliknij [tutaj](https://www.youtube.com/watch?v=jzfpDJ9Kg-A).
 
