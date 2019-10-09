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
ms.date: 09/26/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 51d21dca1d8a5223e67cb7ea8489800989cff55c
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2019
ms.locfileid: "72026033"
---
# <a name="troubleshooting-for-qna-maker"></a>Rozwiązywanie problemów dotyczących QnA Maker

Zanadzorowana lista najbardziej często zadawanych pytań dotyczących usługi QnA Maker pomoże Ci szybciej wdrożyć usługę i uzyskać lepsze wyniki.

<a name="how-to-get-the-qnamaker-service-hostname"></a>

## <a name="how-to-get-the-qnamaker-service-endpoint"></a>Jak uzyskać punkt końcowy usługi QnAMaker

Punkt końcowy usługi QnAMaker jest przydatny do celów debugowania podczas kontaktowania się z pomocą techniczną QnAMaker lub UserVoice. Punkt końcowy jest adresem URL w tej formie: https://your-resource-name.azurewebsites.net.
    
1. Przejdź do usługi QnAMaker (Grupa zasobów) w [Azure Portal](https://portal.azure.com)

    ![QnAMaker grupę zasobów platformy Azure w Azure Portal](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

1. Wybierz App Service skojarzoną z zasobem QnA Maker. Zazwyczaj nazwy są takie same.

     ![Wybierz QnAMaker App Service](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

1. Adres URL punktu końcowego jest dostępny w sekcji Przegląd

    ![Punkt końcowy QnAMaker](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-gethostname.png)
    

## <a name="use-the-help-bot-in-the-qna-maker-portal"></a>Korzystanie z pomocy bot w portalu QnA Maker

QnA Maker udostępnia **Pomoc techniczną** bot w portalu QNA Maker. Pomoc bot jest dostępna na każdej stronie sieci Web. Bot używa QNA Maker, aby zapewnić odpowiedzi i dostarcza [ C# projekt kodu struktury bot](https://github.com/microsoft/BotBuilder-Samples/tree/master/experimental/qnamaker-support) do bot, dzięki czemu można szybko rozpocząć pracę z własnym bot odpowiedzi. 

![! [QnA Maker udostępnia * * help * * bot w portalu QnA Maker, aby Ci pomóc.] (./media/qnamaker-faq/use-qna-maker-help-bot-to-learn-qna-maker-service.png)](./media/qnamaker-faq/use-qna-maker-help-bot-to-learn-qna-maker-service.png#lightbox)

## <a name="manage-the-knowledge-base"></a>Zarządzanie bazą wiedzy

### <a name="i-accidentally-deleted-a-part-of-my-qna-maker-what-should-i-do"></a>Przypadkowo usunięto część QnA Maker, co mam zrobić? 

Nie usuwaj żadnych usług platformy Azure utworzonych wraz z zasobem QnA Maker, takich jak Search lub Web App. Są one niezbędne do pracy QnA Maker, jeśli zostaną usunięte, QnA Maker przestaną działać poprawnie.

Wszystkie usunięcia są trwałe, w tym pary pytań i odpowiedzi, pliki, adresy URL, niestandardowe pytania i odpowiedzi, bazy wiedzy lub zasoby platformy Azure. Przed usunięciem jakiejkolwiek części bazy wiedzy upewnij się, że została wyeksportowana z bazy wiedzy na stronie **Ustawienia** . 

### <a name="why-is-my-urlsfiles-not-extracting-question-answer-pairs"></a>Dlaczego moje adresy URL nie wyodrębniają par pytań i odpowiedzi?

Istnieje możliwość, że QnA Maker nie może wypakować niektórych treści pytań i odpowiedzi (QnA) z prawidłowych adresów URL często zadawanych pytań. W takich przypadkach można wkleić zawartość QnA w pliku txt i sprawdzić, czy narzędzie może go pozyskać. Alternatywnie można dodać zawartość do bazy wiedzy za pomocą [portalu QNA Maker](https://qnamaker.ai).

### <a name="how-large-a-knowledge-base-can-i-create"></a>Jak duże bazy wiedzy można tworzyć?

Rozmiar bazy wiedzy zależy od jednostki SKU usługi Azure Search, którą wybierasz podczas tworzenia usługi QnA Maker. Przeczytaj [tutaj](./Tutorials/choosing-capacity-qnamaker-deployment.md) , aby uzyskać więcej szczegółów.

### <a name="why-cant-i-see-anything-in-the-drop-down-when-i-try-to-create-a-new-knowledge-base"></a>Dlaczego nie widzę niczego na liście rozwijanej podczas próby utworzenia nowej bazy wiedzy?

Nie utworzono jeszcze żadnych usług QnA Maker na platformie Azure. Przeczytaj [tutaj](./How-To/set-up-qnamaker-service-azure.md) , aby dowiedzieć się, jak to zrobić.

### <a name="how-do-i-share-a-knowledge-base-with-others"></a>Jak mogę udostępnić bazę wiedzy innym osobom?

Udostępnianie działa na poziomie usługi QnA Maker, czyli wszystkie bazy wiedzy w usłudze zostaną udostępnione. Przeczytaj [tutaj](./How-To/collaborate-knowledge-base.md) , jak współpracować z bazą wiedzy.

### <a name="can-you-share-a-knowledge-base-with-a-contributor-that-is-not-in-the-same-aad-tenant-to-modify-a-knowledge-base"></a>Czy można udostępnić bazę wiedzy z współautorem, który nie znajduje się w tej samej dzierżawie usługi AAD, aby zmodyfikować bazę wiedzy? 

Udostępnianie odbywa się na podstawie kontroli dostępu opartej na rolach (RBAC) na platformie Azure. Jeśli możesz udostępnić _dowolny_ zasób na platformie Azure innym użytkownikom, możesz również udostępnić QNA Maker.

### <a name="if-you-have-an-app-service-plan-with-5-qnamaker-knowledge-bases-can-you-assign-readwrite-rights-to-5-different-users-so-each-of-them-can-access-only-1-qnamaker-knowledge-base"></a>Jeśli masz plan App Service z 5 QnAMaker bazami wiedzy. Czy można przypisywać prawa do odczytu i zapisu do 5 różnych użytkowników, aby każdy z nich miał dostęp tylko do 1 QnAMaker bazy wiedzy?

Możesz udostępnić całą usługę QnAMaker, a nie poszczególne bazy wiedzy.

### <a name="how-can-i-change-the-default-message-when-no-good-match-is-found"></a>Jak zmienić domyślny komunikat, gdy nie znaleziono dobrego dopasowania?

Domyślna wiadomość jest częścią ustawień w usłudze App Service.
- Przejdź do zasobu usługi App Service w Azure Portal

![qnamaker appService](./media/qnamaker-faq/qnamaker-resource-list-appservice.png)
- Kliknij opcję **Ustawienia**

![Ustawienia appService qnamaker](./media/qnamaker-faq/qnamaker-appservice-settings.png)
- Zmień wartość ustawienia **DefaultAnswer**
- Uruchom ponownie usługę App Service

![qnamaker appService ponownie](./media/qnamaker-faq/qnamaker-appservice-restart.png)

### <a name="why-is-my-sharepoint-link-not-getting-extracted"></a>Dlaczego mój link programu SharePoint nie jest wyodrębniany?

Aby uzyskać więcej informacji, zobacz [lokalizacje źródeł danych](./Concepts/data-sources-supported.md#data-source-locations) .

### <a name="the-updates-that-i-made-to-my-knowledge-base-are-not-reflected-on-publish-why-not"></a>Aktualizacje wprowadzone w bazie wiedzy nie są uwzględniane podczas publikowania. Dlaczego?

Należy zapisać każdą operację edycji, niezależnie od tego, czy w tabeli Update, test lub ustawienia, aby można było ją opublikować. Pamiętaj, aby kliknąć przycisk **Zapisz i pouczenie** po każdej operacji edycji.

### <a name="does-the-knowledge-base-support-rich-data-or-multimedia"></a>Czy baza wiedzy obsługuje bogate dane czy multimedia?

Baza wiedzy obsługuje znaczniki markdown. Jednak funkcja autowyodrębniania z adresów URL ma ograniczoną możliwość konwersji na format HTML na Jeśli chcesz używać promocji o pełnej dopracowane, możesz zmodyfikować zawartość bezpośrednio w tabeli lub przekazać bazę wiedzy z zawartością rozbudowaną.

Multimedia, takie jak obrazy i wideo, nie są w tej chwili obsługiwane.

### <a name="does-qna-maker-support-non-english-languages"></a>Czy usługa QnA Maker obsługuje języki inne niż angielski?

Zobacz więcej szczegółów dotyczących [obsługiwanych języków](./Overview/languages-supported.md).

Jeśli masz zawartość w wielu językach, pamiętaj, aby utworzyć oddzielną usługę dla każdego języka.

## <a name="manage-service"></a>Zarządzanie usługą

### <a name="when-should-i-restart-my-app-service"></a>Kiedy należy uruchomić ponownie usługę App Service? 

Odśwież usługę App Service, gdy ikona przestroga znajduje się obok wartości wersja bazy wiedzy w tabeli **klucze punktów końcowych** na [stronie](https://www.qnamaker.ai/UserSettings) **Ustawienia użytkownika** .

### <a name="i-deleted-my-existing-search-service-how-can-i-fix-this"></a>Usunięto istniejącą usługę wyszukiwania. Jak można to naprawić?

Jeśli usuniesz indeks Azure Search, operacja jest końcowa i nie będzie można odzyskać indeksu. 

### <a name="i-deleted-my-testkb-index-in-my-search-service-how-can-i-fix-this"></a>Usunięto indeks `testkb` w usłudze wyszukiwania. Jak można to naprawić? 

Nie można odzyskać starych danych. Utwórz nowy zasób QnA Maker i ponownie utwórz bazę wiedzy.

### <a name="when-should-i-refresh-my-endpoint-keys"></a>Kiedy należy odświeżyć klucze punktów końcowych?

Odśwież klucze punktów końcowych, jeśli podejrzewasz, że zostały naruszone.

### <a name="can-i-use-the-same-azure-search-resource-for-knowledge-bases-using-multiple-languages"></a>Czy można używać tego samego zasobu Azure Search dla baz wiedzy przy użyciu wielu języków?

Aby korzystać z wielu języków i wielu baz wiedzy, użytkownik musi utworzyć zasób QnA Maker dla każdego języka. Spowoduje to utworzenie oddzielnej usługi Azure Search dla każdego języka. Mieszanie różnych baz wiedzy w ramach jednej usługi Azure Search spowoduje spadek wydajności wyników.

### <a name="how-can-i-change-the-name-of-the-azure-search-resource-used-by-qna-maker"></a>Jak mogę zmienić nazwę zasobu Azure Search używanego przez QnA Maker?

Nazwa zasobu Azure Search to QnA Maker nazwa zasobu z niektórymi losowymi literami dołączanymi na końcu. Dzięki temu trudno jest rozróżnić wiele zasobów wyszukiwania dla QnA Maker. Utwórz oddzielną usługę Azure Search (nazywaj ją w sposób, w jaki chcesz) i połącz ją z usługą QnA. Kroki są podobne do kroków, które należy wykonać, aby [uaktualnić usługę Azure Search](How-To/set-up-qnamaker-service-azure.md#upgrade-the-azure-search-service).

### <a name="when-qna-maker-returns-runtime-core-is-not-initialized-how-do-i-fix-it"></a>Gdy QnA Maker zwraca `Runtime core is not initialized,` jak rozwiązać ten problem?

Miejsce na dysku dla usługi App Service może być pełne. Procedura naprawy miejsca na dysku:

1. W [Azure Portal](https://portal.azure.com)wybierz usługę App Service QNA Maker, a następnie Zatrzymaj usługę.
1. Gdy nadal znajduje się w usłudze App Service, wybierz pozycję **Narzędzia programistyczne**, a następnie **Narzędzia zaawansowane**, a następnie pozycję **Przejdź**. Spowoduje to otwarcie nowego okna przeglądarki.
1. Wybierz pozycję **konsola debugowania**, a następnie polecenie **cmd** , aby otworzyć narzędzie wiersza polecenia. 
1. Przejdź do _lokalizacji site/wwwroot/Data/QnAMaker/_ Directory.
1. Usuń wszystkie foldery, których nazwy zaczynają się od `rd`. 

    **Nie usuwaj** następujących danych:

    * Plik KbIdToRankerMappings. txt
    * Plik EndpointSettings. JSON
    * Folder EndpointKeys 

1. Uruchom usługę App Service.
1. Uzyskaj dostęp do bazy wiedzy, aby sprawdzić, czy teraz działa. 


## <a name="integrate-with-other-services-including-bots"></a>Integracja z innymi usługami, w tym botów

### <a name="do-i-need-to-use-bot-framework-in-order-to-use-qna-maker"></a>Czy do używania narzędzia QnA Maker potrzebna jest struktura botów?

Nie, nie musisz używać [platformy bot](https://github.com/Microsoft/botbuilder-dotnet) z QNA Maker. QnA Maker jest jednak oferowany jako jeden z kilku szablonów w programie [Azure bot Service](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0). Usługa bot umożliwia szybkie inteligentne programowanie bot przy użyciu programu Microsoft bot Framework i działa w środowisku bez serwera.

### <a name="how-can-i-create-a-new-bot-with-qna-maker"></a>Jak utworzyć nowy Bot z QnA Maker?

Postępuj zgodnie z instrukcjami w [tej](./Tutorials/create-qna-bot.md) dokumentacji, aby utworzyć bot za pomocą Azure bot Service.

### <a name="how-do-i-use-a-different-knowledge-base-with-an-existing-azure-bot-service"></a>Jak mogę użyć innej bazy wiedzy z istniejącą usługą Azure bot?

Musisz mieć następujące informacje na temat bazy wiedzy:

* Identyfikator bazy wiedzy.
* Niestandardowa nazwa domeny podrzędnej punktu końcowego bazy wiedzy, znana jako `host`, znajduje się na stronie **Ustawienia** po opublikowaniu.
* Klucz punktu końcowego opublikowany w bazie wiedzy — znajduje się na stronie **Ustawienia** po opublikowaniu. 

Korzystając z tych informacji, przejdź do usługi App Service bot w Azure Portal. W obszarze **ustawienia > Konfiguracja — > Ustawienia aplikacji**Zmień te wartości.  

Klucz punktu końcowego bazy wiedzy ma etykietę `QnAAuthkey` w usłudze ABS. 

### <a name="can-two-or-more-client-applications-share-a-knowledge-base"></a>Czy co najmniej dwie aplikacje klienckie współdzielą bazę wiedzy? 

Tak. w bazie wiedzy można wykonywać zapytania z dowolnej liczby klientów. Jeśli odpowiedź z bazy wiedzy wydaje się powolnić lub przekroczyć limit czasu, rozważ uaktualnienie warstwy usług dla usługi App Service skojarzonej z bazą wiedzy.

### <a name="how-do-i-embed-the-qna-maker-service-in-my-website"></a>Jak osadzić usługę QnA Maker w witrynie internetowej?

Wykonaj następujące kroki, aby osadzić usługę QnA Maker jako kontrolkę rozmowy w sieci Web w witrynie internetowej:

1. Utwórz bot często zadawanych pytań, postępując zgodnie z instrukcjami znajdującymi się [tutaj](./Tutorials/create-qna-bot.md).
2. Włącz rozmowę internetową, wykonując kroki opisane [tutaj](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-webchat) .

## <a name="data-storage"></a>Magazyn danych

### <a name="what-data-is-stored-and-where-is-it-stored"></a>Jakie dane są przechowywane i gdzie są przechowywane? 

Po utworzeniu usługi QnA Maker wybrano region platformy Azure. Bazy wiedzy i pliki dzienników są przechowywane w tym regionie. 
