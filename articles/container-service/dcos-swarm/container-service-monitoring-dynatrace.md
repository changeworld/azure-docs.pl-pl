---
title: (PRZESTARZAŁE) Monitorowanie klastra kontrolera domeny/systemu operacyjnego platformy Azure — Dynatrace
description: Monitoruj klaster kontrolera domeny/systemu operacyjnego usługi kontenera platformy Azure za pomocą dynatrace. Wdrażanie programu Dynatrace OneAgent przy użyciu pulpitu nawigacyjnego kontrolera domeny/systemu operacyjnego.
author: MartinGoodwell
ms.service: container-service
ms.topic: conceptual
ms.date: 12/13/2016
ms.author: rogardle
ms.custom: mvc
ms.openlocfilehash: a82481c5cb3d12b11179b41999f73e67583ec43b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76277748"
---
# <a name="deprecated-monitor-an-azure-container-service-dcos-cluster-with-dynatrace-saasmanaged"></a>(PRZESTARZAŁE) Monitorowanie klastra kontrolera domeny/systemu operacyjnego usługi kontenerowej platformy Azure za pomocą usługi Dynatrace SaaS/Managed

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

W tym artykule pokażemy, jak wdrożyć [Dynatrace](https://www.dynatrace.com/) OneAgent do monitorowania wszystkich węzłów agenta w klastrze usługi Azure Container Service. Do tej konfiguracji potrzebne jest konto dynatrace SaaS/Managed. 

## <a name="dynatrace-saasmanaged"></a>Dynatrace SaaS/Zarządzane
Dynatrace to natywne rozwiązanie do monitorowania dla środowisk kontenerów i klastrów o wysokiej dynamice. Umożliwia lepszą optymalizację wdrożeń kontenerów i alokacji pamięci przy użyciu danych użycia w czasie rzeczywistym. Jest w stanie automatycznie wykryć problemy z aplikacją i infrastrukturą, zapewniając automatyczne baselining, korelacji problemów i wykrywania przyczyn źródłowych.

Na poniższej ilustracji przedstawiono interfejs użytkownika dynatrace:

![Interfejs użytkownika dynatrace](./media/container-service-monitoring-dynatrace/dynatrace.png)

## <a name="prerequisites"></a>Wymagania wstępne 
[Wdrażanie](container-service-deployment.md) klastra skonfigurowanego przez usługę Azure Container Service i [łączenie się z nią.](./../container-service-connect.md) Poznaj [interfejs użytkownika maratonu](container-service-mesos-marathon-ui.md). Przejdź [https://www.dynatrace.com/trial/](https://www.dynatrace.com/trial/) do konfigurowania konta Dynatrace SaaS.  

## <a name="configure-a-dynatrace-deployment-with-marathon"></a>Konfigurowanie wdrożenia Dynatrace za pomocą programu Marathon
Te kroki pokazują, jak skonfigurować i wdrożyć aplikacje Dynatrace w klastrze z marathon.

1. Uzyskaj dostęp do interfejsu użytkownika [http://localhost:80/](http://localhost:80/)dc/os za pośrednictwem programu . Po wejściu do interfejsu użytkownika DC/OS przejdź do karty **Wszechświat,** a następnie wyszukaj **pozycję Dynatrace**.

    ![Dynatrace w uniwersum DC/OS](./media/container-service-monitoring-dynatrace/dynatrace-universe.png)

2. Aby ukończyć konfigurację, potrzebujesz konta Dynatrace SaaS lub bezpłatnego konta próbnego. Po zalogowaniu się do pulpitu nawigacyjnego Dynatrace wybierz pozycję **Wdrażanie dynatrace**.

    ![Dynatrace Konfigurowanie integracji PaaS](./media/container-service-monitoring-dynatrace/setup-paas.png)

3. Na stronie wybierz pozycję **Konfigurowanie integracji PaaS**. 

    ![Token interfejsu API Dynatrace](./media/container-service-monitoring-dynatrace/api-token.png) 

4. Wprowadź token interfejsu API do konfiguracji Dynatrace OneAgent w obrębie wszechświata DC/OS. 

    ![Konfiguracja Dynatrace OneAgent w uniwersum DC/OS](./media/container-service-monitoring-dynatrace/dynatrace-config.png)

5. Ustaw wystąpienia na liczbę węzłów, które mają zostać uruchomione. Ustawienie większej liczby również działa, ale DC/OS będzie nadal próbować znaleźć nowe wystąpienia, dopóki ta liczba nie zostanie faktycznie osiągnięta. Jeśli wolisz, możesz również ustawić tę wartość na wartość 1000000. W takim przypadku za każdym razem, gdy nowy węzeł jest dodawany do klastra, Dynatrace automatycznie wdraża agenta do tego nowego węzła, za cenę dc/os stale próbuje wdrożyć dalsze wystąpienia.

    ![Konfiguracja dynatrace w wystąpieniach wszechświata DC/OS](./media/container-service-monitoring-dynatrace/dynatrace-config2.png)

## <a name="next-steps"></a>Następne kroki

Po zainstalowaniu pakietu przejdź z powrotem do pulpitu nawigacyjnego Dynatrace. Można eksplorować różne metryki użycia dla kontenerów w klastrze. 
