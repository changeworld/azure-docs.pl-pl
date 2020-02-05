---
title: Korzystanie z szablonów aplikacji na platformie Azure IoT Central | Microsoft Docs
description: Jako operator, jak używać zestawów urządzeń w aplikacji IoT Central platformy Azure.
author: dominicbetts
ms.author: dobett
ms.date: 05/30/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 3cc6f82676f426240fba4cc4910246073aa9a556
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/04/2020
ms.locfileid: "76982464"
---
# <a name="use-application-templates"></a>Używanie szablonów aplikacji

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

W tym artykule opisano, jak Menedżer rozwiązań umożliwia tworzenie i używanie szablonów aplikacji.

Podczas tworzenia aplikacji IoT Central platformy Azure Możesz wybrać wbudowane przykładowe szablony. Możesz również tworzyć własne szablony aplikacji z istniejących aplikacji IoT Central. Przy tworzeniu nowych aplikacji możesz użyć własnych szablonów aplikacji.

Podczas tworzenia szablonu aplikacji zawiera on następujące elementy z istniejącej aplikacji:

- Domyślny pulpit nawigacyjny aplikacji, w tym układ pulpitu nawigacyjnego i wszystkie zdefiniowane kafelki.
- Szablony urządzeń, w tym pomiary, ustawienia, właściwości, polecenia i pulpit nawigacyjny.
- Przepisy. Wszystkie definicje reguł są uwzględniane. Jednak akcje, z wyjątkiem akcji poczty e-mail, nie są uwzględniane.
- Zestawy urządzeń, w tym ich warunki i pulpity nawigacyjne.

> [!WARNING]
> Jeśli pulpit nawigacyjny zawiera kafelki, które wyświetlają informacje o określonych urządzeniach, wówczas te kafelki pokazują **żądany zasób nie został odnaleziony** w nowej aplikacji. Należy ponownie skonfigurować te kafelki, aby wyświetlić informacje o urządzeniach w nowej aplikacji.

Podczas tworzenia szablonu aplikacji nie zawiera on następujących elementów:

- Urządzenia
- Użytkownicy
- Definicje zadań
- Definicje eksportu danych ciągłych

Dodaj te elementy ręcznie do wszystkich aplikacji utworzonych na podstawie szablonu aplikacji.

## <a name="create-an-application-template"></a>Tworzenie szablonu aplikacji

Aby utworzyć szablon aplikacji na podstawie istniejącej aplikacji IoT Central:

1. Przejdź do sekcji **Administracja** w swojej aplikacji.
1. Wybierz pozycję **Eksportuj szablon aplikacji**.
1. Na stronie **Eksportuj szablon aplikacji** wprowadź nazwę i opis szablonu.
1. Wybierz przycisk **Eksportuj** , aby utworzyć szablon aplikacji. Teraz można skopiować **link** umożliwiający utworzenie nowej aplikacji na podstawie szablonu:

![Tworzenie szablonu aplikacji](media/howto-use-app-templates/create-template.png)

## <a name="use-an-application-template"></a>Korzystanie z szablonu aplikacji

Aby użyć szablonu aplikacji do utworzenia nowej aplikacji IoT Central, potrzebujesz wcześniej utworzonego **linku**umożliwiającego udostępnianie. Wklej link umożliwiający **udostępnianie** na pasku adresu przeglądarki. Zostanie wyświetlona strona **Tworzenie aplikacji** z wybranym szablonem aplikacji niestandardowej:

![Tworzenie aplikacji na podstawie szablonu](media/howto-use-app-templates/create-app.png)

Wybierz plan cenowy i wypełnij pozostałe pola w formularzu. Następnie wybierz pozycję **Utwórz** , aby utworzyć nową aplikację IoT Centralową z szablonu aplikacji.

## <a name="manage-application-templates"></a>Zarządzanie szablonami aplikacji

Na stronie **eksport szablonu aplikacji** można usunąć lub zaktualizować szablon aplikacji.

W przypadku usunięcia szablonu aplikacji nie można już używać wcześniej wygenerowanego linku umożliwiającego udostępnianie, aby tworzyć nowe aplikacje.

Aby zaktualizować szablon aplikacji, Zmień nazwę lub opis szablonu na stronie **eksportowania szablonu aplikacji** . Następnie ponownie wybierz przycisk **Eksportuj** . Ta akcja spowoduje wygenerowanie nowego **linku** możliwego do udostępniania i unieważnienie dowolnego adresu URL linku możliwego do **współdzielenia** .

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak korzystać z szablonów aplikacji, sugerowanym następnym krokiem jest zapoznanie się z tematem jak [zarządzać IoT Central z Azure Portal](howto-manage-iot-central-from-portal.md)
