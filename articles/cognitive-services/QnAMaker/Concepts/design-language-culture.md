---
title: Projektowanie dla języka - QnA Maker
description: Zasób QnA Maker i wszystkie bazy wiedzy wewnątrz tego zasobu obsługują jeden język. Pojedynczy język jest niezbędny do zapewnienia najlepszych wyników odpowiedzi dla zapytania.
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: 5cb1dcd35649debbafd2e234606ad4c9d6906ea6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "76843424"
---
# <a name="design-knowledge-base-for-content-language"></a>Projektowanie bazy wiedzy dla języka zawartości

Zasób QnA Maker i wszystkie bazy wiedzy wewnątrz tego zasobu obsługują jeden język. Pojedynczy język jest niezbędny do zapewnienia najlepszych wyników odpowiedzi dla zapytania.

## <a name="single-language-per-resource"></a>Jeden język na zasób

Zagadnienia dotyczące programu QnA Maker dotyczące obsługi języków:

* Usługa QnA Maker i wszystkie jej bazy wiedzy obsługują tylko jeden język.
* Język jest jawnie ustawiany podczas tworzenia pierwszej bazy wiedzy usługi
* Język jest określany na podstawie plików i adresów URL dodanych podczas tworzenia bazy wiedzy
* Nie można zmienić języka dla innych baz wiedzy w usłudze
* Język jest używany przez usługę Wyszukiwania Poznawczego (ranker #1) i usługę QnA Maker (ranker #2) do generowania najlepszej odpowiedzi na zapytanie

## <a name="supporting-multiple-languages"></a>Obsługa wielu języków

Jeśli potrzebujesz obsługi systemu bazy wiedzy, który zawiera kilka języków, możesz wybrać jedną z następujących metod:

* Użyj [usługi Tłumaczenia tekstu,](../../translator/translator-info-overview.md) aby przetłumaczyć pytanie na jeden język przed wysłaniem pytania do bazy wiedzy. Pozwala to skupić się na jakości jednego języka i jakości alternatywnych pytań i odpowiedzi.
* Utwórz zasób programu QnA Maker i bazę wiedzy wewnątrz tego zasobu dla każdego języka. Dzięki temu można zarządzać oddzielnymi pytaniami alternatywnymi i odpowiadać na tekst, który jest bardziej zniuansowany dla każdego języka. Zapewnia to znacznie większą elastyczność, ale wymaga znacznie wyższych kosztów konserwacji, gdy pytania lub odpowiedzi zmieniają się we wszystkich językach.

Przejrzyj [języki obsługiwane](../overview/language-support.md) przez qnA maker.

### <a name="support-each-language-with-a-qna-maker-resource"></a>Obsługa każdego języka za pomocą zasobu programu QnA Maker

* Tworzenie zasobu programu QnA Maker dla każdego języka
* Dodawanie plików i adresów URL tylko dla tego języka
* Użyj konwencji nazewnictwa dla zasobu, aby zidentyfikować język. Przykładem `qna-maker-fr` jest dla wszystkich baz wiedzy dla dokumentów francuskich

## <a name="next-steps"></a>Następne kroki

Poznaj [pojęcia](query-knowledge-base.md) dotyczące sposobu wykonywania zapytań do bazy wiedzy o odpowiedź.