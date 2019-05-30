---
title: Omówienie usługi Azure Stream Analytics
description: Poznaj usługę Stream Analytics — zarządzaną usługę, która pomaga analizować dane przesyłane strumieniowo z Internetu rzeczy (IoT) w czasie rzeczywistym.
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: overview
ms.custom: mvc
ms.date: 05/16/2019
ms.openlocfilehash: f607c1e79262efdbec79677fa7f16863b7a9d160
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/30/2019
ms.locfileid: "66393984"
---
# <a name="what-is-azure-stream-analytics"></a>Co to jest usługa Azure Stream Analytics?

Usługa Azure Stream Analytics to aparat przetwarzania zdarzeń, który jest przeznaczony do badać duże ilości danych przesyłanych strumieniowo. Wzorce i relacje można zidentyfikować informacje pochodzące z wielu źródeł danych wejściowych, włącznie z urządzeń, czujników, witryn internetowych, kanałów informacyjnych w mediach społecznościowych i aplikacji. Te wzorce można wyzwalać inne działania podrzędne, takie jak tworzenie alertów, wprowadzając informacje do narzędzia do raportowania lub przechowywania przekształconych danych do późniejszego użycia.

Gdy używasz usługi Azure Stream Analytics, przykłady są następujące scenariusze:

* — Łączenie czujników Internetu rzeczy (IoT) i analizy w czasie rzeczywistym na danych telemetrycznych z urządzenia
* Analiza strumienia kliknięć w internecie oraz analiza dzienników internetowych
* Geoprzestrzenna analiza na potrzeby zarządzania flotą i korzystania z pojazdów nie wymagających kierowcy
* Zdalne monitorowanie i konserwacja predykcyjna wartościowych zasobów
* Analiza danych punktu sprzedaży w czasie rzeczywistym na potrzeby kontroli zapasów i wykrywania anomalii

## <a name="how-does-stream-analytics-work"></a>Jak działa usługa Stream Analytics?

Zadania usługi Azure Stream Analytics obejmuje dane wejściowe, zapytanie przekształcenia oraz dane wyjściowe. Gdy zdarzenia, od oprogramowania lub urządzeń są przetwarzanych przez usługi Azure Event Hubs, Azure IoT Hub lub Azure Blob Storage, można określić co najmniej jedną z tych usług jako źródło danych wejściowych dla zadania. Zapytanie przekształcenia, które jest oparte na język zapytań SQL, służy do łatwego filtrowania, sortowania, agregowania i łączenia danych przesyłanych strumieniowo w określonym czasie. Można dostosować kolejności opcje i czas trwania okien czasu, gdy preforming operacje agregacji zdarzeń.

Każde zadanie ma znajdą się przekształcone dane, i możesz kontrolować, co się dzieje w odpowiedzi na informacje, które zostały przeanalizowane. Można na przykład:

* Wysyłanie danych do monitorowania kolejki w celu wyzwolenia alertów lub podrzędnych niestandardowych przepływów pracy.
* Wysyłanie danych do pulpitu nawigacyjnego usługi Power BI dla wizualizacji w czasie rzeczywistym.
* Store danych w innych usługach magazynu platformy Azure, szkolenia usługi machine learning model na podstawie danych historycznych lub wykonywać analizy wsadowej.

Na poniższej ilustracji przedstawiono, jak dane są wysyłane do usługi Stream Analytics, analizowane i wysyłane do innych akcji, takich jak magazyn lub prezentacji:

![Wprowadzenie do potoku usługi Stream Analytics](./media/stream-analytics-introduction/stream-analytics-intro-pipeline.png)

## <a name="key-capabilities-and-benefits"></a>Najważniejsze funkcje i korzyści

Usługa Stream Analytics została zaprojektowana tak, aby była łatwa w użyciu, elastyczna, niezawodna i skalowalna do dowolnej wielkości zadania. Jest ona dostępna w wielu regionach platformy Azure. Poniższy rysunek ilustruje kluczowe możliwości usługi Azure Stream Analytics:

![Kluczowe możliwości usługi Stream Analytics](./media/stream-analytics-introduction/stream-analytics-key-capabilities.png)

## <a name="ease-of-getting-started"></a>Łatwość rozpoczynania pracy

Usługa Azure Stream Analytics jest łatwe do uruchomienia. Trwa tylko kilka kliknięć, aby połączyć się z różnymi źródłami i ujściami, tworzenia potoku end-to-end. Stream Analytics mogą łączyć się z [usługi Azure Event Hubs](/azure/event-hubs/) i [usługi Azure IoT Hub](/azure/iot-hub/) do pozyskania danych przesyłanych strumieniowo także [usługi Azure Blob storage](/azure/storage/storage-introduction) do pozyskiwania danych historycznych. Dane wejściowe zadania mogą obejmować odwołanie statyczne lub zmieniające dane z usługi Azure Blob storage lub [bazy danych SQL](stream-analytics-use-reference-data.md#azure-sql-database) , możesz dołączyć do przesyłania strumieniowego danych w celu wykonania operacji wyszukiwania.

Stream Analytics pozwala przekierować dane wyjściowe zadania do wielu systemów magazynowych takich jak [usługi Azure Blob storage](/azure/storage/storage-introduction), [usługi Azure SQL Database](/azure/sql-database/), [usługi Azure Data Lake Store](/azure/data-lake-store/), i [platformy Azure Usługa cosmos DB](/azure/cosmos-db/introduction). Analiza danych usługi batch można uruchomić na przechowywanych danych wyjściowych za pomocą usługi Azure HDInsight lub może wysłać dane wyjściowe do innej usługi, takie jak usługa Event Hubs do użycia lub [usługi Power BI](https://docs.microsoft.com/power-bi/) dla wizualizacji w czasie rzeczywistym.

Aby uzyskać całą listę danych wyjściowych usługi Stream Analytics, zobacz [zrozumieć dane wyjściowe z usługi Azure Stream Analytics](stream-analytics-define-outputs.md).

## <a name="programmer-productivity"></a>Produktywność programisty

Usługa Azure Stream Analytics używa języka prostych zapytań SQL, który został uzupełniony o zaawansowane ograniczenia czasowe pozwalające do analizowania danych w ruchu. Aby zdefiniować przekształcenia zadań, należy użyć prostego, deklaratywnego [języka zapytań usługi Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference), który umożliwia tworzenie złożonych zapytań czasowych i analizy przy użyciu prostych konstrukcji języka SQL. Ponieważ język zapytań usługi Stream Analytics jest spójny z językiem SQL, znajomość SQL jest wystarczające, aby rozpocząć tworzenie zadań. Zadania można również tworzyć za pomocą narzędzi deweloperskich, takich jak Azure PowerShell, [narzędzia Stream Analytics programu Visual Studio](stream-analytics-tools-for-visual-studio-install.md), [rozszerzenia Stream Analytics Visual Studio Code](quick-create-vs-code.md), lub szablonów usługi Azure Resource Manager . Narzędzia dla deweloperów pozwalają tworzyć zapytania przekształceń w trybie offline i używać [potoków ciągłej integracji/ciągłego wdrażania](stream-analytics-tools-for-visual-studio-cicd.md) w celu przesłania zadań na platformę Azure.

Język zapytań usługi Stream Analytics oferuje szeroką gamę funkcji do analizowania i przetwarzanie danych przesyłanych strumieniowo. Ten język zapytań obsługuje manipulowanie danymi prosty, funkcje agregacji i funkcje geoprzestrzenne złożone. Można edytować zapytania w portalu oraz testować przy użyciu przykładowych danych, które zostały wyodrębnione ze strumienia na żywo.

Możliwości języka zapytań można rozszerzyć, definiując i wywołując dodatkowe funkcje. Można zdefiniować wywołania funkcji w usłudze Azure Machine Learning, aby móc korzystać z rozwiązania Azure Machine Learning i integracja języka JavaScript lub C# funkcje zdefiniowane przez użytkownika (UDF) lub agregatów zdefiniowanych przez użytkownika, aby wykonywać złożone obliczenia w ramach Stream Analytics query.

## <a name="fully-managed"></a>Pełne zarządzanie

Usługa Azure Stream Analytics to bezserwerowe w pełni zarządzane rozwiązanie typu „platforma jako usługa” (PaaS) platformy Azure. Nie trzeba aprowizować żadnego sprzętu ani zarządzać klastrami w celu uruchamiania zadań. Usługa Azure Stream Analytics w pełni zarządza każdym zadaniu konfigurowanie złożonych klastrów obliczeniowych w chmurze i zwracając szczególną uwagę wydajności dostrajania niezbędne do uruchomienia zadania. Integracja z usługą Azure Event Hubs i Azure IoT Hub umożliwia obsługę milionów zdarzeń na sekundę przychodzących z różnych źródeł, aby uwzględnić połączonych urządzeń, strumieni kliknięć, plików dziennika i zadania. Za pomocą funkcji partycjonowania centrów zdarzeń, możesz obliczenia można podzielić na etapy logiczne, z których każdy może zostać dalej podzielony w celu zwiększenia skalowalności.

## <a name="run-in-the-cloud-on-in-the-intelligent-edge"></a>Działa w chmurze w ramach w inteligentnych węzłach brzegowych

Usługa Azure Stream Analytics można uruchomić w chmurze w celu analizy na dużą skalę, lub uruchomić na brzegowych urządzeniach IoT niezwykle małych opóźnień analizy. Usługa Azure Stream Analytics używa tego samego języka zapytań w chmurze i urządzenia brzegowe, dzięki czemu deweloperzy mogą kompilować prawdziwie hybrydowej architektury przetwarzania strumienia.

## <a name="low-total-cost-of-ownership"></a>Niski całkowity koszt posiadania

Jako usługa w chmurze usługa Stream Analytics jest zoptymalizowana pod kątem niskiego kosztu. Żadnych kosztów ponoszonych z góry są zaangażowani — płacisz tylko za [zużyte jednostki przesyłania strumieniowego](stream-analytics-streaming-unit-consumption.md)oraz ilość przetworzonych danych. Brak zobowiązań i aprowizacji klastra, wymagane, i możesz można skalować w górę lub w dół na podstawie własnych potrzeb biznesowych.

## <a name="mission-critical-ready"></a>Gotowość na sytuacje krytyczne dla działalności firmy

Usługa Azure Stream Analytics jest dostępna w wielu regionach na całym świecie i jest przeznaczona do uruchamiania obciążeń o kluczowym znaczeniu dzięki obsłudze wymagań dotyczących niezawodności, bezpieczeństwa i zgodności.

### <a name="reliability"></a>Niezawodność

Poza tym usługa Azure Stream Analytics gwarantuje przeprowadzenie dokładnie jednej aprowizacji zdarzenia oraz co najmniej jednokrotnego dostarczenia zdarzeń, więc nigdy nie dochodzi do utraty zdarzeń. Dokładnie — po zakończeniu przetwarzania jest gwarantowana przy użyciu wybranych danych wyjściowych, zgodnie z opisem w [gwarancją dostarczania zdarzeń](/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics).

Usługa Azure Stream Analytics ma wbudowane funkcje odzyskiwania na wypadek awarii podczas dostarczania zdarzeń. Stream Analytics również zapewnia wbudowane punkty kontrolne, aby utrzymywać stan zadania i zapewnia powtarzalne wyniki.

Usługa Stream Analytics, jako usługa zarządzana, gwarantuje przetwarzanie zdarzeń z dostępnością 99,9% na poziomie minut. Aby uzyskać więcej informacji, zobacz [Stream Analytics — umowa SLA](https://azure.microsoft.com/support/legal/sla/stream-analytics/v1_0/) strony. 

### <a name="security"></a>Bezpieczeństwo

Z punktu widzenia zabezpieczeń usługa Azure Stream Analytics szyfruje całą komunikację przychodzącą i wychodzącą oraz obsługuje protokół TLS 1.2. Wbudowane punkty kontrolne również są szyfrowane. Usługa Stream Analytics nie przechowuje danych przychodzących, ponieważ całe przetwarzanie odbywa się w pamięci.

### <a name="compliance"></a>Zgodność

Usługa Azure Stream Analytics zapewnia dostosowanie do wielu certyfikatów zgodności, co zostało opisane w [przeglądzie zgodności platformy Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942). 

## <a name="performance"></a>Wydajność

Usługa Stream Analytics może przetwarzać miliony zdarzeń co sekundę oraz dostarczać wyniki z niewielkim opóźnieniem. Umożliwia skalowanie w górę lub zwiększanie skali w poziomie w celu obsługi dużych aplikacji z przetwarzaniem złożonych zdarzeń oraz przetwarzaniem w czasie rzeczywistym. Stream Analytics obsługuje wydajność za pomocą partycjonowania, dzięki czemu złożonych zapytań zrównoleglona i wykonywane na wielu węzłach przesyłania strumieniowego. Usługa Azure Stream Analytics jest oparta na [Trill](https://github.com/Microsoft/Trill), aparacie analizy przesyłania strumieniowego w pamięci o wysokiej wydajności opracowanym we współpracy z Microsoft Research.

## <a name="next-steps"></a>Kolejne kroki

Masz już podstawowe informacje o usłudze Azure Stream Analytics. Teraz możesz utworzyć pierwsze zadanie w usłudze Stream Analytics:

* [Tworzenie zadania usługi Stream Analytics przy użyciu witryny Azure Portal](stream-analytics-quick-create-portal.md).
* [Tworzenie zadania usługi Stream Analytics przy użyciu programu Azure PowerShell](stream-analytics-quick-create-powershell.md).
* [Tworzenie zadania usługi Stream Analytics przy użyciu programu Visual Studio](stream-analytics-quick-create-vs.md).
* [Tworzenie zadania usługi Stream Analytics przy użyciu programu Visual Studio Code](quick-create-vs-code.md).
