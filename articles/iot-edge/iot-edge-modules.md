---
title: Omówienie modułów usługi Azure IoT Edge | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o moduły usługi Azure IoT Edge i sposobu ich konfiguracji
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 02/15/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 76b0bab0f2eb34d7283d38eb0442f4f2f2083db3
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46995386"
---
# <a name="understand-azure-iot-edge-modules"></a>Omówienie modułów usługi Azure IoT Edge

Usługa Azure IoT Edge umożliwia wdrażanie i zarządzanie nimi logiki biznesowej na urządzeniach brzegowych w formie *modułów*. Moduły platformy Azure IoT Edge to najmniejsza obliczeń zarządzane przez usługi IoT Edge i może zawierać usług platformy Azure (np. usługi Azure Stream Analytics) lub kodu specyficznego dla rozwiązania. Aby dowiedzieć się, jak moduły są opracowane, wdrażane i obsługiwane, warto traktować czterech koncepcyjny fragmentów, które tworzą modułu:

* A **obraz modułu** to pakiet zawierający wersję oprogramowania, który definiuje moduł.
* A **wystąpienia modułu** jest konkretnej jednostki obliczeń systemem obraz modułu urządzenia usługi IoT Edge. Wystąpienia modułu jest uruchomiona przez środowisko uruchomieniowe usługi IoT Edge.
* A **tożsamości modułu** to fragment informacji (w tym poświadczenia zabezpieczeń), przechowywane w usłudze IoT Hub, który jest skojarzony z każdego wystąpienia modułu.
* A **bliźniaczą reprezentację modułu** to dokument JSON przechowywany w usłudze IoT Hub, który zawiera informacje o stanie dla wystąpienia modułu, w tym metadane, konfiguracje i warunki. 

## <a name="module-images-and-instances"></a>Obrazy modułu i wystąpienia

Obrazy modułu usługi IoT Edge zawierają aplikacje wykorzystujące zarządzania, zabezpieczeń i funkcji komunikacji środowiska uruchomieniowego usługi IoT Edge. Możesz tworzyć swoje własne obrazy, modułu lub wyeksportować jeden z obsługiwanych usług platformy Azure, takich jak Azure Stream Analytics.
Obrazy istnieją w chmurze i mogą być aktualizowane, zmienić i wdrożone w różnych rozwiązaniach. Na przykład moduł, który korzysta z uczenia maszynowego przewiduje linii produkcyjnej, wyjściowy istnieje jako osobny obraz niż moduł, który używa przetwarzania obrazów w celu kontrolowania nagrodę: drona. 

Każdorazowo obraz modułu jest wdrażana na urządzeniu i uruchomiona przez środowisko uruchomieniowe usługi IoT Edge, tworzone jest nowe wystąpienie tego modułu. Dwa urządzenia w różnych częściach świata, można użyć tego samego obrazu modułu. Jednak każdy musi własne wystąpienie modułu podczas uruchamiania modułu na urządzeniu. 

![Obrazy modułu w chmurze — wystąpienia modułu na urządzeniach][1]

W implementacji obrazów modułów istnieje jako obrazów kontenerów w repozytorium, a wystąpienia modułu to kontenery, które na urządzeniach. 

<!--
As use cases for Azure IoT Edge grow, new types of module images and instances will be created. For example, resource constrained devices cannot run containers so may require module images that exist as dynamic link libraries and instances that are executables. 
-->

## <a name="module-identities"></a>Moduł tożsamości

Po utworzeniu nowego wystąpienia modułu przez środowisko uruchomieniowe usługi IoT Edge to wystąpienie jest skojarzony z odpowiedniej tożsamości modułu. Tożsamość modułu jest przechowywany w usłudze IoT Hub, a jako zakres adresowania i zabezpieczeń dla wszystkich lokalnych i chmurze komunikacji dla tego wystąpienia określonego modułu.
Tożsamość skojarzoną z wystąpieniem modułu zależy od tożsamości urządzenia działającego wystąpienia i nazwę tego modułu jest zapewnienie w rozwiązaniu. Na przykład jeśli wywołasz `insight` moduł, który korzysta z usługi Azure Stream Analytics i wdrożyć go na urządzenie o nazwie `Hannover01`, środowisko uruchomieniowe usługi IoT Edge tworzy odpowiedniej tożsamości modułu o nazwie `/devices/Hannover01/modules/insight`.

Wyraźnie widać w scenariuszach, gdy należy wdrożyć jeden obraz modułu wiele razy w tym samym urządzeniu, można wdrożyć ten sam obraz wielokrotnie pod różnymi nazwami.

![Moduł tożsamości są unikatowe][2]

## <a name="module-twins"></a>Bliźniaczych reprezentacjach modułów

Każde wystąpienie modułu ma też odpowiedni bliźniaczą reprezentację modułu, które umożliwiają skonfigurowanie wystąpienia modułu. Wystąpienia i bliźniaczej reprezentacji są powiązane ze sobą za pomocą tożsamości modułu. 

Bliźniacza reprezentacja modułu jest dokumentem JSON, która przechowuje informacje i konfiguracji właściwości modułu. To pojęcie równoleżnikami [bliźniaczej reprezentacji urządzenia] [ lnk-device-twin] koncepcji z usługi IoT Hub. Struktura bliźniaczą reprezentację modułu jest dokładnie taka sama jak w bliźniaczej reprezentacji urządzenia. Interfejsy API używane do interakcji z oboma typami bliźniaczych elementów również są takie same. Jedyną różnicą między tymi dwoma jest to tożsamość używana do tworzenia wystąpienia zestawu SDK klienta. 

```csharp
// Create a ModuleClient object. This ModuleClient will act on behalf of a 
// module since it is created with a module’s connection string instead 
// of a device connection string. 
ModuleClient client = new ModuleClient.CreateFromEnvironmentAsync(settings); 
await client.OpenAsync(); 
 
// Get the module twin 
Twin twin = await client.GetTwinAsync(); 
```

## <a name="offline-capabilities"></a>Możliwości w trybie offline

Usługa Azure IoT Edge obsługuje operacje w trybie offline na urządzeniach usługi IoT Edge. Te możliwości są ograniczone do teraz. 

Moduły usługi IoT Edge może być w trybie offline przez dłuższy czas, tak długo, jak są spełnione następujące wymagania: 

* **Komunikat, time to live (TTL) nie wygasł**. Wartość domyślna dla komunikatu czas wygaśnięcia to dwie godziny, ale umożliwia zmienione wyższej lub niższej w Store i przekazywania Centrum ustawień konfiguracji w programie usługi IoT Edge. 
* **Moduły nie ma potrzeby ponownego uwierzytelnienia za pomocą usługi IoT Edge hub w trybie offline**. Moduły mogą tylko uwierzytelniać z koncentratorami Edge, które mają aktywne połączenie z usługą IoT hub. Moduły muszą zostać ponownie uwierzytelniony, jeśli ponownym jakiegokolwiek powodu. Moduły nadal może wysyłać komunikaty do Centrum usługi Edge, po upływie ich tokenu sygnatury dostępu Współdzielonego. Po wznowieniu działania łączności, Centrum usługi Edge żądania nowy token z modułu i zweryfikuje go z usługą IoT hub. Jeśli to się powiedzie, Centrum usługi Edge przekazuje komunikaty modułu, który ma być przechowywany, nawet wiadomości, które zostały wysłane, gdy wygasł token modułu. 
* **Moduł, który wysyłane wiadomości w trybie offline nadal działa po wznowieniu działania łączności**. Po połączeniu usługi IoT Hub, Centrum usługi Edge wymagane jest sprawdzenie nowy token modułu (Jeśli poprzedni wygasła) przed można przesyłać dalej wiadomości modułu. Jeśli moduł nie jest w stanie udzielić nowego tokenu, Centrum usługi Edge nie może działać na modułu przechowywanych wiadomości. 
* **Centrum usługi Edge miejscem na dysku do przechowywania wiadomości**. Domyślnie komunikaty są przechowywane w kontenerze Centrum usługi Edge w systemie plików. Brak opcji konfiguracji, aby określić zainstalowany wolumin do przechowywania komunikatów, zamiast tego. W obu przypadkach musi istnieć miejsca do przechowywania komunikatów odroczonego dostarczanie do usługi IoT Hub.  

Dodatkowe możliwości w trybie offline są dostępne w publicznej wersji zapoznawczej. Aby uzyskać więcej informacji, zobacz [opis rozszerzony możliwości w trybie offline dla usługi IoT Edge, urządzeń, moduły i urządzeń podrzędnych](offline-capabilities.md).

## <a name="next-steps"></a>Kolejne kroki
 - [Omówienie środowiska uruchomieniowego usługi Azure IoT Edge oraz jej architektury][lnk-runtime]

<!-- Images -->
[1]: ./media/iot-edge-modules/image_instance.png
[2]: ./media/iot-edge-modules/identity.png

<!-- Links -->
[lnk-device-identity]: ../iot-hub/iot-hub-devguide-identity-registry.md
[lnk-device-twin]: ../iot-hub/iot-hub-devguide-device-twins.md
[lnk-runtime]: iot-edge-runtime.md
