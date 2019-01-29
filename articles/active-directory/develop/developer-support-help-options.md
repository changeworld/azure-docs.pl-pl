---
title: Opcje pomocy technicznej i pomocy dla deweloperów usługi Azure Identity | Dokumentacja firmy Microsoft
description: Wiesz, jak uzyskać pomoc i obsługa techniczna dla problemów i pytania dotyczące projektowania, podczas tworzenia aplikacji, które integrują się z tożsamości Microsoft Azure (Azure Active Directory i zarządzanych kont usług)
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
ms.date: 10/27/2017
ms.author: celested
ms.reviewer: andret
ms.custom: aaddev
ms.openlocfilehash: f8c5e5f598ab8566eacb594ff66b63ce3793f57f
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2019
ms.locfileid: "55093200"
---
# <a name="support-and-help-options-for-developers"></a>Opcje pomocy technicznej i pomocy dla deweloperów

Niezależnie od tego, czy właśnie zaczynają Integracja z usługą Azure Active Directory, Microsoft tożsamości lub interfejsu API programu Microsoft Graph, implementując nowej funkcji do aplikacji, to, ile razy należy uzyskać pomoc od społeczności, lub poznać Opcje pomocy technicznej, które mają dla deweloperów. Ten artykuł zawiera informacje o tych opcjach, poniżej podsumowania:

> [!div class="checklist"]
> * Wyszukaj, aby sprawdzić, czy Twoje pytanie problem ma bez odpowiedzi przez społeczność, czy istniejąca dokumentacja dotycząca funkcji, który próbujesz wdrożyć już istnieje
> * W niektórych przypadkach po prostu chcesz korzystanie z naszych narzędzi pomocy technicznej w celu ułatwienia debugowania określonego problemu
> * Jeśli nie możesz znaleźć odpowiedź na pytanie, z czego potrzebujesz, warto zadać pytanie na *Stack Overflow*
> * Jeśli znajdziesz problem z jednym z naszych bibliotek uwierzytelniania, podnieść *GitHub* problem
> * Na koniec Jeśli potrzebujesz, możesz chcieć Otwórz żądanie obsługi


## <a name="search"></a>Wyszukiwanie

Jeśli masz pytania dotyczące programowania, można znaleźć odpowiedzi, należy na naszej dokumentacji naszych [przykłady dla usługi GitHub](https://github.com/azure-samples), lub odpowiedzi na [Stack Overflow](https://www.stackoverflow.com) pytania.

### <a name="scoped-search"></a>Wyszukiwania zakresowego
Aby szybciej uzyskać wyniki, należy ograniczyć wyszukiwanie do Stack Overflow, dokumentację i nasze przykłady kodu za pomocą następujących na Twoje [ulubiona Wyszukiwarka](https://bing.com):
```
{Your Search Terms} (site:stackoverflow.com OR site:docs.microsoft.com OR site:github.com/azure-samples OR site:cloudidentity.com OR site:developer.microsoft.com/en-us/graph)
```
Gdzie *{Your wyszukiwane terminy}* są wyszukiwane słowa kluczowe.
<br/>

## <a name="use-our-development-support-tools"></a>Użyj naszych narzędzi obsługi programowania

|Narzędzie  |Opis  |
|---------|---------|
|[jwt.ms](https://jwt.ms)| Wklej identyfikator lub dostęp do tokenów do zdekodowania nazwy oświadczenia i wartości |
|[Analizator kodu błędu](https://apps.dev.microsoft.com/portal/tools/errors)| Wklej odebrany kod błędu podczas logowania lub wyrazić zgodę strony, aby wyświetlić możliwe przyczyny i korygowania funkcję |
|[Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)| Narzędzie, które pozwala wysyłać żądania i odpowiedzi dla interfejsu API programu Microsoft Graph|

<br/>

[![Stack Overflow](./media/developer-support-help-options/stackoverflow-logo.png)](https://www.stackoverflow.com)
## <a name="post-a-question-to-stack-overflow"></a>Zamieść pytanie na przepełnienie stosu

Przepełnienie stosu jest preferowany kanał w razie pytań związanych z opracowywaniem — gdzie oba elementy społeczność jako Microsoft członkowie zespołu zaangażowani w ułatwienia możesz rozwiązać tego problemu.

Jeśli nie możesz znaleźć odpowiedzi na problem za pomocą wyszukiwania, Prześlij nowe pytanie do witryny Stack Overflow: Użyj jednego z następujących tagów, podczas wprowadzania pytania, aby ułatwić społeczności zidentyfikować, następnie odpowiedź na Twoje pytanie w sposób terminowy:

|Składnik/obszaru  |Tagi  |
|---------|---------|
|Biblioteka ADAL |[[adal]](https://stackoverflow.com/questions/tagged/adal)|
|Biblioteka MSAL     |[[msal]](https://stackoverflow.com/questions/tagged/msal)|
|Oprogramowanie pośredniczące OWIN  |[[azure-active-directory]](https://stackoverflow.com/questions/tagged/azure-active-directory)|
|[Azure B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)  |[[azure-ad-b2b]](https://stackoverflow.com/questions/tagged/azure-ad-b2b)|
|[Azure B2C](https://azure.microsoft.com/services/active-directory-b2c/)  |[[azure-ad-b2c]](https://stackoverflow.com/questions/tagged/azure-ad-b2b)|
|[Interfejs API programu Microsoft Graph](https://developer.microsoft.com/graph/) |[[microsoft-graph]](https://stackoverflow.com/questions/tagged/microsoft-graph)
|Wszelkich innych aspektów do tematów, uwierzytelniania i autoryzacji |[[azure-active-directory]](https://stackoverflow.com/questions/tagged/azure-active-directory)
<br/>
> [!TIP]
> Następujące wpisy w witrynie Stack Overflow zawiera wskazówki dotyczące sposobu wprowadzania pytania i porady na temat dodawania kodu źródłowego - poniższych wskazówek może pomóc zwiększyć prawdopodobieństwo, że członków społeczności, aby ocenić i szybko reagować na Twoje pytanie:
> - [Jak zadać pytanie dobre](https://stackoverflow.com/help/how-to-ask)
> - [Jak utworzyć minimalny, Zakończ i możliwe do zweryfikowania przykładowego](https://stackoverflow.com/help/mcve)

<br/>


[![Stack Overflow](./media/developer-support-help-options/github-logo.png)](https://www.github.com)
## <a name="create-a-github-issue"></a>Utwórz problem usługi GitHub

 Jeśli znajdziesz błąd lub problem związany z naszych bibliotek, należy zgłosić problem w repozytoriach usługi GitHub. Ponieważ skorzystać z bibliotek typu open source, również są bezpłatne przesłać żądanie ściągnięcia, który jest również. Następujący artykuł zawiera listę bibliotek i ich repozytoriów GitHub:

- [Biblioteki ADAL, biblioteka MSAL i Owin oprogramowanie pośredniczące](active-directory-authentication-libraries.md) bibliotek i repozytoria GitHub

<br/>

## <a name="open-a-support-request"></a>Otwórz żądanie obsługi

Jeśli potrzebujesz, możesz otworzyć żądania pomocy technicznej. Jeśli korzystasz z platformy Azure, dostępnych kilka opcji pomocy technicznej. Aby porównać planów, zobacz [tę stronę](https://azure.microsoft.com/support/plans/). Pomoc techniczna Developer jest również dostępna dla klientów platformy Azure. Aby uzyskać informacje o sposobie zakupu planów pomocy technicznej Developer, zobacz [tę stronę](https://azure.microsoft.com/support/plans/developer/).

- Jeśli masz już Azure Plan pomocy technicznej, [Otwórz żądanie obsługi w tym miejscu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

- Jeśli nie jesteś klientem platformy Azure, można również otworzyć żądania pomocy technicznej z firmą Microsoft za pośrednictwem [obsłudze komercyjnych](https://support.microsoft.com/en-us/gp/contactus81?Audience=Commercial).

Możesz też spróbować [naszego agenta wirtualnego](https://support.microsoft.com/contactus/?ws=support) do uzyskiwania pomocy technicznej lub zadać pytania.

### <a name="free-chat-support-for-a-limited-time"></a>Obsługa rozmowy bezpłatne przez ograniczony czas

Umożliwia także obsłudze rozmowy — która jest bezpłatna for Partners firmy Microsoft przez ograniczony czas. Jeśli Twoja firma nie jest partnerem firmy Microsoft, możesz zarejestrować go za darmo i uzyskać inne korzyści, przechodząc [tutaj](https://partners.microsoft.com/PartnerProgram/simplifiedenrollment.aspx).

Po zarejestrowaniu Twojej firmy, możesz rozpocząć żądanie czatu [tutaj](https://aka.ms/devchat).
