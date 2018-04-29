---
title: Dostęp warunkowy usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak dostęp warunkowy w usłudze Azure Active Directory może ułatwić Ci zarządzanie kontrolą dostępu z centralnej lokalizacji.
services: active-directory
keywords: dostęp warunkowy do aplikacji, dostęp warunkowy przy użyciu usługi Azure AD, bezpieczny dostęp do zasobów firmy, zasady dostępu warunkowego
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/23/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: 94d34df771334319db36009053a7264f0aeacf46
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="conditional-access-in-azure-active-directory"></a>Dostęp warunkowy w usłudze Azure Active Directory

Zabezpieczenia są szczególnie ważne dla organizacji przy użyciu chmury. Kluczowym aspektem zabezpieczeń chmury jest tożsamościami i dostępem, gdy chodzi o zarządzanie zasobami w chmurze. W świecie pierwszy mobile, najpierw chmury użytkownicy mogą korzystać z zasobów organizacji przy użyciu różnych urządzeń i aplikacji z dowolnego miejsca. W wyniku tego właśnie koncentrujących się na kto ma dostęp do zasobu nie jest wystarczające już. W celu głównego równowagi między zabezpieczeń i wydajności, specjalistów IT również trzeba współczynnika jak zasób jest dostępny do decyzji kontroli dostępu. Przy użyciu dostępu warunkowego dla usługi Azure AD można rozwiązać tego wymagania. Dostęp warunkowy jest możliwość usługi Azure Active Directory, która umożliwia wymuszanie kontroli dostępu do aplikacji w danym środowisku, na podstawie określonych warunków z centralnej lokalizacji. 


![Kontrola](./media/active-directory-conditional-access-azure-portal/81.png)

Ten artykuł zawiera omówienie pojęć dotyczących dostępu warunkowego w usłudze Azure AD.


## <a name="common-scenarios"></a>Typowe scenariusze

W świecie pierwszy mobile, najpierw chmury Azure Active Directory umożliwia logowanie jednokrotne do urządzeń, aplikacji i usług z dowolnego miejsca. Rosnąca liczba urządzeń (takich jak BYOD), pracy od sieci firmowej oraz innych firm aplikacji SaaS, specjalistów IT muszą stawiać czoła dwóch celów przeciwna:

- Zwiększenie produktywności użytkowników we wszystkich i w razie możliwości dostępnych dla
- Ochrona zasobów firmy w dowolnym momencie

Za pomocą zasad dostępu warunkowego, należy zastosować formanty prawa dostępu do wymaganych warunkach. Dostęp warunkowy usługi Azure AD pozwala zwiększyć bezpieczeństwo, gdy trzeba i pozostaje poza sposób użytkownika, gdy nie jest. 

Poniżej przedstawiono niektóre typowe problemy dostępu ułatwiających dostęp warunkowy z:



- **[Logowanie ryzyka](active-directory-conditional-access-conditions.md#sign-in-risk)**: Azure AD Identity Protection wykrywa zagrożenie logowania. Jak Jeśli wykryte zagrożenie logowania aktora zły wskazuje, ograniczenie dostępu? Co zrobić, jeśli chcesz pobrać silniejszych dowód, że logowanie przeprowadzono przez wiarygodnego użytkownika lub Twoje wątpliwości są silne nawet blokować określonym użytkownikom uzyskiwanie dostępu do aplikacji?

- **[Lokalizacja sieciowa](active-directory-conditional-access-locations.md)**: usługi Azure AD jest dostępna z dowolnego miejsca. Co zrobić, jeśli próba dostępu jest wykonywane z lokalizacji sieciowej, która nie jest pod kontrolą działu IT? Przy użyciu kombinacji nazwy użytkownika i hasła mogą być wystarczająca potwierdzenie tożsamości w celu udzielenia dostępu próbuje zasobów z sieci firmowej. Co zrobić, jeśli użytkownik żądanie silniejszych potwierdzenie tożsamości dla dostępu nieudane próby były inicjowane z innych nieoczekiwany krajach lub regionach świata? Co zrobić, jeśli chcesz nawet blokować dostęp prób z określonych lokalizacji?  

- **[Zarządzanie urządzeniami](active-directory-conditional-access-conditions.md#device-platforms)**: W usłudze Azure AD, użytkownicy mogą uzyskiwać dostęp do aplikacji w chmurze z szerokiej gamy urządzeniami, takimi jak telefon komórkowy, a także urządzeń osobistych. Co zrobić, jeśli można oczekiwać, że próby dostępu powinno być wykonywane jedynie, za pomocą urządzeń, które są zarządzane przez dział IT? Co zrobić, jeśli nawet chcesz zablokować pewnych typów urządzeń, dostęp do aplikacji w chmurze w swoim środowisku? 

- **[Aplikacja kliencka](active-directory-conditional-access-conditions.md#client-apps)**: obecnie jest dostępne wiele aplikacji w chmurze przy użyciu różnych typów aplikacji, takich jak aplikacje sieci web, aplikacji mobilnych lub aplikacji klasycznych. Co zrobić, jeśli próba dostępu do odbywa się przy użyciu typu aplikacji klienta, który powoduje, że znane problemy? Potrzebne są co zrobić, jeśli urządzenie jest zarządzane przez dział INFORMATYCZNY dla niektórych typów aplikacji? 

Te pytania i odpowiedzi powiązanych reprezentują typowych scenariuszy dostępu warunkowego dostępu usługi Azure AD. Dostęp warunkowy jest możliwość usługi Azure Active Directory, który umożliwia obsługę scenariuszy dostępu przy użyciu podejścia opartych na zasadach.


## <a name="conditional-access-policies"></a>Zasady dostępu warunkowego

Zasady dostępu warunkowego to definicja scenariusza dostępu przy użyciu następującego wzorca:

![Kontrola](./media/active-directory-conditional-access-azure-portal/10.png)

**Następnie wykonaj to** definiuje odpowiedzi zgodnie z zasadami. Należy pamiętać, że celem zasad dostępu warunkowego nie, aby udzielić dostępu do aplikacji w chmurze. W usłudze Azure AD udzielanie dostępu do aplikacji w chmurze jest przedmiotem przypisania użytkowników. Zasady dostępu warunkowego, możesz kontrolować sposób autoryzowanych użytkowników (użytkownicy, którym przyznano dostęp do aplikacji w chmurze) można uzyskać dostępu do aplikacji w chmurze określonych warunkach. Do odpowiedzi wymusić wymagania dodatkowe, takie jak uwierzytelnianie wieloskładnikowe, zarządzanego urządzenia i inne. W kontekście dostępu warunkowego dla usługi Azure AD wymagań, które wymuszają zasady są nazywane kontroli dostępu. W formularzu najbardziej restrykcyjne zasady może zablokować dostęp. Aby uzyskać więcej informacji, zobacz [dostęp do formantów w usłudze Azure Active Directory dostępu warunkowego](active-directory-conditional-access-controls.md).
     

**W takim przypadku** definiuje Przyczyna służącą do wyzwalania zasad. Z tego powodu charakteryzuje się grupy warunków, które zostały spełnione. W usłudze Azure AD dostęp warunkowy przypisanie dwa warunki odtwarzania specjalne uprawnienia:

- **[Użytkownicy](active-directory-conditional-access-conditions.md#users-and-groups)**: zostanie wykonana próba dostępu do użytkowników (**kto**). 

- **[Aplikacje w chmurze](active-directory-conditional-access-conditions.md#cloud-apps)**: próba dostępu do celów (**co**).    

Te dwa warunki są wymagane w zasadach dostępu warunkowego. Oprócz dwa warunki obowiązkowe możesz również dołączać dodatkowe warunki, których opisano, jak odbywa się próba uzyskania dostępu. Typowe przykłady korzystają z urządzeń przenośnych lub lokalizacje, które znajdują się poza siecią firmową. Aby uzyskać więcej informacji, zobacz [warunki dostępu warunkowego w usłudze Azure Active Directory](active-directory-conditional-access-conditions.md).   

Kombinacja warunki kontroli dostępu w sieci reprezentuje zasady dostępu warunkowego. 

![Kontrola](./media/active-directory-conditional-access-azure-portal/51.png)

Przy użyciu dostępu warunkowego dla usługi Azure AD, można kontrolować sposób autoryzowani użytkownicy mogą uzyskiwać dostęp do aplikacji w chmurze. Celem zasad dostępu warunkowego jest wymuszenie formanty dodatkowy dostęp podczas próby dostępu do aplikacji w chmurze, który jest wymuszany przez realizację próba dostępu do.

Stosując podejście na podstawie zasad ochrony dostępu do aplikacji w chmurze jest, że można uruchomić przygotowanie wymagań zasad dla danego środowiska przy użyciu struktury opisane w tym artykule, nie martwiąc się o implementacji technicznej. 

## <a name="what-you-need-to-know"></a>Co należy wiedzieć

### <a name="general-requirements-for-using-conditional-access"></a>Ogólne wymagania dotyczące korzystania z dostępu warunkowego

Dostęp warunkowy do usługi Azure AD umożliwia chronić aplikacje w chmurze, jeśli próba uwierzytelnienia pochodzi z:

- Przeglądarki sieci web

- Aplikacji klienta, który używa nowoczesnego uwierzytelniania

- Exchange ActiveSync

Aby uzyskać więcej informacji, zobacz [aplikacjach klienckich](active-directory-conditional-access-conditions.md#client-apps).

Niektóre [aplikacji w chmurze](active-directory-conditional-access-conditions.md#cloud-apps) również obsługiwać protokoły uwierzytelniania starszej wersji. Dotyczy to na przykład do usługi SharePoint Online i Exchange Online. Gdy aplikacja kliencka umożliwia dostęp do aplikacji w chmurze protokołem uwierzytelniania starszej wersji, usługi Azure AD nie można wymusić zasady dostępu warunkowego na ta próba dostępu. Aby zapobiec pomijanie wymuszania zasad aplikacji klienta, należy sprawdzić, czy jest możliwe tylko włączenie nowoczesnego uwierzytelniania w aplikacjach w chmurze dotyczy.

Przykłady aplikacji dostępu warunkowego nie ma zastosowania do klienta są:

- Office 2010 lub starszy

- Pakiet Office 2013, gdy nowoczesnego uwierzytelniania nie jest włączone.

Aby uzyskać więcej informacji, zobacz [skonfigurować usługi SharePoint Online i Exchange Online dla usługi Azure Active Directory dostępu warunkowego](active-directory-conditional-access-no-modern-authentication.md).


### <a name="license-requirements-for-using-conditional-access"></a>Wymagania licencyjne dotyczące korzystania z dostępu warunkowego

Przy użyciu dostępu warunkowego wymaga licencji usługi Azure AD Premium. Aby znaleźć prawa licencyjne do wymagań, zobacz [porównanie funkcji ogólnie dostępna bezpłatna, Basic i Premium Edition](https://azure.microsoft.com/pricing/details/active-directory/).


## <a name="next-steps"></a>Kolejne kroki

- Jeśli chcesz dowiedzieć się więcej na temat:
    - Warunki, zobacz [warunki dostępu warunkowego w usłudze Azure Active Directory](active-directory-conditional-access-conditions.md).

    - Dostęp do formantów, zobacz [dostęp do formantów w usłudze Azure Active Directory dostępu warunkowego](active-directory-conditional-access-controls.md).

- Jeśli chcesz uzyskać pewne doświadczenie w konfigurowaniu zasad dostępu warunkowego, zobacz [wprowadzenie dostępu warunkowego w usłudze Azure Active Directory](active-directory-conditional-access-azure-portal-get-started.md).

- Jeśli wszystko jest gotowe do skonfigurowania zasad dostępu warunkowego dla danego środowiska, zobacz [najlepszych rozwiązań dotyczących dostępu warunkowego w usłudze Azure Active Directory](active-directory-conditional-access-best-practices.md). 
