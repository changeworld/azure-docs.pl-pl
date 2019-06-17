---
title: Dostosowywanie usługi Azure IoT Central interfejsu użytkownika | Dokumentacja firmy Microsoft
description: Jak dostosować łącza motywu i pomocy dla aplikacji usługi Azure IoT central
author: dominicbetts
ms.author: dobett
ms.date: 04/25/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 4d385f1e8c883453b4153ca4c9119d3be0a608bb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66495558"
---
# <a name="customize-the-azure-iot-central-ui"></a>Dostosowywanie usługi Azure IoT Central interfejsu użytkownika 

*Ten artykuł ma zastosowanie do administratorów.*

IoT Central umożliwia dostosowywanie interfejsu użytkownika aplikacji, stosując motywy niestandardowe i modyfikując łącza pomocy, aby wskazywał zasobów Pomocy niestandardowej. Poniższy zrzut ekranu przedstawia stronę za pomocą standardowych motywu:

![Standardowa motyw IoT Central](./media/howto-customize-ui/standard-ui.png)

Poniższy zrzut ekranu przedstawia stronę z niestandardowych elementów interfejsu użytkownika, wyróżniony za pomocą niestandardowych zrzut ekranu:

![Niestandardowy motyw centralnej IoT](./media/howto-customize-ui/themed-ui.png)

## <a name="create-theme"></a>Utwórz motyw

Aby utworzyć niestandardowy motyw, przejdź do **dostosować aplikację** strony w **administracji** sekcji:

![Motywy IoT Central](./media/howto-customize-ui/themes.png)

Na tej stronie można dostosować następujące aspekty aplikacji:

### <a name="application-logo"></a>Logo aplikacji

Obraz PNG, nie może być większa niż 1 MB z przezroczystym tłem. To są wyświetlane po lewej stronie, na pasku tytułu aplikacji IoT Central.

Jeśli obraz logo zawiera nazwę aplikacji, można ukryć tekst nazwy aplikacji. Aby uzyskać więcej informacji, zobacz [Zarządzanie ustawieniami aplikacji](./howto-administer.md#manage-application-settings).

### <a name="browser-icon-favicon"></a>Ikona przeglądarki (favicon)

Obraz PNG, nie może być większa niż 32 x 32 piksele z przezroczystym tłem. Przeglądarki sieci web można użyć tego obrazu w pasku adresu, historii, zakładki i karcie przeglądarki.

### <a name="browser-colors"></a>Kolory przeglądarki

Możesz zmienić kolor nagłówka strony i kolor accenting przyciski i inne najważniejsze funkcje. Użyj wartości kolor w postaci szesnastkowej sześć znaków w formacie `##ff6347`. Aby uzyskać więcej informacji na temat **wartość SZESNASTKOWA** kolor notacji, zobacz [kolory HTML](https://www.w3schools.com/html/html_colors.asp).

> [!NOTE]
> Zawsze możesz przywrócić na powrót do domyślnych opcji **dostosować aplikację** strony.

### <a name="changes-for-operators"></a>Zmiany operatorów

Jeśli administrator tworzy niestandardowy motyw, a następnie operatorów i innych użytkowników w aplikacji może już nie umożliwia wybranie motywu w **ustawienia**.

## <a name="replace-help-links"></a>Zastąp łącza pomocy

Do dostarczania informacji Pomocy niestandardowej usługi operatorów i innych użytkowników, można zmodyfikować łącza aplikacji **pomocy** menu.

Aby zmodyfikować łącza pomocy, przejdź do **dostosować Pomoc** strony w **administracji** sekcji:

![Dostosowywanie łącza pomocy IoT Central](./media/howto-customize-ui/help-links.png)

Można także dodać nowe wpisy do menu Pomoc i Usuń wpisy domyślne:

![Dostosowane pomocy IoT Central](./media/howto-customize-ui/custom-help.png)

> [!NOTE]
> Zawsze możesz przywrócić na powrót do domyślnej łącza pomocy **dostosować Pomoc** strony.

## <a name="next-steps"></a>Kolejne kroki

Teraz, kiedy znasz sposób dostosowywania interfejsu użytkownika w aplikacji IoT Central, poniżej przedstawiono niektóre Sugerowane następne kroki:

- [Zarządzać aplikacją](./howto-administer.md)
- [Konfigurowanie pulpit nawigacyjny aplikacji](./howto-configure-homepage.md)