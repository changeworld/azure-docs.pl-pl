---
title: Przewodnik po interfejsie użytkownika usługi Azure IoT Central | Microsoft Docs
description: Jako konstruktor możesz zapoznać się z kluczowymi obszarami interfejsu użytkownika usługi Azure IoT Central używanego do utworzenia rozwiązania IoT.
author: dominicbetts
ms.author: dobett
ms.date: 05/31/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: 53306b2087a47f5a61fc2a228e2d1f527127f746
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2019
ms.locfileid: "66476323"
---
# <a name="take-a-tour-of-the-azure-iot-central-ui"></a>Przewodnik po interfejsie użytkownika usługi Azure IoT Central

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

Dostęp do różnych obszarów aplikacji za pomocą menu nawigacji po lewej stronie. Można rozwinąć lub zwinąć paska nawigacyjnego, wybierając **<** lub **>** :

| Menu | Opis |
| ---- | ----------- |
| ![Lewe menu nawigacji](media/overview-iot-central-tour/navigationbar.png) | <ul><li>**Pulpit nawigacyjny** przycisk powoduje wyświetlenie pulpitu nawigacyjnego aplikacji. Jako Konstruktor możesz dostosować pulpit nawigacyjny dla swojej operatorów. Użytkownicy mogą także tworzyć własne pulpity nawigacyjne.</li><li>Przycisk **Device Explorer** wyświetla listę symulowanych i rzeczywistych urządzeń skojarzonych z każdym szablonem urządzenia w aplikacji. Operator używa narzędzia **Device Explorer**, aby zarządzać połączonymi urządzeniami.</li><li>Przycisk **Zestawy urządzeń** umożliwia wyświetlenie i tworzenie zestawów urządzeń. Operator może tworzyć zestawy urządzeń w formie logicznych zbiorów urządzeń określonych w zapytaniu.</li><li>Przycisk **Analiza** umożliwia wyświetlenie analiz na podstawie danych telemetrycznych urządzeń i zestawów urządzeń. Operator może tworzyć widoki niestandardowe na podstawie danych urządzenia w celu uzyskania szczegółowych informacji z aplikacji.</li><li>Przycisk **Zadania** umożliwia zbiorcze zarządzanie urządzeniami przez tworzenie i uruchamianie zadań przeprowadzających aktualizacje w dużej skali.</li><li>Przycisk **Szablony urządzeń** powoduje wyświetlenie narzędzi używanych przez konstruktora do tworzenia szablonów urządzeń i zarządzania nimi.</li><li>Przycisk **Ciągły eksport danych** służy administratorom do konfigurowania ciągłego eksportowania do innych usług platformy Azure, takich jak magazyn czy kolejki.</li><li>Przycisk **Administracja** umożliwia wyświetlenie stron administracyjnych aplikacji, na których administrator może zarządzać użytkownikami, rolami i ustawieniami aplikacji.</li></ul> |

## <a name="search-help-and-support"></a>Wyszukiwanie, pomoc i pomoc techniczna

Górne menu jest wyświetlane na każdej stronie:

![Pasek narzędzi](media/overview-iot-central-tour/toolbar.png)

- Aby wyszukać szablonów urządzeń i urządzeń, należy wprowadzić **wyszukiwania** wartość.
- Aby zmienić język interfejsu użytkownika lub motywu, wybierz **ustawienia** ikony.
- Aby Wyloguj się z aplikacji, wybierz opcję **konta** ikony.
- Aby uzyskać pomoc i pomoc techniczną, wybierz pozycję rozwijaną **Pomoc**, aby uzyskać listę zasobów. W wersji próbnej aplikacji, zasoby pomocy technicznej oferują dostęp do [Czat na żywo](howto-show-hide-chat.md).

Możesz wybrać jasny lub ciemny motyw interfejsu użytkownika:

![Wybieranie motywu interfejsu użytkownika](media/overview-iot-central-tour/themes.png)

> [!NOTE]
> Możliwość wyboru między motywy jasny i ciemny jest niedostępna, jeśli administrator skonfigurował motywu niestandardowego dla aplikacji.

## <a name="dashboard"></a>Pulpit nawigacyjny

![Pulpit nawigacyjny](media/overview-iot-central-tour/homepage.png)

* Pulpit nawigacyjny jest pierwsza strona, który zostanie wyświetlony po zalogowaniu się do aplikacji usługi Azure IoT Central. Jako Konstruktor możesz dostosować pulpit nawigacyjny aplikacji dla innych użytkowników, dodając kafelków. Więcej informacji można znaleźć w samouczku [Customize the Azure IoT Central operator's view (Dostosowywanie widoku operatora usługi Azure IoT Central)](tutorial-customize-operator.md).

* Operator możesz utworzyć spersonalizowane pulpity nawigacyjne i przełączać się między nimi i domyślny pulpit nawigacyjny. Aby dowiedzieć się więcej, zobacz [tworzenie i zarządzanie nimi osobistych pulpitów nawigacyjnych](howto-personalize-dashboard.md) instrukcje.

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

Strona zadania umożliwia uruchamianie operacje zarządzania urządzeniami zbiorczo na urządzeniach. Konstruktor używa tej strony do aktualizowania właściwości urządzeń, ustawień i poleceń. Aby dowiedzieć się więcej, zobacz artykuł [Uruchamianie zadania](howto-run-a-job.md).

## <a name="device-templates"></a>Szablony urządzenia

![Strona Szablony urządzeń](media/overview-iot-central-tour/templates.png)

Na stronie szablonów urządzeń konstruktor tworzy szablony urządzeń w aplikacji i zarządza nimi. Szablon urządzenia określa właściwości urządzenia, takie jak:

- Pomiary dotyczące prawdziwych danych telemetrycznych, stanu i zdarzeń.
- Ustawienia i właściwości.
- Polecenia.
- Reguły na podstawie zdarzeń lub wartości danych telemetrycznych.

Aby dowiedzieć się więcej, zobacz samouczek [Define a new device type in your Azure IoT Central application (Definiowanie nowego typu urządzenia w aplikacji usługi Azure IoT Central)](tutorial-define-device-type.md).

## <a name="continuous-data-export"></a>Ciągły eksport danych

![Strona Ciągły eksport danych](media/overview-iot-central-tour/export.png)

Strona eksportu ciągłego danych jest, gdy administrator definiuje, jak przesłać strumień danych, takich jak dane telemetryczne z aplikacji. Inne usługi mogą zapisywać wyeksportowane dane lub poddawać je analizie. Aby dowiedzieć się więcej, zobacz artykuł [Eksportowanie danych do usługi Azure IoT Central](howto-export-data.md).

## <a name="administration"></a>Administracja

![Strona Administracja](media/overview-iot-central-tour/administration.png)

Na stronie Administracja zawiera linki do narzędzi, które administrator używa takich jak definiowanie użytkownicy i role w aplikacji i dostosowywanie interfejsu użytkownika. Aby dowiedzieć się więcej, zobacz artykuł [Administer your Azure IoT Central application (Administrowanie aplikacją usługi Azure IoT Central)](howto-administer.md).

## <a name="next-steps"></a>Kolejne kroki

Po zapoznaniu się z omówieniem usługi Azure IoT Central i układem interfejsu użytkownika następnym sugerowanym krokiem jest wykonanie przewodnika Szybki start [Create an Azure IoT Central application (Tworzenie aplikacji usługi Azure IoT Central)](quick-deploy-iot-central.md).