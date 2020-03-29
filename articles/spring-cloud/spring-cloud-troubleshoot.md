---
title: Przewodnik rozwiązywania problemów dla usługi Azure Spring Cloud | Dokumenty firmy Microsoft
description: Przewodnik dotyczący rozwiązywania problemów dla usługi Azure Spring Cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: troubleshooting
ms.date: 11/04/2019
ms.author: brendm
ms.openlocfilehash: 5dcdb03a6d4ec4f448108dbd771a44f362aa7f20
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76277584"
---
# <a name="troubleshoot-common-azure-spring-cloud-issues"></a>Rozwiązywanie typowych problemów z usługą Azure Spring Cloud

Ten artykuł zawiera instrukcje dotyczące rozwiązywania problemów z programowaniem usługi Azure Spring Cloud. Aby uzyskać dodatkowe informacje, zobacz [Często zadawane pytania dotyczące usługi Azure Spring Cloud](spring-cloud-faq.md).

## <a name="availability-performance-and-application-issues"></a>Problemy z dostępnością, wydajnością i aplikacjami

### <a name="my-application-cant-start-for-example-the-endpoint-cant-be-connected-or-it-returns-a-502-after-a-few-retries"></a>Nie można uruchomić aplikacji (na przykład nie można połączyć punktu końcowego lub zwraca wartość 502 po kilku ponownych próbach)

Eksportuj dzienniki do usługi Azure Log Analytics. Tabela dla dzienników aplikacji Wiosna nosi nazwę *AppPlatformLogsforSpring*. Aby dowiedzieć się więcej, zobacz [Analizowanie dzienników i metryk z ustawieniami diagnostyki](diagnostic-services.md).

W dziennikach może pojawić się następujący komunikat o błędzie:

> "org.springframework.context.ApplicationContextException: Nie można uruchomić serwera sieci Web"

Komunikat wskazuje jeden z dwóch prawdopodobnych problemów: 
* Brakuje jednej z fasoli lub jednej z jej zależności.
* Brakuje jednej z właściwości ziarna lub jest ona nieprawidłowa. W tym przypadku pojawi się prawdopodobnie komunikat "java.lang.IllegalArgumentException".

Powiązania usługi może również powodować błędy uruchamiania aplikacji. Aby zbadać dzienniki, użyj słów kluczowych, które są związane z usługami powiązanymi. Na przykład załóżmy, że aplikacja ma powiązanie z wystąpieniem MySQL, który jest ustawiony na czas systemu lokalnego. Jeśli uruchomienie aplikacji nie powiedzie się, w dzienniku może pojawić się następujący komunikat o błędzie:

> "java.sql.SQLException: Wartość strefy czasowej serwera 'Skoordynowany czas uniwersalny' jest nierozpoznana lub reprezentuje więcej niż jedną strefę czasową."

Aby naprawić ten błąd, `server parameters` przejdź do wystąpienia MySQL `time_zone` i zmień wartość z *SYSTEM* na *+0:00*.


### <a name="my-application-crashes-or-throws-an-unexpected-error"></a>Moja aplikacja ulega awarii lub zgłasza nieoczekiwany błąd

Gdy debugowanie aplikacji ulega awarii, należy rozpocząć od sprawdzenia stanu uruchamiania i stanu odnajdywania aplikacji. Aby to zrobić, przejdź do _zarządzania aplikacjami_ w witrynie Azure portal, aby upewnić się, że stany wszystkich aplikacji są _uruchomione_ i _up_.

* Jeśli stan jest _uruchomiony,_ ale stan odnajdywania nie jest _up,_ przejdź do sekcji ["Moja aplikacja nie może być zarejestrowana".](#my-application-cant-be-registered)

* Jeśli stan odnajdywania jest _UP_, przejdź do Metryki, aby sprawdzić kondycję aplikacji. Sprawdź następujące dane:


  - `TomcatErrorCount`(_tomcat.global.error):_ Wszystkie wyjątki aplikacji Wiosna są liczone tutaj. Jeśli ta liczba jest duża, przejdź do usługi Azure Log Analytics, aby sprawdzić dzienniki aplikacji.

  - `AppMemoryMax`(_jvm.memory.max):_ Maksymalna ilość pamięci dostępnej dla aplikacji. Kwota może być niezdefiniowana lub może ulec zmianie w czasie, jeśli zostanie zdefiniowana. Jeśli jest zdefiniowany, ilość używanej i zatwierdzonej pamięci jest zawsze mniejsza lub równa maks. Jednak alokacja pamięci może `OutOfMemoryError` zakończyć się niepowodzeniem z komunikatem, jeśli alokacja próbuje zwiększyć używanej pamięci, tak aby *użyta > zatwierdzona,* nawet jeśli *jest używana <= max* jest nadal true. W takiej sytuacji spróbuj zwiększyć maksymalny rozmiar sterty przy użyciu parametru. `-Xmx`

  - `AppMemoryUsed`(_jvm.memory.used):_ Ilość pamięci w bajtach, która jest obecnie używana przez aplikację. Dla normalnego obciążenia aplikacji Java, ta seria metryki tworzy *sawtooth* wzorzec, gdzie użycie pamięci stale wzrasta i zmniejsza się w małych przyrostach i nagle spada dużo, a następnie wzorzec powtarza. Ta seria metryk występuje z powodu wyrzucania elementów bezużytecznych wewnątrz maszyny wirtualnej Java, gdzie akcje zbierania reprezentują krople na wzorzec sawtooth.
    
    Ta metryka jest ważna, aby ułatwić identyfikację problemów z pamięcią, takich jak:
    * Eksplozja pamięci na samym początku.
    * Alokacja pamięci przepięciowej dla określonej ścieżki logicznej.
    * Stopniowe przecieki pamięci.

  Aby uzyskać więcej informacji, zobacz [Metryki](spring-cloud-concept-metrics.md).

Aby dowiedzieć się więcej o usłudze Azure Log Analytics, zobacz [Wprowadzenie do usługi Log Analytics w usłudze Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal).

### <a name="my-application-experiences-high-cpu-usage-or-high-memory-usage"></a>Moja aplikacja używa dużo mocy procesora lub pamięci

Jeśli aplikacja występuje wysokie użycie procesora CPU lub pamięci, jedna z dwóch rzeczy jest prawdą:
* Wszystkie wystąpienia aplikacji występują wysokie użycie procesora CPU lub pamięci.
* Niektóre wystąpienia aplikacji występują wysokie użycie procesora CPU lub pamięci.

Aby ustalić, która sytuacja ma zastosowanie, wykonaj następujące czynności:

1. Przejdź do **metryki**, a następnie wybierz procent **użycia procesora CPU usługi** lub pamięć usługi **używane**.
2. Dodaj **app=** filtr, aby określić, którą aplikację chcesz monitorować.
3. Podziel metryki według **instancji**.

Jeśli *wszystkie wystąpienia* występują wysokie użycie procesora CPU lub pamięci, należy albo skalować w poziomie aplikacji lub skalować w górę użycia procesora CPU lub pamięci. Aby uzyskać więcej informacji, zobacz [Samouczek: Skalowanie aplikacji w usłudze Azure Spring Cloud](spring-cloud-tutorial-scale-manual.md).

Jeśli *w niektórych wystąpieniach* występuje wysokie użycie procesora CPU lub pamięci, sprawdź stan wystąpienia i jego stan odnajdywania.

Aby uzyskać więcej informacji, zobacz [Metryki dla usługi Azure Spring Cloud](spring-cloud-concept-metrics.md).

Jeśli wszystkie wystąpienia są uruchomione, przejdź do usługi Azure Log Analytics, aby zbadać dzienniki aplikacji i przejrzeć logikę kodu. Pomoże to sprawdzić, czy którykolwiek z nich może mieć wpływ na partycjonowanie skali. Aby uzyskać więcej informacji, zobacz [Analizowanie dzienników i metryk z ustawieniami diagnostyki](diagnostic-services.md).

Aby dowiedzieć się więcej o usłudze Azure Log Analytics, zobacz [Wprowadzenie do usługi Log Analytics w usłudze Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal). Kwerenda dzienniki przy użyciu [języka zapytania Kusto](https://docs.microsoft.com/azure/kusto/query/).

### <a name="checklist-for-deploying-your-spring-application-to-azure-spring-cloud"></a>Lista kontrolna wdrażania aplikacji Spring w chmurze Azure Spring Cloud

Przed rozpoczęciem stosowania aplikacji upewnij się, że spełnia ona następujące kryteria:

* Aplikacja może działać lokalnie z określoną wersją środowiska wykonawczego Java.
* Konfiguracja środowiska (CPU/RAM/Instances) spełnia minimalne wymagania określone przez dostawcę aplikacji.
* Elementy konfiguracji mają oczekiwane wartości. Aby uzyskać więcej informacji, zobacz [Config Server](spring-cloud-tutorial-config-server.md).
* Zmienne środowiskowe mają swoje oczekiwane wartości.
* Parametry JVM mają oczekiwane wartości.
* Zaleca się wyłączenie lub usunięcie wbudowanych usług _config server_ i _spring service registry_ z pakietu aplikacji.
* Jeśli jakiekolwiek zasoby platformy Azure mają być powiązane za pomocą _powiązania usługi_, upewnij się, że zasoby docelowe zostały uruchomione.

## <a name="configuration-and-management"></a>Konfigurowanie i zarządzanie

### <a name="i-encountered-a-problem-with-creating-an-azure-spring-cloud-service-instance"></a>Napotkałem problem z tworzeniem wystąpienia usługi Azure Spring Cloud

Po skonfigurowaniu wystąpienia usługi Azure Spring Cloud przy użyciu witryny Azure portal, usługa Azure Spring Cloud wykonuje sprawdzanie poprawności dla Ciebie.

Ale jeśli spróbujesz skonfigurować wystąpienie usługi Azure Spring Cloud przy użyciu interfejsu [wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) lub [szablonu usługi Azure Resource Manager,](https://docs.microsoft.com/azure/azure-resource-manager/)sprawdź, czy:

* Subskrypcja jest aktywna.
* Lokalizacja jest [obsługiwana](spring-cloud-faq.md) przez usługę Azure Spring Cloud.
* Grupa zasobów dla wystąpienia jest już utworzona.
* Nazwa zasobu jest zgodna z regułą nazewnictwa. Musi zawierać tylko małe litery, cyfry i łączniki. Pierwszy znak musi być literą. Ostatni znak musi być literą lub cyfrą. Wartość musi zawierać od 2 do 32 znaków.

Jeśli chcesz skonfigurować wystąpienie usługi Azure Spring Cloud przy użyciu szablonu Menedżera zasobów, najpierw zapoznaj się [ze zrozumieniem struktury i składni szablonów usługi Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates).

Nazwa wystąpienia usługi Azure Spring Cloud będzie używana do żądania nazwy `azureapps.io`poddomeny w obszarze , więc instalacja zakończy się niepowodzeniem, jeśli nazwa zostanie wpuszkuje się z istniejącą. Więcej szczegółów można znaleźć w dziennikach aktywności.

### <a name="i-cant-deploy-a-jar-package"></a>Nie mogę wdrożyć pakietu JAR

Nie można przekazać pakietu archiwum Java (JAR)/source przy użyciu witryny Azure portal lub szablonu Menedżera zasobów.

Po wdrożeniu pakietu aplikacji przy użyciu [interfejsu wiersza polecenia platformy Azure,](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)interfejs wiersza polecenia platformy Azure okresowo sonduje postęp wdrażania i w końcu wyświetla wynik wdrożenia.

Jeśli sondowanie zostanie przerwane, możesz użyć następującego polecenia, aby pobrać dzienniki wdrażania:

`az spring-cloud app show-deploy-log -n <app-name>`

Upewnij się, że aplikacja jest spakowana w prawidłowym [formacie jar pliku wykonywalnego](https://docs.spring.io/spring-boot/docs/current/reference/html/executable-jar.html). Jeśli nie jest poprawnie spakowany, zostanie wyświetlony komunikat o błędzie podobny do następującego:

> "Błąd: Nieprawidłowy lub uszkodzony plik jarfile /jar/38bc8ea1-a6bb-4736-8e93-e8f3b52c8714"

### <a name="i-cant-deploy-a-source-package"></a>Nie mogę wdrożyć pakietu źródłowego

Nie można przekazać pakietu JAR/source przy użyciu witryny Azure portal lub szablonu Menedżera zasobów.

Po wdrożeniu pakietu aplikacji przy użyciu [interfejsu wiersza polecenia platformy Azure,](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)interfejs wiersza polecenia platformy Azure okresowo sonduje postęp wdrażania i w końcu wyświetla wynik wdrożenia.

Jeśli sondowanie zostanie przerwane, możesz użyć następującego polecenia, aby pobrać dzienniki kompilowania i wdrażania:

`az spring-cloud app show-deploy-log -n <app-name>`

Należy jednak pamiętać, że jedno wystąpienie usługi Azure Spring Cloud może wyzwolić tylko jedno zadanie kompilacji dla jednego pakietu źródłowego w tym czasie. Aby uzyskać więcej informacji, zobacz [Wdrażanie aplikacji](spring-cloud-quickstart-launch-app-portal.md) i [Konfigurowanie środowiska przejściowego w usłudze Azure Spring Cloud](spring-cloud-howto-staging-environment.md).

### <a name="my-application-cant-be-registered"></a>Nie można zarejestrować mojej aplikacji

W większości przypadków taka sytuacja występuje, gdy wymagane zależności i *odnajdowanie usługi* nie są poprawnie skonfigurowane w pliku modelu obiektu projektu (POM). *Required Dependencies* Po skonfigurowaniu wbudowany punkt końcowy serwera rejestru usług jest wstrzykiwany jako zmienna środowiskowa z aplikacją. Aplikacje następnie zarejestrować się na serwerze rejestru usługi i odnajdywać inne mikrousług zależne.

Odczekaj co najmniej dwie minuty, zanim nowo zarejestrowane wystąpienie zacznie odbierać ruch.

Jeśli przeprowadzasz migrację istniejącego rozwiązania opartego na chmurze spring na platformę Azure, upewnij się, że wystąpienia _rejestru usług_ ad hoc i _serwera konfiguracji_ są usuwane (lub wyłączone), aby uniknąć konfliktu z wystąpieniami zarządzanymi dostarczonymi przez usługę Azure Spring Cloud.

Można również sprawdzić dzienniki klienta _rejestru usług_ w usłudze Azure Log Analytics. Aby uzyskać więcej informacji, zobacz [Analizowanie dzienników i metryk z ustawieniami diagnostyki](diagnostic-services.md)

Aby dowiedzieć się więcej o usłudze Azure Log Analytics, zobacz [Wprowadzenie do usługi Log Analytics w usłudze Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal). Kwerenda dzienniki przy użyciu [języka zapytania Kusto](https://docs.microsoft.com/azure/kusto/query/).

### <a name="i-want-to-inspect-my-applications-environment-variables"></a>Chcę sprawdzić zmienne środowiskowe aplikacji

Zmienne środowiskowe informują platformę Azure Spring Cloud, zapewniając, że platforma Azure rozumie, gdzie i jak skonfigurować usługi, które tworzą aplikację. Upewnienie się, że zmienne środowiskowe są poprawne, jest niezbędnym pierwszym krokiem w rozwiązywaniu potencjalnych problemów.  Za pomocą punktu końcowego sprężynowego siłownika rozruchowego można przeglądać zmienne środowiskowe.  

> [!WARNING]
> Ta procedura udostępnia zmienne środowiskowe przy użyciu punktu końcowego testu.  Nie należy postępować, jeśli punkt końcowy testu jest publicznie dostępny lub jeśli przypisano nazwę domeny do aplikacji.

1. Przejdź do pozycji `https://<your application test endpoint>/actuator/health` (Plik > Nowy > Inny).  
    - Odpowiedź podobna `{"status":"UP"}` do wskazuje, że punkt końcowy został włączony.
    - Jeśli odpowiedź jest ujemna, dołącz do pliku *POM.xml* następującą zależność:

        ```xml
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-actuator</artifactId>
            </dependency>
        ```

1. Po włączeniu punktu końcowego siłownika rozruchu sprężynowego przejdź do witryny Azure portal i poszukaj strony konfiguracji aplikacji.  Dodaj zmienną środowiskową `MANAGEMENT_ENDPOINTS_WEB_EXPOSURE_INCLUDE` z `*` nazwą i wartością . 

1. Uruchom ponownie aplikację.

1. Przejdź `https://<your application test endpoint>/actuator/env` do odpowiedzi i sprawdź.  Powinny wyglądać następująco:

    ```json
    {
        "activeProfiles": [],
        "propertySources": {,
            "name": "server.ports",
            "properties": {
                "local.server.port": {
                    "value": 1025
                }
            }
        }
    }
    ```

Poszukaj węzła podrzędnego o nazwie `systemEnvironment`.  Ten węzeł zawiera zmienne środowiskowe aplikacji.

> [!IMPORTANT]
> Pamiętaj, aby odwrócić ekspozycję zmiennych środowiskowych przed udostępnieniem aplikacji publicznie.  Przejdź do witryny Azure portal, poszukaj strony konfiguracji `MANAGEMENT_ENDPOINTS_WEB_EXPOSURE_INCLUDE`aplikacji i usuń tę zmienną środowiskową: .

### <a name="i-cant-find-metrics-or-logs-for-my-application"></a>Nie mogę znaleźć danych ani dzienników dla mojej aplikacji

Przejdź do **zarządzania aplikacjami,** aby upewnić się, że stany aplikacji są _uruchomione_ i _up_.

Jeśli widzisz metryki z _JVM,_ ale nie metryki z `spring-boot-actuator` _Tomcat_, sprawdź, czy zależność jest włączona w pakiecie aplikacji i że pomyślnie uruchamia się.

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

Jeśli dzienniki aplikacji można archiwizować na koncie magazynu, ale nie wysyłać do usługi Azure Log Analytics, sprawdź, czy [obszar roboczy został poprawnie skonfigurowany.](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace) Jeśli używasz bezpłatnej warstwy usługi Azure Log Analytics, należy pamiętać, że [warstwa bezpłatna nie zapewnia umowy dotyczącej poziomu usług (SLA).](https://azure.microsoft.com/support/legal/sla/log-analytics/v1_3/)
