---
title: Przesyłanie strumieniowe dzienników aplikacji Azure Spring Cloud w czasie rzeczywistym
description: Jak natychmiast przeglądać dzienniki aplikacji za pomocą strumieniowania dzienników
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: how-to
ms.date: 01/14/2019
ms.openlocfilehash: fc208a3542528fb4554a365a02e13c2da3055cf2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78192204"
---
# <a name="stream-azure-spring-cloud-app-logs-in-real-time"></a>Przesyłanie strumieniowe dzienników aplikacji Azure Spring Cloud w czasie rzeczywistym
Usługa Azure Spring Cloud umożliwia przesyłanie strumieniowe dzienników w interfejsie wiersza polecenia platformy Azure w celu uzyskania dzienników konsoli aplikacji w czasie rzeczywistym w celu rozwiązywania problemów. Można również [analizować dzienniki i metryki z ustawieniami diagnostyki](./diagnostic-services.md).

## <a name="prerequisites"></a>Wymagania wstępne

* Zainstaluj [rozszerzenie interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-cli#install-the-azure-cli-extension) dla spring cloud w wersji minimalnej 0.2.0 .
* Wystąpienie **usługi Azure Spring Cloud** z uruchomiającą aplikacją, na przykład [aplikacją Spring Cloud](./spring-cloud-quickstart-launch-app-cli.md).

> [!NOTE]
>  Rozszerzenie ASC CLI jest aktualizowane z wersji 0.2.0 do 0.2.1. Ta zmiana wpływa na składnię polecenia przesyłania `az spring-cloud app log tail`strumieniowego dziennika: `az spring-cloud app logs`, które jest zastępowane przez: . Polecenie: `az spring-cloud app log tail` zostanie przestarzałe w przyszłej wersji. Jeśli używasz wersji 0.2.0, możesz uaktualnić do wersji 0.2.1. Najpierw usuń starą wersję za `az extension remove -n spring-cloud`pomocą polecenia: .  Następnie zainstaluj 0.2.1 za `az extension add -n spring-cloud`pomocą polecenia: .

## <a name="use-cli-to-tail-logs"></a>Użyj interfejsu wiersza do dzienników ogonowych

Aby uniknąć wielokrotnego określania nazwy grupy zasobów i wystąpienia usługi, należy ustawić domyślną nazwę grupy zasobów i nazwę klastra.
```
az configure --defaults group=<service group name>
az configure --defaults spring-cloud=<service instance name>
```
W poniższych przykładach grupa zasobów i nazwa usługi zostaną pominięte w poleceniach.

### <a name="tail-log-for-app-with-single-instance"></a>Dziennik ogona dla aplikacji z pojedynczym wystąpieniem
Jeśli aplikacja o nazwie auth-service ma tylko jedno wystąpienie, można wyświetlić dziennik wystąpienia aplikacji za pomocą następującego polecenia:
```
az spring-cloud app logs -n auth-service
```
Spowoduje to zwrócenie dzienników:
```
...
2020-01-15 01:54:40.481  INFO [auth-service,,,] 1 --- [main] o.apache.catalina.core.StandardService  : Starting service [Tomcat]
2020-01-15 01:54:40.482  INFO [auth-service,,,] 1 --- [main] org.apache.catalina.core.StandardEngine  : Starting Servlet engine: [Apache Tomcat/9.0.22]
2020-01-15 01:54:40.760  INFO [auth-service,,,] 1 --- [main] o.a.c.c.C.[Tomcat].[localhost].[/uaa]  : Initializing Spring embedded WebApplicationContext
2020-01-15 01:54:40.760  INFO [auth-service,,,] 1 --- [main] o.s.web.context.ContextLoader  : Root WebApplicationContext: initialization completed in 7203 ms

...
```

### <a name="tail-log-for-app-with-multiple-instances"></a>Dziennik ogona dla aplikacji z wieloma wystąpieniami
Jeśli dla aplikacji o nazwie `auth-service`istnieje wiele wystąpień, można `-i/--instance` wyświetlić dziennik wystąpień za pomocą tej opcji. 

Najpierw można uzyskać nazwy wystąpienia aplikacji za pomocą następującego polecenia.

```
az spring-cloud app show -n auth-service --query properties.activeDeployment.properties.instances -o table
```
Z wynikami:

```
Name                                         Status    DiscoveryStatus
-------------------------------------------  --------  -----------------
auth-service-default-12-75cc4577fc-pw7hb  Running   UP
auth-service-default-12-75cc4577fc-8nt4m  Running   UP
auth-service-default-12-75cc4577fc-n25mh  Running   UP
``` 
Następnie można przesyłać strumieniowo dzienniki wystąpienia `-i/--instance` aplikacji z opcją:

```
az spring-cloud app logs -n auth-service -i auth-service-default-12-75cc4577fc-pw7hb
```

Możesz również uzyskać szczegółowe informacje o wystąpieniach aplikacji z witryny Azure Portal.  Po wybraniu **aplikacji** w lewym okienku nawigacji usługi Azure Spring Cloud wybierz pozycję **Wystąpienia aplikacji**.

### <a name="continuously-stream-new-logs"></a>Ciągłe przesyłanie strumieniowe nowych dzienników
Domyślnie `az spring-cloud ap log tail` drukuje tylko istniejące dzienniki przesyłane strumieniowo do konsoli aplikacji, a następnie kończy pracę. Jeśli chcesz przesyłać strumieniowo nowe dzienniki, dodaj -f (--follow):  

```
az spring-cloud app logs -n auth-service -f
``` 
Aby sprawdzić wszystkie obsługiwane opcje rejestrowania:
``` 
az spring-cloud app logs -h 
```

## <a name="next-steps"></a>Następne kroki

* [Analizowanie dzienników i danych za pomocą ustawień diagnostycznych](./diagnostic-services.md)

 





