---
title: Opcje pomocy technicznej i pomocy dla deweloperów aplikacji usługi Azure AD | Dokumentacja firmy Microsoft
description: Wiesz, jak uzyskać pomoc i obsługa techniczna dla problemów i pytania dotyczące projektowania, podczas tworzenia aplikacji, które integrują się z tożsamości firmy Microsoft (konto usługi Azure Active Directory i Microsoft)
services: active-directory
documentationcenter: dev-center-name
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/14/2019
ms.author: celested
ms.reviewer: jmprieur, dadobali
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0de9b3e3535253724d068f806b219736f6889477
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59501599"
---
# <a name="support-and-help-options-for-developers"></a>Opcje pomocy technicznej i pomocy dla deweloperów

Jeśli dopiero zaczynasz do integracji z usługą Azure Active Directory (Azure AD), tożsamości firmy Microsoft lub interfejsu API Microsoft Graph lub gdy w przypadku wdrażania nowej funkcji do aplikacji, są razy, aby uzyskać pomoc od społeczności lub zrozumieć Opcje pomocy technicznej, które mają dla deweloperów. Ten artykuł ułatwi Ci poznanie tych opcji, w tym:

> [!div class="checklist"]
> * Jak wyszukiwać, czy Twoje pytanie nie udzielono odpowiedzi przez społeczność, czy też istniejąca dokumentacja dotycząca funkcji, który próbujesz wdrożyć już istnieje
> * W niektórych przypadkach po prostu chcesz używać naszych narzędzi pomocy technicznej w celu ułatwienia debugowania konkretnego problemu
> * Jeśli nie możesz znaleźć odpowiedzi, które są potrzebne, warto zadać pytanie na *Stack Overflow*
> * Jeśli znajdziesz problem z jednym z naszych bibliotek uwierzytelniania, podnieść *GitHub* problem
> * Na koniec Jeśli potrzebujesz, możesz chcieć Otwórz żądanie obsługi

## <a name="search"></a>Wyszukiwanie

Jeśli masz pytania dotyczące programowania, można znaleźć w dokumentacji, [przykłady dla usługi GitHub](https://github.com/azure-samples), lub odpowiedzi na [Stack Overflow](https://www.stackoverflow.com) pytania.

### <a name="scoped-search"></a>Wyszukiwania zakresowego

Aby szybciej uzyskać wyniki należy ograniczyć wyszukiwanie do witryny Stack Overflow, dokumentacji oraz przykładów kodu przy użyciu następującej kwerendy w ulubiona Wyszukiwarka:

```
{Your Search Terms} (site:stackoverflow.com OR site:docs.microsoft.com OR site:github.com/azure-samples OR site:cloudidentity.com OR site:developer.microsoft.com/graph)
```

Gdzie *{Your wyszukiwane terminy}* odpowiadają słowa kluczowe wyszukiwania.

## <a name="use-the-development-support-tools"></a>Używanie narzędzi obsługi programowania

| Tool  | Opis  |
|---------|---------|
| [jwt.ms](https://jwt.ms) | Wklej identyfikator lub tokenu dostępu w celu zdekodowania nazwy oświadczenia i wartości. |
| [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)| Narzędzie, które pozwala wysyłać żądania i odpowiedzi dla interfejsu API programu Microsoft Graph. |

## <a name="post-a-question-to-stack-overflow"></a>Zamieść pytanie na przepełnienie stosu

Przepełnienie stosu jest preferowany kanał w przypadku pytań związanych z opracowywaniem. W tym miejscu członkowie społeczności deweloperów i członkowie zespołu Microsoft są zaangażowani w pomoc w rozwiązywaniu problemów.

Jeśli nie możesz znaleźć odpowiedź na Twoje pytanie za pomocą funkcji wyszukiwania, Prześlij nowe pytanie do przepełnienia stosu. Zadawanie pytań, aby pomóc zidentyfikować i szybciej uzyskać odpowiedzi na swoje pytanie społeczności, należy użyć jednej z następujących tagów:

|Składnik/obszaru  | Tagi |
|---------|---------|
| Biblioteka ADAL | [[adal]](https://stackoverflow.com/questions/tagged/adal) |
| Biblioteka MSAL     | [[msal]](https://stackoverflow.com/questions/tagged/msal) |
| Oprogramowanie pośredniczące OWIN  | [[azure-active-directory]](https://stackoverflow.com/questions/tagged/azure-active-directory) |
| [Azure B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)  | [[azure-ad-b2b]](https://stackoverflow.com/questions/tagged/azure-ad-b2b) |
| [Azure B2C](https://azure.microsoft.com/services/active-directory-b2c/)  | [[azure-ad-b2c]](https://stackoverflow.com/questions/tagged/azure-ad-b2c) |
| [Interfejs API programu Microsoft Graph](https://developer.microsoft.com/graph/) | [[microsoft-graph]](https://stackoverflow.com/questions/tagged/microsoft-graph) |
| Wszelkich innych aspektów do tematów, uwierzytelniania i autoryzacji | [[azure-active-directory]](https://stackoverflow.com/questions/tagged/azure-active-directory) |

Następujące wpisy w witrynie Stack Overflow zawiera porady dotyczące zadawanie pytań oraz dodawanie kodu źródłowego. Należy przestrzegać następujących wytycznych, aby zwiększyć prawdopodobieństwo, że członków społeczności, aby ocenić i szybko reagować na Twoje pytanie:

* [Jak zadać pytanie dobre](https://stackoverflow.com/help/how-to-ask)
* [Jak utworzyć przykład minimalny, kompletne i możliwe do zweryfikowania](https://stackoverflow.com/help/mcve)

## <a name="create-a-github-issue"></a>Utwórz problem usługi GitHub

Jeśli znajdziesz błąd lub problem związany z naszych bibliotek, należy zgłosić problem w repozytoriach usługi GitHub. Ponieważ skorzystać z bibliotek typu open source, możesz również przekazywać żądania ściągnięcia.

Aby wyświetlić listę bibliotek i ich repozytoriów GitHub, zobacz następujące artykuły:

* [Biblioteki ADAL](active-directory-authentication-libraries.md) bibliotek i repozytoria GitHub
* [Biblioteka MSAL](reference-v2-libraries.md) bibliotek i repozytoria GitHub

## <a name="open-a-support-request"></a>Otwórz żądanie obsługi

Jeśli potrzebujesz, możesz otworzyć żądania pomocy technicznej. Jeśli korzystasz z platformy Azure, dostępnych kilka opcji pomocy technicznej. Aby porównać planów, zobacz [tę stronę](https://azure.microsoft.com/support/plans/). Pomoc techniczna Developer jest również dostępna dla klientów platformy Azure. Aby uzyskać informacje o sposobie zakupu planów pomocy technicznej Developer, zobacz [tę stronę](https://azure.microsoft.com/support/plans/developer/).

* Jeśli masz już Azure Plan pomocy technicznej, [Otwórz żądanie obsługi w tym miejscu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

* Jeśli nie jesteś klientem platformy Azure, można również otworzyć żądania pomocy technicznej z firmą Microsoft za pośrednictwem [obsłudze komercyjnych](https://support.microsoft.com/en-us/gp/contactus81?Audience=Commercial).

Możesz też spróbować [wirtualnej agenta](https://support.microsoft.com/contactus/?ws=support) do uzyskiwania pomocy technicznej lub zadać pytania.

### <a name="free-chat-support-for-a-limited-time"></a>Obsługa rozmowy bezpłatne przez ograniczony czas

Umożliwia także obsłudze rozmowy, która jest bezpłatna for Partners firmy Microsoft przez ograniczony czas. Jeśli Twoja firma nie jest partnerem firmy Microsoft, możesz zarejestrować go za darmo i uzyskać inne korzyści, przechodząc [tutaj](https://partners.microsoft.com/PartnerProgram/simplifiedenrollment.aspx).

Po zarejestrowaniu Twojej firmy, możesz rozpocząć żądanie czatu [tutaj](https://aka.ms/devchat).
