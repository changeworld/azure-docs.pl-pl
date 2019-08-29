---
title: Dostosowywanie interfejsu użytkownika usługi Azure IoT Central | Microsoft Docs
description: Jak dostosować motyw i linki pomocy dla aplikacji Azure IoT Central
author: dominicbetts
ms.author: dobett
ms.date: 08/26/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: faef72fc00727125eef3f98641754e0e78dde3e1
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70068042"
---
# <a name="customize-the-azure-iot-central-ui"></a>Dostosowywanie interfejsu użytkownika usługi Azure IoT Central

[!INCLUDE [iot-central-original-pnp](../../includes/iot-central-original-pnp-note.md)]

W tym artykule opisano, jak administrator może dostosować interfejs użytkownika aplikacji, stosując niestandardowe motywy i modyfikując linki pomocy, aby wskazywały własne niestandardowe zasoby pomocy.

Poniższy zrzut ekranu przedstawia stronę korzystającą ze standardowego motywu:

![Standardowy IoT Central motyw](./media/howto-customize-ui/standard-ui.png)

Poniższy zrzut ekranu przedstawia stronę korzystającą z niestandardowego zrzutu ekranu z wyróżnionymi elementami interfejsu użytkownika:

![Niestandardowy motyw IoT Central](./media/howto-customize-ui/themed-ui.png)

## <a name="create-theme"></a>Utwórz motyw

Aby utworzyć motyw niestandardowy, przejdź do strony **Dostosowywanie aplikacji** w sekcji **Administracja** :

![Motywy IoT Central](./media/howto-customize-ui/themes.png)

Na tej stronie można dostosować następujące aspekty aplikacji:

### <a name="application-logo"></a>Logo aplikacji

Obraz PNG, nie większy niż 1 MB, z przezroczystym tłem. To logo jest wyświetlane w lewym okienku na pasku tytułu aplikacji IoT Central.

Jeśli obraz logo zawiera nazwę aplikacji, można ukryć tekst nazwy aplikacji. Aby uzyskać więcej informacji, zobacz [Zarządzanie aplikacją](./howto-administer.md#change-application-name-and-url).

### <a name="browser-icon-favicon"></a>Ikona przeglądarki (favicon)

Obraz PNG, nie większy niż 32 x 32 pikseli, z przezroczystym tłem. Przeglądarka sieci Web może korzystać z tego obrazu na pasku adresu, na stronie historia, w zakładkach i na karcie przeglądarki.

### <a name="browser-colors"></a>Kolory przeglądarki

Można zmienić kolor nagłówka strony i kolor używany do wyróżniania przycisków i innych podświetleń. Użyj sześciu znaków szesnastkowych wartości koloru w formacie `##ff6347`. Aby uzyskać więcej informacji na temat notacji koloru **wartości szesnastkowej** , zobacz [kolory HTML](https://www.w3schools.com/html/html_colors.asp).

> [!NOTE]
> Zawsze możesz wrócić do opcji domyślnych na stronie **Dostosowywanie aplikacji** .

### <a name="changes-for-operators"></a>Zmiany dla operatorów

Jeśli administrator utworzy motyw niestandardowy, operatorzy i inni użytkownicy aplikacji nie będą już mogli wybierać motywu w **ustawieniach**.

## <a name="replace-help-links"></a>Zastąp linki pomocy

Aby zapewnić niestandardowe informacje pomocy dla operatorów i innych użytkowników, można zmodyfikować linki w menu **Pomoc** aplikacji.

Aby zmodyfikować linki pomocy, przejdź do strony **Dostosowywanie pomocy** w sekcji **Administracja** :

![Dostosuj linki pomocy IoT Central](./media/howto-customize-ui/help-links.png)

Możesz również dodać nowe wpisy do menu Pomoc i usunąć domyślne wpisy:

![Dostosowana IoT Central pomoc](./media/howto-customize-ui/custom-help.png)

> [!NOTE]
> Zawsze możesz wrócić do domyślnych linków pomocy na stronie **Dostosowywanie pomocy** .

## <a name="next-steps"></a>Następne kroki

Teraz, gdy już wiesz, jak dostosować interfejs użytkownika w aplikacji IoT Central, Oto kilka sugerowanych następnych kroków:

- [Administruj swoją aplikacją](./howto-administer.md)
- [Konfigurowanie pulpitu nawigacyjnego aplikacji](./howto-configure-homepage.md)