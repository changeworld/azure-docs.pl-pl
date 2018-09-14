---
title: Najlepsze praktyki — QnA Maker
titlesuffix: Azure Cognitive Services
description: Używania tych najlepszych rozwiązań, aby usprawnić bazy wiedzy i zapewniają lepsze wyniki użytkownikom końcowym bota aplikacji/rozmowy.
services: cognitive-services
author: nstulasi
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: saneppal
ms.openlocfilehash: c82c117d149da39fba7b9a243aebb3e127540881
ms.sourcegitcommit: f983187566d165bc8540fdec5650edcc51a6350a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/13/2018
ms.locfileid: "45542935"
---
# <a name="best-practices"></a>Najlepsze praktyki
[Cykl życia projektowania bazy wiedzy knowledge base](../Concepts/development-lifecycle-knowledge-base.md) przejdziesz na temat sposobu zarządzania nimi KB end-to-end. Używania tych najlepszych rozwiązań, aby usprawnić bazy wiedzy i zapewniają lepsze wyniki użytkownikom końcowym bota aplikacji/rozmowy.

## <a name="extraction"></a>Wyodrębniania
Usługa QnA Maker jest stale poprawy algorytmów, które umożliwiają wyodrębnianie znacznie z zawartości i rozwijając listę plików i strony HTML formaty obsługiwane. Postępuj zgodnie z [wytycznych](../Concepts/data-sources-supported.md) dla wyodrębniania na podstawie typu dokumentu używany jest wyodrębniania z. 

Ogólnie rzecz biorąc często zadawane pytania dotyczące strony powinna być autonomiczne i nie są połączone z innymi informacjami. Podręczniki powinny mieć, usuń zaznaczenie pozycji, a najlepiej strony indeksu. 

## <a name="rankingmatching"></a>Klasyfikacja/dopasowania
Upewnij się, że wykonujesz optymalnie wykorzystać funkcje klasyfikacji, które obsługuje usługi QnA Maker. To zwiększa prawdopodobieństwo który zapytania danego użytkownika zostanie odebrane z właściwą odpowiedzią.

### <a name="add-alternate-questions"></a>Dodaj alternatywne pytania
[Alternatywny pytania](../How-To/edit-knowledge-base.md) zwiększyć prawdopodobieństwo pasują do zapytania użytkownika. Alternatywne pytania są przydatne, gdy istnieje wiele sposobów, w którym może zostać wyświetlony monit tego samego zapytania. Może to obejmować zmiany w strukturze zdanie (na przykład *"Jest dostępna parkowania?"* a *"Masz park samochodu?"* ) lub zmiany w stylu programu word i żargonu (na przykład *"Hi"* a *"Yo"*, *"Hej tam!"* ).

### <a name="use-metadata-filters"></a>Użyj filtrów metadanych
[Metadane](../How-To/edit-knowledge-base.md) dodaje możliwość zawężać wyniki zapytania użytkownika, w oparciu o filtry. Odpowiedź bazy wiedzy knowledge base może się różnić w zależności w tagu metadanych nawet, jeśli zapytanie jest taka sama. Na przykład *"gdzie jest umieszczony parkowania"* mogą mieć różne odpowiedzi, jeśli różni się lokalizacja gałęzi, restauracji — metadanych jest *lokalizacji: Seattle* a *lokalizacji: Redmond*.)

### <a name="use-synonyms"></a>Używać synonimów
Choć niektóre Obsługa synonimów w języku angielskim, użyj [word zmiany](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75fd) można dodać synonimy do słów kluczowych, które przyjmują różne formularza (przykład: *Kup* -> *zakupu*  lub *netbanking* -> *net bankowości*. Synonimy, należy dodać na poziomie usługi QnA Maker i współużytkowane przez wszystkie bazy wiedzy w usłudze.

### <a name="use-distinct-words-to-differentiate-questions"></a>Użyj unikatowych słów, aby odróżnić pytania
Usługa QnA Maker dopasowania i rangi algorytmów, zgodne z zapytaniem użytkownika, za pomocą zapytania w bazie wiedzy knowledge base działają najlepiej, jeśli każde pytanie dotyczy różne potrzeby. Powtórzenie tego samego wyrazu ustawiony między pytania zmniejsza prawdopodobieństwo, że wybrano prawidłowej odpowiedzi dla danego użytkownika zapytania za pomocą tych słów.

## <a name="collaborate"></a>Współpraca
Usługa QnA Maker umożliwia użytkownikom [współpracy](../How-to/collaborate-knowledge-base.md) na wiedzy. Użytkownicy potrzebują dostępu do grupy zasobów platformy Azure usługa QnA Maker, aby uzyskać dostęp do bazy wiedzy. W niektórych organizacjach może być oddelegowanie edytowanie wiedzy i konserwacji i nadal mieć możliwość ochrony dostępu do swoich zasobów platformy Azure. Ten model osoba zatwierdzająca edytora można osiągnąć, konfigurując dwa identyczne [usługi QnA Maker](../How-to/set-up-qnamaker-service-azure.md) w różnych subskrypcjach i wyznaczanie jednego cyklu testowania edycji. Po zakończeniu testowania można przenieść zawartości bazy wiedzy przy użyciu [import-export](../Tutorials/migrate-knowledge-base.md) procesu usługi QnA Maker osoby zatwierdzającej, która zostanie ostatecznie publikowanie bazy wiedzy knowledge base i aktualizowanie punktu końcowego.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Edytowanie wiedzy](../How-to/edit-knowledge-base.md)

