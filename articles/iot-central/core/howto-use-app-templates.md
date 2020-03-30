---
title: Eksportowanie aplikacji Azure IoT Central | Dokumenty firmy Microsoft
description: Jako menedżer rozwiązań chcę wyeksportować szablon aplikacji, aby móc go ponownie użyć.
author: dominicbetts
ms.author: dobett
ms.date: 12/09/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: f50c7e8dcb33fd2ed95829286aaf815926d9fb3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80157637"
---
# <a name="export-your-application"></a>Eksportowanie aplikacji



W tym artykule opisano, jak jako menedżer rozwiązań wyeksportować aplikację IoT Central, aby móc jej ponownie użyć.

Dostępne są dwie opcje:

- Można utworzyć kopię aplikacji, jeśli wystarczy utworzyć zduplikowaną kopię aplikacji.
- Szablon aplikacji można utworzyć z aplikacji, jeśli planujesz utworzyć wiele kopii.

## <a name="copy-your-application"></a>Kopiowanie aplikacji

Można utworzyć kopię dowolnej aplikacji, pomniejszoną o wszystkie wystąpienia urządzeń, historię danych urządzenia i dane użytkownika. Kopia korzysta ze standardowego planu cenowego, za który zostanie naliczona naliczona. Nie można utworzyć aplikacji, która korzysta z bezpłatnego planu cenowego, kopiując aplikację.

Wybierz polecenie **Kopiuj**. W oknie dialogowym wprowadź szczegóły nowej aplikacji. Następnie wybierz **pozycję Kopiuj,** aby potwierdzić, że chcesz kontynuować. Aby dowiedzieć się więcej o polach w formularzu, zobacz Szybki start [tworzenia aplikacji.](quick-deploy-iot-central.md)

![Strona ustawień aplikacji](media/howto-use-app-templates/appcopy2.png)

Po zakończeniu operacji kopiowania aplikacji można przejść do nowej aplikacji za pomocą łącza.

![Strona ustawień aplikacji](media/howto-use-app-templates/appcopy3a.png)

Kopiowanie aplikacji kopiuje również definicję reguł i akcji poczty e-mail. Niektóre akcje, takie jak Aplikacje przepływu i logiki, są powiązane z określonymi regułami za pomocą identyfikatora reguły. Gdy reguła jest kopiowana do innej aplikacji, pobiera swój własny identyfikator reguły. W takim przypadku użytkownicy będą musieli utworzyć nową akcję, a następnie skojarzyć z nią nową regułę. Ogólnie rzecz biorąc, warto sprawdzić reguły i akcje, aby upewnić się, że są aktualne w nowej aplikacji.

> [!WARNING]
> Jeśli pulpit nawigacyjny zawiera kafelki, które wyświetlają informacje o określonych urządzeniach, te kafelki pokazują **Żądany zasób nie został znaleziony** w nowej aplikacji. Należy ponownie skonfigurować te kafelki, aby wyświetlić informacje o urządzeniach w nowej aplikacji.

## <a name="create-an-application-template"></a>Tworzenie szablonu aplikacji

Podczas tworzenia aplikacji Azure IoT Central masz do wyboru wbudowane przykładowe szablony. Można również utworzyć własne szablony aplikacji z istniejących aplikacji IoT Central. Następnie można użyć własnych szablonów aplikacji podczas tworzenia nowych aplikacji.

Podczas tworzenia szablonu aplikacji zawiera on następujące elementy z istniejącej aplikacji:

- Domyślny pulpit nawigacyjny aplikacji, w tym układ pulpitu nawigacyjnego i wszystkie zdefiniowane kafelki.
- Szablony urządzeń, w tym pomiary, ustawienia, właściwości, polecenia i pulpit nawigacyjny.
- Zasady. Wszystkie definicje reguł są uwzględniane. Jednak akcje, z wyjątkiem akcji poczty e-mail, nie są uwzględniane.
- Zestawy urządzeń, w tym ich warunki i pulpity nawigacyjne.

> [!WARNING]
> Jeśli pulpit nawigacyjny zawiera kafelki, które wyświetlają informacje o określonych urządzeniach, te kafelki pokazują **Żądany zasób nie został znaleziony** w nowej aplikacji. Należy ponownie skonfigurować te kafelki, aby wyświetlić informacje o urządzeniach w nowej aplikacji.

Podczas tworzenia szablonu aplikacji nie zawiera on następujących elementów:

- Urządzenia
- Użytkownicy
- Definicje zadań
- Definicje ciągłego eksportu danych

Dodaj te elementy ręcznie do wszystkich aplikacji utworzonych na podstawie szablonu aplikacji.

Aby utworzyć szablon aplikacji z istniejącej aplikacji IoT Central:

1. Przejdź do sekcji **Administracja** w aplikacji.
1. Wybierz pozycję **Eksportowanie szablonów aplikacji**.
1. Na stronie **Eksportowanie szablonu aplikacji** wprowadź nazwę i opis szablonu.
1. Wybierz przycisk **Eksportuj,** aby utworzyć szablon aplikacji. Teraz można skopiować **łącze do udostępniania,** które umożliwia innej osobie tworzenie nowej aplikacji z szablonu:

![Tworzenie szablonu aplikacji](media/howto-use-app-templates/create-template.png)

### <a name="use-an-application-template"></a>Używanie szablonu aplikacji

Aby utworzyć nową aplikację IoT Central za pomocą szablonu aplikacji, potrzebne jest wcześniej utworzone **łącze współużytkowania.** Wklej **link do udostępniania** na pasku adresu przeglądarki. Strona **Utwórz aplikację** jest wyświetlana z wybranym szablonem aplikacji niestandardowej:

![Tworzenie aplikacji na podstawie szablonu](media/howto-use-app-templates/create-app.png)

Wybierz plan cenowy i wypełnij pozostałe pola w formularzu. Następnie wybierz **pozycję Utwórz,** aby utworzyć nową aplikację IoT Central z szablonu aplikacji.

### <a name="manage-application-templates"></a>Zarządzanie szablonami aplikacji

Na stronie **Eksportowanie szablonu aplikacji** można usunąć lub zaktualizować szablon aplikacji.

Jeśli usuniesz szablon aplikacji, nie można już używać wcześniej wygenerowanego łącza współużytkowania do tworzenia nowych aplikacji.

Aby zaktualizować szablon aplikacji, zmień nazwę lub opis szablonu na stronie **Eksportowanie szablonu aplikacji.** Następnie ponownie wybierz przycisk **Eksportuj.** Ta akcja generuje nowe **łącze udostępniania** i unieważnia poprzedni adres URL **łącza udostępnianego.**

## <a name="next-steps"></a>Następne kroki

Teraz, gdy już wiesz, jak korzystać z szablonów aplikacji, sugerowanym następnym krokiem jest dowiedzenie się, jak [zarządzać IoT Central z witryny Azure portal](howto-manage-iot-central-from-portal.md)
