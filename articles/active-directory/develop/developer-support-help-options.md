---
title: Opcje pomocy technicznej i pomocy dla deweloperów aplikacji usługi Azure AD
description: Dowiedz się, jak uzyskać pomoc i pomoc techniczną dotyczącą pytań i problemów związanych z programem podczas tworzenia aplikacji integrującej się z tożsamościami firmy Microsoft (usługa Azure Active Directory i konto Microsoft)
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/23/2019
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev
ms.openlocfilehash: ea1afd2ecac8974c0e865e235288da545a9f1244
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885535"
---
# <a name="support-and-help-options-for-developers"></a>Pomoc techniczna i opcje pomocy dla deweloperów

Jeśli dopiero zaczynasz integrować się z usługą Azure Active Directory (Azure AD), tożsamościami firmy Microsoft lub interfejsem API programu Microsoft Graph lub gdy implementujesz nową funkcję w aplikacji, czasami musisz uzyskać pomoc od społeczności lub zrozumieć opcje pomocy technicznej, które masz jako deweloper. Ten artykuł pomaga zrozumieć te opcje, w tym:

> [!div class="checklist"]
> * Jak wyszukać, czy społeczność nie odpowiedziała na twoje pytanie lub czy istniejąca dokumentacja funkcji, którą próbujesz zaimplementować, już istnieje
> * W niektórych przypadkach wystarczy skorzystać z naszych narzędzi pomocy technicznej, aby pomóc Ci debugować konkretny problem
> * Jeśli nie możesz znaleźć potrzebnej odpowiedzi, możesz zadać pytanie w *usłudze Przepełnienie stosu*
> * Jeśli znajdziesz problem z jedną z naszych bibliotek uwierzytelniania, zgłańz problem z *gitHub*
> * Na koniec, jeśli chcesz z kimś porozmawiać, możesz otworzyć prośbę o pomoc

## <a name="search"></a>Wyszukiwanie

Jeśli masz pytanie związane z rozwojem, możesz znaleźć odpowiedź w dokumentacji, [przykładach GitHub](https://github.com/azure-samples)lub odpowiedzi na pytania [dotyczące przepełnienia stosu.](https://www.stackoverflow.com)

### <a name="scoped-search"></a>Wyszukiwanie z zakresem

Aby uzyskać szybsze wyniki, zakres wyszukiwania do przepełnienia stosu, dokumentacji i przykładów kodu przy użyciu następujące zapytanie w ulubionej wyszukiwarce:

```
{Your Search Terms} (site:stackoverflow.com OR site:docs.microsoft.com OR site:github.com/azure-samples OR site:cloudidentity.com OR site:developer.microsoft.com/graph)
```

Gdzie *{Wyszukiwane terminy}* odpowiadają wyszukiwanym słowom kluczowym.

## <a name="use-the-development-support-tools"></a>Korzystanie z narzędzi wsparcia rozwoju

| Narzędzie  | Opis  |
|---------|---------|
| [jwt.ms](https://jwt.ms) | Wklej identyfikator lub token dostępu, aby zdekodować nazwy i wartości oświadczeń. |
| [Eksplorator programu Microsoft Graph](https://developer.microsoft.com/graph/graph-explorer)| Narzędzie, które umożliwia składanie żądań i wyświetlanie odpowiedzi na interfejs API programu Microsoft Graph. |

## <a name="post-a-question-to-stack-overflow"></a>Zamieść pytanie do Przepełnienia stosu

Przepełnienie stosu jest preferowanym kanałem pytań związanych z rozwojem. W tym miejscu członkowie społeczności deweloperów i członkowie zespołu firmy Microsoft są bezpośrednio zaangażowani w pomoc w rozwiązywaniu problemów.

Jeśli nie możesz znaleźć odpowiedzi na swoje pytanie za pomocą wyszukiwania, prześlij nowe pytanie do Przepełnienie stosu. Podczas zadawania pytań użyj jednego z następujących tagów, aby pomóc społeczności w skuteczniejszym identyfikowaniu i odpowiadaniu na twoje pytanie:

|Komponent/obszar  | Tagi |
|---------|---------|
| Biblioteka ADAL | [[adal]](https://stackoverflow.com/questions/tagged/adal) |
| Biblioteka MSAL     | [[msal]](https://stackoverflow.com/questions/tagged/msal) |
| Oprogramowanie pośredniczące OWIN  | [[azure-active-directory]](https://stackoverflow.com/questions/tagged/azure-active-directory) |
| [Funkcje B2B platformy Azure](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)  | [[azure-ad-b2b]](https://stackoverflow.com/questions/tagged/azure-ad-b2b) |
| [Azure B2C](https://azure.microsoft.com/services/active-directory-b2c/)  | [[azure-ad-b2c]](https://stackoverflow.com/questions/tagged/azure-ad-b2c) |
| [Interfejs API programu Microsoft Graph](https://developer.microsoft.com/graph/) | [[microsoft-graph]](https://stackoverflow.com/questions/tagged/microsoft-graph) |
| Każdy inny obszar związany z tematami uwierzytelniania lub autoryzacji | [[azure-active-directory]](https://stackoverflow.com/questions/tagged/azure-active-directory) |

Poniższe posty z Przepełnienie stosu zawierają wskazówki dotyczące zadawania pytań i dodawania kodu źródłowego. Postępuj zgodnie z tymi wytycznymi, aby zwiększyć szanse członków społeczności na szybką ocenę i udzielenie odpowiedzi na twoje pytanie:

* [Jak zadać dobre pytanie](https://stackoverflow.com/help/how-to-ask)
* [Jak stworzyć minimalny, kompletny i weryfikowalny przykład](https://stackoverflow.com/help/mcve)

## <a name="create-a-github-issue"></a>Tworzenie problemu z githubem

Jeśli znajdziesz błąd lub problem związany z naszymi bibliotekami, zgładź problem w naszych repozytoriach GitHub. Ponieważ nasze biblioteki są open source, można również przesłać żądanie ściągnięcia.

Aby uzyskać listę bibliotek i ich repozytoriów GitHub, zobacz następujące elementy:

* [Biblioteki biblioteki uwierzytelniania usługi Azure Active Directory (ADAL)](../azuread-dev/active-directory-authentication-libraries.md) i repozytoria usługi GitHub
* [Biblioteki biblioteki uwierzytelniania Firmy Microsoft (MSAL)](reference-v2-libraries.md) i repozytoria usługi GitHub

## <a name="open-a-support-request"></a>Otwórz żądanie pomocy technicznej

Jeśli chcesz z kimś porozmawiać, możesz otworzyć prośbę o pomoc techniczną. Jeśli jesteś klientem platformy Azure, dostępnych jest kilka opcji pomocy technicznej. Aby porównać plany, zobacz [tę stronę](https://azure.microsoft.com/support/plans/). Pomoc techniczna dla deweloperów jest również dostępna dla klientów platformy Azure. Aby uzyskać informacje na temat zakupu planów pomocy technicznej dla deweloperów, zobacz [tę stronę](https://azure.microsoft.com/support/plans/developer/).

* Jeśli masz już plan pomocy technicznej platformy Azure, [otwórz żądanie pomocy technicznej tutaj](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

* Jeśli nie jesteś klientem platformy Azure, możesz również otworzyć żądanie pomocy technicznej w firmie Microsoft za pośrednictwem [naszej komercyjnej pomocy technicznej.](https://support.microsoft.com/en-us/gp/contactus81?Audience=Commercial)

Możesz również spróbować [wirtualnego agenta,](https://support.microsoft.com/contactus/?ws=support) aby uzyskać pomoc techniczną lub zadać pytania.
