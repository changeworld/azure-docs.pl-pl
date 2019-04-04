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
ms.openlocfilehash: f0724fd6e5f08f3e09bcb147c12d1657235dc704
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/04/2019
ms.locfileid: "58916890"
---
# <a name="trace-the-flow-of-a-cloud-services-application-with-azure-diagnostics"></a>Śledzenie przepływu aplikacji usługi w chmurze przy użyciu diagnostyki Azure
Śledzenie jest sposobem monitorowania wykonywanie aplikacji, gdy jest on uruchomiony. Możesz użyć [System.Diagnostics.Trace](/dotnet/api/system.diagnostics.trace), [system.Diagnostics.Debug —](/dotnet/api/system.diagnostics.debug), i [System.Diagnostics.TraceSource](/dotnet/api/system.diagnostics.tracesource) klasy do rejestrowania informacji o błędach i wykonywanie aplikacji w dzienniki, pliki tekstowe lub innych urządzeń do późniejszej analizy. Aby uzyskać więcej informacji na temat śledzenia, zobacz [śledzenie i Instrumentacja aplikacji](/dotnet/framework/debug-trace-profile/tracing-and-instrumenting-applications).

## <a name="use-trace-statements-and-trace-switches"></a>Użyj instrukcji śledzenia i przełączników śledzenia
Implementowanie śledzenia w aplikacji usługi w chmurze, dodając [DiagnosticMonitorTraceListener](/previous-versions/azure/reference/ee758610(v=azure.100)) konfiguracji aplikacji i wykonywania wywołań do System.Diagnostics.Trace lub system.Diagnostics.Debug — w sieci Kod aplikacji. Użyj pliku konfiguracji *app.config* dla ról procesów roboczych i *web.config* dla ról sieci web. Kiedy tworzysz nową usługę hostowaną, przy użyciu szablonu programu Visual Studio, usługi Azure Diagnostics jest automatycznie dodawany do projektu i DiagnosticMonitorTraceListener jest dodawany do odpowiedniego pliku konfiguracyjnego dla ról, które dodajesz.

Aby uzyskać informacje na umieszczenie instrukcji śledzenia, zobacz [jak: Dodawanie instrukcji śledzenia do kodu aplikacji](/dotnet/framework/debug-trace-profile/how-to-add-trace-statements-to-application-code).

Umieszczając [przełączniki śledzenia](/dotnet/framework/debug-trace-profile/trace-switches) w kodzie, kontrolowania tego, czy występuje śledzenia i jest jak bardzo rozbudowane. Dzięki temu można monitorować stan aplikacji w środowisku produkcyjnym. Jest to szczególnie ważne w aplikacji biznesowej, która korzysta z wielu składników, działające na wielu komputerach. Aby uzyskać więcej informacji, zobacz [Instrukcje: Konfigurowanie przełączników śledzenia](/dotnet/framework/debug-trace-profile/how-to-create-initialize-and-configure-trace-switches).

## <a name="configure-the-trace-listener-in-an-azure-application"></a>Skonfiguruj odbiornik śledzenia w aplikacji platformy Azure
Śledzenie debugowania, jak i TraceSource, wymagają skonfigurowaniu "odbiorniki", aby zebrać i zapisać wiadomości, które są wysyłane. Odbiorniki zbierać, przechowywać i kierowanie komunikatów śledzenia. Mogą kierować dane wyjściowe śledzenia do odpowiedniego obiektu docelowego, takie jak dziennik, okno lub pliku tekstowego. Diagnostyka Azure używa [DiagnosticMonitorTraceListener](/previous-versions/azure/reference/ee758610(v=azure.100)) klasy.

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

Aby uzyskać więcej informacji na temat odbiorników zobacz [detektorów śledzenia](/dotnet/framework/debug-trace-profile/trace-listeners).

Po wykonaniu kroków w celu dodania odbiornika należy dodawanie instrukcji śledzenia do kodu.

### <a name="to-add-trace-statement-to-your-code"></a>Dodawanie instrukcji śledzenia do kodu
1. Otwórz plik źródłowy aplikacji. Na przykład <RoleName>pliku CS roli procesu roboczego lub roli sieci web.
2. Dodaj następującą instrukcję using, jeśli nie został już dodany:
    ```
        using System.Diagnostics;
    ```
3. Dodawanie instrukcji śledzenia, w którym mają być przechwytywane informacje o stanie aplikacji. Można użyć różnych metod formatowania danych wyjściowych instrukcji śledzenia. Aby uzyskać więcej informacji, zobacz [Instrukcje: Dodawanie instrukcji śledzenia do kodu aplikacji](/dotnet/framework/debug-trace-profile/how-to-add-trace-statements-to-application-code).
4. Zapisz plik źródłowy.

