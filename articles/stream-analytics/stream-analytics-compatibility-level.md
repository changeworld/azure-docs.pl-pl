---
title: Zrozumienie poziom zgodności dla zadania usługi analiza strumienia Azure
description: Dowiedz się, jak ustawić poziom zgodności dla zadania usługi analiza strumienia Azure i najważniejszych zmian w najnowszej poziom zgodności
services: stream-analytics
author: jasonwhowell
ms.author: jasonh
manager: kfile
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/03/2018
ms.openlocfilehash: 32e73918b2dd98822d42d74002b705ff730145d9
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="compatibility-level-for-azure-stream-analytics-jobs"></a>Poziom zgodności dla zadania usługi analiza strumienia Azure
 
Poziom zgodności odwołuje się do zachowania specyficzne dla wersji usługi Azure Stream Analytics. Usługa Azure Stream Analytics jest zarządzane usługi, za pomocą funkcji regularne aktualizacje i wydajności. Zazwyczaj aktualizacje są automatycznie udostępniane użytkownikom końcowym. Jednak niektóre nowe funkcje mogą powodować znaczne zmiany takie jak zmiany zachowania istniejące zadanie, Zmień w procesach konsumowania danych z tych zadań itp. Poziom zgodności jest używana do reprezentowania istotne zmiany wprowadzone w Stream Analytics. Istotne zmiany są zawsze wprowadzono nowy poziom zgodności. 

Poziom zgodności upewnia się, że istniejące zadania są uruchamiane bez jakiekolwiek niepowodzenie. Podczas tworzenia nowego zadania usługi Stream Analytics jest najlepszym rozwiązaniem, aby go utworzyć przy użyciu poziomu zgodności najnowszy dostępny. 
 
## <a name="set-a-compatibility-level"></a>Ustaw poziom zgodności 

Poziom zgodności steruje zachowaniem czasu wykonywania zadania usługi analiza strumienia. Można ustawić poziomu zgodności dla zadania Stream Analytics, za pomocą portalu lub przy użyciu [wywołanie interfejsu API REST zadania tworzenia](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-job). Usługa Azure Stream Analytics obecnie obsługuje dwa zgodności poziomy — "1.0" i "1.1". Domyślnie poziom zgodności jest ustawiony na "1.0", którą wprowadzono w ogólnej dostępności usługi Azure Stream Analytics. Aby zaktualizować wartości domyślne, przejdź do istniejącego zadania usługi analiza strumienia > Wybierz **poziom zgodności** opcji **Konfiguruj** sekcji i zmień wartość. 

Upewnij się, możesz zatrzymać zadanie przed zaktualizowaniem poziomu zgodności. Nie można zaktualizować poziomu zgodności, jeśli zadanie jest w stanie uruchomienia. 

![Poziom zgodności w portalu](media\stream-analytics-compatibility-level/image1.png)

 
Po zaktualizowaniu poziom zgodności kompilatora T-SQL sprawdza stan zadania o składnię, która odpowiada poziomowi zgodności wybranego. 

## <a name="major-changes-in-the-latest-compatibility-level-11"></a>Większych zmian w najnowszej poziom zgodności (1.1)

W poziom zgodności 1.1 wprowadzono następujące istotne zmiany:

* **Format XML magistrali usług**  

  * **poprzednie wersje:** Azure Stream Analytics używane DataContractSerializer, więc zawartość komunikatu uwzględnione tagów XML. Na przykład:
    
   @\u0006string\b3http://schemas.microsoft.com/2003/10/Serialization/\u0001 {"SensorId": "1", "temperatury": 64\}\u0001 

  * **Bieżąca wersja:** treść wiadomości zawiera strumienia bezpośrednio z nie dodatkowe tagi. Na przykład:
  
   {"SensorId": "1", "temperatury": 64} 
 
* **Utrwalanie uwzględnianie wielkości liter dla nazw pól**  

  * **poprzednie wersje:** nazwy pola zostały zmienione na małe litery, gdy przetwarzane przez aparat Azure Stream Analytics. 

  * **Bieżąca wersja:** uwzględnianie wielkości liter jest trwały dla nazw pól, gdy są przetwarzane przez aparat Azure Stream Analytics. 

  > [!NOTE] 
  > Utrwalanie uwzględnianie wielkości liter nie jest jeszcze dostępne dla analitycznego strumienia zadań hostowany przy użyciu środowiska krawędzi. W związku z tym wszystkie nazwy pól są konwertowane na małe litery, jeśli zadanie znajduje się na krawędzi. 

* **FloatNaNDeserializationDisabled**  

  * **poprzednie wersje:** polecenia CREATE TABLE nie filtrowanie zdarzeń z wartością typu NaN (Not a Number. Na przykład w nieskończoność, - Infinity) w kolumnie liczb ZMIENNOPRZECINKOWYCH typu, ponieważ znajdują się poza zakresem udokumentowane dla tych numerów.

  * **Bieżąca wersja:** CREATE TABLE pozwala określić silne schematu. Aparat Stream Analytics weryfikuje, że danych odpowiada tego schematu. W tym modelu polecenia można filtrować zdarzenia o wartości NaN. 

* **Wyłącz automatyczne rozszerzające dla ciągów daty i godziny w formacie JSON.**  

  * **poprzednie wersje:** automatycznie upcast ciągu, wpisz wartości daty/godziny/strefy informacje do daty/godziny, a następnie wykonać jego konwersję na czas UTC czy parser notacji JSON. To dało w wyniku utraty informacje dotyczące strefy czasowej.

  * **Bieżąca wersja:** nie istnieje już automatycznie upcast wartości ciągu daty/godziny/strefy informacje do typu Data/Godzina. W związku z tym jest przechowywane informacje dotyczące strefy czasowej. 

## <a name="next-steps"></a>Kolejne kroki
* [Podręcznik rozwiązywania problemów dotyczących usługi Azure Stream Analytics](stream-analytics-troubleshooting-guide.md)
* [Blok kondycji zasobów usługi analiza strumienia](stream-analytics-resource-health.md)