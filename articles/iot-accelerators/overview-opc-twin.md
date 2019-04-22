---
title: Co to jest bliźniacza reprezentacja OPC — Azure | Dokumentacja firmy Microsoft
description: Omówienie bliźniaczej reprezentacji OPC
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: overview
ms.service: iot-industrialiot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 9daf1a7e58af23cb78705691217bf9709359c4d5
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59496816"
---
# <a name="what-is-azure-iot-open-platform-communications-opc-device-management"></a>Co to jest zarządzanie urządzeniami usługi Azure IoT Otwórz platformy łączności (OPC)?

Bliźniacza reprezentacja OPC składa się z mikrousług connect, chmury i sieci fabrycznej za pomocą usługi Azure IoT Edge i IoT Hub. Bliźniacza reprezentacja OPC udostępnia funkcje odnajdywania, rejestracji i zdalne sterowanie urządzeń przemysłowych za pośrednictwem interfejsów API REST. Bliźniacza reprezentacja OPC nie wymaga OPC Unified architektury (OPC UA) zestawu SDK jest programowania niezależny od języka i mogą być zawarte w przepływie pracy bez użycia serwera. W tym artykule opisano kilka przypadków użycia bliźniaczej reprezentacji OPC.

## <a name="discovery-and-control"></a>Odnajdowanie i sterowanie
Za pomocą bliźniaczej reprezentacji OPC dla prostej odnajdywania i rejestracji.

### <a name="simple-discovery-and-registration"></a>Proste odnajdywania i rejestracji
Bliźniacza reprezentacja OPC umożliwia operatory fabryki do skanowania sieci fabrycznej, tak, aby serwery OPC UA podlega odnajdywaniu i zarejestrowane. Jako alternatywę operatory fabryki również ręcznie zarejestrować urządzenia OPC UA za pomocą adresu URL odnajdywania znane. Aby połączyć wszystkie urządzenia OPC UA, po zainstalowaniu bramy usługi IoT Edge przy użyciu OPC bliźniaczej reprezentacji modułu na etapie produkcji, operator fabryki można na przykład zdalnie wyzwolić skanowania sieci i wizualnie Zobacz wszystkie serwery OPC UA. 

### <a name="simple-control"></a>Prosty formant
Bliźniacza reprezentacja OPC umożliwia operatory fabryki, aby reagować na zdarzenia i skonfigurować automatyczne lub ręczne ich maszyn floor fabryki z chmury na bieżąco. Bliźniacza reprezentacja OPC udostępnia interfejsy API REST do wywołania usług na serwer OPC UA, przeglądanie jego przestrzeni adresowej do odczytu i zapisu zmiennych i wykonywanie metod. Na przykład kocioł używa temperatury kluczowy wskaźnik wydajności do kontrolowania linii produkcyjnej. Czujnik temperatury publikuje zmiany w danych za pomocą wydawcy OPC. Operator fabryki otrzymuje alert, że temperatura osiągnęła wartość progową. Linia produkcyjna chłodzi automatycznie za pomocą bliźniaczej reprezentacji OPC. Operator fabryki jest powiadamiany o superpaska w dół.

## <a name="authentication"></a>Authentication
Za pomocą bliźniaczej reprezentacji OPC dla prostej do uwierzytelniania i środowiska deweloperów proste.

### <a name="simple-authentication"></a>Proste uwierzytelnianie 
Bliźniacza reprezentacja OPC korzysta z uwierzytelniania opartego na usłudze Azure Active Directory AAD i inspekcji z elementu end-to-end. Na przykład OPC bliźniaczej reprezentacji umożliwia aplikacji, które ma zostać utworzony na podstawie bliźniaczej reprezentacji OPC, aby ustalić, operator przeprowadził na maszynie. Na stronie maszyny jest za pośrednictwem serwera OPC UA inspekcji. Po stronie chmury jest do przechowywania dziennika inspekcji niezmienne klienta i uwierzytelnianie w usłudze AAD w interfejsie API REST.

### <a name="simple-developer-experience"></a>Środowisko programistyczne prosty 
Bliźniacza reprezentacja OPC może służyć za pomocą aplikacji napisanych w dowolnym języku programowania za pomocą interfejsów API REST. Wiedzę na temat zestawu SDK serwera OPC UA deweloperów zintegrować to rozwiązanie klienta OPC UA, nie jest konieczne. Bliźniacza reprezentacja OPC można bezproblemowo Zintegruj bezstanowych, bezserwerowej architektury. Na przykład, dla pełnego stosu sieci web deweloperów, którzy odpowiedzialny za rozwój aplikacji dla alarmów i zdarzenia pulpitu nawigacyjnego można napisać logikę odpowiadania na zdarzenia w JavaScript lub TypeScript za pomocą bliźniaczej reprezentacji OPC bez znajomości języka C, C#, lub pełną implementację stosu OPC UA. 

## <a name="next-steps"></a>Kolejne kroki

Skoro wiesz już o bliźniaczych OPC i jego zastosowań, poniżej przedstawiono sugerowany następny krok:

> [!div class="nextstepaction"]
> [Co to jest magazyn OPC](overview-opc-twin-architecture.md)