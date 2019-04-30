---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: time-series-insights
author: ashannon7
ms.service: time-series-insights
ms.topic: include
ms.date: 08/20/2018
ms.author: anshan
ms.custom: include file
ms.openlocfilehash: c9daa86bf36b260001d9969385b9e8a98a8ac0cf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61443246"
---
## <a name="grant-data-access"></a>Przyznawanie dostępu do danych

Wykonaj następujące kroki, aby zezwolić na dostęp do nazwy głównej użytkownika:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. Znajdź środowiska usługi Time Series Insights. Typ **szeregów czasowych** w **wyszukiwania** pole. Wybierz **środowiska usługi Time Series** w wynikach wyszukiwania. 

3. Wybierz środowisko usługi Time Series Insights z listy.

4. Wybierz **zasady dostępu do danych**, a następnie wybierz **+ Dodaj**.
    ![Zarządzanie źródłem usługi Time Series Insights — środowisko](media/iot-tsi-data-access/getstarted-grant-data-access1.png)

5. Wybierz **wybierz użytkownika**.  Wyszukaj adres nazwy lub adresu e-mail użytkownika zlokalizować użytkownika, którego chcesz dodać. Kliknij przycisk **wybierz** aby potwierdzić wybór. 

    ![Zarządzanie źródłem usługi Time Series Insights — dodawanie](media/iot-tsi-data-access/getstarted-grant-data-access2.png)

6. Wybierz **wybierz rolę**. Wybierz rolę odpowiedni dostęp dla użytkownika:
   - Wybierz **Współautor** Jeśli chcesz zezwolić użytkownikowi na zmianę danych referencyjnych i udziału zapisany, zapytań i perspektyw innym użytkownikom środowiska. 
   - W przeciwnym razie wybierz **czytnika** do zezwalania na zapytania o dane użytkownika w środowisku i zapisywanie osobistych zapytań (nie udostępnianych) w środowisku.

     Wybierz **Ok** aby potwierdzić wybór roli.

     ![Zarządzanie źródłem usługi Time Series Insights — wybieranie użytkownika](media/iot-tsi-data-access/getstarted-grant-data-access3.png)

7. Wybierz **Ok** w **wybierz rolę użytkownika** strony.

    ![Zarządzanie źródłem usługi Time Series Insights — wybieranie roli](media/iot-tsi-data-access/getstarted-grant-data-access4.png)

8. **Zasady dostępu do danych** strona zawiera listę użytkowników i rolami dla każdego użytkownika.

    ![Zarządzanie źródłem usługi Time Series Insights — wyniki](media/iot-tsi-data-access/getstarted-grant-data-access5.png)