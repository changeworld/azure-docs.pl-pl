---
title: Obliczeń platformy Azure — rozszerzenie diagnostyczne systemu Linux
description: Jak skonfigurować rozszerzenie diagnostyczne systemu Azure Linux (LAD) do zbierania metryk i rejestrowania zdarzeń z maszyn wirtualnych systemu Linux uruchomionych na platformie Azure.
services: virtual-machines-linux
author: axayjo
manager: gwallace
ms.service: virtual-machines-linux
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 12/13/2018
ms.author: akjosh
ms.openlocfilehash: 7a7c1af1193ba391550438229a22c4a8c116e6be
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80289179"
---
# <a name="use-linux-diagnostic-extension-to-monitor-metrics-and-logs"></a>Używanie rozszerzenia diagnostycznego systemu Linux do monitorowania metryk i dzienników

W tym dokumencie opisano wersję 3.0 i nowszą rozszerzenie diagnostyczne systemu Linux.

> [!IMPORTANT]
> Informacje o wersji 2.3 i starszej można znaleźć w [tym dokumencie](../linux/classic/diagnostic-extension-v2.md).

## <a name="introduction"></a>Wprowadzenie

Rozszerzenie diagnostyczne systemu Linux pomaga użytkownikowi monitorować kondycję maszyny Wirtualnej systemu Linux uruchomionej na platformie Microsoft Azure. Posiada następujące możliwości:

* Zbiera metryki wydajności systemu z maszyny Wirtualnej i przechowuje je w określonej tabeli na wyznaczonym koncie magazynu.
* Pobiera zdarzenia dziennika z syslog i przechowuje je w określonej tabeli na wyznaczonym koncie magazynu.
* Umożliwia użytkownikom dostosowanie metryk danych, które są zbierane i przekazywane.
* Umożliwia użytkownikom dostosowanie obiektów syslog i poziomy ważności zdarzeń, które są zbierane i przekazywane.
* Umożliwia użytkownikom przekazywanie określonych plików dziennika do wyznaczonej tabeli magazynu.
* Obsługuje wysyłanie metryk i rejestrowania zdarzeń do dowolnych punktów końcowych EventHub i obiektów blob w formacie JSON na wyznaczonym koncie magazynu.

To rozszerzenie działa z obu modeli wdrażania platformy Azure.

## <a name="installing-the-extension-in-your-vm"></a>Instalowanie rozszerzenia na maszynie wirtualnej

To rozszerzenie można włączyć przy użyciu poleceń cmdlet programu Azure PowerShell, skryptów interfejsu wiersza polecenia platformy Azure, szablonów ARM lub witryny Azure portal. Aby uzyskać więcej informacji, zobacz [Funkcje rozszerzeń](features-linux.md).

Te instrukcje instalacji i [przykładowa konfiguracja do pobrania](https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json) konfigurują LAD 3.0 na:

* przechwytywanie i przechowywanie tych samych wskaźników, które zostały dostarczone przez LAD 2.3;
* przechwytywanie użytecznego zestawu metryk systemu plików, nowy w LAD 3.0;
* przechwytywanie domyślnej kolekcji syslog włączonej przez LAD 2.3;
* włącz środowisko witryny Azure portal do tworzenia wykresów i alertów na metrykach maszyn wirtualnych.

Konfiguracja do pobrania jest tylko przykładem; zmodyfikować go zgodnie z własnymi potrzebami.

### <a name="prerequisites"></a>Wymagania wstępne

* **Usługa Azure Linux Agent w wersji 2.2.0 lub nowszej**. Większość obrazów galerii maszyn wirtualnych systemu Azure z systemem Linux zawiera wersję 2.2.7 lub nowszą. Uruchom, `/usr/sbin/waagent -version` aby potwierdzić wersję zainstalowaną na maszynie Wirtualnej. Jeśli maszyna wirtualna jest uruchomiona starsza wersja agenta gościa, postępuj zgodnie z [tymi instrukcjami,](https://docs.microsoft.com/azure/virtual-machines/linux/update-agent) aby ją zaktualizować.
* **Narzędzie CLI platformy Azure**. [Konfigurowanie](https://docs.microsoft.com/cli/azure/install-azure-cli) środowiska interfejsu wiersza polecenia platformy Azure na komputerze.
* Polecenie wget, jeśli jeszcze go nie masz: Uruchom `sudo apt-get install wget`.
* Istniejąca subskrypcja platformy Azure i istniejące konto magazynu w nim do przechowywania danych.
* Lista obsługiwanych dystrybucji Linuksa jest whttps://github.com/Azure/azure-linux-extensions/tree/master/Diagnostic#supported-linux-distributions

### <a name="sample-installation"></a>Przykładowa instalacja

Przed uruchomieniem wprowadź poprawne wartości zmiennych w pierwszej sekcji:

```azurecli
# Set your Azure VM diagnostic variables correctly below
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

Przykładowa konfiguracja pobrana w tych przykładach zbiera zestaw standardowych danych i wysyła je do magazynu tabel. Adres URL przykładowej konfiguracji i jego zawartości może ulec zmianie. W większości przypadków należy pobrać kopię pliku JSON ustawień portalu i dostosować go do swoich potrzeb, a następnie mieć szablony lub automatyzacji konstruować użyć własnej wersji pliku konfiguracyjnego, a nie pobieranie tego adresu URL za każdym razem.

#### <a name="powershell-sample"></a>Przykładowy skrypt programu PowerShell

```powershell
$storageAccountName = "yourStorageAccountName"
$storageAccountResourceGroup = "yourStorageAccountResourceGroupName"
$vmName = "yourVMName"
$VMresourceGroup = "yourVMResourceGroupName"

# Get the VM object
$vm = Get-AzVM -Name $vmName -ResourceGroupName $VMresourceGroup

# Get the public settings template from GitHub and update the templated values for storage account and resource ID
$publicSettings = (Invoke-WebRequest -Uri https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json).Content
$publicSettings = $publicSettings.Replace('__DIAGNOSTIC_STORAGE_ACCOUNT__', $storageAccountName)
$publicSettings = $publicSettings.Replace('__VM_RESOURCE_ID__', $vm.Id)

# If you have your own customized public settings, you can inline those rather than using the template above: $publicSettings = '{"ladCfg":  { ... },}'

# Generate a SAS token for the agent to use to authenticate with the storage account
$sasToken = New-AzStorageAccountSASToken -Service Blob,Table -ResourceType Service,Container,Object -Permission "racwdlup" -Context (Get-AzStorageAccount -ResourceGroupName $storageAccountResourceGroup -AccountName $storageAccountName).Context

# Build the protected settings (storage account SAS token)
$protectedSettings="{'storageAccountName': '$storageAccountName', 'storageAccountSasToken': '$sasToken'}"

# Finally install the extension with the settings built above
Set-AzVMExtension -ResourceGroupName $VMresourceGroup -VMName $vmName -Location $vm.Location -ExtensionType LinuxDiagnostic -Publisher Microsoft.Azure.Diagnostics -Name LinuxDiagnostic -SettingString $publicSettings -ProtectedSettingString $protectedSettings -TypeHandlerVersion 3.0 
```

### <a name="updating-the-extension-settings"></a>Aktualizowanie ustawień rozszerzenia

Po zmianie ustawień chronionych lub publicznych należy wdrożyć je na maszynie wirtualnej, uruchamiając to samo polecenie. Jeśli coś się zmieniło w ustawieniach, zaktualizowane ustawienia są wysyłane do rozszerzenia. LAD ponownie ładuje konfigurację i uruchamia się ponownie.

### <a name="migration-from-previous-versions-of-the-extension"></a>Migracja z poprzednich wersji rozszerzenia

Najnowsza wersja rozszerzenia to **3.0**. **Wszystkie stare wersje (2.x) są przestarzałe i mogą zostać nieopublikowane w dniu 31 lipca 2018**r. lub później .

> [!IMPORTANT]
> To rozszerzenie wprowadza przełomowe zmiany w konfiguracji rozszerzenia. Jedna z takich zmian została wszczęła w celu poprawy bezpieczeństwa rozszerzenia; w rezultacie nie można było zachować wstecznej zgodności z 2.x. Ponadto wydawca rozszerzeń dla tego rozszerzenia różni się od wydawcy dla wersji 2.x.
>
> Aby przeprowadzić migrację z wersji 2.x do tej nowej wersji rozszerzenia, należy odinstalować stare rozszerzenie (pod nazwą starego wydawcy), a następnie zainstalować wersję 3 rozszerzenia.

Zalecenia:

* Zainstaluj rozszerzenie z włączoną automatyczną aktualizacją wersji pomocniczej.
  * Na klasycznych maszynach wirtualnych modelu wdrażania określ "3.*" jako wersję, jeśli instalujesz rozszerzenie za pośrednictwem interfejsu wiersza polecenia platformy Azure XPLAT lub programu Powershell.
  * Na maszynach wirtualnych modelu wdrażania usługi Azure Resource Manager dołącz ""autoUpgradeMinorVersion": true" w szablonie wdrażania maszyny Wirtualnej.
* Użyj nowego/innego konta magazynu dla LAD 3.0. Istnieje kilka małych niezgodności między LAD 2.3 i LAD 3.0, które sprawiają, że udostępnianie konta kłopotliwe:
  * LAD 3.0 przechowuje zdarzenia syslog w tabeli o innej nazwie.
  * Ciągi counterSpecifier `builtin` dla metryk różnią się lad 3.0.

## <a name="protected-settings"></a>Ustawienia chronione

Ten zestaw informacji o konfiguracji zawiera poufne informacje, które powinny być chronione przed widokiem publicznym, na przykład poświadczenia magazynu. Te ustawienia są przesyłane do i przechowywane przez rozszerzenie w postaci zaszyfrowanej.

```json
{
    "storageAccountName" : "the storage account to receive data",
    "storageAccountEndPoint": "the hostname suffix for the cloud for this account",
    "storageAccountSasToken": "SAS access token",
    "mdsdHttpProxy": "HTTP proxy settings",
    "sinksConfig": { ... }
}
```

Nazwa | Wartość
---- | -----
storageAccountName | Nazwa konta magazynu, na którym dane są zapisywane przez rozszerzenie.
magazynAccountEndPoint | (opcjonalnie) Punkt końcowy identyfikujący chmurę, w której istnieje konto magazynu. Jeśli to ustawienie jest nieobecne, lad domyślnie do chmury publicznej platformy Azure, `https://core.windows.net`. Aby użyć konta magazynu na platformie Azure w Niemczech, usłudze Azure Dla Instytucji Rządowych lub w Chinach platformy Azure, należy odpowiednio ustawić tę wartość.
pamięć przechowywanieKonkontaSasToken | [Token Sygnatury dostępu Współdzielonego](https://azure.microsoft.com/blog/sas-update-account-sas-now-supports-all-storage-services/) konta dla usług obiektów Blob i Table (`ss='bt'`), mający zastosowanie do kontenerów i obiektów (`srt='co'`), który przyznaje uprawnienia do dodawania, tworzenia, listy, aktualizacji i zapisu (`sp='acluw'`). *Nie* należy dołączać wiodącego znaku zapytania (?).
mdsdHttpProxy | (opcjonalnie) Informacje o serwerze proxy HTTP potrzebne do włączenia rozszerzenia do łączenia się z określonym kontem magazynu i punktem końcowym.
umywalkiConfig | (opcjonalnie) Szczegółowe informacje o alternatywnych miejscach docelowych, do których mogą być dostarczane dane i zdarzenia. Szczegółowe informacje o każdym ujściu danych obsługiwane przez rozszerzenie są omówione w sekcjach, które następują.

Aby uzyskać token sygnatury dostępu Współdzielonego w szablonie Menedżera zasobów, użyj funkcji **listAccountSas.** Przykładowy szablon można znaleźć w [przykładzie funkcji Listy](../../azure-resource-manager/templates/template-functions-resource.md#list-example).

Za pomocą witryny Azure Portal można łatwo utworzyć wymagany token sygnatury dostępu Współdzielonego.

1. Wybierz konto magazynu ogólnego przeznaczenia, na którym ma być zapisywane rozszerzenie
1. Wybierz "Podpis dostępu współdzielonego" w części Ustawienia w menu po lewej stronie
1. Należy wykonać odpowiednie sekcje zgodnie z wcześniejszym opisem
1. Kliknij przycisk "Generuj sygnaturę dostępu Współdzielonego".

![image](./media/diagnostics-linux/make_sas.png)

Skopiuj wygenerowany sygnatury dostępu Współdzielonego do pola storageAccountSasToken; usunięcia wiodącego znaku zapytania ("?).

### <a name="sinksconfig"></a>umywalkiConfig

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

Ta opcjonalna sekcja definiuje dodatkowe miejsca docelowe, do których rozszerzenie wysyła informacje, które zbiera. Tablica "sink" zawiera obiekt dla każdego dodatkowego ujścia danych. Atrybut "type" określa inne atrybuty w obiekcie.

Element | Wartość
------- | -----
name | Ciąg używany do odwoływania się do tego ujścia w innym miejscu w konfiguracji rozszerzenia.
type | Typ zlewu jest zdefiniowany. Określa inne wartości (jeśli istnieją) w wystąpieniach tego typu.

Wersja 3.0 rozszerzenia diagnostycznego systemu Linux obsługuje dwa typy ujścia: EventHub i JsonBlob.

#### <a name="the-eventhub-sink"></a>Zlewozmywak EventHub

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

Wpis "sasURL" zawiera pełny adres URL, w tym token sygnatury dostępu Współdzielonego, dla Centrum zdarzeń, do którego powinny być publikowane dane. LAD wymaga sygnatury dostępu Sygnatury dostępu Współdzielonego zasady, która umożliwia wysyłanie oświadczenia. Przykład:

* Tworzenie obszaru nazw Centrów zdarzeń o nazwie`contosohub`
* Tworzenie Centrum zdarzeń w obszarze nazw o nazwie`syslogmsgs`
* Tworzenie zasad dostępu współdzielonego `writer` w Centrum zdarzeń o nazwie, które umożliwia oświadczenie wysyłania

Jeśli utworzono wartość sygnatury dostępu Współdzielonego do północy UTC 1 stycznia 2018 r., wartość sasURL może być:

```https
https://contosohub.servicebus.windows.net/syslogmsgs?sr=contosohub.servicebus.windows.net%2fsyslogmsgs&sig=xxxxxxxxxxxxxxxxxxxxxxxxx&se=1514764800&skn=writer
```

Aby uzyskać więcej informacji na temat generowania i pobierania informacji o tokenach sygnatury dostępu Współdzielonego dla centrów zdarzeń, zobacz [tę stronę sieci Web](https://docs.microsoft.com/rest/api/eventhub/generate-sas-token#powershell).

#### <a name="the-jsonblob-sink"></a>Zlew JsonBlob

```json
"sink": [
    {
        "name": "sinkname",
        "type": "JsonBlob"
    },
    ...
]
```

Dane kierowane do ujścia JsonBlob są przechowywane w obiektach blob w magazynie platformy Azure. Każde wystąpienie LAD tworzy obiekt blob co godzinę dla każdej nazwy ujścia. Każdy obiekt blob zawsze zawiera syntaktycznie prawidłową tablicę obiektu JSON. Nowe wpisy są niepodzielnie dodawane do tablicy. Obiekty BLOB są przechowywane w kontenerze o takiej samej nazwie jak ujście. Reguły magazynu platformy Azure dla nazw kontenerów obiektów blob mają zastosowanie do nazw pochłaniaczy JsonBlob: między 3 i 63 małe litery alfanumeryczne znaki ASCII lub myślniki.

## <a name="public-settings"></a>Ustawienia publiczne

Ta struktura zawiera różne bloki ustawień, które kontrolują informacje zebrane przez rozszerzenie. Każde ustawienie jest opcjonalne. Jeśli określisz `ladCfg`, należy `StorageAccount`również określić .

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
StorageAccount | Nazwa konta magazynu, na którym dane są zapisywane przez rozszerzenie. Musi mieć taką samą nazwę, jak określono w [ustawieniach chronionych](#protected-settings).
mdsdHttpProxy | (opcjonalnie) Tak samo jak w [ustawieniach chronionych](#protected-settings). Wartość publiczna jest zastępowane przez wartość prywatną, jeśli jest ustawiona. Umieść ustawienia serwera proxy zawierające klucz tajny, taki jak hasło, w [ustawieniach chronionych](#protected-settings).

Pozostałe elementy są szczegółowo opisane w poniższych sekcjach.

### <a name="ladcfg"></a>ladCfg ( ladCfg )

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

Ta opcjonalna struktura steruje zbieraniem metryk i dzienników do dostarczania do usługi Azure Metrics i do innych pochłaniaczy danych. Należy określić `performanceCounters` jedną `syslogEvents` lub obie. Należy określić `metrics` strukturę.

Element | Wartość
------- | -----
eventVolume | (opcjonalnie) Steruje liczbą partycji utworzonych w tabeli magazynu. Musi być `"Large"`jednym `"Medium"`z `"Small"`, lub . Jeśli nie zostanie określona, wartością domyślną jest `"Medium"`.
sampleRateInSekunds | (opcjonalnie) Domyślny interwał między kolekcją nieprzetworzonych (nieagregowanych) metryk. Najmniejsza obsługiwana częstotliwość próbkowania wynosi 15 sekund. Jeśli nie zostanie określona, wartością domyślną jest `15`.

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
resourceId | Identyfikator zasobu usługi Azure Resource Manager maszyny wirtualnej lub zestawu skalowania maszyny wirtualnej, do którego należy maszyna wirtualna. To ustawienie musi być również określone, jeśli w konfiguracji jest używany dowolny obiekt JsonBlob sink.
zaplanowany Okres Transferowy | Częstotliwość, z jaką metryki agregacji mają być obliczane i przenoszone do metryk platformy Azure, wyrażone jako interwał czasu IS 8601. Najmniejszy okres transferu wynosi 60 sekund, czyli PT1M. Należy określić co najmniej jeden scheduledTransferPeriod.

Przykłady metryki określone w sekcji performanceCounters są zbierane co 15 sekund lub z częstotliwością próbkowania jawnie zdefiniowane dla licznika. Jeśli pojawi się wiele zaplanowanych częstotliwościTransferPeriod (jak w przykładzie), każda agregacja jest obliczane niezależnie.

#### <a name="performancecounters"></a>Performancecounters

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

Ta opcjonalna sekcja steruje kolekcją metryk. Próbki surowców są agregowane dla każdego [zaplanowanegoTransferPeriodu](#metrics) w celu wytworzenia następujących wartości:

* średnia
* minimum
* maksimum
* ostatnio pobrana wartość
* liczba próbek pierwotnych użytych do obliczenia agregacji

Element | Wartość
------- | -----
Zlewozmywaki | (opcjonalnie) Oddzielona przecinkami lista nazw pochłaniania, do których LAD wysyła zagregowane wyniki metryki. Wszystkie zagregowane metryki są publikowane do każdego zlewu na liście. Zobacz [sinksConfig](#sinksconfig). Przykład: `"EHsink1, myjsonsink"`.
type | Identyfikuje rzeczywistego dostawcę metryki.
class | Wraz z "licznik", identyfikuje określone metryki w obszarze nazw dostawcy.
counter | Wraz z "class", identyfikuje określone metryki w obszarze nazw dostawcy.
kontraspecyfikator | Identyfikuje określone metryki w obszarze nazw metryk platformy Azure.
Warunek | (opcjonalnie) Wybiera określone wystąpienie obiektu, do którego stosuje się metrykę lub wybiera agregację we wszystkich wystąpieniach tego obiektu. Aby uzyskać więcej `builtin` informacji, zobacz definicje metryki.
sampleRate (Zestaw próbek) | Is 8601 interwał, który ustawia szybkość, z jaką są zbierane surowe próbki dla tej metryki. Jeśli nie jest ustawiony, interwał zbierania jest ustawiany przez wartość [sampleRateInSeconds](#ladcfg). Najkrótsza obsługiwana częstotliwość próbkowania wynosi 15 sekund (PT15S).
unit | Powinien być jednym z tych ciągów: "Count", "Bytes", "Seconds", "Percent", "CountPerSecond", "BytesPerSecond", "Millisecond". Definiuje jednostkę dla metryki. Konsumenci zebranych danych oczekują, że zebrane wartości danych będą zgodne z tą jednostką. LAD ignoruje to pole.
displayName | Etykieta (w języku określonym przez skojarzone ustawienie ustawień regionalnych) do dołączenia do tych danych w metrykach platformy Azure. LAD ignoruje to pole.

CounterSpecifier jest dowolnym identyfikatorem. Konsumenci metryki, takie jak azure portal wykresów i alertów funkcji, użyj counterSpecifier jako "klucz", który identyfikuje metryki lub wystąpienie metryki. W `builtin` przypadku danych zaleca się używanie wartości counterSpecifier, które zaczynają się od `/builtin/`. Jeśli zbierasz określone wystąpienie metryki, zaleca się dołączyć identyfikator wystąpienia do counterSpecifier wartość. Oto niektóre przykłady:

* `/builtin/Processor/PercentIdleTime`- Czas bezczynny uśredniona we wszystkich procesorach wirtualnych
* `/builtin/Disk/FreeSpace(/mnt)`- Wolne miejsce dla /mnt filesystem
* `/builtin/Disk/FreeSpace`- Wolne miejsce uśredniona we wszystkich zamontowanych systemach plików

Ani LAD ani Azure portal oczekuje counterSpecifier wartość do dopasowania dowolnego wzorca. Bądź spójny w sposobie konstruowania counterSpecifier wartości.

Po określeniu `performanceCounters`, LAD zawsze zapisuje dane do tabeli w magazynie platformy Azure. Te same dane mogą być zapisywane w obiektach blob json i/lub centrach zdarzeń, ale nie można wyłączyć przechowywania danych w tabeli. Wszystkie wystąpienia rozszerzenia diagnostycznego skonfigurowane do używania tej samej nazwy konta magazynu i punktu końcowego dodać ich metryki i dzienniki do tej samej tabeli. Jeśli zbyt wiele maszyn wirtualnych zapisuje się na tej samej partycji tabeli, platforma Azure może ograniczyć zapisy do tej partycji. Ustawienie eventVolume powoduje, że wpisy mają być rozłożone na 1 (Małe), 10 (Średni) lub 100 (Duże) różne partycje. Zazwyczaj "Medium" jest wystarczające, aby zapewnić ruch nie jest ograniczona. Funkcja metryki platformy Azure w witrynie Azure portal używa danych w tej tabeli do tworzenia wykresów lub wyzwalania alertów. Nazwa tabeli jest łączenia tych ciągów:

* `WADMetrics`
* "ScheduledTransferPeriod" dla zagregowanych wartości przechowywanych w tabeli
* `P10DV2S`
* Data w postaci "RRRRMMDD", która zmienia się co 10 dni

Przykłady `WADMetricsPT1HP10DV2S20170410` obejmują `WADMetricsPT1MP10DV2S20170609`i .

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

Ta opcjonalna sekcja steruje kolekcją zdarzeń dziennika z syslog. Jeśli sekcja zostanie pominięta, zdarzenia syslog nie są przechwytywane w ogóle.

Kolekcja syslogEventConfiguration ma jeden wpis dla każdego obiektu syslog zainteresowania. Jeśli minSeverity jest "NONE" dla określonego obiektu lub jeśli obiekt ten nie pojawia się w elemencie w ogóle, żadne zdarzenia z tego obiektu nie są przechwytywane.

Element | Wartość
------- | -----
Zlewozmywaki | Oddzielona przecinkami lista nazw pochłaniacza, do których publikowane są poszczególne zdarzenia dziennika. Wszystkie zdarzenia dziennika pasujące do ograniczeń w syslogEventConfiguration są publikowane do każdego ujścia na liście. Przykład: "EHforsyslog"
nazwa obiektu | Nazwa obiektu syslog (na\_przykład "LOG\_USER" lub "LOG LOCAL0"). Pełną listę można znaleźć w sekcji "obiekt" [na stronie man syslog.](http://man7.org/linux/man-pages/man3/syslog.3.html)
minEverity (Zawsze) | Poziom ważności syslog (takich jak\_"LOG ERR" lub "LOG\_INFO"). Zobacz sekcję "level" [na stronie man syslog,](http://man7.org/linux/man-pages/man3/syslog.3.html) aby uzyskać pełną listę. Rozszerzenie przechwytuje zdarzenia wysyłane do obiektu na określonym poziomie lub powyżej.

Po określeniu `syslogEvents`, LAD zawsze zapisuje dane do tabeli w magazynie platformy Azure. Te same dane mogą być zapisywane w obiektach blob json i/lub centrach zdarzeń, ale nie można wyłączyć przechowywania danych w tabeli. Zachowanie partycjonowania dla tej tabeli jest `performanceCounters`takie samo, jak opisano dla . Nazwa tabeli jest łączenia tych ciągów:

* `LinuxSyslog`
* Data w postaci "RRRRMMDD", która zmienia się co 10 dni

Przykłady `LinuxSyslog20170410` obejmują `LinuxSyslog20170609`i .

### <a name="perfcfg"></a>perfCfg

Ta opcjonalna sekcja steruje wykonywaniem dowolnych zapytań [OMI.](https://github.com/Microsoft/omi)

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
namespace | (opcjonalnie) Obszar nazw OMI, w którym kwerenda powinna być wykonywana. Jeśli nie określono, wartością domyślną jest "root/scx", zaimplementowana przez [dostawców międzyplatformowych System Center](https://github.com/Microsoft/SCXcore).
query | Kwerenda OMI do wykonania.
tabela | (opcjonalnie) Tabela magazynu platformy Azure na wyznaczonym koncie magazynu (zobacz [Ustawienia chronione).](#protected-settings)
frequency | (opcjonalnie) Liczba sekund między wykonaniem kwerendy. Wartość domyślna to 300 (5 minut); minimalna wartość wynosi 15 sekund.
Zlewozmywaki | (opcjonalnie) Oddzielona przecinkami lista nazw dodatkowych pochłaniaczy, do których powinny być publikowane wyniki metryki próbki pierwotnej. Nie agregacji tych nieprzetworzonych próbek jest obliczany przez rozszerzenie lub metryki platformy Azure.

Należy określić "table" lub "sinks", lub oba.

### <a name="filelogs"></a>fileLogs (dzienniki plików)

Steruje przechwytywaniem plików dziennika. LAD przechwytuje nowe wiersze tekstu, ponieważ są one zapisywane w pliku i zapisuje je do wierszy tabeli i/lub wszelkich określonych zlewów (JsonBlob lub EventHub).

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
 — plik | Pełna nazwa ścieżki pliku dziennika do obserwowanie i przechwytywanie. Nazwa ścieżki musi nadać nazwę pojedynczemu plikowi; nie może nazwać katalogu ani zawierać symboli wieloznacznych.
tabela | (opcjonalnie) Tabela magazynu platformy Azure, na wyznaczonym koncie magazynu (zgodnie z określoną w chronionej konfiguracji), w której zapisywane są nowe wiersze z "ogona" pliku.
Zlewozmywaki | (opcjonalnie) Oddzielona przecinkami lista nazw dodatkowych pochłaniacze, do których wysyłane są wiersze dziennika.

Należy określić "table" lub "sinks", lub oba.

## <a name="metrics-supported-by-the-builtin-provider"></a>Metryki obsługiwane przez wbudowanego dostawcę

Wbudowany dostawca metryki jest źródłem metryk najbardziej interesujących dla szerokiego zestawu użytkowników. Te wskaźniki dzielą się na pięć klas szerokich:

* Procesor
* Memory (Pamięć)
* Network (Sieć)
* Filesystem
* Dysk

### <a name="builtin-metrics-for-the-processor-class"></a>wbudowane metryki dla klasy Procesor

Klasa Procesor metryki zawiera informacje o użyciu procesora na maszynie Wirtualnej. Podczas agregowania wartości procentowych wynikiem jest średnia dla wszystkich procesorów. W maszynie wirtualnej z dwoma vCPU, jeśli jeden procesor wirtualny był w 100% zajęty, a drugi był w 100% bezczynny, zgłoszony PercentIdleTime będzie miał 50. Jeśli każdy procesor wirtualny był zajęty 50% w tym samym okresie, raportowany wynik będzie również 50. W maszynie wirtualnej z czterema vCPU, z jednym procesorem wirtualnym 100% zajętym, a pozostałe bezczynne, zgłoszony PercentIdleTime będzie miał 75.

counter | Znaczenie
------- | -------
PercentIdleTime (Czas percenta) | Procent czasu podczas okna agregacji, w którym procesory wykonywały pętlę bezczynności jądra
ProcentProcesorTime | Procent czasu wykonywania wątku nieuleczalne
PercentIOWój czasu | Procent czasu oczekiwania na zakończenie operacji we/wy
PercentInterruptTime | Procent czasu wykonywania przerwań sprzętowych/programowych i dpc (odroczone wywołania procedury)
PercentUserTime (ProcentUserTime) | Czas bezczynności w oknie agregacji, procent czasu spędzonego w użytkowniku więcej przy normalnym priorytecie
PercentNiceTime (Czas nikacjowy) | Z czasu bezczynności, procent spędzony przy obniżonym (ładnym) priorytecie
ProcentPrivilegedTime | Z czasu bezczynności, procent spędzony w trybie uprzywilejowanym (jądra)

Pierwsze cztery liczniki powinny wynosić 100%. Ostatnie trzy liczniki również sumują się do 100%; dzielą sumę PercentProcessorTime, PercentIOWaitTime i PercentInterruptTime.

Aby uzyskać pojedynczą metrykę zagregowane `"condition": "IsAggregate=TRUE"`na wszystkich procesorach, ustaw . Aby uzyskać metrykę dla określonego procesora, takiego jak drugi procesor logiczny `"condition": "Name=\\"1\\""`maszyny wirtualnej z czterema procesorami wirtualnymi, należy ustawić . Numery procesorów logicznych `[0..n-1]`są w zakresie .

### <a name="builtin-metrics-for-the-memory-class"></a>wbudowane metryki dla klasy Memory

Klasa Pamięć metryki zawiera informacje na temat wykorzystania pamięci, stronicowania i zamiany.

counter | Znaczenie
------- | -------
DostępneMemory | Dostępna pamięć fizyczna w MiB
ProcentAvailableMemory | Dostępna pamięć fizyczna jako procent całkowitej pamięci
UżywaneMemory | Pamięć fizyczna w użyciu (MiB)
ProcentUsedMemory | Pamięć fizyczna w użyciu jako procent całkowitej pamięci
PagesPerSec (Protokół PagesPerSec) | Stronicowanie razem (odczyt/zapis)
PagesReadPerSec | Strony odczytywane ze sklepu zapasowego (plik wymiany, plik programu, zamapowany plik itp.)
PagesWrittenPerSec | Strony napisane do magazynu kopii zapasowej (plik wymiany, zamapowany plik itp.)
DostępneSwap | Niewykorzystana przestrzeń wymiany (MiB)
ProcentDostępnySwap | Niewykorzystana przestrzeń swapowa jako procent całkowitej wymiany
UżywaneSwap | Przestrzeń wymiany w użyciu (MiB)
ProcentUsedSwap | Przestrzeń wymiany w użyciu jako procent całkowitej wymiany

Ta klasa metryk ma tylko jedno wystąpienie. Atrybut "warunek" nie ma przydatnych ustawień i należy go pominąć.

### <a name="builtin-metrics-for-the-network-class"></a>wbudowane metryki dla klasy Network

Klasa Network metryki zawiera informacje o aktywności sieciowej w poszczególnych interfejsów sieciowych od momentu rozruchu. LAD nie udostępnia metryk przepustowości, które można pobrać z metryk hosta.

counter | Znaczenie
------- | -------
BajtyPrzemiaszone | Całkowita liczba bajtów wysłanych od momentu rozruchu
Bytesreceived | Całkowita liczba bajtów odebranych od czasu rozruchu
BajtyWztę | Całkowita liczba bajtów wysłanych lub odebranych od czasu rozruchu
PakietyTransmitowane | Łączna liczba pakietów wysłanych od momentu rozruchu
PakietyReceived | Łączna liczba pakietów odebranych po rozruchu
TotalRxErrors ( TotalRxErrors ) | Liczba błędów odbierania od czasu rozruchu
TotalTxErrors ( TotalTxErrors ) | Liczba błędów transmisji od czasu rozruchu
TotalCollisions (Razem) | Liczba kolizji zgłoszonych przez porty sieciowe od czasu rozruchu

 Mimo że ta klasa jest instancja, LAD nie obsługuje przechwytywania metryki sieci agregowane na wszystkich urządzeniach sieciowych. Aby uzyskać metryki dla określonego interfejsu, takie jak `"condition": "InstanceID=\\"eth0\\""`eth0, ustaw .

### <a name="builtin-metrics-for-the-filesystem-class"></a>wbudowane metryki dla klasy Filesystem

Klasa Filesystem metryki zawiera informacje o użyciu systemu plików. Wartości bezwzględne i procentowe są zgłaszane, ponieważ będą wyświetlane zwykłemu użytkownikowi (nie rootowi).

counter | Znaczenie
------- | -------
FreeSpace | Dostępne miejsce na dysku w bajtach
Używana przestrzeń | Używane miejsce na dysku w bajtach
Przestrzeń Procentowa | Procent wolnego miejsca
PercentUsedSpace (Obszar procentowy) | Procent używanego miejsca
PercentFreeInodes (ProcentFreeInodes) | Procent nieużywanych i-węzłów
ProcentUżytnia | Procent przydzielonych (używanych) iod zsumowanych we wszystkich systemach plików
Przeztówektejszeksekseksek | Bajty odczytywane na sekundę
Przeztówna naczepę | Bajty zapisywane na sekundę
PrzeztówPerSekunda | Bajty odczytywane lub zapisywane na sekundę
OdczytySekundy | Operacje odczytu na sekundę
WritesPerSecond | Operacje zapisu na sekundę
TransferPerSecond | Operacje odczytu lub zapisu na sekundę

Zagregowane wartości we wszystkich systemach `"condition": "IsAggregate=True"`plików można uzyskać, ustawiając . Wartości dla określonego zamontowanego systemu plików, takie jak "/mnt", można uzyskać, ustawiając `"condition": 'Name="/mnt"'`. 

**UWAGA:** W przypadku korzystania z usługi Azure Portal zamiast JSON, prawidłowym formularzem pola warunku jest Name='/mnt'

### <a name="builtin-metrics-for-the-disk-class"></a>wbudowane metryki dla klasy Dysk

Klasa Dysk metryki zawiera informacje o użyciu urządzenia dyskowego. Te statystyki dotyczą całego dysku. Jeśli na urządzeniu znajduje się wiele systemów plików, liczniki dla tego urządzenia są skutecznie agregowane we wszystkich z nich.

counter | Znaczenie
------- | -------
OdczytySekundy | Operacje odczytu na sekundę
WritesPerSecond | Operacje zapisu na sekundę
TransferPerSecond | Łączna liczba operacji na sekundę
Średnia Czas czytania | Średnia liczba sekund na odczyt
ŚredniaCzas pracy | Średnia sekunda na operację zapisu
ŚredniaCzastransferu | Średnia sekundy na operację
Średnia Wartość DyskieczekQueueLength | Średnia liczba operacji na dysku w kolejce
ReadBytesPerSecond (Odczyt Naprzemognie) | Liczba bajtów odczytanych na sekundę
WriteBytesPerSecond | Liczba bajtów zapisanych na sekundę
PrzeztówPerSekunda | Liczba bajtów odczytanych lub zapisanych na sekundę

Zagregowane wartości na wszystkich dyskach `"condition": "IsAggregate=True"`można uzyskać, ustawiając plik . Aby uzyskać informacje dotyczące określonego urządzenia (na przykład /dev/sdf1), ustaw . `"condition": "Name=\\"/dev/sdf1\\""`

## <a name="installing-and-configuring-lad-30-via-cli"></a>Instalowanie i konfigurowanie rozszerzenia LAD 3.0 za pomocą interfejsu wiersza polecenia

Zakładając, że chronione ustawienia znajdują się w pliku PrivateConfig.json, a informacje o konfiguracji publicznej znajdują się w pliku PublicConfig.json, uruchom to polecenie:

```azurecli
az vm extension set *resource_group_name* *vm_name* LinuxDiagnostic Microsoft.Azure.Diagnostics '3.*' --private-config-path PrivateConfig.json --public-config-path PublicConfig.json
```

Polecenie zakłada, że używasz trybu zarządzania zasobami platformy Azure (ramię) interfejsu wiersza polecenia platformy Azure. Aby skonfigurować maszyny wirtualne modelu wdrażania klasycznego (ASM), przełącz`azure config mode asm`się na tryb "asm" ( ) i pomiń nazwę grupy zasobów w poleceniu. Aby uzyskać więcej informacji, zobacz [dokumentację interfejsu wiersza polecenia na różnych platformach](https://docs.microsoft.com/azure/xplat-cli-connect).

## <a name="an-example-lad-30-configuration"></a>Przykładowa konfiguracja LAD 3.0

Na podstawie poprzednich definicji, oto przykładowa konfiguracja rozszerzenia LAD 3.0 z wyjaśnieniem. Aby zastosować ten przykład do sprawy, należy użyć nazwy własnego konta magazynu, tokenu Sygnatury dostępu Współdzielonego konta i tokenów Sygnatury dostępu Współdzielonego EventHubs.

### <a name="privateconfigjson"></a>PrivateConfig.json

Te ustawienia prywatne konfigurują:

* konto magazynu
* pasujący token sygnatury dostępu Współdzielonego konta
* kilka zlewów (JsonBlob lub EventHubs z tokenami Sygnatury dostępu Współdzielonego)

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

Te ustawienia publiczne powodują, że LAD:

* Przekazywanie procentowego czasu procesora i używanego miejsca `WADMetrics*` na dysku metryki do tabeli
* Przesyłanie wiadomości z funkcji syslog "użytkownik" i `LinuxSyslog*` ważności "info" do tabeli
* Przekazywanie surowych wyników kwerend omi (PercentProcessorTime i `LinuxCPU` PercentIdleTime) do nazwanej tabeli
* Przekazywanie dołączona linia `/var/log/myladtestlog` w `MyLadTestLog` pliku do tabeli

W każdym przypadku dane są również przesyłane do:

* Magazyn obiektów Blob platformy Azure (nazwa kontenera jest zdefiniowana w ujściu JsonBlob)
* Punkt końcowy EventHubs (określony w umywalka EventHubs)

```json
{
  "StorageAccount": "yourdiagstgacct",
  "ladCfg": {
    "sampleRateInSeconds": 15,
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

W `resourceId` konfiguracji musi odpowiadać, że maszyny wirtualnej lub zestawu skalowania maszyny wirtualnej.

* Metryki platformy Azure wykresów i alertów zna resourceId maszyny wirtualnej, nad którą pracujesz. Oczekuje się, aby znaleźć dane dla maszyny Wirtualnej przy użyciu resourceId klucz wyszukiwania.
* Jeśli używasz skalowania automatycznego platformy Azure, resourceId w konfiguracji skalowania automatycznego musi odpowiadać identyfikator zasobów używane przez LAD.
* Identyfikator zasobów jest wbudowany w nazwy JsonBlobs napisane przez LAD.

## <a name="view-your-data"></a>Wyświetlanie danych

Użyj witryny Azure Portal, aby wyświetlić dane dotyczące wydajności lub ustawić alerty:

![image](./media/diagnostics-linux/graph_metrics.png)

Dane `performanceCounters` są zawsze przechowywane w tabeli usługi Azure Storage. Interfejsy API usługi Azure Storage są dostępne dla wielu języków i platform.

Dane wysyłane do zlewozmywaków JsonBlob są przechowywane w obiektach blob na koncie magazynu o nazwie w [ustawieniach chronionych](#protected-settings). Można korzystać z danych obiektów blob przy użyciu dowolnych interfejsów API usługi Azure Blob Storage.

Ponadto można użyć tych narzędzi interfejsu użytkownika, aby uzyskać dostęp do danych w usłudze Azure Storage:

* Eksplorator serwera programu Visual Studio.
* [Eksplorator magazynu platformy Microsoft Azure](https://azurestorageexplorer.codeplex.com/ "Eksplorator usługi Azure Storage").

Ta migawka sesji Eksploratora usługi Microsoft Azure Storage pokazuje wygenerowane tabele i kontenery usługi Azure Storage z poprawnie skonfigurowanego rozszerzenia LAD 3.0 na testowej maszynie wirtualnej. Obraz nie jest dokładnie zgodny z [przykładową konfiguracją LAD 3.0](#an-example-lad-30-configuration).

![image](./media/diagnostics-linux/stg_explorer.png)

Zobacz odpowiednią [dokumentację EventHubs,](../../event-hubs/event-hubs-what-is-event-hubs.md) aby dowiedzieć się, jak korzystać z wiadomości opublikowanych w punkcie końcowym EventHubs.

## <a name="next-steps"></a>Następne kroki

* Tworzenie alertów metryk w [usłudze Azure Monitor](../../monitoring-and-diagnostics/insights-alerts-portal.md) dla metryk, które zbierasz.
* Tworzenie [wykresów monitorowania](../../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md) dla danych.
* Dowiedz się, jak [utworzyć zestaw skalowania maszyny wirtualnej](../linux/tutorial-create-vmss.md) przy użyciu metryk do kontrolowania skalowania automatycznego.
