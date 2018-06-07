---
title: Opis wersji szablonu urządzenia do pracy z aplikacjami Azure IoT centralnej | Dokumentacja firmy Microsoft
description: Iteracja szablonów urządzenia, tworząc nowe wersje i bez wpływu na urządzenia połączenia na żywo
author: sandeeppujar
ms.author: sandeepu
ms.date: 01/19/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: b125d822596675b138560c14c76f9a3120ce3424
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34628831"
---
# <a name="create-a-new-device-template-version"></a>Utwórz nową wersję szablonu urządzenia

Centrum IoT Azure Microsoft umożliwia szybkie opracowywanie aplikacji IoT. Projekty szablonu urządzenia można szybko iteracja przez dodawanie, edytowanie lub usuwanie pomiarów, ustawień lub właściwości. Niektóre z tych zmian można niepożądanych dla aktualnie podłączonego urządzenia. Azure IoT centralnego identyfikuje tych zmian, które psuły i umożliwia bezpieczne wdrażanie tych aktualizacji do urządzeń.

Szablon urządzenie ma numer wersji podczas jego tworzenia. Domyślnie numer wersji jest 1.0.0. Edytowanie szablonu urządzenia, a zmiana może mieć wpływ na żywo połączonych urządzeń, Azure IoT centralnej monit o utworzenie nowej wersji szablonu urządzenia.

> [!NOTE]
> Aby dowiedzieć się więcej o tym, jak utworzyć szablon urządzenia, zobacz [skonfigurować szablon urządzenia](howto-set-up-template.md)

## <a name="changes-that-prompt-a-version-change"></a>Zmiany, które monit o zmianę wersji

Ogólnie rzecz biorąc zmian ustawień lub właściwości szablonu urządzenia monit o zmianę wersji.

> [!NOTE]
> Zmiany wprowadzone w szablonie urządzenia nie Monituj w celu utworzenia nowej wersji, gdy urządzenie nie lub na większość jedno urządzenie jest podłączone.

Poniżej opisano akcje użytkownika, które mogą wymagać nowej wersji:

* Właściwości (wymagane)
    * Dodawanie lub usuwanie wymaganej właściwości
    * Zmiana nazwy pola, właściwości, nazwy pola, które jest używane przez urządzenia do wysyłania wiadomości.
*  Właściwości (opcjonalnie)
    * Usuwanie opcjonalna właściwość
    * Zmiana nazwy pola, właściwości, nazwy pola, które jest używane przez urządzenia do wysyłania wiadomości.
    * Zmiana właściwości opcjonalne do wymaganej właściwości
*  Ustawienia
    * Dodawanie lub usuwanie ustawienie
    * Zmiana nazwy pola ustawienie, nazwy pola, które jest używane przez urządzenia do wysyłania i odbierania wiadomości.

## <a name="what-happens-on-version-change"></a>Co się stanie w przypadku zmiany wersji?

Co się dzieje reguł i pulpity nawigacyjne urządzenia po zmianie wersji?

**Reguły** mogą zawierać warunki, które są zależne od właściwości. Jeśli usunięto co najmniej jeden z tych właściwości, te reguły może być podzielone w nowej wersji szablonu urządzenia. Można przejdź do tych określone zasady i zaktualizuj warunki ustalenie zasad. Zasady dla poprzedniej wersji powinny działać bez wpływu.

**Pulpity nawigacyjne urządzenia** może zawierać kilka typów kafelków. Niektóre z kafelków mogą zawierać ustawienia i właściwości. Usunięcie właściwości lub ustawienie używane na kafelku Kafelek został przerwany pełni lub częściowo. Można przejść do fragmentu i rozwiąż problem przez usunięcie kafelka albo aktualizowanie zawartości fragmentu.

## <a name="migrate-a-device-across-device-template-versions"></a>Migracja urządzenia między wersji szablonu urządzenia

Możesz utworzyć wiele wersji szablonu urządzenia. Wraz z upływem czasu będziesz mieć wielu urządzeń podłączonych przy użyciu tych szablonów urządzenia. Urządzenia można migrować z jednej wersji szablonu urządzenia do innego. W poniższych krokach opisano sposób migracji urządzenia:

1. Przejdź do **Explorer** strony.
1. Wybierz urządzenie, które należy migrować do innej wersji.
1. Wybierz **migracji urządzenia**.
1. Wybierz numer wersji, aby migrować urządzenia i wybierz polecenie **migracji**.

![Jak przeprowadzić migrację urządzenia](media\howto-version-devicetemplate\pick-version.png)

## <a name="next-steps"></a>Kolejne kroki

Teraz, kiedy znasz sposób użycia wersji szablonu urządzenia w aplikacji Azure IoT centralnej, Oto sugerowane następnego kroku:

> [!div class="nextstepaction"]
> [Jak utworzyć zasady telemetrii](howto-create-telemetry-rules.md)