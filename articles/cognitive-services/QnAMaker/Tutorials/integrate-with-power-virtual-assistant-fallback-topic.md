---
title: 'Samouczek: Integracja z power virtual agentem - QnA Maker'
description: W tym samouczku popraw jakość swojej bazy wiedzy dzięki aktywnemu uczeniu się. Przeglądaj, akceptuj lub odrzucaj, dodawaj bez usuwania lub zmieniania istniejących pytań.
ms.topic: tutorial
ms.date: 03/11/2020
ms.openlocfilehash: 283667c587e395a1d712f82f3385582b4c5c3227
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2020
ms.locfileid: "80398877"
---
# <a name="tutorial-add-knowledge-base-to-power-virtual-agent"></a>Samouczek: Dodawanie bazy wiedzy do agenta wirtualnego zasilania
Utwórz i rozszerz [bota agenta wirtualnego zasilania,](https://powervirtualagents.microsoft.com/) aby zapewnić odpowiedzi z bazy wiedzy.

**Ten samouczek zawiera informacje na temat wykonywania następujących czynności:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Tworzenie agenta wirtualnego zasilania
> * Tworzenie tematu rezerwowego systemu
> * Dodaj QnA Maker jako akcję do tematu jako przepływ automatyzacji zasilania
> * Tworzenie rozwiązania Power Automate
> * Dodaj przepływ automatyzacji zasilania do rozwiązania
> * Publikowanie agenta wirtualnego zasilania
> * Test Power Virtual Agent, odbieranie odpowiedzi z bazy wiedzy QnA Maker

## <a name="integrate-a-power-virtual-agent-with-a-knowledge-base"></a>Integracja wirtualnego agenta zasilania z bazą wiedzy

[Power Virtual Agents](https://powervirtualagents.microsoft.com/) umożliwia zespołom łatwe tworzenie potężnych botów za pomocą interfejsu graficznego z przewodnikiem bez kodu bez potrzeby stosowania przez analityków danych lub programistów.

Agent wirtualny zasilania jest tworzony z serii tematów (obszarów tematycznych), aby odpowiedzieć na pytania użytkownika, wykonując akcje. Jeśli nie można znaleźć odpowiedzi, rezerwowy system może zwrócić odpowiedź.

Skonfiguruj agenta, aby wysłać pytanie do bazy wiedzy w ramach działania tematu lub jako część ścieżki tematu **rezerwowego systemu.** Obaj używają tego samego mechanizmu akcji, aby połączyć się z bazą wiedzy i zwrócić odpowiedź.

## <a name="power-automate-connects-to-generateanswer-action"></a>Power Automate łączy się z działaniem GenerateAnswer

Aby połączyć agenta z bazą wiedzy, użyj power automate, aby utworzyć akcję. Power Automate zapewnia **przepływ**procesu, który łączy się z QnA Maker GenerateAnswer API.

Po zaprojektowaniu i zapisaniu **przepływu** jest on dostępny w **uikw.**  Po generateanswer przepływ jest dodawany do rozwiązania, należy użyć tego rozwiązania jako akcji w agencie.

## <a name="process-steps-to-connect-an-agent-to-your-knowledge-base"></a>Czynności procesowe w celu połączenia agenta z bazą wiedzy

Poniższe kroki są przedstawione jako omówienie, aby ułatwić zrozumienie, jak kroki odnoszą się do celu połączenia agenta wirtualnego zasilania z bazą wiedzy QnA Maker.

Kroki, aby użyć agenta power virtual z QnA Maker:
* W portalu [QnA Maker](https://www.qnamaker.ai/)
    * Tworzenie i publikowanie bazy wiedzy
    * Kopiowanie szczegółów bazy wiedzy, w tym identyfikator bazy wiedzy, klucz punktu końcowego środowiska uruchomieniowego i hosta punktu końcowego środowiska uruchomieniowego.
* Portal [Agent wirtualny zasilania](https://powerva.microsoft.com/)
    * Temat agenta kompilacji
    * Wywołanie akcji (do power automatyzuj przepływ)
* W [portalu Power Automate](https://us.flow.microsoft.com/)
    * Zbuduj przepływ ze złączem do [programu QnA Maker GenerateAnswer](https://docs.microsoft.com/connectors/cognitiveservicesqnamaker/)
        * QnA Maker opublikował informacje o bazie wiedzy
            * Identyfikator bazy wiedzy
            * Host punktu końcowego zasobu programu QnA Maker
            * Klucz punktu końcowego zasobu programu QnA Maker
        * Dane wejściowe — zapytanie użytkownika
        * Wyjście - odpowiedź bazy wiedzy
    * Tworzenie rozwiązania i dodawanie przepływu
* Powrót do agenta wirtualnego zasilania
    * Wybieranie danych wyjściowych rozwiązania jako komunikatu dla tematu

## <a name="create-and-publish-a-knowledge-base"></a>Tworzenie i publikowanie bazy wiedzy

1. Postępuj zgodnie z [przewodnikiem Szybki start,](../Quickstarts/create-publish-knowledge-base.md) aby utworzyć bazę wiedzy. Nie należy wypełniać ostatniej sekcji, aby utworzyć bota. Ten samouczek jest zamiennikiem ostatniej sekcji przewodnika Szybki start, ponieważ ten samouczek używa agenta wirtualnego zasilania do utworzenia bota, zamiast bota frameworka w przewodniku Szybki start.

    > [!div class="mx-imgBorder"]
    > ![Wprowadź opublikowane ustawienia bazy wiedzy znajdujące się na stronie **Ustawienia**https://www.qnamaker.ai/) w portalu [QnA Maker].](../media/how-to-integrate-power-virtual-agent/published-knowledge-base-settings.png)

    Te informacje będą potrzebne do [kroku Power Automate,](#create-power-automate-flow-to-connect-to-your-knowledge-base) aby skonfigurować połączenie QnA Maker GenerateAnswer.

1. Znajdź klucz punktu końcowego, host punktu końcowego i identyfikator bazy wiedzy na stronie **Ustawienia** w portalu QnA Maker.

## <a name="create-power-virtual-agent"></a>Tworzenie agenta wirtualnego zasilania

1. [Zaloguj się do](https://go.microsoft.com/fwlink/?LinkId=2108000&clcid=0x409) wirtualnego agenta zasilania za pomocą szkolnego lub służbowego konta e-mail.
1. Jeśli jest to twój pierwszy bot, powinieneś znajdować się na stronie **głównej** agenta. Jeśli nie jest to twój pierwszy agent wirtualny zasilania, wybierz bota z pierwszej prawej nawigacji i wybierz **+ Nowy Bot**.

    > [!div class="mx-imgBorder"]
    > ![Wprowadź opublikowane ustawienia bazy wiedzy znajdujące się na stronie **Ustawienia**https://www.qnamaker.ai/) w portalu [QnA Maker].](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-home.png)

## <a name="several-topics-are-provided-in-the-bot"></a>Kilka tematów znajduje się w bot

Agent używa kolekcji tematów, aby odpowiedzieć na pytania w obszarze tematu. W tym samouczku, agent ma wiele tematów przewidzianych dla Ciebie, podzielone na **tematy użytkownika** i **tematów systemowych**.

> [!div class="mx-imgBorder"]
> ![Agent używa kolekcji tematów, aby odpowiedzieć na pytania w obszarze tematu. W tym samouczku agent ma wiele tematów przeznaczonych do podziału na **Tematy użytkownika** i **Tematy systemowe**.](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-topics-provided.png)


## <a name="create-power-virtual-agents-system-fallback-topic"></a>Tworzenie programu zasilania wirtualnego agenta system rezerwowego tematu

Agent może połączyć się z bazą wiedzy z dowolnego tematu, w tym samouczku użyto tematu **Rezerwa** systemu. Temat rezerwowy jest używany, gdy agent nie może znaleźć odpowiedzi. Agent przekazuje tekst użytkownika do interfejsu API GenerateAnswer programu QnA Maker, odbiera odpowiedź z bazy wiedzy i wyświetla ją z powrotem do użytkownika jako komunikat.

1. W portalu [Power Virtual Agents](https://powerva.microsoft.com/#/) w prawym górnym rogu nawigacji wybierz stronę **Ustawienia.** Ikoną tej strony jest sprzęt. Wybierz **opcję Rezerwa systemowa**.

    > [!div class="mx-imgBorder"]
    > ![Element menu agenta zasilania wirtualnego dla rezerwowego systemu](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-settings-system-fallback.png)

1. W wyskakującym oknie **Ustawienia** wybierz pozycję **+ Dodaj,** aby dodać temat Rezerwowy systemu.

    > [!div class="mx-imgBorder"]
    > ![W oknie Ustawienia dodaj temat rezerwowy.](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-settings-add-fallback-topic.png)

1. Po dodaniu tematu wybierz pozycję **Przejdź do tematu rezerwowego,** aby zatworzeć temat rezerwowy na kanwie tworzenia.

    > [!TIP]
    > Jeśli chcesz powrócić do tematu rezerwowego, jest on dostępny w sekcji **Tematy,** jako część tematów **systemowych.**

## <a name="use-authoring-canvas-to-add-an-action"></a>Dodawanie akcji za pomocą kanwy tworzenia

Użyj kanwy tworzenia agentów wirtualnych zasilania, aby połączyć temat rezerwowy z bazą wiedzy. Temat rozpoczyna się od **nierozpoznanego tekstu użytkownika**. Dodaj akcję, która przekazuje ten tekst do programu QnA Maker, a następnie wyświetla odpowiedź jako wiadomość. Ostatni krok wyświetlania odpowiedzi jest obsługiwany jako [oddzielny krok](#add-solutions-flow-to-power-virtual-agent) w dalszej części tego samouczka.

W tej sekcji tworzy przepływ konwersacji tematu rezerwowego.

1. Nowa akcja rezerwowa może już mieć elementy przepływu konwersacji. Usuń element **Eskalacja,** wybierając menu Opcje.

    > [!div class="mx-imgBorder"]
    > ![Rozpocznij akcję rezerwy z frazami wyzwalacza](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-fallback-topic-delete-escalate.png)

1. Zaznacz **+** łącznik płynący z okna **Komunikat,** a następnie wybierz pozycję **Wywołaj akcję**.

    > [!div class="mx-imgBorder"]
    > ![Wywołanie akcji](../media/how-to-integrate-power-virtual-agent/create-new-item-call-an-action.png)

1. Wybierz pozycję **Utwórz przepływ**. Proces przenosi cię do **Power Automate**, innego portalu opartego na przeglądarce.

    > [!div class="mx-imgBorder"]
    > ![Wywołanie akcji](../media/how-to-integrate-power-virtual-agent/create-a-flow.png)

## <a name="create-power-automate-flow-to-connect-to-your-knowledge-base"></a>Tworzenie usługi Power Automate Flow w celu połączenia się z bazą wiedzy

Poniższa procedura tworzy przepływ **automatyzacji zasilania,** który:
* pobiera przychodzący tekst użytkownika
* wysyła go do QnA Maker
* przypisuje najwyższą odpowiedź QnA Maker do zmiennej
* wysyła zmienną (odpowiedź górną) jako odpowiedź z powrotem do agenta

1. W **umięśna automatyzacja** **szablon przepływu** jest uruchamiany. W elemencie przepływu **agentów wirtualnych zasilania** wybierz pozycję **Edytuj,** aby skonfigurować zmienną wejściową pochodzącą z agenta do bazy wiedzy. Zmienna wejściowa oparta na tekście jest pytaniem tekstowym przesłanym przez użytkownika od agenta.

    > [!div class="mx-imgBorder"]
    > ![Konfigurowanie zmiennej wejściowej jako ciągu tekstowego](../media/how-to-integrate-power-virtual-agent/power-automate-configure-input-variable.png)

1. Dodaj wprowadzanie tekstu i `InputText` nazwij `IncomingUserQuestion`zmienną z opisem . To nazewnictwo pomaga odróżnić tekst wejściowy od tekstu wyjściowego utworzonego później.

    > [!div class="mx-imgBorder"]
    > ![Dodaj dane wejściowe tekstu i nazwij zmienną "InputText" z opisem "UserQuestion"](../media/how-to-integrate-power-virtual-agent/power-automate-configure-input-variable-name-and-description.png)

1. Wybierz **+** łącznik płynący z pola **Power Virtual Agents,** aby wstawić nowy krok w przepływie (przed **wartością zwracaną do agenta wirtualnego zasilania),** a następnie wybierz pozycję **Dodaj akcję**.

1. `Qna` Wyszukaj, aby znaleźć akcje **programu QnA Maker,** a następnie wybierz pozycję **Generuj odpowiedź**.

    > [!div class="mx-imgBorder"]
    > ![Wyszukaj "Qna", aby znaleźć **QnA Maker** akcje, a następnie wybierz **Wygeneruj odpowiedź**](../media/how-to-integrate-power-virtual-agent/generate-answer-action-selected.png)

    Trzy (3) wymagane ustawienia połączenia dla programu QnA Maker pojawiają się w akcji, a ustawienia pytania z agenta wirtualnego zasilania.

    > [!div class="mx-imgBorder"]
    > ![Ustawienia połączeń dla programu QnA Maker są wyświetlane w akcji.](../media/how-to-integrate-power-virtual-agent/generate-answer-knowledge-base-settings.png)

1. Skonfiguruj akcję za pomocą identyfikatora bazy wiedzy, hosta punktu końcowego i klucza końcowego. Znajdują się one na stronie **Ustawienia** bazy wiedzy w portalu QnA Maker.

    > [!div class="mx-imgBorder"]
    > ![Wprowadź opublikowane ustawienia bazy wiedzy znajdujące się na stronie **Ustawienia**https://www.qnamaker.ai/) w portalu [QnA Maker].](../media/how-to-integrate-power-virtual-agent/published-knowledge-base-settings.png)

1. Aby skonfigurować **pytanie,** zaznacz pole tekstowe, a następnie wybierz `InputText` je z listy.

1. Aby wstawić nowy krok w **+** przepływie, wybierz łącznik płynący z pola **Akcji Generuj odpowiedź,** a następnie wybierz pozycję **Dodaj akcję**.

1. Aby dodać zmienną do przechwytywania tekstu odpowiedzi zwróconego z `Initialize variable` GenerateAnswer, wyszukaj i wybierz akcję.

    Ustaw nazwę zmiennej `OutgoingQnAAnswer`na , i wybierz typ jako **Ciąg**. Nie ustawiaj **wartości**.

    > [!div class="mx-imgBorder"]
    > ![Ustaw nazwę zmiennej na "QnAAnswer" i wybierz typ jako **String**](../media/how-to-integrate-power-virtual-agent/initialize-output-variable-for-qna-answer.png)

1. Aby wstawić nowy krok w **+** przepływie, wybierz łącznik płynący z pola akcji **Inicjuj** **zmienną,** a następnie wybierz pozycję Dodaj akcję .

1. Aby ustawić całą odpowiedź JSON bazy wiedzy na zmienną, wyszukaj i wybierz`Apply to each` akcję. Wybierz opcję GenerateAnswer `answers`.

1. Aby zwrócić tylko górną odpowiedź, w tym samym **polu Zastosuj do każdego** wybierz pozycję Dodaj **akcję**. Wyszukaj i wybierz **pozycję Ustaw zmienną**.

    W polu **Ustaw zmienną** zaznacz pole tekstowe **Nazwa**, a następnie wybierz z listy pozycję **OutgoingQnAAnswer.**

    Zaznacz pole tekstowe **dla wartości ,** a następnie wybierz pozycję Odpowiedź **odpowiedzi** z listy.

    > [!div class="mx-imgBorder"]
    > ![Ustawianie nazwy i wartości zmiennej](../media/how-to-integrate-power-virtual-agent/power-automate-flow-apply-to-each-set-variable.png)

1. Aby zwrócić zmienną (i jej wartość), wybierz wartość zwrotu do przepływu **agenta wirtualnego zasilania,** a następnie wybierz pozycję **Edytuj,** a następnie **dodaj dane wyjściowe**. Zaznacz typ wyjścia **tekst,** a `FinalAnswer`następnie wprowadź **tytuł** . Zaznacz pole tekstowe **dla wartości** `OutgoingQnAAnswer` , a następnie wybierz zmienną.

    > [!div class="mx-imgBorder"]
    > ![Ustawianie wartości zwracanej](../media/how-to-integrate-power-virtual-agent/power-automate-flow-return-value.png)

1. Wybierz **pozycję Zapisz,** aby zapisać przepływ.

## <a name="create-solution-and-add-flow"></a>Tworzenie rozwiązania i dodawanie przepływu

Aby agent wirtualny zasilania znalazł przepływ i się z tym połączył, przepływ musi być dołączony do rozwiązania Power Automate.

1. Będąc jeszcze w portalu Power Automate, wybierz **rozwiązania** z nawigacji po lewej stronie.

1. Wybierz pozycję **+ Nowe rozwiązanie**.

1. Podaj nazwę wyświetlaną. Lista rozwiązań zawiera każde rozwiązanie w organizacji lub szkole. Wybierz konwencję nazewnictwa, która ułatwia filtrowanie tylko do rozwiązań, takich `jondoe-power-virtual-agent-qnamaker-fallback`jak prefiks wiadomości e-mail do nazwy rozwiązania: .

1. Wybierz wydawcę z listy opcji.

1. Zaakceptuj domyślne wartości nazwy i wersji.

1. Wybierz **pozycję Utwórz,** aby zakończyć proces.

## <a name="add-flow-to-solution"></a>Dodawanie przepływu do rozwiązania

1. Na liście rozwiązań wybierz rozwiązanie, które właśnie utworzyłeś. Powinien znajdować się na szczycie listy. Jeśli tak nie jest, wyszukaj według nazwy e-mail, która jest częścią nazwy rozwiązania.

1. W rozwiązaniu wybierz **+ Dodaj istniejące**, a następnie wybierz **przepływ** z listy.

1. Znajdź swój przepływ, a następnie wybierz pozycję **Dodaj,** aby zakończyć proces. Jeśli istnieje wiele przepływów, spójrz na **Modified** kolumny, aby znaleźć najnowszy przepływ.

## <a name="add-solutions-flow-to-power-virtual-agent"></a>Dodaj przepływ rozwiązania do power virtual agenta

1. Wróć do karty przeglądarki za pomocą agenta wirtualnego zasilania. Kanwa do tworzenia treści powinna być nadal otwarta.

1. Zaznacz **+** łącznik w polu Akcji **Wiadomość,** aby wstawić nowy krok w przepływie, a następnie wybierz pozycję **Wywołaj akcję**.

1. W nowej akcji wybierz wartość wejściową **NierozpoznanyFrzew .** Spowoduje to przepuszcze tekst z agenta do przepływu.

    > [!div class="mx-imgBorder"]
    > ![W nowej akcji wybierz wartość wejściową **UnrecognizedTriggerPhrase**.](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-select-unrecognized-trigger-phrase.png)

1. Zaznacz **+** łącznik pod polem **Akcja,** aby wstawić nowy krok w przepływie, a następnie wybierz pozycję **Pokaż wiadomość**.

1. Wprowadź tekst `Your answer is:`wiadomości i `FinalAnswer` wybierz jako zmienną kontekstową za pomocą funkcji paska narzędzi w miejscu.

    > [!div class="mx-imgBorder"]
    > ![Wprowadź tekst wiadomości i "FinalAnswer" z Power Automate Flow.](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-topic-authoring-canvas-show-message-final-answer.png)

1. Wybierz **pozycję Zapisz** z paska narzędzi kontekstu, aby zapisać szczegóły kanwy do tworzenia tematu.

Poniżej przedstawiono końcowe płótno.

> [!div class="mx-imgBorder"]
> ![Końcowe płótno agenta](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-topic-authoring-canvas-full-flow.png)

## <a name="test-power-virtual-agent"></a>Agent wirtualny zasilania testowego

1. W okienku testowym przełącz połącz **utwór między tematami**. Dzięki temu można obserwować postęp między tematami, jak również w jednym temacie.

1. Przetestuj agenta, wprowadzając tekst użytkownika w kolejności podanej poniżej. Kanwa tworzenia raportuje pomyślne kroki z zielonym znacznikiem wyboru.

|Kolejność pytań|Pytania testowe|Przeznaczenie|
|--|--|--|
|1|Hello|Rozpocznij konwersację|
|2|Godziny przechowywania|Przykładowy temat — skonfigurowany dla Ciebie bez żadnej dodatkowej pracy z Twojej strony.|
|3|Tak|W odpowiedzi na`Did that answer your question?`|
|4|Excellent (Doskonały)|W odpowiedzi na`Please rate your experience.`|
|5|Tak|W odpowiedzi na`Can I help with anything else?`|
|6|Co to jest baza wiedzy?|To pytanie wyzwala akcję rezerwową, która wysyła tekst do bazy wiedzy, aby odpowiedzieć, a następnie zostanie wyświetlona odpowiedź. |

> [!div class="mx-imgBorder"]
> ![Końcowe płótno agenta](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-test-tracked.png)

## <a name="publish-your-bot"></a>Opublikuj swojego bota

Aby udostępnić agenta wszystkim członkom szkoły lub organizacji, musisz go opublikować.

1. Wybierz **pozycję Publikuj** z lewej strony nawigacji, a następnie wybierz pozycję **Publikuj** na stronie.

1. Wypróbuj bota na stronie demonstracyjnej, pod warunkiem, że link poniżej przycisku **Publikuj** .

    Otworzy się nowa strona internetowa z botem. Zadaj botowi to samo pytanie testowe:`What is a knowledge base?`

    > [!div class="mx-imgBorder"]
    > ![Końcowe płótno agenta](../media/how-to-integrate-power-virtual-agent/demo-chat-bot.png)

## <a name="share-your-bot"></a>Udostępnij swojego bota

Aby udostępnić witrynę demonstracyjną, skonfiguruj ją jako kanał.

1. Wybierz **pozycję Zarządzaj,** a następnie z lewej strony nawigacji. **Channels**

1. Wybierz **pozycję Demonstruj witrynę sieci Web** z listy kanałów.

1. Skopiuj łącze i wybierz pozycję **Zapisz**. Wklej link do witryny demonstracyjnej w wiadomości e-mail do członków szkoły lub organizacji.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po zakończeniu pracy z bazy wiedzy, usuń zasoby QnA Maker w witrynie Azure portal.

## <a name="next-step"></a>Następny krok

[Uzyskiwanie danych analitycznych na potrzeby bazy wiedzy](../How-To/get-analytics-knowledge-base.md)

Dowiedz się więcej o usługach:
* [Agenci Power Virtual Agent](https://docs.microsoft.com/power-virtual-agents/)
* [Power Automate](https://docs.microsoft.com/power-automate/)
* [Złącze QnA Maker](https://us.flow.microsoft.com/connectors/shared_cognitiveservicesqnamaker/qna-maker/) i [ustawienia złącza](https://docs.microsoft.com/connectors/cognitiveservicesqnamaker/)