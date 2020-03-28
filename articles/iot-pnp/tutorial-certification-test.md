---
title: Certyfikuj urządzenie IoT Plug and Play Preview | Dokumenty firmy Microsoft
description: W tym samouczku opisano sposób dodawania informacji o produkcie do wykazu urządzeń certyfikatu platformy Azure dla IoT, łączenia urządzenia z usługą certyfikacji Usługi Azure IoT, a następnie uruchamiania testów certyfikacji IoT Plug and Play.
manager: philmea
ms.service: iot-pnp
services: iot-pnp
ms.topic: tutorial
ms.author: koichih
author: konichi3
ms.date: 12/27/2019
ms.openlocfilehash: dc2c33659f3f3a3df0f11fcc6ab36a9fc993da43
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75550166"
---
# <a name="tutorial-certify-your-iot-plug-and-play-preview-device"></a>Samouczek: Certyfikuj urządzenie IoT Plug and Play Preview

Aby opublikować urządzenie IoT Plug and Play Preview w [katalogu urządzeń certyfikatu platformy Azure dla IoT,](https://aka.ms/iotdevcat)musi przejść zestaw testów certyfikacyjnych. Użyj portalu [Certyfikat platformy Azure dla IoT,](https://aka.ms/ACFI) aby przesłać urządzenie do certyfikacji. [Usługa certyfikacji Usługi Azure IoT](https://aka.ms/azure-iot-aics) uruchamia testy certyfikacji.

W tym samouczku dowiesz się:

> [!div class="checklist"]
> * Jakie są wymagania certyfikacji IoT Plug and Play.
> * Jak dodać nazwę produktu i informacje do portalu.
> * Jak połączyć i odkryć interfejsy IoT Plug and Play.
> * Jak przejrzeć interfejsy IoT Plug and Play i przeprowadzić testy certyfikacyjne.
> * Jak opublikować certyfikowane urządzenie IoT Plug and Play w katalogu.

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka niezbędne są następujące elementy:

* Konto Centrum partnerów firmy Microsoft.
* Identyfikator sieci partnerów firmy Microsoft.

Aby uzyskać więcej informacji, zobacz Jak dołączać do portalu [Certyfikat platformy Azure dla IoT.](howto-onboard-portal.md)

## <a name="certification-requirements"></a>Wymagania dotyczące certyfikacji

Aby certyfikować urządzenie IoT Plug and Play, urządzenie musi spełniać następujące wymagania:

* Kod urządzenia IoT Plug and Play musi być zainstalowany na urządzeniu.
* Twój kod urządzenia IoT Plug and Play są tworzone za pomocą usługi Azure IoT SDK.
* Kod urządzenia musi obsługiwać [usługę inicjowania obsługi administracyjnej urządzeń usługi Azure IoT Hub.](../iot-dps/about-iot-dps.md)
* Kod urządzenia musi być zaimplementowany [w interfejsie informacji o urządzeniu.](concepts-common-interfaces.md)
* Model możliwości i kod urządzenia działają z IoT Central.

Wszystkie urządzenia aktualnie w katalogu są uważane za wstępnie certyfikowane urządzenia IoT Plug and Play. Nie gwarantuje jakości i zgodności produktu końcowego składników oprogramowania IoT Plug and Play, takich jak SDK i Digital Twin Definition Language.

Wszystkie wstępnie certyfikowane urządzenia IoT Plug and Play muszą ponownie potwierdzić ogólną dostępność usługi IoT Plug and Play na podstawie ostatecznej wersji wymagań certyfikacyjnych i składników oprogramowania dostarczonych przez firmę Microsoft.

## <a name="add-product-name"></a>Dodaj nazwę produktu

**Produkt** to przyjazna nazwa modelu produktu, który klient może kupić. Aby dodać produkt:

1. Zaloguj się do portalu [certyfikatu platformy Azure dla IoT.](https://aka.ms/ACFI)
1. Przejdź do strony **Produkty** w portalu z lewego menu i wybierz **+ Nowy**.
1. Wprowadź przyjazną nazwę produktu i wybierz pozycję **Utwórz**. Nazwa wprowadzona w tym miejscu różni się od nazwy wyświetlanej w katalogu urządzeń.

## <a name="add-product-information"></a>Dodawanie informacji o produkcie

Po pomyślnym utworzeniu produktu w portalu produkt jest wyświetlany na stronie **Produkty.** Aby dodać informacje o produkcie:

1. Wybierz utworzone łącze produktu znajdujące się na stronie **Produkt** w kolumnie produkt. Państwo powinno być w stanie roboczym.
1. Wybierz **łącze Edytuj** obok pozycji **Informacje o produkcie.** Wprowadź informacje o produkcie na stronie informacji o produkcie i upewnij się, że wszystkie wymagane pola zostały ukończone.
1. Wybierz **pozycję Zapisz**. Przycisk **Zapisz** pojawia się tylko po zakończeniu wszystkich wymaganych pól. Jeśli pola są niekompletne, przycisk **"Zapisz i zakończ później"**
1. Przejrzyj wprowadzona zawartość. Wypełnij wszystkie pola wymagane do opublikowania urządzenia w katalogu urządzeń. Zawsze możesz wrócić, aby wprowadzić zmiany w informacjach o produkcie na stronie szczegółów produktu, klikając łącze **do edycji** obok **nagłówka Informacje o produkcie.**

## <a name="connect-and-discover-interfaces"></a>Łączenie i odkrywanie interfejsów

Aby uruchomić testy certyfikacyjne, podłącz urządzenie do [usługi certyfikacji Azure IoT](https://aka.ms/azure-iot-aics) (AICS), która jest dostępna w portalu.

Te kroki są krokiem jednorazowym do uruchamiania testów certyfikacyjnych i nie jest konieczne, aby zmienić kod urządzenia produktu. Aby rozpocząć, wykonaj następujące kroki, aby połączyć się z AICS:

1. Zaloguj się do portalu przy użyciu konta Centrum partnerów.
1. Kliknij **przycisk Połącz + test,** aby rozpocząć przepływ certyfikacji.
1. Wybierz [metodę uwierzytelniania,](../iot-dps/concepts-security.md#attestation-mechanism) aby aprowizować urządzenie do usługi AICS przy użyciu [usługi inicjowania obsługi administracyjnej urządzeń usługi Azure IoT Hub.](../iot-dps/about-iot-dps.md)
   * Jeśli używasz [certyfikatu X.509,](../iot-hub/iot-hub-security-x509-get-started.md#prerequisites)przekaż wygenerowany certyfikat X.509. Możesz przejrzeć przykładowy kod, który pokazuje, jak używać certyfikatów X.509: [C](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/samples/iothub_ll_client_x509_sample/iothub_ll_client_x509_sample.c), [C#](../iot-hub/iot-hub-security-x509-get-started.md).
   * Jeśli używasz [klucza symetrycznego,](../iot-dps/concepts-symmetric-key-attestation.md)skopiuj i wklej klucz symetryczny do kodu urządzenia.
   * Metoda uwierzytelniania modułu TPM nie jest obecnie obsługiwana.
1. Skopiuj i wklej następujące wygenerowane identyfikatory do kodu urządzenia.
   * [Identyfikator rejestracji](../iot-dps/use-hsm-with-sdk.md)
   * [Identyfikator DPS](../iot-dps/tutorial-set-up-device.md#create-the-device-registration-software)
   * [Punkt końcowy DPS](../iot-dps/tutorial-set-up-device.md#create-the-device-registration-software)
1. Gdy kod urządzenia jest zbudowany i wdrożony na urządzeniu, wybierz pozycję **Połącz,** aby odkryć interfejsy IoT Plug and Play.
1. Gdy połączenie z usługą AICS zakończy się pomyślnie, wybierz **opcję Dalej,** aby przejrzeć wykryte interfejsy IoT Plug and Play.

## <a name="run-tests-and-publish-the-device"></a>Uruchamianie testów i publikowanie urządzenia

Na stronie recenzji można przejrzeć odnalezione interfejsy IoT Plug and Play. Ta strona służy do sprawdzania ujednoliconych implementacji w ekranie interfejsu poprawnie. Można również sprawdzić lokalizację interfejsu.

1. Upewnij się, że wprowadzone są dane wejściowe ładunku dla wymaganych pól. Pola te zawierają informacje o ładunku dla polecenia prymitywnego dla określonego interfejsu.
1. Po wprowadzeniu wszystkich wymaganych informacji wybierz przycisk **Dalej**.
1. Aby uruchomić testy zaimplementowanych interfejsów IoT Plug and Play, wybierz opcję **Uruchom testy**.
1. Wszystkie testy są uruchamiane automatycznie. Jeśli jakikolwiek test zakończy się niepowodzeniem, wybierz **pozycję Wyświetl dzienniki,** aby wyświetlić komunikaty o błędach z usługi AICS i nieprzetworzone dane telemetryczne wysyłane do usługi Azure IoT Hub.
1. Aby ukończyć testy certyfikacyjne, wybierz **zakończ**.
1. Opublikuj certyfikowane urządzenie IoT Plug and Play w katalogu. Aby dodać certyfikowane urządzenie do katalogu, wybierz pozycję **Dodaj do katalogu** na pasku narzędzi. Jeśli **katalog Dodaj do** jest wyszarzony, oznacza to, że informacje o produkcie są niekompletne lub testy nie powiodły się. 
1. Wybierz link "CERTYFIKOWANE I W KATALOGU", aby wyświetlić opublikowane urządzenie w katalogu urządzeń.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy dowiedziałeś się o certyfikowaniu urządzenia IoT Plug and Play, sugerowanym następnym krokiem jest dowiedzenie się więcej o zarządzaniu modelami możliwości:

> [!div class="nextstepaction"]
> [Zarządzanie modelami](./howto-manage-models.md)
