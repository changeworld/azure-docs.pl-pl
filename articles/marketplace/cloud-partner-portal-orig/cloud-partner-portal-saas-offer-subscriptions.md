---
title: SaaS — sprzedawać za pośrednictwem platformy Azure | Dokumentacja firmy Microsoft
description: W tym artykule opisano rozliczeń modelu dla aplikacji SaaS i sposobie jego implementowania subskrypcji.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/17/2018
ms.author: pbutlerm
ms.openlocfilehash: f193138fbc5e779c3a6671757320d8918e08db46
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2018
ms.locfileid: "48810667"
---
<a name="saas---sell-through-azure"></a>SaaS — sprzedaż za pośrednictwem platformy Azure
=========================

W tym artykule opisano model rozliczeń subskrypcji, aplikacje SaaS i sposobie jego implementowania w portalu Cloud Partner.


<a name="overview"></a>Przegląd
--------

Portal Azure marketplace umożliwia publikowanie i sprzedawanie ofert SaaS w portalu Azure marketplace. Dla klientów, ich można płacić za pośrednictwem bezpośrednio przez rozliczeń platformy Azure i po prostu otrzymają jeden rachunek na platformie Azure dla wszystkich zasobów i ich uaktywnili usług. Do publikacji, subskrypcji SaaS zapewnia następujące korzyści:

-   **Spójne środowisko publikowania** — Jeśli opublikujesz ofert SaaS lub jakąkolwiek inną ofertę w portalu Azure Marketplace, środowisko publikowania za pomocą tego samego portalu wydawców.
-   **Nowe storefront odnajdywania** — wszystkie oferty publikowane będą widoczne, zarówno w sklepie zewnętrznego portalu Azure Marketplace, a także rozszerzenie portalu Azure Marketplace w witrynie Azure portal.
-   **Zintegrowane rozliczenia** -można teraz sprzedawać we wszystkich regionach, w którym sprzedaje platformy Azure i platforma Azure zajmie się rozliczeń dla Ciebie.
-   **Zintegrowane pozyskiwanie potencjalnych** — możesz teraz automatycznie odbierać potencjalnych klientów z platformy Azure w aplikacji CRM, co pozwala zawsze wtedy, gdy użytkownik Azure subskrybuje usługi SaaS, za pomocą witryny Azure marketplace.
-   **Wspólnej sprzedaży ze sprzedawcami firmy Microsoft** — może kwalifikujesz się do realizacji dwukierunkowego udostępniania priorytetowe miejsce w katalogu, i możliwość angażowania i przesyłać do wspólnego obsługuje side-by-side ze sprzedawcami firmy Microsoft.

<a name="billing-models"></a>Modele rozliczeń
--------------

Tylko aktualnie obsługiwany model rozliczeń jest płaskie miesięczną opłatę za subskrypcję usługi SaaS.

**Uwaga:** dodatkowe modele rozliczeń mogą być dostępne w przyszłości.

Każda oferta SaaS może mieć co najmniej jeden plan (na przykład podstawowa lub Premium). Każdy plan zawiera pewne podstawowe metadane skojarzone z nią wraz z ceną skojarzone z planem.

Masz pełną kontrolę nad definicji planu. Na przykład co czy podstawowy plan stanowi? Plan jest zdefiniowany przez użytkownika i zapewniają niezbędne tekst, który je jako część planu publikowania.

Cena skojarzone z planem jest płaskie miesięczna opłata, który użytkownicy platformy Azure za korzystanie z usługi.

### <a name="what-is-not-supported-today"></a>Co nie jest obecnie obsługiwane?

-   Rozliczane na podstawie jednostki niestandardowe — na przykład określoną cenę na podstawie liczby żądań.
-   Rozliczanie oparte na stanowisko alokacji — na przykład zezwolić użytkowników platformy Azure kupić licencje na podstawie liczby użytkowników.

<a name="end-to-end-flow"></a>Przepływ end-to-end
---------------

Subskrypcji w modelu SaaS oferują usługi flow z perspektywy użytkownika końcowego najpierw

**Uwaga:** przepływ założono została opublikowana z oferty SaaS w witrynie Azure marketplace, o nazwie "Pokaz SaaS firmy Contoso".

![Przepływ użytkownika subskrypcji w modelu SaaS](media/saas-offer-subscription/saas-subscription-user-flow.png)

Użytkownikiem platformy Azure może mieć następujące interakcje z Twoją usługą SaaS:

-   Odnajdywanie usługi SaaS w portalu Azure Marketplace
-   Subskrybowanie usługi SaaS na platformie Azure
-   Przejdź do usługi SaaS w witrynie Azure portal
-   Utwórz konto w usłudze SaaS i zarządzanie nimi (zmiana planu/usuwanie) na koncie w modelu SaaS
-   Anuluj subskrypcję usługi SaaS z witryny Azure portal

<a name="discover-your-saas-service-in-azure-marketplace"></a>Odnajdywanie usługi SaaS w portalu Azure Marketplace
-----------------------------------------------

Gdy użytkownicy uruchamiać portalu Azure Marketplace w witrynie Azure portal, zobaczą kategorię o nazwie "Oprogramowanie jako usługa (SaaS)".

![Odkryj SaaS przy użyciu menu kategorii](media/saas-offer-subscription/saas-category-menu.png)

Użytkownicy mogą również przeszukiwać usługi SaaS.

![Odkryj SaaS przy użyciu wyszukiwania](media/saas-offer-subscription/saas-cpp-search.png)

Użytkownicy mogą następnie wyświetlać szczegółowe informacje o usłudze, a następnie kliknij przycisk **Utwórz**.

![Utworzenie subskrypcji w modelu SaaS](media/saas-offer-subscription/saas-create-subscription.png)

### <a name="subscribe-to-saas-service-in-azure"></a>Subskrybujesz usługi SaaS na platformie Azure

Użytkownik może subskrybować usługi SaaS na platformie Azure.

![Subskrybowanie usługi SaaS](media/saas-offer-subscription/saas-subscribe-signup.png)

Gdy użytkownik subskrybuje usługi SaaS, użytkownik udostępnia następujące informacje

-   Nazwa — Nazwa czy użytkownicy mogą odnajdywać lub zarządzać tym wystąpienia subskrypcji SaaS na platformie Azure.
-   Subskrypcja — Kontekst subskrypcji, ich chcesz skojarzyć rozliczeń dla usługi SaaS.
-   Plan — SaaS usługa planu, który chcesz, aby subskrybować.

Dokument warunki użytkowania, w ramach publikowania oferty jest również wyświetlany użytkownikowi przed zasubskrybowaniem przez użytkownika w usłudze SaaS.

Użytkownik może teraz subskrybowania usługi, klikając **Subskrybuj**.
Platforma Azure wysyła powiadomienia w portalu po zakończeniu subskrypcji.

### <a name="navigate-to-the-saas-service-from-azure-portal"></a>Przejdź do usługi SaaS z witryny Azure Portal

Następnie kliknij pozycję Użytkownicy **przejdź do zasobu** na wyświetlanie i zarządzanie ich wystąpienia subskrypcji w modelu SaaS.

![Wyświetlanie i Zarządzanie wystąpieniem usługi SaaS](media/saas-offer-subscription/saas-go-to-resource.png)

Użytkownik zostanie powiadomiony, że ich należy skonfigurować konto w usłudze SaaS najpierw. Ich rozliczeniami rozpocznie się po usłudze SaaS powiadamia platformy Azure, aby uruchomić rozliczeń, które jest, gdy użytkownik tworzy konto usługi w witrynie usługi SaaS.

![Konfigurowanie wystąpienia usługi SaaS](media/saas-offer-subscription/saas-configure-account.png)

Gdy użytkownik kliknie **skonfigurować konto**, zostanie przekierowany do punktu końcowego usługi SaaS. Podczas tego przekierowania token jest przekazywany jako parametr zapytania. Na przykład:

![SaaS konta tokenu](media/saas-offer-subscription/saas-account-token.png)

Zanotuj tę wartość tokenu. Token ten jest krótkotrwałe i musi zostać rozpoznane Pobierz identyfikator subskrypcji na platformie Azure.

<a name="creating-a-saas-offer-subscription"></a>Utworzenie subskrypcji w modelu SaaS oferty
----------------------------------

Aby utworzyć to środowisko, istnieją dwa elementy pracy, które są wymagane:

-   Łączenie usługi witryny sieci Web usługi SaaS z firmą Microsoft\'s interfejsami API usług SaaS. W tym dokumencie [sprzedaży SaaS za pośrednictwem platformy Azure — interfejsy API](./cloud-partner-portal-saas-subscription-apis.md) wyjaśnia, jak utworzyć to połączenie.  
-   Włącz **sprzedawać za pośrednictwem platformy Azure** w portalu Cloud Partner w **informacje techniczne** sekcji, a następnie podaj wszystkie informacje dotyczące konfiguracji.

![Nowe w modelu SaaS oferują informacje techniczne](media/cpp-creating-saas-offers/saas-new-offer-technical-info-2.png)

Poniżej przedstawiono omówienie wszystkie wymagane pola dla **informacje techniczne** sekcji:

|  **Pola oferty**                 |  **Opis**                                   |
|  ----------------                 |  -------------------------------------             |
| Identyfikatory subskrypcji (wersja zapoznawcza)          | Wszystkich subskrypcji platformy Azure identyfikatory używane do testowania oferty w wersji zapoznawczej, zanim będzie dostępny publicznie. |
| Instrukcje z wprowadzeniem      | Wskazówki, aby udostępnić ze swoimi klientami, aby pomóc im nawiązywanie aplikację SaaS. Podstawowe użycia HTML jest dozwolony, tagi, takie jak `<p>`, `<h1>`, `<li>`itp.  |
| Adres URL strony                  | Adres URL witryny, która będzie kierowanie klientów jako proponowany po uzyskaniu z witryny Azure portal, aby. Ten adres URL będzie również punkt końcowy, który będzie otrzymywać połączenia interfejsy API w celu ułatwienia handlowych firmy Microsoft.  |
| Połączenie elementu Webhook                | Dla wszystkich zdarzeń asynchronicznych, które firma Microsoft musi wysłać użytkownikom w imieniu klienta (przykład: stała się nieprawidłowa subskrypcja platformy Azure), wymagamy Opisz element webhook połączenia. Jeśli nie masz jeszcze system elementu webhook w miejscu, najprostsza konfiguracja jest aplikacja logiki punktu końcowego HTTP, który będzie nasłuchiwać zdarzeń ogłaszany do niego i odpowiednio je obsłużyć.  Aby uzyskać więcej informacji, zobacz [wywołania wyzwalacza lub zagnieżdżanie przepływy pracy za pomocą punktów końcowych HTTP w usłudze logic apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-http-endpoint). |
| Dzierżawy usługi Azure AD identyfikator i identyfikator aplikacji     | W witrynie Azure portal, wymagamy, aby utworzyć aplikacji usługi Active Directory, dzięki czemu możemy zweryfikować połączenie między dwoma naszych usług znajduje się za uwierzytelnianiu komunikacji. Dla tych pól, Utwórz aplikację usługi AD i wkleić w odpowiednich identyfikatora dzierżawy oraz identyfikatora aplikacji jest wymagane. |
|  |  |


Na koniec po wybraniu **sprzedawać za pośrednictwem platformy Azure**, istnieje dodano sekcję o nazwie **plany**. Plany są wymagane tylko, jeśli wybrano opcję sprzedaży za pośrednictwem platformy Azure. W tej sekcji przedstawiono określonych planów i ich odpowiednie ceny, obsługiwanych przez aplikację SaaS. Obecnie firma Microsoft umożliwia ceny miesięczne z możliwością umożliwia dla 1 - lub 3-miesięcznym bezpłatny dostęp. Te plany i ceny powinna być zgodna dokładnie plany i ceny, do których masz na swojej stronie aplikacji SaaS.
