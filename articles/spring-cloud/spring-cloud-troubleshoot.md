---
title: Przewodnik rozwiązywania problemów z chmurą wiosenną platformy Azure | Microsoft Docs
description: Przewodnik rozwiązywania problemów z chmurą wiosenną platformy Azure
services: spring-cloud
author: v-vasuke
manager: gwallace
editor: ''
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/07/2019
ms.author: v-vasuke
ms.openlocfilehash: 546c97421fdb3a581a22e34f6110986a1a0732b6
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/25/2019
ms.locfileid: "72929144"
---
# <a name="troubleshooting-guide-for-common-problems"></a>Przewodnik rozwiązywania problemów z typowymi problemami

W tym artykule opisano niektóre typowe problemy i kroki rozwiązywania problemów dla deweloperów pracujących w chmurze wiosennej platformy Azure. Zalecamy także zapoznanie się z naszym [artykułem często zadawanych pytań](spring-cloud-faq.md).

## <a name="availability-performance-and-application-issues"></a>Problemy z dostępnością, wydajnością i aplikacją

### <a name="my-application-cannot-start-for-example-the-endpoint-cannot-be-connected-or-returns-502-after-few-retries"></a>Nie można uruchomić mojej aplikacji (na przykład punkt końcowy nie może być połączony lub zwraca 502 po kilku ponownych próbach)

Wyeksportuj dzienniki do _log Analytics platformy Azure_. Tabela dla dzienników aplikacji Spring ma nazwę `AppPlatformLogsforSpring`. Aby dowiedzieć się więcej, zobacz [Analizowanie dzienników i metryk przy użyciu ustawień diagnostycznych](diagnostic-services.md)

Wyszukiwanie następującego błędu w dziennikach wskazuje jeden z dwóch możliwych problemów:

`org.springframework.context.ApplicationContextException: Unable to start web server`

* Brakuje jednego z ziaren lub jednej z jego zależności.
* Brakuje jednej z właściwości ziarna lub jest ona nieprawidłowa. W takim przypadku prawdopodobnie zobaczysz `java.lang.IllegalArgumentException`.

Powiązania usługi mogą również spowodować błędy uruchomienia aplikacji. Użyj słów kluczowych związanych z powiązanymi usługami do odpytania dzienników.  Na przykład załóżmy, że aplikacja ma powiązanie z wystąpieniem MySQL ustawionym na czas system lokalny. Jeśli uruchomienie aplikacji nie powiedzie się, w dzienniku może wystąpić następujący błąd:

`java.sql.SQLException: The server time zone value 'Coordinated Universal Time' is unrecognized or represents more than one time zone.`

Aby naprawić ten błąd, przejdź do `server parameters` wystąpienia programu MySql i Zmień `time_zone` `SYSTEM` na `+0:00`.


### <a name="my-application-crashes-or-throws-an-unexpected-error"></a>Moja aplikacja ulega awarii lub zgłasza nieoczekiwany błąd

Podczas debugowania awarii aplikacji Zacznij od sprawdzenia stanu uruchomienia i stanu odnajdywania aplikacji. Przejdź do pozycji _Zarządzanie aplikacjami_ w Azure Portal, aby upewnić _się, że_wszystkie aplikacje są _uruchomione i działają_ .

* Jeśli stan jest _uruchomiony_ , ale stan odnajdywania nie jest _ustawiony_, przejdź do [sekcji nie można zarejestrować aplikacji](#my-application-cannot-be-registered).

* Jeśli stan odnajdywania jest _ustawiony_, przejdź do pozycji _metryki_ , aby sprawdzić kondycję aplikacji. Sprawdź następujące metryki:


  - `TomcatErrorCount` (_tomcat. Global. Error_): w tym miejscu będą zliczane wszystkie wyjątki aplikacji wiosennej. Jeśli ta liczba jest duża, przejdź do _usługi Azure log Analytics_ w celu sprawdzenia dzienników aplikacji.

  - `AppMemoryMax` (_JVM. Memory. Max_): Maksymalna ilość pamięci dostępnej dla aplikacji. Jeśli jest zdefiniowany, może być niezdefiniowany lub zmieniony w miarę upływu czasu. Ilość użytej i zatwierdzonej pamięci będzie zawsze mniejsza lub równa wartości maksymalnej, jeśli jest zdefiniowana. Jednak alokacja pamięci może zakończyć się niepowodzeniem z wyjątkiem `OutOfMemoryError` w przypadku próby zwiększenia użytej pamięci w taki sposób, że pamięć użyta będzie większa niż pamięć zatwierdzona, nawet jeśli pamięć użyta będzie mniejsza niż pamięć maksymalna lub jej równa. W takiej sytuacji spróbuj zwiększyć maksymalny rozmiar sterty za pomocą parametru `-Xmx`.

  - `AppMemoryUsed` (_JVM. Memory. użyty_): ilość pamięci w bajtach, która jest obecnie używana przez aplikację. W przypadku aplikacji Java z normalnym obciążeniem ta seria pomiarowa będzie mieć wygląd „zębów piły” — użycie pamięci równomiernie rośnie i spada o niewielkie wartości, a następnie znacznie spada, po czym ten wzorzec powtarza się. Wynika to z faktu, że wyrzucanie elementów bezużytecznych wewnątrz maszyny wirtualnej Java, gdzie akcje kolekcji reprezentują opady na "sawteeth".
    Ta Metryka jest ważna w przypadku identyfikowania problemów z pamięcią, takich jak: * rozłożenie pamięci na początku * przepięcia alokacji pamięci dla określonej ścieżki logicznej * stopniowe przecieki pamięci

  Aby uzyskać więcej informacji, zobacz [metryki](spring-cloud-concept-metrics.md).

Odwiedź [ten artykuł Wprowadzenie](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal) , aby rozpocząć pracę z _usługą Azure log Analytics_.

### <a name="my-application-experiences-high-cpu-usage-or-high-memory-usage"></a>Moja aplikacja używa dużo mocy procesora lub pamięci

Jeśli aplikacja ma duże użycie procesora CPU/pamięci, jedno z dwóch warunków jest prawdziwe:
* Wszystkie wystąpienia aplikacji napotykają duże użycie procesora CPU/pamięci lub
* W niektórych wystąpieniach aplikacji występuje duże użycie procesora CPU/pamięci.

Aby potwierdzić, którą sytuację ma,

1. Przejdź do pozycji _Metryki_ i wybierz pozycję `Service CPU Usage Percentage` lub `Service Memory Used`
2. Dodaj filtr `App=`, aby określić, która aplikacja ma być monitorowana
3. Podziel metryki na `Instance`.

Jeśli wszystkie wystąpienia mają duży procesor CPU/pamięć, należy przeprowadzić skalowanie w poziomie aplikacji lub skalowanie procesora CPU/pamięci. Aby uzyskać więcej informacji, zobacz [skalowanie aplikacji](spring-cloud-tutorial-scale-manual.md)

Jeśli w niektórych wystąpieniach występuje duże użycie procesora CPU/pamięci, sprawdź stan wystąpienia i stan odnajdywania.

Aby uzyskać więcej informacji, odwiedź [metryki](spring-cloud-concept-metrics.md).

Jeśli wszystkie wystąpienia są uruchomione, przejdź do _usługi Azure log Analytics_ , aby wykonać zapytanie dotyczące dzienników aplikacji, i sprawdź logikę kodu, aby sprawdzić, czy którykolwiek z nich może wpływać na partycjonowanie skalowania. Aby uzyskać więcej informacji, zobacz [Analizowanie dzienników i metryk przy użyciu ustawień diagnostycznych](diagnostic-services.md).

Odwiedź [ten artykuł Wprowadzenie](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal) , aby rozpocząć pracę z _usługą Azure log Analytics_. Wykonaj zapytanie dotyczące dzienników przy użyciu [języka zapytań Kusto](https://docs.microsoft.com/azure/kusto/query/).

### <a name="checklist-before-onboarding-your-spring-application-to-azure-spring-cloud"></a>Lista kontrolna przed dołączeniem aplikacji wiosennej do chmury z użyciem platformy Azure

* Aplikacja może działać lokalnie z określoną wersją środowiska uruchomieniowego języka Java.
* Konfiguracja środowiska (procesor/pamięć RAM/wystąpienia) spełnia wymagania minimalne ustawione przez dostawcę aplikacji.
* Elementy konfiguracji mają oczekiwane wartości. Aby uzyskać więcej informacji, zobacz [config Server](spring-cloud-tutorial-config-server.md).
* Zmienne środowiskowe mają oczekiwane wartości.
* Parametry JVM mają oczekiwane wartości.
* Zalecamy wyłączenie/usunięcie z pakietu aplikacji usługi Embedded _Server_ i _sprężynowego rejestru usługi_ .
* Jeśli jakiekolwiek zasoby platformy Azure mają być powiązane za pomocą _powiązania usługi_, upewnij się, że zasoby docelowe zostały uruchomione.

## <a name="configuration-and-management"></a>Konfiguracja i zarządzanie

### <a name="i-encountered-a-problem-creating-an-azure-spring-cloud-service-instance"></a>Wystąpił problem podczas tworzenia wystąpienia usługi w chmurze ze sprężyną Azure

Przy próbie aprowizacji wystąpienia usługi w _chmurze ze sprężyną Azure_ za pośrednictwem portalu, Chmura Wiosenna platformy Azure przeprowadzi weryfikację.

Jednak w przypadku próby aprowizacji wystąpienia usługi w _chmurze Azure wiosny_ za pośrednictwem [interfejsu wiersza polecenia platformy azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) lub [szablonu Menedżer zasobów](https://docs.microsoft.com/azure/azure-resource-manager/)upewnij się, że:

* Subskrypcja jest aktywna.
* Lokalizacja jest [obsługiwana](spring-cloud-faq.md) przez _chmurę wiosenną platformy Azure_.
* Grupa zasobów dla wystąpienia została już utworzona.
* Nazwa zasobu jest zgodna z regułą nazewnictwa. (Może zawierać tylko małe litery, cyfry i łączniki. Pierwszy znak musi być literą. Ostatni znak musi być literą lub cyfrą. Wartość musi mieć długość od 2 do 32 znaków.)

Jeśli spróbujesz udostępnić wystąpienie usługi w _chmurze Azure wiosny_ za pośrednictwem szablonu Menedżer zasobów, odwiedź stronę https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates, aby sprawdzić składnię szablonu.

Nazwa wystąpienia usługi w _chmurze Azure sprężynowego_ zostanie użyta do żądania nazwy domeny podrzędnej w obszarze `azureapps.io`, więc Inicjowanie obsługi zakończy się niepowodzeniem w przypadku konfliktu nazw z istniejącym elementem. Więcej szczegółów można znaleźć w dziennikach aktywności.

### <a name="i-cannot-deploy-a-jar-package"></a>Nie mogę wdrożyć pakietu JAR

Nie można przekazać pakietu JAR/Source przy użyciu szablonu portalu lub Menedżer zasobów.

Po wdrożeniu pakietu aplikacji przy użyciu [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)program okresowo sonduje postęp wdrażania, a na końcu będzie wyświetlał wynik wdrożenia.

Jeśli sondowanie zostanie przerwane, możesz użyć następującego polecenia, aby pobrać dzienniki wdrażania:

`az spring-cloud app show-deploy-log -n <app-name>`

Upewnij się, że aplikacja znajduje się w pakiecie w prawidłowym [formacie wykonywalnego pliku JAR](https://docs.spring.io/spring-boot/docs/current/reference/html/executable-jar.html). Jeśli nie, zostanie wyświetlony błąd podobny do poniższego:

`Error: Invalid or corrupt jarfile /jar/38bc8ea1-a6bb-4736-8e93-e8f3b52c8714`

### <a name="i-cannot-deploy-a-source-package"></a>Nie mogę wdrożyć pakietu źródłowego

Nie można przekazać pakietu JAR/Source przy użyciu szablonu portalu lub Menedżer zasobów.

Podczas wdrażania pakietu aplikacji za pomocą [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) okresowo sonduje on postęp wdrażania, a na końcu wyświetla jego wynik.

Jeśli sondowanie zostanie przerwane, możesz użyć następującego polecenia, aby pobrać dzienniki kompilowania i wdrażania:

`az spring-cloud app show-deploy-log -n <app-name>`

Należy jednak pamiętać, że jedno wystąpienie usługi w _chmurze Azure sprężynowe_ może wyzwolić tylko jedno zadanie kompilacji w jednym z nich. Aby uzyskać więcej informacji, zapoznaj się z tematem [wdrażanie aplikacji](spring-cloud-quickstart-launch-app-portal.md) i [Przewodnik po środowisku przejściowym](spring-cloud-howto-staging-environment.md).

### <a name="my-application-cannot-be-registered"></a>Nie można zarejestrować aplikacji

W większości przypadków dzieje się tak, gdy wymagane zależności/odnajdowanie usług nie są poprawnie skonfigurowane w pliku pliku pom. Po skonfigurowaniu wbudowany punkt końcowy serwera rejestru usługi zostanie dodany jako zmienna środowiskowa do aplikacji. Następnie aplikacje będą się rejestrować na serwerze rejestru usługi i odnajdywać inne zależne mikrousługi.

Odczekaj co najmniej 2 minuty, zanim nowo zarejestrowane wystąpienie zacznie odbierać ruch.

Jeśli przeprowadzasz migrację istniejącego rozwiązania z chmurą w chmurze na platformę Azure, upewnij się, że wystąpienia serwera _rejestru_ i _konfiguracji_ usługi ad hoc zostały usunięte (lub wyłączone), aby uniknąć konfliktu z wystąpieniami zarządzanymi w chmurze z systemem _Azure wiosny_ .

Możesz również sprawdzić dzienniki klienta _usługi_ w usłudze _Azure log Analytics_. Aby uzyskać więcej informacji, zobacz [Analizowanie dzienników i metryk przy użyciu ustawień diagnostycznych](diagnostic-services.md)

Odwiedź [ten artykuł Wprowadzenie](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal) , aby rozpocząć pracę z _usługą Azure log Analytics_. Wykonaj zapytanie dotyczące dzienników przy użyciu [języka zapytań Kusto](https://docs.microsoft.com/azure/kusto/query/).

### <a name="i-want-to-inspect-my-applications-environment-variables"></a>Chcę sprawdzić zmienne środowiskowe aplikacji

Zmienne środowiskowe informują platformę chmurową Azure ze sprężyną, dzięki czemu platforma Azure rozumie, gdzie i jak skonfigurować usługi wchodzące w skład Twojej aplikacji.  Upewnienie się, że zmienne środowiskowe są poprawne, jest to konieczne w pierwszym kroku w rozwiązywaniu potencjalnych problemów.  Aby przejrzeć zmienne środowiskowe, można użyć punktu końcowego uruchamiającego rozruchu sprężynowego.  

> [!WARNING]
> Ta procedura udostępnia zmienne środowiskowe za pomocą punktu końcowego testu.  Nie należy przechodzić, jeśli punkt końcowy testu jest publicznie dostępny lub jeśli przypisano nazwę domeny do aplikacji.

1. Przejdź do tego adresu URL: `https://<your application test endpoint>/actuator/health`.  
    - Odpowiedź podobna do `{"status":"UP"}` wskazuje, że punkt końcowy został włączony.
    - Jeśli odpowiedź jest ujemna, Uwzględnij w `POM.xml` następujący zależność:

        ```xml
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-actuator</artifactId>
            </dependency>
        ```

1. Po włączeniu punktu końcowego uruchamiającego uruchamianie sprężyny przejdź do Azure Portal i Znajdź stronę Konfiguracja aplikacji.  Dodaj zmienną środowiskową o nazwie `MANAGEMENT_ENDPOINTS_WEB_EXPOSURE_INCLUDE` i `*` wartość. 

1. Uruchom ponownie aplikację.

1. Przejdź do `https://<the test endpoint of your app>/actuator/env` i sprawdź odpowiedź.  Powinny wyglądać następująco:

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

Znajdź węzeł podrzędny o nazwie `systemEnvironment`.  Ten węzeł zawiera zmienne środowiskowe aplikacji.

> [!IMPORTANT]
> Pamiętaj, aby wycofać narażenie zmiennych środowiskowych przed udostępnieniem aplikacji publicznie.  Przejdź do Azure Portal, Znajdź stronę konfiguracji aplikacji i Usuń tę zmienną środowiskową: `MANAGEMENT_ENDPOINTS_WEB_EXPOSURE_INCLUDE`.

### <a name="i-cannot-find-metrics-or-logs-for-my-application"></a>Nie mogę odnaleźć metryk ani dzienników dla mojej aplikacji

Przejdź do pozycji _Zarządzanie aplikacjami_ _, aby_upewnić się, że aplikacja jest _uruchomiona i działa_ .

Jeśli są wyświetlane metryki z _JVM_ , ale nie metryki z _Tomcat_, sprawdź, czy w pakiecie aplikacji jest włączona zależność `spring-boot-actuator` i czy rozruch został pomyślnie uruchomiony.

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

Jeśli dzienniki aplikacji można zarchiwizować na koncie magazynu, ale nie są one wysyłane do usługi _Azure Log Analytics_, sprawdź, czy [obszar roboczy został prawidłowo skonfigurowany](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace). W przypadku korzystania z bezpłatnej warstwy _usługi Azure log Analytics_należy pamiętać, że [warstwa Bezpłatna nie zapewnia umowy SLA](https://azure.microsoft.com/support/legal/sla/log-analytics/v1_3/).