---
title: Korzystanie z pulpitu nawigacyjnego Połączonej fabryki — Azure | Dokumenty firmy Microsoft
description: W tym artykule opisano, jak używać funkcji pulpitu nawigacyjnego Połączonej fabryki do monitorowania przemysłowych urządzeń IoT i zarządzania nimi.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 07/10/2018
ms.author: dobett
ms.openlocfilehash: b53177d578768428665891704269e63bd8edb09e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73820177"
---
# <a name="use-features-in-the-connected-factory-solution-accelerator-dashboard"></a>Korzystanie z funkcji na pulpicie nawigacyjnym akceleratora rozwiązań Connected Factory

Przewodnik Szybki start [Wdrażania rozwiązań chmurowych do zarządzania przemysłowymi urządzeniami IoT](quickstart-connected-factory-deploy.md) pokazał, jak poruszać się po pulpicie nawigacyjnym i reagować na alarmy. W tym przewodniku przedstawiono kilka dodatkowych funkcji pulpitu nawigacyjnego, za pomocą których można monitorować przemysłowe urządzenia IoT i zarządzać nimi.

## <a name="apply-filters"></a>Stosowanie filtrów

Informacje wyświetlane na pulpicie nawigacyjnym można filtrować w panelu **Lokalizacje fabryczne** lub w panelu **Alarmy:**

1. Kliknij ikonę **lejka**, aby wyświetlić listę dostępnych filtrów na panelu lokalizacji fabryk lub panelu alarmów.

1. Zostanie wyświetlony panel filtrów:

    [![Filtry akceleratora rozwiązania połączonej fabryki](./media/iot-accelerators-connected-factory-dashboard/filterpanel-inline.png)](./media/iot-accelerators-connected-factory-dashboard/filterpanel-expanded.png#lightbox)

1. Wybierz wymagany filtr i kliknij przycisk **Zastosuj**. Istnieje również możliwość wpisywać wolny tekst w polach filtru.

1. Filtr jest następnie stosowany. Ikona dodatkowego lejka wskazuje, że filtr jest stosowany:

    [![Zastosowany filtr akceleratora rozwiązań connected factory](./media/iot-accelerators-connected-factory-dashboard/filterapplied-inline.png)](./media/iot-accelerators-connected-factory-dashboard/filterapplied-expanded.png#lightbox)

    > [!NOTE]
    > Aktywny filtr nie wpływa na wyświetlane wartości OEE i KPI, ale filtruje tylko zawartość listy.

1. Aby wyczyścić filtr, kliknij lejek i kliknij pozycję **Wyczyść** w panelu filtru.

## <a name="browse-an-opc-ua-server"></a>Przeglądanie serwera OPC UA

Podczas wdrażania akceleratora rozwiązań automatycznie aprowizować zestaw symulowanych serwerów OPC UA, które można przeglądać z pulpitu nawigacyjnego. Symulowane serwery ułatwiają eksperymentowanie z akceleratorem rozwiązań bez konieczności wdrażania rzeczywistych serwerów.

1. Kliknij **ikonę przeglądarki** na pasku nawigacyjnym:

    [![Przeglądarka serwerów w akceleratorze rozwiązania połączonej fabryki](./media/iot-accelerators-connected-factory-dashboard/browser-inline.png)](./media/iot-accelerators-connected-factory-dashboard/browser-expanded.png#lightbox)

1. Wybierz jeden z serwerów z listy zawierającej serwery wdrożone w akceleratorze rozwiązań:

    [![Lista serwerów akceleratora rozwiązań Connected Factory](./media/iot-accelerators-connected-factory-dashboard/serverlist-inline.png)](./media/iot-accelerators-connected-factory-dashboard/serverlist-expanded.png#lightbox)

1. Kliknij przycisk **Połącz**. Zostanie wyświetlone okno dialogowe zabezpieczeń. W przypadku symulacji można bezpiecznie kliknąć przycisk **Kontynuuj**.

1. Aby rozwinąć dowolny węzeł w drzewie serwerów, kliknij go. Węzły, które publikują dane telemetryczne, mają obok nich znacznik wyboru:

    [![Drzewo serwerów w akceleratorze rozwiązania połączonej fabryki](./media/iot-accelerators-connected-factory-dashboard/servertree-inline.png)](./media/iot-accelerators-connected-factory-dashboard/servertree-expanded.png#lightbox)

1. Kliknij element prawym przyciskiem myszy, aby odczytać, zapisać, opublikować lub wywołać ten węzeł. Dostępne akcje zależą od uprawnień posiadanych przez użytkownika i atrybutów węzła. Wybranie opcji odczytu powoduje wyświetlenie panelu kontekstowego z wartością konkretnego węzła. Wybranie opcji zapisu powoduje wyświetlenie panelu kontekstowego, w którym można wprowadzić nową wartość. Wybranie opcji wywołania powoduje wyświetlenie węzła i umożliwia wprowadzenie parametrów wywołania.

## <a name="publish-a-node"></a>Publikowanie węzła

Podczas przeglądania *symulowanego serwera OPC UA* można również opublikować nowe węzły. W rozwiązaniu można przeanalizować dane telemetryczne z tych węzłów. Te *symulowane serwery OPC UA* ułatwiają eksperymentowanie z akceleratorem rozwiązań bez wdrażania rzeczywistych urządzeń:

1. W drzewie przeglądarki serwerów OPC UA przejdź do węzła, który chcesz opublikować.

1. Kliknij węzeł prawym przyciskiem myszy. Kliknij **pozycję Opublikuj**:

    [![Węzeł publikowania akceleratora rozwiązań Connected Factory](./media/iot-accelerators-connected-factory-dashboard/publishnode-inline.png)](./media/iot-accelerators-connected-factory-dashboard/publishnode-expanded.png#lightbox)

1. Wyświetlony panel kontekstowy informuje, że publikowanie zakończyło się pomyślnie. Węzeł pojawi się w widoku na poziomie stacji ze znacznikiem wyboru obok niego:

    [![Akcelerator rozwiązań Connected Factory publikuje sukces](./media/iot-accelerators-connected-factory-dashboard/publishsuccess-inline.png)](./media/iot-accelerators-connected-factory-dashboard/publishsuccess-expanded.png#lightbox)

## <a name="command-and-control"></a>Sterowanie i kontrola

Połączona fabryka umożliwia sterowanie urządzeniami przemysłowymi i kontrolowanie ich bezpośrednio z chmury. Tej funkcji można używać do reagowania na alarmy generowane przez urządzenie. Na przykład można wysłać polecenie do urządzenia, aby otworzyć zawór zwalniający ciśnienie. Dostępne polecenia można znaleźć w węźle **StationCommands** w drzewie przeglądarki serwerów OPC UA. W tym scenariuszu otwierasz zawór bezpieczeństwa na stanowisku montażowym linii produkcyjnej w Monachium. Aby korzystać z funkcji poleceń i kontroli, musisz być w roli **administratora** dla wdrożenia akceleratora rozwiązań:

1. Przejdź do węzła **StationCommands** w drzewie przeglądarki serwera OPC UA dla monachijskiej linii produkcyjnej 0, stacji montażowej.

1. Wybierz polecenie, którego chcesz użyć. Kliknij prawym przyciskiem myszy węzeł **OpenPressureReleaseValve.** Kliknij **przycisk Zadzwoń:**

    [![Polecenie wywoływania w akceleratorze rozwiązania połączonej fabryki](./media/iot-accelerators-connected-factory-dashboard/callcommand-inline.png)](./media/iot-accelerators-connected-factory-dashboard/callcommand-expanded.png#lightbox)

1. Zostanie wyświetlony panel kontekstowy informujący o tym, którą metodę chcesz wywołać i wszelkie szczegóły parametrów. Kliknij **przycisk Zadzwoń:**

    [![Parametry wywołania akceleratora rozwiązania Connected Factory](./media/iot-accelerators-connected-factory-dashboard/callpanel-inline.png)](./media/iot-accelerators-connected-factory-dashboard/callpanel-expanded.png#lightbox)

1. Panel kontekstowy zostanie zaktualizowany o informacje o powodzeniu wywołania metody. Powodzenie wywołania metody można sprawdzić, odczytując zaktualizowaną w wyniku wywołania wartość węzła ciśnienia.

    [![Powodzenie wywołania w akceleratorze rozwiązania połączonej fabryki](./media/iot-accelerators-connected-factory-dashboard/callsuccess-inline.png)](./media/iot-accelerators-connected-factory-dashboard/callsuccess-expanded.png#lightbox)

## <a name="behind-the-scenes"></a>Za kulisami

Podczas wdrażania akceleratora rozwiązania jest tworzonych wiele zasobów w ramach wybranej subskrypcji platformy Azure. Można je wyświetlić w witrynie Azure [Portal](https://portal.azure.com). W procesie wdrażania jest tworzona **grupa zasobów** o nazwie odpowiadającej nazwie akceleratora rozwiązania:

[![Grupa zasobów akceleratora rozwiązań połączonej fabryki](./media/iot-accelerators-connected-factory-dashboard/resourcegroup-inline.png)](./media/iot-accelerators-connected-factory-dashboard/resourcegroup-expanded.png#lightbox)

Aby wyświetlić ustawienia danego zasobu, wybierz go z listy w grupie zasobów.

Można również wyświetlić kod źródłowy akceleratora rozwiązań w repozytorium GitHub w fabryce azure-iot.You can also view the source code for the solution accelerator in the [azure-iot-connected-factory](https://github.com/Azure/azure-iot-connected-factory) GitHub repozytorium.

Po zakończeniu można usunąć akcelerator rozwiązań z subskrypcji platformy Azure w [witrynie azureiotsolutions.com.](https://www.azureiotsolutions.com/Accelerators#dashboard) Ta witryna umożliwia łatwe usunięcie wszystkich zasobów, które zostały aprowizowane po utworzeniu akceleratora rozwiązania.

> [!NOTE]
> Aby upewnić się, że usuniesz wszystko, co jest związane z akceleratorem rozwiązań, usuń je w [witrynie azureiotsolutions.com.](https://www.azureiotsolutions.com/Accelerators#dashboard) Nie należy usuwać grupy zasobów w portalu.

## <a name="next-steps"></a>Następne kroki

Teraz, kiedy został wdrożony akcelerator rozwiązania, który działa, możesz kontynuować poznawanie akceleratorów rozwiązań IoT, czytając następujące artykuły:

* [Konfigurowanie akceleratora rozwiązań Connected Factory](iot-accelerators-connected-factory-configure.md)
* [Uprawnienia w witrynie azureiotsolutions.com](iot-accelerators-permissions.md)
