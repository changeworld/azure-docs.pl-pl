---
title: Przewodnik po interfejsie użytkownika usługi Azure IoT Central | Microsoft Docs
description: Jako konstruktor możesz zapoznać się z kluczowymi obszarami interfejsu użytkownika usługi Azure IoT Central używanego do utworzenia rozwiązania IoT.
author: dominicbetts
ms.author: dobett
ms.date: 07/05/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: 4a0c9d16474ddf032ff88382bc240713bc734ff8
ms.sourcegitcommit: 8fea78b4521921af36e240c8a92f16159294e10a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/02/2019
ms.locfileid: "70211888"
---
# <a name="take-a-tour-of-the-azure-iot-central-ui-preview-features"></a>Skorzystaj z samouczka dotyczącego interfejsu użytkownika platformy Azure IoT Central (funkcje w wersji zapoznawczej)

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

W tym artykule przedstawiono wprowadzenie do interfejsu użytkownika usługi Microsoft Azure IoT Central. Interfejs użytkownika umożliwia tworzenie i używanie rozwiązania usługi Azure IoT Central oraz jego połączonych urządzeń, a także zarządzanie nimi.

_Konstruktor_ używa interfejsu użytkownika usługi Azure IoT Central, aby zdefiniować rozwiązanie usługi Azure IoT Central. Interfejs użytkownika umożliwia wykonywanie następujących zadań:

* Definiowanie typów urządzeń łączących się z rozwiązaniem
* Konfigurowanie reguł i akcji dla urządzeń
* Dostosowywanie interfejsu użytkownika dla _operatora_ używającego rozwiązania

_Operator_ używa interfejsu użytkownika usługi Azure IoT Central, aby zarządzać rozwiązaniem usługi Azure IoT Central. Interfejs użytkownika umożliwia wykonywanie następujących zadań:

* Monitorowanie urządzeń
* Konfigurowanie urządzeń
* Rozwiązywanie i korygowanie problemów z urządzeniami
* Aprowizacja nowych urządzeń.

## <a name="use-the-left-navigation-menu"></a>Używanie lewego menu nawigacji

Użyj menu nawigacji po lewej stronie, aby uzyskać dostęp do różnych obszarów aplikacji. Można rozwinąć lub zwinąć pasek nawigacyjny, wybierając **<** lub: **>**

:::row:::
  :::column span="":::
      ![Left navigation menu](media/overview-iot-central-tour-pnp/navigationbar.png)
  :::column-end:::
  :::column span="2":::

      **Dashboard** displays your application dashboard. As a builder, you can customize the dashboard for your operators. Users can also create their own  dashboards.
    
      **Devices** lists the simulated and real devices associated with each device template in the application. As an operator, you use the **Device Explorer** to manage your connected devices.
    
      **Device groups** lets you view and create device groups. As an operator, you can create device groups as a logical collections of devices specified by a query.

      **Rules** lets you edit rules that fire based on device telemetry and trigger customizable actions.
    
      **Analytics** shows analytics derived from device telemetry for devices and device groups. As an operator, you can create custom views on top of device data to derive insights from your application.
    
      **Jobs** enables bulk device management by having you create and run jobs to update your devices at scale.
    
      **Device templates** shows the tools a builder uses to create and manage device templates.
    
      **Data export** enables an administrator to configure a continuous export to other Azure services such as storage and queues.
    
      **Administration** shows the application administration pages where an administrator can manage application settings, users, and roles.
   :::column-end:::
:::row-end:::

## <a name="search-help-and-support"></a>Wyszukiwanie, pomoc i pomoc techniczna

Górne menu jest wyświetlane na każdej stronie:

![Pasek narzędzi](media/overview-iot-central-tour-pnp/toolbar.png)

* Aby wyszukać szablony urządzeń i urządzenia, wprowadź wartość **wyszukiwania** .
* Aby zmienić język lub motyw interfejsu użytkownika, wybierz ikonę **Ustawienia** .
* Aby wylogować się z aplikacji, wybierz ikonę **konta** .
* Aby uzyskać pomoc i pomoc techniczną, wybierz pozycję rozwijaną **Pomoc**, aby uzyskać listę zasobów. W przypadku wersji próbnej, zasoby pomocy technicznej obejmują dostęp do [rozmowy na żywo](howto-show-hide-chat.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).

Możesz wybrać jasny lub ciemny motyw interfejsu użytkownika:

![Wybieranie motywu interfejsu użytkownika](media/overview-iot-central-tour-pnp/themes.png)

> [!NOTE]
> Opcja wyboru między jasnym i ciemnym motywem nie jest dostępna, jeśli administrator skonfigurował niestandardowy motyw dla aplikacji.

## <a name="dashboard"></a>Pulpit nawigacyjny

![Pulpit nawigacyjny](media/overview-iot-central-tour-pnp/homepage.png)

* Pulpit nawigacyjny to pierwsza strona wyświetlana podczas logowania do aplikacji IoT Central platformy Azure. Jako Konstruktor możesz dostosować pulpit nawigacyjny aplikacji dla innych użytkowników, dodając kafelki. Aby dowiedzieć się więcej, zobacz samouczek [Konfigurowanie szablonu urządzenia](tutorial-define-device-type-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) .

* Jako operator można tworzyć spersonalizowane pulpity nawigacyjne i przełączać się między nimi a domyślnym pulpitem nawigacyjnym. Aby dowiedzieć się więcej, zobacz artykuł [Tworzenie osobistych pulpitów nawigacyjnych i zarządzanie nimi](howto-personalize-dashboard.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) .

## <a name="devices"></a>Urządzenia

![Strona urządzenia](media/overview-iot-central-tour-pnp/explorer.png)

Na stronie eksploratora są wyświetlane _urządzenia_ w aplikacji usługi Azure IoT Central pogrupowane według _szablonu urządzeń_.

* Szablon urządzenia definiuje typ urządzenia, który może łączyć się z aplikacją. Aby dowiedzieć się więcej, zobacz [Define a new device type in your Azure IoT Central application (Definiowanie nowego typu urządzenia w aplikacji usługi Azure IoT Central)](tutorial-define-device-type-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).
* Urządzenie reprezentuje rzeczywiste lub symulowane urządzenie w aplikacji. Aby dowiedzieć się więcej, zobacz [Add a new device to your Azure IoT Central application (Dodawanie nowego urządzenia do aplikacji usługi Azure IoT Central)](tutorial-add-device-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).

## <a name="device-groups"></a>Grupy urządzeń

![Strona grup urządzeń](media/overview-iot-central-tour-pnp/devicesets.png)

Na stronie _grupy urządzeń_ są wyświetlane grupy urządzeń utworzone przez konstruktora. Grupa urządzeń to kolekcja powiązanych urządzeń. Konstruktor definiuje zapytanie w celu zidentyfikowania urządzeń uwzględnionych w grupie urządzeń. Grupy urządzeń służą do dostosowywania analiz w aplikacji. Aby dowiedzieć się więcej, zobacz artykuł [Używanie grup urządzeń w aplikacji platformy Azure IoT Central](howto-use-device-groups-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) .

## <a name="rules"></a>Reguły

![Strona reguł](media/overview-iot-central-tour-pnp/rules.png)

Strona reguły umożliwia definiowanie reguł na podstawie danych telemetrycznych, stanu urządzenia lub zdarzeń urządzeń. Gdy reguła jest wyzwalana, może wyzwolić akcję, taką jak wysłanie wiadomości e-mail do operatora. Konstruktor używa tej strony do tworzenia reguł i zarządzania nimi. Aby uzyskać więcej informacji, zobacz samouczek [Konfigurowanie reguł i akcji dla urządzeń w usłudze Azure IoT Central](tutorial-configure-rules-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) .

## <a name="analytics"></a>Analiza

![Strona Analiza](media/overview-iot-central-tour-pnp/analytics.png)

Strona analizy przedstawia wykresy, które ułatwiają zrozumienie zachowania urządzeń połączonych z aplikacją. Operator używa tej strony do monitorowania i badania problemów z połączonymi urządzeniami. Konstruktor może zdefiniować wykresy wyświetlane na tej stronie. Aby dowiedzieć się więcej, zobacz artykuł [Create custom analytics for your Azure IoT Central application (Tworzenie niestandardowej analizy dla aplikacji usługi Azure IoT Central)](howto-use-device-groups-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).

## <a name="jobs"></a>Zadania

![Strona zadań](media/overview-iot-central-tour-pnp/jobs.png)

Strona zadania umożliwia uruchamianie zbiorczych operacji zarządzania urządzeniami na urządzeniach. Konstruktor używa tej strony do aktualizowania właściwości urządzeń, ustawień i poleceń. Aby dowiedzieć się więcej, zobacz artykuł [Uruchamianie zadania](howto-run-a-job.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).

## <a name="device-templates"></a>Szablony urządzeń

![Strona szablonów urządzeń](media/overview-iot-central-tour-pnp/templates.png)

Na stronie szablonów urządzeń konstruktor tworzy szablony urządzeń w aplikacji i zarządza nimi. Szablon urządzenia określa charakterystykę urządzenia, taką jak:

* Pomiary danych telemetrycznych, stanowych i zdarzeń.
* Aœciwoœci.
* Polecenia.

Konstruktor może również tworzyć formularze i pulpity nawigacyjne dla operatorów, które będą używane do zarządzania urządzeniami.

Aby dowiedzieć się więcej, zobacz samouczek [Define a new device type in your Azure IoT Central application (Definiowanie nowego typu urządzenia w aplikacji usługi Azure IoT Central)](tutorial-define-device-type-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).

## <a name="data-export"></a>Eksport danych

![Strona eksportu danych](media/overview-iot-central-tour-pnp/export.png)

Na stronie Eksportuj dane administrator definiuje sposób przesyłania strumieniowego danych, takich jak dane telemetryczne z aplikacji. Inne usługi mogą zapisywać wyeksportowane dane lub poddawać je analizie. Aby dowiedzieć się więcej, zobacz artykuł [Eksportowanie danych do usługi Azure IoT Central](howto-export-data.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).

## <a name="administration"></a>Administracja

![Strona Administracja](media/overview-iot-central-tour-pnp/administration.png)

Strona Administracja zawiera linki do narzędzi używanych przez administratora, takich jak definiowanie użytkowników i ról w aplikacji oraz dostosowywanie interfejsu użytkownika. Aby dowiedzieć się więcej, zobacz artykuł [Administer your Azure IoT Central application (Administrowanie aplikacją usługi Azure IoT Central)](howto-administer-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).

## <a name="next-steps"></a>Następne kroki

Po zapoznaniu się z omówieniem usługi Azure IoT Central i układem interfejsu użytkownika następnym sugerowanym krokiem jest wykonanie przewodnika Szybki start [Create an Azure IoT Central application (Tworzenie aplikacji usługi Azure IoT Central)](quick-deploy-iot-central-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).
