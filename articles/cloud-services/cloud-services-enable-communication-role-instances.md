---
title: Komunikacji dla ról, usług w chmurze | Dokumentacja firmy Microsoft
description: Wystąpienia roli usług w chmurze ma zdefiniowanych punktów końcowych (http, https, tcp, udp) dla nich komunikujących się z zewnątrz lub między innych wystąpień roli.
services: cloud-services
documentationcenter: ''
author: Thraka
manager: timlt
editor: ''
ms.assetid: 7008a083-acbe-4fb8-ae60-b837ef971ca1
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/14/2016
ms.author: adegeo
ms.openlocfilehash: 96ca9bb2d7a9f30a7d6492be43bfb44edc02fd93
ms.sourcegitcommit: 9a8b9a24d67ba7b779fa34e67d7f2b45c941785e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/08/2018
ms.locfileid: "27701894"
---
# <a name="enable-communication-for-role-instances-in-azure"></a>Włącz komunikację dla wystąpień roli w systemie azure
Role usługi w chmurze komunikują się za pośrednictwem połączeń wewnętrznych i zewnętrznych. Połączenia zewnętrzne są nazywane **wejściowych punktów końcowych** podczas połączenia wewnętrznego są nazywane **wewnętrznych punktów końcowych**. W tym temacie opisano sposób modyfikowania [definicji usługi](cloud-services-model-and-package.md#csdef) do utworzenia punktów końcowych.

## <a name="input-endpoint"></a>Wejściowy punkt końcowy
Wejściowy punkt końcowy jest używany, gdy chcesz udostępnić portu na zewnątrz. Należy określić typ protokół i port punktu końcowego, który następnie dotyczy zarówno wewnętrznych i zewnętrznych portów dla punktu końcowego. Jeśli chcesz, można określić inny port wewnętrzny dla punktu końcowego o [port lokalny](https://msdn.microsoft.com/library/azure/gg557552.aspx#InputEndpoint) atrybutu.

Wejściowy punkt końcowy, można użyć następujących protokołów: **http, https, tcp, udp**.

Aby utworzyć wejściowy punkt końcowy, dodać **InputEndpoint** elementu podrzędnego do **punkty końcowe** element roli sieci web lub procesu roboczego.

```xml
<Endpoints>
  <InputEndpoint name="StandardWeb" protocol="http" port="80" localPort="80" />
</Endpoints> 
```

## <a name="instance-input-endpoint"></a>Wystąpienie wejściowy punkt końcowy
Wystąpienie wejściowych punktów końcowych są podobne do danych wejściowych punktów końcowych, ale można mapować określone porty publicznych dla poszczególnych wystąpień poszczególnych ról przy użyciu portu przekazywania modułu równoważenia obciążenia. Można określić jeden port publicznych, lub zakresem portów.

Wejściowy punkt końcowy wystąpienia można używać tylko **tcp** lub **udp** jako protokół.

Aby utworzyć wystąpienie wejściowy punkt końcowy, Dodaj **InstanceInputEndpoint** elementu podrzędnego do **punkty końcowe** element roli sieci web lub procesu roboczego.

```xml
<Endpoints>
  <InstanceInputEndpoint name="Endpoint2" protocol="tcp" localPort="10100">
    <AllocatePublicPortFrom>
      <FixedPortRange max="10109" min="10105" />
    </AllocatePublicPortFrom>
  </InstanceInputEndpoint>
</Endpoints>
```

## <a name="internal-endpoint"></a>Wewnętrzny punkt końcowy
Wewnętrznych punktów końcowych są dostępne dla komunikacji wystąpienie do wystąpienia. Port jest opcjonalna i pominięcie port dynamiczny jest przypisana do punktu końcowego. Zakres portów może służyć. Brak limitu pięciu wewnętrznych punktów końcowych dla każdej roli.

Wewnętrzny punkt końcowy, można użyć następujących protokołów: **http, tcp, udp, wszelkie**.

Aby utworzyć wewnętrznego wejściowy punkt końcowy, Dodaj **InternalEndpoint** elementu podrzędnego do **punkty końcowe** element roli sieci web lub procesu roboczego.

```xml
<Endpoints>
  <InternalEndpoint name="Endpoint3" protocol="any" port="8999" />
</Endpoints> 
```

Można również użyć zakresu portów.

```xml
<Endpoints>
  <InternalEndpoint name="Endpoint3" protocol="any">
    <FixedPortRange max="8999" min="8995" />
  </InternalEndpoint>
</Endpoints>
```


## <a name="worker-roles-vs-web-roles"></a>Vs role proces roboczy. Role sieci Web
Brak jednego niewielkiej różnicy z punktami końcowymi, podczas pracy z ról sieć web i proces roboczy. Rola sieci web musi mieć co najmniej jeden wejściowy punkt końcowy za pomocą **HTTP** protokołu.

```xml
<Endpoints>
  <InputEndpoint name="StandardWeb" protocol="http" port="80" localPort="80" />
  <!-- more endpoints may be declared after the first InputEndPoint -->
</Endpoints>
```

## <a name="using-the-net-sdk-to-access-an-endpoint"></a>Otwieranie punktu końcowego za pomocą zestawu .NET SDK
Biblioteki zarządzane Azure udostępnia metody dla wystąpień roli do komunikacji w czasie wykonywania. Z kodu uruchomionego w wystąpieniu roli można pobrać informacji na temat innych wystąpień roli i ich punktów końcowych, a także informacje o bieżącym wystąpieniu roli.

> [!NOTE]
> Można tylko pobieranie informacji na temat wystąpień roli, które działają w usługi w chmurze i definiującą co najmniej jeden wewnętrzny punkt końcowy. Nie można uzyskać danych dotyczących wystąpień roli w innej usługi.
> 
> 

Można użyć [wystąpień](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.role.instances.aspx) właściwości można pobrać wystąpień roli. Należy najpierw użyć [CurrentRoleInstance](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.currentroleinstance.aspx) zwraca odwołanie do bieżącego wystąpienia roli, a następnie użyć [roli](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleinstance.role.aspx) właściwość zwraca odwołanie do samej siebie roli.

Po podłączeniu do wystąpienia roli, programowo przy użyciu zestawu .NET SDK jest stosunkowo łatwa do uzyskania dostępu do informacji punktu końcowego. Na przykład po nawiązaniu połączenia już w środowisku określoną rolę, możesz uzyskać portu określonego punktu końcowego o tym kodzie:

```csharp
int port = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["StandardWeb"].IPEndpoint.Port;
```

**Wystąpień** właściwość zwraca kolekcję **RoleInstance** obiektów. Ta kolekcja zawsze zawiera bieżącego wystąpienia. Jeśli rola nie ma zdefiniowanej wewnętrzny punkt końcowy, Kolekcja zawiera bieżącego wystąpienia, ale żadne inne wystąpienia. Liczba wystąpień roli w kolekcji będą zawsze miały 1 w przypadku, gdy żaden wewnętrzny punkt końcowy jest zdefiniowany dla roli. Jeśli rola definiuje wewnętrzny punkt końcowy, jego wystąpienia są wykrywalny w czasie wykonywania, a liczba wystąpień w kolekcji odpowiada liczba wystąpień określone dla roli w pliku konfiguracji usługi.

> [!NOTE]
> Biblioteki zarządzane Azure zapewnia sposób określania kondycji innych wystąpień roli, ale jeśli usługa wymaga tej funkcji można wdrożyć w takiej oceny kondycji. Można użyć [diagnostyki Azure](cloud-services-dotnet-diagnostics.md) można uzyskać informacji o uruchomionych wystąpień roli.
> 
> 

Aby określić numer portu wewnętrzny punkt końcowy w wystąpieniu roli, można użyć [InstanceEndpoints](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleinstance.instanceendpoints.aspx) właściwości, aby zwrócić obiekt słownika zawierający nazwy punktu końcowego i ich odpowiedniego adresu IP, adresy i porty. [IPEndpoint](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleinstanceendpoint.ipendpoint.aspx) właściwość zwraca adres IP i port dla określonego punktu końcowego. **PublicIPEndpoint** właściwość zwraca port punktu końcowego o zrównoważonym obciążeniu. Adres IP część **PublicIPEndpoint** właściwość nie jest używana.

Oto przykład, który iteruje po wystąpień roli.

```csharp
foreach (RoleInstance roleInst in RoleEnvironment.CurrentRoleInstance.Role.Instances)
{
    Trace.WriteLine("Instance ID: " + roleInst.Id);
    foreach (RoleInstanceEndpoint roleInstEndpoint in roleInst.InstanceEndpoints.Values)
    {
        Trace.WriteLine("Instance endpoint IP address and port: " + roleInstEndpoint.IPEndpoint);
    }
}
```

Oto przykład roli procesu roboczego, która pobiera punktu końcowego udostępniane za pośrednictwem definicji usługi i rozpoczyna nasłuchiwanie dla połączenia.

> [!WARNING]
> Ten kod działa tylko dla wdrożonej usługi. Podczas uruchamiania w emulatorze obliczeniowe Azure, elementy konfiguracji, które utworzyć bezpośredniej portów punkty końcowe usługi (**InstanceInputEndpoint** elementy) są ignorowane.
> 
> 

```csharp
using System;
using System.Diagnostics;
using System.Linq;
using System.Net;
using System.Net.Sockets;
using System.Threading;
using Microsoft.WindowsAzure;
using Microsoft.WindowsAzure.Diagnostics;
using Microsoft.WindowsAzure.ServiceRuntime;
using Microsoft.WindowsAzure.StorageClient;

namespace WorkerRole1
{
  public class WorkerRole : RoleEntryPoint
  {
    public override void Run()
    {
      try
      {
        // Initialize method-wide variables
        var epName = "Endpoint1";
        var roleInstance = RoleEnvironment.CurrentRoleInstance;

        // Identify direct communication port
        var myPublicEp = roleInstance.InstanceEndpoints[epName].PublicIPEndpoint;
        Trace.TraceInformation("IP:{0}, Port:{1}", myPublicEp.Address, myPublicEp.Port);

        // Identify public endpoint
        var myInternalEp = roleInstance.InstanceEndpoints[epName].IPEndpoint;

        // Create socket listener
        var listener = new Socket(
          myInternalEp.AddressFamily, SocketType.Stream, ProtocolType.Tcp);

        // Bind socket listener to internal endpoint and listen
        listener.Bind(myInternalEp);
        listener.Listen(10);
        Trace.TraceInformation("Listening on IP:{0},Port: {1}",
          myInternalEp.Address, myInternalEp.Port);

        while (true)
        {
          // Block the thread and wait for a client request
          Socket handler = listener.Accept();
          Trace.TraceInformation("Client request received.");

          // Define body of socket handler
          var handlerThread = new Thread(
            new ParameterizedThreadStart(h =>
            {
              var socket = h as Socket;
              Trace.TraceInformation("Local:{0} Remote{1}",
                socket.LocalEndPoint, socket.RemoteEndPoint);

              // Shut down and close socket
              socket.Shutdown(SocketShutdown.Both);
              socket.Close();
            }
          ));

          // Start socket handler on new thread
          handlerThread.Start(handler);
        }
      }
      catch (Exception e)
      {
        Trace.TraceError("Caught exception in run. Details: {0}", e);
      }
    }

    public override bool OnStart()
    {
      // Set the maximum number of concurrent connections 
      ServicePointManager.DefaultConnectionLimit = 12;

      // For information on handling configuration changes
      // see the MSDN topic at http://go.microsoft.com/fwlink/?LinkId=166357.
      return base.OnStart();
    }
  }
}
```

## <a name="network-traffic-rules-to-control-role-communication"></a>Reguły ruchu sieciowego do kontrolowania komunikacji roli
Po zdefiniowaniu wewnętrznych punktów końcowych, można dodać do kontroli, jak wystąpień roli może komunikować się ze sobą reguły ruchu sieciowego (w oparciu punktów końcowych, które zostały utworzone). Na poniższym diagramie przedstawiono kilka typowych scenariuszy kontroli komunikacji roli:

![Scenariusze reguły ruchu sieciowego](./media/cloud-services-enable-communication-role-instances/scenarios.png "scenariusze reguły ruchu sieciowego")

Poniższy przykład kodu pokazuje definicje ról dla ról przedstawione na diagramie poprzedniej. Każda definicja roli obejmuje co najmniej jeden wewnętrzny punkt końcowy zdefiniowany:

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <WebRole name="WebRole1" vmsize="Medium">
    <Sites>
      <Site name="Web">
        <Bindings>
          <Binding name="HttpIn" endpointName="HttpIn" />
        </Bindings>
      </Site>
    </Sites>
    <Endpoints>
      <InputEndpoint name="HttpIn" protocol="http" port="80" />
      <InternalEndpoint name="InternalTCP1" protocol="tcp" />
    </Endpoints>
  </WebRole>
  <WorkerRole name="WorkerRole1">
    <Endpoints>
      <InternalEndpoint name="InternalTCP2" protocol="tcp" />
    </Endpoints>
  </WorkerRole>
  <WorkerRole name="WorkerRole2">
    <Endpoints>
      <InternalEndpoint name="InternalTCP3" protocol="tcp" />
      <InternalEndpoint name="InternalTCP4" protocol="tcp" />
    </Endpoints>
  </WorkerRole>
</ServiceDefinition>
```

> [!NOTE]
> Ograniczenia komunikacji między rolami może wystąpić z wewnętrznych punktów końcowych zarówno stały i przypisywane automatycznie portów.
> 
> 

Domyślnie po zdefiniowaniu wewnętrzny punkt końcowy komunikacji mogą przepływać z dowolnej roli do wewnętrzny punkt końcowy roli bez ograniczeń. Aby ograniczyć komunikacji, należy dodać **NetworkTrafficRules** elementu **ServiceDefinition** elementu w pliku definicji usługi.

### <a name="scenario-1"></a>Scenariusz 1
Zezwalaj tylko na ruch sieciowy z **WebRole1** do **WorkerRole1**.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo>
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP2" roleName="WorkerRole1"/>
      </Destinations>
      <AllowAllTraffic/>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WebRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
</ServiceDefinition>
```

### <a name="scenario-2"></a>Scenariusz 2
Zezwala na ruch sieciowy z **WebRole1** do **WorkerRole1** i **WorkerRole2**.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo>
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP2" roleName="WorkerRole1"/>
        <RoleEndpoint endpointName="InternalTCP3" roleName="WorkerRole2"/>
      </Destinations>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WebRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
</ServiceDefinition>
```

### <a name="scenario-3"></a>Scenariusz 3
Zezwala na ruch sieciowy z **WebRole1** do **WorkerRole1**, i **WorkerRole1** do **WorkerRole2**.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo>
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP2" roleName="WorkerRole1"/>
      </Destinations>
      <AllowAllTraffic/>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WebRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo>
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP3" roleName="WorkerRole2"/>
      </Destinations>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WorkerRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
</ServiceDefinition>
```

### <a name="scenario-4"></a>Scenariusz 4
Zezwala na ruch sieciowy z **WebRole1** do **WorkerRole1**, **WebRole1** do **WorkerRole2**, i **WorkerRole1**  do **WorkerRole2**.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo>
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP2" roleName="WorkerRole1"/>
      </Destinations>
      <AllowAllTraffic/>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WebRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo >
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP3" roleName="WorkerRole2"/>
      </Destinations>
      <AllowAllTraffic/>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WorkerRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo >
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP4" roleName="WorkerRole2"/>
      </Destinations>
      <AllowAllTraffic/>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WebRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
</ServiceDefinition>
```

Znajduje się odwołanie do schematu XML dla elementów użyta powyżej [tutaj](https://msdn.microsoft.com/library/azure/gg557551.aspx).

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej o usługę w chmurze [modelu](cloud-services-model-and-package.md).

