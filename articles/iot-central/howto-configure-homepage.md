---
title: Konfigurowanie pulpitu nawigacyjnego aplikacji platformy Azure IoT Central | Microsoft Docs
description: Jako Konstruktor można dowiedzieć się, jak skonfigurować domyślny pulpit nawigacyjny aplikacji platformy Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 07/10/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 94ad51ac11687dfe060176132e2030d61b8d4ffc
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/12/2019
ms.locfileid: "67850217"
---
# <a name="configure-the-application-dashboard"></a>Konfigurowanie pulpitu nawigacyjnego aplikacji

**Pulpit nawigacyjny** to strona, która ładuje się, gdy użytkownicy, którzy mają dostęp do aplikacji, przejdą do adresu URL aplikacji. W przypadku wybrania przykładowego szablonu aplikacji **contoso** lub **Sample Devkits** do utworzenia aplikacji aplikacja ma wstępnie zdefiniowany pulpit nawigacyjny. W przypadku wybrania  szablonu aplikacja niestandardowa, pulpit nawigacyjny jest pusty.

> [!NOTE]
> Użytkownicy mogą również [tworzyć własne osobiste pulpity nawigacyjne](howto-personalize-dashboard.md) , które będą używane zamiast domyślnego pulpitu nawigacyjnego aplikacji.

## <a name="add-tiles"></a>Dodaj kafelki

Poniższy zrzut ekranu przedstawia pulpit nawigacyjny w aplikacji utworzonej na podstawie **przykładowego szablonu contoso** . Aby dostosować domyślny pulpit nawigacyjny dla aplikacji, wybierz pozycję **Edytuj** w prawym górnym rogu strony.

![Pulpit nawigacyjny dla aplikacji na podstawie szablonu "Przykładowa contoso"](media/howto-configure-homepage/image1a.png)

Po wybraniu opcji **Edytuj**Otwórz panel Biblioteka pulpitów nawigacyjnych. Biblioteka zawiera elementy pierwotne kafelków i pulpitów nawigacyjnych, których można użyć do dostosowania pulpitu nawigacyjnego.

![Biblioteka pulpitu nawigacyjnego](media/howto-configure-homepage/image2a.png)

Na przykład możesz dodać kafelek **Ustawienia i właściwości urządzenia** , aby wyświetlić wybrane wartości bieżących ustawień i właściwości urządzenia. Aby to zrobić, najpierw wybierz **szablon urządzenia** , a następnie wybierz **wystąpienie urządzenia**. Po utworzeniu kafelka tytuł i wybierz **ustawienie** lub **Właściwość** do wyświetlenia. Poniższy zrzut ekranu przedstawia ustawienia i właściwości wybrane do dodania do kafelka. Wybierz pozycję **gotowe** , aby zapisać zmiany na pulpicie nawigacyjnym.

![Formularz "Konfigurowanie szczegółów urządzenia" z informacjami szczegółowymi dotyczącymi ustawień i właściwości](media/howto-configure-homepage/image3a.png)

Teraz po wyświetleniu domyślnego pulpitu nawigacyjnego aplikacji przez operatora zostanie wyświetlony nowy kafelek z ustawieniem **Ustawianie temperatury** dla urządzenia:

![Karta "pulpit nawigacyjny" z wyświetlonymi ustawieniami i właściwościami kafelka](media/howto-configure-homepage/image4a.png)

Możesz eksplorować inne typy kafelków w bibliotece, aby dowiedzieć się, jak dodatkowo dostosować domyślny pulpit nawigacyjny aplikacji.

Aby dowiedzieć się więcej na temat używania kafelków w usłudze Azure IoT Central, zobacz [Używanie kafelków pulpitu nawigacyjnego](howto-use-tiles.md).

## <a name="next-steps"></a>Następne kroki

Teraz, gdy już wiesz, jak skonfigurować pulpit nawigacyjny domyślnego aplikacji platformy Azure IoT Central, możesz:

> [!div class="nextstepaction"]
> [Dowiedz się, jak przygotować i przekazać obrazy](howto-prepare-images.md)
