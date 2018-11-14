---
title: Często zadawane pytania — usługa QnA Maker
titleSuffix: Azure Cognitive Services
description: Lista często zadawane pytania dotyczące usługi QnA Maker
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 11/08/2018
ms.author: tulasim
ms.openlocfilehash: 2e4a5d9b7ee2a1a88bcfe819be6540385458108f
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2018
ms.locfileid: "51622367"
---
# <a name="frequently-asked-questions"></a>Często zadawane pytania

## <a name="manage-the-knowledge-base"></a>Zarządzanie w bazie wiedzy knowledge base

### <a name="i-accidentally-deleted-a-part-of-my-qna-maker-what-should-i-do"></a>Część mojej usługi QnA Maker przypadkowo usunięty, co należy zrobić? 

Usuwa wszystkie są trwałe, łącznie z pary pytań i odpowiedzi, pliki, adresy URL, niestandardowe pytania i odpowiedzi, baz wiedzy lub zasobów platformy Azure. Upewnij się, należy wyeksportować z bazy wiedzy **ustawienia** strony przed usunięciem jakichkolwiek pracach związanych z bazy wiedzy knowledge base. 

### <a name="why-is-my-urlsfiles-is-not-extracting-question-answer-pairs"></a>Dlaczego jest Moje adresy URL / pliki nie wyodrębnia pary odpowiedź na pytanie?

Istnieje możliwość, że usługa QnA Maker nie może automatycznie wyodrębnić część zawartości (pytań i odpowiedzi) pytań i odpowiedzi z prawidłowych adresów URL. W takich przypadkach możesz wkleić zawartość pytań i odpowiedzi w pliku txt i zobaczyć, jeśli to narzędzie umożliwia pobieranie go. Alternatywnie możesz przez jej tradycyjne zredagowanie dodać zawartość do bazy wiedzy za pośrednictwem [portalu narzędzia QnA Maker](https://qnamaker.ai).

### <a name="how-large-a-knowledge-base-can-i-create"></a>Jak duże bazy wiedzy można tworzyć?

Wyszukiwanie jednostek SKU usługi Azure, które można wybrać podczas tworzenia usługi QnA Maker zależy od rozmiaru bazy wiedzy knowledge base. Odczyt [tutaj](./Tutorials/choosing-capacity-qnamaker-deployment.md) Aby uzyskać więcej informacji.

### <a name="why-cant-i-see-anything-in-the-drop-down-when-i-try-to-create-a-new-knowledge-base"></a>Dlaczego nie widzę żadnych czynności w listy rozwijanej podczas próby utworzenia nowej bazy wiedzy?

Nie utworzono jeszcze żadnych usługi QnA Maker na platformie Azure. Odczyt [tutaj](./How-To/set-up-qnamaker-service-azure.md) dowiesz się, jak to zrobić.

### <a name="how-do-i-share-a-knowledge-base-with-others"></a>Jak udostępnić wiedzy innym osobom?

Udostępnianie działa na poziomie usługi QnA Maker, oznacza to, że wszystkie baz wiedzy w usłudze zostaną udostępnione. Odczyt [tutaj](./How-To/collaborate-knowledge-base.md) jak współpracować nad wiedzy.

### <a name="can-you-share-a-kb-with-a-contributor-that-is-not-in-the-same-aad-tenant-to-modify-a-kb"></a>Możesz udostępnić bazę wiedzy współautorów którzy nie znajduje się w tej samej dzierżawie usługi AAD do modyfikowania bazy wiedzy? 

Udostępnianie bazuje na kontroli dostępu opartej na rolach na platformie Azure (RBAC). Jeśli możesz udostępniać _wszelkie_ zasobów na platformie Azure przy użyciu innego użytkownika, można także udostępnić usługi QnA Maker.

### <a name="if-you-have-an-app-service-plan-with-5-qnamaker-kbs-can-you-assign-readwrite-rights-to-5-different-users-so-each-of-them-can-access-only-1-qnamaker-kb"></a>Jeśli masz Plan usługi App Service z dnia 5 QnAMaker artykułów bazy wiedzy. Aby każdego z nich można uzyskiwać dostęp do QnAMaker tylko 1 KB można przypisać uprawnienia odczytu/zapisu do 5 różnych użytkowników?

Możesz udostępniać całej usługi qna Maker przechowuje wszelką, nie do poszczególnych artykułów bazy wiedzy.

### <a name="how-can-i-change-the-default-message-when-no-good-match-is-found"></a>Jak można zmienić domyślną wiadomość, gdy zostanie odnaleziona żadna zgodna dobre?

Domyślny komunikat jest częścią ustawień w usłudze App service.
- Przejdź do zasobu usługi aplikacji w witrynie Azure portal

![qnamaker appservice](./media/qnamaker-faq/qnamaker-resource-list-appservice.png)
- Kliknij pozycję **ustawienia** opcji

![Ustawienia usługi App Service interfejsu qnamaker](./media/qnamaker-faq/qnamaker-appservice-settings.png)
- Zmień wartość właściwości **DefaultAnswer** ustawienie
- Uruchom ponownie usługi App service

![ponowne uruchomienie usługi App Service interfejsu qnamaker](./media/qnamaker-faq/qnamaker-appservice-restart.png)

### <a name="why-is-my-sharepoint-link-not-getting-extracted"></a>Dlaczego jest Moje łącza programu SharePoint nie wprowadzenie wyodrębnić?

Narzędzie analizuje tylko publiczne adresy URL i nie obsługuje źródeł danych uwierzytelniony w tej chwili. Alternatywnie możesz pobrać plik i użyj opcji przekazywania pliku, aby wyodrębnić pytań i odpowiedzi.


### <a name="the-updates-that-i-made-to-my-knowledge-base-are-not-reflected-on-publish-why-not"></a>Aktualizacje wprowadzone wiedzy nie są odzwierciedlane publikowanie. Dlaczego?

Każdej operacji edycji w tabeli aktualizacji, testowym czy ustawienie, musi zostać zapisany zanim można go opublikować. Należy kliknąć przycisk **Zapisz i szkolenie** przycisku po każdej operacji edycji.

### <a name="does-the-knowledge-base-support-rich-data-or-multimedia"></a>Czy baza wiedzy obsługuje dane sformatowane lub multimediów?

Baza wiedzy obsługuje znaczniki markdown. Jednak automatycznego wyodrębniania z adresów URL ma ograniczone możliwości konwersji HTML na znaczniki Markdown. Jeśli chcesz używać pełnych możliwości znaczników Markdown, możesz zmodyfikować zawartość bezpośrednio w tabeli lub przekazywanie wiedzy z zawartością sformatowaną.

Multimedia, takich jak obrazy i klipy wideo, nie jest obsługiwana w tej chwili.

### <a name="does-qna-maker-support-non-english-languages"></a>Usługa QnA Maker obsługuje języki inne niż angielski?

Zobacz więcej szczegółów na temat [obsługiwane języki](./Overview/languages-supported.md).

Jeśli masz zawartości z wielu języków, należy utworzyć osobną usługą dla każdego języka.

## <a name="manage-service"></a>Zarządzanie usługą

### <a name="when-should-i-restart-my-app-service"></a>Kiedy należy ponownie uruchomić usługę aplikacji? 

Odświeżanie usługi app service, gdy ikona ostrzegawcza znajduje się obok wartości wersji bazy wiedzy w **klucze punktu końcowego** tabeli na **ustawienia użytkownika** [strony](https://www.qnamaker.ai/UserSettings).

### <a name="when-should-i-refresh-my-endpoint-keys"></a>Podczas odświeżania Moje klucze punktu końcowego?

Jeśli podejrzewasz, że zostały naruszone, należy odświeżyć klucze punktu końcowego.

### <a name="can-i-use-the-same-azure-search-resource-for-kbs-using-multiple-languages"></a>Czy można używać tego samego zasobu usługi Azure Search do artykułów bazy wiedzy przy użyciu wielu języków?

Aby korzystać z wielu języków i wiele artykułów bazy wiedzy, użytkownik musi utworzyć zasób usługi QnA Maker dla każdego języka. Spowoduje to utworzenie usługi wyszukiwania platformy Azure, dla każdego języka. Mieszanie inny język artykułów bazy wiedzy w ramach jednej usługi Azure search usługi spowoduje o obniżonym poziomie istotności wyników.

## <a name="integrate-with-other-services-including-bots"></a>Integracja z innymi usługami, w tym Botów

### <a name="do-i-need-to-use-bot-framework-in-order-to-use-qna-maker"></a>Czy do używania narzędzia QnA Maker potrzebna jest struktura botów?

Nie, nie ma potrzeby platformy Bot Framework za pomocą usługi QnA Maker. Jednak narzędzie QnA Maker jest oferowana jako jeden z kilku szablonów w usłudze Azure Bot Service. Usługa BOT Service umożliwia szybkie tworzenie inteligentnych botów programowania za pomocą narzędzi Microsoft Bot Framework i działa w środowisku bez serwera.

### <a name="how-can-i-create-a-bot-with-qna-maker"></a>Jak utworzyć robota przy użyciu usługi QnA Maker?

Postępuj zgodnie z instrukcjami w [to](./Tutorials/create-qna-bot.md) dokumentacji do tworzenia Bota usługa Azure Bot Service.

### <a name="how-do-i-embed-the-qna-maker-service-in-my-website"></a>Jak osadzić usługę QnA Maker w witrynie internetowej?

Wykonaj następujące kroki, aby osadzić usługę QnA Maker jako kontrolkę czatu internetowego w witrynie sieci Web:

1. Utwórz bota — często zadawane pytania, postępując zgodnie z instrukcjami [tutaj](./Tutorials/create-qna-bot.md).
2. Włącz rozmów w sieci web, wykonując kroki [tutaj](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-webchat)

## <a name="data-storage"></a>Magazyn danych

### <a name="what-data-is-stored-and-where-is-it-stored"></a>Jakie dane są przechowywane i gdzie jest przechowywany? 

Podczas tworzenia usługi QnA Maker został wybrany region platformy Azure. Pliki dziennika i baz wiedzy, na których są przechowywane w tym regionie. 
