---
title: Dev Audio inteligentnych Roobo zestawu SDK urządzenia mowy Kit v1 — usługi mowy
titleSuffix: Azure Cognitive Services
description: Wymagania wstępne i instrukcje dla klientów zaczynających się od zestawu Speech Devices SDK, program Roobo inteligentne Audio zestaw deweloperski w wersji 1.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: 7c1a13a44d9db8ed029ce798f0bb34944a1a65a7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65409039"
---
# <a name="device-roobo-smart-audio-dev-kit"></a>Urządzenie: Zestaw deweloperski inteligentnych Roobo Audio

Ten artykuł zawiera urządzenia określonych informacji dotyczących inteligentnych Roobo deweloperski Audio.

## <a name="set-up-the-development-kit"></a>Skonfiguruj zestaw development kit

1. Zestaw development kit ma dwa wczesnych łączniki USB. Łącznik po lewej stronie zasilania deweloperski i jest wyróżniony jako zasilania na poniższej ilustracji. Po prawej stronie jeden ma nad nim kontroli i jest oznaczony jako debugowania na obrazie.

    ![Łączenie zestawu dla deweloperów](media/speech-devices-sdk/qsg-1.png)

1. Zasilacz deweloperski za pomocą kabla USB wczesnych połącz port zasilania do komputera lub dostarczenia adaptera. Wskaźnik zielony zasilania, zostaną wyświetlone w górnym tablicy.

1. Aby kontrolować deweloperski, połącz port debugowania na komputerze przy użyciu drugiej micro kabla USB. Istotne jest zapewnienie niezawodną komunikację przy użyciu kabla wysokiej jakości.

1. Orientacja Twojego zestawu SDK dla dowolnego cykliczne lub liniowy konfiguracji.

    |Tworzenie konfiguracji zestawu|Orientacja|
    |-----------------------------|------------|
    |Cykliczne|Prawidłowa z mikrofon połączonego z limitu|
    |Liniowe|Na bok za pomocą mikrofon przodu (pokazane na poniższej ilustracji)|

    ![Orientacja kit liniowej dev](media/speech-devices-sdk/qsg-2.png)

1. Zainstaluj certyfikaty i ustaw uprawnienia urządzenia dźwiękowego. W oknie wiersza polecenia, wpisz następujące polecenia:

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
    > Jeśli zostanie wyświetlony błąd `no devices/emulators found` Sprawdź kabel USB został podłączony i jest kabla wysokiej jakości. Możesz użyć `adb devices` do sprawdzenia, czy komputer może komunikować się z deweloperski go spowoduje zwrócenie listy urządzeń.
    >
    > [!TIP]
    > Wyciszenia mikrofonu i głośników, należy upewnić się, że pracujesz z mikrofon zestaw development kit Twojego komputera. W ten sposób przypadkowo nie spowoduje wyzwolenia z urządzenia z dźwięku z komputera.

1. Aby dołączyć osoby mówiącej do zestawu dla deweloperów, należy go połączyć z wierszem audio out. Należy wybrać prelegenta dobrej jakości oraz wtyczki analogowy 3,5 mm.

    ![Vysor audio](media/speech-devices-sdk/qsg-14.png)

## <a name="development-information"></a>Tworzenie informacji

Aby uzyskać więcej informacji rozwoju, zobacz [Podręcznik programowania Roobo](http://dwn.roo.bo/server_upload/ddk/ROOBO%20Dev%20Kit-User%20Guide.pdf).

## <a name="audio"></a>Audio

Roobo udostępnia narzędzia, która przechwytuje wszystkie audio w pamięci flash. Mogą pomóc rozwiązać problemy. Wersja narzędzia towarzyszy konfiguracjami development kit. Na [witryny Roobo](https://ddk.roobo.com/), wybierz urządzenie, a następnie wybierz **narzędzia Roobo** widocznego u dołu strony.

## <a name="next-steps"></a>Kolejne kroki

* [Uruchom aplikację przykładową dla systemu Android](speech-devices-sdk-android-quickstart.md)
