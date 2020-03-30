---
title: Omówienie diagnostyki usług Azure Functions
description: Dowiedz się, jak rozwiązać problemy z aplikacją funkcji za pomocą diagnostyki usług Azure Functions.
author: yunjchoi
ms.topic: article
ms.date: 11/01/2019
ms.author: yunjchoi
ms.custom: na
ms.openlocfilehash: 41acc5703f6b6e5cdeedf2afd0dc61b23bc579c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75834048"
---
# <a name="azure-functions-diagnostics-overview"></a>Omówienie diagnostyki usług Azure Functions

Podczas uruchamiania aplikacji funkcji, chcesz być przygotowany na wszelkie problemy, które mogą wystąpić, z błędów 4xx do wyzwalania błędów. Diagnostyka usług Azure Functions to inteligentne i interaktywne środowisko ułatwiające rozwiązywanie problemów z aplikacją funkcji bez konieczności konfigurowania i dodatkowych kosztów. Po napotkaniu problemów z aplikacją funkcji diagnostyka usługi Azure Functions wskazuje, co jest nie tak, aby poprowadzić Cię do właściwych informacji, aby łatwiej i szybciej rozwiązywać i rozwiązywać problem. W tym artykule przedstawiono podstawy korzystania z diagnostyki usługi Azure Functions w celu szybszego diagnozowania i rozwiązywania typowych problemów z aplikacją funkcji.

## <a name="start-azure-functions-diagnostics"></a>Uruchamianie diagnostyki usług Azure Functions

Aby uzyskać dostęp do diagnostyki usług Azure Functions:

1. Przejdź do aplikacji funkcji w [witrynie Azure portal](https://portal.azure.com).
2. Wybierz kartę **Operacje platformy.**
3. Wybierz **opcję Diagnozuj i rozwiązuj problemy** w obszarze Zarządzanie **zasobami**, który otwiera diagnostykę usługi Azure Functions.
4. Wybierz kategorię, która najlepiej opisuje problem aplikacji funkcji przy użyciu słów kluczowych na kafelku strony głównej. Możesz też wpisać słowo kluczowe, które najlepiej opisuje twój problem na pasku wyszukiwania. Na przykład można `execution` wpisać, aby wyświetlić listę raportów diagnostycznych związanych z wykonywaniem aplikacji funkcji i otworzyć je bezpośrednio ze strony głównej.

![Strona główna](./media/functions-diagnostics/homepage.png)

## <a name="use-the-interactive-interface"></a>Korzystanie z interfejsu interaktywnego

Po wybraniu kategorii strony głównej, która najlepiej jest zgodna z problemem aplikacji funkcji, interaktywny interfejs diagnostyki usługi Azure Functions, Genie, może poprowadzić Cię przez diagnozowanie i rozwiązywanie problemów aplikacji. Możesz użyć skrótów kafelków dostarczonych przez firmę Genie, aby wyświetlić pełny raport diagnostyczny kategorii problemu, która Cię interesuje. Skróty kafelków zapewniają bezpośredni sposób uzyskiwania dostępu do metryk diagnostycznych.

![Genie](./media/functions-diagnostics/genie.png)

Po wybraniu kafelka można wyświetlić listę tematów związanych z problemem opisanym na kafelku. Tematy te zawierają fragmenty istotnych informacji z pełnego raportu. Można wybrać dowolny z tych tematów, aby zbadać problemy dalej. Ponadto można wybrać **opcję Wyświetl pełny raport,** aby eksplorować wszystkie tematy na jednej stronie.

![Podgląd raportu diagnostycznego](./media/functions-diagnostics/preview-of-diagnostic-report.png)

## <a name="view-a-diagnostic-report"></a>Wyświetlanie raportu diagnostycznego

Po wybraniu tematu można wyświetlić raport diagnostyczny specyficzny dla aplikacji funkcji. Raporty diagnostyczne używają ikon stanu, aby wskazać, czy występują jakieś określone problemy z aplikacją. Zobaczysz szczegółowy opis problemu, zalecane akcje, powiązane metryki i pomocne dokumenty. Dostosowane raporty diagnostyczne są generowane z serii kontroli uruchamianych w aplikacji funkcji. Raporty diagnostyczne mogą być przydatnym narzędziem do wskazywania problemów w aplikacji funkcji i prowadząc do rozwiązania problemu.

## <a name="find-the-problem-code"></a>Znajdź kod problemu

W przypadku funkcji opartych na skryptach można użyć **wykonywania funkcji i błędów** w obszarze **Zmniejszanie aplikacji funkcji lub błędy raportowania,** aby zawęzić wiersz kodu powodujący wyjątki lub błędy. Ta funkcja może być przydatnym narzędziem do dotarcie do głównej przyczyny i naprawienie problemów z określonego wiersza kodu. Ta opcja nie jest dostępna dla wstępnie skompilowanych funkcji języka C# i Java.

![Raport diagnostyczny dotyczący błędów wykonywania funkcji](./media/functions-diagnostics/diagnostic-report-on-function-execution-errors.png)

![Wyjątek funkcji](./media/functions-diagnostics/function-exception.png)

## <a name="next-steps"></a>Następne kroki

Możesz zadawać pytania lub przekazywać opinie na temat diagnostyki usług Azure Functions w [witrynie UserVoice](https://feedback.azure.com/forums/355860-azure-functions). Proszę `[Diag]` podać w tytule swojej opinii.

> [!div class="nextstepaction"]
> [Monitorowanie aplikacji funkcyjnych](functions-monitoring.md)
