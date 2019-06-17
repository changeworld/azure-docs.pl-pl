---
title: Usługa Azure Application Insights OpenCensus rozproszonego śledzenia lokalnej usługi przesyłania dalej (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: Do przodu OpenCensus rozproszonych śledzi i rozciąga się od języków, takich jak Python, a następnie przejść do usługi Azure Application Insights
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
ms.openlocfilehash: a7efe663a75fa29a31e7157c5eab24c2973a3758
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60699350"
---
# <a name="local-forwarder-preview"></a>Lokalne usługi przesyłania dalej (wersja zapoznawcza)

Lokalne usługi przesyłania dalej jest agent, który gromadzi informacje o usłudze Application Insights lub [OpenCensus](https://opencensus.io/) dane telemetryczne z różnych zestawów SDK i kieruje je do usługi Application Insights. Jest w stanie działać w obszarze Windows i Linux. Może również być może zostać uruchomiony w systemie macOS, ale który nie jest oficjalnie obsługiwany w tej chwili.

## <a name="running-local-forwarder"></a>Uruchamianie lokalne usługi przesyłania dalej

Lokalne usługi przesyłania dalej jest [projekt typu open source w serwisie GitHub](https://github.com/Microsoft/ApplicationInsights-LocalForwarder/releases). Istnieje szereg sposobów, aby uruchomić lokalnej usługi przesyłania dalej na wielu platformach.

### <a name="windows"></a>Windows

#### <a name="windows-service"></a>Windows Service

Jest najprostszym sposobem uruchamiania lokalnego usługi przesyłania dalej w obszarze Windows przez zainstalowanie go jako usługę Windows. Wersja zawiera plik wykonywalny usługi Windows (*WindowsServiceHost/Microsoft.LocalForwarder.WindowsServiceHost.exe*) który można łatwo zarejestrować w systemie operacyjnym.

> [!NOTE]
> Usługa lokalnej usługi przesyłania dalej wymaga co najmniej programu .NET Framework 4.7. Jeśli nie masz programu .NET Framework 4.7 usługi instalacji, ale nie rozpocznie się. Najnowsza wersja programu .NET Framework dostęp do **[odwiedź stronę pobierania programu .NET Framework](
https://www.microsoft.com/net/download/dotnet-framework-runtime/net472?utm_source=getdotnet&utm_medium=referral)** .

1. Pobierz LF. Plik WindowsServiceHost.zip [stronie wersji lokalnej usługi przesyłania dalej](https://github.com/Microsoft/ApplicationInsights-LocalForwarder/releases) w witrynie GitHub.

    ![Zrzut ekranu przedstawiający stronę pobierania wersji lokalnej usługi przesyłania dalej](./media/opencensus-local-forwarder/001-local-forwarder-windows-service-host-zip.png)

2. W tym przykładzie w celu ułatwienia demonstracji, po prostu wyodrębnimy pliku .zip do ścieżki `C:\LF-WindowsServiceHost`.

    Aby zarejestrować usługę i skonfigurować go na uruchomienie przy rozruchu systemu, uruchom następujące polecenie w wierszu polecenia jako Administrator:

    ```
    sc create "Local Forwarder" binpath="C:\LF-WindowsServiceHost\Microsoft.LocalForwarder.WindowsServiceHost.exe" start=auto
    ```
    
    Powinna pojawić się odpowiedź dla:
    
    `[SC] CreateService SUCCESS`
    
    Aby zbadać nowej usługi przy użyciu typu usługi graficznego interfejsu użytkownika ``services.msc``
        
     ![Zrzut ekranu przedstawiający usługi modułu przekazującego lokalne](./media/opencensus-local-forwarder/002-services.png)

3. **Kliknij prawym przyciskiem myszy** nowej lokalnej usługi przesyłania dalej, a następnie wybierz pozycję **Start**. Usługi staną się teraz uruchomiony.

4. Domyślnie usługa została utworzona bez żadnych akcji odzyskiwania. Możesz **kliknij prawym przyciskiem myszy** i wybierz **właściwości** > **odzyskiwania** skonfigurować automatyczne odpowiedzi na awarię usługi.

    Lub jeśli wolisz skonfigurować automatyczne odzyskiwanie programowe przypadku wystąpienia awarii, możesz użyć:

    ```
    sc failure "Local Forwarder" reset= 432000 actions= restart/1000/restart/1000/restart/1000
    ```

5. W tej samej lokalizacji co Twoje ``Microsoft.LocalForwarder.WindowsServiceHost.exe`` pliku, który w tym przykładzie jest ``C:\LF-WindowsServiceHost`` istnieje plik o nazwie ``LocalForwarder.config``. Jest to plik opartymi na języku xml, który pozwala na dostosowanie konfiguracji usługi localforwader i określ klucz Instrumentacji zasobu usługi Application Insights przekazywane dane rozproszonego śledzenia. 

    Po wprowadzeniu zmian w ``LocalForwarder.config`` plik, aby dodać klucz instrumentacji, należy pamiętać o ponownym uruchomieniu **lokalna Usługa przesyłania dalej** umożliwia zmiany zaczęły obowiązywać.
    
6. Aby upewnić się, że żądane ustawienia zostały spełnione i lokalne usługi przesyłania dalej nasłuchuje danych śledzenia jako oczekiwana wyboru ``LocalForwarder.log`` pliku. Powinny zostać wyświetlone wyniki podobne do poniższej ilustracji w dolnej części pliku:

    ![Zrzut ekranu LocalForwarder.log pliku](./media/opencensus-local-forwarder/003-log-file.png)

#### <a name="console-application"></a>Aplikacja konsolowa

Dla niektórych zastosowań, może być korzystne uruchomić lokalnej usługi przesyłania dalej jako aplikację konsolową w języku. Wersja jest dostarczany z następujących wersji pliku wykonywalnego hosta konsoli:
* plik binarny platformy .NET Core zależny od struktury */ConsoleHost/publish/Microsoft.LocalForwarder.ConsoleHost.dll*. Uruchomienie tych danych binarnych wymaga platformy .NET Core zainstalowanego środowiska uruchomieniowego; Ten plik do pobrania można znaleźć [strony](https://www.microsoft.com/net/download/dotnet-core/2.1) Aby uzyskać szczegółowe informacje.
  ```batchfile
  E:\uncdrop\ConsoleHost\publish>dotnet Microsoft.LocalForwarder.ConsoleHost.dll
  ```
* autonomiczny zestaw .NET Core plików binarnych dla x86 i x64 64. Nie wymagają one środowisko uruchomieniowe programu .NET Core do uruchomienia. */ConsoleHost/win-x86/publish/Microsoft.LocalForwarder.ConsoleHost.exe*, */ConsoleHost/win-x64/publish/Microsoft.LocalForwarder.ConsoleHost.exe*.
  ```batchfile
  E:\uncdrop\ConsoleHost\win-x86\publish>Microsoft.LocalForwarder.ConsoleHost.exe
  E:\uncdrop\ConsoleHost\win-x64\publish>Microsoft.LocalForwarder.ConsoleHost.exe
  ```

### <a name="linux"></a>Linux

Podobnie jak w przypadku Windows, wersja jest dostarczany z następujących wersji pliku wykonywalnego hosta konsoli:
* plik binarny platformy .NET Core zależny od struktury */ConsoleHost/publish/Microsoft.LocalForwarder.ConsoleHost.dll*. Uruchomienie tych danych binarnych wymaga platformy .NET Core zainstalowanego środowiska uruchomieniowego; Ten plik do pobrania można znaleźć [strony](https://www.microsoft.com/net/download/dotnet-core/2.1) Aby uzyskać szczegółowe informacje.

```batchfile
dotnet Microsoft.LocalForwarder.ConsoleHost.dll
```

* niezależna platformy .NET Core zestaw plików binarnych dla systemu linux-64. Ta nie wymaga środowisko uruchomieniowe programu .NET Core do uruchomienia. */ConsoleHost/linux-x64/publish/Microsoft.LocalForwarder.ConsoleHost*.

```batchfile
user@machine:~/ConsoleHost/linux-x64/publish$ sudo chmod +x Microsoft.LocalForwarder.ConsoleHost
user@machine:~/ConsoleHost/linux-x64/publish$ ./Microsoft.LocalForwarder.ConsoleHost
```

Wielu użytkowników systemu Linux chcesz uruchomić lokalnej usługi przesyłania dalej jako demon. Systemy Linux są dostarczane z różnych rozwiązań do zarządzania usługami, takimi jak Upstart, sysv lub systemd. Niezależnie od używanej wersji określonego jest, służy do uruchamiania lokalnego usługi przesyłania dalej w sposób, który jest najbardziej odpowiednie dla danego scenariusza.

Na przykład utwórz usługi demona przy użyciu systemd. Użyjemy wersji zależny od struktury, ale takie same może odbywać się dla nich niezależna także.

* Utwórz następujący plik usługi o nazwie *localforwarder.service* i umieść go w */lib/systemd/system*.
W tym przykładzie przyjęto założenie, nazwa użytkownika to SAMPLE_USER i zostały skopiowane pliki binarne framework zależnych od lokalnej usługi przesyłania dalej (z */ConsoleHost/publikowanie*) do */home/SAMPLE_USER/LOCALFORWARDER_DIR*.

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

* Uruchom następujące polecenie, aby nakazać systemd, aby uruchomić lokalnej usługi przesyłania dalej na każdym rozruchu

```
systemctl enable localforwarder
```

* Uruchom następujące polecenie, aby nakazać systemd, aby natychmiast rozpocząć lokalnej usługi przesyłania dalej

```
systemctl start localforwarder
```

* Monitorowanie usługi, sprawdzając * *.log* plików w katalogu /home/SAMPLE_USER/LOCALFORWARDER_DIR.

### <a name="mac"></a>Mac
Lokalne usługi przesyłania dalej, mogą działać z systemem macOS, ale obecnie nie jest oficjalnie obsługiwana.

### <a name="self-hosting"></a>Hostingu samodzielnego
Lokalne usługi przesyłania dalej również jest dystrybuowane jako pakiet NuGet programu .NET Standard, co umożliwia hostowanie w aplikacji .NET.

```C#
using Library;
...
Host host = new Host();

// see section below on configuring local forwarder
string configuration = ...;
    
host.Run(config, TimeSpan.FromSeconds(5));
...
host.Stop();
```

## <a name="configuring-local-forwarder"></a>Konfigurowanie lokalnej usługi przesyłania dalej

* Podczas uruchamiania jednego z hostów lokalnej usługi przesyłania dalej własne (konsoli hosta lub Host usługi Windows), można znaleźć **LocalForwarder.config** umieszczone obok pliku binarnego.
* W przypadku hostowania własnym lokalnym NuGet usługi przesyłania dalej, konfiguracji tego samego formatu musi być podana w kodzie (patrz sekcja hostingu samodzielnego). Składnia konfiguracji można znaleźć [LocalForwarder.config](https://github.com/Microsoft/ApplicationInsights-LocalForwarder/blob/master/src/ConsoleHost/LocalForwarder.config) w repozytorium GitHub. 

> [!NOTE]
> Konfiguracja może zmienić wersji, więc należy zwrócić uwagę, do której wersji używasz.

## <a name="monitoring-local-forwarder"></a>Monitorowanie lokalne usługi przesyłania dalej

Zapisy są zapisywane w do systemu plików, obok pliku wykonywalnego, który uruchamia lokalnej usługi przesyłania dalej (Wyszukaj * *.log* plików). Możesz umieścić plik o nazwie *NLog.config* obok pliku wykonywalnego zapewnienie konfigurację zamiast domyślna. Zobacz [dokumentacji](https://github.com/NLog/NLog/wiki/Configuration-file#configuration-file-format) opisu formatu.

Jeśli nie podano żadnego pliku konfiguracji (jest to ustawienie domyślne), lokalne usługi przesyłania dalej użyje domyślnej konfiguracji, który można znaleźć [tutaj](https://github.com/Microsoft/ApplicationInsights-LocalForwarder/blob/master/src/Common/NLog.config).

## <a name="next-steps"></a>Kolejne kroki

* [Otwórz spis](https://opencensus.io/)
