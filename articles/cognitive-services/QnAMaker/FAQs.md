---
title: Często zadawane pytania — kognitywnych usług firmy Microsoft | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Często zadawane pytania
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 04/21/2018
ms.author: saneppal
ms.openlocfilehash: a6bf32549715d0357771b3f3b0ff72f64788ec20
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348672"
---
# <a name="frequently-asked-questions"></a>Często zadawane pytania

## <a name="why-is-my-urlsfiles-is-not-extracting-question-answer-pairs"></a>Dlaczego jest Mój adresy URL / plików nie jest wyodrębniania pary odpowiedź na pytanie?

Istnieje możliwość, że Maker — strona główna nie może automatycznie — Wyodrębnij zawartość (— strona główna) pytań i odpowiedzi z prawidłowe adresy URL — często zadawane pytania. W takich przypadkach możesz wkleić zawartości — Strona główna w pliku txt i zobacz, czy narzędzie może obsługiwać go. Alternatywnie redakcyjna można dodać zawartości do bazy wiedzy.

## <a name="how-large-a-knowledge-base-can-i-create"></a>Jak duże bazy wiedzy można tworzyć?

Rozmiar bazy wiedzy knowledge base, zależy od wyszukiwanie SKU Azure, które można wybrać podczas tworzenia usługi Maker — strona główna. Odczyt [tutaj](./Tutorials/choosing-capacity-qnamaker-deployment.md) więcej szczegółów.

## <a name="why-do-i-not-see-anything-in-the-drop-down-for-when-i-try-to-create-a-new-knowledge-base"></a>Dlaczego nie widzę żadnych czynności w listy rozwijanej dla podczas próby utworzenia nowej wiedzy?

Nie utworzono jeszcze żadnych usług Maker — strona główna na platformie Azure. Odczyt [tutaj](./How-To/set-up-qnamaker-service-azure.md) , jak to zrobić.

## <a name="how-do-i-share-a-knowledge-base-with-other"></a>Jak udostępnić bazę wiedzy z innymi?

Udostępnianie działa na poziomie usługi Maker — strona główna, tj. wszystkie baz wiedzy w usługach zostaną udostępnione. Odczyt [tutaj](./How-To/collaborate-knowledge-base.md) jak współpracować nad bazy wiedzy.

## <a name="how-can-i-change-the-default-message-when-no-good-match-is-found"></a>Jak można zmienić domyślną wiadomość, gdy nie dobrej znaleziono?

Komunikat domyślny jest częścią ustawień w usłudze App service.
- Przejdź do zasobu usługi aplikacji w portalu Azure

![qnamaker appservice](./media/qnamaker-faq/qnamaker-resource-list-appservice.png)
- Polecenie **ustawienia** opcji

![Ustawienia appservice qnamaker](./media/qnamaker-faq/qnamaker-appservice-settings.png)
- Zmień wartość **DefaultAnswer** ustawienie
- Uruchom ponownie usługi aplikacji

![ponowne uruchomienie usługi aplikacji qnamaker](./media/qnamaker-faq/qnamaker-appservice-restart.png)

## <a name="why-is-my-sharepoint-link-not-getting-extracted"></a>Dlaczego jest Moje łącza programu SharePoint nie pobierania wyodrębnić?

Narzędzie analizuje tylko publiczne adresy URL i nie obsługuje źródeł danych uwierzytelniony w tej chwili. Alternatywnie można pobrać pliku i użyj opcji przekazywania pliku, aby wyodrębnić pytania i odpowiedzi.


## <a name="the-updates-that-i-made-to-my-knowledge-base-are-not-reflected-on-publish-why-not"></a>Aktualizacje, wprowadzone do mojej wiedzy nie są uwzględniane publikowania. Dlaczego?

Każdej operacji edycji w aktualizacji tabeli, testów lub ustawienia, musi zostać zapisany zanim będzie można go opublikować. Należy koniecznie kliknij przycisk Zapisz i uczenia przycisku po każdej operacji edycji.

## <a name="when-should-i-refresh-my-endpoint-keys"></a>Podczas odświeżania klawisze punkt końcowy?

Należy odświeżyć klucze punktu końcowego, jeśli zachodzi podejrzenie, że zostały one złamane.

## <a name="does-the-knowledge-base-support-rich-data-or-multimedia"></a>Czy dane sformatowanego bazy wiedzy pomocy technicznej lub multimedia?

Baza wiedzy obsługuje znaczniki markdown. Jednak automatycznego wyodrębniania z adresów URL ma ograniczone możliwości konwersji HTML do języka znaczników Markdown. Używanie języka znaczników Markdown w pełni funkcjonalnymi, możesz zmodyfikować zawartość bezpośrednio w tabeli, lub Przekaż bazy wiedzy o zaawansowanych zawartości.

Multimedia, takich jak obrazy i klipy wideo, nie jest obsługiwana w tej chwili.

## <a name="does-qna-maker-support-non-english-languages"></a>Maker — strona główna obsługuje języków innych niż angielskie?

Dowiedz się więcej [obsługiwanych języków](./Overview/languages-supported.md).

Jeśli masz zawartości z wielu języków, należy utworzyć oddzielny usługi dla każdego języka.

## <a name="do-i-need-to-use-bot-framework-in-order-to-use-qna-maker"></a>Czy do używania narzędzia QnA Maker potrzebna jest struktura botów?

Nie, nie należy używać Bot Framework z Maker — strona główna. Jednak Maker — strona główna jest oferowany jako jeden z kilku szablonów w usłudze Bot platformy Azure. Usługa BOT umożliwia szybkie bot inteligentnego programowanie za pomocą programu Microsoft Bot Framework i działa na serwerze w środowisku bez.

## <a name="how-can-i-create-a-bot-with-qna-maker"></a>Jak utworzyć robotów z Maker — strona główna

Postępuj zgodnie z instrukcjami [to](./Tutorials/create-qna-bot.md) dokumentacji, aby utworzyć użytkownika Bot z Azure Bot.

## <a name="how-do-i-embed-the-qna-maker-service-in-my-website"></a>Jak osadzić usługę QnA Maker w witrynie internetowej?

Wykonaj następujące kroki, aby osadzić usługę Maker — strona główna jako formant rozmów w sieci web w witrynie sieci Web:

1. Utwórz użytkownika bot — często zadawane pytania zgodnie z instrukcjami [tutaj](./Tutorials/create-qna-bot.md).
2. Włącz rozmów w sieci web, wykonując kroki [tutaj](https://docs.microsoft.com/en-us/azure/bot-service/bot-service-channel-connect-webchat)


