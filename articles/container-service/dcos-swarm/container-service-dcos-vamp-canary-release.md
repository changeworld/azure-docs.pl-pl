---
title: (PRZESTARZAŁE) Wydanie kanaryjne z vampem w klastrze usługi Azure DC/OS
description: Jak używać vamp do kanarek usługi wydania i zastosować inteligentne filtrowanie ruchu w klastrze usługi Azure Container Service DC/OS
author: gggina
ms.service: container-service
ms.topic: conceptual
ms.date: 04/17/2017
ms.author: rasquill
ms.custom: mvc
ms.openlocfilehash: 2af20a1ddf4239b7eec6cceabf2ff9711959c128
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77189108"
---
# <a name="deprecated-canary-release-microservices-with-vamp-on-an-azure-container-service-dcos-cluster"></a>(PRZESTARZAŁE) Mikrousługi kanaryjnej wersji z vamp w klastrze kontrolera domeny/systemu operacyjnego usługi kontenerowej azure

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

W tym instruktażu skonfigurowaliśmy usługę Vamp on Azure Container Service przy za pomocą klastra kontrolera domeny/systemu operacyjnego. Możemy canary zwolnić vamp demo usługi "sava", a następnie rozwiązać niezgodność usługi z Firefoksem, stosując inteligentne filtrowanie ruchu. 

> [!TIP] 
> W tym instruktażu Vamp działa w klastrze DC/OS, ale można również użyć Vamp z Kubernetes jako koordynatora.
>

## <a name="about-canary-releases-and-vamp"></a>O wydaniach kanarek i Vamp


[Canary zwolnienie](https://martinfowler.com/bliki/CanaryRelease.html) to inteligentna strategia wdrażania przyjęta przez innowacyjne organizacje, takie jak Netflix, Facebook i Spotify. Jest to podejście, które ma sens, ponieważ zmniejsza problemy, wprowadza siatki bezpieczeństwa i zwiększa innowacyjność. Dlaczego więc nie wszystkie firmy z niego korzystają? Rozszerzenie potoku ciągłej integracji/ciągłego wdrażania o strategie kanaryjskie zwiększa złożoność i wymaga rozległej wiedzy i doświadczenia devops. To wystarczy, aby zablokować zarówno mniejsze firmy, jak i przedsiębiorstwa, zanim jeszcze się rozpoczną. 

[Vamp](https://vamp.io/) to system open-source zaprojektowany, aby ułatwić to przejście i przynieść canary zwalnianie funkcji do preferowanego harmonogramu kontenerów. Funkcjonalność kanarek Vamp wykracza poza wdrożenia oparte na procentach. Ruch można filtrować i dzielić w różnych warunkach, na przykład w celu kierowania reklam do określonych użytkowników, zakresów adresów IP lub urządzeń. Vamp śledzi i analizuje metryki wydajności, umożliwiając automatyzację na podstawie rzeczywistych danych. Można skonfigurować automatyczne wycofywanie błędów lub skalować poszczególne warianty usługi na podstawie obciążenia lub opóźnienia.

## <a name="set-up-azure-container-service-with-dcos"></a>Konfigurowanie usługi azure container service przy pracy z kontrolerem domeny/systemu operacyjnego



1. [Wdrażanie klastra dc/os](container-service-deployment.md) z jednym wzorcem i dwoma agentami o domyślnym rozmiarze. 

2. [Utwórz tunel SSH,](../container-service-connect.md) aby połączyć się z klastrem DC/OS. W tym artykule przyjęto założenie, że tunel do klastra na porcie lokalnym 80.


## <a name="set-up-vamp"></a>Konfigurowanie vamp

Teraz, gdy masz uruchomiony klaster DC/OS, możesz zainstalować vamp z interfejsu\/użytkownika dc/os (http: /localhost:80). 

![Interfejs użytkownika platformy DC/OS](./media/container-service-dcos-vamp-canary-release/01_set_up_vamp.png)

Instalacja odbywa się w dwóch etapach:

1. **Wdrażanie Elasticsearch**.

2. Następnie **wdrożyć Vamp,** instalując pakiet Vamp DC/OS wszechświata.

### <a name="deploy-elasticsearch"></a>Wdrażanie elasticsearch

Vamp wymaga Elasticsearch dla kolekcji metryki i agregacji. Obrazy [dockera magnetycznego](https://hub.docker.com/r/magneticio/elastic/) umożliwiają wdrożenie zgodnego stosu Vamp Elasticsearch.

1. W interfejsie użytkownika kontrolera domeny/systemu operacyjnego przejdź do **witryny Usługi** i kliknij pozycję **Wdrażanie usługi**.

2. Wybierz **tryb JSON** z okna podręcznego **Wdrażanie nowej usługi.**

   ![Wybierz tryb JSON](./media/container-service-dcos-vamp-canary-release/02_deploy_service_json_mode.png)

3. Wklej w następującym JSON. Ta konfiguracja uruchamia kontener z 1 GB pamięci RAM i podstawową kontrolą kondycji na porcie Elasticsearch.
  
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

   Dc/OS wdraża kontener Elasticsearch. Postęp można śledzić na stronie **Usługi.**  

   ![wdrożyć e? Elasticsearch (wyszukiwanie elastyczne)](./media/container-service-dcos-vamp-canary-release/03_deply_elasticsearch.png)

### <a name="deploy-vamp"></a>Wdrażanie vamp

Po elasticsearch raporty jako **uruchomiony,** można dodać Vamp DC / OS Universe pakiet. 

1. Przejdź do **Universe** i poszukaj **wampira.** 
   ![Vamp w uniwersum DC/OS](./media/container-service-dcos-vamp-canary-release/04_universe_deploy_vamp.png)

2. Kliknij **przycisk Zainstaluj** obok pakietu vamp i wybierz pozycję **Instalacja zaawansowana**.

3. Przewiń w dół i wprowadź `http://elasticsearch.marathon.mesos:9200`następujący elasticsearch-url: . 

   ![Wprowadź adres URL elasticsearch](./media/container-service-dcos-vamp-canary-release/05_universe_elasticsearch_url.png)

4. Kliknij **pozycję Przejrzyj i zainstaluj**, a następnie kliknij przycisk **Zainstaluj,** aby rozpocząć wdrożenie.  

   DC/OS wdraża wszystkie wymagane składniki Vamp. Postęp można śledzić na stronie **Usługi.**
  
   ![Wdrażanie vamp jako pakiet wszechświata](./media/container-service-dcos-vamp-canary-release/06_deploy_vamp.png)
  
5. Po zakończeniu wdrażania można uzyskać dostęp do interfejsu użytkownika Vamp:

   ![Usługa Vamp na dc/os](./media/container-service-dcos-vamp-canary-release/07_deploy_vamp_complete.png)
  
   ![Interfejs użytkownika Vamp](./media/container-service-dcos-vamp-canary-release/08_vamp_ui.png)


## <a name="deploy-your-first-service"></a>Wdrażanie pierwszej usługi

Teraz, gdy Vamp jest uruchomiony, wdrożyć usługę z planu. 

W najprostszej formie [plan Vamp](https://docs.vamp.io/how-vamp-works/vamp-and-kubernetes#vamp-deployments) opisuje punkty końcowe (bramy), klastry i usługi do wdrożenia. Vamp używa klastrów do grupowania różnych wariantów tej samej usługi w grupy logiczne do zwalniania kanarek lub testowania A/B.  

W tym scenariuszu używa przykładowej aplikacji monolityczne o nazwie [**sava**](https://github.com/magneticio/sava), który jest w wersji 1.0. Monolit jest pakowany w kontenerze platformy Docker, który znajduje się w centrum platformy Docker pod względem magneticio/sava:1.0.0. Aplikacja działa zwykle na porcie 8080, ale chcesz udostępnić go pod portem 9050 w tym przypadku. Wdrażanie aplikacji za pośrednictwem Vamp przy użyciu prostego planu.

1. Przejdź do **opcji Wdrożenia**.

2. Kliknij przycisk **Dodaj**.

3. Wklej w poniższym planie YAML. Ten plan zawiera jeden klaster z tylko jednym wariantem usługi, który możemy zmienić w późniejszym kroku:

   ```YAML
   name: sava                        # deployment name
   gateways:
    9050: sava_cluster/webport      # stable endpoint
   clusters:
    sava_cluster:               # cluster to create
        -
          breed:
            name: sava:1.0.0        # service variant name
            deployable: magneticio/sava:1.0.0
            ports:
              webport: 8080/http # cluster endpoint, used for canary releasing
   ```

4. Kliknij przycisk **Zapisz**. Vamp inicjuje wdrożenie.

Wdrożenie jest wymienione na stronie **Wdrożenia.** Kliknij wdrożenie, aby monitorować jego stan.

![Interfejs użytkownika Vamp - wdrażanie sava](./media/container-service-dcos-vamp-canary-release/09_sava100.png)

![usługi sava w Interfejsie Vamp](./media/container-service-dcos-vamp-canary-release/09a_sava100.png)

Tworzone są dwie bramy, które są wymienione na stronie **Bramy:**

* stabilny punkt końcowy dostępu do uruchomionej usługi (port 9050) 
* bramę wewnętrzną zarządzaną przez Vamp (więcej o tej bramie później). 

![Vamp UI - bramy sava](./media/container-service-dcos-vamp-canary-release/10_vamp_sava_gateways.png)

Usługa sava została wdrożona, ale nie można uzyskać do niej dostępu zewnętrznie, ponieważ moduł równoważenia obciążenia platformy Azure nie wie jeszcze, aby przesłać ruch do niej. Aby uzyskać dostęp do usługi, zaktualizuj konfigurację sieci platformy Azure.


## <a name="update-the-azure-network-configuration"></a>Aktualizowanie konfiguracji sieci platformy Azure

Vamp wdrożył usługę sava w węzłach agenta kontrolera domeny/systemu operacyjnego, ujawniając stabilny punkt końcowy na porcie 9050. Aby uzyskać dostęp do usługi spoza klastra kontrolera domeny/systemu operacyjnego, należy wprowadzić następujące zmiany w konfiguracji sieci platformy Azure we wdrożeniu klastra: 

1. **Skonfiguruj moduł równoważenia obciążenia platformy Azure** dla agentów (zasób o nazwie **dcos-agent-lb-xxxx)** za pomocą sondy kondycji i reguły przekazywania ruchu na porcie 9050 do wystąpień sawy. 

2. **Zaktualizuj sieciową grupę zabezpieczeń** dla agentów publicznych (zasób o nazwie **XXXX-agent-public-nsg-XXXX),** aby zezwolić na ruch na porcie 9050.

Aby uzyskać szczegółowe kroki, aby wykonać te zadania za pomocą witryny Azure portal, zobacz [Włączanie dostępu publicznego do aplikacji usługi Azure Container Service.](container-service-enable-public-access.md) Określ port 9050 dla wszystkich ustawień portu.


Po utworzeniu wszystko przejdź do **bloku Przegląd** modułu równoważenia obciążenia agenta DC/OS (zasobu o nazwie **dcos-agent-lb-xxxx**). Znajdź **publiczny adres IP**i użyj adresu, aby uzyskać dostęp do sava w porcie 9050.

![Portal Azure — uzyskaj publiczny adres IP](./media/container-service-dcos-vamp-canary-release/18_public_ip_address.png)

![Sava](./media/container-service-dcos-vamp-canary-release/19_sava100.png)


## <a name="run-a-canary-release"></a>Uruchamianie kanarek

Załóżmy, że masz nową wersję tej aplikacji, które chcesz canary wydania do produkcji. Masz go konteneryzowany jako magneticio /sava:1.1.0 i jesteś gotowy do pracy. Vamp pozwala łatwo dodać nowe usługi do uruchomionego wdrożenia. Te "scalone" usługi są wdrażane wraz z istniejącymi usługami w klastrze i mają przypisaną wagę 0%. Żaden ruch nie jest kierowany do nowo scalonej usługi, dopóki nie dostosujesz dystrybucji ruchu. Suwak wagi w interfejsie vamp zapewnia pełną kontrolę nad dystrybucją, co pozwala na stopniowe regulacje (uwolnienie kanarek) lub natychmiastowe wycofywanie.

### <a name="merge-a-new-service-variant"></a>Scalanie nowego wariantu usługi

Aby scalić nową usługę sava 1.1 z uruchomionym wdrożeniem:

1. W interfejsie użytkownika vamp kliknij pozycję **Plany**.

2. Kliknij **przycisk Dodaj** i wklej w następującym planie YAML: Ten plan opisuje nowy wariant usługi (sava:1.1.0) do wdrożenia w istniejącym klastrze (sava_cluster).

   ```YAML
   name: sava:1.1.0      # blueprint name
   clusters:
    sava_cluster:       # cluster to update
        -
          breed:
            name: sava:1.1.0    # service variant name
            deployable: magneticio/sava:1.1.0    
            ports:
              webport: 8080/http # cluster endpoint to update
   ```
  
3. Kliknij przycisk **Zapisz**. Plan jest przechowywany i wyświetlany na stronie **Plany.**

4. Otwórz menu akcji w planie sava:1.1 i kliknij przycisk **Scal do**.

   ![Interfejs Vamp - plany](./media/container-service-dcos-vamp-canary-release/20_sava110_mergeto.png)

5. Wybierz wdrożenie **sava** i kliknij przycisk **Scal**.

   ![Interfejs użytkownika Vamp - schemat scalania do wdrożenia](./media/container-service-dcos-vamp-canary-release/21_sava110_merge.png)

Vamp wdraża nowy wariant usługi sava:1.1.0 opisany w planie obok sava:1.0.0 w **sava_cluster** uruchomionego wdrożenia. 

![Interfejs użytkownika Vamp — zaktualizowane wdrożenie sava](./media/container-service-dcos-vamp-canary-release/22_sava_cluster.png)

Brama **sava/sava_cluster/webport** (punkt końcowy klastra) jest również aktualizowana, dodając trasę do nowo wdrożonej sava:1.1.0. W tym momencie nie jest tu kierowany ruch **(waga** jest ustawiona na 0%).

![Interfejs użytkownika vamp — brama klastra](./media/container-service-dcos-vamp-canary-release/23_sava_cluster_webport.png)

### <a name="canary-release"></a>Uwolnienie kanaryjne

W obu wersjach sava wdrożonych w tym samym klastrze dostosuj rozkład ruchu między nimi, przesuwając suwak **WEIGHT.**

1. Kliknij ![pozycję Vamp](./media/container-service-dcos-vamp-canary-release/vamp_ui_edit.png) UI - edytuj obok **pozycji WAGA**.

2. Ustaw rozkład masy na 50%/50% i kliknij przycisk **Zapisz**.

   ![Vamp UI - suwak wagi bramy](./media/container-service-dcos-vamp-canary-release/24_sava_cluster_webport_weight.png)

3. Wróć do przeglądarki i odśwież stronę sava jeszcze kilka razy. Aplikacja sava przełącza się teraz między stroną sava:1.0 a stroną sava:1.1.

   ![naprzemiennie usługi sava1.0 i sava1.1](./media/container-service-dcos-vamp-canary-release/25_sava_100_101.png)


  > [!NOTE]
  > Ta zmiana strony działa najlepiej w trybie "Incognito" lub "Anonimowy" przeglądarki ze względu na buforowanie zasobów statycznych.
  >

### <a name="filter-traffic"></a>Filtrowanie ruchu

Załóżmy, że po wdrożeniu wykryto niezgodność w sava:1.1.0, która powoduje problemy z wyświetlaniem w przeglądarkach Firefox. Możesz ustawić Vamp, aby filtrować ruch przychodzący i kierować wszystkich użytkowników Firefoksa z powrotem do znanej stabilnej sava:1.0.0. Ten filtr natychmiast rozwiązuje zakłócenia dla użytkowników Firefoksa, podczas gdy wszyscy inni nadal korzystają z zalet ulepszonego sava:1.1.0.

Vamp używa **warunków** do filtrowania ruchu między trasami w bramie. Ruch jest najpierw filtrowany i kierowany zgodnie z warunkami stosowanymi dla każdej trasy. Cały pozostały ruch jest rozdzielany zgodnie z ustawieniem wagi bramy.

Możesz utworzyć warunek filtrowania wszystkich użytkowników Firefoksa i kierowania ich do starego sava:1.0.0:

1. Na stronie sava/sava_cluster/webport **Gateways** kliknij ![pozycję Interfejs użytkownika](./media/container-service-dcos-vamp-canary-release/vamp_ui_edit.png) vamp — edytuj, aby dodać **warunek** do trasy sava/sava_cluster/sava:1.0.0/webport. 

2. Wprowadź warunek **user-agent ==** ![Firefox i kliknij](./media/container-service-dcos-vamp-canary-release/vamp_ui_save.png)Vamp UI - zapisz .

   Vamp dodaje warunek z domyślną siłą 0%. Aby rozpocząć filtrowanie ruchu, należy dostosować siłę stanu.

3. Kliknij ![przycisk Vamp](./media/container-service-dcos-vamp-canary-release/vamp_ui_edit.png) UI - edytuj, aby zmienić **siłę** zastosowaną do warunku.
 
4. Ustaw **siłę** na 100% ![i kliknij Vamp UI - zapisz,](./media/container-service-dcos-vamp-canary-release/vamp_ui_save.png) aby zapisać.

   Vamp wysyła teraz cały ruch pasujący do warunku (wszyscy użytkownicy Firefoksa) do sava:1.0.0.

   ![Interfejs użytkownika vamp - zastosuj warunek do bramy](./media/container-service-dcos-vamp-canary-release/26_apply_condition.png)

5. Na koniec dostosuj wagę bramy, aby wysłać cały pozostały ruch (wszyscy użytkownicy spoza Firefoksa) do nowego sava:1.1.0. Kliknij ![przycisk Vamp](./media/container-service-dcos-vamp-canary-release/vamp_ui_edit.png) UI - edytuj obok **pozycji WAGA** i ustaw rozkład masy, aby w 100% został przekierowany na trasę sava/sava_cluster/sava:1.1.0/webport.

   Cały ruch niefiltrowany według warunku jest teraz kierowany do nowego sava:1.1.0.

6. Aby zobaczyć filtr w akcji, otwórz dwie różne przeglądarki (jeden Firefox i jedna druga przeglądarka) i uzyskaj dostęp do usługi sava z obu. Wszystkie żądania Firefoksa są wysyłane do sava:1.0.0, podczas gdy wszystkie inne przeglądarki są kierowane do sava:1.1.0.

   ![Interfejs użytkownika vamp - filtr ruchu](./media/container-service-dcos-vamp-canary-release/27_filter_traffic.png)

## <a name="summing-up"></a>Podsumowanie

Ten artykuł był szybkim wprowadzeniem do Vamp w klastrze DC/OS. Na początek uruchomiono usługę Vamp w klastrze kontrolera domeny/systemu operacyjnego usługi kontenerowej platformy Azure, wdrożyłeś usługę z planem Vamp i uzyskasz do niej dostęp w ujawnionym punkcie końcowym (bramie).

Dotknęliśmy również kilku zaawansowanych funkcji Vamp: scalanie nowego wariantu usługi z uruchomionym wdrożeniem i wprowadzanie go stopniowo, a następnie filtrowanie ruchu w celu rozwiązania znanej niezgodności.


## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o zarządzaniu działaniami Vamp za pośrednictwem [interfejsu API Vamp REST](https://docs.vamp.io/how-vamp-works/events-and-metrics#events).

* Tworzenie skryptów automatyzacji Vamp w node.js i uruchamianie ich jako [przepływów pracy Vamp](https://docs.vamp.io/how-vamp-works/concepts-and-components#workflows).

* Zobacz dodatkowe [samouczki VAMP](https://docs.vamp.io/tutorials/).

