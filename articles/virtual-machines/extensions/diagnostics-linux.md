---
title: Obliczeń platformy Azure — rozszerzenie Diagnostyka systemu Linux | Dokumentacja firmy Microsoft
description: Jak skonfigurować Azure Linux diagnostycznych rozszerzenia (LAD) do zbierania metryk i rejestrowania zdarzeń z maszyn wirtualnych systemu Linux działających na platformie Azure.
services: virtual-machines-linux
author: abhijeetgaiha
manager: sankalpsoni
ms.service: virtual-machines-linux
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 05/09/2017
ms.author: agaiha
ms.openlocfilehash: 8ffa9823000efbb101be73397cd0025f9933cecd
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34652648"
---
# <a name="use-linux-diagnostic-extension-to-monitor-metrics-and-logs"></a>Rozszerzenie diagnostycznych Linux służy do monitorowania, metryki i dzienniki

W tym dokumencie opisano w wersji 3.0 oraz nowszych rozszerzenia diagnostyczne systemu Linux.

> [!IMPORTANT]
> Informacje o wersji 2.3 i starsze, zobacz [tego dokumentu](../linux/classic/diagnostic-extension-v2.md).

## <a name="introduction"></a>Wprowadzenie

Rozszerzenie diagnostycznych Linux pomaga użytkownika monitora kondycji maszyny Wirtualnej systemu Linux uruchomiony w systemie Microsoft Azure. Ma ona następujące możliwości:

* Zbiera metryki wydajności systemu z maszyny Wirtualnej i zapisuje je w określonej tabeli w ramach konta magazynu wyznaczonego.
* Pobiera zdarzenia dziennika z syslog i przechowuje je w określonej tabeli w ramach konta magazynu wyznaczonego.
* Umożliwia dostosowanie metryk dane są zbierane i przekazać.
* Umożliwia dostosowanie urządzeń syslog i poziomy ważności zdarzeń, które są zbierane i przekazać.
* Umożliwia użytkownikom na przekazywanie do tabeli magazynu wyznaczonego określone pliki dziennika.
* Obsługuje wysyłanie metryki i dziennik zdarzeń do dowolnego EventHub punktów końcowych i formacie JSON obiekty BLOB na koncie magazynu wyznaczonego.

To rozszerzenie współpracuje z obu modeli wdrażania platformy Azure.

## <a name="installing-the-extension-in-your-vm"></a>Instalowanie rozszerzenia w maszynie Wirtualnej

To rozszerzenie można włączyć za pomocą poleceń cmdlet programu Azure PowerShell, skryptów wiersza polecenia platformy Azure lub szablony wdrożenia usługi Azure. Aby uzyskać więcej informacji, zobacz [funkcji rozszerzenia](features-linux.md).

Azure portal nie można włączyć lub skonfigurować LAD 3.0. Zamiast tego instaluje i konfiguruje wersji 2.3. Wykresy portalu Azure i alerty pracować z danymi obie wersje rozszerzenia.

Te instrukcje instalacji i [do pobrania Przykładowa konfiguracja](https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json) LAD 3.0 można skonfigurować:

* przechwycenie i zapisanie te same metryki, ponieważ dostarczonych przez LAD 2.3;
* Przechwyć zbiór przydatne metryki systemu plików, nowe 3.0 LAD;
* Przechwytywanie domyślnej kolekcji syslog włączane przez LAD 2.3;
* Włącz portalu Azure obsługi wykresów i alerty na metryki maszyny Wirtualnej.

Do pobrania konfiguracji jest tylko przykładowe; Zmodyfikuj go do własnych potrzeb.

### <a name="prerequisites"></a>Wymagania wstępne

* **Agenta systemu Linux platformy Azure w wersji 2.2.0 lub nowszym**. Większość obrazów Galeria Linux maszyny Wirtualnej Azure zawierają wersję 2.2.7 lub nowszym. Uruchom `/usr/sbin/waagent -version` o potwierdzenie wersja zainstalowana na maszynie Wirtualnej. Jeśli maszyna wirtualna działa starszą wersję agenta gościa, należy wykonać [tych instrukcji](https://docs.microsoft.com/azure/virtual-machines/linux/update-agent) go zaktualizować.
* **Interfejs wiersza polecenia platformy Azure**. [Konfigurowanie programu Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) środowiska na tym komputerze.
* Polecenie wget, jeśli nie masz jeszcze go: Uruchom `sudo apt-get install wget`.
* Istniejącą subskrypcję platformy Azure i istniejące konto magazynu w niej do przechowywania danych.

### <a name="sample-installation"></a>Przykładowe instalacji

Podaj prawidłowe parametry w pierwszych trzech wierszy, a następnie wykonaj ten skrypt jako katalogu głównego:

```bash
# Set your Azure VM diagnostic parameters correctly below
my_resource_group=<your_azure_resource_group_name_containing_your_azure_linux_vm>
my_linux_vm=<your_azure_linux_vm_name>
my_diagnostic_storage_account=<your_azure_storage_account_for_storing_vm_diagnostic_data>

# Should login to Azure first before anything else
az login

# Select the subscription containing the storage account
az account set --subscription <your_azure_subscription_id>

# Download the sample Public settings. (You could also use curl or any web browser)
wget https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json -O portal_public_settings.json

# Build the VM resource ID. Replace storage account name and resource ID in the public settings.
my_vm_resource_id=$(az vm show -g $my_resource_group -n $my_linux_vm --query "id" -o tsv)
sed -i "s#__DIAGNOSTIC_STORAGE_ACCOUNT__#$my_diagnostic_storage_account#g" portal_public_settings.json
sed -i "s#__VM_RESOURCE_ID__#$my_vm_resource_id#g" portal_public_settings.json

# Build the protected settings (storage account SAS token)
my_diagnostic_storage_account_sastoken=$(az storage account generate-sas --account-name $my_diagnostic_storage_account --expiry 2037-12-31T23:59:00Z --permissions wlacu --resource-types co --services bt -o tsv)
my_lad_protected_settings="{'storageAccountName': '$my_diagnostic_storage_account', 'storageAccountSasToken': '$my_diagnostic_storage_account_sastoken'}"

# Finallly tell Azure to install and enable the extension
az vm extension set --publisher Microsoft.Azure.Diagnostics --name LinuxDiagnostic --version 3.0 --resource-group $my_resource_group --vm-name $my_linux_vm --protected-settings "${my_lad_protected_settings}" --settings portal_public_settings.json
```

Adres URL Przykładowa konfiguracja i jego zawartość, mogą ulec zmianie. Pobierz kopię pliku JSON ustawienia portalu i dostosować go do potrzeb. Wszystkie szablony lub automatyzacji, który można skonstruować należy używać własną kopię zamiast pobierania zawsze tego adresu URL.

### <a name="updating-the-extension-settings"></a>Aktualizowanie ustawień rozszerzenia

Po zmianie ustawienia publiczne lub chronione, na których je wdrożyć na maszynie wirtualnej za pomocą tego samego polecenia. Jeśli jakieś zmiany w ustawieniach, zaktualizowano ustawienia są wysyłane do rozszerzenia. LAD ponowne załadowanie konfiguracji i uruchamia się ponownie.

### <a name="migration-from-previous-versions-of-the-extension"></a>Migracja z poprzednich wersji rozszerzenia

Najnowsza wersja rozszerzenia **3.0**. **Wszystkie starsze wersje (2.x) są przestarzałe i mogą być publikowane na wcześniejsze niż 31 lipca 2018**.

> [!IMPORTANT]
> To rozszerzenie wprowadzono istotne zmiany w konfiguracji rozszerzenia. Taka zmiana została wprowadzona w celu zwiększenia bezpieczeństwa rozszerzenia. w związku z tym zapewnienia zgodności z 2.x nie można wykonać konserwacji. Ponadto wydawcy rozszerzenia dla tego rozszerzenia jest inny niż wydawcy dla wersji 2.x.
>
> Aby przeprowadzić migrację z 2.x do nowej wersji rozszerzenia, należy odinstalować stary rozszerzenia (pod starą nazwę wydawcy), a następnie zainstaluj rozszerzenie w wersji 3.

Zalecenia:

* Uaktualnianie wersji pomocniczej automatyczne włączone, należy zainstalować rozszerzenie.
  * W klasycznym modelu wdrażania maszyn wirtualnych należy określić "3.*" jako wersja instalowania rozszerzenia za pomocą interfejsu wiersza polecenia XPLAT platformy Azure lub programu Powershell.
  * We wdrożeniu usługi Azure Resource Manager modelu maszyn wirtualnych, obejmują ""autoUpgradeMinorVersion": true" w szablonie wdrożenia maszyny Wirtualnej.
* Użyj nowego/innego konta magazynu dla LAD 3.0. Istnieje kilka małych niezgodności między LAD 2.3 i LAD 3.0 powodujących, że udostępnianie powodującymi konta:
  * LAD 3.0 przechowuje zdarzenia dziennika systemowego w tabeli z inną nazwą.
  * CounterSpecifier ciągów dla `builtin` metryki różnią się w LAD 3.0.

## <a name="protected-settings"></a>Ustawienia chronionego

Ten zestaw informacji o konfiguracji zawiera poufne informacje, które powinny być chronione w widoku publicznych, na przykład poświadczenia magazynu. Te ustawienia są przesyłane do i przechowywane przez rozszerzenie w postaci zaszyfrowanej.

```json
{
    "storageAccountName" : "the storage account to receive data",
    "storageAccountEndPoint": "the hostname suffix for the cloud for this account",
    "storageAccountSasToken": "SAS access token",
    "mdsdHttpProxy": "HTTP proxy settings",
    "sinksConfig": { ... }
}
```

Name (Nazwa) | Wartość
---- | -----
storageAccountName | Nazwa konta magazynu, w którym dane są zapisywane przez rozszerzenie.
storageAccountEndPoint | (opcjonalnie) Punkt końcowy identyfikowanie chmury, w której istnieje konto magazynu. Jeśli to ustawienie jest nieobecne, LAD domyślnie chmurze publicznej Azure `https://core.windows.net`. Aby korzystać z konta magazynu platformy Azure w Niemczech, Azure dla instytucji rządowych lub chińskiej wersji platformy Azure, w związku z tym Ustaw tę wartość.
storageAccountSasToken | [Tokenu sygnatury dostępu Współdzielonego konta](https://azure.microsoft.com/blog/sas-update-account-sas-now-supports-all-storage-services/) usług obiektów Blob i tabeli (`ss='bt'`), mające zastosowanie do kontenerów i obiektów (`srt='co'`), która przyznaje dodać, tworzenie listy, aktualizacji i uprawnienia do zapisu (`sp='acluw'`). Czy *nie* zawierać wiodących znak zapytania (?).
mdsdHttpProxy | (opcjonalnie) Wymagane do włączenia rozszerzenia do nawiązania połączenia z określonego konta magazynu i punktu końcowego informacji serwera proxy HTTP.
sinksConfig | (opcjonalnie) Szczegóły dotyczące alternatywnych miejsc docelowych, do których mogą być dostarczane metryk i zdarzeń. W poniższych sekcjach opisano konkretne szczegółowe informacje o każdym ujścia danych obsługiwane przez rozszerzenie.


> [!NOTE]
> W przypadku wdrażania rozszerzeń z szablonem wdrożenia usługi Azure, konta magazynu i tokenu sygnatury dostępu Współdzielonego musi wcześniej utworzony i następnie przekazywane do szablonu. Nie można wdrożyć maszyny Wirtualnej, konta magazynu i skonfigurować rozszerzenia w jednym szablonie. Tworzenie tokenu sygnatury dostępu Współdzielonego, w ramach szablonu nie jest obecnie obsługiwane.

Można łatwo utworzyć wymagany token sygnatury dostępu Współdzielonego za pośrednictwem portalu Azure.

1. Wybierz konto magazynu ogólnego przeznaczenia, do którego mają rozszerzenie do zapisu
1. Wybierz opcję "Udostępniania sygnatury dostępu" z części ustawień menu po lewej stronie
1. Wprowadź odpowiednie części, jak opisano wcześniej
1. Kliknij przycisk "Generowanie sygnatury dostępu Współdzielonego".

![Obraz](./media/diagnostics-linux/make_sas.png)

Skopiuj wygenerowany SAS do pola storageAccountSasToken; Usuń znak znaku zapytania ("?").

### <a name="sinksconfig"></a>sinksConfig

```json
"sinksConfig": {
    "sink": [
        {
            "name": "sinkname",
            "type": "sinktype",
            ...
        },
        ...
    ]
},
```

Ta sekcja opcjonalna definiuje dodatkowe miejsc docelowych, do których rozszerzenie wysyła zebrane informacje. Tablica "sink" zawiera obiekt dla każdego obiektu sink dodatkowe dane. Atrybut "type" Określa inne atrybuty w obiekcie.

Element | Wartość
------- | -----
name | Ciąg używany do odwoływania się do tego obiektu sink występującego w konfiguracji rozszerzenia.
type | Typ ujścia definiowanego. Określa (jeśli istnieją) inne wartości w wystąpień tego typu.

Wersja 3.0 rozszerzenia diagnostycznych Linux obsługuje dwa typy zbiornika: EventHub i JsonBlob.

#### <a name="the-eventhub-sink"></a>Obiekt sink EventHub

```json
"sink": [
    {
        "name": "sinkname",
        "type": "EventHub",
        "sasURL": "https SAS URL"
    },
    ...
]
```

Wpis "sasURL" zawiera pełny adres URL tokenu sygnatury dostępu Współdzielonego, w tym Centrum zdarzeń, do którego powinien zostać opublikowany, dane. LAD wymaga sygnatury dostępu Współdzielonego nazewnictwa zasad, która umożliwia wysyłanie oświadczeń. Przykład:

* Tworzenie przestrzeni nazw usługi Event Hubs, o nazwie `contosohub`
* Tworzenie Centrum zdarzeń w obszarze nazw o nazwie `syslogmsgs`
* Tworzenie zasad dostępu współużytkowanego w Centrum zdarzeń o nazwie `writer` , który umożliwia wysyłanie oświadczeń

Jeśli utworzono sygnatury dostępu Współdzielonego dobra do północy czasu UTC 1 stycznia 2018 wartość sasURL może być:

```url
https://contosohub.servicebus.windows.net/syslogmsgs?sr=contosohub.servicebus.windows.net%2fsyslogmsgs&sig=xxxxxxxxxxxxxxxxxxxxxxxxx&se=1514764800&skn=writer
```

Aby uzyskać więcej informacji na temat generowania tokeny sygnatury dostępu Współdzielonego usługi Event hubs, zobacz [tej strony sieci web](../../event-hubs/event-hubs-authentication-and-security-model-overview.md).

#### <a name="the-jsonblob-sink"></a>Obiekt sink JsonBlob

```json
"sink": [
    {
        "name": "sinkname",
        "type": "JsonBlob"
    },
    ...
]
```

Dane, skierowane do ujścia JsonBlob są przechowywane w obiektach BLOB w magazynie Azure. Każde wystąpienie LAD tworzy obiektu blob co godzinę dla każdej nazwy obiektu sink. Każdy obiekt blob jest zawsze zawiera nieprawidłową składnię tablicy JSON obiektu. Nowe wpisy są automatycznie dodawane do tablicy. Obiekty BLOB są przechowywane w kontenerze o takiej samej nazwie jak obiekt sink. Zasady usługi Azure storage dla nazwy kontenera obiektów blob się do nazwy wychwytywanie JsonBlob: od 3 do 63 małe znaki alfanumeryczne ASCII lub łączniki.

## <a name="public-settings"></a>Ustawienia publicznego

Ta struktura zawiera bloki różnych ustawień kontrolujących informacje zebrane przez rozszerzenie. Każde ustawienie jest opcjonalne. Jeśli określisz `ladCfg`, należy także określić `StorageAccount`.

```json
{
    "ladCfg":  { ... },
    "perfCfg": { ... },
    "fileLogs": { ... },
    "StorageAccount": "the storage account to receive data",
    "mdsdHttpProxy" : ""
}
```

Element | Wartość
------- | -----
StorageAccount | Nazwa konta magazynu, w którym dane są zapisywane przez rozszerzenie. Muszą być tej samej nazwie, jak określono w [chronionych ustawień](#protected-settings).
mdsdHttpProxy | (opcjonalnie) Sam jak w [chronionych ustawień](#protected-settings). Wartość publicznego jest zastępowany przez wartość prywatne, jeśli ustawiona. Umieść ustawienia serwera proxy, które zawierają klucz tajny, takie jak hasła, w [chronionych ustawień](#protected-settings).

Wszystkie pozostałe elementy zostały szczegółowo opisane w kolejnych sekcjach.

### <a name="ladcfg"></a>ladCfg

```json
"ladCfg": {
    "diagnosticMonitorConfiguration": {
        "eventVolume": "Medium",
        "metrics": { ... },
        "performanceCounters": { ... },
        "syslogEvents": { ... }
    },
    "sampleRateInSeconds": 15
}
```

To określa opcjonalne struktury wychwytywanie zbieranie metryk i dzienników w celu dostarczania do usługi Azure metryki i innych danych. Należy określić `performanceCounters` lub `syslogEvents` lub oba. Należy określić `metrics` struktury.

Element | Wartość
------- | -----
eventVolume | (opcjonalnie) Określa liczbę partycji tworzonych w tabeli magazynu. Musi mieć jedną z `"Large"`, `"Medium"`, lub `"Small"`. Jeśli nie zostanie określony, wartością domyślną jest `"Medium"`.
sampleRateInSeconds | (opcjonalnie) Domyślny interwał między kolekcję pierwotnych metryki (unaggregated). Szybkość, z najmniejszą obsługiwanych próbki wynosi 15 sekund. Jeśli nie zostanie określony, wartością domyślną jest `15`.

#### <a name="metrics"></a>metrics

```json
"metrics": {
    "resourceId": "/subscriptions/...",
    "metricAggregation" : [
        { "scheduledTransferPeriod" : "PT1H" },
        { "scheduledTransferPeriod" : "PT5M" }
    ]
}
```

Element | Wartość
------- | -----
resourceId | Ustaw identyfikator zasobu usługi Azure Resource Manager z maszyny Wirtualnej lub skali maszyny wirtualnej do której należy maszyna wirtualna. To ustawienie musi być także określona, jeśli wszystkie zbiornika JsonBlob jest używane w konfiguracji.
scheduledTransferPeriod | Częstotliwość, z jaką metryki agregacji są obliczane i przeniesione do metryki Azure, wyrażona jako czas 8601 jest. Najmniejsza okres transfer jest 60 sekund, czyli PT1M. Należy określić co najmniej jeden scheduledTransferPeriod.

Przykłady metryk określone w sekcji liczniki wydajności są zbierane co 15 sekund lub na przykład ocenić jawnie zdefiniowane dla licznika. Jeśli występuje wiele częstotliwości scheduledTransferPeriod (jak pokazano w przykładzie), każdy agregacji jest obliczana niezależnie.

#### <a name="performancecounters"></a>liczniki wydajności

```json
"performanceCounters": {
    "sinks": "",
    "performanceCounterConfiguration": [
        {
            "type": "builtin",
            "class": "Processor",
            "counter": "PercentIdleTime",
            "counterSpecifier": "/builtin/Processor/PercentIdleTime",
            "condition": "IsAggregate=TRUE",
            "sampleRate": "PT15S",
            "unit": "Percent",
            "annotation": [
                {
                    "displayName" : "Aggregate CPU %idle time",
                    "locale" : "en-us"
                }
            ]
        }
    ]
}
```

Ta sekcja opcjonalna kontrolę nad zbieraniem metryki. Przykłady pierwotnych są agregowane dla wszystkich [scheduledTransferPeriod](#metrics) do tworzenia tych wartości:

* Średnia
* minimalnie
* maksymalnie
* wartości zbierane przez ostatnie
* Liczba próbek raw, używany do obliczania wartości zagregowanej

Element | Wartość
------- | -----
sink — obiekty | (opcjonalnie) Rozdzielana przecinkami lista nazw wychwytywanie, do których LAD wysyła zagregowane metryki wyników. Wszystkie metryki zagregowane są publikowane do każdej z wymienionych ujścia. Zobacz [sinksConfig](#sinksconfig). Przykład: `"EHsink1, myjsonsink"`.
type | Określa dostawcę rzeczywiste metryki.
klasa | Wraz z "licznika" identyfikuje określonej metryki w obszarze nazw dostawcy.
Licznik | Wraz z "class" identyfikuje określonej metryki w obszarze nazw dostawcy.
counterSpecifier | Identyfikuje określonej metryki w obszarze nazw metryki Azure.
warunek | (opcjonalnie) Wybiera konkretne wystąpienie obiektu do którego Metryka stosuje lub wybiera agregacji we wszystkich wystąpieniach tego obiektu. Aby uzyskać więcej informacji, zobacz [ `builtin` definicji metryk](#metrics-supported-by-builtin).
sampleRate | JEST interwałem 8601, która ustawia współczynnik pobierane pierwotnych próbek ta metryka. Jeśli nie jest ustawiona, kolekcji interwał jest ustawiany przez wartość [sampleRateInSeconds](#ladcfg). Najkrótszy częstotliwość próbkowania obsługiwanych wynosi 15 sekund (PT15S).
jednostka | Powinien być jednym z tych ciągów: "Count", "B", "Seconds", "%", "CountPerSecond", "BytesPerSecond", "Milisekund". Definiuje jednostkę metryki. Konsumenci danych zebranych oczekuje wartości zebrane dane do tej jednostki. LAD są ignorowane w tym polu.
displayName | Etykieta (w języku określonym przez ustawienie skojarzone ustawienia regionalne) jest dołączony do tych danych w Azure metryki. LAD są ignorowane w tym polu.

CounterSpecifier jest umownym identyfikatorem. Konsumenci metryki, Azure portalu wykresów, takich jak i alerty funkcji, użyj counterSpecifier jako "klucza", który identyfikuje metrykę lub wystąpienia metryki. Aby uzyskać `builtin` metryki, zalecane jest użycie counterSpecifier wartości, które zaczynają się od `/builtin/`. Określone wystąpienie metryki są zbierane, firma Microsoft zaleca się, że możesz dołączyć do wartości counterSpecifier identyfikator wystąpienia. Oto niektóre przykłady:

* `/builtin/Processor/PercentIdleTime` -Bezczynności uśredniona wszystkie Vcpu
* `/builtin/Disk/FreeSpace(/mnt)` -Wolnego miejsca w systemie plików w katalogu/mnt
* `/builtin/Disk/FreeSpace` — Wolne miejsce uśredniona wszystkie zainstalowane systemy plików

Portalu Azure ani LAD oczekuje, że wartość counterSpecifier na zgodny z żadnym wzorcem. Być zgodne w konstrukcji counterSpecifier wartości.

Po określeniu `performanceCounters`, zawsze LAD zapisuje dane do tabeli w magazynie Azure. Może mieć te same dane zapisane obiekty BLOB JSON i/lub centra zdarzeń, ale nie można wyłączyć zapisywanie danych w tabeli. Wszystkie wystąpienia rozszerzenia diagnostycznych skonfigurowana do używania tej samej nazwy konta magazynu i punktu końcowego dodać dzienniki i metryk do tej samej tabeli. Jeśli pisania zbyt wiele maszyn wirtualnych w tej samej partycji tabeli, Azure można ograniczyć zapisy do tej partycji. Ustawienie eventVolume powoduje, że wpisy rozpowszechniania przez 10 (małe), 1 (średnia liczba godzin) lub 100 (duży) różnych partycji. Zwykle wystarczające, aby upewnić się, że nie jest ograniczany ruch sieciowy jest "Średnia". Funkcję metryki Azure w portalu Azure używa danych zawartych w tej tabeli do produkcji wykresy lub wyzwalanie alertów. Nazwa tabeli jest złączeniem te ciągi:

* `WADMetrics`
* "ScheduledTransferPeriod" zagregowane wartości przechowywane w tabeli
* `P10DV2S`
* Data, w postaci "RRRRMMDD", która zmienia co 10 dni

Przykłady obejmują `WADMetricsPT1HP10DV2S20170410` i `WADMetricsPT1MP10DV2S20170609`.

#### <a name="syslogevents"></a>syslogEvents

```json
"syslogEvents": {
    "sinks": "",
    "syslogEventConfiguration": {
        "facilityName1": "minSeverity",
        "facilityName2": "minSeverity",
        ...
    }
}
```

Ta sekcja opcjonalna kontrolę nad zbieraniem dziennika zdarzeń z dziennika systemowego. W przypadku pominięcia sekcji zdarzenia dziennika systemowego nie są przechwytywane w ogóle.

Kolekcja syslogEventConfiguration ma jeden wpis dla każdego obiektu syslog zainteresowań. W przypadku minSeverity "NONE" dla określonego obiektu lub tego obiektu nie ma w elemencie w ogóle, żadne zdarzenia z tym zakładzie są przechwytywane.

Element | Wartość
------- | -----
sink — obiekty | Rozdzielana przecinkami lista nazw wychwytywanie, w którym publikowane są zdarzenia dziennika poszczególnych. Dopasowywanie ograniczeń w syslogEventConfiguration wszystkie zdarzenia dziennika są publikowane do każdej z wymienionych ujścia. Przykład: "EHforsyslog"
facilityName | Nazwę obiektu syslog (takich jak "dziennika\_użytkownika" lub "dziennika\_LOCAL0"). Zobacz sekcję "funkcje" [strony man syslog](http://man7.org/linux/man-pages/man3/syslog.3.html) dla pełnej listy.
minSeverity | Poziom ważności syslog (takich jak "dziennika\_błąd" lub "dziennika\_informacje"). Zobacz sekcję "poziomu" [strony man syslog](http://man7.org/linux/man-pages/man3/syslog.3.html) dla pełnej listy. Rozszerzenie przechwytuje zdarzeń wysłanych do funkcji lub wyższym określonego poziomu.

Po określeniu `syslogEvents`, zawsze LAD zapisuje dane do tabeli w magazynie Azure. Może mieć te same dane zapisane obiekty BLOB JSON i/lub centra zdarzeń, ale nie można wyłączyć zapisywanie danych w tabeli. Zachowanie partycjonowania dla tej tabeli jest taka sama jak `performanceCounters`. Nazwa tabeli jest złączeniem te ciągi:

* `LinuxSyslog`
* Data, w postaci "RRRRMMDD", która zmienia co 10 dni

Przykłady obejmują `LinuxSyslog20170410` i `LinuxSyslog20170609`.

### <a name="perfcfg"></a>perfCfg

Określa tę sekcję, wykonanie dowolnego [OMI](https://github.com/Microsoft/omi) zapytania.

```json
"perfCfg": [
    {
        "namespace": "root/scx",
        "query": "SELECT PercentAvailableMemory, PercentUsedSwap FROM SCX_MemoryStatisticalInformation",
        "table": "LinuxOldMemory",
        "frequency": 300,
        "sinks": ""
    }
]
```

Element | Wartość
------- | -----
przestrzeń nazw | (opcjonalnie) Przestrzeń nazw OMI, w którym można wykonać zapytania. Jeśli zostanie określona, wartością domyślną jest "główny/scx", implementowane przez [programu System Center i platform dostawców](http://scx.codeplex.com/wikipage?title=xplatproviders&referringTitle=Documentation).
query | Zapytanie OMI do wykonania.
tabela | (opcjonalnie) W tabeli magazynu systemu Azure w ramach konta magazynu wyznaczonego (zobacz [chronionych ustawień](#protected-settings)).
frequency | (opcjonalnie) Liczba sekund między wykonywania zapytania. Wartość domyślna to 300 (5 minut); wartość minimalna wynosi 15 sekund.
sink — obiekty | (opcjonalnie) Rozdzielana przecinkami lista nazw wychwytywanie dodatkowe, do których powinien zostać opublikowany, przykładowe nieprzetworzone wyniki metryki. Bez agregacji te przykłady pierwotnych jest obliczana przez rozszerzenie lub metryk usługi Azure.

Należy określić albo "Tabela" lub "sink" i/lub.

### <a name="filelogs"></a>fileLogs

Steruje przechwytywania plików dziennika. LAD przechwytuje nowych wierszy tekstu, ponieważ są one zapisywane do pliku i zapisuje je w tabeli wiersze i/lub dowolnej określonej wychwytywanie (JsonBlob lub EventHub).

```json
"fileLogs": [
    {
        "file": "/var/log/mydaemonlog",
        "table": "MyDaemonEvents",
        "sinks": ""
    }
]
```

Element | Wartość
------- | -----
plik | Pełna nazwa ścieżki pliku dziennika, aby być obserwowane i przechwycone. Nazwa ścieżki musi nazwę jednego pliku; Nie można jej nazwę katalogu lub zawierać symbole wieloznaczne.
tabela | (opcjonalnie) Tabela magazynu Azure w ramach konta magazynu wyznaczonego (określone w konfiguracji chronionym), w którym są zapisywane nowe wiersze z "fragmentu" w pliku.
sink — obiekty | (opcjonalnie) Rozdzielana przecinkami lista nazw dodatkowe wychwytywanie do wierszy dziennika, które wysyłane.

Należy określić albo "Tabela" lub "sink" i/lub.

## <a name="metrics-supported-by-the-builtin-provider"></a>Metryki obsługiwane przez dostawcę wbudowane

Dostawca metryki wbudowane jest źródłem szeroką gamę użytkowników najbardziej interesujące metryki. Te metryki można podzielić na pięć szerokie klasy:

* Procesor
* Memory (Pamięć)
* Sieć
* System plików
* Dysk

### <a name="builtin-metrics-for-the-processor-class"></a>wbudowane metryki dla klasy procesora

Klasa procesora metryk udostępnia informacje na temat użycia procesora w maszynie Wirtualnej. Podczas agregowania wartości procentowych, wynikiem jest średnią przez wszystkie procesory. Na maszynie Wirtualnej z dwoma vCPU Jeśli jeden vCPU był zajęty 100% i innych był bezczynny, 100% zgłoszone PercentIdleTime będzie 50. Jeśli każda vCPU 50% zajęte przez ten sam okres, zgłoszone również będą 50. W czterech vCPU maszyny Wirtualnej, z jedną vCPU 100% zajęty i innych bezczynny zgłoszone PercentIdleTime jest 75.

Licznik | Znaczenie
------- | -------
PercentIdleTime | Procent czasu, w oknie agregacji, że procesory zostały wykonywania pętli bezczynności jądra
percentProcessorTime | Procent czasu wykonania wątku czynnego
PercentIOWaitTime | Procent czasu oczekiwania na zakończenie operacji We/Wy
PercentInterruptTime | Procent czasu wykonywania sprzęt i oprogramowanie przerwań i wywołań DPC (opóźnione wywołania procedur)
PercentUserTime | Czynnego czasu podczas okna agregacji procent czasu spędzony w użytkownika więcej przy normalnym priorytecie
PercentNiceTime | Czynnego czasu wartość procentowa działania priorytetem obniżona (nieuprzywilejowany)
PercentPrivilegedTime | Czas czynnego wartość procentowa działania w trybie uprzywilejowanym (jądra)

Pierwsze cztery liczniki powinien Suma, umożliwiającej 100%. Ostatnich trzech również liczniki Suma do 100%; Suma PercentProcessorTime, PercentIOWaitTime i PercentInterruptTime ich podziału.

Uzyskanie pojedynczego metrykę zagregowane we wszystkich procesorów, wartość `"condition": "IsAggregate=TRUE"`. Uzyskanie metryki dla określonego procesora, takich jak drugi procesor logiczny vCPU cztery maszyny wirtualnej, należy ustawić `"condition": "Name=\\"1\\""`. Numery procesorów logicznych znajdują się w zakresie `[0..n-1]`.

### <a name="builtin-metrics-for-the-memory-class"></a>wbudowane metryki dla klasy pamięci

Klasa pamięci metryk zawiera informacje o wykorzystanie pamięci, stronicowania i wymiany.

Licznik | Znaczenie
------- | -------
AvailableMemory | Dostępnej pamięci fizycznej w MiB
PercentAvailableMemory | Dostępna pamięć fizyczna jako procent całkowitej ilości pamięci
UsedMemory | W użyciu pamięć fizyczna (MiB)
PercentUsedMemory | W użyciu pamięć fizyczna jako procent całkowitej ilości pamięci
PagesPerSec | Całkowita liczba stronicowania (odczyt/zapis)
PagesReadPerSec | Strony odczytywać kopii magazynu (pliku wymiany, pliku programu, pliku mapowanego itp.)
PagesWrittenPerSec | Stron zapisanych do magazynu zapasowego (pliku wymiany, pliku mapowanego itp.)
AvailableSwap | Obszar wymiany nieużywane (MiB)
PercentAvailableSwap | Obszar wymiany nieużywane jako procent całkowitego obszaru wymiany
UsedSwap | W użyciu obszar wymiany (MiB)
PercentUsedSwap | W obsłudze obszaru wymiany w procentach całkowitego obszaru wymiany

Ta klasa metryki ma tylko jedno wystąpienie. Atrybut "warunku" nie zawiera przydatne ustawień i powinny być pominięte.

### <a name="builtin-metrics-for-the-network-class"></a>wbudowane metryki dla klasy sieciowe

Klasa sieci metryk zawiera informacje o aktywności sieci dla poszczególnych interfejsów od uruchomienia. LAD nie ujawnia przepustowości metryk, które mogą być pobierane z hosta metryki.

Licznik | Znaczenie
------- | -------
BytesTransmitted | Całkowita liczba bajtów wysłanych od momentu rozruchu
BytesReceived | Całkowita liczba bajtów odebranych od rozruchu
BytesTotal | Całkowita liczba bajtów wysyłane lub odbierane od rozruchu
PacketsTransmitted | Łączna liczba pakietów wysłanych od momentu rozruchu
PacketsReceived | Całkowita liczba pakietów otrzymanych od rozruchu
TotalRxErrors | Liczba błędów od rozruchu
TotalTxErrors | Liczba transmisji błędów od rozruchu
TotalCollisions | Liczba kolizji zgłoszone przez porty od rozruchu

 Mimo że ta klasa jest instancji, LAD nie obsługuje przechwytywania metryki sieci zagregowane we wszystkich urządzeń sieciowych. Aby uzyskać metryki dla określonego interfejsu, takich jak eth0, ustaw `"condition": "InstanceID=\\"eth0\\""`.

### <a name="builtin-metrics-for-the-filesystem-class"></a>wbudowane metryki dla klasy systemu plików

Klasa Filesystem metryk udostępnia informacje na temat użycia systemu plików. Wartości bezwzględne i procent ma być zgłaszane będzie wyświetlana do zwykłego użytkownika (a nie głównego).

Licznik | Znaczenie
------- | -------
FreeSpace | Dostępnego miejsca na dysku w bajtach
UsedSpace | Używane miejsce na dysku w bajtach
PercentFreeSpace | Wartość procentowa wolnego miejsca
PercentUsedSpace | Procent wykorzystania miejsca
PercentFreeInodes | Wartość procentowa węzłów i nieużywanych
PercentUsedInodes | Wartość procentowa przydzielonych (w użyciu) węzły i sumowane przez wszystkie systemy plików
BytesReadPerSecond | Bajtów odczytanych na sekundę
BytesWrittenPerSecond | Bajtów zapisanych na sekundę
Bajty na sekundę | Bajty odczytu lub zapisu na sekundę
ReadsPerSecond | Operacje odczytu na sekundę
WritesPerSecond | Zapis operacji na sekundę
TransfersPerSecond | Operacje odczytu lub zapisu na sekundę

Wartości zagregowane we wszystkich systemach plików można uzyskać przez ustawienie `"condition": "IsAggregate=True"`. Wartości dla określonego zainstalowany system plików, takich jak "/ mnt", można uzyskać przez ustawienie `"condition": 'Name="/mnt"'`.

### <a name="builtin-metrics-for-the-disk-class"></a>wbudowane metryki dla klasy dysku

Klasa dysku metryk udostępnia informacje na temat użycia urządzenia dysku. Te statystyki mają zastosowanie do całego dysku. Jeśli istnieje wiele systemów plików na urządzeniu, liczników dla tego urządzenia to w rzeczywistości zagregowane we wszystkich z nich.

Licznik | Znaczenie
------- | -------
ReadsPerSecond | Operacje odczytu na sekundę
WritesPerSecond | Zapis operacji na sekundę
TransfersPerSecond | Całkowita liczba operacji na sekundę
AverageReadTime | Średnia liczba sekund na operacja odczytu
AverageWriteTime | Średnia liczba sekund na operację zapisu
AverageTransferTime | Średnia liczba sekund na operację
AverageDiskQueueLength | Średnia liczba operacji w kolejce dysku
ReadBytesPerSecond | Liczba bajtów odczytanych na sekundę
WriteBytesPerSecond | Liczba bajtów zapisanych na sekundę
Bajty na sekundę | Liczba bajtów odczytywanych lub zapisywanych na sekundę

Zagregowane wartości na wszystkich dyskach można uzyskać przez ustawienie `"condition": "IsAggregate=True"`. Aby uzyskać informacje dotyczące określonego urządzenia (na przykład/dev/sdf1), należy ustawić `"condition": "Name=\\"/dev/sdf1\\""`.

## <a name="installing-and-configuring-lad-30-via-cli"></a>Instalowanie i konfigurowanie LAD 3.0 za pomocą interfejsu wiersza polecenia

Zakładając, że ustawienia chronionego znajdują się w pliku PrivateConfig.json i informacje o konfiguracji publicznego jest PublicConfig.json, uruchom następujące polecenie:

```azurecli
az vm extension set *resource_group_name* *vm_name* LinuxDiagnostic Microsoft.Azure.Diagnostics '3.*' --private-config-path PrivateConfig.json --public-config-path PublicConfig.json
```

Polecenie przyjęto założenie, że używany jest tryb zarządzania zasobami Azure (arm) z wiersza polecenia platformy Azure. Aby skonfigurować LAD wdrożenia klasycznego modelu (ASM), maszynach wirtualnych, Przełącz do trybu "asm" (`azure config mode asm`) i pominąć nazwę grupy zasobów w poleceniu. Aby uzyskać więcej informacji, zobacz [dokumentacji interfejsu wiersza polecenia i platform](https://docs.microsoft.com/azure/xplat-cli-connect).

## <a name="an-example-lad-30-configuration"></a>Przykład LAD 3.0 konfiguracji

Oparte na poprzednim definicje, Oto przykładowa konfiguracja rozszerzenia LAD 3.0 z wyjaśnień. Aby zastosować w tym przykładzie do sprawę, należy używać własne nazwy konta magazynu, token sygnatury dostępu Współdzielonego konta i tokeny EventHubs sygnatury dostępu Współdzielonego.

### <a name="privateconfigjson"></a>PrivateConfig.json

Skonfiguruj te ustawienia prywatnych:

* Konto magazynu
* zgodnego tokenu sygnatury dostępu Współdzielonego konta
* wychwytywanie kilka (JsonBlob lub EventHubs z tokenami SAS)

```json
{
  "storageAccountName": "yourdiagstgacct",
  "storageAccountSasToken": "sv=xxxx-xx-xx&ss=bt&srt=co&sp=wlacu&st=yyyy-yy-yyT21%3A22%3A00Z&se=zzzz-zz-zzT21%3A22%3A00Z&sig=fake_signature",
  "sinksConfig": {
    "sink": [
      {
        "name": "SyslogJsonBlob",
        "type": "JsonBlob"
      },
      {
        "name": "FilelogJsonBlob",
        "type": "JsonBlob"
      },
      {
        "name": "LinuxCpuJsonBlob",
        "type": "JsonBlob"
      },
      {
        "name": "MyJsonMetricsBlob",
        "type": "JsonBlob"
      },
      {
        "name": "LinuxCpuEventHub",
        "type": "EventHub",
        "sasURL": "https://youreventhubnamespace.servicebus.windows.net/youreventhubpublisher?sr=https%3a%2f%2fyoureventhubnamespace.servicebus.windows.net%2fyoureventhubpublisher%2f&sig=fake_signature&se=1808096361&skn=yourehpolicy"
      },
      {
        "name": "MyMetricEventHub",
        "type": "EventHub",
        "sasURL": "https://youreventhubnamespace.servicebus.windows.net/youreventhubpublisher?sr=https%3a%2f%2fyoureventhubnamespace.servicebus.windows.net%2fyoureventhubpublisher%2f&sig=yourehpolicy&skn=yourehpolicy"
      },
      {
        "name": "LoggingEventHub",
        "type": "EventHub",
        "sasURL": "https://youreventhubnamespace.servicebus.windows.net/youreventhubpublisher?sr=https%3a%2f%2fyoureventhubnamespace.servicebus.windows.net%2fyoureventhubpublisher%2f&sig=yourehpolicy&se=1808096361&skn=yourehpolicy"
      }
    ]
  }
}
```

### <a name="publicconfigjson"></a>PublicConfig.json

Te ustawienia publicznego spowodować LAD do:

* Przekaż metryki procent procesora czasu i używać miejsca na dysku do `WADMetrics*` tabeli
* Przekazywanie komunikatów z syslog zakładzie "użytkownika" i ważność "informacje" do `LinuxSyslog*` tabeli
* Przekaż nieprzetworzone wyniki zapytania OMI (PercentProcessorTime i PercentIdleTime) do nazwanego `LinuxCPU` tabeli
* Przekaż dołączany wiersze w pliku `/var/log/myladtestlog` do `MyLadTestLog` tabeli

W każdym przypadku również przekazywania danych do:

* Magazyn obiektów Blob Azure (nazwa kontenera jest zdefiniowany w ujściu JsonBlob)
* Punkt końcowy EventHubs (jak określono w ujściu EventHubs)

```json
{
  "StorageAccount": "yourdiagstgacct",
  "sampleRateInSeconds": 15,
  "ladCfg": {
    "diagnosticMonitorConfiguration": {
      "performanceCounters": {
        "sinks": "MyMetricEventHub,MyJsonMetricsBlob",
        "performanceCounterConfiguration": [
          {
            "unit": "Percent",
            "type": "builtin",
            "counter": "PercentProcessorTime",
            "counterSpecifier": "/builtin/Processor/PercentProcessorTime",
            "annotation": [
              {
                "locale": "en-us",
                "displayName": "Aggregate CPU %utilization"
              }
            ],
            "condition": "IsAggregate=TRUE",
            "class": "Processor"
          },
          {
            "unit": "Bytes",
            "type": "builtin",
            "counter": "UsedSpace",
            "counterSpecifier": "/builtin/FileSystem/UsedSpace",
            "annotation": [
              {
                "locale": "en-us",
                "displayName": "Used disk space on /"
              }
            ],
            "condition": "Name=\"/\"",
            "class": "Filesystem"
          }
        ]
      },
      "metrics": {
        "metricAggregation": [
          {
            "scheduledTransferPeriod": "PT1H"
          },
          {
            "scheduledTransferPeriod": "PT1M"
          }
        ],
        "resourceId": "/subscriptions/your_azure_subscription_id/resourceGroups/your_resource_group_name/providers/Microsoft.Compute/virtualMachines/your_vm_name"
      },
      "eventVolume": "Large",
      "syslogEvents": {
        "sinks": "SyslogJsonBlob,LoggingEventHub",
        "syslogEventConfiguration": {
          "LOG_USER": "LOG_INFO"
        }
      }
    }
  },
  "perfCfg": [
    {
      "query": "SELECT PercentProcessorTime, PercentIdleTime FROM SCX_ProcessorStatisticalInformation WHERE Name='_TOTAL'",
      "table": "LinuxCpu",
      "frequency": 60,
      "sinks": "LinuxCpuJsonBlob,LinuxCpuEventHub"
    }
  ],
  "fileLogs": [
    {
      "file": "/var/log/myladtestlog",
      "table": "MyLadTestLog",
      "sinks": "FilelogJsonBlob,LoggingEventHub"
    }
  ]
}
```

`resourceId` w konfiguracji muszą być zgodne czy maszyny Wirtualnej lub skalowania maszyny wirtualnej ustawić.

* Metryki platformy Azure, wykresów i alerty wie resourceId pracy maszyny wirtualnej. Oczekuje się znaleźć dane dla maszyny Wirtualnej przy użyciu element resourceId klucz wyszukiwania.
* Jeśli używasz platformy Azure skalowania automatycznego resourceId w konfiguracji automatycznego skalowania musi być zgodna z resourceId używane przez LAD.
* Element resourceId jest wbudowana w nazwach JsonBlobs napisane przez LAD.

## <a name="view-your-data"></a>Wyświetlanie danych

Użyj portalu Azure, aby wyświetlić dane wydajności lub Ustaw alerty:

![Obraz](./media/diagnostics-linux/graph_metrics.png)

`performanceCounters` Danych zawsze są przechowywane w tabeli magazynu Azure. Interfejsy API usługi Azure Storage są dostępne dla wielu języków i platform.

Dane wysłane do ujścia JsonBlob są przechowywane w obiektach blob na koncie magazynu o nazwie w [chronionych ustawień](#protected-settings). Będzie można korzystać z danych obiektu blob, przy użyciu dowolnego interfejsów API magazynu obiektów Blob Azure.

Ponadto można tych narzędzi interfejsu użytkownika dostępu do danych w usłudze Azure Storage:

* Eksplorator serwera programu Visual Studio.
* [Eksplorator usługi Storage platformy Microsoft Azure](https://azurestorageexplorer.codeplex.com/ "Eksploratora usługi Azure Storage").

Tej migawki sesji Eksploratora usługi Microsoft Azure Storage jest wyświetlana wygenerowanego tabele usługi Azure Storage i kontenery z rozszerzeniem LAD 3.0 poprawnie skonfigurowana na testowej maszynie Wirtualnej. Obraz nie jest zgodna z [Przykładowa konfiguracja LAD 3.0](#an-example-lad-30-configuration).

![Obraz](./media/diagnostics-linux/stg_explorer.png)

Zobacz odpowiedniego [dokumentacji EventHubs](../../event-hubs/event-hubs-what-is-event-hubs.md) aby nauczyć się korzystać komunikaty opublikowane do punktu końcowego EventHubs.

## <a name="next-steps"></a>Kolejne kroki

* Tworzenie metryki alertów w [Azure Monitor](../../monitoring-and-diagnostics/insights-alerts-portal.md) dla metryki zbierania.
* Utwórz [monitorowania wykresy](../../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md) dla Twojego metryki.
* Dowiedz się, jak [utworzyć zestaw skali maszyny wirtualnej](../linux/tutorial-create-vmss.md) przy użyciu programu metryk, aby kontrolować skalowanie automatyczne.
