---
title: Speech Devices SDK roobo Smart audio dev Kit V1-Speech Service
titleSuffix: Azure Cognitive Services
description: Wymagania wstępne i instrukcje dotyczące rozpoczynania pracy z zestawem SDK usługi Speech Devices, roobo Smart audio dev Kit v1.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 08bac7cd833f52d2dfec4561c2f87330a4119748
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68552865"
---
# <a name="device-roobo-smart-audio-dev-kit"></a>Urządzenie: Roobo Smart audio dev Kit

Ten artykuł zawiera informacje dotyczące urządzeń dla zestawu roobo Smart audio dev Kit.

## <a name="set-up-the-development-kit"></a>Skonfiguruj zestaw development kit

1. Zestaw deweloperski ma dwa łączniki Micro USB. Lewy łącznik to Włączanie zestawu deweloperskiego i został wyróżniony jako energia na poniższym obrazie. Po prawej stronie jest on kontrolowany i jest oznaczony jako debugowanie w obrazie.

    ![Łączenie zestawu dla deweloperów](media/speech-devices-sdk/qsg-1.png)

1. Włącz zestaw deweloperski przy użyciu kabla micro USB, aby podłączyć port do komputera lub zasilacza. Zielony wskaźnik napięcia zostanie wyróżniony w górnej części tablicy.

1. Aby sterować zestawem deweloperskim, Podłącz port debugowania do komputera przy użyciu drugiego kabla micro USB. W celu zapewnienia niezawodnej komunikacji należy użyć kabla wysokiej jakości.

1. Orientacja Twojego zestawu SDK dla dowolnego cykliczne lub liniowy konfiguracji.

    |Tworzenie konfiguracji zestawu|Orientacja|
    |-----------------------------|------------|
    |Cykliczne|Prawidłowa z mikrofon połączonego z limitu|
    |Liniowe|Na bok za pomocą mikrofon przodu (pokazane na poniższej ilustracji)|

    ![Orientacja kit liniowej dev](media/speech-devices-sdk/qsg-2.png)

1. Zainstaluj certyfikaty i Ustaw uprawnienia urządzenia dźwiękowego. W oknie wiersza polecenia, wpisz następujące polecenia:

   ```powershell
   adb push C:\SDSDK\Android-Sample-Release\scripts\roobo_setup.sh /data/
   adb shell
   cd /data/
   chmod 777 roobo_setup.sh
   ./roobo_setup.sh
   exit
   ```

    > [!NOTE]
    > Te polecenia korzystają z mostka debugowania systemu Android, `adb.exe`, który jest częścią instalacji programu Android Studio. To narzędzie znajduje się w C:\Users\[nazwa_użytkownika] \AppData\Local\Android\Sdk\platform narzędzia. Ten katalog można dodać do swojej ścieżki, aby był bardziej wygodne do wywołania `adb`. W przeciwnym razie należy określić pełną ścieżkę do instalacji adb.exe w każdego polecenia, który wywołuje `adb`.
    >
    > Jeśli zobaczysz błąd `no devices/emulators found` , sprawdź, czy kabel USB jest podłączony i jest kablem wysokiej jakości. Możesz użyć `adb devices` , aby sprawdzić, czy komputer może komunikować się z zestawem deweloperskim, ponieważ zwróci listę urządzeń.
    >
    > [!TIP]
    > Wyciszenia mikrofonu i głośników, należy upewnić się, że pracujesz z mikrofon zestaw development kit Twojego komputera. W ten sposób przypadkowo nie spowoduje wyzwolenia z urządzenia z dźwięku z komputera.

1. Aby dołączyć osoby mówiącej do zestawu dla deweloperów, należy go połączyć z wierszem audio out. Należy wybrać głośnik o wysokiej jakości z dwukierunkowym złączem 3,5 mm.

    ![Vysor audio](media/speech-devices-sdk/qsg-14.png)

## <a name="development-information"></a>Informacje o programowaniu

Aby uzyskać więcej informacji na temat programowania, zobacz [Przewodnik programowania w roobo](http://dwn.roo.bo/server_upload/ddk/ROOBO%20Dev%20Kit-User%20Guide.pdf).

## <a name="audio"></a>Audio

Roobo zapewnia narzędzie, które przechwytuje cały dźwięk do pamięci flash. Mogą pomóc rozwiązać problemy. Wersja narzędzia towarzyszy konfiguracjami development kit. W [witrynie roobo](https://ddk.roobo.com/)wybierz urządzenie, a następnie wybierz łącze **Narzędzia roobo** w dolnej części strony.

## <a name="next-steps"></a>Kolejne kroki

* [Uruchamianie przykładowej aplikacji dla systemu Android](speech-devices-sdk-android-quickstart.md)
