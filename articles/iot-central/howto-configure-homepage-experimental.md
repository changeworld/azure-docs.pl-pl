---
title: Konfigurowanie strony głównej aplikacji usługi Azure IoT Central | Dokumentacja firmy Microsoft
description: Jako Konstruktor Dowiedz się, jak skonfigurować stronę główną aplikacji usługi Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 02/05/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 599616f046b2eccee009e8a08e8a2f51cf968644
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/06/2019
ms.locfileid: "55773525"
---
# <a name="configuring-homepage"></a>Konfigurowanie strony głównej

Strona główna jest strona która ładuje, gdy użytkownicy, którzy mają dostęp do aplikacji, przejdź do adresu URL aplikacji. Zaznaczenie "Przykładowy Contoso" lub "Przykładowe Devkits" szablonów aplikacji podczas tworzenia aplikacji, aplikacja będzie mieć wstępnie zdefiniowane strony główne. Jeśli z drugiej strony został wybrany szablon aplikacji "Aplikacja niestandardowa", strony głównej będzie puste.

## <a name="add-tiles"></a>Dodaj Kafelki

Na przykład Oto strona główna dla aplikacji na podstawie szablonu "Contoso próbki". Aby dostosować stronę główną dla aplikacji, należy najpierw wybrać **Edytuj** w prawym górnym rogu. 

![Strona główna dla aplikacji na podstawie szablonu "Contoso próbki"](media/howto-configure-homepage-experimental/image1.png)

Wybieranie **Edytuj**, zostanie otwarty Biblioteka pulpitu nawigacyjnego w panelu po lewej stronie. Istnieje wiele rodzajów Kafelki pulpitu nawigacyjnego w nim elementów podstawowych, które można dodać w celu dostosowania strony głównej.

![Biblioteka pulpitu nawigacyjnego](media/howto-configure-homepage-experimental/image2.png)

Na przykład można dodać **ustawień i właściwości** Kafelek, aby pokazywać zaznaczenie bieżące wartości ustawień i właściwości. Aby to zrobić, najpierw wybierz **szablon urządzenia** polecenie **wystąpienia urządzenia**. Po które podają kafelka, tytuł i wybierz **ustawienie** lub **właściwość** do wyświetlenia. W tym przypadku Wybraliśmy **Ustaw temperatury**. Klikając **gotowe** spowoduje, że ten Kafelek, aby są wyświetlane na stronie głównej.

!["Configure szczegóły urządzenia" formularz Szczegóły ustawień i właściwości](media/howto-configure-homepage-experimental/image3.png)

Teraz gdy operator wyświetla stronę główną, można wyświetlić tego kafelka, powoduje wyświetlenie właściwości lub ustawień urządzenia:

![Karta "Pulpit nawigacyjny" z wyświetlanych ustawień i właściwości dla kafelka](media/howto-configure-homepage-experimental/image4.png)

Poeksperymentuj z różnych innych kafelków typów w bibliotece Aby dowiedzieć się, jak można dostosować stronę główną w swojej aplikacji nawet więcej.

## <a name="next-steps"></a>Kolejne kroki

Teraz, kiedy znasz sposób konfigurowania strony głównej usługi Azure IoT Central, możesz wykonywać następujące czynności:

> [!div class="nextstepaction"]
> [Dowiedz się, jak przygotować i przekazać obrazy](howto-prepare-images-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)
