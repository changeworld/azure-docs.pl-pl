---
title: Informacje o wersji szablonów urządzeń dla aplikacji IoT Central platformy Azure | Microsoft Docs
description: Wykonaj iterację szablonów urządzeń, tworząc nowe wersje i bez wpływu na urządzenia połączone na żywo
author: sandeeppujar
ms.author: sandeepu
ms.date: 07/08/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 155f392410c5722a28ba09acafc1480e72586773
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70100903"
---
# <a name="create-a-new-device-template-version"></a>Utwórz nową wersję szablonu urządzenia

[!INCLUDE [iot-central-original-pnp](../../includes/iot-central-original-pnp-note.md)]

Usługa Azure IoT Central umożliwia szybkie opracowywanie aplikacji IoT. Możesz szybko wykonać iterację w projektach szablonów urządzeń, dodając, edytując lub usuwając pomiary, ustawienia lub właściwości. Niektóre z tych zmian mogą być inwazyjne dla aktualnie połączonych urządzeń. Usługa Azure IoT Central identyfikuje te istotne zmiany i umożliwia bezpieczne wdrażanie tych aktualizacji na urządzeniach.

Szablon urządzenia ma numer wersji podczas jego tworzenia. Domyślnie numer wersji to 1.0.0. Jeśli edytujesz szablon urządzenia, a zmiana ta mogłaby mieć wpływ na urządzenia połączone na żywo, usługa Azure IoT Central poprosi o utworzenie nowej wersji szablonu urządzenia.

> [!NOTE]
> Aby dowiedzieć się więcej na temat tworzenia szablonu urządzenia, zobacz [Konfigurowanie szablonu urządzenia](howto-set-up-template.md)

## <a name="changes-that-prompt-a-version-change"></a>Zmiany, które monitują o zmianę wersji

W obszarze Ogólne zmiany ustawień lub właściwości szablonu urządzenia zostanie wyświetlony monit o zmianę wersji.

> [!NOTE]
> Zmiany wprowadzone w szablonie urządzenia nie monitują o utworzenie nowej wersji, gdy nie jest połączone żadne urządzenie lub co najmniej jedno urządzenie.

Poniższa lista zawiera opis akcji użytkownika, które mogą wymagać nowej wersji:

* Właściwości (wymagane)
    * Dodawanie lub usuwanie wymaganej właściwości
    * Zmiana nazwy pola właściwości, nazwy pola używanej przez urządzenia do wysyłania komunikatów.
*  Właściwości (opcjonalnie)
    * Usuwanie właściwości opcjonalnej
    * Zmiana nazwy pola właściwości, nazwy pola używanej przez urządzenia do wysyłania komunikatów.
    * Zmiana właściwości opcjonalnej na wymaganą Właściwość
*  Ustawienia
    * Dodawanie lub usuwanie ustawienia
    * Zmiana nazwy pola ustawienia, nazwy pola używanego przez urządzenia do wysyłania i odbierania komunikatów.

## <a name="what-happens-on-version-change"></a>Co się stanie w przypadku zmiany wersji?

Co się stanie z regułami i pulpitami nawigacyjnymi urządzeń w przypadku zmiany wersji?

**Reguły** w poprzedniej wersji szablonu urządzenia nadal pracują bez zmian. Reguły nie są automatycznie migrowane do nowej wersji szablonu urządzenia. Możesz tworzyć reguły dla nowej wersji szablonu w zwykły sposób. Aby uzyskać więcej informacji, zobacz artykuł dotyczący [tworzenia reguły telemetrii i konfigurowania powiadomień w aplikacji IoT Central platformy Azure](howto-create-telemetry-rules.md) .

**Pulpity nawigacyjne urządzeń** mogą zawierać kilka typów kafelków. Niektóre kafelki mogą zawierać ustawienia i właściwości. Gdy właściwość lub ustawienie użyte w kafelku zostanie usunięte, kafelek jest całkowicie lub częściowo uszkodzony. Możesz przejść do kafelka i rozwiązać problem, usuwając kafelek lub aktualizując zawartość kafelka.

## <a name="migrate-a-device-across-device-template-versions"></a>Migrowanie urządzenia w różnych wersjach szablonów urządzeń

Można utworzyć wiele wersji szablonu urządzenia. Z biegiem czasu będziesz mieć wiele połączonych urządzeń korzystających z tych szablonów urządzeń. Możliwe jest Migrowanie urządzeń z jednej wersji szablonu urządzenia do innej. W poniższych krokach opisano sposób migrowania urządzenia:

1. Przejdź do strony **Device Explorer** .
1. Wybierz urządzenie, które ma być migrowane do innej wersji.
1. Wybierz kolejno pozycje **Migruj urządzenie**.
1. Wybierz numer wersji, do której chcesz przeprowadzić migrację urządzenia, a następnie wybierz pozycję **Migruj**.

![Jak migrować urządzenie](media/howto-version-device-template/pick-version.png)

## <a name="next-steps"></a>Następne kroki

Teraz, kiedy wiesz już, jak używać wersji szablonów urządzeń w aplikacji IoT Central platformy Azure, Oto sugerowany następny krok:

> [!div class="nextstepaction"]
> [Jak utworzyć reguły telemetrii](howto-create-telemetry-rules.md)