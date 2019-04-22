---
title: Informacje o urządzeniu wersji szablonu dla Twoich aplikacji w usłudze Azure IoT Central | Dokumentacja firmy Microsoft
description: Iteracja szablonów urządzenia, tworząc nowe wersje i bez wywierania wpływu na żywo połączonych urządzeń
author: sandeeppujar
ms.author: sandeepu
ms.date: 03/26/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: d4f9617a5c2ba6f6cf8dc261845aa98e33d70a55
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59281781"
---
# <a name="create-a-new-device-template-version"></a>Utwórz nową wersję szablonu urządzenia

Usługa Azure IoT Central umożliwia szybkie opracowywanie aplikacji IoT. Projekty szablonu urządzenia można szybko iteracji poprzez dodawanie, edytowanie lub usuwanie pomiarów, ustawienia lub właściwości. Niektóre z tych zmian może być istotnych dla aktualnie połączonych urządzeń. Usługa Azure IoT Central identyfikuje te przełomowe zmiany i zapewnia sposób bezpiecznie wdrożyć aktualizacje na urządzeniach.

Szablon urządzenia ma numer wersji, podczas jego tworzenia. Domyślnie numer wersji to 1.0.0. Edytuj szablon urządzenia, a ta zmiana może mieć wpływ na live połączonych urządzeń, usługi Azure IoT Central monit o utworzenie nowej wersji szablonu urządzenia.

> [!NOTE]
> Aby dowiedzieć się więcej na temat sposobu tworzenia szablonu urządzenia zobacz [Konfigurowanie szablonu urządzenia](howto-set-up-template.md)

## <a name="changes-that-prompt-a-version-change"></a>Zmiany, które monit o zmianę wersji

Ogólnie rzecz biorąc zmian ustawień lub właściwości szablonu urządzenia monit o zmianę wersji.

> [!NOTE]
> Zmiany wprowadzone w szablonie urządzenia nie Monituj o utworzenie nowej wersji, gdy urządzenie nie lub na większości jedno urządzenie jest podłączone.

Na poniższej liście opisano akcje użytkownika, których może wymagać nowej wersji:

* Właściwości (wymagana)
    * Dodawanie lub usuwanie wymaganej właściwości
    * Zmiana nazwy pola, właściwości, nazwy pola, który jest używany przez urządzenia do wysyłania wiadomości.
*  Właściwości (opcjonalnie)
    * Usuwanie opcjonalną właściwością
    * Zmiana nazwy pola, właściwości, nazwy pola, który jest używany przez urządzenia do wysyłania wiadomości.
    * Zmiana opcjonalna właściwość wymaganej właściwości
*  Ustawienia
    * Dodawanie lub usuwanie ustawienia
    * Zmiana nazwy pola ustawienie, nazwy pola, który jest używany przez urządzenia do wysyłania i odbierania komunikatów.

## <a name="what-happens-on-version-change"></a>Co się stanie w przypadku zmiany wersji?

Co się dzieje reguł i pulpitów nawigacyjnych z urządzenia po zmianie wersji?

**Reguły** może zawierać warunki, które są zależne od właściwości. Jeśli usunięto co najmniej jeden z tych właściwości, te reguły można zaburzyć w nowej wersji szablonu urządzenia. Można przejść do tych określone zasady i aktualizować warunki, aby rozwiązać reguły. Reguły dla poprzedniej wersji powinny współpracować z żadnego wpływu.

**Pulpity nawigacyjne urządzenia** może zawierać kilka typów kafelków. Niektóre z kafelków mogą zawierać, ustawień i właściwości. Usunięcie właściwości lub ustawienie używane we fragmencie Kafelek został w pełni lub częściowo przerwany. Można przejść do kafelka i rozwiązać ten problem przez usunięcie kafelka albo aktualizowania zawartość kafelka.

## <a name="migrate-a-device-across-device-template-versions"></a>Migrowanie urządzenia między wersje szablonów urządzenia

Możesz utworzyć wiele wersji szablonu urządzenia. Wraz z upływem czasu będziesz mieć wiele połączonych urządzeń, za pomocą tych szablonów urządzenia. Urządzenia można migrować z jednej wersji szablonu urządzenia do innego. W poniższych krokach opisano sposób migrowania urządzenia:

1. Przejdź do **Device Explorer** strony.
1. Wybierz urządzenie, którego chcesz przeprowadzić migrację do innej wersji.
1. Wybierz **migracji urządzenia**.
1. Wybierz numer wersji, aby migrować urządzenia, a następnie wybierz **migracji**.

![Jak przeprowadzić migrację na urządzeniu](media/howto-version-devicetemplate/pick-version.png)

## <a name="next-steps"></a>Kolejne kroki

Teraz, gdy wiesz jak używać wersji szablonów urządzenia w aplikacji usługi Azure IoT Central, poniżej przedstawiono sugerowany następnego kroku:

> [!div class="nextstepaction"]
> [Jak utworzyć zasady telemetrii](howto-create-telemetry-rules.md)