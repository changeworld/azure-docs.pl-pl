---
title: (PRZESTARZAŁE) Monitorowanie klastra usługi Azure DC/OS — zarządzanie operacjami
description: Monitorowanie klastra usługi Azure Container Service DC/OS z usługą Log Analytics.
services: container-service
author: keikhara
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 11/17/2016
ms.author: keikhara
ms.custom: mvc
ms.openlocfilehash: 290141136672729060f5156d645c47ac303fa0c3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60809977"
---
# <a name="deprecated-monitor-an-azure-container-service-dcos-cluster-with-log-analytics"></a>(PRZESTARZAŁE) Monitorowanie klastra usługi Azure Container Service DC/OS z usługą Log Analytics

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Log Analytics jest firmy Microsoft oparte na chmurze rozwiązanie zarządzania IT, która ułatwia zarządzanie i chronić lokalne i infrastruktury chmury. Rozwiązanie kontenerów to rozwiązanie w usłudze Log Analytics, która ułatwia wyświetlanie dzienników kontenera magazynu, wydajności i w obrębie jednej lokalizacji. Możesz inspekcji, rozwiązywanie problemów z kontenerów, wyświetlając dzienniki w centralnej lokalizacji i Znajdź generujące dużo alertów, wykorzystywanie nadmiarowe kontenera na hoście.

![](media/container-service-monitoring-oms/image1.png)

Aby uzyskać więcej informacji na temat rozwiązania kontenerów, zobacz [kontenera rozwiązanie Log Analytics](../../azure-monitor/insights/containers.md).

## <a name="setting-up-log-analytics-from-the-dcos-universe"></a>Konfigurowanie usługi Log Analytics z uniwersum DC/OS


W tym artykule założono, że skonfigurowano DC/OS i wdrożono prostych kontenera aplikacji internetowych w klastrze.

### <a name="pre-requisite"></a>Wymagania wstępne
- [Subskrypcja usługi Microsoft Azure](https://azure.microsoft.com/free/) — możesz bezpłatnie uzyskać subskrypcję.  
- Ustawienia obszaru roboczego analizy dziennika — zobacz "Krok 3" poniżej
- [Interfejs wiersza polecenia DC/OS](https://docs.mesosphere.com/1.12/cli) zainstalowane.

1. Na pulpicie nawigacyjnym platformy DC/OS kliknij wszechświat i poszukaj pozycji "OMS", jak pokazano poniżej.

   >[!NOTE]
   >Pakiet OMS jest teraz nazywana usługi Log Analytics.

   ![](media/container-service-monitoring-oms/image2.png)

2. Kliknij pozycję **Zainstaluj**. Zobaczysz okno podręczne z informacjami o wersji i **zainstaluj pakiet** lub **zaawansowane instalacji** przycisku. Po kliknięciu **zaawansowane instalacji**, co prowadzi do **OMS określonej konfiguracji właściwości** strony.

   ![](media/container-service-monitoring-oms/image3.png)

   ![](media/container-service-monitoring-oms/image4.png)

3. W tym miejscu użytkownik jest proszony o podanie `wsid` (identyfikator obszaru roboczego usługi Log Analytics) i `wskey` (klucz podstawowy dla Identyfikatora obszaru roboczego). Aby uzyskać zarówno `wsid` i `wskey` , musisz utworzyć konto w <https://mms.microsoft.com>.
   Wykonaj kroki, aby utworzyć konto. Po zakończeniu tworzenia konta, musisz uzyskać swoje `wsid` i `wskey` , klikając **ustawienia**, następnie **połączone źródła**, a następnie **serwerówzsystememLinux**, jak pokazano poniżej.

   ![](media/container-service-monitoring-oms/image5.png)

4. Wybierz liczbę wystąpień, chcesz, a następnie kliknij przycisk "Sprawdź i zainstaluj". Zazwyczaj chcesz liczbą wystąpień równą liczbie maszyn wirtualnych w klastrze agenta. Log Analytics agent dla systemu Linux jest instalowany jako poszczególnych kontenerów na każdej maszynie Wirtualnej, który chce, aby zebrać informacje dotyczące monitorowania i rejestrowania informacji.

   [!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 

## <a name="setting-up-a-simple-log-analytics-dashboard"></a>Skonfigurowanie prostego pulpitu nawigacyjnego do usługi Log Analytics

Po zainstalowaniu agenta usługi Log Analytics dla systemu Linux na maszynach wirtualnych, następnym krokiem jest ustanowienie pulpit nawigacyjny usługi Log Analytics. Można skonfigurować pulpitu nawigacyjnego w witrynie Azure portal.

### <a name="azure-portal"></a>Azure Portal 

Zaloguj się do witryny Azure portal pod adresem <https://portal.microsoft.com/>. Przejdź do **Marketplace**, wybierz opcję **monitorowanie + zarządzanie** i kliknij przycisk **Zobacz wszystkie**. Następnie wpisz `containers` w polu wyszukiwania. Zostanie wyświetlony "kontenery" w wynikach wyszukiwania. Wybierz **kontenery** i kliknij przycisk **Utwórz**.

![](media/container-service-monitoring-oms/image9.png)

Po kliknięciu **Utwórz**, będzie monitować dla obszaru roboczego. Wybierz swój obszar roboczy lub jeśli nie masz, Utwórz nowy obszar roboczy.

![](media/container-service-monitoring-oms/image10.PNG)

Po wybraniu obszaru roboczego kliknij **Utwórz**.

![](media/container-service-monitoring-oms/image11.png)

Aby uzyskać więcej informacji na temat rozwiązanie kontenera Log Analytics można znaleźć [kontenera rozwiązanie Log Analytics](../../azure-monitor/insights/containers.md).

### <a name="how-to-scale-log-analytics-agent-with-acs-dcos"></a>Jak skalować agenta usługi Log Analytics przy użyciu platformy DC/OS usługi ACS 

W przypadku, gdy trzeba zainstalowano agenta usługi Log Analytics mniejszej niż liczba węzłów rzeczywiste, czy jest skalowanie w górę skalowania maszyn wirtualnych, dodając więcej maszyn wirtualnych, możesz to zrobić przy użyciu skalowania `msoms` usługi.

Możesz przejść do platformy Marathon lub na karcie usług interfejsu użytkownika platformy DC/OS i skalowanie w górę swoje liczby węzłów.

![](media/container-service-monitoring-oms/image12.PNG)

Spowoduje to wdrożenie do innych węzłów, które nie zostały jeszcze wdrożone agenta usługi Log Analytics.

## <a name="uninstall-ms-oms"></a>Odinstalowywanie pakietu OMS MS

Aby odinstalować MS OMS, wprowadź następujące polecenie:

```bash
$ dcos package uninstall msoms
```

## <a name="let-us-know"></a>Daj nam znać!
Jakie rozwiązania najlepiej? Czego brakuje? Co jeszcze potrzebujesz to być przydatne dla Ciebie? Daj nam znać o <a href="mailto:OMSContainers@microsoft.com">OMSContainers</a>.

## <a name="next-steps"></a>Kolejne kroki

 Teraz, po skonfigurowaniu usługi Log Analytics do monitorowania kontenerów,[wyświetlić pulpit nawigacyjny kontenera](../../azure-monitor/insights/containers.md).
