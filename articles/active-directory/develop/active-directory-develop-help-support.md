---
title: Opcje pomocy i obsługi technicznej dla deweloperów usługi Azure Identity | Dokumentacja firmy Microsoft
description: Wiedzieć, jak uzyskać pomoc i obsługę problemów i pytania związane z rozwojem podczas tworzenia aplikacji, które integrują się z tożsamości Microsoft Azure (Azure Active Directory i zarządzanych kont usług)
services: active-directory
documentationcenter: dev-center-name
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/27/2017
ms.author: celested
ms.reviewer: andret
ms.custom: aaddev
ms.openlocfilehash: ae682830a8d0e2c5c27211a3396d04c881c41585
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
---
# <a name="support-and-help-options-for-developers"></a>Opcje pomocy i obsługi technicznej dla deweloperów 

Niezależnie od tego, czy uruchamiana jest integrowana z usługi Azure Active Directory, Microsoft tożsamości lub interfejsu API programu Graph firmy Microsoft, implementując nowej funkcji do aplikacji, są razy, które należy uzyskać pomoc od społeczności i zrozumienie Opcje pomocy technicznej, takiej jak dewelopera. Ten artykuł ułatwia zrozumienie poniższych podsumowanie tych opcji:

> [!div class="checklist"]
> * Wyszukiwanie, aby sprawdzić, czy problem pytanie nie ma odpowiedzi przez społeczność Jeśli istniejąca dokumentacja dotycząca funkcję próbujesz wdrożyć już istnieje
> * W niektórych przypadkach po prostu chcesz użycie narzędziach pomocy technicznej firmy Microsoft w celu ułatwienia debugowania konkretnego problemu
> * Jeśli nie możesz znaleźć odpowiedzi od co jest potrzebne, może zajść potrzeba Zadaj pytanie na *przepełnienie stosu*
> * Jeśli napotkasz problem z jednym z naszych bibliotek uwierzytelniania podnieść *GitHub* problem
> * Ponadto należy można otworzyć żądania obsługi


## <a name="search"></a>Wyszukiwanie

Jeśli masz pytanie dotyczące programowania, można znaleźć odpowiedzi, należy w naszej dokumentacji naszych [przykłady github](https://github.com/azure-samples), lub odpowiedzi na [przepełnienie stosu](https://www.stackoverflow.com) pytania.

### <a name="scoped-search"></a>Ukierunkowane wyszukiwanie
Szybszych efektów, określić zakres wyszukiwania przepełnienie stosu naszej dokumentacji i nasze przykłady kodu przy użyciu następujących na Twojej [ulubiona Wyszukiwarka](https://bing.com):
```
{Your Search Terms} (site:stackoverflow.com OR site:docs.microsoft.com OR site:github.com/azure-samples OR site:cloudidentity.com OR site:developer.microsoft.com/en-us/graph)
```
Gdzie *{Your terminy wyszukiwania}* jest słowa kluczowe wyszukiwania.
<br/>

## <a name="use-our-development-support-tools"></a>Użyj narzędzia obsługi naszych programowanie

|Narzędzie  |Opis  |
|---------|---------|
|[jwt.MS](https://jwt.ms)| Wklej identyfikator lub dostęp do tokenów celu zdekodowania nazwy oświadczeń i wartości |
|[Analizator kodu błędu](https://apps.dev.microsoft.com/portal/tools/errors)| Wklej kod błędu otrzymał podczas logowania lub zgody strony, aby wyświetlić możliwe przyczyny i korygowania funkcję |
|[Eksplorator Microsoft Graph](https://developer.microsoft.com/graph/graph-explorer)| Narzędzie, które umożliwia wysyłanie żądań i odpowiedzi z interfejsu API programu Microsoft Graph|

<br/>

[![Przepełnienie stosu](media/active-directory-develop-help-support/stackoverflow-logo.png)](https://www.stackoverflow.com)
## <a name="post-a-question-to-stack-overflow"></a>Zadaj pytanie przepełnienie stosu

Przepełnienie stosu jest preferowany kanał odpowiedzi na pytania związane z rozwojem - gdzie oba członków społeczności jako Microsoft członkowie zespołu są bezpośrednio związane z na pomaga rozwiązać problem.

Jeśli nie możesz znaleźć rozwiązania swojego problemu za pomocą wyszukiwania, należy przesłać nowe pytanie do przepełnienia stosu: Użyj jednego z następujących tagów podczas wprowadzania pytania, aby ułatwić społeczności zidentyfikować, następnie odpowiedź na Twoje pytanie, w sposób terminowy:

|Składnik/obszaru  |Tagi  |
|---------|---------|
|Biblioteka ADAL |[[adal]](http://stackoverflow.com/questions/tagged/adal)|
|Biblioteka MSAL     |[[msal]](http://stackoverflow.com/questions/tagged/msal)|
|Oprogramowanie pośredniczące OWIN  |[[azure-active-directory]](http://stackoverflow.com/questions/tagged/azure-active-directory)|
|[Funkcje B2B platformy Azure](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)  |[[ruch b2b usługi azure ad]](http://stackoverflow.com/questions/tagged/azure-ad-b2b)|
|[Usługa Azure B2C](https://azure.microsoft.com/services/active-directory-b2c/)  |[[azure-ad b2c]](http://stackoverflow.com/questions/tagged/azure-ad-b2b)|
|[Program Microsoft Graph API](https://developer.microsoft.com/graph/) |[[microsoft graph]](http://stackoverflow.com/questions/tagged/microsoft-graph)
|Wszelkich innych aspektów do tematów, uwierzytelniania i autoryzacji |[[azure-active-directory]](http://stackoverflow.com/questions/tagged/azure-active-directory)
<br/>
> [!TIP]
> Następujące wpisy z przepełnienie stosu zawiera wskazówki dotyczące sposobu wprowadzania pytania i porady na kod źródłowy — następujące wskazówki mogą zwiększyć prawdopodobieństwo członków społeczności, aby ocenić i szybko reagować na swoje pytanie:  
> - [Jak zadać pytanie dobrej](https://stackoverflow.com/help/how-to-ask)
> - [Jak utworzyć minimalnym, pełnej i przykład możliwe do zweryfikowania](https://stackoverflow.com/help/mcve)

<br/>


[![Przepełnienie stosu](media/active-directory-develop-help-support/github-logo.png)](https://www.github.com)
## <a name="create-a-github-issue"></a>Utwórz problem GitHub

 Jeśli napotkasz błąd lub problem związany z naszych bibliotek Zgłoś problem na naszych repozytoriów GitHub. Ponieważ naszych biblioteki typu open source, również są mógł przesłać żądanie ściągnięcia, jak również. Artykuł zawiera listę bibliotek i ich repozytoriów GitHub:

- [Biblioteki ADAL, MSAL i Owin oprogramowanie pośredniczące](active-directory-authentication-libraries.md) bibliotek i repozytoriów GitHub

<br/>

## <a name="open-a-support-request"></a>Otwórz żądanie pomocy technicznej

Jeśli potrzebujesz, można otworzyć żądania obsługi. Jeśli klient usługi Azure, Brak dostępnych kilka opcji pomocy technicznej. Aby porównać planów, zobacz [tej strony](https://azure.microsoft.com/support/plans/). Pomoc techniczna Developer jest również dostępna dla klientów platformy Azure. Aby uzyskać informacje na temat sposobu zakupu plany pomocy technicznej Developer, zobacz [tej strony](https://azure.microsoft.com/support/plans/developer/).

- Jeśli masz już Azure obsługuje planowanie, [otwarcia żądania pomocy technicznej w tym miejscu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

- Jeśli nie masz Azure klienta, można również otworzyć żądania obsługi z firmą Microsoft za pośrednictwem [komercyjnych technicznej](https://support.microsoft.com/en-us/gp/contactus81?Audience=Commercial).

Można też spróbować [naszych wirtualnych agenta](https://support.microsoft.com/contactus/?ws=support) do uzyskiwania pomocy technicznej lub zadać pytania.

### <a name="free-chat-support-for-a-limited-time"></a>Obsługa wolnych rozmów przez ograniczony czas

Można również użyć technicznej rozmów — czyli wolnego for Microsoft Partners przez ograniczony czas. Jeśli firma nie jest partnera firmy Microsoft, możesz rejestrować je bezpłatnie i uzyskać inne korzyści, przechodząc [tutaj](https://partners.microsoft.com/PartnerProgram/simplifiedenrollment.aspx).

Po zarejestrowaniu firmy, możesz uruchomić żądanie czatu [tutaj](https://aka.ms/devchat).