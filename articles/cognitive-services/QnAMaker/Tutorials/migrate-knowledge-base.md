---
title: Migrowanie baz wiedzy - QnA Maker
description: Migracja bazy wiedzy wymaga eksportowania z jednej bazy wiedzy, a następnie importowania do innej.
ms.topic: article
ms.date: 03/25/2020
ms.openlocfilehash: 13e5e79bf4eaf6ec59e41b3e12aa1bb23f2c1578
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80258094"
---
# <a name="migrate-a-knowledge-base-using-export-import"></a>Migrowanie bazy wiedzy przy użyciu eksportu i importu

Migracja to proces tworzenia nowej bazy wiedzy z istniejącej bazy wiedzy. Możesz to zrobić z kilku powodów:

* proces tworzenia kopii zapasowych i przywracania
* Potok ciągłej integracji/ciągłego wdrażania
* przenoszenie regionów

Migracja bazy wiedzy wymaga eksportowania z istniejącej bazy wiedzy, a następnie importowania do innej.

## <a name="prerequisites"></a>Wymagania wstępne

* Zanim rozpoczniesz, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Konfigurowanie nowej [usługi QnA Maker](../How-To/set-up-qnamaker-service-azure.md)

## <a name="migrate-a-knowledge-base-from-qna-maker"></a>Migrowanie bazy wiedzy z programu QnA Maker
1. Zaloguj się do [portalu QnA Maker](https://qnamaker.ai).
1. Wybierz bazę wiedzy pochodzenia, którą chcesz przeprowadzić migrację.

1. Na stronie **Ustawienia** wybierz **pozycję Eksportuj bazę wiedzy,** aby pobrać plik .tsv zawierający zawartość bazy wiedzy pochodzenia — pytania, odpowiedzi, metadane, monity uzupełniające i nazwy źródeł danych, z których zostały wyodrębnione.

1. Wybierz **pozycję Utwórz bazę wiedzy** z górnego menu, a następnie utwórz _pustą_ bazę wiedzy. Jest pusta, ponieważ podczas tworzenia nie zamierzasz dodawać żadnych adresów URL ani plików. Są to dodawane podczas kroku importu, po utworzeniu.

    Skonfiguruj bazę wiedzy. Ustaw tylko nową nazwę bazy wiedzy. Obsługiwane są zduplikowane nazwy i obsługiwane są również znaki specjalne.

    Nie należy wybierać niczego z kroku 4, ponieważ te wartości zostaną zastąpione podczas importowania pliku.

1. W kroku 5 wybierz pozycję **Utwórz**.

1. W tej nowej bazie wiedzy otwórz kartę **Ustawienia** i wybierz pozycję **Importuj bazę wiedzy**. Spowoduje to import pytań, odpowiedzi, metadanych, monitów uzupełniających i zachowuje nazwy źródeł danych, z których zostały wyodrębnione.

   > [!div class="mx-imgBorder"]
   > [![Importowanie bazy wiedzy](../media/qnamaker-how-to-migrate-kb/Import.png)](../media/qnamaker-how-to-migrate-kb/Import.png#lightbox)

1. **Przetestuj** nową bazę wiedzy za pomocą panelu testowego. Dowiedz się, jak [przetestować swoją bazę wiedzy](../How-To/test-knowledge-base.md).

1. **Opublikuj** bazę wiedzy i utwórz bota czatu. Dowiedz się, jak [opublikować bazę wiedzy](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base).

## <a name="programmatically-migrate-a-knowledge-base-from-qna-maker"></a>Programowo migruj bazę wiedzy z programu QnA Maker

Proces migracji jest programowo dostępny przy użyciu następujących interfejsów API REST:

**Eksportuj**

* [Pobierz interfejs API bazy wiedzy](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/download)

**Import**

* [Zamień interfejs API (ponowne załadowanie identyfikatorem bazy wiedzy)](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/replace)
* [Tworzenie interfejsu API (ładowanie przy nowym identyfikatorze bazy wiedzy)](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create)


## <a name="chat-logs-and-alterations"></a>Dzienniki czatu i zmiany
Zmiany bez uwzględniania wielkości liter (synonimy) nie są importowane automatycznie. Użyj [interfejsów API V4,](https://go.microsoft.com/fwlink/?linkid=2092179) aby przenieść zmiany w nowej bazie wiedzy.

Nie ma możliwości migracji dzienników czatu, ponieważ nowa baza wiedzy używa usługi Application Insights do przechowywania dzienników czatu.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Edytowanie bazy wiedzy](../How-To/edit-knowledge-base.md)
