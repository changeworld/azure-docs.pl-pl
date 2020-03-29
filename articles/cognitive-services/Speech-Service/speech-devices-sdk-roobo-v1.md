---
title: Urządzenia mowy SDK Roobo Smart Audio Dev Kit v1 - Usługa mowy
titleSuffix: Azure Cognitive Services
description: Wymagania wstępne i instrukcje dotyczące rozpoczynania pracy z zestawem SDK urządzeń mowy Roobo Smart Audio Dev Kit v1.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 9add5b063b67ddcc4cd5bf93e7f5b570b004e5ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "74815580"
---
# <a name="device-roobo-smart-audio-dev-kit"></a>Urządzenie: Roobo Smart Audio Dev Kit

Ten artykuł zawiera informacje dotyczące urządzenia dla Roobo Smart Audio Dev Kit.

## <a name="set-up-the-development-kit"></a>Konfigurowanie zestawu deweloperskiego

1. Zestaw deweloperski posiada dwa złącza micro USB. Lewe złącze jest do zasilania zestawu deweloperskiego i jest wyróżniony jako moc na obrazku poniżej. Po prawej stronie jest kontrolowanie go i jest oznaczony debugowania na obrazie.

    ![Podłączanie zestawu deweloperskiego](media/speech-devices-sdk/qsg-1.png)

1. Podłącz zestaw deweloperski za pomocą kabla micro USB do podłączenia portu zasilania do komputera lub zasilacza. Pod górną płytą zaświeć zielony wskaźnik zasilania.

1. Aby sterować zestawem deweloperskim, podłącz port debugowania do komputera za pomocą drugiego kabla micro USB. Konieczne jest użycie kabla wysokiej jakości, aby zapewnić niezawodną komunikację.

1. Orientuj zestaw deweloperski dla konfiguracji kołowej lub liniowej.

    |Konfiguracja zestawu deweloperskiego|Orientacja|
    |-----------------------------|------------|
    |Okrągłe|Pionowo, z mikrofonami skierowanymi do sufitu|
    |Liniowe|Z boku, z mikrofonami skierowanymi w stronę ciebie (pokazanymi na poniższej ilustracji)|

    ![Liniowa orientacja zestawu deweloperskiego](media/speech-devices-sdk/qsg-2.png)

1. Zainstaluj certyfikaty i ustaw uprawnienia urządzenia dźwiękowego. W oknie wiersza polecenia wpisz następujące polecenia:

   ```powershell
   adb push C:\SDSDK\Android-Sample-Release\scripts\roobo_setup.sh /data/
   adb shell
   cd /data/
   chmod 777 roobo_setup.sh
   ./roobo_setup.sh
   exit
   ```

    > [!NOTE]
    > Te polecenia używają mostka debugowania systemu Android, `adb.exe`który jest częścią instalacji Android Studio. To narzędzie znajduje się w\[języku C:\Użytkownicy nazwa użytkownika]\AppData\Local\Android\Sdk\platform-tools. Możesz dodać ten katalog do ścieżki, aby ułatwić `adb`wywoływanie . W przeciwnym razie należy określić pełną ścieżkę do instalacji adb.exe w każdym wywołaniu `adb`polecenia .
    >
    > Jeśli widzisz błąd, `no devices/emulators found` sprawdź, czy kabel USB jest podłączony i jest wysokiej jakości kablem. Można użyć, `adb devices` aby sprawdzić, czy komputer może rozmawiać z zestawem deweloperskim, ponieważ zwróci listę urządzeń.
    >
    > [!TIP]
    > Wycisz mikrofon i głośnik komputera, aby mieć pewność, że pracujesz z mikrofonami zestawu deweloperskiego. W ten sposób nie spowoduje to przypadkowego wyzwolenia urządzenia z dźwiękiem z komputera.

1. Jeśli chcesz podłączyć głośnik do zestawu deweloperskiego, możesz podłączyć go do linii audio. Powinieneś wybrać dobrej jakości głośnik z wtyczką analogową 3,5 mm.

    ![Dźwięk Vysor](media/speech-devices-sdk/qsg-14.png)

## <a name="development-information"></a>Informacje o rozwoju

Aby uzyskać więcej informacji na temat rozwoju, zobacz [przewodnik rozwoju Roobo](http://dwn.roo.bo/server_upload/ddk/ROOBO%20Dev%20Kit-User%20Guide.pdf).

## <a name="audio"></a>Dźwięk

Roobo zapewnia narzędzie, które przechwytuje cały dźwięk do pamięci flash. Może to pomóc w rozwiązywaniu problemów z dźwiękiem. Wersja narzędzia jest dostępna dla każdej konfiguracji zestawu deweloperskiego. Na [stronie Roobo](https://ddk.roobo.com/)wybierz urządzenie, a następnie wybierz link **Roobo Tools** u dołu strony.

## <a name="next-steps"></a>Następne kroki

* [Uruchamianie przykładowej aplikacji systemu Android](speech-devices-sdk-android-quickstart.md)
