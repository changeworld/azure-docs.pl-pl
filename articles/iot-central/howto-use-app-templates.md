---
title: Używanie szablonów aplikacji w usłudze Azure IoT Central | Dokumentacja firmy Microsoft
description: Uprawnienia operatora sposób używania urządzenia ustawia się w aplikacji usługi Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 05/30/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: a26f70c5a61f3855a3de991072a7e84103e87b69
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2019
ms.locfileid: "66499222"
---
# <a name="use-application-templates"></a>Używanie szablonów aplikacji

W tym artykule opisano sposób tworzenia i używania szablonów aplikacji Menedżer rozwiązania.

Podczas tworzenia aplikacji usługi Azure IoT Central, dostępne są przykładowe wbudowanych szablonów. Można również utworzyć własne szablony aplikacji z istniejących aplikacji IoT Central. Następnie można użyć szablonów aplikacji, podczas tworzenia nowych aplikacji.

Podczas tworzenia szablonów aplikacji zawiera następujące elementy z istniejącej aplikacji:

- Domyślny pulpit nawigacyjny aplikacji, w tym układ pulpitu nawigacyjnego i wszystkie Kafelki, które zostały zdefiniowane.
- Szablony urządzenia, w tym pomiarów, ustawienia, właściwości, poleceń i pulpitu nawigacyjnego.
- Reguły. Uwzględniane są wszystkie definicje reguły. Jednak operacje z wyjątkiem akcji poczty e-mail nie są uwzględniane.
- Ustawia urządzeń, łącznie z ich warunków i pulpitów nawigacyjnych.

> [!WARNING]
> Jeśli pulpit nawigacyjny zawiera Kafelki, które zawierają informacje o określonych urządzeniach, a następnie wyświetlić te Kafelki **nie odnaleziono żądanego zasobu** w nowej aplikacji. Należy ponownie skonfigurować te Kafelki, aby wyświetlić informacje o urządzeniach w nowej aplikacji.

Podczas tworzenia szablonów aplikacji nie zawiera następujące elementy:

- Urządzenia
- Użytkownicy
- Definicje zadań
- Definicje eksportu ciągłego danych

Wszystkie aplikacje utworzone na podstawie szablonu aplikacji ręcznie dodać te elementy.

## <a name="create-an-application-template"></a>Tworzenie szablonów aplikacji

Aby utworzyć szablon aplikacji z istniejącej aplikacji IoT Central:

1. Przejdź do **administracji** sekcji w aplikacji.
1. Wybierz **eksportowania szablonu aplikacji**.
1. Na **eksportowania szablonu aplikacji** strony, wprowadź nazwę i opis szablonu.
1. Wybierz **wyeksportować** przycisk, aby utworzyć szablon aplikacji. Teraz możesz skopiować **możliwego do udostępnienia łącze** , który umożliwia osobie, Utwórz nową aplikację z szablonu:

![Tworzenie szablonów aplikacji](media/howto-use-app-templates/create-template.png)

## <a name="use-an-application-template"></a>Użyj szablonu aplikacji

Skorzystać z szablonu aplikacji, aby utworzyć nową aplikację IoT Central, należy wcześniej utworzony **możliwego do udostępnienia łącze**. Wklej **możliwego do udostępnienia łącze** do paska adresu w przeglądarce. **Tworzenia aplikacji** strony wyświetli z wybraniu szablonu aplikacji niestandardowej:

![Tworzenie aplikacji na podstawie szablonu](media/howto-use-app-templates/create-app.png)

Wybierz plan płatności, a następnie wypełnij pola w formularzu. Następnie wybierz pozycję **Utwórz** Aby utworzyć nową aplikację IoT Central z szablonu aplikacji.

## <a name="manage-application-templates"></a>Zarządzanie szablonami aplikacji

Na **eksportowania szablonu aplikacji** strony, można usunąć lub zaktualizować szablon aplikacji.

Jeśli usuniesz szablonów aplikacji, nie jest już służy wcześniej wygenerowany link możliwego do udostępnienia do tworzenia nowych aplikacji.

Aby zaktualizować szablon aplikacji, należy zmienić na Nazwa i opis szablonu **eksportowania szablonu aplikacji** strony. Następnie wybierz pozycję **wyeksportować** ponownie przycisk. Ta akcja generuje nowy **możliwego do udostępnienia łącze** a unieważnia wszelkie poprzednie **możliwego do udostępnienia łącze** adresu URL.

## <a name="next-steps"></a>Kolejne kroki

Teraz, kiedy znasz sposób używania szablonów aplikacji, sugerowane następnym krokiem jest Dowiedz się, jak [Zarządzanie IoT Central w witrynie Azure portal](howto-manage-iot-central-from-portal.md)
