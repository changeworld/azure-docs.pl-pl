---
title: Śledzenie przepływu w aplikacji usług w chmurze za pomocą diagnostyki platformy Azure
titleSuffix: Azure Cloud Services
description: Dodaj komunikaty śledzenia do aplikacji platformy Azure, aby ułatwić debugowanie, mierzenie wydajności, monitorowanie, analizę ruchu i inne.
services: cloud-services
documentationcenter: .net
author: tgore03
ms.service: cloud-services
ms.devlang: dotnet
ms.topic: article
ms.date: 02/20/2016
ms.author: tagore
ms.openlocfilehash: 47a33ba27dd6d2df626d93695c421303bace6a0b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75386514"
---
# <a name="trace-the-flow-of-a-cloud-services-application-with-azure-diagnostics"></a>Śledzenie przepływu aplikacji usług w chmurze za pomocą diagnostyki platformy Azure
Śledzenie jest sposobem monitorowania wykonywania aplikacji, gdy jest uruchomiona. Za pomocą klas [System.Diagnostics.Trace](/dotnet/api/system.diagnostics.trace), [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug)i [System.Diagnostics.TraceSource](/dotnet/api/system.diagnostics.tracesource) można rejestrować informacje o błędach i wykonywaniu aplikacji w dziennikach, plikach tekstowych lub innych urządzeniach do późniejszej analizy. Aby uzyskać więcej informacji na temat śledzenia, zobacz [Śledzenie i instrumentacja aplikacji](/dotnet/framework/debug-trace-profile/tracing-and-instrumenting-applications).

## <a name="use-trace-statements-and-trace-switches"></a>Używanie instrukcji śledzenia i przełączników śledzenia
Zaimplementuj śledzenie w aplikacji usług w chmurze, dodając [DiagnosticMonitorTraceListener](/previous-versions/azure/reference/ee758610(v=azure.100)) do konfiguracji aplikacji i nawiązując wywołania do System.Diagnostics.Trace lub System.Diagnostics.Debug w kodzie aplikacji. Użyj pliku konfiguracji *app.config* dla ról procesu roboczego i *web.config* dla ról sieci web. Podczas tworzenia nowej usługi hostowane przy użyciu szablonu programu Visual Studio, diagnostyka Azure jest automatycznie dodawany do projektu i DiagnosticMonitorTraceListener jest dodawany do pliku konfiguracji odpowiednie dla ról, które można dodać.

Aby uzyskać informacje na temat umieszczania instrukcji śledzenia, zobacz [Jak: Dodawanie instrukcji śledzenia do kodu aplikacji](/dotnet/framework/debug-trace-profile/how-to-add-trace-statements-to-application-code).

Umieszczając [przełączniki śledzenia](/dotnet/framework/debug-trace-profile/trace-switches) w kodzie, można kontrolować, czy śledzenie występuje i jak obszerne jest. Dzięki temu można monitorować stan aplikacji w środowisku produkcyjnym. Jest to szczególnie ważne w aplikacji biznesowej, która używa wielu składników uruchomionych na wielu komputerach. Aby uzyskać więcej informacji, zobacz [Jak: Konfigurowanie przełączników śledzenia](/dotnet/framework/debug-trace-profile/how-to-create-initialize-and-configure-trace-switches).

## <a name="configure-the-trace-listener-in-an-azure-application"></a>Konfigurowanie odbiornika śledzenia w aplikacji platformy Azure
Śledzenie, debugowanie i tracesource, wymagają skonfigurowania "odbiorników" do zbierania i rejestrowania wiadomości, które są wysyłane. Detektory zbierają, przechowują i trasy śledzenia wiadomości. Kierują dane wyjściowe śledzenia do odpowiedniego obiektu docelowego, takiego jak dziennik, okno lub plik tekstowy. Usługa Azure Diagnostics używa klasy [DiagnosticMonitorTraceListener.](/previous-versions/azure/reference/ee758610(v=azure.100))

Przed wykonaniem poniższej procedury należy zainicjować monitor diagnostyczny platformy Azure. Aby to zrobić, zobacz [Włączanie diagnostyki na platformie Microsoft Azure](cloud-services-dotnet-diagnostics.md).

Należy zauważyć, że jeśli używasz szablonów, które są dostarczane przez program Visual Studio, konfiguracja odbiornika jest dodawany automatycznie dla Ciebie.

### <a name="add-a-trace-listener"></a>Dodawanie odbiornika śledzenia
1. Otwórz plik web.config lub app.config dla swojej roli.
2. Dodaj do pliku następujący kod. Zmień atrybut Version, aby użyć numeru wersji zestawu, do którego się odwołujesz. Wersja zestawu nie musi się zmieniać z każdej wersji zestawu Azure SDK, chyba że są aktualizacje do niego.
   
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
   > Upewnij się, że masz odwołanie do projektu do zestawu Microsoft.WindowsAzure.Diagnostics. Zaktualizuj numer wersji w powyższym pliku xml, aby dopasować go do wersji zestawu Microsoft.WindowsAzure.Diagnostics, do którego istnieje odwołanie.
   > 
   > 
3. Zapisz plik konfiguracyjny.

Aby uzyskać więcej informacji o odbiornikach, zobacz [Śledzenie odbiorników](/dotnet/framework/debug-trace-profile/trace-listeners).

Po wykonaniu kroków, aby dodać odbiornik, można dodać instrukcje śledzenia do kodu.

### <a name="to-add-trace-statement-to-your-code"></a>Aby dodać instrukcję śledzenia do kodu
1. Otwórz plik źródłowy dla aplikacji. Na przykład \<plik RoleName>.cs dla roli procesu roboczego lub roli sieci web.
2. Dodaj następujące przy użyciu dyrektywy, jeśli nie została jeszcze dodana:
    ```
        using System.Diagnostics;
    ```
3. Dodaj instrukcje Trace, w którym chcesz przechwycić informacje o stanie aplikacji. Można użyć różnych metod, aby sformatować dane wyjściowe Trace instrukcji. Aby uzyskać więcej informacji, zobacz [Jak: Dodawanie instrukcji śledzenia do kodu aplikacji](/dotnet/framework/debug-trace-profile/how-to-add-trace-statements-to-application-code).
4. Zapisz plik źródłowy.




