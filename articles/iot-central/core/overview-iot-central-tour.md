---
title: Przewodnik po interfejsie użytkownika usługi Azure IoT Central | Microsoft Docs
description: Zapoznaj się z kluczowymi obszarami interfejsu użytkownika centrali Azure IoT, których używasz do tworzenia rozwiązania IoT, zarządzania nim i korzystania z niego.
author: lmasieri
ms.author: lmasieri
ms.date: 12/09/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: corywink
ms.openlocfilehash: b905b1e86810b25c4c94072d6cd414b993e2a883
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77426194"
---
# <a name="take-a-tour-of-the-azure-iot-central-ui"></a>Przewodnik po interfejsie użytkownika usługi Azure IoT Central



W tym artykule przedstawiono wprowadzenie do interfejsu użytkownika usługi Microsoft Azure IoT Central. Interfejs użytkownika umożliwia tworzenie i używanie rozwiązania usługi Azure IoT Central oraz jego połączonych urządzeń, a także zarządzanie nimi.

Jako _konstruktor rozwiązań_użyj centralnej interfejsu użytkownika usługi Azure IoT do zdefiniowania rozwiązania Usługi Azure IoT Central. Interfejs użytkownika umożliwia wykonywanie następujących zadań:

* Definiowanie typów urządzeń łączących się z rozwiązaniem
* Konfigurowanie reguł i akcji dla urządzeń 
* Dostosowywanie interfejsu użytkownika dla _operatora_ używającego rozwiązania

_Operator_ używa interfejsu użytkownika usługi Azure IoT Central, aby zarządzać rozwiązaniem usługi Azure IoT Central. Interfejs użytkownika umożliwia wykonywanie następujących zadań:

* Monitorowanie urządzeń
* Konfigurowanie urządzeń
* Rozwiązywanie i korygowanie problemów z urządzeniami
* Aprowizacja nowych urządzeń.

## <a name="iot-central-homepage"></a>Strona główna IoT Central

Strona [główna IoT Central](https://aka.ms/iotcentral-get-started) to miejsce, w którym możesz dowiedzieć się więcej o najnowszych wiadomościach i funkcjach dostępnych w UIOT Central, tworzyć nowe aplikacje oraz wyświetlać i uruchamiać istniejącą aplikację.

> [!div class="mx-imgBorder"]
> ![Strona główna IoT Central](media/overview-iot-central-tour/iot-central-homepage-pnp.png)

### <a name="create-an-application"></a>Tworzenie aplikacji

W sekcji Kompilacja możesz przeglądać listę szablonów IoT Central odpowiednich dla branży, aby ułatwić szybkie rozpoczęcie pracy, lub zacząć od zera przy użyciu niestandardowego szablonu aplikacji.  
> [!div class="mx-imgBorder"]
> ![Strona kompilacji IoT Central](media/overview-iot-central-tour/iot-central-build-pnp.png)

Aby dowiedzieć się więcej, zobacz Szybki start [aplikacji Tworzenie aplikacji Azure IoT Central.](quick-deploy-iot-central.md)

### <a name="launch-your-application"></a>Uruchom aplikację

Aplikację IoT Central można uruchomić, przechodząc do adresu URL wybranego przez użytkownika lub konstruktora rozwiązań podczas tworzenia aplikacji. Listę wszystkich aplikacji, do których masz dostęp, można również wyświetlić w [menedżerze aplikacji IoT Central](https://aka.ms/iotcentral-apps).

> [!div class="mx-imgBorder"]
> ![Menedżer aplikacji IoT Central](media/overview-iot-central-tour/app-manager-pnp.png)

## <a name="navigate-your-application"></a>Poruszanie się po aplikacji

Gdy znajdziesz się w aplikacji IoT, użyj lewego okienka, aby uzyskać dostęp do różnych obszarów. Lewe okienko można rozwinąć lub zwinąć, zaznaczając ikonę z trzema wyściełami na górze okienka:

> [!NOTE]
> Elementy widoczne w lewym okienku zależą od roli użytkownika. Dowiedz się więcej o [zarządzaniu użytkownikami i rolami](howto-manage-users-roles.md). 

:::row:::
  :::column span="":::
      > [!div class="mx-imgBorder"]
      > ![lewe okienko](media/overview-iot-central-tour/navigationbar-pnp.png)
  :::column-end:::
  :::column span="2":::
     **Na pulpicie nawigacyjnym** aplikacji zostanie wyświetlony pulpit nawigacyjny aplikacji. Jako *konstruktor rozwiązań*możesz dostosować globalny pulpit nawigacyjny dla operatorów. W zależności od roli użytkownika operatorzy mogą również tworzyć własne osobiste pulpity nawigacyjne.
     
     **Urządzenia** umożliwiają zarządzanie podłączonymi urządzeniami — rzeczywistymi i symulowanymi.

     **Grupy urządzeń** umożliwiają wyświetlanie i tworzenie logicznych kolekcji urządzeń określonych przez kwerendę. Można zapisać tę kwerendę i użyć grup urządzeń za pośrednictwem aplikacji do wykonywania operacji zbiorczych.

     **Reguły** umożliwiają tworzenie i edytowanie reguł w celu monitorowania urządzeń. Reguły są oceniane na podstawie danych telemetrycznych urządzenia i wyzwalania konfigurowalnych akcji.

     **Analytics** umożliwia tworzenie widoków niestandardowych na podstawie danych urządzenia w celu uzyskania szczegółowych informacji z aplikacji.

     **Zadania** umożliwiają zarządzanie urządzeniami na dużą skalę przez uruchamianie operacji zbiorczych.

     **Szablony urządzeń** to miejsce, w którym można tworzyć i zarządzać charakterystyką urządzeń, które łączą się z aplikacją.

     **Eksport danych** umożliwia skonfigurowanie ciągłego eksportu do usług zewnętrznych , takich jak magazyn i kolejki.

     **Administracja** to miejsce, w którym można zarządzać ustawieniami aplikacji, dostosowywaniem, rozliczeniami, użytkownikami i rolami.

     **IoT Central** umożliwia *administratorom* powrót do menedżera aplikacji usługi IoT Central.
     
   :::column-end:::
:::row-end:::

### <a name="search-help-theme-and-support"></a>Wyszukiwanie, pomoc, motyw i pomoc techniczna

Górne menu jest wyświetlane na każdej stronie:

> [!div class="mx-imgBorder"]
> ![Pasek narzędzi](media/overview-iot-central-tour/toolbar-pnp.png)

* Aby wyszukać szablony urządzeń i urządzenia, wprowadź wartość **wyszukiwania.**
* Aby zmienić język interfejsu użytkownika lub motyw, wybierz ikonę **Ustawienia.** Dowiedz się więcej o [zarządzaniu preferencjami aplikacji](howto-manage-preferences.md)
* Aby wylogować się z aplikacji, wybierz ikonę **Konto.**
* Aby uzyskać pomoc i pomoc techniczną, wybierz pozycję rozwijaną **Pomoc**, aby uzyskać listę zasobów. W aplikacji na bezpłatny plan cenowy, zasoby pomocy technicznej obejmują dostęp do [czatu na żywo](howto-show-hide-chat.md).

Możesz wybrać jasny lub ciemny motyw interfejsu użytkownika:

> [!NOTE]
> Opcja wyboru między jasnymi i ciemnymi motywami nie jest dostępna, jeśli administrator skonfigurował niestandardowy motyw dla aplikacji.

> [!div class="mx-imgBorder"]
> ![Wybieranie motywu interfejsu użytkownika](media/overview-iot-central-tour/themes-pnp.png)

### <a name="dashboard"></a>Pulpit nawigacyjny
> [!div class="mx-imgBorder"]
> ![Pulpit nawigacyjny](media/overview-iot-central-tour/dashboard-pnp.png)

* Pulpit nawigacyjny jest pierwszą stroną, którą widzisz po zalogowaniu się do aplikacji Usługi Azure IoT Central. Jako *konstruktor rozwiązań*możesz tworzyć i dostosowywać wiele globalnych pulpitów nawigacyjnych aplikacji dla innych użytkowników. Dowiedz się więcej o [dodawaniu kafelków do pulpitu nawigacyjnego](howto-add-tiles-to-your-dashboard.md)

* Jako *operator*, jeśli twoja rola użytkownika na to pozwala, możesz tworzyć osobiste pulpity nawigacyjne, aby monitorować, na czym Ci zależy. Aby dowiedzieć się więcej, zobacz [tworzenie osobistych pulpitów nawigacyjnych usługi Azure IoT Central.](howto-create-personal-dashboards.md)

### <a name="devices"></a>Urządzenia

> [!div class="mx-imgBorder"]
> ![Strona Urządzenia](media/overview-iot-central-tour/devices-pnp.png)

Na stronie eksploratora są wyświetlane _urządzenia_ w aplikacji usługi Azure IoT Central pogrupowane według _szablonu urządzeń_. 

* Szablon urządzenia definiuje typ urządzenia, który może łączyć się z aplikacją.
* Urządzenie reprezentuje rzeczywiste lub symulowane urządzenie w aplikacji.

Aby dowiedzieć się więcej, zobacz Szybki start [monitoruj swoje urządzenia.](./quick-monitor-devices.md) 

### <a name="device-groups"></a>Device groups

> [!div class="mx-imgBorder"]
> ![Strona Grupy urządzeń](media/overview-iot-central-tour/device-groups-pnp.png)

Grupa urządzeń to zbiór powiązanych urządzeń. *Konstruktor rozwiązań* definiuje kwerendę w celu zidentyfikowania urządzeń, które znajdują się w grupie urządzeń. Grupy urządzeń są używane do wykonywania operacji zbiorczych w aplikacji. Aby dowiedzieć się więcej, zobacz [użyj grup urządzeń w aplikacji Azure IoT Central.](tutorial-use-device-groups.md)

### <a name="rules"></a>Reguły
> [!div class="mx-imgBorder"]
> ![Strona Reguły](media/overview-iot-central-tour/rules-pnp.png)

Strona reguł umożliwia definiowanie reguł na podstawie danych telemetrycznych, stanu lub zdarzeń urządzeń. Gdy reguła jest uruchamiana, może wyzwolić jedną lub więcej akcji , takich jak wysyłanie wiadomości e-mail, powiadamianie systemu zewnętrznego za pośrednictwem alertów webhook itp. Aby się tego nauczyć, zobacz samouczek [Konfigurowanie reguł.](tutorial-create-telemetry-rules.md) 

### <a name="analytics"></a>Analiza

> [!div class="mx-imgBorder"]
> ![Strona Analiza](media/overview-iot-central-tour/analytics-pnp.png)

Analiza umożliwia tworzenie widoków niestandardowych na podstawie danych urządzenia, aby uzyskać szczegółowe informacje z aplikacji. Aby dowiedzieć się więcej, zobacz [artykuł Tworzenie analizy dla aplikacji Usługi Azure IoT Central.](howto-create-analytics.md)

### <a name="jobs"></a>Stanowiska

> [!div class="mx-imgBorder"]
> ![Strona zadań](media/overview-iot-central-tour/jobs-pnp.png)

Strona zadania umożliwia uruchamianie zbiorczych operacji zarządzania urządzeniami na urządzeniach. Można aktualizować właściwości urządzenia, ustawienia i wykonywać polecenia względem grup urządzeń. Aby dowiedzieć się więcej, zobacz artykuł [Uruchamianie zadania](howto-run-a-job.md).

### <a name="device-templates"></a>Szablony urządzeń

> [!div class="mx-imgBorder"]
> ![Strona szablony urządzeń](media/overview-iot-central-tour/templates-pnp.png)

Na stronie szablonów urządzeń konstruktor tworzy szablony urządzeń w aplikacji i zarządza nimi. Szablon urządzenia określa charakterystyki urządzeń, takie jak:

* Pomiary danych telemetrycznych, stanu i zdarzeń
* Właściwości
* Polecenia
* Widoki

*Konstruktor rozwiązań* można również tworzyć formularze i pulpity nawigacyjne dla operatorów do zarządzania urządzeniami.

Aby dowiedzieć się więcej, zobacz samouczek [Define a new device type in your Azure IoT Central application (Definiowanie nowego typu urządzenia w aplikacji usługi Azure IoT Central)](howto-set-up-template.md). 

### <a name="data-export"></a>Eksport danych
> [!div class="mx-imgBorder"]
> ![Strona eksportu danych](media/overview-iot-central-tour/export-pnp.png)

Eksport danych umożliwia skonfigurowanie strumieni danych, takich jak dane telemetryczne, z aplikacji do systemów zewnętrznych. Aby dowiedzieć się więcej, zobacz artykuł [Eksportowanie danych do usługi Azure IoT Central](./howto-export-data.md).

### <a name="administration"></a>Administracja
> [!div class="mx-imgBorder"]
> ![Strona Administracja](media/overview-iot-central-tour/administration-pnp.png)

Strona administracyjna umożliwia konfigurowanie i dostosowywanie aplikacji IoT Central. W tym miejscu można zmienić nazwę aplikacji, adres URL, motywy, zarządzać użytkownikami i rolami, tworzyć tokeny interfejsu API i eksportować aplikację. Aby dowiedzieć się więcej, zobacz artykuł [Administer your Azure IoT Central application (Administrowanie aplikacją usługi Azure IoT Central)](howto-administer.md).

## <a name="next-steps"></a>Następne kroki

Po zapoznaniu się z omówieniem usługi Azure IoT Central i układem interfejsu użytkownika następnym sugerowanym krokiem jest wykonanie przewodnika Szybki start [Create an Azure IoT Central application (Tworzenie aplikacji usługi Azure IoT Central)](quick-deploy-iot-central.md).
