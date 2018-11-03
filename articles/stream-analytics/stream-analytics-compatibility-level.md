---
title: Zrozumieć poziom zgodności dla zadań usługi Azure Stream Analytics
description: Dowiedz się, jak ustawić poziom zgodności dla zadania usługi Azure Stream Analytics i istotne zmiany w najnowszej poziom zgodności
services: stream-analytics
author: jasonwhowell
ms.author: mamccrea
manager: kfile
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 10/15/2018
ms.openlocfilehash: 83bbb777f5af6d29736db3b53ca39c449402c78e
ms.sourcegitcommit: 1fc949dab883453ac960e02d882e613806fabe6f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/03/2018
ms.locfileid: "50977717"
---
# <a name="compatibility-level-for-azure-stream-analytics-jobs"></a>Poziom zgodności dla zadań usługi Azure Stream Analytics
 
Poziom zgodności odnosi się do zachowania specyficzne dla wersji usługi Azure Stream Analytics. Usługa Azure Stream Analytics to zarządzana usługa, za pomocą funkcji regularnych aktualizacji i ulepszenia wydajności. Zazwyczaj aktualizacje są automatycznie udostępniane użytkownikom końcowym. Jednak niektóre nowe funkcje mogą wprowadzać znaczące zmiany takich jak zmiany w zachowaniu istniejącego zadania, zmiany w procesach korzystanie z danych z tych zadań itd. Poziom zgodności jest używana do reprezentowania istotne zmiany wprowadzone w usłudze Stream Analytics. Zawsze wprowadzono znaczące zmiany dzięki nowemu poziomowi zgodności. 

Poziom zgodności gwarantuje, że istniejące zadania będą uruchamiane bez żadnych błędów. Podczas tworzenia nowego zadania usługi Stream Analytics jest najlepszym rozwiązaniem, aby je utworzyć za pomocą najnowszych poziom zgodności, które są dostępne dla Ciebie. 
 
## <a name="set-a-compatibility-level"></a>Ustaw poziom zgodności 

Poziom zgodności steruje zachowaniem czasu wykonywania zadania usługi stream analytics. Można ustawić poziom zgodności dla zadania usługi Stream Analytics, za pomocą portalu lub przy użyciu [wywołanie interfejsu API REST zadania tworzenia](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-job). Usługa Azure Stream Analytics obsługuje obecnie dwie zgodności poziomów — "1.0" i "1.1". Domyślnie poziom zgodności jest równa "1.0", który został wprowadzony w ogólnie dostępnej wersji usługi Azure Stream Analytics. Aby zaktualizować wartość domyślną, przejdź do istniejącego zadania usługi Stream Analytics > Wybierz **poziom zgodności** opcji **Konfiguruj** sekcji, a następnie zmień wartość. 

Upewnij się, Zatrzymaj zadanie przed zaktualizowaniem poziom zgodności. Nie można zaktualizować poziom zgodności, jeśli zadanie jest w stanie uruchomienia. 

![Poziom zgodności w portalu](media\stream-analytics-compatibility-level/image1.png)

 
Po zaktualizowaniu poziom zgodności kompilatora języka T-SQL sprawdza poprawność zadania za pomocą składni, który odpowiada poziomowi zgodności wybranego. 

## <a name="major-changes-in-the-latest-compatibility-level-11"></a>Istotne zmiany w najnowszej poziom zgodności (1.1)

Następujące istotne zmiany są wprowadzane przy poziomie zgodności 1.1:

* **Format XML magistrali usług**  

  * **poprzednie wersje:** usługi Azure Stream Analytics używane DataContractSerializer, dlatego zawartość komunikatu tagów XML. Na przykład:
    
   @\u0006string\b3http://schemas.microsoft.com/2003/10/Serialization/\u0001{ "SensorId": "1", "Temperatura": 64\}\u0001 

  * **Bieżąca wersja:** treść wiadomości zawiera strumienia bezpośrednio z nie dodatkowe tagi. Na przykład:
  
   {"SensorId": "1", "Temperatura": 64} 
 
* **Utrwalanie uwzględnianie wielkości liter dla nazw pól**  

  * **poprzednie wersje:** nazwy pola zostały zmienione na małe litery, gdy jest przetwarzane przez aparat usługi Azure Stream Analytics. 

  * **Bieżąca wersja:** uwzględnianie wielkości liter są utrwalane dotyczące nazw pól, gdy są przetwarzane przez aparat usługi Azure Stream Analytics. 

  > [!NOTE] 
  > Utrwalanie uwzględnianie wielkości liter nie jest jeszcze dostępna dla zadania Stream Analytics hostowany przy użyciu środowiska krawędzi. W rezultacie wszystkie nazwy pól są konwertowane na małe litery, gdy zadanie jest obsługiwana na urządzeniach brzegowych. 

* **FloatNaNDeserializationDisabled**  

  * **poprzednie wersje:** polecenia CREATE TABLE nie filtrowanie zdarzeń z NaN (Not a Number. Na przykład w nieskończoność, - nieskończoność) w kolumnie FLOAT typu, ponieważ są one poza zakresem udokumentowane w przypadku tych liczb.

  * **Bieżąca wersja:** CREATE TABLE pozwala określić silne schematu. Aparat usługi Stream Analytics weryfikuje, że danych odpowiada tym schemacie. W tym modelu polecenia można filtrować zdarzenia przy użyciu wartości NaN. 

* **Wyłącz automatyczne rozszerzające dla ciągów daty i godziny w formacie JSON.**  

  * **poprzednie wersje:** analizatora składni notacji JSON będzie automatycznie upcast — ciąg, wpisz wartości daty/godziny/strefy informacji do daty/godziny, a następnie przekonwertować go na UTC. Pozwoliło to odnotować utraty informacji o strefie czasowej.

  * **Bieżąca wersja:** ma nie więcej automatycznie upcast — wartości ciągu daty/godziny/strefy informacje umożliwiające typu DateTime. W rezultacie są przechowywane informacje dotyczące strefy czasowej. 

## <a name="next-steps"></a>Kolejne kroki
* [Rozwiązywanie problemów z danych wejściowych usługi Azure Stream Analytics](stream-analytics-troubleshoot-input.md)
* [Stream Analytics Resource health bloku](stream-analytics-resource-health.md)