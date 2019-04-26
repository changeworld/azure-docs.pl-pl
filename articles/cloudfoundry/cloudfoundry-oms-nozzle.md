---
title: Wdrażanie dodatek usługi Azure Log Analytics Nozzle dla usługi Cloud Foundry monitorowania | Dokumentacja firmy Microsoft
description: Wskazówki krok po kroku dotyczące wdrażania usługi Cloud Foundry loggregator dodatek Nozzle dla usługi Azure Log Analytics. Za pomocą dodatku nozzle usługi CF można monitorować metryki kondycji i wydajności systemu Cloud Foundry.
services: virtual-machines-linux
author: ningk
manager: jeconnoc
tags: Cloud-Foundry
ms.assetid: 00c76c49-3738-494b-b70d-344d8efc0853
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/22/2017
ms.author: ningk
ms.openlocfilehash: 6220aebdef6970f3d5f7017e4ae48f6f409ae0ce
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60199401"
---
# <a name="deploy-azure-log-analytics-nozzle-for-cloud-foundry-system-monitoring"></a>Wdrażanie dodatek usługi Azure Log Analytics Nozzle dla monitorowania systemu Cloud Foundry

[Usługa Azure Monitor](https://azure.microsoft.com/services/log-analytics/) to usługa platformy Azure. Pomaga zbierać i analizować dane, która jest generowana z chmury i środowiskach lokalnych.

Dodatek Log Analytics Nozzle (dodatek Nozzle) to składnik Cloud Foundry (CF), która przekazuje metryki [loggregator Cloud Foundry](https://docs.cloudfoundry.org/loggregator/architecture.html) pilnym dzienniki usługi Azure Monitor. Za pomocą dodatku nozzle usługi CF można zbierać, wyświetlanie i analizowanie usługi CF kondycję i wydajność metryki systemu, w ramach wielu wdrożeń będących częścią.

W tym dokumencie dowiesz się, jak wdrożyć dodatek Nozzle dla danego środowiska usługi CF, a następnie uzyskać dostęp do danych z poziomu konsoli Dzienniki usługi Azure Monitor.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Poniższe kroki są wymagania wstępne dotyczące wdrażania dodatku nozzle usługi CF.

### <a name="1-deploy-a-cf-or-pivotal-cloud-foundry-environment-in-azure"></a>1. Wdrożenie w środowisku usługi CF lub Pivotal Cloud Foundry na platformie Azure

Za pomocą dodatku nozzle usługi CF z wdrożenia usługi CF "open source" lub wdrożenia Pivotal Cloud Foundry (PCF).

* [Usługa Cloud Foundry na platformie Azure](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/blob/master/docs/guidance.md)

* [Wdrażanie rozwiązania Pivotal Cloud Foundry na platformie Azure](https://docs.pivotal.io/pivotalcf/1-11/customizing/azure.html)

### <a name="2-install-the-cf-command-line-tools-for-deploying-the-nozzle"></a>2. Zainstaluj narzędzia wiersza polecenia usługi CF wdrażania dodatku nozzle usługi CF

Dodatek Nozzle działa jako aplikacja w środowisku usługi CF. Należy CF interfejsu wiersza polecenia, aby wdrożyć aplikację.

Dodatek Nozzle wymaga również uprawnienie do pilnym loggregator i kontroler chmury. Aby utworzyć i skonfigurować dla użytkownika, niezbędna jest usługa konta użytkownika i uwierzytelniania (UAA).

* [Instalowanie usługi Cloud Foundry interfejsu wiersza polecenia](https://docs.cloudfoundry.org/cf-cli/install-go-cli.html)

* [Zainstaluj klienta wiersza polecenia Cloud Foundry UAA](https://github.com/cloudfoundry/cf-uaac/blob/master/README.md)

Przed skonfigurowaniem UAA klienta wiersza polecenia, upewnij się, że zainstalowano RubyGems.

### <a name="3-create-a-log-analytics-workspace-in-azure"></a>3. Utwórz obszar roboczy usługi Log Analytics na platformie Azure

Obszar roboczy usługi Log Analytics można utworzyć ręcznie lub za pomocą szablonu. Szablon umożliwia wdrożenie ustawień wstępnie skonfigurowane widoki kluczowy wskaźnik wydajności i alertów w konsoli Dzienniki usługi Azure Monitor. 

#### <a name="to-create-the-workspace-manually"></a>Aby ręcznie utworzyć obszar roboczy:

1. W witrynie Azure portal Wyszukaj listę usług w witrynie Azure Marketplace, a następnie wybierz obszary robocze usługi Log Analytics.
2. Wybierz **Utwórz**, a następnie wybierz opcje dla następujących elementów:

   * **Zaloguj się obszar roboczy usługi Analytics**: Wpisz nazwę obszaru roboczego.
   * **Subskrypcja**: Jeśli masz wiele subskrypcji, wybierz jedną, która jest taka sama jak CF wdrożenia.
   * **Grupa zasobów**: Utwórz nową grupę zasobów lub użyj jednego z wdrożeniem usługi CF.
   * **Lokalizacja**: Wprowadź lokalizację.
   * **Warstwa cenowa**: Wybierz **OK** do ukończenia.

Aby uzyskać więcej informacji, zobacz [Rozpoczynanie pracy z usługą Azure Monitor dzienniki](https://docs.microsoft.com/azure/log-analytics/log-analytics-get-started).

#### <a name="to-create-the-log-analytics-workspace-through-the-monitoring-template-from-azure-market-place"></a>Aby utworzyć obszar roboczy usługi Log Analytics przy użyciu szablonu monitorowania z platformę handlową platformy Azure:

1. Otwórz witrynę Azure portal.
1. Kliknij znak "+" lub "Utwórz zasób" w lewym górnym rogu.
1. W oknie wyszukiwania wpisz "Cloud Foundry", wybierz pozycję "Cloud Foundry rozwiązanie do monitorowania".
1. Cloud Foundry monitorowania strony szablon rozwiązania jest załadowany, kliknij przycisk "Utwórz", aby uruchomić bloku szablonu.
1. Wprowadź wymagane parametry:
    * **Subskrypcja**: Wybierz subskrypcję platformy Azure dla obszaru roboczego usługi Log Analytics, zazwyczaj taki sam jak wdrażanie rozwiązania Cloud Foundry.
    * **Grupa zasobów**: Wybierz istniejącą grupę zasobów lub Utwórz nową grupę dla obszaru roboczego usługi Log Analytics.
    * **Lokalizacja grupy zasobów**: Wybierz lokalizację grupy zasobów.
    * **OMS_Workspace_Name**: Wprowadź nazwę obszaru roboczego, jeśli obszar roboczy nie istnieje, ten szablon utworzy nową.
    * **OMS_Workspace_Region**: Wybierz lokalizację dla obszaru roboczego.
    * **OMS_Workspace_Pricing_Tier**: Wybierz obszar roboczy usługi Log Analytics jednostki SKU. Zobacz [wskazówki dotyczące cen](https://azure.microsoft.com/pricing/details/log-analytics/) dla odwołania.
    * **Postanowienia prawne**: Postanowienia prawne, a następnie kliknij przycisk "Utwórz", aby zaakceptować określenie prawne.
1. Po wszystkie parametry są określone, kliknij przycisk "Utwórz", aby wdrożyć szablon. Po zakończeniu wdrożenia stan będzie wyświetlany na karcie powiadomienia.


## <a name="deploy-the-nozzle"></a>Wdrażanie dodatku nozzle usługi CF

Istnieje kilka różnych sposobów wdrażania dodatku nozzle usługi CF: jako Kafelek PCF lub aplikacji usługi CF.

### <a name="deploy-the-nozzle-as-a-pcf-ops-manager-tile"></a>Wdrażanie dodatku nozzle usługi CF jako rozwiązanie PCF menedżera operacji

Postępuj zgodnie z instrukcjami, aby [zainstalować i skonfigurować dodatek usługi Azure Log Analytics Nozzle dla PCF](https://docs.pivotal.io/partners/azure-log-analytics-nozzle/installing.html). Jest to uproszczone podejście, Kafelek Menedżer PCF Ops automatycznie skonfiguruje i Wypchnij dodatku nozzle usługi CF. 

### <a name="deploy-the-nozzle-manually-as-a-cf-application"></a>Ręczne wdrażanie dodatku nozzle usługi CF jako aplikacji usługi CF

Jeśli nie używasz menedżera operacji PCF, należy wdrożyć dodatku nozzle usługi CF jako aplikacja. Ten proces można znaleźć w poniższych sekcjach.

#### <a name="sign-in-to-your-cf-deployment-as-an-admin-through-cf-cli"></a>Zaloguj się do wdrożenia usługi CF jako administrator przy użyciu interfejsu wiersza polecenia usługi CF

Uruchom następujące polecenie:
```
cf login -a https://api.${SYSTEM_DOMAIN} -u ${CF_USER} --skip-ssl-validation
```

"SYSTEM_DOMAIN" to nazwa domeny CF. Można je pobrać, wyszukując "SYSTEM_DOMAIN" w pliku manifestu wdrożenia CF. 

"CF_User" jest CF nazwę administratora. Można pobrać nazwy i hasła, wyszukiwanie w sekcji "Standard scim", wyszukiwania dla nazwy i "cf_admin_password" w pliku manifestu wdrożenia CF.

#### <a name="create-a-cf-user-and-grant-required-privileges"></a>Utwórz użytkownika usługi CF i przyznać wymaganych uprawnień

Uruchom następujące polecenia:
```
uaac target https://uaa.${SYSTEM_DOMAIN} --skip-ssl-validation
uaac token client get admin
cf create-user ${FIREHOSE_USER} ${FIREHOSE_USER_PASSWORD}
uaac member add cloud_controller.admin ${FIREHOSE_USER}
uaac member add doppler.firehose ${FIREHOSE_USER}
```

"SYSTEM_DOMAIN" to nazwa domeny CF. Można je pobrać, wyszukując "SYSTEM_DOMAIN" w pliku manifestu wdrożenia CF.

#### <a name="download-the-latest-log-analytics-nozzle-release"></a>Pobieranie najnowszej wersji dodatek Log Analytics Nozzle

Uruchom następujące polecenie:
```
git clone https://github.com/Azure/oms-log-analytics-firehose-nozzle.git
cd oms-log-analytics-firehose-nozzle
```

#### <a name="set-environment-variables"></a>Ustawianie zmiennych środowiskowych

Teraz możesz ustawić zmienne środowiskowe w pliku manifest.yml w bieżącym katalogu. Na poniższym obrazie przedstawiono manifest aplikacji dla dodatku nozzle usługi CF. Zastąp wartości konkretnych informacji obszaru roboczego usługi Log Analytics.

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

Upewnij się, czy w folderze pakietu oms — dodatek log-analytics pilnym nozzle. Uruchom następujące polecenie:
```
cf push
```

## <a name="validate-the-nozzle-installation"></a>Zweryfikuj instalację dodatku nozzle usługi CF

### <a name="from-apps-manager-for-pcf"></a>Z aplikacji Menedżera (PCF)

1. Zaloguj się do menedżera operacji i upewnij się, że Kafelek jest wyświetlany na pulpicie nawigacyjnym instalacji.
2. Zaloguj się do Menedżera aplikacji, upewnij się, że miejsce, które zostały utworzone dla dodatku nozzle usługi CF znajduje się w raporcie użycia i upewnij się, że jego kondycja jest normalne.

### <a name="from-your-development-computer"></a>Z komputera dewelopera

W oknie CF interfejsu wiersza polecenia wpisz:
```
cf apps
```
Upewnij się, że aplikacja dodatku OMS nozzle usługi CF jest uruchomiona.

## <a name="view-the-data-in-the-azure-portal"></a>Wyświetlanie danych w witrynie Azure portal

Jeśli wdrożono rozwiązanie do monitorowania przy użyciu szablonu portalu Marketplace, przejdź do witryny Azure portal i Znajdź rozwiązania. Rozwiązanie można znaleźć w grupie zasobów, które określiłeś w szablonie. Kliknij przycisk rozwiązania, przejdź do "log analytics Konsola", wstępnie skonfigurowane widoki są wyświetlane, najważniejsze usługi Cloud Foundry system kluczowe wskaźniki wydajności, dane aplikacji, alerty i metryki kondycji maszyny Wirtualnej. 

Jeśli po utworzeniu obszaru roboczego usługi Log Analytics ręcznie, wykonaj poniższe kroki, aby tworzyć widoki i alerty:

### <a name="1-import-the-oms-view"></a>1. Zaimportuj widok pakietu OMS

Z poziomu portalu pakietu OMS, przejdź do **Projektant widoków** > **importu** > **Przeglądaj**i wybierz jeden z plików omsview. Na przykład wybierz *Foundry.omsview chmury*i Zapisz widoku. Teraz Kafelek jest wyświetlany na **Przegląd** strony. Wybierz ją, aby zobaczyć metryki zwizualizowanego.

Można dostosować tych widoków lub tworzenie nowych widoków przez **Projektant widoków**.

*"Chmura Foundry.omsview"* jest szablon widoku Cloud Foundry OMS wersję zapoznawczą. To jest w pełni skonfigurowany szablon domyślny. Jeśli masz sugestie lub opinie na temat szablonu, wyślij je do [wystawiać sekcji](https://github.com/Azure/oms-log-analytics-firehose-nozzle/issues).

### <a name="2-create-alert-rules"></a>2. Tworzenie reguł alertów

Możesz [Tworzenie alertów](https://docs.microsoft.com/azure/log-analytics/log-analytics-alerts)i dostosować zapytania i wartości progowe, zgodnie z potrzebami. Zaleca się następujące alerty:

| Zapytanie wyszukiwania                                                                  | Generuj alert w oparciu o | Opis                                                                       |
| ----------------------------------------------------------------------------- | ----------------------- | --------------------------------------------------------------------------------- |
| Type=CF_ValueMetric_CL Origin_s=bbs Name_s="Domain.cf-apps"                   | Liczba wyników < 1   | **BBS. Aplikacje Domain.cf** wskazuje, czy domena aplikacji usługi cf jest aktualny. Oznacza to, że żądania aplikacji usługi CF z kontrolera chmury są synchronizowane bbs. LRPsDesired (AIs żądanego Diego) do wykonania. Nie odebrano żadnych danych oznacza, że domena aplikacji usługi cf nie jest aktualny w określonym przedziale czasu. |
| Type=CF_ValueMetric_CL Origin_s=rep Name_s=UnhealthyCell Value_d>1            | Liczba wyników > 0   | W przypadku komórek Diego 0 oznacza, że działa prawidłowo, a 1 oznacza złej kondycji. Ustaw alert w przypadku wykrycia wielu komórek Diego nieprawidłowości w określonym przedziale czasu. |
| Type=CF_ValueMetric_CL Origin_s="bosh-hm-forwarder" Name_s="system.healthy" Value_d=0 | Liczba wyników > 0 | 1 oznacza, że system jest w dobrej kondycji, a wartość 0 oznacza, że system nie jest w dobrej kondycji. |
| Type=CF_ValueMetric_CL Origin_s=route_emitter Name_s=ConsulDownMode Value_d>0 | Liczba wyników > 0   | Konsul emituje jej stan kondycji okresowo. 0 oznacza, że system jest w dobrej kondycji i 1 oznacza, że nadajnika trasy wykryje, że Konsul nie działa. |
| Type=CF_CounterEvent_CL Origin_s=DopplerServer (Name_s="TruncatingBuffer.DroppedMessages" or Name_s="doppler.shedEnvelopes") Delta_d>0 | Liczba wyników > 0 | Różnicowe liczba wiadomości, które celowo przez Doppler'a porzucony w efekcie zastosowania ciśnienia. |
| Type=CF_LogMessage_CL SourceType_s=LGR MessageType_s=ERR                      | Liczba wyników > 0   | Emituje Loggregator **LGR** do wskazania problemów z procesem rejestrowania. Przykładem takiego problemu jest, gdy dane wyjściowe komunikatu dziennika jest zbyt wysoka. |
| Type=CF_ValueMetric_CL Name_s=slowConsumerAlert                               | Liczba wyników > 0   | Gdy dodatek Nozzle otrzymuje alert konsumenta wolne od loggregator, wysyła **slowConsumerAlert** dzienniki ValueMetric do usługi Azure Monitor. |
| Type=CF_CounterEvent_CL Job_s=nozzle Name_s=eventsLost Delta_d>0              | Liczba wyników > 0   | Jeśli liczba zmian zdarzenia utracone osiąga próg, oznacza to, że dodatek Nozzle może być problem z uruchomieniem. |

## <a name="scale"></a>Skalowanie

Możesz skalować dodatek Nozzle i loggregator.

### <a name="scale-the-nozzle"></a>Skalowanie dodatku nozzle usługi CF

Co najmniej dwa wystąpienia dodatku nozzle usługi CF powinien zaczynać. Pilnym rozkłada obciążenie na wszystkie wystąpienia dodatku nozzle usługi CF.
Aby upewnić się, że dodatek Nozzle może nadążyć za ruch danych od pilnym, skonfigurować **slowConsumerAlert** alertu (wymienione w poprzedniej sekcji "Tworzenie reguły alertu"). Po użytkownik został alerty, postępuj zgodnie z [wskazówki dotyczące dodatku powolne nozzle usługi CF](https://docs.pivotal.io/pivotalcf/1-11/loggregator/log-ops-guide.html#slow-noz) do określenia, czy skalowanie jest wymagana.
Skalowanie w górę dodatku nozzle usługi CF, użyj Menedżera aplikacji lub interfejsu wiersza polecenia usługi CF, zwiększenie liczby wystąpień lub zasoby pamięci lub na dysku dla dodatku nozzle usługi CF.

### <a name="scale-the-loggregator"></a>Skala loggregator

Wysyła Loggregator **LGR** komunikatu dziennika w celu wskazania problemów z procesem rejestrowania. Możesz monitorować ten alert, aby ustalić, czy loggregator musi być skalowana w górę.
Skalowanie w górę loggregator, zwiększyć rozmiar buforu doplerowskiej albo dodać dodatkowego serwera doplerowskiej wystąpienia w manifeście usługi CF. Aby uzyskać więcej informacji, zobacz [wskazówek dotyczących skalowania loggregator](https://docs.cloudfoundry.org/running/managing-cf/logging-config.html#scaling).

## <a name="update"></a>Aktualizacja

Aby zaktualizować dodatek Nozzle przy użyciu nowszej wersji, pobieranie nowej wersji dodatku nozzle usługi CF, postępuj zgodnie z instrukcjami w poprzedniej sekcji "Wdrażanie końcówki" i wypchnąć ponownie aplikację.

### <a name="remove-the-nozzle-from-ops-manager"></a>Usuń dodatek Nozzle z menedżera operacji

1. Zaloguj się do menedżera operacji.
2. Znajdź **dodatek Microsoft Azure Log Analytics Nozzle dla PCF** kafelka.
3. Wybierz ikonę wyrzucania elementów, a następnie Potwierdź usunięcie.

### <a name="remove-the-nozzle-from-your-development-computer"></a>Usuń dodatek Nozzle z komputera dewelopera

W oknie CF interfejsu wiersza polecenia wpisz:
```
cf delete <App Name> -r
```

Jeśli usuniesz dodatek Nozzle, dane w portalu pakietu OMS nie zostanie automatycznie usunięty. Wygasa w oparciu o ustawienia przechowywania dzienników usługi Azure Monitor.

## <a name="support-and-feedback"></a>Pomoc techniczna i opinie

Dodatek usługi Azure Log Analytics Nozzle jest "open source". Wyślij swoje pytania i opinie, aby [sekcji GitHub](https://github.com/Azure/oms-log-analytics-firehose-nozzle/issues). Otwarcia żądania pomocy technicznej platformy Azure, wybierz pozycję "Maszyny wirtualnej z usługą Cloud Foundry" jak kategoria usług. 

## <a name="next-step"></a>Następny krok

Z PCF2.0 metryki wydajności maszyny Wirtualnej są przenoszone do dodatek usługi Azure Log Analytics nozzle przez System metryki usługi przesyłania dalej i zintegrowany obszar roboczy usługi Log Analytics. Nie potrzebujesz już agenta usługi Log Analytics dla metryki wydajności maszyny Wirtualnej. Można jednak nadal używać agenta usługi Log Analytics do zbierania informacji dziennika systemowego. Jako dodatek Bosh, do maszyn wirtualnych CF zainstalowano agenta usługi Log Analytics. 

Aby uzyskać więcej informacji, zobacz [agenta wdrażania usługi Log Analytics do wdrożenia usługi Cloud Foundry](https://github.com/Azure/oms-agent-for-linux-boshrelease).
