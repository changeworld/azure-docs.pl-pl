---
title: Co to jest OPC Twin - Azure | Dokumenty firmy Microsoft
description: Ten artykuł zawiera omówienie OPC Twin. OPC Twin zapewnia odnajdowanie, rejestrację i zdalne sterowanie urządzeniami przemysłowymi za pośrednictwem interfejsów API REST.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 91448f55f0ebb88ba6c685b960ece9d91cb98e25
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "73826223"
---
# <a name="what-is-opc-twin"></a>Co to jest OPC Twin?

Funkcja OPC Twin składa się z mikrousług, które używają usługi Azure IoT Edge i usługi IoT Hub do łączenia chmury z siecią fabryczną. OPC Twin zapewnia odnajdowanie, rejestrację i zdalne sterowanie urządzeniami przemysłowymi za pośrednictwem interfejsów API REST. OPC Twin nie wymaga OPC Unified Architecture (OPC UA) SDK, jest niezależny od języka programowania i może być uwzględniony w przepływie pracy bezserwerowej. W tym artykule opisano kilka przypadków użycia OPC Twin.

## <a name="discovery-and-control"></a>Odnajdowanie i kontrola
Możesz użyć OPC Twin dla prostego do odnajdowania i rejestracji.

### <a name="simple-discovery-and-registration"></a>Proste odkrywanie i rejestracja
OPC Twin umożliwia operatorom fabrycznym skanowanie sieci fabrycznej, dzięki czemu serwery OPC UA mogą być odnajdowane i zarejestrowane. Alternatywnie operatorzy fabryki mogą również ręcznie rejestrować urządzenia OPC UA przy użyciu znanego adresu URL odnajdywania. Na przykład, aby połączyć się ze wszystkimi urządzeniami OPC UA po zainstalowaniu bramy IoT Edge z modułem OPC Twin na hali produkcyjnej, operator fabryki może zdalnie uruchomić skanowanie sieci i wizualnie wyświetlić wszystkie serwery OPC UA. 

### <a name="simple-control"></a>Prosta kontrola
OPC Twin pozwala operatorom fabryki reagować na zdarzenia i rekonfigurować swoje maszyny fabryczne z chmury automatycznie lub ręcznie w locie. OPC Twin udostępnia interfejsy API REST do wywoływania usług na serwerze OPC UA, przeglądania jego przestrzeni adresowej, a także do odczytu/zapisu zmiennych i wykonywania metod. Na przykład kocioł używa kluczowego wskaźnika temperatury do sterowania linią produkcyjną. Czujnik temperatury publikuje zmiany w danych za pomocą programu OPC Publisher. Operator fabryki odbiera alert, że temperatura osiągnęła próg. Linia produkcyjna ochładza się automatycznie za pośrednictwem OPC Twin. Operator fabryki jest powiadamiany o schładzaniu.

## <a name="authentication"></a>Uwierzytelnianie
OPC Twin można używać do uwierzytelniania i prostego środowiska deweloperskiego.

### <a name="simple-authentication"></a>Uwierzytelnianie proste 
OPC Twin używa uwierzytelniania opartego na usłudze Azure Active Directory (AAD) i inspekcji od końca do końca. Na przykład OPC Twin umożliwia wbudowanie aplikacji na wierzchu OPC Twin, aby określić, co operator wykonał na komputerze. Po stronie maszyny jest to za pośrednictwem inspekcji OPC UA. Po stronie chmury jest to poprzez przechowywanie niezmienne dziennika inspekcji klienta i uwierzytelniania usługi AAD w interfejsie API REST.

### <a name="simple-developer-experience"></a>Proste środowisko deweloperskie 
OPC Twin może być używany z aplikacjami napisanymi w dowolnym języku programowania za pośrednictwem interfejsów API REST. Ponieważ deweloperzy integrują klienta OPC UA z rozwiązaniem, znajomość SDK OPC UA nie jest konieczna. OPC Twin może bezproblemowo zintegrować się z bezstanowymi, bezserwerowymi architekturami. Na przykład deweloper sieci web pełnego stosu, który opracowuje aplikację dla pulpitu nawigacyjnego alarmu i zdarzeń, może napisać logikę, aby reagować na zdarzenia w języku JavaScript lub TypeScript przy użyciu funkcji OPC Twin bez znajomości języka C, C#lub pełnej implementacji stosu OPC UA. 

## <a name="next-steps"></a>Następne kroki

Teraz, gdy dowiedziałeś się o OPC Twin i jego zastosowaniach, oto sugerowany następny krok:

> [!div class="nextstepaction"]
> [Co to jest magazyn OPC](overview-opc-vault.md)
