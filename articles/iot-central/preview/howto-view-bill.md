---
title: Zarządzaj rachunkiem i Konwertuj wersję próbną na płatność zgodnie z rzeczywistym użyciem w aplikacji Azure IoT Central | Microsoft Docs
description: Skontaktuj się z administratorem, aby dowiedzieć się, jak zarządzać rachunkiem i przekonwertować z wersji próbnej, aby zalogować się w aplikacji IoT Central platformy Azure
author: v-krghan
ms.author: v-krghan
ms.date: 07/26/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 11bcdaab34cf4d82b688a578ab2d9ee686ffe07e
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/09/2019
ms.locfileid: "73894814"
---
# <a name="manage-your-bill-in-an-iot-central-application"></a>Zarządzanie rozliczeniami w aplikacji IoT Central

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

W tym artykule opisano, jak administrator może zarządzać rozliczeniami w aplikacji platformy Azure IoT Central w sekcji Administracja, a także w przypadku konwersji wersji próbnej na płatność zgodnie z rzeczywistym użyciem.

Aby uzyskać dostęp do sekcji **Administracja** i korzystać z niej, musisz mieć rolę **administratora** dla aplikacji IoT Central platformy Azure. Jeśli utworzysz aplikację IoT Central platformy Azure, zostanie ona automatycznie przypisana do roli **administratora** dla tej aplikacji.

## <a name="view-your-bill"></a>Wyświetlanie rachunku

Aby wyświetlić rachunek, przejdź do strony **rozliczeń** w sekcji **Administracja** . Na nowej karcie zostanie otwarta strona rozliczenia platformy Azure, w której będzie widoczny rachunek dla każdej aplikacji IoT Central platformy Azure.

## <a name="convert-your-trial-to-pay-as-you-go"></a>Konwertuj wersję próbną na płatność zgodnie z rzeczywistym użyciem

- **Wersje próbne** aplikacji są bezpłatne przez siedem dni przed ich wygaśnięciem. W dowolnym momencie przed wygaśnięciem można przekonwertować te aplikacje na typ Płatność zgodnie z rzeczywistym użyciem.
- W przypadku aplikacji obsługujących **płatność zgodnie z rzeczywistym** użyciem opłaty są naliczone za urządzenie, a pierwsze pięć urządzeń jest bezpłatnych dla każdej subskrypcji.

Więcej informacji o cenach znajdziesz na [stronie cennika usługi Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/).

W sekcji dotyczącej rozliczeń możesz przekonwertować aplikacje próbne na płatność zgodnie z rzeczywistym użyciem.

Aby ukończyć ten proces samoobsługowy, wykonaj następujące kroki:

1. Przejdź do strony **rozliczeń** w sekcji **Administracja** .

    ![Stan wersji próbnej](media/howto-view-bill/freetrialbilling.png)

1. Wybierz pozycję **Konwertuj na płatność zgodnie z rzeczywistym**użyciem.

    ![Konwertuj wersję próbną](media/howto-view-bill/convert.png)

1. Wybierz odpowiednie Azure Active Directory, a następnie subskrypcję platformy Azure, która ma zostać użyta w przypadku aplikacji z opcją płatność zgodnie z rzeczywistym użyciem.

1. Po wybraniu opcji **Konwertuj**Twoja aplikacja jest teraz aplikacją z płatnością zgodnie z rzeczywistym użyciem i zaczniesz otrzymywać opłaty.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak zarządzać rozliczeniami w aplikacji IoT Central platformy Azure, sugerowanym następnym krokiem jest zapoznanie się z tematem [Dostosowywanie interfejsu użytkownika aplikacji](howto-customize-ui.md) na platformie Azure IoT Central.