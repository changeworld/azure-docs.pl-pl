---
title: Kontrola dostępu do danych zdalnego monitorowania — Platforma Azure | Dokumenty firmy Microsoft
description: Ten artykuł zawiera informacje dotyczące sposobu konfigurowania kontrolek dostępu dla programu telemetrycznego usługi Time Series Insights w akceleratorze rozwiązań do zdalnego monitorowania
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 08/06/2018
ms.topic: conceptual
ms.openlocfilehash: 9d5d572c3e32e3645e65ba8d6fc28b567b3c1e9a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "65827211"
---
# <a name="configure-access-controls-for-the-time-series-insights-telemetry-explorer"></a>Konfigurowanie kontrolek dostępu dla eksploratora telemetrii usługi Time Series Insights

Ten artykuł zawiera informacje dotyczące konfigurowania formantów dostępu dla eksploratora Usługi Time Series Insights w akceleratorze rozwiązań do zdalnego monitorowania. Aby umożliwić użytkownikom akceleratora rozwiązań dostęp do Eksploratora Usługi Time Series Insights, należy udzielić każdemu dostępowi do danych użytkownika.

Zasady dostępu do danych umożliwiają przyznawanie uprawnień do wysyłania zapytań dotyczących danych, manipulowania danymi referencyjnymi w środowisku oraz udostępniania zapisanych zapytań i perspektyw skojarzonych ze środowiskiem.

## <a name="grant-data-access"></a>Przyznawanie dostępu do danych

Wykonaj następujące kroki, aby udzielić dostępu do danych dla podmiotu zabezpieczeń użytkownika:

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).

2. Znajdź środowisko usługi Time Series Insights. Wpisz **szeregi czasowe** w polu **wyszukiwania.** Wybierz **środowisko szeregów czasowych** w wynikach wyszukiwania. 

3. Wybierz środowisko usługi Time Series Insights z listy.

4. Wybierz pozycję **Zasady dostępu do danych**, a następnie wybierz pozycję + **Dodaj**.
    ![Zarządzanie źródłem usługi Time Series Insights — środowisko](media/iot-accelerators-remote-monitoring-rbac-tsi/getstarted-grant-data-access1.png)

5. Wybierz **wybierz wybierz użytkownika**.  Wyszukaj nazwę użytkownika lub adres e-mail, aby zlokalizować użytkownika, którego chcesz dodać. Kliknij **przycisk Wybierz,** aby potwierdzić wybór. 

    ![Zarządzanie źródłem usługi Time Series Insights — dodawanie](media/iot-accelerators-remote-monitoring-rbac-tsi/getstarted-grant-data-access2.png)

6. Wybierz **pozycję Wybierz rolę**. Wybierz odpowiednią rolę dostępu dla użytkownika:
   - Wybierz **współautora,** jeśli chcesz zezwolić użytkownikowi na zmianę danych referencyjnych i udostępnianie zapisanych zapytań i perspektyw innym użytkownikom środowiska. 
   - W przeciwnym razie wybierz **program Reader,** aby zezwolić na dane zapytań użytkownika w środowisku i zapisać osobiste (nieuwzdłe) zapytania w środowisku.

     Wybierz **przycisk Ok,** aby potwierdzić wybór roli.

     ![Zarządzanie źródłem usługi Time Series Insights — wybieranie użytkownika](media/iot-accelerators-remote-monitoring-rbac-tsi/getstarted-grant-data-access3.png)

7. Wybierz **pozycję Ok** na stronie Wybierz rolę **użytkownika.**

    ![Zarządzanie źródłem usługi Time Series Insights — wybieranie roli](media/iot-accelerators-remote-monitoring-rbac-tsi/getstarted-grant-data-access4.png)

8. Na stronie **Zasady dostępu do danych** są wyszczególniane użytkowników i role dla każdego użytkownika.

    ![Zarządzanie źródłem usługi Time Series Insights — wyniki](media/iot-accelerators-remote-monitoring-rbac-tsi/getstarted-grant-data-access5.png)

## <a name="next-steps"></a>Następne kroki

W tym artykule dowiesz się, jak formanty dostępu są przyznawane dla Eksploratora usługi Time Series Insights w akceleratorze rozwiązań zdalnego monitorowania.

Aby uzyskać więcej informacji koncepcyjnych dotyczących akceleratora rozwiązań do zdalnego monitorowania, zobacz [Architektura zdalnego monitorowania](iot-accelerators-remote-monitoring-sample-walkthrough.md)

Aby uzyskać więcej informacji na temat dostosowywania rozwiązania do zdalnego monitorowania, zobacz [Dostosowywanie i ponowne wdrożenie mikrousług](iot-accelerators-microservices-example.md)
<!-- Next tutorials in the sequence -->