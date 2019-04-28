---
title: Pulpit nawigacyjny połączonej fabryki — Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak korzystać z funkcji Pulpit nawigacyjny połączonej fabryki.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 07/10/2018
ms.author: dobett
ms.openlocfilehash: 05befc72f512f502456f798f25b6c72571451363
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61450984"
---
# <a name="use-features-in-the-connected-factory-solution-accelerator-dashboard"></a>Używanie funkcji na pulpicie nawigacyjnym akcelerator rozwiązania połączonej fabryki

[Wdrożyć rozwiązanie oparte na chmurze do zarządzania urządzeniami IoT, Moje przemysłowych](quickstart-connected-factory-deploy.md) Szybki Start pokazuje, jak nawigować pulpitu nawigacyjnego i reagowania na alarmy. Ten poradnik pokazuje niektóre funkcje dodatkowe pulpit nawigacyjny, który umożliwia monitorowanie i zarządzanie urządzeniami IoT przemysłowych.

## <a name="apply-filters"></a>Stosowanie filtrów

Można filtrować dane wyświetlane na pulpicie nawigacyjnym albo w **lokalizacje fabryk** panel lub **alarmy** panelu:

1. Kliknij ikonę **lejka**, aby wyświetlić listę dostępnych filtrów na panelu lokalizacji fabryk lub panelu alarmów.

1. Poniżej przedstawiono Panel filtrów:

    [![Połączonej fabryki rozwiązania akceleratora filtry](./media/iot-accelerators-connected-factory-dashboard/filterpanel-inline.png)](./media/iot-accelerators-connected-factory-dashboard/filterpanel-expanded.png#lightbox)

1. Wybierz filtr, który wymaga i kliknij przycisk **Zastosuj**. Użytkownik może również wpisać dowolny tekst w polach filtrów.

1. Następnie jest stosowany filtr. Ikona lejka dodatkowe wskazuje, że filtr jest stosowany:

    [![Połączonej fabryki rozwiązania akceleratora zastosowano filtr](./media/iot-accelerators-connected-factory-dashboard/filterapplied-inline.png)](./media/iot-accelerators-connected-factory-dashboard/filterapplied-expanded.png#lightbox)

    > [!NOTE]
    > Aktywny filtr nie ma wpływu na wyświetlane wartości ogólnej wydajności sprzętu i kluczowych wskaźników wydajności, filtrowana jest tylko zawartość listy.

1. Aby wyczyścić filtr, kliknij ikonę lejka, a następnie kliknij przycisk **wyczyść** w panelu Filtr.

## <a name="browse-an-opc-ua-server"></a>Przeglądanie serwera OPC UA

Podczas wdrażania akceleratora rozwiązania automatycznie aprowizujesz zestaw symulowanych serwerów OPC UA, które można przeglądać z poziomu pulpitu nawigacyjnego. Symulowane serwery ułatwiają eksperymentowanie z akceleratora rozwiązań bez konieczności wdrażania serwerów rzeczywistych. Jeśli chcesz połączyć prawdziwy serwer OPC UA z rozwiązaniem, zobacz [łączenie urządzenia OPC UA do akcelerator rozwiązania połączonej fabryki](iot-accelerators-connected-factory-gateway-deployment.md) samouczka.

1. Kliknij przycisk **ikonę przeglądarki** na pasku nawigacyjnym pulpitu nawigacyjnego:

    [![Przeglądarki serwera akcelerator rozwiązania połączonej fabryki](./media/iot-accelerators-connected-factory-dashboard/browser-inline.png)](./media/iot-accelerators-connected-factory-dashboard/browser-expanded.png#lightbox)

1. Wybierz jeden z serwerów z listy, która zawiera serwery wdrożone dla Ciebie w akcelerator rozwiązań:

    [![Lista serwerów akcelerator rozwiązania połączonej fabryki](./media/iot-accelerators-connected-factory-dashboard/serverlist-inline.png)](./media/iot-accelerators-connected-factory-dashboard/serverlist-expanded.png#lightbox)

1. Kliknij przycisk **Połącz**. Zostanie wyświetlone okno dialogowe zabezpieczeń. Symulacji, jest bezpiecznie kliknąć pozycję **Kontynuuj**.

1. Aby rozwinąć dowolny węzeł w drzewie serwerów, kliknij go. Węzły, które publikują dane telemetryczne zaznaczyć pole wyboru obok nich:

    [![Drzewo serwerów akcelerator rozwiązania połączonej fabryki](./media/iot-accelerators-connected-factory-dashboard/servertree-inline.png)](./media/iot-accelerators-connected-factory-dashboard/servertree-expanded.png#lightbox)

1. Kliknij element prawym przyciskiem myszy, aby odczytać, zapisać, opublikować lub wywołać ten węzeł. Dostępne akcje zależą od uprawnień posiadanych przez użytkownika i atrybutów węzła. Wybranie opcji odczytu powoduje wyświetlenie panelu kontekstowego z wartością konkretnego węzła. Wybranie opcji zapisu powoduje wyświetlenie panelu kontekstowego, w którym można wprowadzić nową wartość. Wybranie opcji wywołania powoduje wyświetlenie węzła i umożliwia wprowadzenie parametrów wywołania.

## <a name="publish-a-node"></a>Publikowanie węzła

Podczas przeglądania *symulowanego serwera OPC UA* można również opublikować nowe węzły. W rozwiązaniu można przeanalizować dane telemetryczne z tych węzłów. Te *symulowane serwery OPC UA* ułatwiają eksperymentowanie z akceleratora rozwiązań bez konieczności wdrażania prawdziwych urządzeń:

1. W drzewie przeglądarki serwerów OPC UA przejdź do węzła, który chcesz opublikować.

1. Kliknij węzeł prawym przyciskiem myszy. Kliknij przycisk **publikowania**:

    [![Akcelerator rozwiązań usługi połączonej fabryki publikowanie węzła](./media/iot-accelerators-connected-factory-dashboard/publishnode-inline.png)](./media/iot-accelerators-connected-factory-dashboard/publishnode-expanded.png#lightbox)

1. Wyświetlony panel kontekstowy informuje, że publikowanie zakończyło się pomyślnie. Węzeł jest wyświetlany w widoku poziomu stacji znacznik wyboru obok niej:

    [![Akcelerator rozwiązań usługi połączonej fabryki publikowanie zakończyło się powodzeniem](./media/iot-accelerators-connected-factory-dashboard/publishsuccess-inline.png)](./media/iot-accelerators-connected-factory-dashboard/publishsuccess-expanded.png#lightbox)

## <a name="command-and-control"></a>Sterowanie i kontrola

Połączona fabryka umożliwia sterowanie urządzeniami przemysłowymi i kontrolowanie ich bezpośrednio z chmury. Tej funkcji można używać do reagowania na alarmy generowane przez urządzenie. Na przykład można wysłać polecenia do urządzenia, aby otworzyć zawór bezpieczeństwa. Dostępne polecenia można znaleźć w węźle **StationCommands** w drzewie przeglądarki serwerów OPC UA. W tym scenariuszu otwierasz zawór bezpieczeństwa na stanowisku montażowym linii produkcyjnej w Monachium. Aby korzystać z funkcji poleceń i kontroli, użytkownik musi być w **administratora** rola dla wdrożenia akcelerator rozwiązań:

1. Przejdź do **StationCommands** węzeł w drzewie przeglądarki serwerów OPC UA Monachium, linia produkcyjna 0, stacji montażowej.

1. Wybierz polecenie, którego chcesz użyć. Kliknij prawym przyciskiem myszy **OpenPressureReleaseValve** węzła. Kliknij przycisk **wywołania**:

    [![Połączonej fabryki rozwiązania akceleratora wywołań — polecenie](./media/iot-accelerators-connected-factory-dashboard/callcommand-inline.png)](./media/iot-accelerators-connected-factory-dashboard/callcommand-expanded.png#lightbox)

1. Wyświetlony panel kontekstowy informujące, jakiej metody, które masz zamiar wywołania i szczegółami parametru. Kliknij przycisk **wywołania**:

    [![Połączonej fabryki rozwiązania akceleratora wywołania parametry](./media/iot-accelerators-connected-factory-dashboard/callpanel-inline.png)](./media/iot-accelerators-connected-factory-dashboard/callpanel-expanded.png#lightbox)

1. Panel kontekstowy zostanie zaktualizowany o informacje o powodzeniu wywołania metody. Powodzenie wywołania metody można sprawdzić, odczytując zaktualizowaną w wyniku wywołania wartość węzła ciśnienia.

    [![Powodzenie wywołania akcelerator rozwiązania połączonej fabryki](./media/iot-accelerators-connected-factory-dashboard/callsuccess-inline.png)](./media/iot-accelerators-connected-factory-dashboard/callsuccess-expanded.png#lightbox)

## <a name="behind-the-scenes"></a>Za kulisami

Podczas wdrażania akceleratora rozwiązania jest tworzonych wiele zasobów w ramach wybranej subskrypcji platformy Azure. Można wyświetlić te zasoby na platformie Azure [portal](https://portal.azure.com). W procesie wdrażania jest tworzona **grupa zasobów** o nazwie odpowiadającej nazwie akceleratora rozwiązania:

[![Grupy zasobów akcelerator rozwiązania połączonej fabryki](./media/iot-accelerators-connected-factory-dashboard/resourcegroup-inline.png)](./media/iot-accelerators-connected-factory-dashboard/resourcegroup-expanded.png#lightbox)

Aby wyświetlić ustawienia danego zasobu, wybierz go z listy w grupie zasobów.

Można również wyświetlić kod źródłowy dla akceleratora rozwiązań w [azure-iot-connected-factory](https://github.com/Azure/azure-iot-connected-factory) repozytorium GitHub.

Gdy wszystko będzie gotowe, możesz usunąć akcelerator rozwiązań z subskrypcji platformy Azure na [azureiotsolutions.com](https://www.azureiotsolutions.com/Accelerators#dashboard) lokacji. Ta witryna umożliwia łatwe usunięcie wszystkich zasobów, które zostały aprowizowane po utworzeniu akceleratora rozwiązania.

> [!NOTE]
> Aby upewnić się, że zostaną usunięte wszystkie elementy powiązane ze akcelerator rozwiązań, Usuń rozwiązanie [azureiotsolutions.com](https://www.azureiotsolutions.com/Accelerators#dashboard) lokacji. Nie należy usuwać grupy zasobów w portalu.

## <a name="next-steps"></a>Kolejne kroki

Teraz, kiedy został wdrożony akcelerator rozwiązania, który działa, możesz kontynuować poznawanie akceleratorów rozwiązań IoT, czytając następujące artykuły:

* [Połączone Przewodnik po akceleratorze rozwiązań fabryki](iot-accelerators-connected-factory-sample-walkthrough.md)
* [Podłączanie urządzenia do akceleratora rozwiązania połączonej fabryki](iot-accelerators-connected-factory-gateway-deployment.md)
* [Uprawnienia w witrynie azureiotsolutions.com](iot-accelerators-permissions.md)
