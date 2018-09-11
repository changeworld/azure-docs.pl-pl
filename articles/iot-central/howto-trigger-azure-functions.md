---
title: Wyzwalanie usługi Azure Functions przy użyciu elementów webhook w usłudze Azure IoT Central
description: Tworzenie aplikacji funkcji, która działa z każdym razem, gdy reguła jest wyzwalana w usłudze Azure IoT Central.
author: viv-liu
ms.author: viviali
ms.date: 09/06/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 7b14dc4eeec1ab543c407b1bb1cf8a5ce46f3ecb
ms.sourcegitcommit: af9cb4c4d9aaa1fbe4901af4fc3e49ef2c4e8d5e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/11/2018
ms.locfileid: "44356908"
---
# <a name="trigger-azure-functions-using-webhooks-in-azure-iot-central"></a>Wyzwalanie usługi Azure Functions przy użyciu elementów webhook w usłudze Azure IoT Central

Uruchamianie kodu bez serwera na dane wyjściowe elementu webhook z IoT Central reguły za pomocą usługi Azure Functions. Nie trzeba aprowizować maszynę Wirtualną lub publikowania aplikacji sieci web za pomocą usługi Azure Functions, ale zamiast tego można uruchomić ten kod serverlessly. Przekształcanie ładunek elementu webhook przed wysłaniem ich do miejsca docelowego, takich jak bazy danych SQL lub usługi Event Grid przy użyciu usługi Azure Functions. 

## <a name="prerequisites"></a>Wymagania wstępne
+ Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="how-to-connect-azure-functions"></a>Sposób połączenia usługi Azure Functions

1. [Utwórz nową aplikację funkcji w witrynie Azure Portal. ](https://ms.portal.azure.com/#create/Microsoft.FunctionApp).
2. Rozwiń swoją aplikację funkcji, a następnie kliknij przycisk + przycisk obok funkcji. Jeśli ta funkcja jest pierwszy z nich w aplikacji funkcji, wybierz funkcję niestandardowego. Spowoduje to wyświetlenie pełnego zestawu szablonów funkcji.
3. W polu wyszukiwania wpisz ogólne, a następnie wybierz żądany język dla szablonu wyzwalacza ogólnego elementu webhook. W tym temacie użyto funkcji języka C#. 
4. W nowej funkcji kliknij pozycję **</> Pobierz adres URL funkcji**, po czym skopiuj i zapisz wartość. Będzie ona używana do skonfigurowania elementu webhook. 
4. Zawiera reguły, którą chcesz nawiązać połączenie z aplikacją funkcji IoT Central. 
5. Dodawanie akcji elementu webhook. Wprowadź **nazwę wyświetlaną** i wklej adres URL funkcji, które wcześniej zostały skopiowane.
6. Zapisać reguły. Teraz po wyzwoleniu reguły, element webhook wywoła aplikację funkcji, aby uruchomić. W aplikacji funkcji można zaobserwować, dzienniki i zobacz, w każdym wyzwoleniu funkcji.

Więcej informacji można znaleźć w artykule usługi Azure Functions o [Tworzenie funkcji wyzwalanej przez ogólny element webhook](https://docs.microsoft.com/azure/azure-functions/functions-create-generic-webhook-triggered-function). 

## <a name="next-steps"></a>Kolejne kroki
Teraz, gdy wiesz jak konfigurowanie i używanie elementów webhook sugerowane następnym krokiem jest Eksplorowanie [tworzenia przepływów pracy w programie Microsoft Flow](howto-add-microsoft-flow.md).
