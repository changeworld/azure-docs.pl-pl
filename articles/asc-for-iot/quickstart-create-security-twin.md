---
title: Tworzenie bliźniaczą reprezentację modułu zabezpieczeń Centrum zabezpieczeń Azure dla IoT (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć wiedzy usługi Azure Security Center bliźniacza reprezentacja modułu IoT do użycia z usługą ASC dla IoT.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: c782692e-1284-4c54-9d76-567bc13787cc
ms.service: ascforiot
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2019
ms.author: mlottner
ms.openlocfilehash: 93b6aa6fe133953025cd504e0486a51c4a3d5e04
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2019
ms.locfileid: "58758365"
---
# <a name="quickstart-create-an-azureiotsecurity-module-twin"></a>Szybki start: Utwórz bliźniaczą reprezentację modułu azureiotsecurity

> [!IMPORTANT]
> Centrum zabezpieczeń Azure dla IoT jest obecnie w publicznej wersji zapoznawczej. Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ten przewodnik Szybki Start objaśnienia dotyczące sposobu tworzenia poszczególnych _azureiotsecurity_ bliźniaczych reprezentacjach modułów na nowych urządzeniach lub partii tworzenie bliźniaczych reprezentacjach modułów dla wszystkich urządzeń w usłudze IoT Hub.  

## <a name="understanding-azureiotsecurity-module-twins"></a>Informacje o bliźniaczych reprezentacjach modułów azureiotsecurity 

Dla rozwiązań IoT, zbudowany na platformie Azure bliźniacze reprezentacje urządzeń odgrywają kluczową rolę zarówno w przypadku zarządzania urządzeniami, jak i automatyzację procesów. 

Usługa Azure Security Center (ASC) dla IoT oferuje pełną integrację ze swojej istniejącej IoT platforma do zarządzania urządzeniami, dzięki któremu można zarządzać stanu zabezpieczeń urządzenia oraz upewnij korzystać z istniejących możliwości kontroli urządzenia.
ASC integracji IoT jest osiągane dzięki wykorzystaniu usługi IoT Hub bliźniaczej reprezentacji mechanizm.  

Zobacz [bliźniaczych reprezentacjach modułów usługi IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-module-twins) dowiedzieć się więcej o ogólnym założeniem bliźniaczych reprezentacjach modułów w usłudze Azure IoT Hub. 
 
ASC dla IoT ułatwia korzystanie z mechanizmu bliźniaczej reprezentacji modułu i utrzymuje bliźniaczą reprezentację modułu na zabezpieczeń o nazwie _azureiotsecurity_ dla każdego urządzenia.
Bliźniacza reprezentacja modułu zabezpieczeń przechowuje wszystkie informacje dotyczą zasady zabezpieczeń urządzeń dla każdego urządzenia. 
 
Aby wykorzystać ASC funkcji IoT, musisz tworzenia, konfigurowania i używania tych bliźniaczych reprezentacjach modułów zabezpieczeń dla każdego urządzenia w usłudze.  

## <a name="create-azureiotsecurity-module-twin"></a>Utwórz bliźniaczą reprezentację modułu azureiotsecurity 

_azureiotsecurity_ bliźniaczych reprezentacjach modułów można utworzyć na dwa sposoby:
1. [Skrypt wsadowy modułu](https://aka.ms/iot-security-github-create-module) — automatycznie tworzy bliźniaczą reprezentację modułu dla nowych urządzeń lub urządzeń bez bliźniaczą reprezentację modułu korzysta z konfiguracji domyślnej.
2. Ręczna Edycja każdego bliźniaczą reprezentację modułu za pomocą konfiguracji specyficznych dla każdego urządzenia.

>[!NOTE] 
> Za pomocą metody partii nie spowoduje zastąpienie istniejących azureiotsecurity bliźniaczych reprezentacjach modułów. TYLKO przy użyciu metody batch tworzy nowy bliźniaczych reprezentacjach modułów dla urządzeń, które nie zostały jeszcze bliźniaczą reprezentację modułu zabezpieczeń. 

Zobacz [konfiguracji agenta](how-to-agent-configuration.md) dowiesz się, jak zmodyfikować lub zmiany konfiguracji istniejących bliźniaczą reprezentację modułu. 

Aby ręcznie utworzyć nowy _azureiotsecurity_ modułu bliźniacza reprezentacja urządzenia, skorzystaj z poniższych instrukcji: 

1. W usłudze IoT Hub Znajdź i wybierz urządzenie, którego chcesz utworzyć zabezpieczeń bliźniaczą reprezentację modułu w usłudze IoT Hub.
1. Kliknij przycisk na urządzeniu, a następnie na **Dodaj tożsamość modułu**.
1. W **nazwy tożsamości modułu** wprowadź **azureiotsecurity**.

1. Kliknij pozycję **Zapisz**. 

## <a name="verify-creation-of-a-module-twin"></a>Sprawdź tworzenia bliźniaczej reprezentacji modułu

Aby sprawdzić, czy bliźniaczą reprezentację modułu zabezpieczeń istnieje dla określonego urządzenia:

1. W usłudze Azure IoT Hub, wybierz **urządzeń IoT** z **Eksploratory** menu.    
1. Wprowadź identyfikator urządzenia, lub wybierz opcję **pola urządzenia zapytania** i kliknij przycisk **zapytania urządzeń**. 
    ![Zapytania urządzeń](./media/quickstart/verify-security-module-twin.png)
1. Wybierz urządzenie lub dwukrotnie kliknij go, aby otworzyć stronę szczegółów urządzenia. 
1. Wybierz **tożsamości modułu** menu i Sprawdź istnienie **azureiotsecurity** modułu na liście tożsamości modułu skojarzone z urządzeniem. 
    ![Moduły skojarzone z urządzenia](./media/quickstart/verify-security-module-twin-3.png)


Aby dowiedzieć się więcej o dostosowywaniu właściwości ASC dla bliźniaczych reprezentacjach modułów IoT, zobacz [konfiguracji agenta](how-to-agent-configuration.md).

## <a name="next-steps"></a>Kolejne kroki

Przejdź do następnego artykułu, aby dowiedzieć się, jak skonfigurować niestandardowe alerty...

> [!div class="nextstepaction"]
> [Skonfiguruj alerty niestandardowe](quickstart-create-custom-alerts.md)
