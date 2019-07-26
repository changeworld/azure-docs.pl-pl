---
title: Usługa Azure Application Insights OpenCensus — moduł przekazujący śledzenie rozproszone (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: Przekazuj OpenCensus rozproszone i rozpięte z języków, takich jak Python, i przejdź do platformy Azure Application Insights
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/18/2018
ms.reviewer: nimolnar
ms.author: mbullwin
ms.openlocfilehash: aa64755b636005f4ed8ea5c074ffaada51fb8dd9
ms.sourcegitcommit: e72073911f7635cdae6b75066b0a88ce00b9053b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/19/2019
ms.locfileid: "68348155"
---
# <a name="local-forwarder-preview"></a>Lokalna usługa przesyłania dalej (wersja zapoznawcza)

Lokalna usługa przesyłania dalej to Agent, który zbiera dane telemetryczne Application Insights lub [OpenCensus](https://opencensus.io/) z różnych zestawów SDK i przekierowuje je do Application Insights. Może działać w systemach Windows i Linux. Może być również możliwe uruchomienie go w ramach macOS, ale nie jest to oficjalnie obsługiwane.

## <a name="running-local-forwarder"></a>Uruchamianie lokalnego usługi przesyłania dalej

Lokalna usługa przesyłania dalej to [projekt Open Source w usłudze GitHub](https://github.com/Microsoft/ApplicationInsights-LocalForwarder/releases). Istnieją różne sposoby uruchamiania lokalnego usługi przesyłania dalej na wielu platformach.

### <a name="windows"></a>Windows

#### <a name="windows-service"></a>Usługa systemu Windows

Najprostszym sposobem uruchamiania lokalnego usługi przesyłania dalej w systemie Windows jest zainstalowanie jej jako usługi systemu Windows. Wydanie zawiera plik wykonywalny usługi systemu Windows (*WindowsServiceHost/Microsoft. LocalForwarder. WindowsServiceHost. exe*), który może być łatwo zarejestrowany w systemie operacyjnym.

> [!NOTE]
> Lokalna usługa przesyłania dalej wymaga co najmniej .NET Framework 4,7. Jeśli nie masz .NET Framework 4,7, usługa zostanie zainstalowana, ale nie zostanie uruchomiona. Najnowsza wersja programu .NET Framework dostęp do **[odwiedź stronę pobierania programu .NET Framework](
https://www.microsoft.com/net/download/dotnet-framework-runtime/net472?utm_source=getdotnet&utm_medium=referral)** .

1. Pobierz LF. Plik WindowsServiceHost. zip z [lokalnej strony wersji usługi przesyłania dalej](https://github.com/Microsoft/ApplicationInsights-LocalForwarder/releases) w witrynie GitHub.

    ![Zrzut ekranu strony pobierania wersji usługi przesyłania dalej](./media/opencensus-local-forwarder/001-local-forwarder-windows-service-host-zip.png)

2. W tym przykładzie w celu ułatwienia demonstracji po prostu wyodrębnimy plik. zip do ścieżki `C:\LF-WindowsServiceHost`.

    Aby zarejestrować usługę i skonfigurować ją do uruchamiania przy rozruchu systemu, uruchom następujące polecenie w wierszu polecenia jako administrator:

    ```
    sc create "Local Forwarder" binpath="C:\LF-WindowsServiceHost\Microsoft.LocalForwarder.WindowsServiceHost.exe" start=auto
    ```
    
    Powinna zostać wyświetlona odpowiedź:
    
    `[SC] CreateService SUCCESS`
    
    Aby przeanalizować nową usługę za pomocą typu graficznego interfejsu użytkownika usług``services.msc``
        
     ![Zrzut ekranu usługi lokalnego przesyłania dalej](./media/opencensus-local-forwarder/002-services.png)

3. **Kliknij prawym przyciskiem myszy** nową lokalną usługę przesyłania dalej i wybierz pozycję **Uruchom**. Usługa zostanie teraz uruchomiona.

4. Domyślnie usługa jest tworzona bez żadnych akcji odzyskiwania. Możesz **kliknąć prawym przyciskiem myszy** i wybrać pozycję **Właściwości** > **odzyskiwanie** , aby skonfigurować automatyczne reagowanie na awarię usługi.

    Lub jeśli wolisz ustawiać automatyczne opcje odzyskiwania w przypadku wystąpienia błędów, możesz użyć:

    ```
    sc failure "Local Forwarder" reset= 432000 actions= restart/1000/restart/1000/restart/1000
    ```

5. W tej samej lokalizacji ``Microsoft.LocalForwarder.WindowsServiceHost.exe`` , w której znajduje się plik, w tym ``C:\LF-WindowsServiceHost`` przykładzie istnieje plik o nazwie. ``LocalForwarder.config`` Jest to plik oparty na formacie XML, który umożliwia dostosowanie konfiguracji localforwader i określenie klucza Instrumentacji zasobu Application Insights, do którego mają być przekazywane dane śledzenia rozproszonego. 

    Po przeprowadzeniu edycji ``LocalForwarder.config`` pliku w celu dodania klucza Instrumentacji należy ponownie uruchomić **usługę przesyłania dalej** , aby umożliwić wprowadzenie zmian.
    
6. Aby upewnić się, że żądane ustawienia są na miejscu i że lokalny moduł przesyłania dalej nasłuchuje danych śledzenia zgodnie z ``LocalForwarder.log`` oczekiwaniami, sprawdź plik. Powinny być widoczne wyniki podobne do poniższej ilustracji w dolnej części pliku:

    ![Zrzut ekranu przedstawiający plik LocalForwarder. log](./media/opencensus-local-forwarder/003-log-file.png)

#### <a name="console-application"></a>Aplikacja konsolowa

W przypadku niektórych przypadków użycia może być korzystne uruchomienie lokalnego modułu przekazującego jako aplikacji konsolowej. Wydanie zawiera następujące wersje wykonywalne hosta konsoli:
* zależny od platformy .NET Core */ConsoleHost/Publish/Microsoft.LocalForwarder.ConsoleHost.dll*binarny. Uruchomienie tego pliku binarnego wymaga zainstalowania środowiska uruchomieniowego platformy .NET Core; Aby uzyskać szczegółowe informacje, zobacz tę [stronę](https://www.microsoft.com/net/download/dotnet-core/2.1) pobierania.
  ```batchfile
  E:\uncdrop\ConsoleHost\publish>dotnet Microsoft.LocalForwarder.ConsoleHost.dll
  ```
* własny zestaw plików binarnych platformy .NET Core dla platform x86 i x64. Nie wymaga to uruchomienia środowiska uruchomieniowego .NET Core. */ConsoleHost/win-x86/publish/Microsoft.LocalForwarder.ConsoleHost.exe*, */ConsoleHost/win-x64/publish/Microsoft.LocalForwarder.ConsoleHost.exe*.
  ```batchfile
  E:\uncdrop\ConsoleHost\win-x86\publish>Microsoft.LocalForwarder.ConsoleHost.exe
  E:\uncdrop\ConsoleHost\win-x64\publish>Microsoft.LocalForwarder.ConsoleHost.exe
  ```

### <a name="linux"></a>Linux

Podobnie jak w przypadku systemu Windows, wydanie zawiera następujące wersje wykonywalne hosta konsoli:
* zależny od platformy .NET Core */ConsoleHost/Publish/Microsoft.LocalForwarder.ConsoleHost.dll*binarny. Uruchomienie tego pliku binarnego wymaga zainstalowania środowiska uruchomieniowego platformy .NET Core; Aby uzyskać szczegółowe informacje, zobacz tę [stronę](https://www.microsoft.com/net/download/dotnet-core/2.1) pobierania.

```batchfile
dotnet Microsoft.LocalForwarder.ConsoleHost.dll
```

* własny zestaw plików binarnych platformy .NET Core dla systemu Linux-64. To nie wymaga uruchomienia środowiska uruchomieniowego .NET Core. */ConsoleHost/linux-x64/publish/Microsoft.LocalForwarder.ConsoleHost*.

```batchfile
user@machine:~/ConsoleHost/linux-x64/publish$ sudo chmod +x Microsoft.LocalForwarder.ConsoleHost
user@machine:~/ConsoleHost/linux-x64/publish$ ./Microsoft.LocalForwarder.ConsoleHost
```

Wielu użytkowników systemu Linux będzie chcą uruchamiać lokalny moduł przesyłania dalej jako demon. Systemy Linux są dostarczane z różnymi rozwiązaniami do zarządzania usługami, takimi jak sysv i system. Niezależnie od konkretnej wersji, można użyć jej do uruchamiania lokalnego usługi przesyłania dalej w sposób, który jest najbardziej odpowiedni dla Twojego scenariusza.

Na przykład Utwórzmy usługę demona przy użyciu systemu. Użyjemy wersji zależnej od struktury, ale ten sam można wykonać dla samego siebie.

* Utwórz następujący plik usługi o nazwie *localforwarder. Service* i umieść go w usłudze */lib/systemd/system*.
W tym przykładzie przyjęto założenie, że Twoja nazwa użytkownika to SAMPLE_USER i skopiowano pliki binarne zależne od platformy (z */ConsoleHost/Publish*) do */Home/SAMPLE_USER/LOCALFORWARDER_DIR*.

```
# localforwarder.service
# Place this file into /lib/systemd/system/
# Use 'systemctl enable localforwarder' to start the service automatically on each boot
# Use 'systemctl start localforwarder' to start the service immediately

[Unit]
Description=Local Forwarder service
After=network.target
StartLimitIntervalSec=0

[Service]
Type=simple
Restart=always
RestartSec=1
User=SAMPLE_USER
WorkingDirectory=/home/SAMPLE_USER/LOCALFORWARDER_DIR
ExecStart=/usr/bin/env dotnet /home/SAMPLE_USER/LOCALFORWARDER_DIR/Microsoft.LocalForwarder.ConsoleHost.dll noninteractive

[Install]
WantedBy=multi-user.target
```

* Uruchom następujące polecenie, aby poinstruować system w celu uruchomienia lokalnej usługi przesyłania dalej na każdym rozruchu

```
systemctl enable localforwarder
```

* Uruchom następujące polecenie, aby poinstruować system o konieczności natychmiastowego uruchomienia usługi przesyłania dalej

```
systemctl start localforwarder
```

* Monitoruj usługę, sprawdzając pliki * *. log* w katalogu/Home/SAMPLE_USER/LOCALFORWARDER_DIR.

### <a name="mac"></a>Mac
Lokalna usługa przesyłania dalej może współpracować z usługą macOS, ale nie jest obecnie oficjalnie obsługiwana.

### <a name="self-hosting"></a>Samoobsługowe hosting
Lokalna usługa przesyłania dalej jest również dystrybuowana jako pakiet NuGet .NET Standard, co umożliwia hostowanie go w ramach własnej aplikacji .NET.

```csharp
using Library;
...
Host host = new Host();

// see section below on configuring local forwarder
string configuration = ...;
    
host.Run(config, TimeSpan.FromSeconds(5));
...
host.Stop();
```

## <a name="configuring-local-forwarder"></a>Konfigurowanie usługi przesyłania dalej lokalnego

* Podczas uruchamiania jednego z lokalnych hostów usługi przesyłania dalej (hosta konsoli lub hosta usługi systemu Windows) znajduje się plik **LocalForwarder. config** umieszczony obok pliku binarnego.
* W przypadku samoobsługowego programu NuGet lokalnego usługi przesyłania dalej konfiguracja tego samego formatu musi być podana w kodzie (zobacz sekcję dotyczącą samoobsługowego udostępniania). W przypadku składni konfiguracji Sprawdź [plik LocalForwarder. config](https://github.com/Microsoft/ApplicationInsights-LocalForwarder/blob/master/src/ConsoleHost/LocalForwarder.config) w repozytorium GitHub. 

> [!NOTE]
> Konfiguracja może ulec zmianie z wersji na wydanie, więc należy zwrócić uwagę na wersję, której używasz.

## <a name="monitoring-local-forwarder"></a>Monitorowanie lokalnej usługi przesyłania dalej

Ślady są zapisywane w systemie plików obok pliku wykonywalnego, w którym działa lokalna usługa przesyłania dalej (wyszukiwanie plików * *. log* ). Możesz umieścić plik o nazwie *nLOG. config* obok pliku wykonywalnego, aby zapewnić własną konfigurację zamiast domyślnej. Opis formatu można znaleźć w [dokumentacji](https://github.com/NLog/NLog/wiki/Configuration-file#configuration-file-format) .

Jeśli nie podano pliku konfiguracji (co jest ustawieniem domyślnym), lokalna usługa przesyłania dalej będzie używać konfiguracji domyślnej, którą można znaleźć [tutaj](https://github.com/Microsoft/ApplicationInsights-LocalForwarder/blob/master/src/Common/NLog.config).

## <a name="next-steps"></a>Kolejne kroki

* [Otwórz spis](https://opencensus.io/)
