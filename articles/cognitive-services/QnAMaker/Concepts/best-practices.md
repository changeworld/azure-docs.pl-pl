---
title: Najlepsze praktyki kognitywnych usług Azure - Maker — strona główna - | Dokumentacja firmy Microsoft
description: Aby poprawić bazy wiedzy i udostępnić lepsze wyniki użytkownikom końcowym bot Twojej aplikacji/rozmowy, należy użyć następujące najlepsze rozwiązania.
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 05/07/2018
ms.author: saneppal
ms.openlocfilehash: 7a85ebbc3892a90e98e73a73425c1f8ec1de0b35
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "35349901"
---
# <a name="best-practices"></a>Najlepsze praktyki
[Cyklu programistycznym wiedzy](../Concepts/development-lifecycle-knowledge-base.md) prowadzi użytkownika na temat zarządzania należy KB end-to-end. Aby poprawić bazy wiedzy i udostępnić lepsze wyniki użytkownikom końcowym bot Twojej aplikacji/rozmowy, należy użyć następujące najlepsze rozwiązania.

## <a name="extraction"></a>Wyodrębniania
Maker — strona główna jest stale poprawy algorytmów wyodrębniania QnAs zawartości i rozwijania listy plików i obsługiwane formaty strony HTML. Postępuj zgodnie z [wytyczne](../Concepts/data-sources-supported.md) dla wyodrębniania na podstawie typu dokumentu czy wyodrębniania z. 

Ogólnie rzecz biorąc często zadawane pytania dotyczące stron powinna być autonomiczne i nie są połączone z innymi informacjami. Podręczniki powinny mieć wyczyść nagłówki, najlepiej strony indeksu. 

## <a name="rankingmatching"></a>Klasyfikacja/dopasowania
Upewnij się, że dokonywania najlepsze wykorzystanie funkcje klasyfikacji, które obsługuje Maker — strona główna. Dzięki temu zwiększy prawdopodobieństwo który zapytania danego użytkownika zostanie odebrane z kolei odpowiednią odpowiedź.

### <a name="add-alternate-questions"></a>Dodaj alternatywne pytania
[Alternatywny pytania](../How-To/edit-knowledge-base.md) zwiększyć prawdopodobieństwo zgodny z zapytaniem użytkownika. Alternatywne pytania są przydatne, gdy istnieje wiele sposobów, w którym może zostać wyświetlony monit tego samego zapytania. Może to obejmować zmian w strukturze zdanie (na przykład *"Jest dostępne postojowego"?* a *"Masz wstrzymywanie samochód?"* ) lub zmiany w stylu programu word i żargon (na przykład *"Hi"* i *"Yo"*, *"Brak Witaj!"* ).

### <a name="use-metadata-filters"></a>Używanie filtrów metadanych
[Metadane](../How-To/edit-knowledge-base.md) dodaje możliwość zawęzić liczbę wyników kwerendy użytkownika oparte na filtrów. Odpowiedzi bazy wiedzy knowledge base może się różnić w zależności w tagu metadanych, nawet jeśli zapytanie jest taka sama. Na przykład *"gdzie jest postojowego znajduje się"* mogą mieć różne odpowiedzi, jeśli różni się lokalizację gałęzi restauracji — metadanych jest *lokalizacji: Seattle* i *lokalizacji: Redmond*.)

### <a name="use-synonyms"></a>Użyj synonimy
Chociaż niektóre obsługę synonimy w języku angielskim, użyj [zmiany w programie word](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75fd) można dodać synonimy słów kluczowych, które przyjmują innej formie (przykład: *kupić* -> *zakupu*  lub *netbanking* -> *net banku*. Synonimy należy dodać na poziomie usługi Maker — strona główna i współużytkowane przez wszystkie bazy wiedzy w usłudze.

### <a name="use-distinct-words-to-differentiate-questions"></a>Umożliwia rozróżnianie pytania unikatowych słów
Algorytmy dopasowania rangę Maker — strona główna, zgodne z zapytaniem użytkownika, z zapytania w bazie wiedzy knowledge base działają najlepiej, jeśli pytania adresów różnych potrzeb. Powtarzania tej samej Word ustawiony między wartością pytania zmniejsza prawdopodobieństwo, że wybrano prawo odpowiedzi dla danego użytkownika zapytania słowa.

## <a name="collaborate"></a>Współpraca
Maker — strona główna umożliwia użytkownikom [współpracę](../How-to/collaborate-knowledge-base.md) w bazie wiedzy. Użytkownicy potrzebują dostępu do grupy zasobów platformy Azure — strona główna Maker, aby uzyskać dostęp do bazy wiedzy. W niektórych organizacjach może być zewnętrzny edycji wiedzy i konserwacji i nadal mieć możliwość ochrony dostępu do ich zasobów platformy Azure. Ten model osoba zatwierdzająca edytora można wykonywać, konfigurując dwa identyczne [Maker — strona główna usług](../How-to/set-up-qnamaker-service-azure.md) w ramach różnych subskrypcji i wyznaczenie jednego cyklu testowania edycji. Po zakończeniu testowania można przesyłać zawartość wiedzy z [importowania i eksportowania](../Tutorials/migrate-knowledge-base.md) procesu z usługą Maker — strona główna osoba zatwierdzająca zostanie ostatecznie opublikować w bazie wiedzy i zaktualizować punktu końcowego.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Edytuj bazy wiedzy](../How-to/edit-knowledge-base.md)

