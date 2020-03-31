---
title: (PRZESTARZAŁE) Monitorowanie klastra kontrolera domeny/systemu operacyjnego platformy Azure — zarządzanie operacjami
description: Monitoruj klaster kontrolera domeny/systemu operacyjnego usługi kontenera platformy Azure za pomocą usługi Log Analytics.
author: keikhara
ms.service: container-service
ms.topic: conceptual
ms.date: 11/17/2016
ms.author: keikhara
ms.custom: mvc
ms.openlocfilehash: 1ab8d1cf3eb38a17f0b3d6c8137e37237498a527
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76277317"
---
# <a name="deprecated-monitor-an-azure-container-service-dcos-cluster-with-log-analytics"></a>(PRZESTARZAŁE) Monitorowanie klastra kontrolera domeny/systemu operacyjnego usługi kontenerów platformy Azure za pomocą analizy dzienników

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Usługa Log Analytics to oparte na chmurze rozwiązanie firmy Microsoft do zarządzania it, które pomaga zarządzać infrastrukturą lokalną i chmurową oraz chronić ją.Rozwiązanie kontenera to rozwiązanie w usłudze Log Analytics, które ułatwia wyświetlanie zapasów kontenerów, wydajności i dzienników w jednej lokalizacji. Można przeprowadzić inspekcję, rozwiązywać problemy z kontenerami, wyświetlając dzienniki w scentralizowanej lokalizacji i znaleźć hałaśliwe zużycie nadmiaru kontenera na hoście.

![](media/container-service-monitoring-oms/image1.png)

Aby uzyskać więcej informacji na temat rozwiązania kontenera, zobacz [analiza dziennika rozwiązań kontenerowych](../../azure-monitor/insights/containers.md).

## <a name="setting-up-log-analytics-from-the-dcos-universe"></a>Konfigurowanie usługi Log Analytics z uniwersum DC/OS


W tym artykule przyjęto założenie, że skonfigurowano kontroler domeny/systemu operacyjnego i wdrożono proste aplikacje kontenerów sieci web w klastrze.

### <a name="pre-requisite"></a>Wymagania wstępne
- [Subskrypcja platformy Microsoft Azure](https://azure.microsoft.com/free/) — możesz uzyskać subskrypcję za darmo.  
- Konfiguracja obszaru roboczego analizy dzienników — patrz "Krok 3" poniżej
- [Dc/OS CLI](https://docs.mesosphere.com/1.12/cli) zainstalowany.

1. Na pulpicie nawigacyjnym DC/OS kliknij universe i wyszukaj "OMS", jak pokazano poniżej.

   >[!NOTE]
   >Usługa OMS jest teraz określana jako usługa Log Analytics.

   ![](media/container-service-monitoring-oms/image2.png)

2. Kliknij **pozycję Zainstaluj**. Zostanie wyświetlene okno podręczne z informacjami o wersji i przyciskiem **Zainstaluj pakiet** lub **Instalacja zaawansowana.** Po kliknięciu przycisku **Instalacja zaawansowana**, która prowadzi do strony **właściwości konfiguracji specyficzne dla systemu OMS.**

   ![](media/container-service-monitoring-oms/image3.png)

   ![](media/container-service-monitoring-oms/image4.png)

3. W tym miejscu zostaniesz `wsid` poproszony o wprowadzenie (identyfikator obszaru `wskey` roboczego usługi Log Analytics) i (klucz podstawowy identyfikatora obszaru roboczego). Aby uzyskać `wsid` `wskey` zarówno i trzeba utworzyć <https://mms.microsoft.com>konto w .
   Wykonaj kroki, aby utworzyć konto. Po zakończeniu tworzenia konta, trzeba `wsid` uzyskać i `wskey` klikając **Ustawienia**, następnie Połączone **źródła**, a następnie **serwery Linux**, jak pokazano poniżej.

   ![](media/container-service-monitoring-oms/image5.png)

4. Wybierz odpowiednią liczbę wystąpień i kliknij przycisk "Przejrzyj i zainstaluj". Zazwyczaj należy mieć liczbę wystąpień równą liczbie maszyn wirtualnych w klastrze agenta. Agent analizy dzienników dla systemu Linux instaluje jako pojedyncze kontenery na każdej maszynie wirtualnej, która chce zbierać informacje do monitorowania i rejestrowania informacji.

   [!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 

## <a name="setting-up-a-simple-log-analytics-dashboard"></a>Konfigurowanie prostego pulpitu nawigacyjnego usługi Log Analytics

Po zainstalowaniu agenta usługi Log Analytics dla systemu Linux na maszynach wirtualnych następnym krokiem jest skonfigurowanie pulpitu nawigacyjnego usługi Log Analytics. Pulpit nawigacyjny można skonfigurować za pośrednictwem witryny Azure Portal.

### <a name="azure-portal"></a>Portal Azure 

Zaloguj się do <https://portal.microsoft.com/>witryny Azure portal w . Przejdź do **marketplace**, wybierz **Monitorowanie + zarządzanie** i kliknij pozycję Zobacz **wszystko**. Następnie `containers` wpisz w wyszukiwaniu. Zobaczysz "kontenery" w wynikach wyszukiwania. Wybierz **pozycję Kontenery** i kliknij pozycję **Utwórz**.

![](media/container-service-monitoring-oms/image9.png)

Po **kliknięciu przycisku Utwórz**poprosi o obszar roboczy. Wybierz obszar roboczy lub jeśli go nie masz, utwórz nowy obszar roboczy.

![](media/container-service-monitoring-oms/image10.PNG)

Po wybraniu obszaru roboczego kliknij przycisk **Utwórz**.

![](media/container-service-monitoring-oms/image11.png)

Aby uzyskać więcej informacji na temat rozwiązania kontenera analizy dzienników, zapoznaj się z [analizą dziennika rozwiązania kontenera.](../../azure-monitor/insights/containers.md)

### <a name="how-to-scale-log-analytics-agent-with-acs-dcos"></a>Jak skalować agenta usługi Log Analytics za pomocą usługi ACS DC/OS 

W przypadku, gdy trzeba mieć zainstalowany agent usługi Log Analytics krótki rzeczywistej liczby węzłów lub są skalowanie w `msoms` górę skalowania maszyny wirtualnej zestaw przez dodanie większej liczby maszyn wirtualnych, można to zrobić przez skalowanie usługi.

Możesz przejść do karty Marathon lub DC/OS UI i zwiększyć liczbę węzłów.

![](media/container-service-monitoring-oms/image12.PNG)

Spowoduje to wdrożenie w innych węzłach, które nie zostały jeszcze wdrożone agenta usługi Log Analytics.

## <a name="uninstall-ms-oms"></a>Odinstalowywanie usługi MS OMS

Aby odinstalować ms oms, wprowadź następujące polecenie:

```bash
$ dcos package uninstall msoms
```

## <a name="let-us-know"></a>Daj nam znać!!!
Co działa? Czego brakuje? Czego jeszcze potrzebujesz, aby to było dla Ciebie przydatne? Daj nam znać w <a href="mailto:OMSContainers@microsoft.com">OMSContainers</a>.

## <a name="next-steps"></a>Następne kroki

 Teraz, gdy skonfigurowałeś usługi Log Analytics do monitorowania kontenerów,[zobacz pulpit nawigacyjny kontenera](../../azure-monitor/insights/containers.md).
