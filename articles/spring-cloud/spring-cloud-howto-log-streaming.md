---
title: Przesyłanie strumieniowe dzienników aplikacji w chmurze Azure wiosny w czasie rzeczywistym
description: Jak używać przesyłania strumieniowego dzienników do natychmiastowego wyświetlania dzienników aplikacji
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: how-to
ms.date: 01/14/2019
ms.openlocfilehash: fa2e7af51ff681da0bfdac928cc08bf75126a3b8
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/16/2020
ms.locfileid: "76156424"
---
# <a name="stream-azure-spring-cloud-app-logs-in-real-time"></a>Przesyłanie strumieniowe dzienników aplikacji w chmurze Azure wiosny w czasie rzeczywistym
Chmura sprężynowa Azure umożliwia przesyłanie strumieniowe dzienników w interfejsie wiersza polecenia platformy Azure w czasie rzeczywistym w celu rozwiązywania problemów. Możesz również [analizować dzienniki i metryki przy użyciu ustawień diagnostycznych](./diagnostic-services.md).

## <a name="prerequisites"></a>Wymagania wstępne

* Zainstaluj [rozszerzenie interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-cli#install-the-azure-cli-extension) dla chmury wiosennej, minimalną wersję 0.2.0.
* Wystąpienie **chmurowej platformy Azure** z uruchomioną aplikacją, na przykład [aplikacja Wiosenna w chmurze](./spring-cloud-quickstart-launch-app-cli.md).

## <a name="use-cli-to-tail-logs"></a>Używanie interfejsu wiersza polecenia do dzienników końcowych

Aby uniknąć wielokrotnego określania nazwy grupy zasobów i wystąpienia usługi, należy ustawić domyślną nazwę grupy zasobów i nazwę klastra.
```
az configure --defaults group=<service group name>
az configure --defaults spring-cloud=<service instance name>
```
W poniższych przykładach nazwa grupy zasobów i usługi zostanie pominięta w poleceniach.

### <a name="tail-log-for-app-with-single-instance"></a>Dziennik końcowy aplikacji z pojedynczym wystąpieniem
Jeśli aplikacja o nazwie auth-Service ma tylko jedno wystąpienie, można wyświetlić dziennik wystąpienia aplikacji za pomocą następującego polecenia:
```
az spring-cloud app log tail -n auth-service
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

### <a name="tail-log-for-app-with-multiple-instances"></a>Dziennik końcowy aplikacji z wieloma wystąpieniami
Jeśli istnieje wiele wystąpień dla aplikacji o nazwie `auth-service`, dziennik wystąpienia można wyświetlić przy użyciu opcji `-i/--instance`. Na przykład można przesłać strumieniowo dziennik wystąpienia jednej aplikacji, określając nazwę aplikacji i nazwę wystąpienia:

```
az spring-cloud app log tail -n auth-service -i auth-service-default-12-75cc4577fc-pw7hb
```
Możesz również pobrać wystąpienia aplikacji z Azure Portal. 
1. Przejdź do grupy zasobów i wybierz swoje wystąpienie chmury Azure wiosennej.
1. W oknie Omówienie wystąpienia chmury wiosennej na platformie Azure wybierz pozycję **aplikacje** w lewym okienku nawigacji.
1. Wybierz aplikację, a następnie kliknij pozycję **wystąpienia aplikacji** w okienku nawigacji po lewej stronie. 
1. Zostaną wyświetlone wystąpienia aplikacji.

### <a name="continuously-stream-new-logs"></a>Ciągle Przesyłaj strumieniowo nowe dzienniki
Domyślnie program `az spring-cloud ap log tail` drukuje tylko istniejące dzienniki przesyłane strumieniowo do konsoli aplikacji, a następnie kończy pracę. Jeśli chcesz przesyłać strumieniowo nowe dzienniki, Dodaj polecenie-f (--Obserwuj):  

```
az spring-cloud app log tail -n auth-service -f
``` 
Aby sprawdzić wszystkie obsługiwane opcje rejestrowania:
``` 
az spring-cloud app log tail -h 
```

## <a name="next-steps"></a>Następne kroki

* [Analizowanie dzienników i metryk przy użyciu ustawień diagnostycznych](./diagnostic-services.md)

 





