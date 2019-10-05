---
title: Cykl życia bazy wiedzy — QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker uczy się najlepiej w iteracyjnym cyklu zmian modelu, wypowiedź przykładów, publikowania i zbierania danych z zapytań punktów końcowych.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 09/25/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 52d4efaba25dcede3322586d05fceb4d166f51e3
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2019
ms.locfileid: "71973802"
---
# <a name="knowledge-base-lifecycle-in-qna-maker"></a>Cykl życia bazy wiedzy w QnA Maker
QnA Maker uczy się najlepiej w iteracyjnym cyklu zmian modelu, wypowiedź przykładów, publikowania i zbierania danych z zapytań punktów końcowych. 

![Cykl tworzenia](../media/qnamaker-concepts-lifecycle/kb-lifecycle.png)

## <a name="creating-a-qna-maker-knowledge-base"></a>Tworzenie bazy wiedzy QnA Maker
Punkt końcowy QnA Maker Knowledge Base (KB) zapewnia najlepszą zgodność z kwerendą użytkownika w oparciu o zawartość KB. Tworzenie bazy wiedzy to jednorazowa akcja służąca do konfigurowania repozytorium zawartości pytań, odpowiedzi i skojarzonych metadanych. Bazę wiedzy można utworzyć, przeszukiwanie istniejącej zawartości, takiej jak strony często zadawanych pytań, podręczniki produktu lub strukturalne pary pytań i odpowiedzi. Dowiedz się, jak [utworzyć bazę wiedzy](../quickstarts/create-publish-knowledge-base.md).

## <a name="testing-and-updating-the-knowledge-base"></a>Testowanie i aktualizowanie bazy wiedzy

Baza wiedzy jest gotowa do testowania, gdy zostanie wypełniona zawartością, z redakcją lub przez automatyczne wyodrębnianie. Testy interaktywne można wykonać w portalu QnA Maker za pomocą panelu **test** , wprowadzając typowe zapytania użytkownika i sprawdzając, czy odpowiedzi zwrócone z prawidłową odpowiedzią i odpowiednim wynikiem pewności. 

* **Aby naprawić oceny o niskim poziomie pewności**: Dodaj alternatywne pytania. 
* **Gdy zapytanie niepoprawnie zwróci [domyślną odpowiedź](confidence-score.md#change-default-answer)** : Dodaj nowe odpowiedzi do poprawnego pytania. 

Ta ścisła pętla test-Update jest kontynuowana, dopóki wyniki nie zostaną spełnione. Dowiedz się, jak [przetestować bazę wiedzy](../How-To/test-knowledge-base.md).

W przypadku dużych artykułów bazy wiedzy Użyj testów automatycznych z [interfejsem API generateAnswer](../how-to/metadata-generateanswer-usage.md#get-answer-predictions-with-the-generateanswer-api) i właściwością treści `isTest`, która wysyła zapytanie do bazy wiedzy `test`, zamiast opublikowanej bazy wiedzy. 

```json
{
  "question": "example question",
  "top": 3,
  "userId": "Default",
  "isTest": true
}
```

## <a name="publish-the-knowledge-base"></a>Publikowanie bazy wiedzy
Po zakończeniu testowania bazy wiedzy możesz ją opublikować. Publikowanie powoduje wypchnięcie najnowszej wersji przetestowanej bazy wiedzy na dedykowany indeks Azure Search reprezentujący **opublikowaną** bazę wiedzy. Zostanie również utworzony punkt końcowy, który można wywoływać w aplikacji lub czatbocie.

W ten sposób wszelkie zmiany wprowadzone w wersji testowej bazy wiedzy nie mają wpływu na opublikowaną wersję, która może znajdować się w aplikacji produkcyjnej.

Każdy z tych baz wiedzy może być przeznaczony do testowania osobno. Korzystając z interfejsów API, można wskazać wersję testową bazy wiedzy z właściwością treści `isTest` w wywołaniu generateAnswer.

Dowiedz się, jak [opublikować bazę wiedzy](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base).

## <a name="monitor-usage"></a>Monitorowanie użycia
Aby móc rejestrować dzienniki rozmowy usługi, należy włączyć Application Insights podczas [tworzenia usługi QNA Maker](../How-To/set-up-qnamaker-service-azure.md).

Możesz uzyskać różne analizy użycia usługi. Dowiedz się więcej na temat sposobu korzystania z usługi Application Insights w celu uzyskania [analiz w usłudze QNA Maker](../How-To/get-analytics-knowledge-base.md).

Na podstawie tego, co uczysz się na analizie, wprowadź odpowiednie [aktualizacje bazy wiedzy](../How-To/edit-knowledge-base.md).

## <a name="version-control-of-a-knowledge-base"></a>Kontrola wersji bazy wiedzy

Kontrola wersji nie jest dostarczana przez QnA Maker. Musisz wyeksportować bazę wiedzy ze strony **Ustawienia** i użyć własnej metody i narzędzi.

Eksportowanie bazy wiedzy do formatu TSV lub XLS zostało zakończone ze strony **ustawień** . 

Jeśli musisz wrócić do określonej wersji, musisz zaimportować ten plik z systemu lokalnego. Na stronie **Ustawienia** Zaimportuj plik TSV lub xls. Spowoduje to zamienienie pytań i odpowiedzi znajdujących się w bazie wiedzy z zawartością zaimportowanego pliku.   

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Wynik pewności](./confidence-score.md)