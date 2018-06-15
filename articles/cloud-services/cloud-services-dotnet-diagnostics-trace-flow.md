---
title: Śledzenie przepływu w aplikacji usługi w chmurze z diagnostyki Azure | Dokumentacja firmy Microsoft
description: Dodaj śledzenie wiadomości do aplikacji Azure w celu ułatwienia, debugowanie, pomiaru wydajności, monitorowania, analizy ruchu sieciowego i inne.
services: cloud-services
documentationcenter: .net
author: thraka
manager: timlt
editor: ''
ms.assetid: 09934772-cc07-4fd2-ba88-b224ca192f8e
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/20/2016
ms.author: adegeo
ms.openlocfilehash: 7551ae2e3a7c5f3ff6010bf34e7600da1028d67f
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/22/2018
ms.locfileid: "29461248"
---
# <a name="trace-the-flow-of-a-cloud-services-application-with-azure-diagnostics"></a>Śledzenie przepływu aplikacji usługi w chmurze Diagnostyka Azure
Śledzenie jest sposobem monitorowania wykonanie aplikacji jest uruchomiona. Można użyć [System.Diagnostics.Trace](https://msdn.microsoft.com/library/system.diagnostics.trace.aspx), [System.Diagnostics.Debug](https://msdn.microsoft.com/library/system.diagnostics.debug.aspx), i [System.Diagnostics.TraceSource](https://msdn.microsoft.com/library/system.diagnostics.tracesource.aspx) klasy do rejestrowania informacji o błędach i wykonanie aplikacji w dzienniki, pliki tekstowe lub innych urządzeń w celu późniejszej analizy. Aby uzyskać więcej informacji dotyczących śledzenia, zobacz [śledzenie i Instrumentacja aplikacji](https://msdn.microsoft.com/library/zs6s4h68.aspx).

## <a name="use-trace-statements-and-trace-switches"></a>Użyj instrukcji śledzenia i przełączniki śledzenia
Implementowanie śledzenia w aplikacji usługi w chmurze, dodając [DiagnosticMonitorTraceListener](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.diagnostics.diagnosticmonitortracelistener.aspx) do konfiguracji aplikacji i wykonywania wywołań do System.Diagnostics.Trace lub System.Diagnostics.Debug w kodzie aplikacji. Użyj pliku konfiguracji *app.config* dla roli proces roboczy i *web.config* ról sieci web. Jeśli utworzysz nową usługę hostowaną przy użyciu szablonu programu Visual Studio, Diagnostyka Azure zostanie automatycznie dodany do projektu i DiagnosticMonitorTraceListener zostanie dodany do pliku konfiguracyjnego odpowiedniego dla ról, które można dodać.

Aby uzyskać informacje na umieszczenie instrukcji śledzenia, zobacz [porady: Dodawanie instrukcji śledzenia do kodu aplikacji](https://msdn.microsoft.com/library/zd83saa2.aspx).

Przez umieszczenie [przełączniki śledzenia](https://msdn.microsoft.com/library/3at424ac.aspx) w kodzie, można kontrolować czy występuje śledzenia i jest ich zakresu. Dzięki temu można monitorować stan aplikacji w środowisku produkcyjnym. Jest to szczególnie ważne w aplikacji biznesowych, która używa wielu składniki działające na wielu komputerach. Aby uzyskać więcej informacji, zobacz [porady: Konfigurowanie przełączników śledzenia](https://msdn.microsoft.com/library/t06xyy08.aspx).

## <a name="configure-the-trace-listener-in-an-azure-application"></a>Skonfiguruj odbiornik śledzenia w aplikacji Azure
Śledzenie debugowania i TraceSource, wymagają ustawienia "odbiorników" zbieranie i rejestrowanie komunikatów, które są wysyłane. Odbiorniki zbierania, przechowywania i wiadomości śledzenia trasy. Bezpośrednie ich dane wyjściowe śledzenia do odpowiedniego obiektu docelowego, takich jak dziennika, okna lub plik tekstowy. Diagnostyka Azure używa [DiagnosticMonitorTraceListener](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.diagnostics.diagnosticmonitortracelistener.aspx) klasy.

Przed wykonaniem następującej procedury, należy zainicjować Azure monitora diagnostycznego. Aby to zrobić, zobacz [Włączanie diagnostyki w Microsoft Azure](cloud-services-dotnet-diagnostics.md).

Należy pamiętać, że użycie szablonów, które są dostarczane przez program Visual Studio, konfiguracji odbiornika jest automatycznie dodawane automatycznie.

### <a name="add-a-trace-listener"></a>Dodać obiektu nasłuchującego śledzenia
1. Otwórz plik web.config lub app.config dla roli użytkownika.
2. Dodaj następujący kod do pliku. Zmieniono atrybutu wersji, aby użyć numeru wersji zestawu, do którego się odwołujesz. Wersja zestawu nie zawsze zmienia się w każdej wersji zestawu Azure SDK chyba, że istnieją aktualizacje do niego.
   
    ```
    <system.diagnostics>
        <trace>
            <listeners>
                <add type="Microsoft.WindowsAzure.Diagnostics.DiagnosticMonitorTraceListener,
                  Microsoft.WindowsAzure.Diagnostics,
                  Version=2.8.0.0,
                  Culture=neutral,
                  PublicKeyToken=31bf3856ad364e35"
                  name="AzureDiagnostics">
                    <filter type="" />
                </add>
            </listeners>
        </trace>
    </system.diagnostics>
    ```
   > [!IMPORTANT]
   > Upewnij się, że masz odwołanie projektu do zestawu Microsoft.WindowsAzure.Diagnostics. Zaktualizować numer wersji w powyższym xml do zgodna z wersją Microsoft.WindowsAzure.Diagnostics przywoływanego zestawu.
   > 
   > 
3. Zapisz plik konfiguracji.

Aby uzyskać więcej informacji na temat odbiorników, zobacz [obiektów nasłuchujących śledzenia](https://msdn.microsoft.com/library/4y5y10s7.aspx).

Po zakończeniu procedury można dodać obiektu nasłuchującego można Dodawanie instrukcji śledzenia do kodu.

### <a name="to-add-trace-statement-to-your-code"></a>Aby dodać instrukcji śledzenia do kodu
1. Otwórz plik źródłowy dla aplikacji. Na przykład <RoleName>plik CS roli procesu roboczego lub roli sieci web.
2. Dodaj następującą instrukcję using, jeśli nie został już dodany:
    ```
        using System.Diagnostics;
    ```
3. Dodawanie instrukcji śledzenia, w którym mają być przechwytywane informacje o stanie aplikacji. Można użyć różnych metod do formatowania danych wyjściowych instrukcji śledzenia. Aby uzyskać więcej informacji, zobacz [porady: Dodawanie instrukcji śledzenia do kodu aplikacji](https://msdn.microsoft.com/library/zd83saa2.aspx).
4. Zapisz plik źródłowy.

