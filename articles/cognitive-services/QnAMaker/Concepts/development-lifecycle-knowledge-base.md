---
title: Cykl życia bazy wiedzy - QnA Maker
description: QnA Maker uczy się najlepiej w cyklu iteracji zmian modelu, przykłady wypowiedź, publikowanie i zbieranie danych z zapytań punktu końcowego.
ms.topic: conceptual
ms.date: 02/27/2020
ms.openlocfilehash: 98fbd81baa717c981486f33cfb2b3a608cec27c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77914956"
---
# <a name="knowledge-base-lifecycle-in-qna-maker"></a>Podstawowy cykl życia wiedzy w QnA Maker
QnA Maker uczy się najlepiej w cyklu iteracji zmian modelu, przykłady wypowiedź, publikowanie i zbieranie danych z zapytań punktu końcowego.

![Cykl tworzenia](../media/qnamaker-concepts-lifecycle/kb-lifecycle.png)

## <a name="creating-a-qna-maker-knowledge-base"></a>Tworzenie bazy wiedzy programu QnA Maker
Punkt końcowy bazy wiedzy QnA Maker (KB) zapewnia najlepszą odpowiedź na zapytanie użytkownika na podstawie zawartości bazy wiedzy KB. Tworzenie bazy wiedzy jest jednorazową akcją konfigurowania repozytorium zawartości pytań, odpowiedzi i skojarzonych metadanych. Bazę wiedzy można utworzyć, indeksując wcześniej istniejącą zawartość, taką jak strony często zadawanych pytań, podręczniki produktów lub ustrukturyzowane pary Q-A. Dowiedz się, jak [utworzyć bazę wiedzy](../quickstarts/create-publish-knowledge-base.md).

## <a name="testing-and-updating-the-knowledge-base"></a>Testowanie i aktualizowanie bazy wiedzy

Baza wiedzy jest gotowa do testowania po wypełnieniu zawartości, redakcyjnie lub poprzez automatyczne wyodrębnianie. Interaktywne testowanie można przeprowadzić w portalu QnA Maker za pośrednictwem panelu **testowego,** wprowadzając typowe zapytania użytkowników i sprawdzając, czy odpowiedzi zostały zwrócone z poprawną odpowiedzią i wystarczającym wynikiem zaufania.

* **Aby ustalić niskie wyniki zaufania:** dodaj pytania alternatywne.
* **Gdy kwerenda niepoprawnie zwraca [domyślną odpowiedź:](../How-to/change-default-answer.md)** dodaj nowe odpowiedzi do prawidłowego pytania.

Ta ciasna pętla aktualizacji testu jest kontynuowana, dopóki nie będziesz zadowolony z wyników. Dowiedz się, jak [przetestować swoją bazę wiedzy](../How-To/test-knowledge-base.md).

W przypadku dużych kb należy użyć automatycznego testowania `isTest` za pomocą interfejsu `test` [API generateAnswer](../how-to/metadata-generateanswer-usage.md#get-answer-predictions-with-the-generateanswer-api) i właściwości body, która wysyła zapytania do bazy wiedzy zamiast opublikowanej bazy wiedzy.

```json
{
  "question": "example question",
  "top": 3,
  "userId": "Default",
  "isTest": true
}
```

## <a name="publish-the-knowledge-base"></a>Publikowanie bazy wiedzy
Po zakończeniu testowania bazy wiedzy można ją opublikować. Publikowanie wypycha najnowszą wersję testowanego baza wiedzy do dedykowanego indeksu usługi Azure Cognitive Search reprezentującego **opublikowaną** bazę wiedzy. Zostanie również utworzony punkt końcowy, który można wywoływać w aplikacji lub czatbocie.

W ten sposób wszelkie zmiany wprowadzane do wersji testowej bazy wiedzy nie mają wpływu na opublikowaną wersję, która może być dostępna w aplikacji produkcyjnej.

Każda z tych baz wiedzy może być przeznaczona do testowania oddzielnie. Za pomocą interfejsów API, można kierować wersję `isTest` testową bazy wiedzy z właściwością treści w generateAnswer wywołania.

Dowiedz się, jak [opublikować bazę wiedzy](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base).

## <a name="monitor-usage"></a>Monitorowanie użycia
Aby móc rejestrować dzienniki czatu w usłudze, należy włączyć usługę Application Insights podczas [tworzenia usługi QnA Maker.](../How-To/set-up-qnamaker-service-azure.md)

Możesz uzyskać różne analizy użycia usługi. Dowiedz się więcej o tym, jak korzystać ze statystyk aplikacji w celu uzyskania [analizy usługi QnA Maker.](../How-To/get-analytics-knowledge-base.md)

Na podstawie tego, czego uczysz się z analityki, dokonaj odpowiednich [aktualizacji bazy wiedzy.](../How-To/edit-knowledge-base.md)

## <a name="version-control-for-data-in-your-knowledge-base"></a>Kontrola wersji danych w bazie wiedzy

Kontrola wersji danych jest dostępna za pośrednictwem funkcji importu/eksportu na stronie **Ustawienia** w portalu QnA Maker.

Można wyw kopii zapasowej bazy wiedzy, eksportując `.tsv` `.xls` bazę wiedzy w jednym lub formacie. Po wyeksportowaniu dołącz ten plik jako część regularnego sprawdzania kontroli źródła.

Jeśli musisz wrócić do określonej wersji, musisz zaimportować ten plik z systemu lokalnego. Wyeksportowana baza wiedzy **może** być używana tylko za pomocą importu na stronie **Ustawienia.** Nie można go używać jako źródła danych dokumentu pliku lub adresu URL. Spowoduje to zastąpienie pytań i odpowiedzi obecnie w bazie wiedzy zawartością importowanego pliku.

## <a name="test-and-production-knowledge-base"></a>Baza wiedzy testowej i produkcyjnej
Baza wiedzy to repozytorium pytań i zestawów odpowiedzi utworzonych, utrzymywanych i używanych za pośrednictwem programu QnA Maker. Każdy zasób QnA Maker może zawierać wiele baz wiedzy.

Baza wiedzy ma dwa stany: *test* i *opublikowane*.

### <a name="test-knowledge-base"></a>Baza wiedzy testowej

*Baza wiedzy testowej* to wersja aktualnie edytowana, zapisywana i testowana pod kątem dokładności i kompletności odpowiedzi. Zmiany wprowadzone w bazie wiedzy testowej nie mają wpływu na użytkownika końcowego aplikacji lub czatu bota. Baza wiedzy o testach jest znana jako `test` w żądaniu HTTP. Wiedza `test` jest dostępna w interaktywnym okienku **testowym** portalu QnA Maker.

### <a name="production-knowledge-base"></a>Baza wiedzy produkcyjnej

*Opublikowana baza wiedzy* to wersja używana w twoim czacie bota lub aplikacji. Działanie publikowania bazy wiedzy umieszcza zawartość bazy wiedzy testowej w opublikowanej wersji bazy wiedzy. Ponieważ opublikowana baza wiedzy jest wersją, której aplikacja używa za pośrednictwem punktu końcowego, upewnij się, że zawartość jest poprawna i dobrze przetestowana. Opublikowana baza wiedzy jest `prod` znana jako w żądaniu HTTP.


## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Aktywne sugestie dotyczące uczenia się](./active-learning-suggestions.md)