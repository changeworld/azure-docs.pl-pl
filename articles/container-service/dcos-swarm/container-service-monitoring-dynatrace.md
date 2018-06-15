---
title: Monitor klastra Azure DC/OS - Dynatrace
description: Monitorowanie klastra usługi kontenera platformy Azure DC/OS z Dynatrace. Wdrażanie Dynatrace OneAgent przy użyciu pulpitu nawigacyjnego DC/OS.
services: container-service
author: MartinGoodwell
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 12/13/2016
ms.author: rogardle
ms.custom: mvc
ms.openlocfilehash: 3d1bfc3bb61781d487c40831edd5da6fcb5a7df9
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
ms.locfileid: "32162045"
---
# <a name="monitor-an-azure-container-service-dcos-cluster-with-dynatrace-saasmanaged"></a>Monitor klastra usługi kontenera platformy Azure DC/OS z Dynatrace SaaS/zarządzane

W tym artykule zostanie przedstawiony zostanie sposób wdrażania [Dynatrace](https://www.dynatrace.com/) OneAgent do wszystkich węzłów w klastrze usługi kontenera platformy Azure agenta monitorowania. Musisz mieć konto z Dynatrace SaaS/zarządzanego dla tej konfiguracji. 

## <a name="dynatrace-saasmanaged"></a>Dynatrace SaaS/zarządzaną
Dynatrace to rozwiązanie monitorowania chmury natywne dla środowiska klastra i dynamicznej kontenera. Pozwala na lepsze zoptymalizować wdrożenia kontenerów, a przydziału pamięci za pomocą danych użycia w czasie rzeczywistym. Jest w stanie automatycznie trafić zapewniając automatycznego określania poziomu odniesienia, problem korelacji i wykrywania głównej przyczyny problemów dotyczących aplikacji i infrastruktury.

Na poniższej ilustracji przedstawiono Dynatrace interfejsu użytkownika:

![Dynatrace interfejsu użytkownika](./media/container-service-monitoring-dynatrace/dynatrace.png)

## <a name="prerequisites"></a>Wymagania wstępne 
[Wdrażanie](container-service-deployment.md) i [połączyć](./../container-service-connect.md) klastrowi konfigurowane za pomocą usługi kontenera platformy Azure. Przegląd [interfejsu użytkownika platformy Marathon](container-service-mesos-marathon-ui.md). Przejdź do [ https://www.dynatrace.com/trial/ ](https://www.dynatrace.com/trial/) skonfigurować konto Dynatrace SaaS.  

## <a name="configure-a-dynatrace-deployment-with-marathon"></a>Konfigurowanie wdrożenia Dynatrace przy użyciu platformy Marathon
Te kroki pokazują, jak skonfigurować i wdrożyć aplikacje Dynatrace do klastra przy użyciu platformy Marathon.

1. Dostęp za pośrednictwem interfejsu użytkownika platformy DC/OS [ http://localhost:80/ ](http://localhost:80/). Raz w Interfejsie użytkownika DC/OS, przejdź do **Universe** karcie, a następnie wyszukaj **Dynatrace**.

    ![Dynatrace w Uniwersum DC/OS](./media/container-service-monitoring-dynatrace/dynatrace-universe.png)

2. Aby zakończyć konfigurację, należy na koncie Dynatrace SaaS lub bezpłatne konto próbne. Po zalogowaniu się do pulpitu nawigacyjnego Dynatrace, wybierz **wdrażanie Dynatrace**.

    ![Konfigurowanie Dynatrace PaaS integracji](./media/container-service-monitoring-dynatrace/setup-paas.png)

3. Na stronie wybierz **konfigurowania integracji PaaS**. 

    ![Token Dynatrace interfejsu API](./media/container-service-monitoring-dynatrace/api-token.png) 

4. Wprowadź token interfejsu API w konfiguracji Dynatrace OneAgent w Uniwersum DC/OS. 

    ![Konfiguracja Dynatrace OneAgent na całym świecie DC/OS](./media/container-service-monitoring-dynatrace/dynatrace-config.png)

5. Zestaw wystąpień do liczby węzłów, które zamierzasz uruchomić. Ustawienie wyższej także działania, ale DC/OS będzie próbować znaleźć nowych wystąpień do momentu rzeczywistości osiągnięciu tej liczby. Jeśli wolisz, możesz także ustawić dla tej wartości, takich jak 1000000. W takim przypadku zawsze, gdy nowy węzeł zostanie dodany do klastra, Dynatrace automatycznie wdraża agenta tego nowego węzła w cenie DC/OS stale w trakcie wdrażania dalsze wystąpień.

    ![Konfiguracja Dynatrace w DC/OS Universe-wystąpienia](./media/container-service-monitoring-dynatrace/dynatrace-config2.png)

## <a name="next-steps"></a>Kolejne kroki

Po zainstalowaniu pakietu przejdź z powrotem do pulpitu nawigacyjnego Dynatrace. Metryki użycia różnych kontenerów można sprawdzić w ramach klastra. 