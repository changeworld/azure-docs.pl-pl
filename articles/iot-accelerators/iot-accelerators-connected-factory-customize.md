---
title: Dostosowanie rozwiązania połączonej fabryki — Azure | Dokumentacja firmy Microsoft
description: Opis sposobu dostosowywania zachowania akcelerator rozwiązania połączonej fabryki.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.devlang: csharp
ms.topic: conceptual
ms.date: 12/14/2017
ms.author: dobett
ms.openlocfilehash: 6062f8b3992732e0e0f9bbdae9549e69c393f4ff
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67080485"
---
# <a name="customize-how-the-connected-factory-solution-displays-data-from-your-opc-ua-servers"></a>Dostosowywanie sposobu wyświetlania danych z serwerów OPC UA w rozwiązaniu połączonej fabryki

Rozwiązania połączonej fabryki, agreguje i wyświetla dane z serwerów OPC UA, połączonego z rozwiązaniem. Można przeglądać i wysyłanie poleceń do serwerów OPC UA w rozwiązaniu. Aby uzyskać więcej informacji na temat OPC UA, zobacz [Connected Factory FAQ (Połączona fabryka — często zadawane pytania)](iot-accelerators-faq-cf.md).

Przykładami zagregowane dane w rozwiązaniu ogólnej wydajności sprzętu (OEE) oraz kluczowych wskaźników wydajności (KPI) można wyświetlić na pulpicie nawigacyjnym poziomie fabryki, linii i stacji. Poniższy zrzut ekranu przedstawia wartości ogólnej wydajności sprzętu i kluczowych wskaźników wydajności **zestawu** stacji na **linii produkcyjnej 1**w **Monachium** fabryki:

![Przykład wartości ogólnej wydajności sprzętu i kluczowych wskaźników wydajności w rozwiązaniu][img-oee-kpi]

To rozwiązanie umożliwia wyświetlanie szczegółowych informacji z elementów określonych danych z serwerów OPC UA, o nazwie *stacji*. Poniższy zrzut ekranu przedstawia powierzchni liczby wyprodukowanych elementy z określonej stacji:

![Liczba elementów to wykresy][img-manufactured-items]

Po kliknięciu poszczególnych wykresów możesz eksplorować dane, korzystając z usługi Time Series Insights (TSI):

![Eksplorowanie danych za pomocą usługi Time Series Insights][img-tsi]

W tym artykule opisano:

- Jak danych staje się dostępny do różnych widoków w rozwiązaniu.
- W jaki sposób dostosować sposób rozwiązania Wyświetla określone dane.

## <a name="data-sources"></a>Źródła danych

Rozwiązania połączonej fabryki są wyświetlane dane z serwerów OPC UA, połączonego z rozwiązaniem. Instalacja domyślna obejmuje kilka serwerów OPC UA, uruchamiania symulacji fabryki. Można dodać własnych serwerów OPC UA, [nawiązać połączenie za pośrednictwem bramy] [lnk-connect-cf] do rozwiązania.

Możesz przeglądać elementy danych, które połączony serwer OPC UA mogą wysyłać do rozwiązania na pulpicie nawigacyjnym:

1. Wybierz **przeglądarki** można przejść do **wybierz serwer OPC UA** widoku:

    ![Przejdź do wyboru widoku serwerów OPC UA][img-select-server]

1. Wybierz serwer, a następnie kliknij przycisk **Connect**. Kliknij przycisk **Kontynuuj** kiedy pojawi się ostrzeżenie o zabezpieczeniach.

    > [!NOTE]
    > Ostrzeżenie to pojawia się jeden raz dla każdego serwera i tylko ustanawia relację zaufania między pulpit nawigacyjny rozwiązania a serwerem.

1. Możesz teraz przeglądać elementy danych, które serwer może wysłać do rozwiązania. Zaznaczono elementów, które są wysyłane do rozwiązania:

    ![Opublikowane elementy][img-published]

1. Jeśli jesteś *administratora* w rozwiązaniu, użytkownik może opublikować elementu danych, aby udostępnić ją w rozwiązaniu połączonej fabryki. Jako Administrator możesz zmienić wartości elementów danych i wywoływać metody w serwera OPC UA.

## <a name="map-the-data"></a>Mapowania danych

Rozwiązania połączonej fabryki mapuje i agreguje elementy opublikowanych danych z serwerem OPC UA do różnych widoków w rozwiązaniu. Podczas aprowizacji rozwiązania rozwiązania połączonej fabryki wdraża się do konta platformy Azure. Plik w formacie JSON w rozwiązaniu Visual Studio Connected Factory przechowuje informacje o mapowaniu. Można wyświetlać i modyfikować tego pliku konfiguracji JSON w rozwiązaniu połączona fabryka programu Visual Studio. Po wprowadzeniu zmiany można ponownie wdrożyć rozwiązanie.

Można użyć pliku konfiguracji, aby:

- Edytuj istniejący symulowanej fabryki, linii produkcyjnych i stacji.
- Dane mapy z prawdziwych serwerów OPC UA, z którymi się łączysz się z rozwiązaniem.

Aby uzyskać więcej informacji na temat mapowania i agregowanie danych pod kątem wymagań dotyczących zobacz [sposobu konfigurowania akcelerator rozwiązania połączonej fabryki ](iot-accelerators-connected-factory-configure.md).

## <a name="deploy-the-changes"></a>Wdrażanie zmiany

Po zakończeniu wprowadzania zmian do **ContosoTopologyDescription.json** pliku, należy ponownie wdrożyć rozwiązania połączonej fabryki z kontem platformy Azure.

**Azure-iot-connected-factory** repozytorium zawiera **build.ps1** skrypt programu PowerShell, można użyć, aby ponownie skompilować i wdrożyć rozwiązanie.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o akcelerator rozwiązania połączonej fabryki, czytając następujące artykuły:

* [Uprawnienia w witrynie azureiotsolutions.com][lnk-permissions]
* [Połączona fabryka — często zadawane pytania](iot-accelerators-faq-cf.md)
* [FAQ][lnk-faq]


[img-oee-kpi]: ./media/iot-accelerators-connected-factory-customize/oeenadkpi.png
[img-manufactured-items]: ./media/iot-accelerators-connected-factory-customize/manufactured.png
[img-tsi]: ./media/iot-accelerators-connected-factory-customize/tsi.png
[img-select-server]: ./media/iot-accelerators-connected-factory-customize/selectserver.png
[img-published]: ./media/iot-accelerators-connected-factory-customize/published.png


[lnk-permissions]: iot-accelerators-permissions.md
[lnk-faq]: iot-accelerators-faq.md