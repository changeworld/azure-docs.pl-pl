---
title: Wdrażanie końcówki Analiza dzienników Azure do monitorowania chmury Foundry | Dokumentacja firmy Microsoft
description: Wskazówki krok po kroku dotyczące wdrażania loggregator Foundry chmury końcówki dla usługi Analiza dzienników Azure. Za pomocą końcówkę monitorować metryki kondycji i wydajności systemu Foundry chmury.
services: virtual-machines-linux
documentationcenter: ''
author: ningk
manager: jeconnoc
editor: ''
tags: Cloud-Foundry
ms.assetid: 00c76c49-3738-494b-b70d-344d8efc0853
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/22/2017
ms.author: ningk
ms.openlocfilehash: 687356b60ad0bbc469d67e071ce3bccc8b61ebd7
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34609005"
---
# <a name="deploy-azure-log-analytics-nozzle-for-cloud-foundry-system-monitoring"></a>Wdrażanie końcówki Analiza dzienników Azure do monitorowania systemu chmury Foundry

[Analiza dzienników Azure](https://azure.microsoft.com/services/log-analytics/) jest usługą platformy Azure. Pomaga zbierania i analizowania danych, które są generowane na podstawie danych z chmury i lokalnych środowiskach.

Końcówkę analizy dziennika (końcówkę) to składnik chmury Foundry (CF), który przesyła dalej metryki z [loggregator Foundry chmury](https://docs.cloudfoundry.org/loggregator/architecture.html) pilnym do analizy dzienników. Z końcówkę można zbieranie, wyświetlanie i analizowanie Twojej CF systemu kondycji i wydajności metryki, w wielu wdrożeń.

W tym dokumencie Dowiedz się jak wdrożyć końcówkę do środowiska CF, a następnie uzyskać dostęp do danych z poziomu konsoli analizy dzienników.

## <a name="prerequisites"></a>Wymagania wstępne

Poniższe kroki są wymagania wstępne dotyczące wdrażania końcówkę.

### <a name="1-deploy-a-cf-or-pivotal-cloud-foundry-environment-in-azure"></a>1. Wdrażanie w środowisku CF lub kluczową Foundry chmurze na platformie Azure

Można użyć końcówkę wdrożenia CF typu open source lub wdrożenia kluczową chmury Foundry (PCF).

* [Wdrażanie Foundry chmurze na platformie Azure](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/blob/master/docs/guidance.md)

* [Wdrażanie Foundry kluczową chmurze na platformie Azure](https://docs.pivotal.io/pivotalcf/1-11/customizing/azure.html)

### <a name="2-install-the-cf-command-line-tools-for-deploying-the-nozzle"></a>2. Zainstaluj narzędzia wiersza polecenia CF wdrażania końcówkę

Końcówkę działa jako aplikacja w środowisku CF. Należy CF interfejsu wiersza polecenia, aby wdrożyć aplikację.

Końcówkę musi też mieć uprawnienia dostępu do pilnym loggregator i kontrolerem chmury. Aby utworzyć i skonfigurować dla użytkownika, należy usługi konta użytkownika i uwierzytelniania (UAA).

* [Zainstaluj Foundry chmury interfejsu wiersza polecenia](https://docs.cloudfoundry.org/cf-cli/install-go-cli.html)

* [Instalowanie klienta wiersza polecenia UAA Foundry chmury](https://github.com/cloudfoundry/cf-uaac/blob/master/README.md)

Przed skonfigurowaniem UAA klient wiersza polecenia, upewnij się, że zainstalowano Rubygems.

### <a name="3-create-a-log-analytics-workspace-in-azure"></a>3. Tworzenie obszaru roboczego analizy dzienników na platformie Azure

Można utworzyć obszaru roboczego analizy dzienników, ręcznie lub za pomocą szablonu. Szablon zostanie wdrożona ustawień wstępnie skonfigurowane widoki OMS kluczowy wskaźnik wydajności i alertów w konsoli OMS. 

#### <a name="to-create-the-workspace-manually"></a>Aby ręcznie utworzyć obszaru roboczego:

1. W portalu Azure Wyszukaj na liście usług w portalu Azure Marketplace, a następnie wybierz analizy dzienników.
2. Wybierz **Utwórz**, a następnie wybierz opcje dla następujących elementów:

   * **Obszar roboczy OMS**: wpisz nazwę obszaru roboczego.
   * **Subskrypcja**: Jeśli masz wiele subskrypcji, wybierz jedną, która jest taka sama jak CF wdrożenia.
   * **Grupa zasobów**: Utwórz nową grupę zasobów lub użyj jednego z wdrożeniem CF.
   * **Lokalizacja**: Wprowadź lokalizację.
   * **Warstwa cenowa**: Wybierz **OK** do wykonania.

Aby uzyskać więcej informacji, zobacz [wprowadzenie do analizy dzienników](https://docs.microsoft.com/azure/log-analytics/log-analytics-get-started).

#### <a name="to-create-the-oms-workspace-through-the-oms-monitoring-template-from-azure-market-place"></a>Aby utworzyć obszar roboczy OMS przy użyciu szablonu monitorowania OMS z handlową platformy Azure:

1. Otwórz Azure portal.
2. Kliknij znak "+" lub "Utwórz zasób" w lewym górnym rogu.
3. Typ "chmury Foundry" w oknie wyszukiwania wybierz "OMS Foundry monitorowania rozwiązanie chmury".
4. Foundry chmury OMS monitorowania strony front szablonu rozwiązanie po załadowaniu, kliknij przycisk Utwórz, aby uruchomić bloku szablonu.
5. Wprowadź wymagane parametry:
    * **Subskrypcja**: Wybierz subskrypcję platformy Azure dla obszaru roboczego OMS, zazwyczaj taki sam jak wdrożenia chmury Foundry.
    * **Grupa zasobów**: Wybierz istniejącą grupę zasobów lub Utwórz nową obszarem roboczym pakietu OMS.
    * **Lokalizacja grupy zasobów**: Wybierz lokalizację, w grupie zasobów.
    * **OMS_Workspace_Name**: Wprowadź nazwę obszaru roboczego, jeśli obszar roboczy nie istnieje, zostanie utworzona przez szablon nowego.
    * **OMS_Workspace_Region**: Wybierz lokalizację, w obszarze roboczym.
    * **OMS_Workspace_Pricing_Tier**: Wybierz obszar roboczy OMS jednostki SKU. Zobacz [cennik wskazówki](https://azure.microsoft.com/pricing/details/log-analytics/) odwołania.
    * **Postanowienia prawne**: kliknij przepisami, następnie kliknij przycisk "Utwórz", aby zaakceptować określenie prawne.
- Po wszystkie parametry są określone, kliknij przycisk Utwórz, aby wdrożyć szablon. Po zakończeniu wdrożenia stan będzie wyświetlany na karcie powiadomienia.


## <a name="deploy-the-nozzle"></a>Wdrażanie końcówkę

Istnieje kilka różnych sposobów wdrażania końcówkę: jako Kafelek PCF lub aplikacji CF.

### <a name="deploy-the-nozzle-as-a-pcf-ops-manager-tile"></a>Wdrażanie końcówkę jako Kafelek PCF Operations Manager

Wykonaj kroki, aby [zainstalować i skonfigurować końcówkę Analiza dzienników Azure dla PCF](http://docs.pivotal.io/partners/azure-log-analytics-nozzle/installing.html). Jest to metoda uproszczona, automatycznie skonfiguruje i push końcówkę PCF Ops kafelka menedżera. 

### <a name="deploy-the-nozzle-manually-as-a-cf-application"></a>Ręczne wdrażanie końcówkę jako aplikacja CF

Jeśli nie używasz PCF Operations Manager, należy wdrożyć końcówkę jako aplikacja. W poniższych sekcjach opisano tego procesu.

#### <a name="sign-in-to-your-cf-deployment-as-an-admin-through-cf-cli"></a>Zaloguj się do wdrożenia CF administratora za pomocą interfejsu wiersza polecenia CF

Uruchom następujące polecenie:
```
cf login -a https://api.${SYSTEM_DOMAIN} -u ${CF_USER} --skip-ssl-validation
```

"SYSTEM_DOMAIN" jest nazwą domeny CF. Można je pobrać przeszukując "SYSTEM_DOMAIN" w pliku manifestu wdrożenia CF. 

"CF_User" jest nazwą CF administratora. Wyszukiwanie w sekcji "scim", wyszukiwania dla nazwy i "cf_admin_password" w pliku manifestu wdrożenia CF można pobrać nazwy i hasła.

#### <a name="create-a-cf-user-and-grant-required-privileges"></a>Utwórz użytkownika CF i udzielić wymaganych uprawnień

Uruchom następujące polecenia:
```
uaac target https://uaa.${SYSTEM_DOMAIN} --skip-ssl-validation
uaac token client get admin
cf create-user ${FIREHOSE_USER} ${FIREHOSE_USER_PASSWORD}
uaac member add cloud_controller.admin ${FIREHOSE_USER}
uaac member add doppler.firehose ${FIREHOSE_USER}
```

"SYSTEM_DOMAIN" jest nazwą domeny CF. Można je pobrać przeszukując "SYSTEM_DOMAIN" w pliku manifestu wdrożenia CF.

#### <a name="download-the-latest-log-analytics-nozzle-release"></a>Pobierz najnowszą wersję końcówki analizy dzienników

Uruchom następujące polecenie:
```
git clone https://github.com/Azure/oms-log-analytics-firehose-nozzle.git
cd oms-log-analytics-firehose-nozzle
```

#### <a name="set-environment-variables"></a>Ustawianie zmiennych środowiskowych

Można teraz ustawić zmienne środowiskowe w pliku manifest.yml w bieżącym katalogu. Poniżej przedstawiono końcówkę manifest aplikacji. Zastąp wartości konkretnych informacji obszaru roboczego analizy dzienników.

```
OMS_WORKSPACE             : Log Analytics workspace ID: open OMS portal from your Log Analytics workspace, select Settings, and select connected sources.
OMS_KEY                   : OMS key: open OMS portal from your Log Analytics workspace, select Settings, and select connected sources.
OMS_POST_TIMEOUT          : HTTP post timeout for sending events to Log Analytics. The default is 10 seconds.
OMS_BATCH_TIME            : Interval for posting a batch to Log Analytics. The default is 10 seconds.
OMS_MAX_MSG_NUM_PER_BATCH : The maximum number of messages in a batch to Log Analytics. The default is 1000.
API_ADDR                  : The API URL of the CF environment. For more information, see the preceding section, "Sign in to your CF deployment as an admin through CF CLI."
DOPPLER_ADDR              : Loggregator's traffic controller URL. For more information, see the preceding section, "Sign in to your CF deployment as an admin through CF CLI."
FIREHOSE_USER             : CF user you created in the preceding section, "Create a CF user and grant required privileges." This user has firehose and Cloud Controller admin access.
FIREHOSE_USER_PASSWORD    : Password of the CF user above.
EVENT_FILTER              : Event types to be filtered out. The format is a comma-separated list. Valid event types are METRIC, LOG, and HTTP.
SKIP_SSL_VALIDATION       : If true, allows insecure connections to the UAA and the traffic controller.
CF_ENVIRONMENT            : Enter any string value for identifying logs and metrics from different CF environments.
IDLE_TIMEOUT              : The Keep Alive duration for the firehose consumer. The default is 60 seconds.
LOG_LEVEL                 : The logging level of the Nozzle. Valid levels are DEBUG, INFO, and ERROR.
LOG_EVENT_COUNT           : If true, the total count of events that the Nozzle has received and sent are logged to Log Analytics as CounterEvents.
LOG_EVENT_COUNT_INTERVAL  : The time interval of the logging event count to Log Analytics. The default is 60 seconds.
```

### <a name="push-the-application-from-your-development-computer"></a>Wypychanie aplikacji na komputerze projektowym

Upewnij się, że jesteś w folderze oms — dziennik analytics — pilnym końcówki. Uruchom następujące polecenie:
```
cf push
```

## <a name="validate-the-nozzle-installation"></a>Sprawdź poprawność instalacji końcówki

### <a name="from-apps-manager-for-pcf"></a>W Menedżerze aplikacji (w przypadku PCF)

1. Zaloguj się do programu Operations Manager i upewnij się, że Kafelek jest wyświetlane na pulpicie nawigacyjnym instalacji.
2. Zaloguj się do Menedżera aplikacji, upewnij się, że miejsca, które utworzono końcówkę znajduje się na raport użycia i upewnij się, że stan to normalne.

### <a name="from-your-development-computer"></a>Na komputerze projektowym

W oknie CF interfejsu wiersza polecenia wpisz:
```
cf apps
```
Upewnij się, że jest uruchomiona aplikacja końcówki OMS.

## <a name="view-the-data-in-the-oms-portal"></a>Wyświetlanie danych w portalu OMS

Jeśli wdrożono OMS monitorowania rozwiązania za pomocą szablonu rynku, przejdź do portalu Azure i znajduje się w zakresie OMS. Rozwiązanie znajduje się w grupie zasobów określone w szablonie. Kliknij przycisk rozwiązaniu, przeglądanie "Konsoli OMS", są wyświetlane wstępnie skonfigurowane widoki, z górnym Foundry chmury systemu kluczowych wskaźników wydajności, dane aplikacji, alertów i metryki kondycji maszyny Wirtualnej. 

Jeśli utworzono obszar roboczy OMS ręcznie, wykonaj poniższe kroki, aby tworzyć widoki oraz alerty:

### <a name="1-import-the-oms-view"></a>1. Zaimportuj widok OMS

W portalu OMS, przejdź do **Widok projektanta** > **importu** > **Przeglądaj**i wybierz jeden z plików omsview. Na przykład wybierz *Foundry.omsview chmury*i Zapisz widoku. Teraz kafelka jest wyświetlany na **omówienie** strony. Wybierz, aby wyświetlić wizualizowanego metryki.

Można dostosować te widoki lub tworzenia nowych widoków przez **Widok projektanta**.

*"Chmury Foundry.omsview"* jest wersja zapoznawcza szablon widoku OMS Foundry chmury. Jest to szablon domyślny w pełni skonfigurowany. Jeśli masz sugestie lub swoją opinię na temat szablonu, wyślij je do [wystawiać sekcji](https://github.com/Azure/oms-log-analytics-firehose-nozzle/issues).

### <a name="2-create-alert-rules"></a>2. Tworzenie reguł alertów

Możesz [tworzyć alerty](https://docs.microsoft.com/azure/log-analytics/log-analytics-alerts)i dostosowywać zapytania i wartości progowe, zgodnie z potrzebami. Zalecane są następujące alerty:

| Zapytanie wyszukiwania                                                                  | Generuj alert w oparciu o | Opis                                                                       |
| ----------------------------------------------------------------------------- | ----------------------- | --------------------------------------------------------------------------------- |
| Type=CF_ValueMetric_CL Origin_s=bbs Name_s="Domain.cf-apps"                   | Liczba wyników < 1   | **BBS. Aplikacje Domain.cf** wskazuje, czy domena aplikacji cf jest aktualny. Oznacza to, że żądania aplikacji CF z kontrolerem chmury są synchronizowane z bbs. LRPsDesired (AIs potrzeby Diego) do wykonania. Żadne dane otrzymane oznacza, że aplikacje cf domeny nie jest aktualny w określone okno czasu. |
| Typ = CF_ValueMetric_CL Origin_s = rep Name_s = UnhealthyCell Value_d > 1            | Liczba wyników > 0   | Dla komórek Diego 0 oznacza, że działa prawidłowo, a 1 oznacza złej kondycji. Wykrycie wielu komórek Diego złej kondycji w określone okno czasu, należy ustawić alert. |
| Type=CF_ValueMetric_CL Origin_s="bosh-hm-forwarder" Name_s="system.healthy" Value_d=0 | Liczba wyników > 0 | 1 oznacza, że system jest w dobrej kondycji, a wartość 0 oznacza, że system nie jest w dobrej kondycji. |
| Typ = CF_ValueMetric_CL Origin_s = route_emitter Name_s = ConsulDownMode Value_d > 0 | Liczba wyników > 0   | Konsul emituje okresowo jej stan kondycji. 0 oznacza, że system jest w dobrej kondycji i 1 oznacza nadajnika trasy wykryje, że Konsul jest wyłączony. |
| Type=CF_CounterEvent_CL Origin_s=DopplerServer (Name_s="TruncatingBuffer.DroppedMessages" or Name_s="doppler.shedEnvelopes") Delta_d>0 | Liczba wyników > 0 | Delta liczba komunikatów porzuconych celowo przez Doppler'a z powodu braku Wstecz. |
| Typ = CF_LogMessage_CL SourceType_s = LGR MessageType_s = błąd                      | Liczba wyników > 0   | Emituje Loggregator **LGR** wskazująca problemy podczas procesu rejestracji. Przykładem takiego problemu jest dane wyjściowe komunikatu dziennika jest za duża. |
| Typ = CF_ValueMetric_CL Name_s = slowConsumerAlert                               | Liczba wyników > 0   | Gdy końcówkę odbiera alert konsumenta wolne od loggregator, wysyła **slowConsumerAlert** ValueMetric do analizy dzienników. |
| Type=CF_CounterEvent_CL Job_s=nozzle Name_s=eventsLost Delta_d>0              | Liczba wyników > 0   | Jeśli delta liczby zdarzeń utracone osiągnie próg, oznacza to, że końcówkę może być problem z uruchomieniem. |

## <a name="scale"></a>Skalowanie

Można też skalować końcówkę oraz loggregator.

### <a name="scale-the-nozzle"></a>Skala końcówkę

Powinny rozpoczynać się co najmniej dwa wystąpienia programu końcówkę. Pilnym dystrybuuje obciążenie we wszystkich wystąpieniach programu końcówkę.
Aby upewnić się, końcówkę może obsługiwać ruch danych z pilnym, skonfiguruj **slowConsumerAlert** alert (wymienione w poprzedniej sekcji "Tworzenie reguły alertu"). Po użytkownik został ostrzeżony, wykonaj [wskazówki dotyczące powolne końcówki](https://docs.pivotal.io/pivotalcf/1-11/loggregator/log-ops-guide.html#slow-noz) ustalenie, czy jest potrzebny skalowania.
Aby skalować końcówkę, należy użyć Menedżera aplikacji lub CF interfejsu wiersza polecenia można zwiększyć liczby wystąpień lub zasobów pamięci lub na dysku dla końcówkę.

### <a name="scale-the-loggregator"></a>Skala loggregator

Wysyła Loggregator **LGR** wiadomości dziennika, który wskazuje problemy podczas procesu rejestracji. Można monitorować alert, aby określić, czy loggregator powinien być skalowane w górę.
Aby skalować loggregator, Zwiększ rozmiar buforu doplerowskiej lub dodać dodatkowy serwer doplerowskiej wystąpień w manifeście CF. Aby uzyskać więcej informacji, zobacz [wskazówek dotyczących skalowania loggregator](https://docs.cloudfoundry.org/running/managing-cf/logging-config.html#scaling).

## <a name="update"></a>Aktualizacja

Aby zaktualizować końcówkę przy użyciu nowszej wersji, pobrać nową wersję końcówki, wykonaj kroki opisane w poprzedniej sekcji "Wdrażanie końcówki" i push ponownie aplikację.

### <a name="remove-the-nozzle-from-ops-manager"></a>Usuń końcówkę z programu Operations Manager

1. Zaloguj się do programu Operations Manager.
2. Zlokalizuj **Microsoft Azure dziennika analizy końcówki dla PCF** kafelka.
3. Wybierz ikonę pamięci i potwierdzić usunięcie.

### <a name="remove-the-nozzle-from-your-development-computer"></a>Usuń końcówkę na komputerze projektowym

W oknie CF interfejsu wiersza polecenia wpisz:
```
cf delete <App Name> -r
```

Jeśli usuniesz końcówkę danych w portalu OMS nie będzie automatycznie usuwane. Wygasa ona zgodnie z ustawieniem przechowywania analizy dzienników.

## <a name="support-and-feedback"></a>Pomoc techniczna i opinie

Azure końcówki analizy dziennika jest otwarta pochodzenia. Wyślij pytania i opinię do [sekcji GitHub](https://github.com/Azure/oms-log-analytics-firehose-nozzle/issues). Otwarcia żądania pomocy technicznej platformy Azure, wybierz "Systemem Foundry chmury maszyny wirtualnej", jak kategoria usług. 

## <a name="next-step"></a>Następny krok

Z PCF2.0 metryki wydajności maszyny Wirtualnej są przenoszone do końcówki Analiza dzienników Azure przez usługę przesyłania dalej systemu metryki oraz zintegrowane w obszarze roboczym pakietu OMS. Nie są już potrzebne agent pakietu OMS dla metryki wydajności maszyny Wirtualnej. Jednak do zbierania informacji Syslog można nadal używać agent pakietu OMS. Agent pakietu OMS jest instalowany jako dodatek Bosh, aby CF maszyn wirtualnych. 

Aby uzyskać więcej informacji, zobacz [wdrożyć agenta pakietu OMS z danym wdrożeniem chmury Foundry](https://github.com/Azure/oms-agent-for-linux-boshrelease).
