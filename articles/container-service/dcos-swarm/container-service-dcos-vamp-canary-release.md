---
title: (PRZESTARZAŁE) Wydania canary za pomocą systemu Vamp w klastrze Azure DC/OS
description: Jak używać pomocą systemu Vamp do wydania canary usług i zastosować ruchu inteligentnego filtrowania w klastrze usługi Azure Container Service DC/OS
services: container-service
author: gggina
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 04/17/2017
ms.author: rasquill
ms.custom: mvc
ms.openlocfilehash: f1b3c08cce2cb33feab899ea082fc6fb40225182
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61458191"
---
# <a name="deprecated-canary-release-microservices-with-vamp-on-an-azure-container-service-dcos-cluster"></a>(PRZESTARZAŁE) Mikrousługi wydania canary za pomocą systemu Vamp w klastrze usługi Azure Container Service DC/OS

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

W tym przewodniku skonfigurujemy pomocą systemu Vamp w usłudze Azure Container Service z klastrem DC/OS. Firma Microsoft canary wersji usługi pokaz pomocą systemu Vamp "sava", a następnie rozwiąż problem niezgodności usługi w przeglądarce Firefox, stosując filtrowanie ruchu inteligentne. 

> [!TIP] 
> W tym przewodniku pomocą systemu Vamp działa w klastrze DC/OS, ale umożliwia także pomocą systemu Vamp przy użyciu rozwiązania Kubernetes jako orkiestrator.
>

## <a name="about-canary-releases-and-vamp"></a>Temat kanarkowe wydania i pomocą systemu Vamp


[Zwalnianie canary](https://martinfowler.com/bliki/CanaryRelease.html) jest przyjęte przez organizacje innowacyjne, takich jak Netflix, Facebook i Spotify strategią smart wdrożenia. To podejście, które ma sens, ponieważ zmniejsza problemy, wprowadza bezpieczeństwa sieci i zwiększa innowacji. Więc Dlaczego nie wszyscy producenci używana? Rozszerzanie potoku CI/CD, aby uwzględnić canary strategie zwiększa złożoność i wymaga rozbudowane devops wiedzy i doświadczenia. Jest wystarczająco dużo, aby zablokować mniejszych firmami i przedsiębiorstwami podobne przed ich nawet uruchomieniu. 

[Vamp](https://vamp.io/) systemem typu open-source przeznaczony do jej obsługi ułatwiają realizację tego przejścia i przełączyć canary udostępnia funkcje do harmonogramu preferowanych kontenera. Funkcje canary pomocą systemu vamp firmy wykracza poza wdrożeniach opartych na procentach. Ruch można filtrować i podzielony na szereg warunków, na przykład do określonych użytkowników docelowych, zakresy adresów IP lub urządzeń. Pomocą systemu vamp śledzi i analizuje metryki wydajności, dzięki czemu w przypadku usługi automation, na podstawie rzeczywistych danych. Konfigurowanie automatycznego wycofania w razie wystąpienia błędów lub skalować wariantów poszczególnych usług, w zależności od obciążenia lub opóźnienia.

## <a name="set-up-azure-container-service-with-dcos"></a>Konfigurowanie usługi Azure Container Service za pomocą platformy DC/OS



1. [Wdrażanie klastra DC/OS](container-service-deployment.md) z jednym węzłem głównym i dwoma agentami domyślny rozmiar. 

2. [Tworzenie tunelu SSH](../container-service-connect.md) nawiązać połączenia z klastrem DC/OS. W tym artykule założono tunel do klastra w lokalnym porcie 80.


## <a name="set-up-vamp"></a>Konfigurowanie pomocą systemu Vamp

Teraz, gdy masz działającego klastra DC/OS, pomocą systemu Vamp można zainstalować z poziomu interfejsu użytkownika platformy DC/OS (http:\//localhost:80). 

![Interfejs użytkownika platformy DC/OS](./media/container-service-dcos-vamp-canary-release/01_set_up_vamp.png)

Instalacja jest przeprowadzana w dwóch etapach:

1. **Wdrażanie programu Elasticsearch**.

2. Następnie **wdrażanie pomocą systemu Vamp** , instalując pakiet wszechświata Vamp DC/OS.

### <a name="deploy-elasticsearch"></a>Wdrażanie programu Elasticsearch

Pomocą systemu vamp wymaga Elasticsearch zbieranie metryk i agregacji. Możesz użyć [obrazów platformy Docker magneticio](https://hub.docker.com/r/magneticio/elastic/) celu wdrożenia zgodnych stosu Vamp Elasticsearch.

1. W Interfejsie użytkownika platformy DC/OS, przejdź do **usług** i kliknij przycisk **Wdróż usługę**.

2. Wybierz **tryb JSON** z **Wdrażanie nowej usługi** wyskakujących.

   ![Wybierz tryb JSON](./media/container-service-dcos-vamp-canary-release/02_deploy_service_json_mode.png)

3. Wklej następujący kod JSON. Ta konfiguracja działa kontener z 1 GB pamięci RAM i sprawdzanie kondycji podstawowych na porcie programu Elasticsearch.
  
   ```JSON
   {
    "id": "elasticsearch",
    "instances": 1,
    "cpus": 0.2,
    "mem": 1024.0,
    "container": {
      "docker": {
        "image": "magneticio/elastic:2.2",
        "network": "HOST",
        "forcePullImage": true
      }
    },
    "healthChecks": [
      {
        "protocol": "TCP",
        "gracePeriodSeconds": 30,
        "intervalSeconds": 10,
        "timeoutSeconds": 5,
        "port": 9200,
        "maxConsecutiveFailures": 0
      }
    ]
   }
   ```
  

3. Kliknij przycisk **Deploy (Wdróż)**.

   DC/OS służy do wdrażania kontenerów programu Elasticsearch. Postęp można śledzić na **usług** strony.  

   ![Wdrażanie e? Usługa Elasticsearch](./media/container-service-dcos-vamp-canary-release/03_deply_elasticsearch.png)

### <a name="deploy-vamp"></a>Wdrażanie pomocą systemu Vamp

Po Elasticsearch zgłasza jako **systemem**, można dodać pakiet Vamp Uniwersum DC/OS. 

1. Przejdź do **wszechświat** i wyszukaj **vamp**. 
   ![Vamp w uniwersum DC/OS](./media/container-service-dcos-vamp-canary-release/04_universe_deploy_vamp.png)

2. Kliknij przycisk **zainstalować** obok pomocą systemu vamp pakietu, a następnie wybierz **zaawansowane instalacji**.

3. Przewiń w dół, a następnie wprowadź następujące url elasticsearch: `http://elasticsearch.marathon.mesos:9200`. 

   ![Wprowadź adres URL usługi Elasticsearch](./media/container-service-dcos-vamp-canary-release/05_universe_elasticsearch_url.png)

4. Kliknij przycisk **Przejrzyj i zainstaluj**, następnie kliknij przycisk **zainstalować** rozpocząć wdrażanie.  

   DC/OS służy do wdrażania wszystkich wymaganych składników pomocą systemu Vamp. Postęp można śledzić na **usług** strony.
  
   ![Wdrażanie pomocą systemu Vamp jako pakiet wszechświat](./media/container-service-dcos-vamp-canary-release/06_deploy_vamp.png)
  
5. Po zakończeniu wdrożenia można uzyskać dostęp Vamp interfejsu użytkownika:

   ![Usługa pomocą systemu vamp na DC/OS](./media/container-service-dcos-vamp-canary-release/07_deploy_vamp_complete.png)
  
   ![Vamp interfejsu użytkownika](./media/container-service-dcos-vamp-canary-release/08_vamp_ui.png)


## <a name="deploy-your-first-service"></a>Wdrażanie pierwszej usługi

Po tym pomocą systemu Vamp jest uruchomiona, należy wdrożyć usługę z planu. 

W najprostszej postaci [planu pomocą systemu Vamp](https://vamp.io/documentation/using-vamp/blueprints/) opisuje punktów końcowych (bramy), klastrów i usług do wdrożenia. Pomocą systemu vamp używa klastrów do grupowania różne odmiany tej samej usługi w logiczne grupy canary przy zwalnianiu lub A / B, testowanie.  

W tym scenariuszu przykładowej aplikacji monolitycznej, o nazwie [ **sava**](https://github.com/magneticio/sava), który jest w wersji 1.0. Monolityczna jest spakowane w kontenerze platformy Docker, który znajduje się w usłudze Docker Hub w ramach magneticio/sava:1.0.0. Aplikacja jest uruchamiana normalnie na porcie 8080, ale chcesz udostępnić ją w obszarze port 9050 w tym przypadku. Wdrażanie aplikacji za pośrednictwem pomocą systemu Vamp przy użyciu prostego planu.

1. Przejdź do **wdrożeń**.

2. Kliknij pozycję **Add** (Dodaj).

3. Wklej następujący plan YAML. Ten plan zawiera jeden klaster z wariantnymi tylko jednej usługi, które możemy zmienić w późniejszym kroku:

   ```YAML
   name: sava                        # deployment name
   gateways:
    9050: sava_cluster/webport      # stable endpoint
   clusters:
    sava_cluster:               # cluster to create
     services:
        -
          breed:
            name: sava:1.0.0        # service variant name
            deployable: magneticio/sava:1.0.0
            ports:
              webport: 8080/http # cluster endpoint, used for canary releasing
   ```

4. Kliknij pozycję **Zapisz**. Pomocą systemu vamp inicjuje wdrożenia.

Wdrożenie, znajduje się na **wdrożeń** strony. Kliknij wdrożenie, aby monitorować jej stan.

![Interfejs użytkownika — wdrażanie sava vamp](./media/container-service-dcos-vamp-canary-release/09_sava100.png)

![Usługa sava Vamp interfejsu użytkownika](./media/container-service-dcos-vamp-canary-release/09a_sava100.png)

Bramy są tworzone, które są wymienione na **bram** strony:

* stabilna punktu końcowego uzyskać dostęp do uruchomionej usługi (port 9050) 
* zarządzane pomocą systemu Vamp wewnętrzny bramy (więcej informacji na temat tej bramy w dalszej części). 

![Vamp interfejsu użytkownika — sava bram](./media/container-service-dcos-vamp-canary-release/10_vamp_sava_gateways.png)

Usługa sava teraz została wdrożona, ale nie masz dostępu do jej zewnętrznie, ponieważ do przesyłania ruchu do go jeszcze nie wie, usługi Azure Load Balancer. Aby uzyskać dostęp do usługi, należy zaktualizować konfiguracji sieci platformy Azure.


## <a name="update-the-azure-network-configuration"></a>Zaktualizuj konfigurację sieci platformy Azure

Pomocą systemu vamp wdrożono usługę sava węzłach agenta DC/OS, udostępnianie stabilne punktu końcowego w porcie 9050. Dostęp do usługi z spoza klastra DC/OS, należy wprowadzić następujące zmiany w konfiguracji sieci platformy Azure we wdrożeniu klastra: 

1. **Konfigurowanie modułu równoważenia obciążenia Azure** dla agentów (zasób o nazwie **dcos-agent-lb-xxxx**) za pomocą sondy kondycji i zasadę, aby przekazywać dalej ruch na porcie 9050 do wystąpień sava. 

2. **Zaktualizuj sieciową grupę zabezpieczeń** dla agentów publicznych (zasób o nazwie **XXXX-agent-public-sieciowej grupy zabezpieczeń XXXX**) aby zezwolić na ruch na porcie 9050.

Aby uzyskać szczegółowe instrukcje dotyczące wykonania tych zadań za pomocą witryny Azure portal, zobacz [Włączanie publicznego dostępu do aplikacji usługi Azure Container Service](container-service-enable-public-access.md). Określ port 9050 dla wszystkich ustawień portu.


Wszystko, od momentu utworzenia, przejdź do **Przegląd** bloku modułu równoważenia obciążenia agenta DC/OS (zasób o nazwie **dcos-agent-lb-xxxx**). Znajdź **publiczny adres IP**i umożliwia dostęp sava portu 9050 adres.

![Witryna Azure portal — Pobierz publiczny adres IP](./media/container-service-dcos-vamp-canary-release/18_public_ip_address.png)

![sava](./media/container-service-dcos-vamp-canary-release/19_sava100.png)


## <a name="run-a-canary-release"></a>Wydania canary uruchamiania

Załóżmy, że masz nową wersję tej aplikacji, które mają do wydania canary w środowisku produkcyjnym. Jego kontenerowych nimi jako magneticio/sava:1.1.0 i są gotowe do współpracy. Pomocą systemu vamp umożliwia łatwe dodawanie nowych usług na bieżącym wdrożeniu. Te usługi "scalonych" są wdrażana wraz z istniejących usług w klastrze, a przypisany wagi 0%. Żaden ruch jest kierowany do nowo scalonego, aż dopasowywanie Dystrybucja ruchu. Suwak wagi w Interfejsie użytkownika Vamp zapewnia pełną kontrolę nad dystrybucji, umożliwiające przyrostowe zmiany (wydania canary) lub błyskawiczne wycofywania.

### <a name="merge-a-new-service-variant"></a>Scal nowy wariant usługi

Aby scalić Nowa usługa sava 1.1 z bieżącym wdrożeniu:

1. W Interfejsie Vamp kliknij **plany**.

2. Kliknij przycisk **Dodaj** i wklej następujący plan YAML: Ten plan w tym artykule opisano nowy wariant usługi (sava: 1.1.0) do wdrożenia w istniejącym klastrze (sava_cluster).

   ```YAML
   name: sava:1.1.0      # blueprint name
   clusters:
    sava_cluster:       # cluster to update
      services:
        -
          breed:
            name: sava:1.1.0    # service variant name
            deployable: magneticio/sava:1.1.0    
            ports:
              webport: 8080/http # cluster endpoint to update
   ```
  
3. Kliknij pozycję **Zapisz**. Planu są przechowywane i wyświetlane na **plany** strony.

4. Otwórz menu akcji dla planu sava: 1.1, a następnie kliknij przycisk **scalanie**.

   ![Vamp interfejsu użytkownika — plany](./media/container-service-dcos-vamp-canary-release/20_sava110_mergeto.png)

5. Wybierz **sava** wdrożenia i kliknij przycisk **scalania**.

   ![Vamp interfejsu użytkownika — planu scalania do wdrożenia](./media/container-service-dcos-vamp-canary-release/21_sava110_merge.png)

Pomocą systemu vamp wdraża nowy wariant usługi sava: 1.1.0 opisanego w planie obok sava: 1.0.0 w **sava_cluster** z bieżącym wdrożeniu. 

![Interfejs użytkownika — zaktualizowano wdrożenie sava vamp](./media/container-service-dcos-vamp-canary-release/22_sava_cluster.png)

**Sava/sava_cluster/webport** bramy (punkt końcowy klastra) jest także aktualizowana Dodawanie trasy do nowo wdrożonym sava: 1.1.0. W tym momencie nie ruch odbywa się w tym miejscu ( **wagi** jest ustawiona na 0%).

![Vamp interfejsu użytkownika — klastra bramy](./media/container-service-dcos-vamp-canary-release/23_sava_cluster_webport.png)

### <a name="canary-release"></a>Wydania canary

W obu wersjach sava wdrożonych w tym samym klastrze dopasować rozłożenie ruchu między nimi, przenosząc **wagi** suwaka.

1. Kliknij przycisk ![Vamp interfejsu użytkownika — edytowanie](./media/container-service-dcos-vamp-canary-release/vamp_ui_edit.png) obok **wagi**.

2. Ustaw dystrybucja wag do 50% 50% i kliknij przycisk **Zapisz**.

   ![Vamp interfejsu użytkownika — suwak wagi bramy](./media/container-service-dcos-vamp-canary-release/24_sava_cluster_webport_weight.png)

3. Wróć do przeglądarki, a następnie odśwież stronę sava kilka razy. Teraz aplikacja sava stan zmieni się między stronę sava: 1.0 i stronę sava: 1.1.

   ![Przemienne sava1.0 i sava1.1 usług](./media/container-service-dcos-vamp-canary-release/25_sava_100_101.png)


  > [!NOTE]
  > Ta alternatywa strony działa najlepiej z "Incognito" lub "Anonimowy" Tryb przeglądarki z powodu buforowania statyczne elementy zawartości.
  >

### <a name="filter-traffic"></a>Filtrowanie ruchu

Załóżmy, że po wdrożeniu napotykasz niezgodności w sava: 1.1.0, że powoduje problemy z wyświetlaniem w przeglądarki Firefox. Możesz ustawić pomocą systemu Vamp do filtrowania ruchu przychodzącego i bezpośrednie, wszyscy użytkownicy programu Firefox z powrotem do znanego stabilne sava: 1.0.0. Ten filtr natychmiast jest rozpoznawany jako zakłóceń dla użytkowników programu Firefox, podczas gdy wszyscy else w dalszym ciągu korzystać z zalet ulepszonych sava: 1.1.0.

Zastosowań vamp **warunki** do filtrowania ruchu między trasy bramy. Ruch jest najpierw filtrowane i kierowane zgodnie z warunkami stosowane do każdej trasy. Cały pozostały ruch jest dystrybuowane zgodnie z ustawieniem bramy wagi.

Można utworzyć warunek do filtrowania wszyscy użytkownicy programu Firefox i kierować je do starego sava: 1.0.0:

1. Na sava/sava_cluster/webport **bram** kliknij ![Vamp interfejsu użytkownika — edytowanie](./media/container-service-dcos-vamp-canary-release/vamp_ui_edit.png) dodać **WARUNEK** do sava/sava_cluster/sava:1.0.0/webport trasy. 

2. Wprowadź warunek **agenta użytkownika przeglądarki Firefox ==** i kliknij przycisk ![Vamp interfejsu użytkownika — zapisywanie](./media/container-service-dcos-vamp-canary-release/vamp_ui_save.png).

   Pomocą systemu vamp dodaje warunku siłę domyślnej 0%. Aby rozpocząć filtrowanie ruchu, wymagane jest dostosowanie siły warunku.

3. Kliknij przycisk ![Vamp interfejsu użytkownika — edytowanie](./media/container-service-dcos-vamp-canary-release/vamp_ui_edit.png) zmienić **siły** zastosowana do warunku.
 
4. Ustaw **siły** do 100% i kliknij przycisk ![Vamp interfejsu użytkownika — zapisywanie](./media/container-service-dcos-vamp-canary-release/vamp_ui_save.png) do zapisania.

   Pomocą systemu vamp teraz wysyła cały ruch dopasowania warunek (wszyscy użytkownicy programu Firefox) sava: 1.0.0.

   ![Vamp interfejsu użytkownika — Zastosuj warunek do bramy](./media/container-service-dcos-vamp-canary-release/26_apply_condition.png)

5. Możesz także dopasować wagi bramy do wysyłania całego ruchu pozostałe (wszyscy użytkownicy — do Firefox) do nowego sava: 1.1.0. Kliknij przycisk ![Vamp interfejsu użytkownika — edytowanie](./media/container-service-dcos-vamp-canary-release/vamp_ui_edit.png) obok **wagi** i ustaw dystrybucja wag, dzięki czemu 100% zostaje skierowany do sava/sava_cluster/sava:1.1.0/webport trasy.

   Cały ruch, nie są filtrowane według warunku teraz jest kierowany do nowego sava: 1.1.0.

6. Aby wyświetlić filtru akcji, otwórz dwóch różnych przeglądarek (jeden Firefox i innych przeglądarek), a dostęp do usługi sava z obu. Wszystkie żądania Firefox są wysyłane do sava: 1.0.0, podczas gdy wszystkich innych przeglądarkach są kierowane do sava: 1.1.0.

   ![Vamp interfejsu użytkownika — filtrowania ruchu](./media/container-service-dcos-vamp-canary-release/27_filter_traffic.png)

## <a name="summing-up"></a>Podsumowanie

W tym artykule było szybkie wprowadzenie do pomocą systemu Vamp w klastrze DC/OS. Po pierwsze stało się pomocą systemu Vamp i uruchomiona na usługi Azure Container DC/OS usługi klastra, wdrożone usługi za pomocą systemu Vamp planu i dostęp do niego w punkcie końcowym narażonych (bramy).

Możemy również wspomnieliśmy niektóre zaawansowane funkcje pomocą systemu Vamp: scalanie nowy wariant usługi do uruchomionego wdrożenia i stopniowo wprowadzenie do, a następnie filtrowanie ruchu, aby rozwiązać znane niezgodności.


## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o zarządzaniu pomocą systemu Vamp akcji za pomocą [Vamp interfejsu API REST](https://vamp.io/documentation/api/api-reference/).

* Tworzenie skryptów automatyzacji pomocą systemu Vamp w środowisku Node.js i uruchamiaj je jako [Vamp przepływy pracy](https://vamp.io/documentation/using-vamp/v1.0.0/workflows/#create-a-workflow).

* Zobacz dodatkowe [samouczki pomocą systemu VAMP](https://vamp.io/documentation/tutorials/).

