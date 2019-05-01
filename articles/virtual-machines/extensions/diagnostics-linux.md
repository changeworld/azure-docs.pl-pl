---
title: Obliczeniowe platformy Azure — rozszerzenie diagnostyczne systemu Linux | Dokumentacja firmy Microsoft
description: Jak skonfigurować Azure Linux diagnostycznych rozszerzenia (LAD) do zbierania metryk i rejestrowania zdarzeń z maszyn wirtualnych systemu Linux działających na platformie Azure.
services: virtual-machines-linux
author: abhijeetgaiha
manager: sankalpsoni
ms.service: virtual-machines-linux
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 12/13/2018
ms.author: agaiha
ms.openlocfilehash: e43ba83581b6ce012c619036317361a7c1c0bf4f
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64710412"
---
# <a name="use-linux-diagnostic-extension-to-monitor-metrics-and-logs"></a>Użyj rozszerzenia diagnostycznego systemu Linux do monitorowania metryk i dzienników

Ten dokument opisuje wersję 3.0 lub nowszej rozszerzenia diagnostycznego systemu Linux.

> [!IMPORTANT]
> Aby uzyskać informacje dotyczące wersji 2.3 i starszych, zobacz [w tym dokumencie](../linux/classic/diagnostic-extension-v2.md).

## <a name="introduction"></a>Wprowadzenie

Rozszerzenia diagnostycznego systemu Linux pozwala użytkownika monitora kondycji maszyny Wirtualnej systemu Linux w systemie Microsoft Azure. Ma następujące możliwości:

* Zbiera metryki wydajności systemu z maszyny Wirtualnej i zapisuje je w określonej tabeli w ramach konta magazynu wyznaczonego.
* Pobiera zdarzenia z dziennika z dziennika systemu i przechowuje je w określonej tabeli w ramach konta magazynu wyznaczonego.
* Umożliwia użytkownikom dostosować metryki danych, które są zbierane i przekazane.
* Umożliwia użytkownikom dostosowywanie urządzeń usługi syslog i poziomów ważności zdarzeń, które są zbierane i przekazane.
* Umożliwia użytkownikom przekazywanie określone pliki dziennika do tabeli magazynu wyznaczonego.
* Obsługuje wysyłanie metryk i dzienników zdarzeń do dowolnych punktów końcowych usługi EventHub i sformatowanego JSON z obiektami BLOB w ramach konta magazynu wyznaczonego.

To rozszerzenie współpracuje z obu modeli wdrażania platformy Azure.

## <a name="installing-the-extension-in-your-vm"></a>Instalowanie rozszerzenia maszyny Wirtualnej

To rozszerzenie można włączyć za pomocą poleceń cmdlet programu Azure PowerShell, skryptów wiersza polecenia platformy Azure, szablonów ARM lub witryny Azure portal. Aby uzyskać więcej informacji, zobacz [funkcji rozszerzenia](features-linux.md).

Te instrukcje dotyczące instalacji i [do pobrania Przykładowa konfiguracja](https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json) LAD 3.0 można skonfigurować:

* Przechwytywanie i przechowywanie te same metryki zostały dostarczone przez LAD 2.3;
* Przechwytywanie zbiór przydatne metryki systemu plików, jesteś nowym użytkownikiem LAD 3.0;
* Przechwytywanie domyślną kolekcję syslog włączane przez LAD 2.3;
* Włącz środowisku witryny Azure portal dla wykresów i alertów dotyczących metryk maszyny Wirtualnej.

Konfiguracja do pobrania jest tylko przykładowe; Zmodyfikuj go do swoich potrzeb.

### <a name="prerequisites"></a>Wymagania wstępne

* **Agent systemu Linux platformy Azure w wersji 2.2.0 lub nowszej**. Większość obrazów w galerii Azure VM Linux zawierają wersję 2.2.7 lub nowszej. Uruchom `/usr/sbin/waagent -version` aby upewnić się, wersja zainstalowana na maszynie Wirtualnej. Jeśli maszyna wirtualna jest uruchomiona starsza wersja agenta gościa, postępuj zgodnie z [w instrukcjach](https://docs.microsoft.com/azure/virtual-machines/linux/update-agent) ją zaktualizować.
* **Interfejs wiersza polecenia platformy Azure**. [Konfigurowanie interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) środowiska na swojej maszynie.
* Polecenia wget, jeśli nie jeszcze: Uruchom polecenie `sudo apt-get install wget`.
* Istniejącej subskrypcji platformy Azure i istniejące konto magazynu w niej do przechowywania danych.
* Znajduje się na liście obsługiwanych dystrybucjach systemu Linux https://github.com/Azure/azure-linux-extensions/tree/master/Diagnostic#supported-linux-distributions

### <a name="sample-installation"></a>Przykład instalacji

Podaj prawidłowe parametry w pierwszych trzech wierszy, a następnie wykonanie tego skryptu, jako katalog główny:

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

Adres URL dla przykładowej konfiguracji i jego zawartość, mogą ulec zmianie. Pobierz kopię pliku JSON ustawienia portalu i go dostosować do własnych potrzeb. Wszystkie szablony lub automatyzacji, który można skonstruować należy używać swoją własną kopią zamiast pobierać tego adresu URL każdorazowo.

### <a name="updating-the-extension-settings"></a>Trwa aktualizowanie ustawień rozszerzenia

Po zmianie ustawienia publiczny lub chroniony, na których je wdrożyć na maszynie wirtualnej za pomocą tego samego polecenia. Jeśli jakieś zmiany w ustawieniach, zaktualizowane ustawienia są wysyłane do rozszerzenia. LAD ponowne załadowanie konfiguracji i uruchamia się ponownie.

### <a name="migration-from-previous-versions-of-the-extension"></a>Migracja z wcześniejszych wersji rozszerzenia

Najnowsza wersja rozszerzenia jest **3.0**. **Wszystkie starsze wersje (2.x) są przestarzałe i może być nieopublikowane na lub po 31 lipca 2018 r**.

> [!IMPORTANT]
> To rozszerzenie wprowadza zmiany powodujące niezgodność w konfiguracji rozszerzenia. Taka zmiana została wprowadzona w celu zwiększenia bezpieczeństwa rozszerzenia. w wyniku wstecznej zgodności z 2.x może nie być obsługiwane. Ponadto wydawca rozszerzenia dla tego rozszerzenia jest inny niż wydawcy dla wersji 2.x.
>
> Aby przeprowadzić migrację 2.x, aby nowa wersja rozszerzenia, należy odinstalować stare rozszerzenia (w obszarze starej nazwy wydawcy), a następnie zainstalować rozszerzenia w wersji 3.

Rekomendacje:

* Uaktualnianie automatyczne podverze włączone, należy zainstalować rozszerzenie.
  * W klasycznym modelu wdrażania maszyn wirtualnych należy określić "3.*" jako wersję w przypadku instalowania rozszerzeń przy użyciu wiersza polecenia XPLAT platformy Azure lub programu Powershell.
  * Na usługi Azure Resource Manager deployment model maszyn wirtualnych, należy umieścić ""autoUpgradeMinorVersion": true" w szablonie wdrożenia maszyny Wirtualnej.
* Nowe/innego konta magazynu na użytek LAD 3.0. Istnieje kilka małych niezgodności między LAD 2.3 i LAD 3.0, które ułatwić udostępnianie problematycznych konta:
  * Zdarzenia dziennika systemowego LAD 3.0 są przechowywane w tabeli o innej nazwie.
  * CounterSpecifier ciągi dla `builtin` metryki różnią się w LAD 3.0.

## <a name="protected-settings"></a>Chronione ustawienia

Ten zestaw informacji o konfiguracji zawiera poufne informacje, które powinny być chronione z widoku publicznego, na przykład poświadczenia magazynu. Te ustawienia są przesyłane do i przechowywane przez rozszerzenie w postaci zaszyfrowanej.

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
storageAccountEndPoint | (opcjonalnie) Punkt końcowy identyfikowanie chmury, w której istnieje konto magazynu. Jeśli to ustawienie jest nieobecne, LAD wartość domyślna to chmura platformy Azure, `https://core.windows.net`. Aby korzystać z konta magazynu Azure (Niemcy), platformy Azure Government lub Azure (Chiny), należy odpowiednio ustawić tę wartość.
storageAccountSasToken | [Tokenu sygnatury dostępu Współdzielonego konta](https://azure.microsoft.com/blog/sas-update-account-sas-now-supports-all-storage-services/) usługi obiektów Blob oraz tabel (`ss='bt'`), zastosowanie do kontenerów i obiektów (`srt='co'`), która przyznaje dodać, tworzenie, listy, aktualizacji i uprawnień do zapisu (`sp='acluw'`). Czy *nie* zawierają wiodące znak zapytania (?).
mdsdHttpProxy | (opcjonalnie) Wymagane do włączenia rozszerzenia Aby nawiązać połączenie z podanego konta magazynu i punktu końcowego informacji serwera proxy HTTP.
sinksConfig | (opcjonalnie) Szczegółowe informacje o alternatywnych miejsc docelowych, do których mogą być dostarczane metryk i zdarzeń. Określone szczegóły każdego ujście danych obsługiwanego przez rozszerzenie zostały uwzględnione w kolejnych sekcjach.


> [!NOTE]
> Podczas wdrażania rozszerzenia za pomocą szablonu wdrażania na platformie Azure, konta magazynu i tokenu sygnatury dostępu Współdzielonego należy wcześniej utworzony i następnie przekazywane do szablonu. Nie można wdrożyć maszynę Wirtualną, konta magazynu i skonfigurowanie rozszerzenia w jednym szablonie. Tworzenie tokenu sygnatury dostępu Współdzielonego, w ramach szablonu nie jest obecnie obsługiwane.

Łatwo można skonstruować wymagany token sygnatury dostępu Współdzielonego, za pośrednictwem witryny Azure portal.

1. Wybierz konto magazynu ogólnego przeznaczenia, do którego mają rozszerzenia Aby zapisać
1. Wybierz pozycję "Sygnaturę dostępu współdzielonego" z części ustawienia w menu po lewej stronie
1. Wprowadź odpowiednie części, jak opisano wcześniej
1. Kliknij przycisk "Generowanie sygnatury dostępu Współdzielonego".

![image](./media/diagnostics-linux/make_sas.png)

Skopiuj wygenerowany sygnatury dostępu Współdzielonego do pola storageAccountSasToken; Usuń znak zapytania wiodących ("?").

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

Ta opcjonalna sekcja definiuje dodatkowe miejsc docelowych, do których rozszerzenie wysyła informacje, które są zbierane. Tablica "sink" zawiera obiekt dla każdego obiektu sink dodatkowe dane. Atrybut "type" Określa inne atrybuty w obiekcie.

Element | Wartość
------- | -----
name | Ciąg używany do odwoływania się do tego obiektu sink w innym miejscu w konfiguracji rozszerzenia.
type | Typ ujścia, które zostały zdefiniowane. Określa inne wartości (jeśli istnieją) w przypadku wystąpienia tego typu.

Wersję 3.0 rozszerzenia diagnostycznego systemu Linux obsługuje dwa typy ujścia: Centrum zdarzeń i JsonBlob.

#### <a name="the-eventhub-sink"></a>Obiekt sink usługi EventHub

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

Wpis "adresie sasURL" zawiera pełny adres URL, uwzględniający tokenu sygnatury dostępu Współdzielonego Centrum zdarzeń, do którego powinny być publikowane dane. LAD wymaga sygnatury dostępu Współdzielonego nazewnictwa zasad, która umożliwia wysyłania oświadczenia. Przykład:

* Tworzenie przestrzeni nazw usługi Event Hubs o nazwie `contosohub`
* Tworzenie Centrum zdarzeń w przestrzeni nazw o nazwie `syslogmsgs`
* Tworzenie zasad dostępu współdzielonego w Centrum zdarzeń, o nazwie `writer` umożliwiającej oświadczenia wysyłania

Jeśli utworzono sygnaturę dostępu Współdzielonego dobre aż do północy czasu UTC w dniu 1 stycznia 2018, wartość adresie sasURL może być:

```url
https://contosohub.servicebus.windows.net/syslogmsgs?sr=contosohub.servicebus.windows.net%2fsyslogmsgs&sig=xxxxxxxxxxxxxxxxxxxxxxxxx&se=1514764800&skn=writer
```

Aby uzyskać więcej informacji na temat generowania tokenów sygnatur dostępu Współdzielonego dla usługi Event Hubs, zobacz [tę stronę sieci web](../../event-hubs/event-hubs-authentication-and-security-model-overview.md).

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

Dane kierowane do ujścia JsonBlob jest przechowywane w obiektach BLOB w usłudze Azure storage. Każde wystąpienie LAD tworzy obiekt blob, co godzinę dla każdej nazwy ujścia. Każdy obiekt blob jest zawsze zawiera nieprawidłową składnię tablicę JSON obiektu. Nowe wpisy niepodzielne są dodawane do tablicy. Obiekty BLOB są przechowywane w kontenerze o takiej samej nazwie jako obiekt sink. Zasady usługi Azure storage dla nazwy kontenera obiektów blob dotyczą nazwy ujścia JsonBlob: od 3 do 63 małych znaków alfanumerycznych ASCII lub kreski.

## <a name="public-settings"></a>Ustawienia publicznego

Ta struktura zawiera różne bloki sterujące informacje zebrane przez rozszerzenie. Każde ustawienie jest opcjonalne. Jeśli określisz `ladCfg`, należy także określić `StorageAccount`.

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
StorageAccount | Nazwa konta magazynu, w którym dane są zapisywane przez rozszerzenie. Musi być tej samej nazwie, jak określono w [ustawieniach chronionego](#protected-settings).
mdsdHttpProxy | (opcjonalnie) Podobnie jak w [ustawieniach chronionego](#protected-settings). Wartość publicznego jest zastępowany przez wartość prywatnego, jeśli ustawiona. Umieść ustawienia serwera proxy, które zawierają klucz tajny, takie jak hasła, w [ustawieniach chronionego](#protected-settings).

Wszystkie pozostałe elementy są opisane szczegółowo w poniższych sekcjach.

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

Ten formantów opcjonalnych struktury wychwytywanie zbieranie metryk i dzienników dostarczania w usłudze Azure metryki i inne dane. Należy określić `performanceCounters` lub `syslogEvents` lub obu. Należy określić `metrics` struktury.

Element | Wartość
------- | -----
eventVolume | (opcjonalnie) Określa liczbę partycji tworzonych w tabeli magazynu. Musi być jednym z `"Large"`, `"Medium"`, lub `"Small"`. Jeśli nie zostanie określony, wartością domyślną jest `"Medium"`.
sampleRateInSeconds | (opcjonalnie) Domyślny interwał między kolekcję pierwotnych metryki (unaggregated). Najmniejsza obsługiwana próbkowania wynosi 15 sekund. Jeśli nie zostanie określony, wartością domyślną jest `15`.

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
resourceId | Identyfikator zasobu usługi Azure Resource Manager, maszyn wirtualnych lub skalowania maszyn wirtualnych zestawu, do której należy maszyna wirtualna. To ustawienie musi być także określona, jeśli dowolnego ujścia JsonBlob jest używany w konfiguracji.
scheduledTransferPeriod | Częstotliwość, z jaką metryki agregacji są obliczane i przekazywane do metryk usługi Azure, wyrażone jako przedział czasu jest 8601. Najmniejszy czas transferu jest 60 sekund, czyli PT1M. Należy określić co najmniej jeden okres zaplanowanego transferu.

Przykłady metryki określone w sekcji liczniki wydajności są zbierane, co 15 sekund lub na przykład ocenić jawnie zdefiniowany dla licznika. Jeśli wiele częstotliwości okres zaplanowanego transferu na liście (tak jak w przykładzie), każda agregacji jest obliczana niezależnie.

#### <a name="performancecounters"></a>Liczniki wydajności

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

Ta opcjonalna sekcja kontrolę nad zbieraniem metryki. Przykłady pierwotne są agregowane dla wszystkich [okres zaplanowanego transferu](#metrics) do tworzenia tych wartości:

* Średnia
* minimalnie
* maksymalnie
* wartości zbierane przez ostatnie
* Liczba próbek raw, używany do obliczania agregacji

Element | Wartość
------- | -----
Wychwytywanie | (opcjonalnie) Rozdzielana przecinkami lista nazw ujścia, które LAD wysyła zagregowaną metryki wyników. Wszystkie zagregowane metryki są publikowane w każdej z wymienionych ujścia. Zobacz [sinksConfig](#sinksconfig). Przykład: `"EHsink1, myjsonsink"`.
type | Określa dostawcę rzeczywiste metryki.
klasa | Wraz z "counter" identyfikuje określone metryki w przestrzeni nazw dostawcy.
counter | Wraz z "class" identyfikuje określone metryki w przestrzeni nazw dostawcy.
counterSpecifier | Identyfikuje określone metryki w obszarze nazw metryk usługi Azure.
warunek | (opcjonalnie) Wybiera konkretne wystąpienie obiektu do którego stosuje metrykę, lub wybiera agregacji ze wszystkich wystąpień tego obiektu. Aby uzyskać więcej informacji, zobacz `builtin` definicje metryk.
sampleRate | JEST interwałem 8601, który ustawia szybkość jaką pierwotne próbki dla tej metryki są pobierane. Jeśli nie został ustawiony, interwał zbierania jest ustawiony przez wartość [sampleRateInSeconds](#ladcfg). Najkrótszy częstotliwość próbkowania obsługiwanych wynosi 15 sekund (PT15S).
jednostka | Powinna być jedną z tych ciągów: "Liczba", "B", "S", "Procent", "CountPerSecond", "BytesPerSecond", "Milisekund". Definiuje jednostkę metryki. Osoby korzystające z danych zebranych oczekiwać, że wartości zebranych danych do potrzeb tej jednostki. LAD ignoruje tego pola.
displayName | Etykieta (w języku określonym przez ustawienie regionalne skojarzone) do podłączenia do tych danych w metryk usługi Azure. LAD ignoruje tego pola.

CounterSpecifier jest umownym identyfikatorem. Konsumentów metryk, takich jak Azure portal wykresów i alertów funkcji, użyj counterSpecifier jako "key" identyfikująca metrykę lub wystąpienie metryki. Aby uzyskać `builtin` metryki, zalecane jest użycie counterSpecifier wartości, które zaczynają się od `/builtin/`. Jeśli zbierasz konkretnego wystąpienia metrykę, zalecamy Dołącz identyfikator wystąpienia wartości counterSpecifier. Oto niektóre przykłady:

* `/builtin/Processor/PercentIdleTime` -Bezczynności we wszystkich procesorów wirtualnych Vcpu
* `/builtin/Disk/FreeSpace(/mnt)` — Wolne miejsce do katalogu/mnt systemu plików
* `/builtin/Disk/FreeSpace` -Miejsca we wszystkich zainstalowanych systemów plików

LAD ani w witrynie Azure portal oczekuje, że wartość counterSpecifier, aby dopasować wszelkie wzorzec. Zachowaj spójność w sposobie konstruowania counterSpecifier wartości.

Po określeniu `performanceCounters`, LAD zawsze zapisuje dane do tabeli w usłudze Azure storage. Może mieć te same dane, które są zapisywane do obiektów blob JSON i/lub usługi Event Hubs, ale nie można wyłączyć, zapisywanie danych w tabeli. Wszystkie wystąpienia elementu rozszerzenie diagnostyki jest skonfigurowany do używania tej samej nazwy konta magazynu i punktu końcowego, dodaj ich metryk i dzienników do tej samej tabeli. Jeśli zbyt wiele maszyn wirtualnych będą zapisywane do tej samej partycji tabeli, Azure ograniczać operacje zapisu do tej partycji. Ustawienie eventVolume powoduje, że wpisy rozprzestrzeniania się między 1 (małego), 10 (średni) lub 100 (duży) różnych partycji. "Średnia" oznacza zwykle wystarczające, aby upewnić się, że nie jest ograniczany ruch sieciowy. Funkcja metryk usługi Azure w witrynie Azure Portal wykorzystuje dane w tej tabeli, tworzyć wykresy lub wyzwolenia alertów. Nazwa tabeli jest łączenia tych ciągów:

* `WADMetrics`
* "Okres zaplanowanego transferu" dla wartości zagregowane, przechowywane w tabeli
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

Ta opcjonalna sekcja kontrolę nad zbieraniem dzienników zdarzeń z dziennika systemu. W przypadku pominięcia sekcji zdarzenia dziennika systemowego nie są przechwytywane w ogóle.

Kolekcja syslogEventConfiguration ma jeden wpis dla każdego obiektu syslog zainteresowania. Jeśli minSeverity "NONE" dla konkretnego pomieszczenia lub tej funkcji nie pojawia się w elemencie wcale, są przechwytywane żadne zdarzenia z tej funkcji.

Element | Wartość
------- | -----
Wychwytywanie | Rozdzielana przecinkami lista nazw ujścia, do których osobny dziennik zdarzenia są publikowane. Wszystkie zdarzenia dziennika spełniające warunki ograniczenia określone w syslogEventConfiguration są publikowane w każdej z wymienionych ujścia. Przykład: "EHforsyslog"
facilityName | Nazwę funkcji dziennika systemowego (takie jak "dziennik\_użytkownika" lub "LOG\_LOCAL0"). Zobacz sekcję "funkcji" [strony ataków typu man syslog](http://man7.org/linux/man-pages/man3/syslog.3.html) poznania pełnej listy.
minSeverity | Poziom ważności syslog (takich jak "dziennik\_ERR" lub "LOG\_informacje"). Zobacz sekcję "level" [strony ataków typu man syslog](http://man7.org/linux/man-pages/man3/syslog.3.html) poznania pełnej listy. Rozszerzenie rejestruje zdarzeń wysyłanych do funkcji lub być powyżej określonego poziomu.

Po określeniu `syslogEvents`, LAD zawsze zapisuje dane do tabeli w usłudze Azure storage. Może mieć te same dane, które są zapisywane do obiektów blob JSON i/lub usługi Event Hubs, ale nie można wyłączyć, zapisywanie danych w tabeli. Partycjonowania zachowanie dla tej tabeli jest taka sama, jak opisano dla `performanceCounters`. Nazwa tabeli jest łączenia tych ciągów:

* `LinuxSyslog`
* Data, w postaci "RRRRMMDD", która zmienia co 10 dni

Przykłady obejmują `LinuxSyslog20170410` i `LinuxSyslog20170609`.

### <a name="perfcfg"></a>perfCfg

Ta opcjonalna sekcja kontroluje wykonywanie dowolnego [OMI](https://github.com/Microsoft/omi) zapytania.

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
przestrzeń nazw | (opcjonalnie) Przestrzeń nazw OMI, w którym mają zostać wykonane zapytanie. Jeśli nie zostanie podany, wartość domyślna to "główny/scx", implementowany przez [programu System Center dla wielu platform dostawców](https://scx.codeplex.com/wikipage?title=xplatproviders&referringTitle=Documentation).
query | Zapytanie OMI do wykonania.
tabela | (opcjonalnie) W tabeli usługi Azure storage w ramach konta magazynu wyznaczonego (zobacz [ustawieniach chronionego](#protected-settings)).
frequency | (opcjonalnie) Liczba sekund między wykonanie zapytania. Wartość domyślna to 300 (5 minut); wartość minimalna wynosi 15 sekund.
Wychwytywanie | (opcjonalnie) Rozdzielana przecinkami lista nazw dodatkowe ujścia, do których przykładowe nieprzetworzone wyniki metryki powinny być publikowane. Brak agregacji nieprzetworzonych przykładów jest obliczana przez rozszerzenie lub metryk usługi Azure.

"Tabela" lub "sink" albo obu, należy określić.

### <a name="filelogs"></a>fileLogs

Steruje przechwytywania plików dziennika. LAD przechwytuje nowych wierszy tekstu, ponieważ są one zapisywane do pliku i zapisuje je do wierszy tabeli i/lub dowolnym określonym ujść (JsonBlob lub Centrum zdarzeń).

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
plik | Pełna nazwa ścieżki pliku dziennika, aby być obserwowane i przechwycone. Nazwa ścieżki należy nazywać pojedynczego pliku; Nie można jej nazwa katalogu lub zawierać symbole wieloznaczne.
tabela | (opcjonalnie) Tabela magazynu platformy Azure w ramach konta magazynu wyznaczonego (określone w konfiguracji chronionych), w którym są zapisywane nowe wiersze z pliku "ogona".
Wychwytywanie | (opcjonalnie) Rozdzielana przecinkami lista nazw ujść dodatkowych wierszy dziennika, które wysłane.

"Tabela" lub "sink" albo obu, należy określić.

## <a name="metrics-supported-by-the-builtin-provider"></a>Obsługiwane przez dostawcę builtin metryki

Dostawca metryki builtin jest źródło metryki najbardziej interesujących do szerokiego zestawu użytkowników. Tych metryk można podzielić na pięć szerokiego klas:

* Procesor
* Memory (Pamięć)
* Sieć
* System plików
* Dysk

### <a name="builtin-metrics-for-the-processor-class"></a>wbudowane metryki dla klasy procesora

Klasa procesora, metryk udostępnia informacje na temat użycia procesora na maszynie wirtualnej. Podczas agregowania wartości procentowych, wynik jest średnią dla wszystkich procesorów CPU. W procesorów vCPU dwóch maszyn wirtualnych Jeśli jeden procesor wirtualny był zajęty w 100%, a drugie wartość 100% bezczynny, PercentIdleTime zgłaszane jest 50. Jeśli każdy procesor wirtualny był zajęty, w tym samym okresie w 50%, zgłoszony wynik będą również 50. Na maszynie wirtualnej 4 vCPU, jeden procesor wirtualny 100% zajęty i innym osobom bezczynny zgłaszane PercentIdleTime byłoby 75.

counter | Znaczenie
------- | -------
PercentIdleTime | Wartość procentowa czasu przedziale agregacji, że procesorów zostały wykonywania pętli bezczynności jądra
PercentProcessorTime | Wartość procentowa czasu wykonania wątku czynnego
PercentIOWaitTime | Wartość procentowa czasu oczekiwania na zakończenie operacji We/Wy
PercentInterruptTime | Wartość procentowa czasu wykonywania, sprzętu i oprogramowania przerwań i wywołań DPC (opóźnione wywołania procedur)
PercentUserTime | Czynnego czasu przedziale agregacji procent czasu spędzony w użytkownika bardziej przy normalnym priorytecie
PercentNiceTime | Czas czynnego procent poświęconego skróconego priorytetem (nieuprzywilejowany)
PercentPrivilegedTime | Czas czynnego procent działania w trybie uprzywilejowanym (jądra)

Pierwsze cztery liczniki należy zsumować do 100%. Ostatnie trzy liczniki również Suma do 100%; Suma PercentProcessorTime PercentIOWaitTime i PercentInterruptTime oni podzielić.

Aby uzyskać jedną metrykę agregowane dla wszystkich procesorów, należy ustawić `"condition": "IsAggregate=TRUE"`. Aby uzyskać metryki dla określonemu procesorowi, takich jak drugi procesor logiczny procesor vCPU cztery maszyny wirtualnej, należy ustawić `"condition": "Name=\\"1\\""`. Liczby procesorów logicznych znajdują się w zakresie `[0..n-1]`.

### <a name="builtin-metrics-for-the-memory-class"></a>wbudowane metryki dla klasy pamięci

Klasa pamięci metryk zawiera informacje dotyczące wykorzystanie pamięci, stronicowanie i zamianę.

counter | Znaczenie
------- | -------
AvailableMemory | Dostępnej pamięci fizycznej w MiB
PercentAvailableMemory | Dostępna pamięć fizyczna jako procent całkowitej ilości pamięci
UsedMemory | W użyciu pamięć fizyczna (MiB)
PercentUsedMemory | W użyciu pamięć fizyczna jako procent całkowitej ilości pamięci
PagesPerSec | Łączna liczba stronicowania (odczyt/zapis)
PagesReadPerSec | Strony odczytane z kopii magazynu (pliku wymiany, pliku programu, pliku mapowanego itp.)
PagesWrittenPerSec | Strony zapisywane w magazynie pomocniczym (pliku wymiany, pliku mapowanego itp.)
AvailableSwap | Obszar wymiany nieużywane (MiB)
PercentAvailableSwap | Obszar wymiany nieużywane jako wartości procentowej całkowitego obszaru wymiany
UsedSwap | W użyciu obszar wymiany (MiB)
PercentUsedSwap | W użyciu obszaru wymiany w procentach całkowitego obszaru wymiany

Ta klasa metryki ma tylko jedno wystąpienie. Atrybut "warunek" nie zawiera żadnych ustawień przydatne i powinien zostać pominięty.

### <a name="builtin-metrics-for-the-network-class"></a>wbudowane metryki dla klasy sieciowe

Klasa sieci metryk informacje dotyczące aktywności sieciowej w interfejsach sieciowych poszczególnych od rozruchu. LAD nie ujawnia metryk przepustowości, które mogą być pobierane z metryki hosta.

counter | Znaczenie
------- | -------
BytesTransmitted | Całkowita liczba bajtów wysłanych od momentu rozruchu
BytesReceived | Suma bajtów odebranych od rozruchu
BytesTotal | Całkowita liczba bajtów wysłanych lub odebranych od rozruchu
PacketsTransmitted | Łączna liczba pakietów wysłanych od momentu rozruchu
PacketsReceived | Łączna liczba pakietów otrzymanych od rozruchu
TotalRxErrors | Liczba błędów od rozruchu
TotalTxErrors | Liczba transmisji błędów od rozruchu
TotalCollisions | Liczby kolizji zgłoszone przez porty sieciowe od rozruchu

 Mimo że ta klasa jest wystąpienia, LAD nie obsługuje przechwytywanie metryki sieci agregowana dla wszystkich urządzeń sieciowych. Aby uzyskać metryki dla określonego interfejsu, takich jak eth0, należy ustawić `"condition": "InstanceID=\\"eth0\\""`.

### <a name="builtin-metrics-for-the-filesystem-class"></a>wbudowane metryki dla klasy systemu plików

Klasa systemu plików, metryk udostępnia informacje na temat użycia systemu plików. Bezwzględna i procentowa wartości ma być zgłaszane będzie można wyświetlić do zwykłego użytkownika (nie root).

counter | Znaczenie
------- | -------
FreeSpace | Dostępnego miejsca na dysku w bajtach
UsedSpace | Używane miejsce na dysku w bajtach
PercentFreeSpace | Wartość procentowa wolnego miejsca.
PercentUsedSpace | Procent wykorzystania miejsca
PercentFreeInodes | Procent węzłów i nieużywanych
PercentUsedInodes | Procent przydzielonych (w użyciu) węzłów i sumowane w wszystkie systemy plików
BytesReadPerSecond | Odczytano bajtów na sekundę
BytesWrittenPerSecond | Bajty zapisane na sekundę
Bajty na sekundę | Bajty odczytu lub zapisu na sekundę
ReadsPerSecond | Operacje odczytu na sekundę
WritesPerSecond | Zapisu na sekundę
TransfersPerSecond | Operacje odczytu lub zapisu na sekundę

Zagregowane wartości we wszystkich systemach plików można uzyskać przez ustawienie `"condition": "IsAggregate=True"`. Wartości dla określonego zainstalowany system plików, takich jak "/ mnt", można uzyskać przez ustawienie `"condition": 'Name="/mnt"'`. 

**UWAGA**: Jeśli przy użyciu witryny Azure Portal zamiast JSON, poprawny stan formularza pole jest nazwa = "/ mnt"

### <a name="builtin-metrics-for-the-disk-class"></a>wbudowane metryki dla klasy dysku

Klasa dysku metryk udostępnia informacje na temat użycia urządzenia dysku. Te statystyki mają zastosowanie do całego dysku. W przypadku wielu systemów plików na urządzeniu, liczniki dla tego urządzenia to w rzeczywistości agregowane w obrębie wszystkich z nich.

counter | Znaczenie
------- | -------
ReadsPerSecond | Operacje odczytu na sekundę
WritesPerSecond | Zapisu na sekundę
TransfersPerSecond | Łączna liczba operacji na sekundę
AverageReadTime | Średnia liczba sekund na operacje odczytu
AverageWriteTime | Średnia liczba sekund na operację zapisu
AverageTransferTime | Średnia liczba sekund na operację
AverageDiskQueueLength | Średnia liczba operacji w kolejce dysku
ReadBytesPerSecond | Liczba odczytanych bajtów na sekundę
WriteBytesPerSecond | Liczba bajtów zapisanych na sekundę
Bajty na sekundę | Liczba bajtów odczytywanych lub zapisywanych na sekundę

Zagregowane wartości dla wszystkich dysków można uzyskać przez ustawienie `"condition": "IsAggregate=True"`. Aby uzyskać informacje dotyczące określonego urządzenia (na przykład/dev/sdf1), należy ustawić `"condition": "Name=\\"/dev/sdf1\\""`.

## <a name="installing-and-configuring-lad-30-via-cli"></a>Instalowanie i konfigurowanie LAD 3.0 za pomocą interfejsu wiersza polecenia

Przy założeniu, że w ustawieniach chronionego znajdują się w pliku PrivateConfig.json i informacje o konfiguracji publicznej znajduje się w PublicConfig.json, uruchom następujące polecenie:

```azurecli
az vm extension set *resource_group_name* *vm_name* LinuxDiagnostic Microsoft.Azure.Diagnostics '3.*' --private-config-path PrivateConfig.json --public-config-path PublicConfig.json
```

Polecenia przyjęto założenie, że używany jest tryb usługi Azure Resource Management (arm) z wiersza polecenia platformy Azure. Można skonfigurować LAD dla wdrożenia klasycznego modelu maszyn wirtualnych (ASM), Przełącz do trybu "asm" (`azure config mode asm`) i pominąć nazwę grupy zasobów w poleceniu. Aby uzyskać więcej informacji, zobacz [dokumentacji interfejsu wiersza polecenia dla wielu platform](https://docs.microsoft.com/azure/xplat-cli-connect).

## <a name="an-example-lad-30-configuration"></a>LAD 3.0 przykładową konfigurację

Na podstawie poprzedniego definicji, Oto przykładowa konfiguracja rozszerzenia LAD 3.0 z niektórych wyjaśnienie. Aby zastosować w tym przykładzie w danym przypadku, należy użyć własne nazwy konta magazynu, token sygnatury dostępu Współdzielonego konta i tokeny sygnatur dostępu Współdzielonego EventHubs.

### <a name="privateconfigjson"></a>PrivateConfig.json

Skonfiguruj te ustawienia prywatnego:

* Konto magazynu
* zgodnego tokenu sygnatury dostępu Współdzielonego konta
* kilka ujść (JsonBlob lub EventHubs przy użyciu tokenów sygnatur dostępu Współdzielonego)

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

Te ustawienia publicznych powodują LAD do:

* Metryki procent procesora w czasie i używać miejsca na dysku, aby przekazać `WADMetrics*` tabeli
* Przekazywanie komunikatów z usługi syslog funkcji "user" i ważność "info" do `LinuxSyslog*` tabeli
* Przekaż nieprzetworzone wyniki zapytania OMI (PercentProcessorTime i PercentIdleTime) do nazwanego `LinuxCPU` tabeli
* Przekaż dołączonych wierszy w pliku `/var/log/myladtestlog` do `MyLadTestLog` tabeli

W obu przypadkach dane również są przekazywane do:

* Usługa Azure Blob storage (nazwa kontenera jest zgodnie z definicją w ujściu JsonBlob)
* Końcowy EventHubs (jak określono w ujściu EventHubs)

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

`resourceId` w konfiguracji muszą być zgodne, maszyna wirtualna lub skalowania maszyn wirtualnych zestawu.

* Metryki platformy Azure, tworzenie wykresów i alertów wie resourceId pracy maszyny wirtualnej. Oczekuje znaleźć dane dla maszyny Wirtualnej przy użyciu resourceId klucz wyszukiwania.
* Jeśli używasz skalowania automatycznego platformy Azure, resourceId w konfiguracji skalowania automatycznego musi odpowiadać resourceId posługują się LAD.
* ResourceId jest wbudowana w nazwach JsonBlobs napisane przez LAD.

## <a name="view-your-data"></a>Wyświetlanie danych

Aby wyświetlić dane dotyczące wydajności lub ustawić alerty, należy użyć witryny Azure portal:

![image](./media/diagnostics-linux/graph_metrics.png)

`performanceCounters` Danych zawsze są przechowywane w tabeli usługi Azure Storage. Interfejsy API usługi Azure Storage są dostępne dla wielu języków i platform.

Dane wysyłane do ujścia JsonBlob są przechowywane w obiektach blob na koncie magazynu o nazwie w [ustawieniach chronionego](#protected-settings). Mogą wykorzystywać danych obiektów blob za pomocą interfejsów API dowolnej usługi Azure Blob Storage.

Ponadto można użyć tych narzędzi interfejsu użytkownika, dostęp do danych w usłudze Azure Storage:

* Visual Studio Server Explorer.
* [Microsoft Azure Storage Explorer](https://azurestorageexplorer.codeplex.com/ "Eksplorator usługi Azure Storage").

Ta migawka sesji programu Microsoft Azure Storage Explorer pokazuje wygenerowanego tabele usługi Azure Storage i kontenery z rozszerzeniem LAD 3.0 poprawnie skonfigurowana na testowej maszynie Wirtualnej. Obraz, który nie jest zgodna z [Przykładowa konfiguracja LAD 3.0](#an-example-lad-30-configuration).

![image](./media/diagnostics-linux/stg_explorer.png)

Zobacz odpowiednią [dokumentacji EventHubs](../../event-hubs/event-hubs-what-is-event-hubs.md) dowiesz się, jak używać komunikaty opublikowane do punktu końcowego usługi EventHubs.

## <a name="next-steps"></a>Kolejne kroki

* Tworzenie alertów dotyczących metryk w [usługi Azure Monitor](../../monitoring-and-diagnostics/insights-alerts-portal.md) dotyczące metryk są zbierane.
* Tworzenie [wykresy monitorowania](../../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md) dla metryk.
* Dowiedz się, jak [Tworzenie zestawu skalowania maszyn wirtualnych](../linux/tutorial-create-vmss.md) przy użyciu metryk do kontrolowania skalowania automatycznego.
