---
title: Wdrażanie dyszy usługi Azure Log Analytics do monitorowania odlewnictwa w chmurze
description: Wskazówki krok po kroku dotyczące wdrażania dyszy rejestratora odnajdywania chmury dla usługi Azure Log Analytics. Dysza służy do monitorowania metryk kondycji i wydajności systemu Cloud Foundry.
services: virtual-machines-linux
author: ningk
tags: Cloud-Foundry
ms.assetid: 00c76c49-3738-494b-b70d-344d8efc0853
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/22/2017
ms.author: ningk
ms.openlocfilehash: bf6691310ec964a1d6293f3a60c151e3d6f8e641
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76277361"
---
# <a name="deploy-azure-log-analytics-nozzle-for-cloud-foundry-system-monitoring"></a>Wdrażanie dyszy usługi Azure Log Analytics do monitorowania systemu odnajdowania w chmurze

[Usługa Azure Monitor](https://azure.microsoft.com/services/log-analytics/) to usługa na platformie Azure. Ułatwia zbieranie i analizowanie danych generowanych z chmury i środowisk lokalnych.

Dysza usługi Log Analytics (dysza) jest składnikiem odnajdywacza w chmurze (CF), który przekazuje metryki z [loggregatora firehose odnajdywacza chmury](https://docs.cloudfoundry.org/loggregator/architecture.html) do dzienników usługi Azure Monitor. Dzięki dyszy można zbierać, wyświetlać i analizować metryki kondycji i wydajności systemu CF w wielu wdrożeniach.

W tym dokumencie dowiesz się, jak wdrożyć dyszę w środowisku CF, a następnie uzyskać dostęp do danych z konsoli dzienników usługi Azure Monitor.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Poniższe kroki są warunkami wstępnymi do wdrożenia dyszy.

### <a name="1-deploy-a-cf-or-pivotal-cloud-foundry-environment-in-azure"></a>1. Wdrażanie środowiska cf lub pivotal cloud foundry na platformie Azure

Dyszy można używać z wdrożeniem CF typu open source lub wdrożeniem Pivotal Cloud Foundry (PCF).

* [Usługa Cloud Foundry na platformie Azure](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/blob/master/docs/guidance.md)

* [Wdrażanie odlewnictwa chmury kluczowej na platformie Azure](https://docs.pivotal.io/pivotalcf/1-11/customizing/azure.html)

### <a name="2-install-the-cf-command-line-tools-for-deploying-the-nozzle"></a>2. Zainstaluj narzędzia wiersza polecenia CF do wdrażania dyszy

Dysza działa jako aplikacja w środowisku CF. Do wdrożenia aplikacji potrzebny jest interfejs CF CLI.

Dysza wymaga również uprawnień dostępu do firehose loggregatora i kontrolera chmury. Aby utworzyć i skonfigurować użytkownika, potrzebujesz usługi Konto użytkownika i uwierzytelnianie (UAA).

* [Zainstaluj cli odlewniczej chmury](https://docs.cloudfoundry.org/cf-cli/install-go-cli.html)

* [Zainstaluj klienta wiersza polecenia UAA odlewniczej w chmurze](https://github.com/cloudfoundry/cf-uaac/blob/master/README.md)

Przed skonfigurowaniem klienta wiersza polecenia UAA upewnij się, że rubygems jest zainstalowany.

### <a name="3-create-a-log-analytics-workspace-in-azure"></a>3. Tworzenie obszaru roboczego usługi Log Analytics na platformie Azure

Obszar roboczy usługi Log Analytics można utworzyć ręcznie lub za pomocą szablonu. Szablon wdroży konfigurację wstępnie skonfigurowanych widoków kluczowych wskaźników wydajności i alertów dla konsoli dzienników usługi Azure Monitor. 

#### <a name="to-create-the-workspace-manually"></a>Aby ręcznie utworzyć obszar roboczy:

1. W witrynie Azure portal przeszukaj listę usług w portalu Azure Marketplace, a następnie wybierz obszary robocze usługi Log Analytics.
2. Wybierz **pozycję Utwórz**, a następnie wybierz opcje dla następujących elementów:

   * **Obszar roboczy usługi Log Analytics:** Wpisz nazwę obszaru roboczego.
   * **Subskrypcja:** Jeśli masz wiele subskrypcji, wybierz tę, która jest taka sama jak wdrożenie cf.
   * **Grupa zasobów:** Można utworzyć nową grupę zasobów lub użyć tej samej z wdrożeniem cf.
   * **Lokalizacja**: Wprowadź lokalizację.
   * **Warstwa cenowa:** Wybierz **przycisk OK,** aby zakończyć.

Aby uzyskać więcej informacji, zobacz [Wprowadzenie do dzienników usługi Azure Monitor](https://docs.microsoft.com/azure/log-analytics/log-analytics-get-started).

#### <a name="to-create-the-log-analytics-workspace-through-the-monitoring-template-from-azure-market-place"></a>Aby utworzyć obszar roboczy usługi Log Analytics za pomocą szablonu monitorowania z rynku platformy Azure:

1. Otwórz witrynę Azure Portal.
1. Kliknij znak "+" lub "Utwórz zasób" w lewym górnym rogu.
1. Wpisz "Cloud Foundry" w oknie wyszukiwania, wybierz "Cloud Foundry Monitoring Solution".
1. Zostanie załadowana strona główna szablonu rozwiązania do monitorowania odlewnicza chmury, kliknij przycisk "Utwórz", aby uruchomić blok szablonu.
1. Wprowadź wymagane parametry:
    * **Subskrypcja**: Wybierz subskrypcję platformy Azure dla obszaru roboczego usługi Log Analytics, zwykle taka sama z wdrożeniem odlewniczej chmury.
    * **Grupa zasobów:** Wybierz istniejącą grupę zasobów lub utwórz nową dla obszaru roboczego usługi Log Analytics.
    * **Lokalizacja grupy zasobów:** Wybierz lokalizację grupy zasobów.
    * **OMS_Workspace_Name**: Wprowadź nazwę obszaru roboczego, jeśli obszar roboczy nie istnieje, szablon utworzy nowy.
    * **OMS_Workspace_Region**: Wybierz lokalizację obszaru roboczego.
    * **OMS_Workspace_Pricing_Tier**: Wybierz jednostkę SKU obszaru roboczego usługi Log Analytics. Zapoznaj się ze [wskazówkami dotyczącymi cen](https://azure.microsoft.com/pricing/details/log-analytics/) w celach informacyjnych.
    * **Warunki prawne**: Kliknij pozycję Warunki prawne, a następnie kliknij przycisk "Utwórz", aby zaakceptować termin prawny.
1. Po określeniu wszystkich parametrów kliknij przycisk "Utwórz", aby wdrożyć szablon. Po zakończeniu wdrażania stan pojawi się na karcie powiadomień.


## <a name="deploy-the-nozzle"></a>Wdrażanie dyszy

Istnieje kilka różnych sposobów wdrażania dyszy: jako płytka PCF lub jako aplikacja CF.

### <a name="deploy-the-nozzle-as-a-pcf-ops-manager-tile"></a>Wdrażanie dyszy jako kafelka pcf ops manager

Wykonaj kroki, aby [zainstalować i skonfigurować dyszę usługi Azure Log Analytics dla PCF](https://docs.pivotal.io/partners/azure-log-analytics-nozzle/installing.html). Jest to uproszczone podejście, płytka menedżera PCF Ops automatycznie skonfiguruje i wypchnie dyszę. 

### <a name="deploy-the-nozzle-manually-as-a-cf-application"></a>Rozmieszczaj dyszę ręcznie jako aplikację CF

Jeśli nie używasz Menedżera operacji PCF, należy wdrożyć dyszę jako aplikację. W poniższych sekcjach opisano ten proces.

#### <a name="sign-in-to-your-cf-deployment-as-an-admin-through-cf-cli"></a>Zaloguj się do wdrożenia CF jako administrator za pośrednictwem interfejsu wiersza polecenia CF

Uruchom następujące polecenie:
```
cf login -a https://api.${SYSTEM_DOMAIN} -u ${CF_USER} --skip-ssl-validation
```

"SYSTEM_DOMAIN" to twoja nazwa domeny CF. Można go pobrać, przeszukując "SYSTEM_DOMAIN" w pliku manifestu wdrożenia CF. 

"CF_User" to nazwa administratora CF. Nazwę i hasło można odzyskać, przeszukując sekcję "scim", wyszukując nazwę i "cf_admin_password" w pliku manifestu wdrożenia CF.

#### <a name="create-a-cf-user-and-grant-required-privileges"></a>Tworzenie użytkownika cf i przyznawanie wymaganych uprawnień

Uruchom następujące polecenia:
```
uaac target https://uaa.${SYSTEM_DOMAIN} --skip-ssl-validation
uaac token client get admin
cf create-user ${FIREHOSE_USER} ${FIREHOSE_USER_PASSWORD}
uaac member add cloud_controller.admin ${FIREHOSE_USER}
uaac member add doppler.firehose ${FIREHOSE_USER}
```

"SYSTEM_DOMAIN" to twoja nazwa domeny CF. Można go pobrać, przeszukując "SYSTEM_DOMAIN" w pliku manifestu wdrożenia CF.

#### <a name="download-the-latest-log-analytics-nozzle-release"></a>Pobierz najnowszą wersję dyszy log analytics

Uruchom następujące polecenie:
```
git clone https://github.com/Azure/oms-log-analytics-firehose-nozzle.git
cd oms-log-analytics-firehose-nozzle
```

#### <a name="set-environment-variables"></a>Ustawianie zmiennych środowiskowych

Teraz możesz ustawić zmienne środowiskowe w pliku manifest.yml w bieżącym katalogu. Poniżej przedstawiono manifest aplikacji dla dyszy. Zamień wartości na określone informacje o obszarze roboczym usługi Log Analytics.

```
OMS_WORKSPACE             : Log Analytics workspace ID: Open your Log Analytics workspace in the Azure portal, select **Advanced settings**, select **Connected Sources**, and select **Windows Servers**.
OMS_KEY                   : OMS key: Open your Log Analytics workspace in the Azure portal, select **Advanced settings**, select **Connected Sources**, and select **Windows Servers**.
OMS_POST_TIMEOUT          : HTTP post timeout for sending events to Azure Monitor logs. The default is 10 seconds.
OMS_BATCH_TIME            : Interval for posting a batch to Azure Monitor logs. The default is 10 seconds.
OMS_MAX_MSG_NUM_PER_BATCH : The maximum number of messages in a batch to Azure Monitor logs. The default is 1000.
API_ADDR                  : The API URL of the CF environment. For more information, see the preceding section, "Sign in to your CF deployment as an admin through CF CLI."
DOPPLER_ADDR              : Loggregator's traffic controller URL. For more information, see the preceding section, "Sign in to your CF deployment as an admin through CF CLI."
FIREHOSE_USER             : CF user you created in the preceding section, "Create a CF user and grant required privileges." This user has firehose and Cloud Controller admin access.
FIREHOSE_USER_PASSWORD    : Password of the CF user above.
EVENT_FILTER              : Event types to be filtered out. The format is a comma-separated list. Valid event types are METRIC, LOG, and HTTP.
SKIP_SSL_VALIDATION       : If true, allows insecure connections to the UAA and the traffic controller.
CF_ENVIRONMENT            : Enter any string value for identifying logs and metrics from different CF environments.
IDLE_TIMEOUT              : The Keep Alive duration for the firehose consumer. The default is 60 seconds.
LOG_LEVEL                 : The logging level of the Nozzle. Valid levels are DEBUG, INFO, and ERROR.
LOG_EVENT_COUNT           : If true, the total count of events that the Nozzle has received and sent are logged to Azure Monitor logs as CounterEvents.
LOG_EVENT_COUNT_INTERVAL  : The time interval of the logging event count to Azure Monitor logs. The default is 60 seconds.
```

### <a name="push-the-application-from-your-development-computer"></a>Wypychanie aplikacji z komputera dewelopera

Upewnij się, że znajdujesz się w folderze oms-log-analytics-firehose-dysza. Uruchom następujące polecenie:
```
cf push
```

## <a name="validate-the-nozzle-installation"></a>Sprawdź poprawność instalacji dyszy

### <a name="from-apps-manager-for-pcf"></a>Z Menedżera aplikacji (dla PCF)

1. Zaloguj się do Menedżera operacji i upewnij się, że kafelek jest wyświetlany na pulpicie nawigacyjnym instalacji.
2. Zaloguj się do Menedżera aplikacji, upewnij się, że miejsce utworzone dla dyszy jest wymienione w raporcie użycia i upewnij się, że stan jest normalny.

### <a name="from-your-development-computer"></a>Z komputera dewelopera

W oknie CF CLI wpisz:
```
cf apps
```
Upewnij się, że aplikacja dyszy OMS jest uruchomiona.

## <a name="view-the-data-in-the-azure-portal"></a>Wyświetlanie danych w witrynie Azure portal

Jeśli wdrożono rozwiązanie do monitorowania za pomocą szablonu rynku, przejdź do witryny Azure portal i znajdź rozwiązanie. Rozwiązanie można znaleźć w grupie zasobów określonej w szablonie. Kliknij rozwiązanie, przejdź do "konsoli analizy dzienników", wstępnie skonfigurowane widoki są wymienione, z najlepszych cloud foundry system KPI, dane aplikacji, alerty i metryki kondycji maszyny Wirtualnej. 

Jeśli obszar roboczy usługi Log Analytics został utworzony ręcznie, wykonaj poniższe czynności, aby utworzyć widoki i alerty:

### <a name="1-import-the-oms-view"></a>1. Importowanie widoku OMS

Z portalu oms przejdź do widoku**przeglądaj zaimportuj** > **Browse** **projektanta** > i wybierz jeden z plików omsview. Na przykład wybierz *Cloud Foundry.omsview*i zapisz widok. Teraz kafelek jest wyświetlany na stronie **Przegląd.** Wybierz go, aby wyświetlić zwizualizowane metryki.

Widoki te można dostosować lub utworzyć za pomocą **widoku .**

*"Cloud Foundry.omsview"* jest wersją zapoznawczą szablonu widoku Cloud Foundry OMS. Jest to w pełni skonfigurowany, domyślny szablon. Jeśli masz sugestie lub opinie na temat szablonu, wyślij je do [sekcji problemu](https://github.com/Azure/oms-log-analytics-firehose-nozzle/issues).

### <a name="2-create-alert-rules"></a>2. Tworzenie reguł alertów

Można [utworzyć alerty](https://docs.microsoft.com/azure/log-analytics/log-analytics-alerts)i dostosować zapytania i wartości progowe w razie potrzeby. Następujące alerty są zalecane:

| Zapytanie wyszukiwania                                                                  | Generowanie alertu na podstawie | Opis                                                                       |
| ----------------------------------------------------------------------------- | ----------------------- | --------------------------------------------------------------------------------- |
| Type=CF_ValueMetric_CL Origin_s=bbs Name_s="Domain.cf-apps"                   | Liczba wyników < 1   | **bbs. Domain.cf-apps** wskazuje, czy domena cf-apps jest aktualna. Oznacza to, że żądania aplikacji CF z kontrolera chmury są synchronizowane z bbs. LRPsDesired (Diego-desired AIs) do wykonania. Brak otrzymanych danych oznacza, że domena cf-apps nie jest aktualna w określonym przedziale czasu. |
| Type=CF_ValueMetric_CL Origin_s=rep Name_s=UnhealthyCell Value_d>1            | Liczba wyników > 0   | Dla komórek Diego, 0 oznacza zdrowe, i 1 oznacza niezdrowe. Ustaw alert, jeśli w określonym przedziale czasu zostanie wykrytych wiele komórek Diego w złej kondycji. |
| Type=CF_ValueMetric_CL Origin_s="bosh-hm-forwarder" Name_s="system.healthy" Value_d=0 | Liczba wyników > 0 | 1 oznacza, że system jest zdrowy, a 0 oznacza, że system nie jest zdrowy. |
| Typ=CF_ValueMetric_CL Origin_s=route_emitter Name_s=Value_d konsula>0 | Liczba wyników > 0   | Konsul okresowo emituje swój stan zdrowia. 0 oznacza, że system jest zdrowy, a 1 oznacza, że emiter trasy wykrywa, że konsul jest w dół. |
| Type=CF_CounterEvent_CL Origin_s=DopplerServer (Name_s="TruncatingBuffer.DroppedMessages" lub Name_s="doppler.shedEnvelopes") Delta_d>0 | Liczba wyników > 0 | Delta liczba komunikatów celowo spadła przez Doppler z powodu nacisku wstecznego. |
| Typ=CF_LogMessage_CL SourceType_s=LGR MessageType_s=ERR                      | Liczba wyników > 0   | Loggregator emituje **LGR,** aby wskazać problemy z procesem rejestrowania. Przykładem takiego problemu jest, gdy dane wyjściowe komunikatu dziennika jest zbyt wysoka. |
| Type=CF_ValueMetric_CL Name_s=slowConsumerAlert                               | Liczba wyników > 0   | Gdy dysza odbiera alert powolnego konsumenta z loggregatora, wysyła **slowConsumerAlert** ValueMetric do dzienników usługi Azure Monitor. |
| Typ=CF_CounterEvent_CL Job_s=dysza Name_s=eventsLost Delta_d>0              | Liczba wyników > 0   | Jeśli delta liczba utraconych zdarzeń osiągnie próg, oznacza to, że dysza może mieć problem z uruchomieniem. |

## <a name="scale"></a>Skalowanie

Dyszę i loggregator można skalować.

### <a name="scale-the-nozzle"></a>Skaluj dyszę

Należy rozpocząć od co najmniej dwóch wystąpień dyszy. Firehose rozdziela obciążenie we wszystkich wystąpieniach dyszy.
Aby upewnić się, że dysza może nadążyć za ruchem danych z firehose, skonfiguruj alert **slowConsumerAlert** (wymieniony w powyższej sekcji "Tworzenie reguł alertów"). Po otrzymaniu alertu postępuj zgodnie ze [wskazówkami dotyczącymi powolnej dyszy,](https://docs.pivotal.io/pivotalcf/1-11/loggregator/log-ops-guide.html#slow-noz) aby ustalić, czy jest potrzebne skalowanie.
Aby zwiększyć skalę dyszy, użyj Menedżera aplikacji lub interfejsu wiersza polecenia CF, aby zwiększyć liczbę wystąpień lub zasoby pamięci lub dysku dyszy.

### <a name="scale-the-loggregator"></a>Skalowanie loggregatora

Loggregator wysyła komunikat dziennika **LGR,** aby wskazać problemy z procesem rejestrowania. Można monitorować alert, aby ustalić, czy rejestrator musi być skalowany w górę.
Aby skalować w górę loggregatora, należy zwiększyć rozmiar buforu Dopplera lub dodać dodatkowe wystąpienia serwera Dopplera w manifeście CF. Aby uzyskać więcej informacji, zobacz [wskazówki dotyczące skalowania loggregatora](https://docs.cloudfoundry.org/running/managing-cf/logging-config.html#scaling).

## <a name="update"></a>Aktualizacja

Aby zaktualizować dyszę o nowszą wersję, pobierz nową wersję dyszy, wykonaj kroki opisane w poprzedniej sekcji "Wdrażanie dyszy" i ponownie wypchnij aplikację.

### <a name="remove-the-nozzle-from-ops-manager"></a>Wyjmij dyszę z Menedżera operacji

1. Zaloguj się do Menedżera operacji.
2. Znajdź dyszę usługi Microsoft Azure Log Analytics dla kafelka **PCF.**
3. Wybierz ikonę śmieci i potwierdź usunięcie.

### <a name="remove-the-nozzle-from-your-development-computer"></a>Wyjmij dyszę z komputera dewelopera

W oknie CF CLI wpisz:
```
cf delete <App Name> -r
```

Jeśli usuniesz dyszę, dane w portalu OMS nie zostaną automatycznie usunięte. Wygasa na podstawie ustawienia przechowywania dzienników usługi Azure Monitor.

## <a name="support-and-feedback"></a>Pomoc techniczna i opinie

Dysza usługi Azure Log Analytics jest open source. Wyślij swoje pytania i opinie do [sekcji GitHub](https://github.com/Azure/oms-log-analytics-firehose-nozzle/issues). Aby otworzyć żądanie pomocy technicznej platformy Azure, wybierz opcję "Maszyna wirtualna z odnależeniem w chmurze" jako kategorię usługi. 

## <a name="next-step"></a>Następny krok

Z PCF2.0 metryki wydajności maszyny Wirtualnej są przenoszone do dyszy usługi Azure Log Analytics przez usługę Spedycję metryk systemu i zintegrowane z obszarem roboczym usługi Log Analytics. Nie potrzebujesz już agenta usługi Log Analytics dla metryk wydajności maszyny Wirtualnej. Jednak nadal można użyć agenta usługi Log Analytics do zbierania informacji Syslog. Agent usługi Log Analytics jest zainstalowany jako dodatek Bosh do maszyn wirtualnych CF. 

Aby uzyskać szczegółowe informacje, zobacz [Wdrażanie agenta usługi Log Analytics we wdrożeniu odlewniczej chmury.](https://github.com/Azure/oms-agent-for-linux-boshrelease)
