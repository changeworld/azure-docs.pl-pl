---
title: Dostosowywanie rozwiązania Połączonej fabryki — Azure | Dokumenty firmy Microsoft
description: Opis sposobu dostosowywania zachowania akceleratora rozwiązań połączonej fabryki.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.devlang: csharp
ms.topic: conceptual
ms.date: 12/14/2017
ms.author: dobett
ms.openlocfilehash: 6062f8b3992732e0e0f9bbdae9549e69c393f4ff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67080485"
---
# <a name="customize-how-the-connected-factory-solution-displays-data-from-your-opc-ua-servers"></a>Dostosowywanie sposobu wyświetlania danych z serwerów OPC UA w rozwiązaniu Connected Factory

Rozwiązanie Connected Factory agreguje i wyświetla dane z serwerów OPC UA podłączonych do rozwiązania. W rozwiązaniu można przeglądać i wysyłać polecenia do serwerów OPC UA. Aby uzyskać więcej informacji na temat OPC UA, zobacz [często zadawane pytania dotyczące połączonej fabryki](iot-accelerators-faq-cf.md).

Przykłady zagregowanych danych w rozwiązaniu obejmują ogólne wskaźniki wydajności sprzętu (OEE) i kluczowe wskaźniki wydajności (KPI), które można wyświetlić na pulpicie nawigacyjnym na poziomie fabrycznym, liniowym i stacji. Poniższy zrzut ekranu przedstawia wartości OEE i KPI dla stacji **montażowej** w **linii produkcyjnej 1**w fabryce w **Monachium:**

![Przykład wartości OEE i KPI w rozwiązaniu][img-oee-kpi]

Rozwiązanie umożliwia wyświetlanie szczegółowych informacji z określonych elementów danych z serwerów OPC UA, zwanych *stacjami.* Poniższy zrzut ekranu przedstawia wykresy liczby wyprodukowanych towarów z określonej stacji:

![Wykresy liczby wyprodukowanych towarów][img-manufactured-items]

Po kliknięciu jednego z wykresów można eksplorować dane za pomocą usługi Time Series Insights (TSI):

![Eksplorowanie danych przy użyciu usługi Time Series Insights][img-tsi]

W tym artykule opisano:

- Sposób udostępniania danych do różnych widoków w rozwiązaniu.
- Jak można dostosować sposób, w jaki rozwiązanie wyświetla dane.

## <a name="data-sources"></a>Źródła danych

Rozwiązanie Connected Factory wyświetla dane z serwerów OPC UA podłączonych do rozwiązania. Instalacja domyślna obejmuje kilka serwerów OPC UA z symulacją fabryczną. Do rozwiązania można dodać własne serwery OPC UA, które [łączą się za pośrednictwem bramy][lnk-connect-cf].

Można przeglądać elementy danych, które podłączony serwer OPC UA może wysłać do rozwiązania na pulpicie nawigacyjnym:

1. Wybierz **pozycję Przeglądarka,** aby przejść do widoku **Wybierz serwer UA OPC:**

    ![Przechodzenie do widoku Wybierz serwer UA OPC][img-select-server]

1. Wybierz serwer i kliknij przycisk **Połącz**. Po wyświetleniu ostrzeżenia o zabezpieczeniach kliknij przycisk **Kontynuuj.**

    > [!NOTE]
    > To ostrzeżenie pojawia się tylko raz dla każdego serwera i ustanawia relację zaufania między pulpitem nawigacyjnym rozwiązania a serwerem.

1. Teraz można przeglądać elementy danych, które serwer może wysłać do rozwiązania. Elementy, które są wysyłane do rozwiązania mają znacznik wyboru:

    ![Opublikowane elementy][img-published]

1. Jeśli jesteś *administratorem* w rozwiązaniu, możesz opublikować element danych, aby udostępnić go w rozwiązaniu Połączonego systemu fabrycznego. Jako administrator możesz również zmienić wartość elementów danych i metod wywołania na serwerze OPC UA.

## <a name="map-the-data"></a>Mapowanie danych

Rozwiązanie Połączonej fabryki mapuje i agreguje opublikowane elementy danych z serwera OPC UA do różnych widoków w rozwiązaniu. Rozwiązanie Połączonej fabryki wdraża na koncie platformy Azure podczas inicjowania obsługi administracyjnej rozwiązania. Plik JSON w rozwiązaniu połączonej fabryki programu Visual Studio przechowuje te informacje mapowania. Można wyświetlić i zmodyfikować ten plik konfiguracyjny JSON w rozwiązaniu Connected Factory Visual Studio. Po dokonaniu zmiany można ponownie wdrożyć rozwiązanie.

Za pomocą pliku konfiguracyjnego można:

- Edytuj istniejące symulowane fabryki, linie produkcyjne i stacje.
- Mapuj dane z prawdziwych serwerów OPC UA, które łączysz z rozwiązaniem.

Aby uzyskać więcej informacji na temat mapowania i agregowania danych w celu spełnienia określonych wymagań, zobacz [Jak skonfigurować akcelerator rozwiązań Connected Factory ](iot-accelerators-connected-factory-configure.md).

## <a name="deploy-the-changes"></a>Wdrażanie zmian

Po zakończeniu wprowadzania zmian w pliku **ContosoTopologyDescription.json** należy ponownie wdrożyć rozwiązanie połączonego systemu fabrycznego na koncie platformy Azure.

Repozytorium **fabryki azure-iot-connected zawiera** skrypt **programu PowerShell build.ps1,** którego można użyć do odbudowy i wdrożenia rozwiązania.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o akceleratorze rozwiązań Connected Factory, czytając następujące artykuły:

* [Uprawnienia w witrynie azureiotsolutions.com][lnk-permissions]
* [Najczęściej zadawane pytania dotyczące połączonej fabryki](iot-accelerators-faq-cf.md)
* [Najczęściej zadawane pytania][lnk-faq]


[img-oee-kpi]: ./media/iot-accelerators-connected-factory-customize/oeenadkpi.png
[img-manufactured-items]: ./media/iot-accelerators-connected-factory-customize/manufactured.png
[img-tsi]: ./media/iot-accelerators-connected-factory-customize/tsi.png
[img-select-server]: ./media/iot-accelerators-connected-factory-customize/selectserver.png
[img-published]: ./media/iot-accelerators-connected-factory-customize/published.png


[lnk-permissions]: iot-accelerators-permissions.md
[lnk-faq]: iot-accelerators-faq.md