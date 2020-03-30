---
title: Dostosowywanie centralnego interfejsu użytkownika usługi Azure IoT | Dokumenty firmy Microsoft
description: Jak dostosować motyw i łącza pomocy dla centralnej aplikacji Usługi Azure IoT
author: dominicbetts
ms.author: dobett
ms.date: 12/06/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 8f76f143b6c6a26b88b78e20d8d5d8ae1ae48553
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80158023"
---
# <a name="customize-the-azure-iot-central-ui"></a>Dostosowywanie centralnego interfejsu użytkownika usługi Azure IoT

W tym artykule opisano, jak jako administrator można dostosować interfejs użytkownika aplikacji, stosując niestandardowe motywy i modyfikując łącza pomocy, aby wskazać własne niestandardowe zasoby pomocy. 



Poniższy zrzut ekranu przedstawia stronę używającą standardowego motywu:

![Kompozycja Standardowy ioT Central](./media/howto-customize-ui/standard-ui.png)

Poniższy zrzut ekranu przedstawia stronę przy użyciu niestandardowego zrzutu ekranu z wyróżnionymi niestandardowymi elementami interfejsu użytkownika:

![Kompozycja Niestandardowe centrum IoT](./media/howto-customize-ui/themed-ui.png)

## <a name="create-theme"></a>Tworzenie motywu

Aby utworzyć motyw niestandardowy, przejdź do strony **Dostosowywanie aplikacji** w sekcji **Administracja:**

![Motywy centralne IoT](./media/howto-customize-ui/themes.png)

Na tej stronie można dostosować następujące aspekty aplikacji:

### <a name="application-logo"></a>Logo aplikacji

Obraz PNG, nie większy niż 1 MB, z przezroczystym tłem. To logo jest wyświetlane po lewej stronie na pasku tytułu aplikacji IoT Central.

Jeśli obraz logo zawiera nazwę aplikacji, można ukryć tekst nazwy aplikacji. Aby uzyskać więcej informacji, zobacz [Zarządzanie aplikacją](howto-administer.md#change-application-name-and-url).

### <a name="browser-icon-favicon"></a>Ikona przeglądarki (favicon)

Obraz PNG o rozmiarze nie większym niż 32 x 32 piksele z przezroczystym tłem. Przeglądarka internetowa może używać tego obrazu na pasku adresu, historii, zakładkach i karcie przeglądarki.

### <a name="browser-colors"></a>Kolory przeglądarki

Można zmienić kolor nagłówka strony i kolor używany do akcentowania przycisków i innych wyróżnień. Użyj sześcioznakowej wartości koloru `##ff6347`sześcioznakowego w formacie . Aby uzyskać więcej informacji na temat notacji kolorów **wartości HEX,** zobacz [Kolory HTML](https://www.w3schools.com/html/html_colors.asp).

> [!NOTE]
> Zawsze możesz przywrócić opcje domyślne na stronie **Dostosowywanie aplikacji.**

### <a name="changes-for-operators"></a>Zmiany dla operatorów

Jeśli administrator utworzy motyw niestandardowy, operatorzy i inni użytkownicy aplikacji nie będą już mogli wybrać motywu w **ustawieniach**.

## <a name="replace-help-links"></a>Zamienianie łączy pomocy

Aby zapewnić niestandardowe informacje pomocy operatorom i innym użytkownikom, można zmodyfikować łącza w menu **Pomocy** aplikacji.

Aby zmodyfikować łącza pomocy, przejdź do strony **Dostosowywanie pomocy** w sekcji **Administracja:**

![Dostosowywanie łączy pomocy IoT Central](./media/howto-customize-ui/help-links.png)

Można również dodać nowe wpisy do menu pomocy i usunąć wpisy domyślne:

![Dostosowana pomoc IoT Central](./media/howto-customize-ui/custom-help.png)

> [!NOTE]
> Zawsze możesz przywrócić domyślne łącza pomocy na stronie **Dostosowywanie pomocy.**

## <a name="next-steps"></a>Następne kroki

Teraz, gdy już wiesz, jak dostosować interfejs użytkownika w aplikacji IoT Central, oto kilka sugerowanych następnych kroków:

- [Administrowanie aplikacją](./howto-administer.md)
- [Dodawanie kafelków do pulpitu nawigacyjnego](howto-add-tiles-to-your-dashboard.md)