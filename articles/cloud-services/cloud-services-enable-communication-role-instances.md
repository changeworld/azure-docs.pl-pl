---
title: Komunikacji dla ról w usługach Cloud Services | Dokumentacja firmy Microsoft
description: Wystąpienia roli w usługach w chmurze może mieć zdefiniowanych punktów końcowych (http, https, tcp, udp) dla nich, które komunikują się z zewnątrz lub od innych wystąpień roli.
services: cloud-services
documentationcenter: ''
author: jpconnock
manager: timlt
editor: ''
ms.assetid: 7008a083-acbe-4fb8-ae60-b837ef971ca1
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/14/2016
ms.author: jeconnoc
ms.openlocfilehash: 8b521ebe869210b66ac3b3efeebda873f7c0e50b
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59792484"
---
# <a name="enable-communication-for-role-instances-in-azure"></a>Włącz komunikację dla wystąpień ról na platformie azure
Role usługi w chmurze komunikują się za pośrednictwem połączeń wewnętrznych i zewnętrznych. Połączenia zewnętrzne są nazywane **wejściowych punktów końcowych** podczas połączenia wewnętrzne są nazywane **wewnętrznych punktów końcowych**. W tym temacie opisano sposób modyfikowania [definicji usługi](cloud-services-model-and-package.md#csdef) do tworzenia punktów końcowych.

## <a name="input-endpoint"></a>Wejściowy punkt końcowy
Wejściowy punkt końcowy jest używany, gdy chcesz uwidocznić port na zewnątrz. Należy określić typ protokół i port punktu końcowego, który następnie stosuje dla obu portów wewnętrznych i zewnętrznych dla punktu końcowego. Jeśli chcesz, można określić inny port wewnętrzny dla punktu końcowego o [localPort](/previous-versions/azure/reference/gg557552(v=azure.100)#InputEndpoint) atrybutu.

Wejściowy punkt końcowy, można użyć następujących protokołów: **http, https, tcp, udp**.

Aby utworzyć wejściowy punkt końcowy, Dodaj **InputEndpoint** element podrzędny do **punktów końcowych** element rola sieci web lub proces roboczy.

```xml
<Endpoints>
  <InputEndpoint name="StandardWeb" protocol="http" port="80" localPort="80" />
</Endpoints> 
```

## <a name="instance-input-endpoint"></a>Wystąpienie wejściowy punkt końcowy
Wejściowe punkty końcowe wystąpienia są podobne do danych wejściowych punktów końcowych jednak pozwala na mapowanie określonych portów publicznego dla poszczególnych wystąpień pojedynczej roli przy użyciu przekierowania portów w module równoważenia obciążenia. Można określić pojedynczy port publicznego lub zakres portów.

Wejściowy punkt końcowy wystąpienia można używać tylko **tcp** lub **udp** jako protokół.

Aby utworzyć wystąpienie wejściowy punkt końcowy, Dodaj **InstanceInputEndpoint** element podrzędny do **punktów końcowych** element rola sieci web lub proces roboczy.

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
Wewnętrznych punktów końcowych są dostępne w komunikacji z wystąpienia do wystąpienia. Numer portu jest opcjonalny, a w przypadku pominięcia port dynamiczny jest przypisany do punktu końcowego. Zakres portów może służyć. Ma limitu pięciu wewnętrznych punktów końcowych w każdej roli.

Wewnętrzny punkt końcowy, można użyć następujących protokołów: **http, tcp, udp, wszelkie**.

Aby utworzyć wewnętrzne wejściowy punkt końcowy, Dodaj **InternalEndpoint** element podrzędny do **punktów końcowych** element rola sieci web lub proces roboczy.

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


## <a name="worker-roles-vs-web-roles"></a>Vs ról procesów roboczych. Role sieci Web
Podczas pracy z ról sieci web i proces roboczy ma jeden niewielkiej różnicy z punktami końcowymi. Rola sieci web musi mieć co najmniej jeden wejściowy punkt końcowy przy użyciu **HTTP** protokołu.

```xml
<Endpoints>
  <InputEndpoint name="StandardWeb" protocol="http" port="80" localPort="80" />
  <!-- more endpoints may be declared after the first InputEndPoint -->
</Endpoints>
```

## <a name="using-the-net-sdk-to-access-an-endpoint"></a>Dostęp do punktu końcowego przy użyciu zestawu .NET SDK
Biblioteka zarządzana platformy Azure udostępnia metody dla wystąpień roli do komunikacji w czasie wykonywania. Z poziomu kodu w ramach wystąpienia roli można pobrać informacji o innych wystąpień roli i ich punktami końcowymi, a także informacje o bieżącym wystąpieniu roli.

> [!NOTE]
> Możesz pobrać wyłącznie informacje o wystąpieniach roli są uruchamiane w usłudze w chmurze i definiują co najmniej jeden punkt końcowy wewnętrznego. Nie można uzyskać danych dotyczących ról wystąpień uruchomionych w innej usłudze.
> 
> 

Możesz użyć [wystąpień](/previous-versions/azure/reference/ee741904(v=azure.100)) właściwość służąca do pobierania wystąpień roli. Najpierw użyj [CurrentRoleInstance](/previous-versions/azure/reference/ee741907(v=azure.100)) zwraca odwołanie do bieżącego wystąpienia roli, a następnie użyć [roli](/previous-versions/azure/reference/ee741918(v=azure.100)) właściwość zwraca odwołanie do samego roli.

Po nawiązaniu połączenia z wystąpieniem roli programowo przy użyciu zestawu .NET SDK, jest stosunkowo łatwo uzyskać dostęp do informacji punktu końcowego. Na przykład po którym już nawiązano środowisku określoną rolę, można uzyskać portu określonego punktu końcowego przy użyciu tego kodu:

```csharp
int port = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["StandardWeb"].IPEndpoint.Port;
```

**Wystąpień** właściwość zwraca kolekcję **RoleInstance** obiektów. Ta kolekcja zawsze zawiera bieżącego wystąpienia. Jeśli rola nie definiuje wewnętrzny punkt końcowy, Kolekcja zawiera bieżące wystąpienie, ale nie innych wystąpień. Liczba wystąpień roli w kolekcji zawsze będzie 1 w przypadku, w którym nie wewnętrzny punkt końcowy jest zdefiniowana dla roli. Jeśli rola definiuje wewnętrzny punkt końcowy, jego wystąpienia są wykrywalni w czasie wykonywania, a liczby wystąpień w kolekcji będą odpowiadać liczby wystąpień określone dla roli w pliku konfiguracji usługi.

> [!NOTE]
> Biblioteka zarządzana platformy Azure nie zapewnia sposób określania kondycji innych wystąpień roli, ale jeśli Twoja usługa musi tę funkcję można zaimplementować w takich oceny kondycji. Możesz użyć [diagnostyki Azure](cloud-services-dotnet-diagnostics.md) można uzyskać informacji o uruchomionych wystąpień roli.
> 
> 

Aby określić numer portu dla wewnętrznego punktu końcowego dla wystąpienia roli, można użyć [InstanceEndpoints](/previous-versions/azure/reference/ee741917(v=azure.100)) właściwości, aby zwrócić obiekt słownika, która zawiera nazwy punktów końcowych i ich odpowiedni adres IP, adresów i portów. [IPEndpoint](/previous-versions/azure/reference/ee741919(v=azure.100)) właściwość zwraca adres IP i portu dla określonego punktu końcowego. **PublicIPEndpoint** właściwość zwraca port punktu końcowego o zrównoważonym obciążeniu. Części adresu **PublicIPEndpoint** właściwość nie jest używana.

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

Oto przykład roli procesu roboczego, pobiera punktu końcowego uwidocznionego przez definicji usługi, która rozpoczyna się nasłuchiwać w poszukiwaniu połączeń.

> [!WARNING]
> Ten kod będzie działać tylko dla wdrożonej usługi. Podczas pracy w emulatorze obliczeń systemu Azure, usługi elementy konfiguracji, służące do tworzenia punktów końcowych przeniesiony bezpośrednio (**InstanceInputEndpoint** elementy), są ignorowane.
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
      // see the MSDN topic at https://go.microsoft.com/fwlink/?LinkId=166357.
      return base.OnStart();
    }
  }
}
```

## <a name="network-traffic-rules-to-control-role-communication"></a>Reguły ruchu sieciowego do kontrolowania komunikacji roli
Po zdefiniowaniu wewnętrznych punktów końcowych, można dodać reguły ruchu sieciowego (oparte na punktach końcowych, które zostały utworzone) do kontroli, jak wystąpień roli może komunikować się ze sobą. Na poniższym diagramie przedstawiono kilka typowych scenariuszy, do kontrolowania komunikacji roli:

![Scenariusze reguł ruchu sieci](./media/cloud-services-enable-communication-role-instances/scenarios.png "scenariusze reguł ruchu w sieci")

Poniższy przykład kodu pokazuje definicje ról dla ról pokazano na poprzednim rysunku. Każda definicja roli obejmuje co najmniej jeden wewnętrzny punkt końcowy zdefiniowany:

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
> Ograniczenia komunikacji między rolami może wystąpić z wewnętrznych punktów końcowych zarówno stała i przypisywany portów.
> 
> 

Domyślnie po zdefiniowaniu wewnętrzny punkt końcowy komunikacji mogą przepływać z dowolnej roli do wewnętrznego punktu końcowego roli bez żadnych ograniczeń. Aby ograniczyć komunikację, należy dodać **networktrafficrules —** elementu **definicja ServiceDefinition** elementu w pliku definicji usługi.

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
Zezwala tylko na ruch sieciowy z **WebRole1** do **WorkerRole1** i **WorkerRole2**.

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
Zezwala tylko na ruch sieciowy z **WebRole1** do **WorkerRole1**, i **WorkerRole1** do **WorkerRole2**.

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
Zezwala tylko na ruch sieciowy z **WebRole1** do **WorkerRole1**, **WebRole1** do **WorkerRole2**, i **WorkerRole1**  do **WorkerRole2**.

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

Odwołanie do schematu XML dla elementów powyżej można znaleźć [tutaj](/previous-versions/azure/reference/gg557551(v=azure.100)).

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej o usłudze w chmurze [modelu](cloud-services-model-and-package.md).

