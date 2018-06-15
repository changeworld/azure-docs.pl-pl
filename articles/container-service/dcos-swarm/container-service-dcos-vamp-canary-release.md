---
title: Mozgi zlecenia przy użyciu Vamp w klastrze Azure DC/OS
description: Jak używać Vamp mozgi wersji usług i zastosować filtrowanie w klastrze usługi kontenera platformy Azure DC/OS inteligentne ruchu
services: container-service
author: gggina
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 04/17/2017
ms.author: rasquill
ms.custom: mvc
ms.openlocfilehash: 339864f6261d031a21b138f880654bcd6ef51855
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
ms.locfileid: "32177805"
---
# <a name="canary-release-microservices-with-vamp-on-an-azure-container-service-dcos-cluster"></a>Mikrousług mozgi wersji z Vamp w klastrze usługi kontenera platformy Azure DC/OS

W tym przewodniku skonfigurujemy Vamp na usługi kontenera platformy Azure z klastrem DC/OS. Firma Microsoft mozgi wersji usługi demonstracyjnej Vamp "sava", a następnie Rozwiąż niezgodności usługę za pomocą przeglądarki Firefox, stosując filtrowanie ruchu inteligentne. 

> [!TIP] 
> W tym przewodniku Vamp działa w klastrze DC/OS, ale umożliwia także Vamp z Kubernetes jako orchestrator.
>

## <a name="about-canary-releases-and-vamp"></a>Temat element canary zwalnia i Vamp


[Zwalnianie mozgi](https://martinfowler.com/bliki/CanaryRelease.html) jest stosowane przez innowacyjnych organizacjami, takimi jak Netflix, Facebook i serwis Spotify strategii wdrażania inteligentne. Jest to sens, ponieważ zmniejsza problemy, wprowadza bezpieczeństwa sieci i zwiększa innowacji podejście. Dlaczego nie wszystkie firmy przy użyciu go? Rozszerzanie potoku CI/CD uwzględnienie mozgi strategii dodaje złożoność i wymaga devops dużej wiedzy i doświadczenia. To jest wystarczająca do blokowania mniejszych firmami i przedsiębiorstwami podobne, zanim one nawet wprowadzenie. 

[Vamp](http://vamp.io/) system typu open source zaprojektowane, aby ułatwić ten proces przejścia i Przenieś element canary udostępnia funkcje do harmonogramu z preferowanych kontenera. Funkcje mozgi vamp firmy wykracza poza wdrożeniach opartych na procentach. Można filtrować ruch i podziału na szeroki zakres warunków, na przykład dla konkretnych użytkowników docelowych, zakresy adresów IP lub urządzeń. Vamp śledzi i analizuje metryki wydajności, co pozwala na automatyzację na podstawie danych rzeczywistych. Konfigurowanie automatycznego wycofywania na błędy lub skalować wariantów pojedynczej usługi na podstawie obciążenia lub opóźnienia.

## <a name="set-up-azure-container-service-with-dcos"></a>Konfigurowanie usługi kontenera platformy Azure z DC/OS



1. [Wdrażanie klastra DC/OS](container-service-deployment.md) jeden z nich i dwaj agenci domyślny rozmiar. 

2. [Tworzenie tunelu SSH](../container-service-connect.md) nawiązać połączenia z klastrem DC/OS. W tym artykule przyjęto założenie, tunel do klastra lokalnego portu 80.


## <a name="set-up-vamp"></a>Konfigurowanie Vamp

Teraz, gdy masz uruchomiony klastra DC/OS Vamp można zainstalować z poziomu interfejsu użytkownika DC/OS (http://localhost:80). 

![Interfejs użytkownika platformy DC/OS](./media/container-service-dcos-vamp-canary-release/01_set_up_vamp.png)

Instalacja jest przeprowadzana w dwóch etapach:

1. **Wdrażanie Elasticsearch**.

2. Następnie **wdrażanie Vamp** instalując pakiet universe Vamp DC/OS.

### <a name="deploy-elasticsearch"></a>Wdrażanie Elasticsearch

Vamp wymaga Elasticsearch zbierania metryk i agregacji. Można użyć [obrazy usługi Docker magneticio](https://hub.docker.com/r/magneticio/elastic/) wdrażania zgodne stosu Vamp Elasticsearch.

1. W Interfejsie użytkownika DC/OS, przejdź do **usług** i kliknij przycisk **wdrażanie usługi**.

2. Wybierz **tryb JSON** z **wdrożenia nowej usługi** wyskakujących.

  ![Wybierz tryb JSON](./media/container-service-dcos-vamp-canary-release/02_deploy_service_json_mode.png)

3. Wklej następujący kod JSON. Ta konfiguracja jest uruchamiana kontenera z 1 GB pamięci RAM i sprawdzenie kondycji podstawowe na porcie Elasticsearch.
  
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
  

3. Kliknij przycisk **wdrażanie**.

  DC/OS wdraża Elasticsearch kontenera. Możesz śledzić postępy w **usług** strony.  

  ![Wdrażanie e? Elasticsearch](./media/container-service-dcos-vamp-canary-release/03_deply_elasticsearch.png)

### <a name="deploy-vamp"></a>Wdrażanie Vamp

Po Elasticsearch raporty jako **systemem**, można dodać pakiet Vamp Universe DC/OS. 

1. Przejdź do **Universe** i wyszukaj **vamp**. 
  ![Vamp na universe DC/OS](./media/container-service-dcos-vamp-canary-release/04_universe_deploy_vamp.png)

2. Kliknij przycisk **zainstalować** obok vamp pakietu, a następnie wybierz pozycję **zaawansowane instalacji**.

3. Przewiń w dół i wprowadź następujący elasticsearch — adres url: `http://elasticsearch.marathon.mesos:9200`. 

  ![Wprowadź adres URL Elasticsearch](./media/container-service-dcos-vamp-canary-release/05_universe_elasticsearch_url.png)

4. Kliknij przycisk **Przejrzyj i zainstaluj**, następnie kliknij przycisk **zainstalować** do wdrożenia.  

  DC/OS wdraża wszystkich wymaganych składników Vamp. Możesz śledzić postępy w **usług** strony.
  
  ![Wdrażanie Vamp jako pakiet universe](./media/container-service-dcos-vamp-canary-release/06_deploy_vamp.png)
  
5. Po zakończeniu wdrażania, aby dostęp do interfejsu użytkownika Vamp:

  ![Usługa vamp na DC/OS](./media/container-service-dcos-vamp-canary-release/07_deploy_vamp_complete.png)
  
  ![Vamp interfejsu użytkownika](./media/container-service-dcos-vamp-canary-release/08_vamp_ui.png)


## <a name="deploy-your-first-service"></a>Wdrażanie w ramach pierwszej usługi

Teraz tego Vamp jest uruchomiona, należy wdrożyć usługę z planu. 

W najprostszej postaci [planu Vamp](http://vamp.io/documentation/using-vamp/blueprints/) opisuje punktów końcowych (bramy), klastrami i usługi do wdrożenia. Vamp używa klastrów do grupowania różnych wariantów o tej samej usługi w grupy logiczne mozgi zwalniania lub A / B testowania.  

W tym scenariuszu przykładowej aplikacji wbudowanymi o nazwie [ **sava**](https://github.com/magneticio/sava), które jest w wersji 1.0. Monolityczna jest spakowany w kontenerze Docker, który znajduje się w Centrum Docker w obszarze magneticio/sava:1.0.0. Aplikacja jest uruchamiana normalnie portu 8080, ale w takim przypadku udostępnić obszarze port 9050. Wdróż aplikację za pomocą Vamp przy użyciu prostego planu.

1. Przejdź do **wdrożeń**.

2. Kliknij pozycję **Add** (Dodaj).

3. Wklej następujący plan yaml programu. Ten plan zawiera jednego klastra z tylko jedną usługę variant, który możemy zmienić w późniejszym kroku:

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

4. Kliknij pozycję **Zapisz**. Vamp inicjuje wdrożenia.

Wdrożenie, znajduje się na **wdrożeń** strony. Kliknij przycisk wdrożenia do monitorowania stanu.

![Vamp interfejsu użytkownika — wdrażanie sava](./media/container-service-dcos-vamp-canary-release/09_sava100.png)

![Usługa sava w Vamp interfejsu użytkownika](./media/container-service-dcos-vamp-canary-release/09a_sava100.png)

Bramy są tworzone, które są wymienione na **bram** strony:

* punkt końcowy stabilna dostęp do uruchomionej usługi (port 9050) 
* zarządzane Vamp wewnętrzny bramy (więcej informacji na temat tej bramy później). 

![Vamp interfejsu użytkownika — sava bram](./media/container-service-dcos-vamp-canary-release/10_vamp_sava_gateways.png)

Teraz wdrożono usługę sava, ale nie masz dostępu do jego zewnętrznie ponieważ modułu równoważenia obciążenia Azure nie może ustalić, aby przesyłał dalej ruch do niego jeszcze. Dostęp do usługi, zaktualizuj konfiguracji sieci platformy Azure.


## <a name="update-the-azure-network-configuration"></a>Zaktualizuj konfigurację sieci platformy Azure

Vamp wdrożyć usługę sava w węzłach agenta DC/OS, udostępnianie stabilna punktu końcowego w porcie 9050. Aby uzyskać dostęp do usługi z poza klastrem DC/OS, wprowadź następujące zmiany w konfiguracji sieci platformy Azure w danym wdrożeniu klastra: 

1. **Konfigurowanie równoważenia obciążenia Azure** dla agentów (zasób o nazwie **dcos-agent-lb-xxxx**) z sondy kondycji i zasadę, aby przesyłał dalej ruch na porcie 9050 do wystąpień sava. 

2. **Zaktualizuj sieciową grupę zabezpieczeń** dla agentów publicznych (zasób o nazwie **XXXX-agent publicznego nsg-XXXX**) zezwalająca na ruch na porcie 9050.

Aby uzyskać szczegółowe instrukcje dotyczące wykonywania tych zadań za pomocą portalu Azure, zobacz [włączyć publiczny dostęp do aplikacji usługi kontenera platformy Azure](container-service-enable-public-access.md). Określ port 9050 dla wszystkich ustawień portu.


Po utworzeniu wszystko przejdź do **omówienie** bloku modułu równoważenia obciążenia agenta DC/OS (zasób o nazwie **dcos-agent-lb-xxxx**). Znajdź **publicznego adresu IP**i umożliwia dostęp sava portu 9050 adres.

![Portal Azure — Pobierz publiczny adres IP](./media/container-service-dcos-vamp-canary-release/18_public_ip_address.png)

![sava](./media/container-service-dcos-vamp-canary-release/19_sava100.png)


## <a name="run-a-canary-release"></a>Uruchom mozgi zlecenia

Załóżmy, że masz nową wersję tej aplikacji, które mają mozgi wersji w środowisku produkcyjnym. Została ona konteneryzowanych jako magneticio/sava:1.1.0 i gotowe. Vamp umożliwia łatwe dodanie nowych usług do uruchomionego wdrożenia. Te usługi "scalonych" są wdrożyć obok istniejącej usługi w klastrze i przypisano wagę % 0. Nie ruch jest kierowany do nowo scalonego dopóki dostosować Dystrybucja ruchu. Suwak wagi w Interfejsie użytkownika Vamp umożliwia pełną kontrolę nad dystrybucji, umożliwiające przyrostowe dostosowań (wersja mozgi) lub błyskawicznych wycofywania.

### <a name="merge-a-new-service-variant"></a>Scal nowy wariant usługi

Aby scalić nową usługę sava 1.1 z wykonywanego wdrożenia:

1. W Interfejsie użytkownika Vamp, kliknij przycisk **plany**.

2. Kliknij przycisk **Dodaj** i wklej następujący plan yaml programu: ten plan opisano nowe usługi typ variant (sava: 1.1.0) do wdrożenia w ramach istniejącego klastra (sava_cluster).

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
  
3. Kliknij pozycję **Zapisz**. Plan są przechowywane i wyświetlane na **plany** strony.

4. Otwórz menu Akcja na plan sava: 1.1, a następnie kliknij przycisk **scalić**.

  ![Vamp interfejsu użytkownika — plany](./media/container-service-dcos-vamp-canary-release/20_sava110_mergeto.png)

5. Wybierz **sava** wdrożenia i kliknij przycisk **scalania**.

  ![Vamp interfejsu użytkownika — plan scalania do wdrożenia](./media/container-service-dcos-vamp-canary-release/21_sava110_merge.png)

Vamp wdraża nowy wariant usługi sava: 1.1.0 opisanego w plan obok sava: 1.0.0 w **sava_cluster** uruchomionego wdrożenia. 

![Vamp interfejsu użytkownika — wdrażania sava aktualizacji](./media/container-service-dcos-vamp-canary-release/22_sava_cluster.png)

**Sava/sava_cluster/webport** bramy (punkt końcowy klastra) jest także aktualizowana za Dodawanie trasy do nowo wdrożonym sava: 1.1.0. W tym momencie nie ruch jest przekierowywany tutaj ( **wagi** jest ustawiona na 0%).

![Vamp interfejsu użytkownika — klastra bramy](./media/container-service-dcos-vamp-canary-release/23_sava_cluster_webport.png)

### <a name="canary-release"></a>Mozgi zlecenia

W obu wersjach sava wdrożone w tym samym klastrze, Dostosuj Dystrybucja ruchu między nimi za pomocą przenoszenia **wagi** suwaka.

1. Kliknij przycisk ![Vamp interfejsu użytkownika — Edytuj](./media/container-service-dcos-vamp-canary-release/vamp_ui_edit.png) obok **wagi**.

2. Ustaw dystrybucja wag do 50% 50% i kliknij przycisk **zapisać**.

  ![Vamp interfejsu użytkownika — suwak wagi bramy](./media/container-service-dcos-vamp-canary-release/24_sava_cluster_webport_weight.png)

3. Wróć do przeglądarki i Odśwież stronę sava kilka razy. Teraz aplikacja sava przełącza między strony sava: 1.0 i strony sava: 1.1.

  ![naprzemiennych sava1.0 i sava1.1 usługi](./media/container-service-dcos-vamp-canary-release/25_sava_100_101.png)


  > [!NOTE]
  > Alternatywy tej strony działa najlepiej z "Incognito" lub "Anonymous" Tryb przeglądarki z powodu buforowanie zasoby statyczne.
  >

### <a name="filter-traffic"></a>Filtrowanie ruchu

Po wdrożeniu Załóżmy, że wykrywane niezgodności w sava: 1.1.0, że powoduje problemy z wyświetlaniem w przeglądarki Firefox. Można ustawić Vamp do filtrowania ruchu przychodzącego i bezpośrednie, wszyscy użytkownicy Firefox z powrotem do znanego stabilna sava: 1.0.0. Ten filtr natychmiast rozpoznaje przerw w działaniu Firefox użytkowników, gdy każdy inny nadal korzystać z zalet ulepszone sava: 1.1.0.

Vamp używa **warunki** do filtrowania ruchu między trasy w bramy. Ruch jest najpierw filtrowane i kierowane zgodnie z warunkami stosowane do każdej trasy. Wszystkie pozostałe ruchu jest dystrybuowane zgodnie z ustawieniem wagi bramy.

Można utworzyć warunek filtrowania wszyscy użytkownicy Firefox i kierowania ich do starego sava: 1.0.0:

1. Na sava/sava_cluster/webport **bram** kliknij przycisk ![Vamp interfejsu użytkownika — Edytuj](./media/container-service-dcos-vamp-canary-release/vamp_ui_edit.png) można dodać **WARUNKU** do sava/sava_cluster/sava:1.0.0/webport trasy. 

2. Wprowadź warunek **agenta użytkownika przeglądarki Firefox ==** i kliknij przycisk ![Vamp interfejsu użytkownika — Zapisz](./media/container-service-dcos-vamp-canary-release/vamp_ui_save.png).

  Vamp dodaje stanie siły domyślne 0%. Aby rozpocząć filtrowanie ruchu, należy dostosować siły warunku.

3. Kliknij przycisk ![Vamp interfejsu użytkownika — Edytuj](./media/container-service-dcos-vamp-canary-release/vamp_ui_edit.png) zmienić **siły** zastosowana do warunku.
 
4. Ustaw **siły** do 100% i kliknij przycisk ![Vamp interfejsu użytkownika — Zapisz](./media/container-service-dcos-vamp-canary-release/vamp_ui_save.png) do zapisania.

  Vamp teraz wysyła cały ruch spełniających warunek (wszyscy użytkownicy Firefox) do sava: 1.0.0.

  ![Vamp interfejsu użytkownika — dotyczy stanu bramy](./media/container-service-dcos-vamp-canary-release/26_apply_condition.png)

5. Na koniec należy dostosować wagi bramy do wysyłania całego ruchu pozostałych (wszyscy użytkownicy z systemem innym niż Firefox) do nowej sava: 1.1.0. Kliknij przycisk ![Vamp interfejsu użytkownika — Edytuj](./media/container-service-dcos-vamp-canary-release/vamp_ui_edit.png) obok **wagi** i ustawić dystrybucja wag, tak aby 100% jest kierowany do sava/sava_cluster/sava:1.1.0/webport trasy.

  Cały ruch, które nie są filtrowane według warunku jest teraz kierowane do nowego sava: 1.1.0.

6. Aby wyświetlić filtru akcji, otwórz dwóch różnych przeglądarkach (jeden Firefox i inną przeglądarkę) i uzyskania dostępu do usługi sava z obu. Wszystkie żądania Firefox są wysyłane do sava: 1.0.0, podczas gdy wszystkich innych przeglądarkach są kierowane do sava: 1.1.0.

  ![Vamp interfejsu użytkownika — filtrowania ruchu](./media/container-service-dcos-vamp-canary-release/27_filter_traffic.png)

## <a name="summing-up"></a>Podsumowanie

Ten artykuł został szybkie wprowadzenie do Vamp w klastrze DC/OS. Po pierwsze otrzymano Vamp i systemem w sieci Azure kontenera DC/OS usługi klastra, wdrożyć usługi za pomocą planu Vamp i dostęp do niego w punkcie końcowym narażonych (bramy).

Możemy również dotknięciu na niektóre zaawansowane funkcje Vamp: scalanie nowych wariant usługi do uruchomionego wdrożenia i wprowadzenie do przyrostowo, a następnie filtrowania ruchu, aby rozwiązać znany narażony.


## <a name="next-steps"></a>Kolejne kroki

* Więcej informacji o zarządzaniu Vamp akcji za pomocą [Vamp interfejsu API REST](http://vamp.io/documentation/api/api-reference/).

* Tworzenie skryptów automatyzacji Vamp w środowisku Node.js i uruchom je jako [Vamp przepływy pracy](http://vamp.io/documentation/tutorials/create-a-workflow/).

* Zobacz dodatkowe [samouczki VAMP](http://vamp.io/documentation/tutorials/overview/).

