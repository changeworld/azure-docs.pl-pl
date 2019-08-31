---
title: Rozwiązywanie problemów — QnA Maker
titleSuffix: Azure Cognitive Services
description: Zanadzorowana lista najbardziej często zadawanych pytań dotyczących usługi QnA Maker pomoże Ci szybciej wdrożyć usługę i uzyskać lepsze wyniki.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 08/30/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 60a2b63c7d37440bb08c7a7f15f052cfa2607406
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/30/2019
ms.locfileid: "70193693"
---
# <a name="troubleshooting-for-qna-maker"></a>Rozwiązywanie problemów dotyczących QnA Maker

Zanadzorowana lista najbardziej często zadawanych pytań dotyczących usługi QnA Maker pomoże Ci szybciej wdrożyć usługę i uzyskać lepsze wyniki.


## <a name="how-to-get-the-qnamaker-service-hostname"></a>Jak uzyskać nazwa hosta usługi qna Maker przechowuje wszelką

Nazwa hosta usługi qna Maker przechowuje wszelką jest przydatny do celów debugowania podczas skontaktuj się z działem pomocy technicznej QnAMaker lub UserVoice. Nazwa hosta jest adresem URL w tej formie: https:// *{hostname}* . azurewebsites.NET.
    
1. Przejdź do usługi qna Maker przechowuje wszelką (Grupa zasobów) [witryny Azure portal](https://portal.azure.com)

    ![Grupa zasobów QnAMaker Azure w witrynie Azure portal](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

1. Wybierz App Service skojarzoną z zasobem QnA Maker. Zazwyczaj nazwy są takie same.

     ![Wybierz usługę aplikacji interfejsu QnAMaker](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

1. Nazwa hosta adresu URL jest dostępny w sekcji Przegląd

    ![Nazwa hosta w interfejsie QnAMaker](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-gethostname.png)
    

## <a name="use-the-help-bot-in-the-qna-maker-portal"></a>Korzystanie z pomocy bot w portalu QnA Maker

QnA Maker udostępnia **Pomoc techniczną** bot w portalu QNA Maker. Pomoc bot jest dostępna na każdej stronie sieci Web. Bot używa QNA Maker, aby zapewnić odpowiedzi i dostarcza [ C# projekt kodu struktury bot](https://github.com/microsoft/BotBuilder-Samples/tree/master/experimental/qnamaker-support) do bot, dzięki czemu można szybko rozpocząć pracę z własnym bot odpowiedzi. 

![! [QnA Maker udostępnia * * help * * bot w portalu QnA Maker, aby Ci pomóc.] (./media/qnamaker-faq/use-qna-maker-help-bot-to-learn-qna-maker-service.png)](./media/qnamaker-faq/use-qna-maker-help-bot-to-learn-qna-maker-service.png#lightbox)

## <a name="manage-the-knowledge-base"></a>Zarządzanie w bazie wiedzy knowledge base

### <a name="i-accidentally-deleted-a-part-of-my-qna-maker-what-should-i-do"></a>Część mojej usługi QnA Maker przypadkowo usunięty, co należy zrobić? 

Usuwa wszystkie są trwałe, łącznie z pary pytań i odpowiedzi, pliki, adresy URL, niestandardowe pytania i odpowiedzi, baz wiedzy lub zasobów platformy Azure. Upewnij się, należy wyeksportować z bazy wiedzy **ustawienia** strony przed usunięciem jakichkolwiek pracach związanych z bazy wiedzy knowledge base. 

### <a name="why-is-my-urlsfiles-not-extracting-question-answer-pairs"></a>Dlaczego jest Moje adresy URL / pliki nie wyodrębniania pary odpowiedź na pytanie?

Istnieje możliwość, że usługa QnA Maker nie może automatycznie wyodrębnić część zawartości (pytań i odpowiedzi) pytań i odpowiedzi z prawidłowych adresów URL. W takich przypadkach możesz wkleić zawartość pytań i odpowiedzi w pliku txt i zobaczyć, jeśli to narzędzie umożliwia pobieranie go. Alternatywnie możesz przez jej tradycyjne zredagowanie dodać zawartość do bazy wiedzy za pośrednictwem [portalu narzędzia QnA Maker](https://qnamaker.ai).

### <a name="how-large-a-knowledge-base-can-i-create"></a>Jak duże bazy wiedzy można tworzyć?

Wyszukiwanie jednostek SKU usługi Azure, które można wybrać podczas tworzenia usługi QnA Maker zależy od rozmiaru bazy wiedzy knowledge base. Odczyt [tutaj](./Tutorials/choosing-capacity-qnamaker-deployment.md) Aby uzyskać więcej informacji.

### <a name="why-cant-i-see-anything-in-the-drop-down-when-i-try-to-create-a-new-knowledge-base"></a>Dlaczego nie widzę żadnych czynności w listy rozwijanej podczas próby utworzenia nowej bazy wiedzy?

Nie utworzono jeszcze żadnych usługi QnA Maker na platformie Azure. Odczyt [tutaj](./How-To/set-up-qnamaker-service-azure.md) dowiesz się, jak to zrobić.

### <a name="how-do-i-share-a-knowledge-base-with-others"></a>Jak udostępnić wiedzy innym osobom?

Udostępnianie działa na poziomie usługi QnA Maker, oznacza to, że wszystkie baz wiedzy w usłudze zostaną udostępnione. Odczyt [tutaj](./How-To/collaborate-knowledge-base.md) jak współpracować nad wiedzy.

### <a name="can-you-share-a-knowledge-base-with-a-contributor-that-is-not-in-the-same-aad-tenant-to-modify-a-knowledge-base"></a>Możesz udostępnić wiedzy współautorów którzy nie znajduje się w tej samej dzierżawie usługi AAD do modyfikowania bazy wiedzy? 

Udostępnianie bazuje na kontroli dostępu opartej na rolach na platformie Azure (RBAC). Jeśli możesz udostępniać _wszelkie_ zasobów na platformie Azure przy użyciu innego użytkownika, można także udostępnić usługi QnA Maker.

### <a name="if-you-have-an-app-service-plan-with-5-qnamaker-knowledge-bases-can-you-assign-readwrite-rights-to-5-different-users-so-each-of-them-can-access-only-1-qnamaker-knowledge-base"></a>Jeśli masz Plan usługi App Service z 5 bazami wiedzy QnAMaker. Aby każdego z nich można uzyskiwać dostęp do tylko 1 QnAMaker bazie wiedzy knowledge base możesz przypisywać uprawnienia odczytu/zapisu do 5 różnych użytkowników?

Możesz udostępniać całej usługi qna Maker przechowuje wszelką, nie do poszczególnych baz wiedzy.

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

Zobacz [lokalizacji źródła danych](./Concepts/data-sources-supported.md#data-source-locations) Aby uzyskać więcej informacji.

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

### <a name="i-deleted-my-existing-search-service-how-can-i-fix-this"></a>Usunięto istniejącą usługę wyszukiwania. Jak można to naprawić?

Jeśli usuniesz indeks Azure Search, operacja jest końcowa i nie będzie można odzyskać indeksu. 

### <a name="i-deleted-my-testkb-index-in-my-search-service-how-can-i-fix-this"></a>Mój `testkb` indeks został usunięty w usłudze wyszukiwania. Jak można to naprawić? 

Nie można odzyskać starych danych. Utwórz nowy zasób QnA Maker i ponownie utwórz bazę wiedzy.

### <a name="when-should-i-refresh-my-endpoint-keys"></a>Podczas odświeżania Moje klucze punktu końcowego?

Jeśli podejrzewasz, że zostały naruszone, należy odświeżyć klucze punktu końcowego.

### <a name="can-i-use-the-same-azure-search-resource-for-knowledge-bases-using-multiple-languages"></a>Potrzeby baz danych przy użyciu wielu języków mogą używać tego samego zasobu usługi Azure Search?

Aby korzystać z wielu języków oraz wielu baz wiedzy, użytkownik musi utworzyć zasób usługi QnA Maker dla każdego języka. Spowoduje to utworzenie oddzielnej usługi Azure Search dla każdego języka. Mieszanie baz wiedzy innym języku, w ramach jednej usługi Azure search usługi spowoduje o obniżonym poziomie istotności wyników.

### <a name="how-can-i-change-the-name-of-the-azure-search-resource-used-by-qna-maker"></a>Jak zmienić nazwę zasobu usługi Azure Search, używane przez narzędzie QnA Maker?

Nazwa zasobu usługi Azure Search jest nazwą zasobu usługi QnA Maker niektóre litery losowe dołączany na końcu. Dzięki temu trudno rozróżnienie między wieloma Wyszukaj zasoby dotyczące usługi QnA Maker. Utwórz osobną usługą Azure Search (nadawania jej nazwy w sposób, aby) i połączyć ją z usługą pytań i odpowiedzi. Kroki są podobne kroki konieczne do [uaktualnienie usługi Azure Search](How-To/set-up-qnamaker-service-azure.md#upgrade-azure-search-service).

### <a name="when-qna-maker-returns-runtime-core-is-not-initialized-how-do-i-fix-it"></a>Kiedy QNA Maker zwraca `Runtime core is not initialized,` , jak rozwiązać ten problem?

Miejsce na dysku dla usługi App Service może być pełne. Procedura naprawy miejsca na dysku:

1. W [Azure Portal](https://portal.azure.com)wybierz usługę App Service QNA Maker, a następnie Zatrzymaj usługę.
1. Gdy nadal znajduje się w usłudze App Service, wybierz pozycję **Narzędzia programistyczne**, a następnie **Narzędzia zaawansowane**, a następnie pozycję **Przejdź**. Spowoduje to otwarcie nowego okna przeglądarki.
1. Wybierz pozycję **konsola debugowania**, a następnie polecenie **cmd** , aby otworzyć narzędzie wiersza polecenia. 
1. Przejdź do _lokalizacji site/wwwroot/Data/QnAMaker/_ Directory.
1. Usuń wszystkie foldery, których nazwy zaczynają `rd`się od. 

    **Nie usuwaj** następujących danych:

    * Plik KbIdToRankerMappings. txt
    * Plik EndpointSettings. JSON
    * Folder EndpointKeys 

1. Uruchom usługę App Service.
1. Uzyskaj dostęp do bazy wiedzy, aby sprawdzić, czy teraz działa. 


## <a name="integrate-with-other-services-including-bots"></a>Integracja z innymi usługami, w tym Botów

### <a name="do-i-need-to-use-bot-framework-in-order-to-use-qna-maker"></a>Czy do używania narzędzia QnA Maker potrzebna jest struktura botów?

Nie, nie musisz używać [platformy bot](https://github.com/Microsoft/botbuilder-dotnet) z QNA Maker. QnA Maker jest jednak oferowany jako jeden z kilku szablonów w programie [Azure bot Service](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0). Usługa BOT Service umożliwia szybkie tworzenie inteligentnych botów programowania za pomocą narzędzi Microsoft Bot Framework i działa w środowisku bez serwera.

### <a name="how-can-i-create-a-new-bot-with-qna-maker"></a>Jak utworzyć nowy Bot z QnA Maker?

Postępuj zgodnie z instrukcjami w [to](./Tutorials/create-qna-bot.md) dokumentacji do tworzenia Bota usługa Azure Bot Service.

### <a name="how-do-i-use-a-different-knowledge-base-with-an-existing-azure-bot-service"></a>Jak mogę użyć innej bazy wiedzy z istniejącą usługą Azure bot?

Musisz mieć następujące informacje na temat bazy wiedzy:

* Identyfikator bazy wiedzy.
* Nazwa hosta punktu końcowego z opublikowanym bazą wiedzy — znajduje się na stronie **Ustawienia** po opublikowaniu.
* Klucz punktu końcowego opublikowany w bazie wiedzy — znajduje się na stronie **Ustawienia** po opublikowaniu. 

Korzystając z tych informacji, przejdź do usługi App Service bot w Azure Portal. W obszarze **ustawienia > Konfiguracja — > Ustawienia aplikacji**Zmień te wartości.  

Klucz punktu końcowego bazy wiedzy jest oznaczony `QnAAuthkey` jako usługa ABS. 

### <a name="can-two-or-more-client-applications-share-a-knowledge-base"></a>Czy co najmniej dwie aplikacje klienckie współdzielą bazę wiedzy? 

Tak. w bazie wiedzy można wykonywać zapytania z dowolnej liczby klientów. Jeśli odpowiedź z bazy wiedzy wydaje się powolnić lub przekroczyć limit czasu, rozważ uaktualnienie warstwy usług dla usługi App Service skojarzonej z bazą wiedzy.

### <a name="how-do-i-embed-the-qna-maker-service-in-my-website"></a>Jak osadzić usługę QnA Maker w witrynie internetowej?

Wykonaj następujące kroki, aby osadzić usługę QnA Maker jako kontrolkę czatu internetowego w witrynie sieci Web:

1. Utwórz bota — często zadawane pytania, postępując zgodnie z instrukcjami [tutaj](./Tutorials/create-qna-bot.md).
2. Włącz rozmów w sieci web, wykonując kroki [tutaj](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-webchat)

## <a name="data-storage"></a>Magazyn danych

### <a name="what-data-is-stored-and-where-is-it-stored"></a>Jakie dane są przechowywane i gdzie jest przechowywany? 

Podczas tworzenia usługi QnA Maker został wybrany region platformy Azure. Pliki dziennika i baz wiedzy, na których są przechowywane w tym regionie. 
