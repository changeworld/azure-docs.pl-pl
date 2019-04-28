---
title: (PRZESTARZAŁE) Klaster DC/OS usługi Azure Monitor — Dynatrace
description: Monitorowanie klastra usługi Azure Container Service DC/OS za pomocą rozwiązania Dynatrace. Wdróż Dynatrace OneAgent za pomocą pulpitu nawigacyjnego platformy DC/OS.
services: container-service
author: MartinGoodwell
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 12/13/2016
ms.author: rogardle
ms.custom: mvc
ms.openlocfilehash: 8f34a00d9256c288a2842e905c06d5336522eece
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62119861"
---
# <a name="deprecated-monitor-an-azure-container-service-dcos-cluster-with-dynatrace-saasmanaged"></a>(PRZESTARZAŁE) Monitorowanie klastra usługi Azure Container Service DC/OS za pomocą rozwiązania Dynatrace SaaS/zarządzane

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

W tym artykule pokazujemy, jak wdrożyć [Dynatrace](https://www.dynatrace.com/) OneAgent do monitorowania wszystkich węzłów agenta w klastrze usługi Azure Container Service. W przypadku tej konfiguracji jest potrzebne konto za pomocą rozwiązania Dynatrace SaaS/zarządzane. 

## <a name="dynatrace-saasmanaged"></a>Zarządzane SaaS Dynatrace
Dynatrace to rozwiązanie monitorowania natywnych dla chmury dla środowisk klastra i wysoce dynamicznych kontenera. Umożliwia ona lepiej optymalizacji wdrożeń kontenera i alokacji pamięci na podstawie danych użycia w czasie rzeczywistym. Jest w stanie automatycznie trafić problemy związane z aplikacji i infrastruktury, zapewniając automatycznego określania poziomu odniesienia, problem korelacji i wykrywanie głównej przyczyny problemu.

Na poniższej ilustracji przedstawiono Dynatrace interfejsu użytkownika:

![Dynatrace UI](./media/container-service-monitoring-dynatrace/dynatrace.png)

## <a name="prerequisites"></a>Wymagania wstępne 
[Wdrażanie](container-service-deployment.md) i [połączyć](./../container-service-connect.md) do klastra skonfigurowanego przez usługę Azure Container Service. Przegląd [interfejsu użytkownika platformy Marathon](container-service-mesos-marathon-ui.md). Przejdź do [ https://www.dynatrace.com/trial/ ](https://www.dynatrace.com/trial/) skonfigurować konto Dynatrace SaaS.  

## <a name="configure-a-dynatrace-deployment-with-marathon"></a>Konfigurowanie wdrożenia Dynatrace przy użyciu platformy Marathon
Te kroki pokazują, jak konfigurować i wdrażać aplikacje Dynatrace z klastrem przy użyciu platformy Marathon.

1. Dostęp do interfejsu użytkownika platformy DC/OS za pośrednictwem [ http://localhost:80/ ](http://localhost:80/). Jeden raz w Interfejsie użytkownika platformy DC/OS, przejdź do **wszechświat** kartę, a następnie wyszukaj **Dynatrace**.

    ![Dynatrace we wszechświecie DC/OS](./media/container-service-monitoring-dynatrace/dynatrace-universe.png)

2. Aby ukończyć konfigurację, należy na koncie Dynatrace SaaS lub konto bezpłatnej wersji próbnej. Po zalogowaniu się do pulpitu nawigacyjnego rozwiązania Dynatrace wybierz **wdrażanie Dynatrace**.

    ![Dynatrace Konfigurowanie integracja modelu PaaS](./media/container-service-monitoring-dynatrace/setup-paas.png)

3. Na stronie, wybierz **konfigurowania integracji PaaS**. 

    ![Token interfejsu API rozwiązania Dynatrace](./media/container-service-monitoring-dynatrace/api-token.png) 

4. Wprowadź token interfejsu API do konfiguracji Dynatrace OneAgent we wszechświecie DC/OS. 

    ![Konfiguracja Dynatrace OneAgent we wszechświecie DC/OS](./media/container-service-monitoring-dynatrace/dynatrace-config.png)

5. Ustaw wystąpienia na liczbę węzłów, które zamierzasz uruchomić. Ustawienie większej liczby również działa, ale DC/OS będzie próbować znaleźć nowe wystąpienia, aż do osiągnięcia tej liczby jest faktycznie. Jeśli wolisz, możesz to ustawić na wartość taką jak 1000000. W takim przypadku po każdym dodaniu nowego węzła do klastra Dynatrace automatycznie wdroży agenta tym nowym węźle po cenie stale dążymy do wdrażania wystąpień dalsze DC/OS.

    ![Konfiguracja rozwiązania Dynatrace w DC/OS wystąpieniach wszechświata](./media/container-service-monitoring-dynatrace/dynatrace-config2.png)

## <a name="next-steps"></a>Kolejne kroki

Po zainstalowaniu pakietu przejdź z powrotem do pulpitu nawigacyjnego rozwiązania Dynatrace. Można zbadać różne metryki użycia dla kontenerów w klastrze. 