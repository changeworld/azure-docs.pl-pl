---
title: Zarządzaj rachunkiem i Konwertuj z bezpłatnego planu cenowego w aplikacji Azure IoT Central | Microsoft Docs
description: Skontaktuj się z administratorem, aby dowiedzieć się, jak zarządzać rozliczeniami i przechodzić z planu cen bezpłatnych do standardowego planu cenowego w aplikacji IoT Central platformy Azure
author: v-krghan
ms.author: v-krghan
ms.date: 07/26/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: e45109842a081b437c20de81321bf70f909efa2a
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/04/2020
ms.locfileid: "76982367"
---
# <a name="view-your-bill-in-iot-central-application"></a>Wyświetlanie rachunku IoT Central aplikacji

W tym artykule opisano, jak administrator może zarządzać rozliczeniami w aplikacji IoT Central platformy Azure w sekcji Administracja. Dowiesz się, jak przenieść aplikację z bezpłatnego planu cenowego do standardowego planu cenowego, a także jak uaktualnić lub obniżyć plan cenowy.

Aby uzyskać dostęp do sekcji **Administracja** i korzystać z niej, musisz mieć rolę *administratora* lub mieć *niestandardową rolę użytkownika* , która umożliwia wyświetlanie rozliczeń dla aplikacji IoT Central platformy Azure. Jeśli utworzysz aplikację IoT Central platformy Azure, zostanie ona automatycznie przypisana do roli **administratora** dla tej aplikacji.

## <a name="move-from-free-to-standard-pricing-plan"></a>Przejdź od bezpłatnej do standardowego planu cen

- Aplikacje korzystające z bezpłatnego planu cenowego są bezpłatne przez siedem dni przed ich wygaśnięciem. Aby uniknąć utraty danych, można przenieść je do standardowego planu cenowego w dowolnym momencie przed wygaśnięciem.

W sekcji Cennik możesz przenieść aplikację z bezpłatnej do standardowego planu cenowego.

Aby ukończyć ten proces samoobsługowy, wykonaj następujące kroki:

1. Przejdź do strony **cennika** w sekcji **Administracja** .

    ![Stan wersji próbnej](media/howto-administer/freetrialbilling.png)

1. Wybierz pozycję **Konwertuj na płatny plan**.

    ![Konwertuj wersję próbną](media/howto-administer/convert.png)

1. Wybierz odpowiednie Azure Active Directory, a następnie subskrypcję platformy Azure, która ma być używana dla aplikacji korzystającej z płatnego planu.

1. Po wybraniu opcji **Konwertuj**Twoja aplikacja korzysta z planu płatnego, a opłaty są naliczane.

> [!Note]
> Domyślnie jest konwertowany na plan cenowy w *warstwie Standardowa 2* .

## <a name="how-to-change-your-application-pricing-plan"></a>Jak zmienić plan cenowy aplikacji

- W przypadku aplikacji korzystających ze standardowego planu cenowego są naliczone opłaty za każde urządzenie, a pierwsze dwa urządzenia są bezpłatne dla każdej aplikacji.

W sekcji Cennik można w dowolnym momencie uaktualnić lub obniżyć plan cen usługi Azure IoT.

1. Przejdź do strony **cennika** w sekcji **Administracja** .

    ![Stan wersji próbnej](media/howto-administer/pricing.png)

1. Wybierz **Plan** , a następnie kliknij przycisk **Zapisz** w celu uaktualnienia lub obniżenia poziomu.

Więcej informacji o cenach znajdziesz na [stronie cennika usługi Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/).

## <a name="view-your-bill"></a>Wyświetlanie rachunku

Aby wyświetlić rachunek, przejdź do strony **cennika** w sekcji **Administracja** . Na stronie z cennikiem platformy Azure zostanie otwarta nowa karta, w której będzie widoczny rachunek dla każdej aplikacji IoT Central platformy Azure.


## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak wyświetlić rachunek w aplikacji Azure IoT Central, sugerowanym następnym krokiem jest zapoznanie się z tematem [Dostosowywanie interfejsu użytkownika aplikacji](howto-customize-ui.md) w usłudze Azure IoT Central.