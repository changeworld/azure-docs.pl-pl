---
title: Kontrola - Azure dostępu do danych monitorowania zdalnego | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera informacje dotyczące sposobu konfigurowania kontroli dostępu dla Eksploratora usługi Time Series Insights telemetrii w akceleratora rozwiązania monitorowania zdalnego
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 08/06/2018
ms.topic: conceptual
ms.openlocfilehash: 9d5d572c3e32e3645e65ba8d6fc28b567b3c1e9a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65827211"
---
# <a name="configure-access-controls-for-the-time-series-insights-telemetry-explorer"></a>Konfigurowanie kontroli dostępu w Eksploratorze danych telemetrycznych usługi Time Series Insights

Ten artykuł zawiera informacje o sposobie konfigurowania kontroli dostępu w Eksploratorze usługi Time Series Insights w akceleratora rozwiązania monitorowania zdalnego. Aby umożliwić użytkownikom akcelerator rozwiązań dostępu Eksploratora usługi Time Series Insights, musisz udzielić dostępu do danych w poszczególnych użytkowników.

Zasady dostępu do danych umożliwiają przyznawanie uprawnień do wysyłania zapytań dotyczących danych, manipulowania danymi referencyjnymi w środowisku oraz udostępniania zapisanych zapytań i perspektyw skojarzonych ze środowiskiem.

## <a name="grant-data-access"></a>Przyznawanie dostępu do danych

Wykonaj następujące kroki, aby zezwolić na dostęp do nazwy głównej użytkownika:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. Znajdź środowiska usługi Time Series Insights. Typ **szeregów czasowych** w **wyszukiwania** pole. Wybierz **środowiska usługi Time Series** w wynikach wyszukiwania. 

3. Wybierz środowisko usługi Time Series Insights z listy.

4. Wybierz **zasady dostępu do danych**, a następnie wybierz **+ Dodaj**.
    ![Zarządzanie źródłem usługi Time Series Insights — środowisko](media/iot-accelerators-remote-monitoring-rbac-tsi/getstarted-grant-data-access1.png)

5. Wybierz **wybierz użytkownika**.  Wyszukaj adres nazwy lub adresu e-mail użytkownika zlokalizować użytkownika, którego chcesz dodać. Kliknij przycisk **wybierz** aby potwierdzić wybór. 

    ![Zarządzanie źródłem usługi Time Series Insights — dodawanie](media/iot-accelerators-remote-monitoring-rbac-tsi/getstarted-grant-data-access2.png)

6. Wybierz **wybierz rolę**. Wybierz rolę odpowiedni dostęp dla użytkownika:
   - Wybierz **Współautor** Jeśli chcesz zezwolić użytkownikowi na zmianę danych referencyjnych i udziału zapisany, zapytań i perspektyw innym użytkownikom środowiska. 
   - W przeciwnym razie wybierz **czytnika** do zezwalania na zapytania o dane użytkownika w środowisku i zapisywanie osobistych zapytań (nie udostępnianych) w środowisku.

     Wybierz **Ok** aby potwierdzić wybór roli.

     ![Zarządzanie źródłem usługi Time Series Insights — wybieranie użytkownika](media/iot-accelerators-remote-monitoring-rbac-tsi/getstarted-grant-data-access3.png)

7. Wybierz **Ok** w **wybierz rolę użytkownika** strony.

    ![Zarządzanie źródłem usługi Time Series Insights — wybieranie roli](media/iot-accelerators-remote-monitoring-rbac-tsi/getstarted-grant-data-access4.png)

8. **Zasady dostępu do danych** strona zawiera listę użytkowników i rolami dla każdego użytkownika.

    ![Zarządzanie źródłem usługi Time Series Insights — wyniki](media/iot-accelerators-remote-monitoring-rbac-tsi/getstarted-grant-data-access5.png)

## <a name="next-steps"></a>Kolejne kroki

W tym artykule przedstawiono, jak kontroli dostępu są udzielane w Eksploratorze usługi Time Series Insights w akceleratora rozwiązania monitorowania zdalnego.

Aby uzyskać obszerniejszych informacji koncepcyjnych związanych akceleratora rozwiązania monitorowania zdalnego, zobacz [Architektura zdalnego monitorowania](iot-accelerators-remote-monitoring-sample-walkthrough.md)

Aby uzyskać więcej informacji na temat dostosowywania rozwiązania do zdalnego monitorowania, zobacz [dostosowywanie i ponowne wdrażanie mikrousługi](iot-accelerators-microservices-example.md)
<!-- Next tutorials in the sequence -->