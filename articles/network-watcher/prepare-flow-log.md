---
title: Zmiany dziennika przepływu w grupie zabezpieczeń sieci platformy Azure | Dokumentacja firmy Microsoft
description: Więcej informacji na temat zmiany dziennika przepływu w grupie zabezpieczeń sieci platformy Azure.
services: network-watcher
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/16/2018
ms.author: jdial
ms.openlocfilehash: b3a5ca929c83f70c31d667c2d9c811623691cff8
ms.sourcegitcommit: 1aedb52f221fb2a6e7ad0b0930b4c74db354a569
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/17/2018
ms.locfileid: "40180820"
---
# <a name="network-security-group-flow-logs--version-2--upcoming-changes"></a>Przepływ grupy zabezpieczeń sieci rejestruje nadchodzące — wersja 2 — zmiany

Format dzienników przepływu grupy zabezpieczeń sieci ulegają zmianie, począwszy od 15 września 2018 r. Dodane pola udostępniają informacje z przepływem stanu i przyrostowych liczby bajtów i pakietów przesyłanych w każdym kierunku. Aplikacje, które analizowania dzienników przepływu wpływ tej zmiany i należy odpowiednio zaktualizować. W tym artykule opisano szczegóły zmiany.

## <a name="what-is-changing"></a>Co ulega zmianie?

Wersja dzienniki przepływu sieciowych grup zabezpieczeń zostanie zmieniony z "Wersja": 1, aby "Wersja": 2 dodatkowe pola dodane do *flowTuples* właściwość w dziennikach. Szczegółowe informacje na temat formatu znajdują się w [jak przepływu w modelu w wersji 2](#how-is-a-flow-modeled-in-version-2).

### <a name="version-1-flow-tuple-format"></a>W wersji 1 usługi flow krotki formatu

```
"1493763938,185.170.185.105,10.2.0.4,35370,23,T,I,D"
```

### <a name="version-2-flow-tuple-format"></a>W wersji 2 usługi flow krotki formatu

```
"1493763938,185.170.185.105,10.2.0.4,35370,23,T,I,A,C,1,103,1,186"
```

## <a name="when-will-this-change-take-place"></a>Jeśli ta zmiana będzie miała miejsce?

Ta zmiana przejdzie do początku efekt **15 września 2018** w regionie zachodnio-środkowe stany USA. Wprowadzanie zmian w regionach chmury publicznej zakończy się za 31 listopad maja 2018 r. po rejestruje tylko wersji 2 będą dostępne.

## <a name="am-i-affected-by-the-change"></a>Jestem dotyczy jakaś zmiana?

Użytkownik może mieć wpływ tej zmiany kompilacji lub korzystanie z aplikacji, która przetwarza dane dziennika przepływu grupy zabezpieczeń sieci.

### <a name="if-i-use-traffic-analytics"></a>Użycie analizy ruchu?

Nie. Analiza ruchu nie będzie mieć wpływ podczas przechodzenia w wersji 1 w wersji 2 usługi flow rejestrowania. Ulepszenia wkrótce będzie się korzystać z dodatkowych informacji sesji i przepustowości w wersji 2 usługi flow dzienniki.

### <a name="if-im-using-third-party-tooling"></a>Jeśli używam innej narzędzi?

Zmiany w formacie dziennika zostało przekazane z wyprzedzeniem ze wszystkimi [partnerów usługi Network Watcher](https://azure.microsoft.com/services/network-watcher). Skontaktuj się z dostawcą, aby uzyskać szczegółowe informacje.

### <a name="if-i-have-built-a-custom-application-or-integration"></a>Jeśli mam utworzone z niestandardowych aplikacji lub Integracja z usługą?

**Tak**, należy zmodyfikować aplikację w celu przetwarzania dzienników przepływu sieciowych grup zabezpieczeń w nowym formacie.

## <a name="what-is-the-new-flow-logging-format"></a>Co to jest nowy format rejestrowania usługi flow?

Przepływ dzienników w wersji 2 zawiera przepływ krotek w następującym formacie:

```
"1493763938,185.170.185.105,10.2.0.4,35370,23,T,I,A,C,1,103,1,186".
```

Tabela poniżej zawiera właściwości nazwy i opisy dla krotki 2 usługi flow wersji grupy zabezpieczeń sieci. Rekordy pełny przykład można znaleźć w [zdarzenie próbkowania w wersji 2](#version2sampleevent).

| Nazwa właściwości                        | Wartość           | Opis                                                                                                                                                 |
| ------------------------------------ | --------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Znacznik czasu                           | 1493763938      | Sygnatura czasowa odpowiadający wpis przepływu. Jest używany format EPOKI systemu UNIX.                                                                                       |
| Źródłowy adres IP                    | 185.170.185.105 |                                                                                                                                                             |
| Docelowy adres IP               | 10.2.0.4        |                                                                                                                                                             |
| Port źródłowy                          | 35370           |                                                                                                                                                             |
| Port docelowy                     | 23              |                                                                                                                                                             |
| Ruch protokołu                     | T               | **T**: TCP i **U**: UDP.                                                                                                                                  |
| Kierunek przepływu ruchu               | I               | **Czy mogę**: ruch przychodzący i **O**: ruch wychodzący.                                                                                                         |
| Decyzja ruchu                     | A               | **A**: zezwolono przepływu i **D**: przepływ został odrzucony.                                                                                                         |
| Stan przepływu                           | C               | **B**: początek, gdy zostanie utworzony przepływ. Statystyki nie są dostarczane. **C**: kontynuowanie ciągłego przepływu. Statystyki są podane na 5-minutowych interwałach. **E**: koniec, gdy przepływ zostanie zakończony. Statystyki są dostarczane.                                                                                                                                                        |
| Pakiety - źródła do miejsca docelowego      | 1               | Całkowita liczba pakietów TCP i UDP, odsyłane ze źródła do miejsca docelowego od czasu ostatniej aktualizacji.                                                                  |
| Liczba wysłanych bajtów - źródła do miejsca docelowego   | 103             | Całkowita liczba bajtów pakiet TCP i UDP, odsyłane ze źródła do miejsca docelowego od czasu ostatniej aktualizacji. Bajty pakietu obejmują pakiet nagłówka i ładunku.         |
| Pakiety wysłane - docelowym ze źródłem | 1               | Całkowita liczba pakietów TCP i UDP, wysyłane z docelowym źródłem od czasu ostatniej aktualizacji.                                                                  |
| Liczba wysłanych bajtów - docelowym ze źródłem   | 186             | Całkowita liczba bajtów pakiet TCP i UDP, wysyłane z docelowym źródłem od czasu ostatniej aktualizacji. Bajty pakietu obejmują pakiet nagłówka i ładunku.             |

## <a name="how-is-a-flow-modeled-in-version-2"></a>Jak przepływu w modelu w wersji 2?

Dzienniki w wersji 2 wprowadzono stan przepływu. Stan przepływu *B* jest rejestrowane, gdy przepływ jest inicjowana. Stan przepływu *C* i stan przepływu *E* są Stany, oznaczające kontynuacji przepływ i zakończenia przepływu. Zarówno *C* i *E* stany zawierają informacje o przepustowości ruchu.

**Przykład**: przepływ krotkami z konwersacji między 185.170.185.105:35370 i 10.2.0.4:23 TCP:

"1493763938,185.170.185.105,10.2.0.4,35370,23,T,I,A,B,,," "1493695838,185.170.185.105,10.2.0.4,35370,23,T,I,A,C,1021,588096,8005,4610880" "1493696138,185.170.185.105,10.2.0.4,35370,23,T,I,A,E,52,29952,47,27072"

### <a name="how-does-the-format-differ-from-version-1"></a>Czym różni się format z wersji 1?

W wersji 1 usługi flow spójnej kolekcji ["1493763938,185.170.185.105,10.2.0.4,35370,23,T,I,D"] jest tworzony za każdym razem, gdy przepływ jest ustanowione lub próbowało nawiązać (odmowa przypadek).

### <a name="how-are-bytes-and-packets-accounted-for"></a>Jak są bajtów i pakietów rozliczane?

Dla kontynuacji *C* i na końcu *E* Stany przepływu wygasają po, liczby bajtów i pakietów są łącznej liczby od czasu poprzedniego rekordu przepływu w spójnej kolekcji. Całkowita liczba pakietów przesyłanych odwołujące się do konwersacji poprzedniego przykładu, jest 1021 + 52 + 8005 + 47 = 9125. Całkowita liczba bajtów przesłanych jest 588096 + 29952 + 4610880 + 27072 = 5256000.

### <a name="if-my-application-doesnt-understand-the-traffic-bandwidth-fields-how-does-version-2-affect-the-application"></a>Jeśli Moja aplikacja nie rozumiesz pola przepustowości ruchu, jak w wersji 2 wpływa na aplikację?

Aplikacje przy użyciu zabezpieczeń sieci w wersji 1 grupy rejestrowania zwykle liczba Liczba unikatowych przepływów usługi flow. Jeśli nie podczas analizowania zostanie zmienione, aby uwzględnić stan przepływu, może zostać wyświetlony niedokładne wzrost liczby przepływów zgłaszane. Liczenie unikatowych przepływów można osiągnąć, ignorując krotek przepływu w *C* i *E* przepływ stanów.

## <a name="can-i-control-the-version-format-i-receive"></a>Można kontrolować format wersji, który pojawia się?

Nie. Włączanie i wyłączanie dzienników przepływu nie wpłynie na format danych wyjściowych dzienników. Zmiany z wersji 1 do dzienników w wersji 2 będą naliczane w zasadzie według regionu. Gdy region jest uaktualnienie z wersji 1 do wersji 2, mogą pojawić się dzienników przepływów sieciowych grup zabezpieczeń w obu formatów. Po zakończeniu wprowadzania aktualizacji tylko w wersji 2 dzienniki będą dostępne.

## <a name="while-the-change-occurs-can-i-see-both-formats-for-the-same-network-security-group"></a>Gdy wystąpi zmiana, można wyświetlić obu formatów dla tej samej grupy zabezpieczeń sieci?

Tak. W danym regionie, na podstawie adresów dla komputerów mac wystąpi przejścia. Ponieważ grupy zabezpieczeń sieci można zastosować do wielu maszyn, możesz zobaczyć dzienniki w obu formatów zapisywane w magazynie. Dzienniki będą albo w wersji 1 lub 2.

## <a name="will-i-see-duplicate-data"></a>Czy otrzymam zduplikowanych danych?

Nie będzie dublowania przepływu rejestrowanie danych w różnych formatach. Przepływ będą rejestrowane w formacie w wersji 1 lub w wersji 2, gdy nastąpi zmiana.

## <a name="how-can-i-test-the-new-format-ahead-of-time"></a>Jak przetestować, nowego formatu wcześniejsze?

Tak. Możesz [Pobierz](https://aka.ms/nsgflowlogsv2blobsample) przykładowy plik dziennika przepływu w wersji 2 na potrzeby testowania rozwiązania.

## <a name="are-there-changes-to-configuration-and-management-of-network-security-group-flow-logging"></a>Czy istnieją zmiany w konfiguracji i zarządzania rejestrowanie przepływu grup zabezpieczeń sieci?

Nie. Obsługa kont usługi Azure Storage w różnych subskrypcjach, które współużytkują z tą samą dzierżawą usługi Azure Active Directory została [wydane](https://azure.microsoft.com/blog/new-azure-network-watcher-integrations-and-network-security-group-flow-logging-updates/) wcześniej w tym roku. Ta zmiana pozwala zmniejszyć liczbę kont magazynu, na potrzeby związane z zarządzaniem dzienniki przepływu sieciowych grup zabezpieczeń.

## <a name="questions-or-feedback"></a>Pytania lub opinie?

Chętnie czekamy na informacje o Twoich wrażeniach dzienniki przepływu sieciowych grup zabezpieczeń i usługi Network Watcher. Można podać opinię lub sugestie online lub za pośrednictwem poczty e-mail na AzureNetworkWatcher@microsoft.com.

## <a name="version-2-sample"></a>Przykładowe w wersji 2

```json
{

"records": [

{

"time": "2018-08-03T17:00:54.5657764Z",

"systemId": "bbd48473-8ce0-4834-99bb-2e13b9b23ff8",

"category": "NetworkSecurityGroupFlowEvent",

"resourceId":
"/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FLOWLOGSV2DEMO/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/MULTITIERAPP2-NSG",

"operationName": "NetworkSecurityGroupFlowEvents",

"properties": {

"Version": 2,

"flows": [

{

"rule": "DefaultRule_AllowInternetOutBound",

"flows": [

{

"mac": "002248034CC3",

"flowTuples": [

"1533315592,10.1.1.6,204.79.197.200,62375,80,T,O,A,B,,,,",

"1533315595,10.1.1.6,204.79.197.200,62373,80,T,O,A,E,30,1784,92,123631",

"1533315597,10.1.1.6,204.79.197.200,62376,80,T,O,A,B,,,,",

"1533315601,10.1.1.6,204.79.197.200,62374,80,T,O,A,E,13,866,87,123079",

"1533315603,10.1.1.6,204.79.197.200,62377,80,T,O,A,B,,,,",

"1533315604,10.1.1.6,204.79.197.200,62375,80,T,O,A,E,33,1946,90,123247",

"1533315608,10.1.1.6,204.79.197.200,62378,80,T,O,A,B,,,,",

"1533315610,10.1.1.6,204.79.197.200,62376,80,T,O,A,E,20,1244,92,123355",

"1533315613,10.1.1.6,204.79.197.200,62379,80,T,O,A,B,,,,",

"1533315616,10.1.1.6,204.79.197.200,62377,80,T,O,A,E,24,1460,92,123355",

"1533315619,10.1.1.6,204.79.197.200,62380,80,T,O,A,B,,,,",

"1533315622,10.1.1.6,204.79.197.200,62378,80,T,O,A,E,23,1406,93,123409",

"1533315624,10.1.1.6,204.79.197.200,62381,80,T,O,A,B,,,,",

"1533315628,10.1.1.6,204.79.197.200,62379,80,T,O,A,E,16,1028,88,123133",

"1533315630,10.1.1.6,204.79.197.200,62382,80,T,O,A,B,,,,",

"1533315631,10.1.1.6,204.79.197.200,62380,80,T,O,A,E,13,866,87,123079",

"1533315635,10.1.1.6,204.79.197.200,62384,80,T,O,A,B,,,,",

"1533315637,10.1.1.6,204.79.197.200,62381,80,T,O,A,E,15,974,86,123025",

"1533315640,10.1.1.6,204.79.197.200,62385,80,T,O,A,B,,,,",

"1533315643,10.1.1.6,204.79.197.200,62382,80,T,O,A,E,16,1028,88,123139",

"1533315646,10.1.1.6,204.79.197.200,62386,80,T,O,A,B,,,,",

"1533315649,10.1.1.6,204.79.197.200,62384,80,T,O,A,E,21,1298,92,123355",

"1533315651,10.1.1.6,204.79.197.200,62387,80,T,O,A,B,,,,"

]

}

]

}

]

}

},

{

"time": "2018-08-03T17:01:54.5668880Z",

"systemId": "bbd48473-8ce0-4834-99bb-2e13b9b23ff8",

"category": "NetworkSecurityGroupFlowEvent",

"resourceId":
"/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FLOWLOGSV2DEMO/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/MULTITIERAPP2-NSG",

"operationName": "NetworkSecurityGroupFlowEvents",

"properties": {

"Version": 2,

"flows": [

{

"rule": "DefaultRule_DenyAllInBound",

"flows": [

{

"mac": "002248034CC3",

"flowTuples": [

"1533315685,80.211.83.37,10.1.1.6,45321,81,T,I,D,B,,,,"

]

}

]

},

{

"rule": "DefaultRule_AllowInternetOutBound",

"flows": [

{

"mac": "002248034CC3",

"flowTuples": [

"1533315655,10.1.1.6,204.79.197.200,62385,80,T,O,A,E,20,1244,87,123079",

"1533315657,10.1.1.6,204.79.197.200,62388,80,T,O,A,B,,,,",

"1533315658,10.1.1.6,204.79.197.200,62386,80,T,O,A,E,26,1568,92,123355",

"1533315662,10.1.1.6,204.79.197.200,62389,80,T,O,A,B,,,,",

"1533315664,10.1.1.6,204.79.197.200,62387,80,T,O,A,E,15,974,88,123133",

"1533315667,10.1.1.6,204.79.197.200,62390,80,T,O,A,B,,,,",

"1533315670,10.1.1.6,204.79.197.200,62388,80,T,O,A,E,18,1136,88,123139",

"1533315673,10.1.1.6,204.79.197.200,62391,80,T,O,A,B,,,,",

"1533315676,10.1.1.6,204.79.197.200,62389,80,T,O,A,E,14,920,87,123079",

"1533315678,10.1.1.6,204.79.197.200,62392,80,T,O,A,B,,,,",

"1533315679,10.1.1.6,204.79.197.200,62390,80,T,O,A,E,31,1838,94,123739",

"1533315684,10.1.1.6,204.79.197.200,62393,80,T,O,A,B,,,,",

"1533315685,10.1.1.6,204.79.197.200,62391,80,T,O,A,E,28,1676,101,141199",

"1533315689,10.1.1.6,204.79.197.200,62394,80,T,O,A,B,,,,",

"1533315691,10.1.1.6,204.79.197.200,62392,80,T,O,A,E,21,1298,93,123409",

"1533315694,10.1.1.6,204.79.197.200,62395,80,T,O,A,B,,,,",

"1533315697,10.1.1.6,204.79.197.200,62393,80,T,O,A,E,26,1568,91,123393",

"1533315700,10.1.1.6,204.79.197.200,62396,80,T,O,A,B,,,,",

"1533315703,10.1.1.6,204.79.197.200,62394,80,T,O,A,E,14,920,89,123187",

"1533315705,10.1.1.6,204.79.197.200,62397,80,T,O,A,B,,,,",

"1533315706,10.1.1.6,204.79.197.200,62395,80,T,O,A,E,13,866,87,123079",

"1533315711,10.1.1.6,204.79.197.200,62398,80,T,O,A,B,,,,"

]

}

]

}

]

}

}

]

}
```

## <a name="version-1-sample"></a>Przykład wersja 1

```json
{ 

    "records": [ 

        { 

            "time": "2017-02-16T22:00:32.8950000Z", 

            "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434", 

            "category": "NetworkSecurityGroupFlowEvent", 

            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG", 

            "operationName": "NetworkSecurityGroupFlowEvents", 

            "properties": { 

                "Version": 1, 

                "flows": [ 

                    { 

                        "rule": "DefaultRule_DenyAllInBound", 

                        "flows": [ 

                            { 

                                "mac": "000D3AF8801A", 

                                "flowTuples": [ 

                                    "1487282421,42.119.146.95,10.1.0.4,51529,5358,T,I,D" 

                                ] 

                            } 

                        ] 

                    }, 

                    { 

                        "rule": "UserRule_default-allow-rdp", 

                        "flows": [ 

                            { 

                                "mac": "000D3AF8801A", 

                                "flowTuples": [ 

                                    "1487282370,163.28.66.17,10.1.0.4,61771,3389,T,I,A", 

                                    "1487282393,5.39.218.34,10.1.0.4,58596,3389,T,I,A", 

                                    "1487282393,91.224.160.154,10.1.0.4,61540,3389,T,I,A", 

                                    "1487282423,13.76.89.229,10.1.0.4,53163,3389,T,I,A" 

                                ] 

                            } 

                        ] 

                    } 

                ] 

            } 

        }, 

        { 

            "time": "2017-02-16T22:01:32.8960000Z", 

            "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434", 

            "category": "NetworkSecurityGroupFlowEvent", 

            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG", 

            "operationName": "NetworkSecurityGroupFlowEvents", 

            "properties": { 

                "Version": 1, 

                "flows": [ 

                    { 

                        "rule": "DefaultRule_DenyAllInBound", 

                        "flows": [ 

                            { 

                                "mac": "000D3AF8801A", 

                                "flowTuples": [ 

                                    "1487282481,195.78.210.194,10.1.0.4,53,1732,U,I,D" 

                                ] 

                            } 

                        ] 

                    }, 

                    { 

                        "rule": "UserRule_default-allow-rdp", 

                        "flows": [ 

                            { 

                                "mac": "000D3AF8801A", 

                                "flowTuples": [ 

                                    "1487282435,61.129.251.68,10.1.0.4,57776,3389,T,I,A",

                                    "1487282454,84.25.174.170,10.1.0.4,59085,3389,T,I,A",

                                    "1487282477,77.68.9.50,10.1.0.4,65078,3389,T,I,A"

                                ] 

                            } 

                        ] 

                    } 

                ] 

            } 

        } 

    ] 
}
```