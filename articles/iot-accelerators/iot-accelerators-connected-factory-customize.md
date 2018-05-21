---
title: Dostosowywanie rozwiązania połączone fabryki - Azure | Dokumentacja firmy Microsoft
description: Opis sposobu dostosowywania zachowanie akcelerator rozwiązań połączone fabryki.
services: iot-suite
suite: iot-suite
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: ''
ms.service: iot-suite
ms.devlang: c#
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/14/2017
ms.author: dobett
ms.openlocfilehash: 6d3008fb92f2240c22680f1b9486067e8bd2ae88
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
---
# <a name="customize-how-the-connected-factory-solution-displays-data-from-your-opc-ua-servers"></a>Dostosuj sposób rozwiązania połączone fabryki wyświetlania danych z serwerów OPC UA

Rozwiązania połączone fabryki agreguje i wyświetla dane z serwerów OPC UA nawiązaniu połączenia z rozwiązaniem. Można wybrać i wysyłać polecenia do serwerów OPC UA w rozwiązaniu. Aby uzyskać więcej informacji na temat OPC UA, zobacz [Connected Factory FAQ (Połączona fabryka — często zadawane pytania)](iot-accelerators-faq-cf.md).

Przykładami zagregowane dane w rozwiązaniu ogólną wydajność sprzętu (OEE) oraz kluczowych wskaźników wydajności (KPI) widoczne na pulpicie nawigacyjnym poziomie fabryki, wiersza i stacji. Poniższy zrzut ekranu przedstawia wartości OEE i KPI **zestawu** stacji na **wiersza produkcyjnym 1**w **we Wrocławiu** fabryki:

![Przykładowe wartości OEE i wskaźnika KPI w rozwiązaniu][img-oee-kpi]

Rozwiązanie umożliwia wyświetlenie szczegółowych informacji z elementów określonych danych z serwerów OPC UA, nazywany *stacji*. Poniższy zrzut ekranu przedstawia powierzchni liczba wyprodukowanych elementów z określonym stacji:

![Liczba elementów wyprodukowanych wykresy][img-manufactured-items]

Kliknięcie wykresy można eksplorować danych za pomocą czasu serii Insights (TSI):

![Eksploruj dane przy użyciu czasu serii Insights][img-tsi]

W tym artykule opisano:

- Jak danych ma zostać udostępnione różne widoki w rozwiązaniu.
- Jak można dostosować sposób rozwiązania wyświetla dane.

## <a name="data-sources"></a>Źródła danych

Rozwiązania połączone fabryki wyświetla dane z serwerów OPC UA nawiązaniu połączenia z rozwiązaniem. Domyślna instalacja obejmuje kilka serwerów OPC UA systemem symulacji fabryki. Można dodać serwery OPC UA który [łączenie się za pośrednictwem bramy] [ lnk-connect-cf] do rozwiązania.

Możesz przejść elementów danych, które połączony serwer OPC Agent użytkownika może wysłać do rozwiązania na pulpicie nawigacyjnym:

1. Wybierz **przeglądarki** można przejść do **wybierz serwer OPC UA** widoku:

    ![Przejdź do wybierz Widok serwera OPC UA][img-select-server]

1. Wybierz serwer i kliknij przycisk **Connect**. Kliknij przycisk **Kontynuuj** gdy pojawi się ostrzeżenie o zabezpieczeniach.

    > [!NOTE]
    > To ostrzeżenie występuje raz dla każdego serwera i tylko ustanawia relację zaufania między pulpit nawigacyjny rozwiązania i serwer.

1. Możesz teraz przeglądać elementów danych, które serwer może wysłać do rozwiązania. Zaznaczono elementów, które są wysyłane do rozwiązania:

    ![Opublikowane elementy][img-published]

1. Jeśli jesteś *administratora* w rozwiązaniu, możesz opublikować element danych, aby była ona dostępna w rozwiązaniu fabryka połączenia. Jako Administrator możesz zmienić wartości elementów danych i wywołanie metody Server OPC UA.

## <a name="map-the-data"></a>Mapowania danych

Rozwiązania połączone fabryki mapuje i agreguje elementy opublikowanych danych z serwera OPC UA do różnych widoków w rozwiązaniu. Podczas obsługi administracyjnej rozwiązania rozwiązania połączone fabryki wdraża się do konta platformy Azure. Plik JSON w rozwiązaniu Visual Studio połączone fabryki przechowuje informacje o mapowaniu. Można wyświetlać i modyfikować tego pliku konfiguracji JSON w rozwiązaniu fabryki połączone w Visual Studio. Po wprowadzeniu zmiany można ponownie wdrożyć rozwiązanie.

Możesz użyć pliku konfiguracji do:

- Edytowanie istniejącego fabryki symulowane, linii produkcyjnych i stacji.
- Mapowanie danych rzeczywistych serwerów OPC UA, których połączenia z rozwiązaniem.

Aby uzyskać więcej informacji dotyczących mapowania i agregowanie danych do własnych wymagań, zobacz [Konfigurowanie akcelerator rozwiązań połączone fabryki ](iot-accelerators-connected-factory-configure.md).

## <a name="deploy-the-changes"></a>Wdrażanie zmiany

Po zakończeniu wprowadzania zmian do **ContosoTopologyDescription.json** pliku, należy ponownie wdrożyć rozwiązanie fabryki połączony z kontem platformy Azure.

**Azure iot — połączony fabryka** repozytorium zawiera **build.ps1** skrypt programu PowerShell, można użyć, aby ponownie skompilować i wdrożyć rozwiązanie.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o akcelerator rozwiązań połączone fabryki przeczytaj następujące artykuły:

* [Przewodnik po akceleratorze rozwiązania połączonej fabryki][lnk-rm-walkthrough]
* [Wdrażanie bramy dla fabryki połączone][lnk-connect-cf]
* [Uprawnienia w witrynie azureiotsuite.com][lnk-permissions]
* [Połączona fabryka — często zadawane pytania](iot-accelerators-faq-cf.md)
* [FAQ][lnk-faq]


[img-oee-kpi]: ./media/iot-accelerators-connected-factory-customize/oeenadkpi.png
[img-manufactured-items]: ./media/iot-accelerators-connected-factory-customize/manufactured.png
[img-tsi]: ./media/iot-accelerators-connected-factory-customize/tsi.png
[img-select-server]: ./media/iot-accelerators-connected-factory-customize/selectserver.png
[img-published]: ./media/iot-accelerators-connected-factory-customize/published.png


[lnk-rm-walkthrough]:iot-accelerators-connected-factory-sample-walkthrough.md
[lnk-connect-cf]:iot-accelerators-connected-factory-gateway-deployment.md
[lnk-permissions]: ../iot-suite/iot-suite-v1-permissions.md
[lnk-faq]: ../iot-suite/iot-suite-v1-faq.md