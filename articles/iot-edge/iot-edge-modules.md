---
title: Zrozumienie modułów Azure IoT krawędzi | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o moduły krawędzi IoT Azure i sposobu ich konfiguracji
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 02/15/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 9c196ec92fc7997617fa464d676dc93ca9fe84f0
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37029098"
---
# <a name="understand-azure-iot-edge-modules"></a>Zrozumienie modułów krawędzi IoT Azure

Krawędź IoT Azure umożliwia wdrażanie i zarządzanie nimi logiki biznesowej na krawędzi w formie *modułów*. Azure IoT krawędzi modułów najmniejsza obliczeń zarządza krawędzi IoT i mogą zawierać usług platformy Azure (np. Azure Stream Analytics) lub kodu określonego rozwiązania. Aby zrozumieć, jak moduły opracowany, wdrożona i jest aktualizowany, warto wziąć pod uwagę z czterech części pojęciach, wchodzące w skład modułu:

* A **obrazu modułu** pakiet z oprogramowaniem, który definiuje modułu.
* A **wystąpienia modułu** jest konkretnej jednostki obliczeń systemem obrazu modułu urządzenia IoT. Wystąpienie modułu jest uruchomiona przez środowisko uruchomieniowe IoT krawędzi.
* A **tożsamości modułu** jest informacji (w tym poświadczenia zabezpieczeń) przechowywane w Centrum IoT, skojarzony do każdego wystąpienia modułu.
* A **dwie modułu** jest przechowywane w Centrum IoT, który zawiera informacje o stanie dla wystąpienia modułu, w tym metadanych, konfiguracji i warunki dokumentu JSON. 

## <a name="module-images-and-instances"></a>Moduł obrazów i wystąpień

Obrazy modułu krawędzi IoT zawierają aplikacji korzystających z zarządzania, zabezpieczeń i funkcji komunikacji środowiska uruchomieniowego IoT krawędzi. Tworzenie obrazów modułu lub wyeksportować jeden z obsługiwanych usługi Azure, takich jak usługi Azure Stream Analytics.
Obrazy istnieją w chmurze i mogą być aktualizowane, zmienić i wdrożone w różnych rozwiązaniach. Na przykład moduł, który używa prognozowania dane wyjściowe wiersza produkcyjnym uczenia maszynowego istnieje jako osobny obraz niż moduł, który używa wizji komputera do sterowania drone. 

Obraz modułu jest wdrażana na urządzeniu i uruchomiona przez środowisko uruchomieniowe krawędzi IoT, nowe wystąpienie klasy ten moduł jest tworzony. Dwa urządzenia na różne części świata można użyć tego samego obrazu modułu. Jednak każdy musi własne wystąpienie modułu podczas uruchamiania modułu na urządzeniu. 

![Moduł obrazów w chmurze — wystąpienia modułu, na urządzeniach][1]

W implementacji obrazy modułów istnieje jako kontener obrazów w repozytorium, a wystąpienia modułu są kontenerami na urządzeniach. Wzrostem przypadków użycia usługi Azure IoT Edge zostaną utworzone nowe typy obrazów modułu i wystąpień. Na przykład ograniczone zasobów urządzeń nie można uruchomić kontenerów, tak więc może wymagać modułu obrazów, które istnieje jako biblioteki dołączanej dynamicznie i wystąpień, które są pliki wykonywalne. 

## <a name="module-identities"></a>Moduł tożsamości

Po utworzeniu nowego wystąpienia modułu przez środowisko uruchomieniowe krawędzi IoT wystąpienie jest skojarzone z tożsamością odpowiedniego modułu. Znajduje się w Centrum IoT i jako zakres adresowania i zabezpieczeń dla wszystkich lokalnych i chmurze komunikacji dla tego wystąpienia modułu specyficznego tożsamości modułu.
Tożsamość skojarzoną z wystąpieniem modułu zależy tożsamości urządzenia, na którym działa wystąpienie i nazwy Podaj do tego modułu w rozwiązaniu. Na przykład można wywołać `insight` moduł, który korzysta z usługi Azure Stream Analytics i należy ją wdrożyć na urządzenie nazywane `Hannover01`, środowiska uruchomieniowego krawędzi IoT tworzy odpowiedni tożsamość modułu o nazwie `/devices/Hannover01/modules/insight`.

Wyraźnie widać w scenariuszach, gdy należy wdrożyć jeden obraz modułu wiele razy w tym samym urządzeniu, można wdrożyć ten sam obraz wiele razy przy użyciu innej nazwy.

![Moduł tożsamości są unikatowe][2]

## <a name="module-twins"></a>Moduł twins

Każde wystąpienie modułu ma również odpowiedniego dwie modułu, którego można użyć do skonfigurowania wystąpienia modułu. Wystąpienie i dwie są powiązane ze sobą za pomocą tożsamości modułu. 

Dwie modułu jest dokument JSON, który przechowuje właściwości informacje i konfiguracji modułu. To pojęcie równoleżnikami [dwie urządzenia] [ lnk-device-twin] koncepcji z Centrum IoT. Struktury dwie modułu jest dokładnie taka sama jak dwie urządzenia. Interfejsy API używane do interakcji z oboma typami twins również są takie same. Jedyną różnicą między tymi dwoma jest tożsamości używanej do tworzenia wystąpienia zestawu SDK klienta. 

```csharp
// Create a DeviceClient object. This DeviceClient will act on behalf of a 
// module since it is created with a module’s connection string instead 
// of a device connection string. 
DeviceClient client = new DeviceClient.CreateFromConnectionString(moduleConnectionString, settings); 
await client.OpenAsync(); 
 
// Get the model twin 
Twin twin = await client.GetTwinAsync(); 
```

## <a name="offline-capabilities"></a>Możliwości w trybie offline

Azure IoT krawędzi obsługuje operacje w trybie offline na urządzeniach IoT krawędzi. Te możliwości są ograniczone teraz, a dodatkowe scenariusze są opracowywane. 

Moduły krawędzi IoT może być w trybie offline przez dłuższy czas, tak długo, jak są spełnione następujące wymagania: 

* **Czas wygaśnięcia wiadomości (TTL) nie wygasł**. Wartością domyślną dla czas wygaśnięcia wiadomości jest o dwie godziny, ale może zostać zmieniony wyższe lub niższe w magazynie i przekazywania konfiguracji w programie IoT Edge hub ustawienia. 
* **Moduły nie ma potrzeby ponownego uwierzytelnienia z Centrum IoT krawędzi w trybie offline**. Moduły mogą uwierzytelniać tylko z koncentratorami krawędzi, które mają aktywne połączenie z Centrum IoT. Moduły muszą ponownego uwierzytelnienia, jeśli ich ponownego uruchomienia różnych przyczyn. Moduły nadal może wysyłać wiadomości do koncentratora krawędzi, po upływie ich tokenu sygnatury dostępu Współdzielonego. Po wznowieniu połączenia, Centrum krawędzi żąda nowy token z modułu, a następnie zweryfikuje go z Centrum IoT. W przypadku powodzenia Centrum krawędzi przesyła dalej wiadomości moduł, który przechowywanych nawet wiadomości, które zostały wysłane, gdy token modułu wygasła. 
* **Moduł, który wysyłane wiadomości w trybie offline nadal działa po wznowieniu połączenia**. Po połączeniu z Centrum IoT, Centrum krawędzi wymagane jest sprawdzenie nowy token modułu (Jeśli poprzedni ważność) przed może przekazywać komunikaty modułu. Jeśli moduł nie jest dostępne w celu zapewnienia nowy token, Centrum krawędzi nie może działać na modułu przechowywane wiadomości. 
* **Koncentrator krawędzi ma miejsce na dysku do przechowywania wiadomości**. Domyślnie komunikaty są przechowywane w kontenerze Centrum krawędzi systemu plików. Brak opcji konfiguracji, aby określić zainstalowany wolumin do przechowywania wiadomości zamiast tego. W obu przypadkach musi istnieć miejsca do przechowywania komunikatów do odroczonego dostarczenia Centrum IoT.  

## <a name="next-steps"></a>Kolejne kroki
 - [Zrozumienie jego architektura i środowiska uruchomieniowego krawędzi IoT Azure][lnk-runtime]

<!-- Images -->
[1]: ./media/iot-edge-modules/image_instance.png
[2]: ./media/iot-edge-modules/identity.png

<!-- Links -->
[lnk-device-identity]: ../iot-hub/iot-hub-devguide-identity-registry.md
[lnk-device-twin]: ../iot-hub/iot-hub-devguide-device-twins.md
[lnk-runtime]: iot-edge-runtime.md