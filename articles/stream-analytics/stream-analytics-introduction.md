---
title: Omówienie usługi Azure Stream Analytics
description: Poznaj usługę Stream Analytics — zarządzaną usługę, która pomaga analizować dane przesyłane strumieniowo z Internetu rzeczy (IoT) w czasie rzeczywistym.
author: jseb225
ms.author: jeanb
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: overview
ms.custom: seodec18
ms.date: 12/07/2018
ms.openlocfilehash: 09f402f81700b53eb9e4a95e36545ef02850660a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61479715"
---
# <a name="what-is-azure-stream-analytics"></a>Co to jest usługa Azure Stream Analytics?

Usługa Azure Stream Analytics to aparat przetwarzania zdarzeń, który umożliwia analizowanie dużej ilości danych przesyłanych strumieniowo z urządzeń. Dane przychodzące mogą pochodzić między innymi z urządzeń, czujników, witryn internetowych, kanałów informacyjnych mediów społecznościowych, aplikacji itp. Poza tym usługa ta obsługuje również wyodrębnianie informacji ze strumieni danych, identyfikowanych wzorców oraz relacji. Dzięki temu korzystanie ze wzorców umożliwia wyzwalanie innych działań podrzędnych, takich jak tworzenie alertów, przekazywanie informacji do narzędzia do raportowania lub przechowywanie ich w razie potrzeby do późniejszego użycia.

Poniżej przedstawiono przykłady użycia usługi Azure Stream Analytics: 

* Łączenie czujników Internetu rzeczy (IoT, Internet of Things) i analiza w czasie rzeczywistym danych telemetrycznych urządzenia
* Analiza strumienia kliknięć w internecie oraz analiza dzienników internetowych
* Geoprzestrzenna analiza na potrzeby zarządzania flotą i korzystania z pojazdów nie wymagających kierowcy
* Zdalne monitorowanie i konserwacja predykcyjna wartościowych zasobów
* Analiza danych punktu sprzedaży w czasie rzeczywistym na potrzeby kontroli zapasów i wykrywania anomalii

## <a name="how-does-stream-analytics-work"></a>Jak działa usługa Stream Analytics?

W początkowej fazie usługa Azure Stream Analytics wymaga źródła danych przesyłania, które zostało pozyskane z Centrum zdarzeń Azure, usługi Azure IoT Hub lub z magazynu danych, takiego jak usługa Azure Blob Storage. Aby przeanalizować strumienie, należy utworzyć zadanie usługi Stream Analytics, które określa źródło danych wejściowych przesyłające dane strumieniowo. Zadanie określa również zapytanie przekształcenia, które określa, w jaki sposób wyszukiwać dane, wzorce lub relacje. Zapytanie przekształcenia wykorzystuje język zapytań SQL, który służy do łatwego filtrowania, sortowania, agregowania i łączenia danych przesyłanych strumieniowo w danym okresie czasu. Podczas wykonywania zadania możliwe jest dostosowanie opcji przetwarzania zdarzeń, a także czasu wyświetlania okien czasu w trakcie wykonywania operacji agregacji.

Po przeanalizowaniu danych przychodzących należy określić przekształcone dane wyjściowe. Można też podjąć decyzję w kwestii kolejnych czynności będących odpowiedzią na przeanalizowane informacje. Można na przykład wykonać takie działania, jak:

* Wysyłanie danych do monitorowania kolejki w celu wyzwolenia alertów lub podrzędnych niestandardowych przepływów pracy.
* Wysyłanie danych do pulpitu nawigacyjnego usługi Power BI na potrzeby wizualizacji w czasie rzeczywistym.
* Przechowywanie danych w innych usługach magazynu platformy Azure w celu umożliwienia szkolenia modelu uczenia maszynowego w oparciu o dane historyczne lub wykonywania analizy wsadowej.

Poniższy rysunek ilustruje potok usługi Stream Analytics. W przypadku danych wejściowych i wyjściowych dane zadanie usługi Stream Analytics może wykorzystać wszystkie lub wybrane zestawy potoków. Ten rysunek pokazuje, w jaki sposób dane są przesyłane do usługi Stream Analytics, analizowane i wysyłane w celu wykonania innych działań, takich jak przechowywanie lub prezentacja:

![Wprowadzenie do potoku usługi Stream Analytics](./media/stream-analytics-introduction/stream-analytics-intro-pipeline.png)

## <a name="key-capabilities-and-benefits"></a>Najważniejsze funkcje i korzyści

Usługa Stream Analytics została zaprojektowana tak, aby była łatwa w użyciu, elastyczna, niezawodna i skalowalna do dowolnej wielkości zadania. Jest ona dostępna w wielu regionach platformy Azure. Poniższy rysunek ilustruje kluczowe możliwości usługi Azure Stream Analytics:

![Kluczowe możliwości usługi Stream Analytics](./media/stream-analytics-introduction/stream-analytics-key-capabilities.png)

## <a name="ease-of-getting-started"></a>Łatwość rozpoczynania pracy

Rozpoczęcie pracy z usługą Azure Stream Analytics jest łatwe. Wystarczy kilka kliknięć, aby połączyć się z wieloma źródłami i ujściami danych oraz utworzyć potok końcowy. Usługa Stream Analytics może łączyć się z usługą [Centra zdarzeń Azure](/azure/event-hubs/) oraz usługą [Azure IoT Hub](/azure/iot-hub/) w celu pozyskania danych przesyłanych strumieniowo. Poza tym może łączyć się z usługą [Azure Blob Storage](/azure/storage/storage-introduction) w celu pozyskania danych historycznych. Usługa ta ma też możliwość łączenia danych z centrów zdarzeń z innymi źródłami danych i aparatami przetwarzania. Dane wejściowe zadań mogą też zawierać statyczne dane referencyjne lub dane, które zmieniają się powoli, co więcej dane przesyłane strumieniowo można łączyć z tymi danymi referencyjnymi w celu wykonania operacji wyszukiwania.

Usługa Stream Analytics pozwala przekierować dane wyjściowe zadań do wielu systemów magazynowych, takich jak [Azure Blob](/azure/storage/storage-introduction), [Azure SQL Database](/azure/sql-database/), [Azure Data Lake Stores](/azure/data-lake-store/) lub [Azure Cosmos DB](/azure/cosmos-db/introduction). Po zapisaniu można uruchomić analizę danych wsadowych w usłudze Azure HDInsight lub wysłać dane wyjściowe do innej usługi, takiej jak centra zdarzeń, w celu ich zużycia, lub do usługi [Power BI](https://docs.microsoft.com/power-bi/) w celu przeprowadzenia wizualizacji w czasie rzeczywistym za pomocą interfejsu API przesyłania strumieniowego w usłudze Power BI.

## <a name="programmer-productivity"></a>Wydajność programistów

Usługa Azure Stream Analytics używa prostego język zapytań SQL, który został uzupełniony o zaawansowane ograniczenia czasowe pozwalające na analizowanie danych w ruchu. Aby zdefiniować przekształcenia zadań, należy użyć prostego, deklaratywnego [języka zapytań usługi Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx), który umożliwia tworzenie złożonych zapytań czasowych i analizy przy użyciu prostych konstrukcji języka SQL. Język zapytań usługi Stream Analytics jest spójny z językiem SQL, którego znajomość jest wystarczająca, aby rozpocząć tworzenie zadań. Zadania można również tworzyć za pomocą narzędzi dla deweloperów, takich jak program Azure PowerShell, [narzędzia Stream Analytics dla programu Visual Studio](stream-analytics-tools-for-visual-studio-install.md) lub szablony usługi Azure Resource Manager. Narzędzia dla deweloperów pozwalają tworzyć zapytania przekształceń w trybie offline i używać [potoków ciągłej integracji/ciągłego wdrażania](stream-analytics-tools-for-visual-studio-cicd.md) w celu przesłania zadań na platformę Azure. 

Język zapytań usługi Stream Analytics oferuje szeroką gamę funkcji do analizowania i przetwarzania danych przesyłania strumieniowego. Ten język zapytań obsługuje proste operacje wykonywane na danych, funkcje agregacji oraz złożone funkcje geoprzestrzenne. Zapytania można edytować w portalu oraz testować przy użyciu przykładowych danych, które zostały wyodrębnione ze strumienia na żywo.

Możliwości języka zapytań można rozszerzyć, definiując i wywołując dodatkowe funkcje. Wywołania funkcji można zdefiniować w usłudze Azure Machine Learning. Zastosowanie zawartych w niej rozwiązań oraz zintegrowanie napisanych w języku JavaScript funkcji zdefiniowanych przez użytkownika (UDF) lub agregatów zdefiniowanych przez użytkownika (UDA) pozwala wykonywać złożone obliczenia w ramach zapytania usługi Stream Analytics.

## <a name="fully-managed"></a>Pełne zarządzanie 

Usługa Azure Stream Analytics to bezserwerowe w pełni zarządzane rozwiązanie typu „platforma jako usługa” (PaaS) platformy Azure. Oznacza to, że nie trzeba aprowizować żadnego sprzętu ani zarządzać klastrami w celu uruchomienia zadań. Usługa Azure Stream Analytics w pełni zarządza każdym zadaniem, dbając o konfigurowanie złożonych klastrów obliczeniowych w chmurze i dostrajanie wydajności niezbędnej do uruchomienia zadania. Integracja z usługami Centra zdarzeń Azure i Azure IoT Hub pozwala zadaniom pozyskiwać w ciągu sekundy miliony zdarzeń przychodzących z połączonych urządzeń, strumieni kliknięć, plików dziennika i innych źródeł. Za pomocą funkcji partycjonowania centrów zdarzeń obliczenia można podzielić na etapy logiczne, z których każdy może zostać dalej podzielony w celu zwiększenia skalowalności.

## <a name="run-in-the-cloud-on-in-the-intelligent-edge"></a>Działa w chmurze w ramach w inteligentnych węzłach brzegowych

Usługa Azure Stream Analytics można uruchomić w chmurze w celu analizy na dużą skalę, lub uruchomić na inteligentnych urządzeniach brzegowych analizy bardzo niskimi opóźnieniami.
Usługa Azure Stream Analytics używa tego samego języka zapytań zarówno w chmurze, jak i inteligentnych rozwiązań brzegowych, dzięki czemu deweloperzy mogą kompilować prawdziwie hybrydowej architektury przetwarzania strumienia.

## <a name="low-total-cost-of-ownership"></a>Niski całkowity koszt posiadania

Jako usługa w chmurze usługa Stream Analytics jest zoptymalizowana pod kątem niskiego kosztu. Brak jakichkolwiek kosztów początkowych — opłaty pobierane są wyłącznie za [zużyte jednostki przesyłania strumieniowego](stream-analytics-streaming-unit-consumption.md) i ilość przetworzonych danych. Nie jest wymagane angażowanie ani aprowizowanie klastra. Zadania przesyłania strumieniowego można skalować w górę lub w dół w zależności od potrzeb biznesowych. 

## <a name="mission-critical-ready"></a>Gotowość na sytuacje krytyczne dla działalności firmy
Usługa Azure Stream Analytics jest dostępna w wielu regionach na całym świecie i jest przeznaczona do uruchamiania obciążeń o kluczowym znaczeniu dzięki obsłudze wymagań dotyczących niezawodności, bezpieczeństwa i zgodności.
### <a name="reliability"></a>Niezawodność
Poza tym usługa Azure Stream Analytics gwarantuje przeprowadzenie dokładnie jednej aprowizacji zdarzenia oraz co najmniej jednokrotnego dostarczenia zdarzeń, więc nigdy nie dochodzi do utraty zdarzeń. Dokładnie — po zakończeniu przetwarzania jest gwarantowana przy użyciu wybranych danych wyjściowych, zgodnie z opisem w [gwarancją dostarczania zdarzeń](/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics).
Usługa Azure Stream Analytics ma wbudowane funkcje odzyskiwania na wypadek awarii podczas dostarczania zdarzeń. Ponadto usługa Stream Analytics zapewnia wbudowane punkty kontrolne umożliwiające utrzymywanie stanu zadania i zapewniające powtarzalne wyniki.

Usługa Stream Analytics, jako usługa zarządzana, gwarantuje przetwarzanie zdarzeń z dostępnością 99,9% na poziomie minut. Aby uzyskać więcej informacji, zobacz stronę [Stream Analytics — umowa SLA](https://azure.microsoft.com/support/legal/sla/stream-analytics/v1_0/). 

### <a name="security"></a>Bezpieczeństwo
Z punktu widzenia zabezpieczeń usługa Azure Stream Analytics szyfruje całą komunikację przychodzącą i wychodzącą oraz obsługuje protokół TLS 1.2. Wbudowane punkty kontrolne również są szyfrowane. Usługa Stream Analytics nie przechowuje danych przychodzących, ponieważ całe przetwarzanie odbywa się w pamięci. 

### <a name="compliance"></a>Zgodność
Usługa Azure Stream Analytics zapewnia dostosowanie do wielu certyfikatów zgodności, co zostało opisane w [przeglądzie zgodności platformy Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942). 

## <a name="performance"></a>Wydajność

Usługa Stream Analytics może przetwarzać miliony zdarzeń co sekundę oraz dostarczać wyniki z niewielkim opóźnieniem.
Umożliwia skalowanie w górę lub zwiększanie skali w poziomie w celu obsługi dużych aplikacji z przetwarzaniem złożonych zdarzeń oraz przetwarzaniem w czasie rzeczywistym. Usługa Stream Analytics obsługuje wydajność za pomocą partycjonowania, dzięki czemu złożone zapytania mogą być równolegle wykonywane na wielu węzłach przesyłania strumieniowego.
Usługa Azure Stream Analytics jest oparta na [Trill](https://github.com/Microsoft/Trill), aparacie analizy przesyłania strumieniowego w pamięci o wysokiej wydajności opracowanym we współpracy z Microsoft Research. 

## <a name="next-steps"></a>Kolejne kroki

Masz już podstawowe informacje o usłudze Azure Stream Analytics. Teraz możesz utworzyć pierwsze zadanie w usłudze Stream Analytics:

* [Tworzenie zadania usługi Stream Analytics przy użyciu witryny Azure Portal](stream-analytics-quick-create-portal.md).
* [Tworzenie zadania usługi Stream Analytics przy użyciu programu Azure PowerShell](stream-analytics-quick-create-powershell.md).
* [Tworzenie zadania usługi Stream Analytics przy użyciu programu Visual Studio](stream-analytics-quick-create-vs.md).

