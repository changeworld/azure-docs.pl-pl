---
title: Często zadawane pytania — usługa QnA Maker
titleSuffix: Azure Cognitive Services
description: Lista często zadawane pytania dotyczące usługi QnA Maker
services: cognitive-services
author: tulasim88
manager: cjgronlund
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: tulasim
ms.openlocfilehash: be1621bb461d491d4304474034a39a83c5293570
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46972644"
---
# <a name="frequently-asked-questions"></a>Często zadawane pytania

## <a name="why-is-my-urlsfiles-is-not-extracting-question-answer-pairs"></a>Dlaczego jest Moje adresy URL / pliki nie wyodrębnia pary odpowiedź na pytanie?

Istnieje możliwość, że usługa QnA Maker nie może automatycznie wyodrębnić część zawartości (pytań i odpowiedzi) pytań i odpowiedzi z prawidłowych adresów URL. W takich przypadkach możesz wkleić zawartość pytań i odpowiedzi w pliku txt i zobaczyć, jeśli to narzędzie umożliwia pobieranie go. Alternatywnie możesz przez jej tradycyjne zredagowanie dodać zawartość do bazy wiedzy.

## <a name="how-large-a-knowledge-base-can-i-create"></a>Jak duże bazy wiedzy można tworzyć?

Wyszukiwanie jednostek SKU usługi Azure, które można wybrać podczas tworzenia usługi QnA Maker zależy od rozmiaru bazy wiedzy knowledge base. Odczyt [tutaj](./Tutorials/choosing-capacity-qnamaker-deployment.md) Aby uzyskać więcej informacji.

## <a name="why-do-i-not-see-anything-in-the-drop-down-for-when-i-try-to-create-a-new-knowledge-base"></a>Dlaczego nie widzę żadnego z listy rozwijanej dla elementów podczas próby utworzenia nowej bazy wiedzy?

Nie utworzono jeszcze żadnych usługi QnA Maker na platformie Azure. Odczyt [tutaj](./How-To/set-up-qnamaker-service-azure.md) jak to zrobić.

## <a name="how-do-i-share-a-knowledge-base-with-other"></a>Jak udostępnić wiedzy innym?

Udostępnianie działa na poziomie usługi QnA Maker, czyli wszystkich baz wiedzy w usługach zostaną udostępnione. Odczyt [tutaj](./How-To/collaborate-knowledge-base.md) jak współpracować nad wiedzy.

## <a name="how-can-i-change-the-default-message-when-no-good-match-is-found"></a>Jak można zmienić domyślną wiadomość, gdy zostanie odnaleziona żadna zgodna dobre?

Domyślny komunikat jest częścią ustawień w usłudze App service.
- Przejdź do zasobu usługi aplikacji w witrynie Azure portal

![qnamaker appservice](./media/qnamaker-faq/qnamaker-resource-list-appservice.png)
- Kliknij pozycję **ustawienia** opcji

![Ustawienia usługi App Service interfejsu qnamaker](./media/qnamaker-faq/qnamaker-appservice-settings.png)
- Zmień wartość właściwości **DefaultAnswer** ustawienie
- Uruchom ponownie usługi App service

![ponowne uruchomienie usługi App Service interfejsu qnamaker](./media/qnamaker-faq/qnamaker-appservice-restart.png)

## <a name="why-is-my-sharepoint-link-not-getting-extracted"></a>Dlaczego jest Moje łącza programu SharePoint nie wprowadzenie wyodrębnić?

Narzędzie analizuje tylko publiczne adresy URL i nie obsługuje źródeł danych uwierzytelniony w tej chwili. Alternatywnie możesz pobrać plik i użyj opcji przekazywania pliku, aby wyodrębnić pytań i odpowiedzi.


## <a name="the-updates-that-i-made-to-my-knowledge-base-are-not-reflected-on-publish-why-not"></a>Aktualizacje wprowadzone wiedzy nie są odzwierciedlane publikowanie. Dlaczego?

Każdej operacji edycji w aktualizacji tabeli, testów i ustawień, musi zostać zapisany zanim można go opublikować. Pamiętaj kliknij przycisk Zapisz, a następnie szkolenie przycisku po każdej operacji edycji.

## <a name="when-should-i-refresh-my-endpoint-keys"></a>Podczas odświeżania Moje klucze punktu końcowego?

Jeśli podejrzewasz, że zostały naruszone, należy odświeżyć klucze punktu końcowego.

## <a name="does-the-knowledge-base-support-rich-data-or-multimedia"></a>Czy baza wiedzy obsługuje dane sformatowane lub multimediów?

Baza wiedzy obsługuje znaczniki markdown. Jednak automatycznego wyodrębniania z adresów URL ma ograniczone możliwości konwersji HTML na znaczniki Markdown. Jeśli chcesz używać pełnych możliwości znaczników Markdown, możesz zmodyfikować zawartość bezpośrednio w tabeli lub przekazywanie wiedzy z zawartością sformatowaną.

Multimedia, takich jak obrazy i klipy wideo, nie jest obsługiwana w tej chwili.

## <a name="does-qna-maker-support-non-english-languages"></a>Usługa QnA Maker obsługuje języki inne niż angielski?

Zobacz więcej szczegółów na temat [obsługiwane języki](./Overview/languages-supported.md).

Jeśli masz zawartości z wielu języków, należy utworzyć osobną usługą dla każdego języka.

## <a name="do-i-need-to-use-bot-framework-in-order-to-use-qna-maker"></a>Czy do używania narzędzia QnA Maker potrzebna jest struktura botów?

Nie, nie ma potrzeby platformy Bot Framework za pomocą usługi QnA Maker. Jednak narzędzie QnA Maker jest oferowana jako jeden z kilku szablonów w usłudze Azure Bot Service. Usługa BOT Service umożliwia szybkie tworzenie inteligentnych botów programowania za pomocą narzędzi Microsoft Bot Framework i działa na serwerze mniej środowiska.

## <a name="how-can-i-create-a-bot-with-qna-maker"></a>Jak utworzyć robota przy użyciu usługi QnA Maker?

Postępuj zgodnie z instrukcjami w [to](./Tutorials/create-qna-bot.md) dokumentacji do tworzenie Bota przy użyciu usługi Azure Bot.

## <a name="how-do-i-embed-the-qna-maker-service-in-my-website"></a>Jak osadzić usługę QnA Maker w witrynie internetowej?

Wykonaj następujące kroki, aby osadzić usługę QnA Maker jako kontrolkę czatu internetowego w witrynie sieci Web:

1. Utwórz bota — często zadawane pytania, postępując zgodnie z instrukcjami [tutaj](./Tutorials/create-qna-bot.md).
2. Włącz rozmów w sieci web, wykonując kroki [tutaj](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-webchat)


