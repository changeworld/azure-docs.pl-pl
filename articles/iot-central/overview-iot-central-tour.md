---
title: Przewodnik po interfejsie użytkownika usługi Azure IoT Central | Microsoft Docs
description: Jako konstruktor możesz zapoznać się z kluczowymi obszarami interfejsu użytkownika usługi Azure IoT Central używanego do utworzenia rozwiązania IoT.
author: dominicbetts
ms.author: dobett
ms.date: 06/09/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: c4eba3e08e403243e76ac891d3bfcb59f12cad85
ms.sourcegitcommit: 8fea78b4521921af36e240c8a92f16159294e10a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/02/2019
ms.locfileid: "70211905"
---
# <a name="take-a-tour-of-the-azure-iot-central-ui"></a>Przewodnik po interfejsie użytkownika usługi Azure IoT Central

[!INCLUDE [iot-central-original-pnp](../../includes/iot-central-original-pnp-note.md)]

W tym artykule przedstawiono wprowadzenie do interfejsu użytkownika usługi Microsoft Azure IoT Central. Interfejs użytkownika umożliwia tworzenie i używanie rozwiązania usługi Azure IoT Central oraz jego połączonych urządzeń, a także zarządzanie nimi.

_Konstruktor_ używa interfejsu użytkownika usługi Azure IoT Central, aby zdefiniować rozwiązanie usługi Azure IoT Central. Interfejs użytkownika umożliwia wykonywanie następujących zadań:

- Definiowanie typów urządzeń łączących się z rozwiązaniem
- Konfigurowanie reguł i akcji dla urządzeń
- Dostosowywanie interfejsu użytkownika dla _operatora_ używającego rozwiązania

_Operator_ używa interfejsu użytkownika usługi Azure IoT Central, aby zarządzać rozwiązaniem usługi Azure IoT Central. Interfejs użytkownika umożliwia wykonywanie następujących zadań:

- Monitorowanie urządzeń
- Konfigurowanie urządzeń
- Rozwiązywanie i korygowanie problemów z urządzeniami
- Aprowizacja nowych urządzeń.

## <a name="use-the-left-navigation-menu"></a>Używanie lewego menu nawigacji

Użyj menu nawigacji po lewej stronie, aby uzyskać dostęp do różnych obszarów aplikacji. Można rozwinąć lub zwinąć pasek nawigacyjny, wybierając **<** lub: **>**

:::row:::
  :::column span="":::
      ![Left navigation menu](media/overview-iot-central-tour/navigationbar.png)
  :::column-end:::
  :::column span="2":::

      **Dashboard** displays your application dashboard. As a builder, you can customize the dashboard for your operators. Users can also create their own  dashboards.
    
      **Device Explorer** lists the simulated and real devices associated with each device template in the application. As an operator, you use the **Device Explorer** to manage your connected devices.
    
      **Device Sets** enables you to view and create device sets. As an operator, you can create device sets as a logical collection of devices specified by a query.
    
      **Analytics** shows analytics derived from device telemetry for devices and device sets. As an operator, you can create custom views on top of device data to derive insights from your application.
    
      **Jobs** enables bulk device management by having you create and run jobs to perform updates at scale.
    
      **Device Templates** shows the tools a builder uses to create and manage device templates.
    
      **Continuous Data Export** enables an administrator to configure a continuous export to other Azure services such as storage and queues.
    
      **Administration** shows the application administration pages where an administrator can manage application settings, users, and roles.
   :::column-end:::
:::row-end:::

## <a name="search-help-and-support"></a>Wyszukiwanie, pomoc i pomoc techniczna

Górne menu jest wyświetlane na każdej stronie:

![Pasek narzędzi](media/overview-iot-central-tour/toolbar.png)

- Aby wyszukać szablony urządzeń i urządzenia, wprowadź wartość **wyszukiwania** .
- Aby zmienić język lub motyw interfejsu użytkownika, wybierz ikonę **Ustawienia** .
- Aby wylogować się z aplikacji, wybierz ikonę **konta** .
- Aby uzyskać pomoc i pomoc techniczną, wybierz pozycję rozwijaną **Pomoc**, aby uzyskać listę zasobów. W przypadku wersji próbnej, zasoby pomocy technicznej obejmują dostęp do [rozmowy na żywo](howto-show-hide-chat.md).

Możesz wybrać jasny lub ciemny motyw interfejsu użytkownika:

![Wybieranie motywu interfejsu użytkownika](media/overview-iot-central-tour/themes.png)

> [!NOTE]
> Opcja wyboru między jasnym i ciemnym motywem nie jest dostępna, jeśli administrator skonfigurował niestandardowy motyw dla aplikacji.

## <a name="dashboard"></a>Pulpit nawigacyjny

![Pulpit nawigacyjny](media/overview-iot-central-tour/homepage.png)

* Pulpit nawigacyjny to pierwsza strona wyświetlana podczas logowania do aplikacji IoT Central platformy Azure. Jako Konstruktor możesz dostosować pulpit nawigacyjny aplikacji dla innych użytkowników, dodając kafelki. Więcej informacji można znaleźć w samouczku [Customize the Azure IoT Central operator's view (Dostosowywanie widoku operatora usługi Azure IoT Central)](tutorial-customize-operator.md).

* Jako operator można tworzyć spersonalizowane pulpity nawigacyjne i przełączać się między nimi a domyślnym pulpitem nawigacyjnym. Aby dowiedzieć się więcej, zobacz artykuł [Tworzenie osobistych pulpitów nawigacyjnych i zarządzanie nimi](howto-personalize-dashboard.md) .

## <a name="device-explorer"></a>Device Explorer

![Strona Eksplorator](media/overview-iot-central-tour/explorer.png)

Na stronie eksploratora są wyświetlane _urządzenia_ w aplikacji usługi Azure IoT Central pogrupowane według _szablonu urządzeń_.

* Szablon urządzenia definiuje typ urządzenia, który może łączyć się z aplikacją. Aby dowiedzieć się więcej, zobacz [Define a new device type in your Azure IoT Central application (Definiowanie nowego typu urządzenia w aplikacji usługi Azure IoT Central)](tutorial-define-device-type.md).
* Urządzenie reprezentuje rzeczywiste lub symulowane urządzenie w aplikacji. Aby dowiedzieć się więcej, zobacz [Add a new device to your Azure IoT Central application (Dodawanie nowego urządzenia do aplikacji usługi Azure IoT Central)](tutorial-add-device.md).

## <a name="device-sets"></a>Zestawy urządzeń

![Strona Zestawy urządzeń](media/overview-iot-central-tour/devicesets.png)

Na stronie _Zestawy urządzeń_ są wyświetlane zestawy urządzeń utworzone przez konstruktora. Zestaw urządzeń to zbiór pokrewnych urządzeń. Konstruktor definiuje zapytanie, aby zidentyfikować urządzenia w zestawie urządzeń. Zestawy urządzeń są używane podczas dostosowywania analizy w aplikacji. Aby dowiedzieć się więcej, zobacz artykuł [Use device sets in your Azure IoT Central application (Używanie zestawów urządzeń w aplikacji usługi Azure IoT Central)](howto-use-device-sets.md).

## <a name="analytics"></a>Analiza

![Strona Analiza](media/overview-iot-central-tour/analytics.png)

Strona analizy przedstawia wykresy, które ułatwiają zrozumienie zachowania urządzeń połączonych z aplikacją. Operator używa tej strony do monitorowania i badania problemów z połączonymi urządzeniami. Konstruktor może zdefiniować wykresy wyświetlane na tej stronie. Aby dowiedzieć się więcej, zobacz artykuł [Create custom analytics for your Azure IoT Central application (Tworzenie niestandardowej analizy dla aplikacji usługi Azure IoT Central)](howto-use-device-sets.md).

## <a name="jobs"></a>Zadania

![Strona zadań](media/overview-iot-central-tour/jobs.png)

Strona zadania umożliwia uruchamianie zbiorczych operacji zarządzania urządzeniami na urządzeniach. Konstruktor używa tej strony do aktualizowania właściwości urządzeń, ustawień i poleceń. Aby dowiedzieć się więcej, zobacz artykuł [Uruchamianie zadania](howto-run-a-job.md).

## <a name="device-templates"></a>Szablony urządzeń

![Strona Szablony urządzeń](media/overview-iot-central-tour/templates.png)

Na stronie szablonów urządzeń konstruktor tworzy szablony urządzeń w aplikacji i zarządza nimi. Szablon urządzenia określa charakterystykę urządzenia, taką jak:

- Pomiary danych telemetrycznych, stanowych i zdarzeń.
- Ustawienia i właściwości.
- Polecenia.
- Reguły oparte na zdarzeniach lub wartościach telemetrii.

Aby dowiedzieć się więcej, zobacz samouczek [Define a new device type in your Azure IoT Central application (Definiowanie nowego typu urządzenia w aplikacji usługi Azure IoT Central)](tutorial-define-device-type.md).

## <a name="continuous-data-export"></a>Ciągły eksport danych

![Strona Ciągły eksport danych](media/overview-iot-central-tour/export.png)

Na stronie Eksport danych ciągłych administrator definiuje sposób przesyłania strumieniowego danych, takich jak dane telemetryczne z aplikacji. Inne usługi mogą zapisywać wyeksportowane dane lub poddawać je analizie. Aby dowiedzieć się więcej, zobacz artykuł [Eksportowanie danych do usługi Azure IoT Central](howto-export-data.md).

## <a name="administration"></a>Administracja

![Strona Administracja](media/overview-iot-central-tour/administration.png)

Strona Administracja zawiera linki do narzędzi używanych przez administratora, takich jak definiowanie użytkowników i ról w aplikacji oraz dostosowywanie interfejsu użytkownika. Aby dowiedzieć się więcej, zobacz artykuł [Administer your Azure IoT Central application (Administrowanie aplikacją usługi Azure IoT Central)](howto-administer.md).

## <a name="next-steps"></a>Następne kroki

Po zapoznaniu się z omówieniem usługi Azure IoT Central i układem interfejsu użytkownika następnym sugerowanym krokiem jest wykonanie przewodnika Szybki start [Create an Azure IoT Central application (Tworzenie aplikacji usługi Azure IoT Central)](quick-deploy-iot-central.md).