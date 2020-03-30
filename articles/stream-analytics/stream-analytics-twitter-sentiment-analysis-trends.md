---
title: Analiza nastrojów na Twitterze w czasie rzeczywistym za pomocą usługi Azure Stream Analytics
description: W tym artykule opisano, jak używać usługi Stream Analytics do analizy nastrojów na Twitterze w czasie rzeczywistym. Wskazówki krok po kroku od generowania zdarzeń do danych na pulpicie nawigacyjnym na żywo.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: 4b265bb574895e4728ad93ee25c9dad0da226ea4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240302"
---
# <a name="real-time-twitter-sentiment-analysis-in-azure-stream-analytics"></a>Analiza opinii w usłudze Twitter w czasie rzeczywistym za pomocą usługi Azure Stream Analytics

W tym artykule dowiesz się, jak utworzyć rozwiązanie analizy nastrojów w mediach społecznościowych, wprowadzając zdarzenia twittera w czasie rzeczywistym do usługi Azure Event Hubs. Piszesz zapytanie usługi Azure Stream Analytics, aby analizować dane i przechowywać wyniki do późniejszego użycia lub utworzyć pulpit nawigacyjny [usługi Power BI,](https://powerbi.com/) aby zapewnić szczegółowe informacje w czasie rzeczywistym.

Narzędzia do analizy mediów społecznościowych pomagają organizacjom zrozumieć popularne tematy. Popularne tematy to tematy i postawy, które mają dużą liczbę postów w mediach społecznościowych. Analiza nastrojów, która jest również nazywana *eksploracją opinii,* wykorzystuje narzędzia do analizy mediów społecznościowych do określania postaw wobec produktu lub pomysłu. 

Analiza trendów na Twitterze w czasie rzeczywistym jest doskonałym przykładem narzędzia analitycznego, ponieważ model subskrypcji hashtagów umożliwia słuchanie konkretnych słów kluczowych (hashtagów) i rozwijanie analizy nastrojów w kanale.

## <a name="scenario-social-media-sentiment-analysis-in-real-time"></a>Scenariusz: Analiza nastrojów w mediach społecznościowych w czasie rzeczywistym

Firma, która ma stronę internetową mediów informacyjnych jest zainteresowana uzyskaniem przewagi nad konkurentami poprzez prezentowanie treści witryny, które są natychmiast istotne dla jej czytelników. Firma wykorzystuje analizę mediów społecznościowych na tematy, które są istotne dla czytelników, wykonując analizę nastrojów w czasie rzeczywistym danych Twittera.

Aby zidentyfikować popularne tematy w czasie rzeczywistym na Twitterze, firma potrzebuje analizy w czasie rzeczywistym na temat głośności tweetów i nastrojów dla kluczowych tematów.

## <a name="prerequisites"></a>Wymagania wstępne

W tym poradniku możesz użyć aplikacji klienckiej, która łączy się z Twitterem i wyszukuje tweety, które mają określone hashtagi (które możesz ustawić). Aby uruchomić aplikację i analizować tweety przy użyciu usługi Azure Streaming Analytics, musisz mieć następujące właściwości:

* Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/).

* Konto [na Twitterze.](https://twitter.com)

* Aplikacja TwitterClientCore, która czyta kanał Twitter. Aby uzyskać tę aplikację, pobierz [TwitterClientCore](https://github.com/Azure/azure-stream-analytics/tree/master/DataGenerators/TwitterClientCore).

* Zainstaluj [plik .NET Core CLI](https://docs.microsoft.com/dotnet/core/tools/?tabs=netcore2x) w wersji 2.1.0.

## <a name="create-an-event-hub-for-streaming-input"></a>Tworzenie centrum zdarzeń do przesyłania strumieniowego danych wejściowych

Przykładowa aplikacja generuje zdarzenia i wypycha je do centrum zdarzeń platformy Azure. Usługi Azure Event Hubs są preferowaną metodą pozyskiwania zdarzeń dla usługi Stream Analytics. Aby uzyskać więcej informacji, zobacz [dokumentację usługi Azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md).

### <a name="create-an-event-hub-namespace-and-event-hub"></a>Tworzenie obszaru nazw centrum zdarzeń i centrum zdarzeń
W tej sekcji utworzysz obszar nazw centrum zdarzeń i dodasz centrum zdarzeń do tej przestrzeni nazw. Przestrzenie nazw centrum zdarzeń są używane do logicznej grupowanie wystąpień magistrali zdarzeń powiązanych. 

1. Zaloguj się do portalu Azure i wybierz pozycję **Utwórz zasób**. Następnie. wyszukaj **Centra zdarzeń** i wybierz pozycję **Utwórz**.

2. Na stronie **Tworzenie obszaru nazw** wprowadź nazwę obszaru nazw. Można użyć dowolnej nazwy dla obszaru nazw, ale nazwa musi być prawidłowa dla adresu URL i musi być unikatowa na platformie Azure. 
    
3. Wybierz warstwę cenową i subskrypcję oraz utwórz lub wybierz grupę zasobów. Następnie wybierz lokalizację i wybierz pozycję **Utwórz**. 
 
4. Po zakończeniu wdrażania obszaru nazw przejdź do grupy zasobów i znajdź obszar nazw centrum zdarzeń na liście zasobów platformy Azure. 

5. W nowej przestrzeni nazw ** + &nbsp;** wybierz pozycję Centrum zdarzeń . 

6. Nazwij nowe centrum zdarzeń *socialtwitter-eh*. Możesz użyć innej nazwy. Jeśli to zrobisz, zanotuj to, ponieważ potrzebujesz nazwy później. Nie trzeba ustawiać żadnych innych opcji dla centrum zdarzeń.
 
7. Wybierz **pozycję Utwórz**.

### <a name="grant-access-to-the-event-hub"></a>Udzielanie dostępu do centrum zdarzeń

Zanim proces może wysyłać dane do centrum zdarzeń, centrum zdarzeń potrzebuje zasad, które umożliwiają dostęp. Zasady dostępu generują parametry połączenia, które zawierają informacje o autoryzacji.

1.  Na pasku nawigacyjnym po lewej stronie obszaru nazw centrów zdarzeń wybierz pozycję **Centrum zdarzeń**, które znajduje się w sekcji **Jednostki.** Następnie wybierz właśnie utworzony centrum zdarzeń.

2.  Na pasku nawigacyjnym po lewej stronie wybierz pozycję **Zasady dostępu współdzielonego znajdujące** się w obszarze **Ustawienia**.

    >[!NOTE]
    >Istnieje opcja zasady dostępu współdzielonego w obszarze obszaru nazw centrum zdarzeń i centrum zdarzeń. Upewnij się, że pracujesz w kontekście centrum zdarzeń, a nie w ogólnej przestrzeni nazw centrum zdarzeń.

3.  Na stronie zasad dostępu wybierz pozycję **+ Dodaj**. Następnie wprowadź *dostęp do programu socialtwitter* dla **nazwy zasad** i zaznacz pole wyboru **Zarządzaj.**
 
4.  Wybierz **pozycję Utwórz**.

5.  Po wdrożeniu zasad wybierz zasady z listy zasad dostępu współdzielonego.

6.  Znajdź pole oznaczone **jako Główny klucz ciągu połączenia** i wybierz przycisk kopiowania obok ciągu połączenia.
 
7.  Wklej parametry połączenia do edytora tekstu. Ten ciąg połączenia jest potrzebny do następnej sekcji po dokonaniu kilku drobnych zmian.

   Parametry połączenia wyglądają następująco:
   
   ```
   Endpoint=sb://EVENTHUBS-NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=socialtwitter-access;SharedAccessKey=Gw2NFZw6r...FxKbXaC2op6a0ZsPkI=;EntityPath=socialtwitter-eh
   ```

   Należy zauważyć, że ciąg połączenia zawiera wiele par klucz-wartość, `SharedAccessKey`oddzielonych `EntityPath`średnikami: `Endpoint`, `SharedAccessKeyName`, , i .  

   > [!NOTE]
   > Ze względów bezpieczeństwa części ciągu połączenia w przykładzie zostały usunięte.

## <a name="configure-and-start-the-twitter-client-application"></a>Konfigurowanie i uruchamianie aplikacji klienckiej Twittera

Aplikacja kliencka pobiera zdarzenia tweet bezpośrednio z Twitter. W tym celu wymaga uprawnień do wywoływania interfejsów API przesyłania strumieniowego twittera. Aby skonfigurować to uprawnienie, należy utworzyć aplikację w aplikacji Twitter, która generuje unikatowe poświadczenia (takie jak token OAuth). Następnie można skonfigurować aplikację kliencką do używania tych poświadczeń podczas wykonywania wywołań interfejsu API. 

### <a name="create-a-twitter-application"></a>Tworzenie aplikacji usługi Twitter
Jeśli nie masz jeszcze aplikacji Twitter, której możesz użyć do tego przewodnika, możesz ją utworzyć. Musisz mieć już konto na Twitterze.

> [!NOTE]
> Dokładny proces w Aplikacji Twitter do tworzenia aplikacji i pobieranie kluczy, wpisów tajnych i token może ulec zmianie. Jeśli te instrukcje nie są zgodne z tym, co widzisz w witrynie Twittera, zapoznaj się z dokumentacją programisty Twittera.

1. W przeglądarce internetowej przejdź do [aplikacji Twitter Dla deweloperów](https://developer.twitter.com/en/apps), utwórz konto dewelopera i wybierz pozycję **Utwórz aplikację**. Może pojawić się komunikat z informacją, że musisz złożyć wniosek o konto programisty na Twitterze. Nie krępuj się to zrobić, a po zatwierdzeniu wniosku powinieneś zobaczyć wiadomość e-mail z potwierdzeniem. Zatwierdzenie konta dewelopera może potrwać kilka dni.

   ![Szczegóły aplikacji na Twitterze](./media/stream-analytics-twitter-sentiment-analysis-trends/provide-twitter-app-details.png "Szczegóły aplikacji na Twitterze")

2. Na stronie **Create an application (Tworzenie aplikacji)** podaj szczegóły nowej aplikacji, a następnie wybierz pozycję **Create your Twitter application (Utwórz aplikację usługi Twitter)**.

   ![Szczegóły aplikacji na Twitterze](./media/stream-analytics-twitter-sentiment-analysis-trends/provide-twitter-app-details-create.png "Szczegóły aplikacji na Twitterze")

3. Na stronie aplikacji wybierz kartę **Klucze i tokeny** oraz skopiuj wartości **klucza tajnego interfejsu API konsumenta** i **klucza tajnego interfejsu API konsumenta**. Ponadto wybierz pozycję **Utwórz** w obszarze **Token dostępu i Klucz tajny tokenu dostępu,** aby wygenerować tokeny dostępu. Skopiuj wartości pól **Access Token (Token dostępu)** i **Access Token Secret (Klucz tajny tokenu dostępu)**.

   Zapisz wartości dotyczące aplikacji usługi Twitter. Wartości są potrzebne później.

> [!NOTE]
> Klucze i tajemnice aplikacji Twitter zapewniają dostęp do konta Twitter. Potraktuj te informacje jako poufne, tak samo jak hasło do Twittera. Na przykład nie osadzaj tych informacji w aplikacji, którą przekazujesz innym osobom. 

### <a name="configure-the-client-application"></a>Konfigurowanie aplikacji klienckiej

Utworzyliśmy aplikację kliencką, która łączy się z danymi Twittera przy użyciu [interfejsów API przesyłania strumieniowego twittera](https://dev.twitter.com/streaming/overview) do zbierania zdarzeń tweetów dotyczących określonego zestawu tematów.

Przed uruchomieniu aplikacji wymaga pewnych informacji od Ciebie, takich jak klucze Twitter i parametry połączenia centrum zdarzeń.

1. Upewnij się, że pobrałeś aplikację [TwitterClientCore,](https://github.com/Azure/azure-stream-analytics/tree/master/DataGenerators/TwitterClientCore) zgodnie z wymaganiami wstępnymi.

2. Użyj edytora tekstu, aby otworzyć plik *App.config.* Wykonuj `<appSettings>` następujące zmiany w elemencie:

   * Ustaw `oauth_consumer_key` klucz konsumenta Twittera (API). 
   * Ustaw `oauth_consumer_secret` klucz tajny konsumenta twittera (klucz tajny interfejsu API).
   * Ustaw `oauth_token` token dostępu twitterowego.
   * Ustaw `oauth_token_secret` klucz tajny tokenu dostępu twitterowego.
   * Ustaw `EventHubNameConnectionString` ciąg połączenia.
   * Ustaw `EventHubName` nazwę centrum zdarzeń (czyli wartość ścieżki encji).

3. Otwórz wiersz polecenia i przejdź do katalogu, w którym znajduje się aplikacja TwitterClientCore. Użyj polecenia, `dotnet build` aby utworzyć projekt. Następnie użyj `dotnet run` polecenia, aby uruchomić aplikację. Aplikacja wysyła tweety do Centrum zdarzeń.

## <a name="create-a-stream-analytics-job"></a>Tworzenie zadania usługi Stream Analytics

Teraz, gdy zdarzenia tweetów są przesyłane strumieniowo w czasie rzeczywistym z Twittera, możesz skonfigurować zadanie usługi Stream Analytics do analizowania tych zdarzeń w czasie rzeczywistym.

1. W witrynie Azure portal przejdź do grupy zasobów i wybierz pozycję **+ Dodaj**. Następnie wyszukaj **zadanie usługi Stream Analytics** i wybierz pozycję **Utwórz**.

2. Nazwij `socialtwitter-sa-job` zadanie i określ subskrypcję, grupę zasobów i lokalizację.

    Warto umieścić zadanie i centrum zdarzeń w tym samym regionie, aby uzyskać najlepszą wydajność i nie płacić za przesyłanie danych między regionami.

3. Wybierz **pozycję Utwórz**. Następnie przejdź do zadania po zakończeniu wdrażania.

## <a name="specify-the-job-input"></a>Określanie danych wejściowych zadania

1. W zadaniu usługi Stream Analytics wybierz **pozycję Wejścia** z lewego menu w obszarze **Topologia zadań**.

2. Wybierz pozycję > **Event Hub** ** + &nbsp;Dodaj centrum zdarzeń wprowadzania strumienia**. Wypełnij formularz **Nowe dane wejściowe** następującymi informacjami:

   |**Ustawienie**  |**Sugerowana wartość**  |**Opis**  |
   |---------|---------|---------|
   |Alias danych wejściowych| *TwitterStream (Strumień Twittera)* | Wprowadź alias danych wejściowych. |
   |Subskrypcja  | \<Twoja subskrypcja\> |  Wybierz subskrypcję platformy Azure, której chcesz użyć. |
   |Przestrzeń nazw centrum zdarzeń | *asa-twitter-eventhub* |
   |Nazwa centrum zdarzeń | *socialtwitter-eh* | Wybierz *pozycję Użyj istniejącego*. Następnie wybierz utworzone Centrum zdarzeń.|
   |Typ kompresji zdarzeń| Gzip | Typ kompresji danych.|

   Pozostaw pozostałe wartości domyślne i wybierz pozycję **Zapisz**.

## <a name="specify-the-job-query"></a>Określanie kwerendy zadania

Usługa Stream Analytics obsługuje prosty, deklaratywny model zapytań, który opisuje przekształcenia. Aby dowiedzieć się więcej o języku, zobacz [odwołanie do języka kwerendy usługi Azure Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference). Ten poradnik pomaga tworzyć i testować kilka zapytań za pośrednictwem danych Twittera.

Aby porównać liczbę wzmianek między tematami, możesz użyć [okna Tumbling,](https://docs.microsoft.com/stream-analytics-query/tumbling-window-azure-stream-analytics) aby uzyskać liczbę wzmianek według tematu co pięć sekund.

1. W **omówieniu**zadania wybierz pozycję **Edytuj kwerendę** w prawym górnym rogu pola Kwerenda. Platforma Azure wyświetla listę danych wejściowych i wyjściowych, które są skonfigurowane dla zadania i umożliwia utworzenie kwerendy w celu przekształcenia strumienia wejściowego, gdy jest wysyłany do danych wyjściowych.

2. Zmień kwerendę w edytorze kwerend na następującą:

   ```sql
   SELECT *
   FROM TwitterStream
   ```

3. Dane zdarzeń z komunikatów powinny pojawić się w oknie **podglądu danych wejściowych** poniżej zapytania. Upewnij się, że **widok** jest ustawiony na **JSON**. Jeśli nie widzisz żadnych danych, upewnij się, że generator danych wysyła zdarzenia do centrum zdarzeń i że wybrano **GZip** jako typ kompresji dla danych wejściowych.

4. Wybierz **opcję Testuj kwerendę** i zwróć uwagę na wyniki w oknie **Wyniki testu** poniżej zapytania.

5. Zmień kwerendę w edytorze kodu na następującą i wybierz **opcję Testuj kwerendę:**

   ```sql
   SELECT System.Timestamp as Time, text
   FROM TwitterStream
   WHERE text LIKE '%Azure%'
   ```

6. Ta kwerenda zwraca wszystkie tweety, które zawierają słowo kluczowe *Azure*.

## <a name="create-an-output-sink"></a>Tworzenie ujścia wyjściowego

Teraz zdefiniowano strumień zdarzeń, dane wejściowe centrum zdarzeń do pozyskiwania zdarzeń i kwerendę do przeprowadzenia transformacji za pomocą strumienia. Ostatnim krokiem jest zdefiniowanie ujścia wyjściowego dla zadania.  

W tym przewodniku inicjowania zapisu zagregowane zdarzenia tweet z kwerendy zadania do magazynu obiektów Blob platformy Azure.  Wyniki można również wypchnąć do usługi Azure SQL Database, usługi Azure Table Storage, Event Hubs lub Power BI, w zależności od potrzeb aplikacji.

## <a name="specify-the-job-output"></a>Określanie danych wyjściowych zadania

1. W sekcji **Topologia zadań** w menu nawigacji po lewej stronie wybierz pozycję **Wyjścia**. 

2. Na stronie **Dane wyjściowe** kliknij pozycję ** + &nbsp;Dodaj** i **magazyn obiektów blob/magazyn danych Gen2:**

   * **Alias wyjściowy**: `TwitterStream-Output`Użyj nazwy . 
   * **Opcje importowania**: Wybierz **pozycję Wybierz miejsce do magazynowania z subskrypcji**.
   * **Konto magazynu**. Wybierz swoje konto magazynu.
   * **Kontener**. Wybierz **pozycję Utwórz nowy** i wprowadź `socialtwitter`.
   
4. Wybierz **pozycję Zapisz**.   

## <a name="start-the-job"></a>Uruchamianie zadania

Określono dane wejściowe, kwerendę i dane wyjściowe zadania. Możesz rozpocząć zadanie usługi Stream Analytics.

1. Upewnij się, że aplikacja TwitterClientCore jest uruchomiona. 

2. W omówieniu zadania wybierz pozycję **Start**.

3. Na stronie **Rozpocznij pracę** dla **czasu rozpoczęcia zadania wyjściowego**wybierz pozycję **Teraz,** a następnie wybierz pozycję **Start**.

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej
Aby uzyskać dalszą pomoc, wypróbuj nasze [forum usługi Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Następne kroki
* [Wprowadzenie do usługi Azure Stream Analytics](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics (Rozpoczynanie pracy z usługą Azure Stream Analytics)](stream-analytics-real-time-fraud-detection.md)
* [Scale Azure Stream Analytics jobs (Skalowanie zadań usługi Azure Stream Analytics)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Query Language Reference (Dokumentacja dotycząca języka zapytań usługi Azure Stream Analytics)](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure Stream Analytics Management REST API Reference (Dokumentacja interfejsu API REST zarządzania usługą Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn835031.aspx)
