---
title: Zarządzanie rachunkiem i konwersja z bezpłatnego planu cenowego w aplikacji Azure IoT Central | Dokumenty firmy Microsoft
description: Jako administrator dowiedz się, jak zarządzać rachunkiem i przechodzić od bezpłatnego planu cenowego do standardowego planu cenowego w aplikacji Azure IoT Central
author: v-krghan
ms.author: v-krghan
ms.date: 12/09/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 888b9f31b8082ac124a773cc489293b335d7d255
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80157523"
---
# <a name="manage-your-bill-in-an-iot-central-application"></a>Zarządzanie rachunkiem w aplikacji IoT Central

W tym artykule opisano, jak jako administrator możesz zarządzać rachunkiem w aplikacji Azure IoT Central w sekcji administracyjnej. Dowiesz się, jak przenieść aplikację z bezpłatnego planu cenowego do standardowego planu cenowego, a także jak uaktualnić lub obniżyć plan cenowy.

Aby uzyskać dostęp do sekcji **Administracja** i korzystać z niej, musisz być w roli *administratora* lub mieć *niestandardową rolę użytkownika,* która umożliwia wyświetlanie rozliczeń dla aplikacji Azure IoT Central. Jeśli tworzysz aplikację Azure IoT Central, automatycznie zostaniesz przypisany do roli **administratora** dla tej aplikacji.

## <a name="move-from-free-to-standard-pricing-plan"></a>Przejście od bezpłatnego do standardowego planu cenowego

- Aplikacje korzystające z bezpłatnego planu cenowego są bezpłatne przez siedem dni przed ich wygaśnięciem. Aby uniknąć utraty danych, możesz przenieść je do standardowego planu cenowego w dowolnym momencie przed ich wygaśnięciem.
- Aplikacje korzystające ze standardowego planu cenowego są naliczane za urządzenie, z dwoma pierwszymi urządzeniami za darmo, dla aplikacji.

Więcej informacji o cenach znajdziesz na [stronie cennika usługi Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/).

W sekcji cennik możesz przenieść aplikację z bezpłatnego do standardowego planu cenowego.

Aby zakończyć ten proces samoobsługi, wykonaj następujące kroki:

1. Przejdź do strony **Cennik** w sekcji **Administracja.**

    ![Stan próby](media/howto-view-bill/freetrialbilling.png)

1. Wybierz **opcję Konwertuj na płatny plan**.

    ![Konwertowanie wersji próbnej](media/howto-view-bill/convert.png)

1. Wybierz odpowiednią usługę Azure Active Directory, a następnie subskrypcję platformy Azure do użycia dla aplikacji korzystającej z płatnego planu.

1. Po **wybraniu opcji Konwertuj**aplikacja korzysta teraz z płatnego planu i zaczynasz otrzymywać naliczone rachunki.

> [!Note]
> Domyślnie użytkownik jest konwertowany na plan cenowy *Standard 2.*

## <a name="how-to-change-your-application-pricing-plan"></a>Jak zmienić plan cenowy aplikacji

Aplikacje korzystające ze standardowego planu cenowego są naliczane za urządzenie, z dwoma pierwszymi urządzeniami za darmo, dla aplikacji.

W sekcji cen można uaktualnić lub obniżyć swój plan cenowy Usługi Azure IoT w dowolnym momencie.

1. Przejdź do strony **Cennik** w sekcji **Administracja.**

    ![Stan próby](media/howto-view-bill/pricing.png)

1. Wybierz **opcję Planuj** i kliknij przycisk **Zapisz,** aby uaktualnić lub obniżyć wersję.

## <a name="view-your-bill"></a>Wyświetlanie rachunku

1. Wybierz odpowiednią usługę Azure Active Directory, a następnie subskrypcję platformy Azure do użycia dla aplikacji korzystającej z płatnego planu.

1. Po **wybraniu opcji Konwertuj**aplikacja korzysta teraz z płatnego planu i zaczynasz otrzymywać naliczone rachunki.

> [!Note]
> Domyślnie użytkownik jest konwertowany na plan cenowy *Standard 2.*

## <a name="next-steps"></a>Następne kroki

Teraz, gdy dowiesz się, jak zarządzać rachunkiem w aplikacji Azure IoT Central, sugerowanym następnym krokiem jest zapoznanie się z [dostosowywania interfejsu użytkownika aplikacji](howto-customize-ui.md) w usłudze Azure IoT Central.