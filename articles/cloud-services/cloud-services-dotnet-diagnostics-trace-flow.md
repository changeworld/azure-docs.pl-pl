---
title: Śledzenie przepływu w aplikacji usługi w chmurze przy użyciu diagnostyki Azure | Dokumentacja firmy Microsoft
description: Dodaj komunikaty dotyczące śledzenia do aplikacji platformy Azure, aby ułatwić debugowanie, mierzenia wydajności, monitorowania, analizy ruchu sieciowego i inne.
services: cloud-services
documentationcenter: .net
author: jpconnock
manager: timlt
editor: ''
ms.assetid: 09934772-cc07-4fd2-ba88-b224ca192f8e
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/20/2016
ms.author: jeconnoc
ms.openlocfilehash: 2ba97e43616386a0ff8459316bfc4d3ddfe241a0
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/13/2018
ms.locfileid: "39000899"
---
# <a name="trace-the-flow-of-a-cloud-services-application-with-azure-diagnostics"></a>Śledzenie przepływu aplikacji usługi w chmurze przy użyciu diagnostyki Azure
Śledzenie jest sposobem monitorowania wykonywanie aplikacji, gdy jest on uruchomiony. Możesz użyć [System.Diagnostics.Trace](https://msdn.microsoft.com/library/system.diagnostics.trace.aspx), [system.Diagnostics.Debug —](https://msdn.microsoft.com/library/system.diagnostics.debug.aspx), i [System.Diagnostics.TraceSource](https://msdn.microsoft.com/library/system.diagnostics.tracesource.aspx) klasy do rejestrowania informacji o błędach i wykonywanie aplikacji w dzienniki, pliki tekstowe lub innych urządzeń do późniejszej analizy. Aby uzyskać więcej informacji na temat śledzenia, zobacz [śledzenie i Instrumentacja aplikacji](https://msdn.microsoft.com/library/zs6s4h68.aspx).

## <a name="use-trace-statements-and-trace-switches"></a>Użyj instrukcji śledzenia i przełączników śledzenia
Implementowanie śledzenia w aplikacji usługi w chmurze, dodając [DiagnosticMonitorTraceListener](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.diagnostics.diagnosticmonitortracelistener.aspx) konfiguracji aplikacji i wykonywania wywołań do System.Diagnostics.Trace lub system.Diagnostics.Debug — w sieci Kod aplikacji. Użyj pliku konfiguracji *app.config* dla ról procesów roboczych i *web.config* dla ról sieci web. Kiedy tworzysz nową usługę hostowaną, przy użyciu szablonu programu Visual Studio, usługi Azure Diagnostics jest automatycznie dodawany do projektu i DiagnosticMonitorTraceListener jest dodawany do odpowiedniego pliku konfiguracyjnego dla ról, które dodajesz.

Aby uzyskać informacje na umieszczenie instrukcji śledzenia, zobacz [porady: Dodawanie instrukcji śledzenia do kodu aplikacji](https://msdn.microsoft.com/library/zd83saa2.aspx).

Umieszczając [przełączniki śledzenia](https://msdn.microsoft.com/library/3at424ac.aspx) w kodzie, kontrolowania tego, czy występuje śledzenia i jest jak bardzo rozbudowane. Dzięki temu można monitorować stan aplikacji w środowisku produkcyjnym. Jest to szczególnie ważne w aplikacji biznesowej, która korzysta z wielu składników, działające na wielu komputerach. Aby uzyskać więcej informacji, zobacz [porady: Konfigurowanie przełączników śledzenia](https://msdn.microsoft.com/library/t06xyy08.aspx).

## <a name="configure-the-trace-listener-in-an-azure-application"></a>Skonfiguruj odbiornik śledzenia w aplikacji platformy Azure
Śledzenie debugowania, jak i TraceSource, wymagają skonfigurowaniu "odbiorniki", aby zebrać i zapisać wiadomości, które są wysyłane. Odbiorniki zbierać, przechowywać i kierowanie komunikatów śledzenia. Mogą kierować dane wyjściowe śledzenia do odpowiedniego obiektu docelowego, takie jak dziennik, okno lub pliku tekstowego. Diagnostyka Azure używa [DiagnosticMonitorTraceListener](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.diagnostics.diagnosticmonitortracelistener.aspx) klasy.

Przed wykonaniem następującej procedury, musisz zainicjować usługi Azure monitor diagnostyczny. Aby to zrobić, zobacz [Włączanie usługi Diagnostyka w systemie Microsoft Azure](cloud-services-dotnet-diagnostics.md).

Należy pamiętać, że jeśli używasz szablonów, które są dostarczane przez program Visual Studio, konfiguracji odbiornika jest automatycznie dodawany dla Ciebie.

### <a name="add-a-trace-listener"></a>Dodawanie odbiornika śledzenia
1. Otwórz plik web.config lub app.config dla roli użytkownika.
2. Dodaj następujący kod do pliku. Zmieniono atrybutu wersji, aby użyć numeru wersji zestawu, do którego się odwołujesz. Wersja zestawu nie zawsze zmiany wraz z każdą wersją zestawu Azure SDK, chyba że są dostępne aktualizacje do niego.
   
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
   > Upewnij się, że masz odwołanie do zestawu Microsoft.WindowsAzure.Diagnostics. Zaktualizowania numeru wersji w pliku xml powyżej, aby dopasować wersję przywoływanego zestawu Microsoft.WindowsAzure.Diagnostics.
   > 
   > 
3. Zapisz plik konfiguracji.

Aby uzyskać więcej informacji na temat odbiorników zobacz [detektorów śledzenia](https://msdn.microsoft.com/library/4y5y10s7.aspx).

Po wykonaniu kroków w celu dodania odbiornika należy dodawanie instrukcji śledzenia do kodu.

### <a name="to-add-trace-statement-to-your-code"></a>Dodawanie instrukcji śledzenia do kodu
1. Otwórz plik źródłowy aplikacji. Na przykład <RoleName>pliku CS roli procesu roboczego lub roli sieci web.
2. Dodaj następującą instrukcję using, jeśli nie został już dodany:
    ```
        using System.Diagnostics;
    ```
3. Dodawanie instrukcji śledzenia, w którym mają być przechwytywane informacje o stanie aplikacji. Można użyć różnych metod formatowania danych wyjściowych instrukcji śledzenia. Aby uzyskać więcej informacji, zobacz [porady: Dodawanie instrukcji śledzenia do kodu aplikacji](https://msdn.microsoft.com/library/zd83saa2.aspx).
4. Zapisz plik źródłowy.

