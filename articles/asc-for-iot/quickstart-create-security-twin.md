---
title: 'Szybki start: tworzenie bliźniaczej reprezentacji modułu zabezpieczeń dla usługi Azure Security Center dla IoT'
description: W tym przewodniku Szybki start dowiedz się, jak utworzyć bliźniaczątkę modułu Usługi Azure Security Center dla IoT do użytku z usługą Azure Security Center dla IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: c782692e-1284-4c54-9d76-567bc13787cc
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/08/2019
ms.author: mlottner
ms.openlocfilehash: b362130c2b717f813a6332f81a3c8179bea4166a
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "73904164"
---
# <a name="quickstart-create-an-azureiotsecurity-module-twin"></a>Szybki start: tworzenie bliźniaczej reprezentacji modułu azureiotsecurity

W tym przewodniku Szybki start wyjaśniono, jak utworzyć pojedyncze bliźniacze _moduły azureiotsecurity_ dla nowych urządzeń lub utworzyć bliźniacze moduły wsadowe dla wszystkich urządzeń w centrum IoT Hub.  

## <a name="understanding-azureiotsecurity-module-twins"></a>Opis bliźniacze modułów azureiotsecurity 

W przypadku rozwiązań IoT wbudowanych w platformę Azure bliźniacze urządzenia odgrywają kluczową rolę zarówno w zarządzaniu urządzeniami, jak i automatyzacji procesów. 

Usługa Azure Security Center for IoT oferuje pełną integrację z istniejącą platformą zarządzania urządzeniami IoT, umożliwiając zarządzanie stanem zabezpieczeń urządzenia, a także korzystanie z istniejących funkcji sterowania urządzeniami.
Integracja usługi Azure Security Center dla IoT jest osiągana przez użycie mechanizmu bliźniaczego IoT Hub.  

Zobacz [bliźniacze moduły usługi IoT Hub,](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-module-twins) aby dowiedzieć się więcej o ogólnej koncepcji bliźniaczych modułów w usłudze Azure IoT Hub. 
 
Usługa Azure Security Center for IoT korzysta z mechanizmu bliźniaczej reprezentacji modułu i utrzymuje bliźniaczą część modułu zabezpieczeń o nazwie _azureiotsecurity_ dla każdego z twoich urządzeń.

Moduł bliźniaczej reprezentacji modułu zabezpieczeń zawiera wszystkie informacje istotne dla bezpieczeństwa urządzeń dla każdego z urządzeń. 
 
Aby w pełni korzystać z funkcji Usługi Azure Security Center dla IoT, należy utworzyć, skonfigurować i używać tych bliźniaczych modułów zabezpieczeń dla każdego urządzenia w usłudze.  

## <a name="create-azureiotsecurity-module-twin"></a>Tworzenie bliźniaczej reprezentacji modułu azureiotsecurity 

_azureiotsecurity_ bliźniacze modułu mogą być tworzone na dwa sposoby:
1. [Skrypt wsadowy modułu](https://aka.ms/iot-security-github-create-module) - automatycznie tworzy bliźniaczą reprezentację modułu dla nowych urządzeń lub urządzeń bez bliźniaczej reprezentacji modułu przy użyciu domyślnej konfiguracji.
2. Ręczna edycja każdego modułu bliźniaczej reprezentacji indywidualnie z określonymi konfiguracjami dla każdego urządzenia.

>[!NOTE] 
> Za pomocą metody wsadowej nie zastąpi istniejących azureiotsecurity bliźniacze modułu. Za pomocą metody wsadowej TYLKO tworzy nowe bliźniacze moduły dla urządzeń, które nie mają jeszcze bliźniaczej reprezentacji modułu zabezpieczeń. 

Zobacz [konfigurację agenta,](how-to-agent-configuration.md) aby dowiedzieć się, jak zmodyfikować lub zmienić konfigurację istniejącej bliźniaczej reprezentacji modułu. 

Aby ręcznie utworzyć nową bliźniaczkę modułu _azureiotsecurity_ dla urządzenia, należy użyć następujących instrukcji: 

1. W centrum IoT Hub znajdź i wybierz urządzenie, dla którego chcesz utworzyć bliźniaczątkę modułu zabezpieczeń.
1. Kliknij urządzenie, a następnie dodaj **tożsamość modułu**.
1. W polu **Nazwa tożsamości modułu** wprowadź **azureiotsecurity**.

1. Kliknij przycisk **Zapisz**. 

## <a name="verify-creation-of-a-module-twin"></a>Weryfikowanie tworzenia bliźniaczej reprezentacji modułu

Aby sprawdzić, czy dla określonego urządzenia istnieje bliźniacza reprezentacja modułu zabezpieczeń:

1. W centrum Usługi Azure IoT Hub wybierz **urządzenia IoT** z menu **Eksploratory.**    
1. Wprowadź identyfikator urządzenia lub wybierz opcję w **polu Urządzenie kwerendy** i kliknij pozycję **Urządzenia kwerendy**. 
    ![Urządzenia kwerendy](./media/quickstart/verify-security-module-twin.png)
1. Wybierz urządzenie lub kliknij je dwukrotnie, aby otworzyć stronę Szczegóły urządzenia. 
1. Wybierz **menu Tożsamości modułu** i potwierdź istnienie modułu **azureiotsecurity** na liście tożsamości modułu skojarzonych z urządzeniem. 
    ![Moduły skojarzone z urządzeniem](./media/quickstart/verify-security-module-twin-3.png)


Aby dowiedzieć się więcej na temat dostosowywania właściwości usługi Azure Security Center dla bliźniaczych modułów IoT, zobacz [Konfiguracja agenta](how-to-agent-configuration.md).

## <a name="next-steps"></a>Następne kroki

Przejdź do następnego artykułu, aby dowiedzieć się, jak skonfigurować niestandardowe alerty...

> [!div class="nextstepaction"]
> [Konfigurowanie alertów niestandardowych](quickstart-create-custom-alerts.md)
