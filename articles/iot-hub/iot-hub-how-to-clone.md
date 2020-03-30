---
title: Jak sklonować centrum Usługi Azure IoT
description: Jak sklonować centrum Usługi Azure IoT
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: robinsh
ms.openlocfilehash: c54853717f7e0b234df013e5aee575682d0d3d97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75429155"
---
# <a name="how-to-clone-an-azure-iot-hub-to-another-region"></a>Jak sklonować centrum Usługi Azure IoT hub do innego regionu

W tym artykule omówiono sposoby klonowania centrum IoT Hub i przedstawiono kilka pytań, na które należy odpowiedzieć przed rozpoczęciem. Oto kilka powodów, dla których warto sklonować centrum IoT Hub:
 
* Przenosisz firmę z jednego regionu do drugiego, na przykład z Europy do Ameryki Północnej (lub odwrotnie) i chcesz, aby twoje zasoby i dane były geograficznie blisko nowej lokalizacji, więc musisz przenieść centrum.

* Konfigurujesz centrum dla środowiska deweloperskiego i produkcyjnego.

* Chcesz wykonać niestandardową implementację wysokiej dostępności wielu centrów. Aby uzyskać więcej informacji, zobacz [jak osiągnąć przekrojowe przekrojowe sekcję WYSOKIEJ dostępności usługi IoT Hub o wysokiej dostępności i odzyskiwanie po awarii](iot-hub-ha-dr.md#achieve-cross-region-ha).

* Chcesz zwiększyć liczbę [partycji](iot-hub-scaling.md#partitions) skonfigurowanych dla koncentratora. Jest to ustawiane przy pierwszym utworzeniu koncentratora i nie można go zmienić. Można użyć informacji w tym artykule do sklonowania centrum i po utworzeniu klonu, zwiększyć liczbę partycji.

Aby sklonować koncentrator, potrzebujesz subskrypcji z dostępem administracyjnym do oryginalnego koncentratora. Nowe centrum można umieścić w nowej grupie zasobów i regionie w tej samej subskrypcji co oryginalne centrum, a nawet w nowej subskrypcji. Po prostu nie można użyć tej samej nazwy, ponieważ nazwa centrum musi być unikatowa globalnie.

> [!NOTE]
> W tej chwili nie ma żadnej funkcji dostępnej do automatycznego klonowania centrum IoT Hub. Jest to przede wszystkim proces ręczny, a zatem jest dość podatny na błędy. Złożoność klonowania koncentratora jest wprost proporcjonalna do złożoności koncentratora. Na przykład klonowanie centrum IoT hub bez routingu wiadomości jest dość proste. Jeśli dodasz routing wiadomości jako tylko jedną złożoność, klonowanie koncentratora staje się co najmniej o rząd wielkości bardziej skomplikowane. Jeśli również przenieść zasoby używane do routingu punktów końcowych, jest to inna kolejność magniture bardziej skomplikowane. 

## <a name="things-to-consider"></a>Rzeczy do rozważenia

Istnieje kilka rzeczy, które należy wziąć pod uwagę przed sklonowaniem centrum IoT hub.

* Upewnij się, że wszystkie funkcje dostępne w oryginalnej lokalizacji są również dostępne w nowej lokalizacji. Niektóre usługi są w wersji zapoznawczej i nie wszystkie funkcje są dostępne wszędzie.

* Nie należy usuwać oryginalnych zasobów przed utworzeniem i weryfikacją sklonowanej wersji. Po usunięciu koncentratora usługa ta zniknie na zawsze i nie ma możliwości jego odzyskania w celu sprawdzenia ustawień lub danych, aby upewnić się, że koncentrator jest poprawnie replikowany.

* Wiele zasobów wymaga globalnie unikatowych nazw, więc należy użyć różnych nazw dla sklonowanych wersji. Należy również użyć innej nazwy dla grupy zasobów, do której należy sklonowane centrum. 

* Dane dla oryginalnego centrum IoT hub nie są migrowane. Obejmuje to komunikaty telemetryczne, polecenia z chmury do urządzenia (C2D) oraz informacje związane z zadaniami, takie jak harmonogramy i historia. Metryki i wyniki rejestrowania również nie są migrowane. 

* W przypadku danych lub wiadomości kierowanych do usługi Azure Storage można pozostawić dane na oryginalnym koncie magazynu, przenieść te dane do nowego konta magazynu w nowym regionie lub pozostawić stare dane w miejscu i utworzyć nowe konto magazynu w nowej lokalizacji dla nowych danych. Aby uzyskać więcej informacji na temat przenoszenia danych w magazynie obiektów Blob, zobacz [Wprowadzenie do azcopy](../storage/common/storage-use-azcopy-v10.md).

* Nie można migrować danych dla centrów zdarzeń oraz tematów i kolejek usługi Service Bus. Jest to dane w czasie i nie jest przechowywany po wiadomości są przetwarzane.

* Należy zaplanować przestoje dla migracji. Klonowanie urządzeń do nowego koncentratora wymaga czasu. Jeśli używasz metody Import/Eksport, testy porównawcze wykazały, że przeniesienie 500 000 urządzeń może potrwać około dwóch godzin i cztery godziny na przeniesienie miliona urządzeń. 

* Urządzenia można kopiować do nowego koncentratora bez wyłączania i zmieniania urządzeń. 

    * Jeśli urządzenia zostały pierwotnie aprowizować przy użyciu dps, ponowne inicjowanie obsługi administracyjnej im aktualizuje informacje o połączeniu przechowywane w każdym urządzeniu. 
    
    * W przeciwnym razie należy użyć importu/eksportu metody, aby przenieść urządzenia, a następnie urządzenia muszą być zmodyfikowane, aby użyć nowego koncentratora. Na przykład można skonfigurować urządzenie do korzystania z nazwy hosta Usługi IoT Hub z bliźniaczej reprezentacji żądane właściwości. Urządzenie weźmie tę nazwę hosta Usługi IoT Hub, odłączy urządzenie od starego koncentratora i ponownie połączy go z nowym.
    
* Należy zaktualizować wszystkie używane certyfikaty, aby można było ich używać z nowymi zasobami. Prawdopodobnie centrum jest zdefiniowane w tabeli DNS — konieczne będzie zaktualizowanie tych informacji DNS.

## <a name="methodology"></a>Metodologia

Jest to ogólna metoda, którą zaleca się do przenoszenia centrum IoT hub z jednego regionu do drugiego. W przypadku routingu wiadomości zakłada się, że zasoby nie są przenoszone do nowego regionu. Aby uzyskać więcej informacji, zobacz [sekcję Routing wiadomości](#how-to-handle-message-routing).

   1. Eksportuj centrum i jego ustawienia do szablonu Menedżera zasobów. 
   
   1. Wprowadzać niezbędne zmiany w szablonie, takie jak aktualizowanie wszystkich wystąpień nazwy i lokalizacji sklonowanego koncentratora. W przypadku wszystkich zasobów w szablonie używanym do routingu punktów końcowych wiadomości należy zaktualizować klucz w szablonie dla tego zasobu.
   
   1. Zaimportuj szablon do nowej grupy zasobów w nowej lokalizacji. Spowoduje to utworzenie klona.

   1. Debugowanie w razie potrzeby. 
   
   1. Dodaj wszystko, co nie zostało wyeksportowane do szablonu. 
   
       Na przykład grupy odbiorców nie są eksportowane do szablonu. Należy ręcznie dodać grupy odbiorców do szablonu lub użyć [witryny Azure portal](https://portal.azure.com) po utworzeniu centrum. Istnieje przykład dodawania jednej grupy odbiorców do szablonu w [artykule Konfigurowanie routingu wiadomości usługi IoT Hub za pomocą szablonu usługi Azure Resource Manager.](tutorial-routing-config-message-routing-rm-template.md)
       
   1. Skopiuj urządzenia z oryginalnego koncentratora do klonu. Jest to opisane w sekcji [Zarządzanie urządzeniami zarejestrowanymi w centrum IoT hub](#managing-the-devices-registered-to-the-iot-hub).

## <a name="how-to-handle-message-routing"></a>Jak obsługiwać routing wiadomości

Jeśli koncentrator używa [routingu niestandardowego,](iot-hub-devguide-messages-read-custom.md)eksportowanie szablonu dla koncentratora zawiera konfigurację routingu, ale nie obejmuje samych zasobów. Należy wybrać, czy zasoby marszruty mają zostać przeniesione do nowej lokalizacji, czy pozostawić je na swoim miejscu i nadal używać ich "tak, jak jest". 

Załóżmy na przykład, że masz centrum w zachodnie stany USA, które kieruje wiadomości do konta magazynu (również w zachodnie stany USA) i chcesz przenieść centrum do wschodnich stanów USA. Możesz przenieść centrum i nadal kierować wiadomości na konto magazynu w zachodnie stany USA lub przenieść centrum, a także przenieść konto magazynu. Może to być mały wynik wykonania z routingu wiadomości do zasobów punktu końcowego w innym regionie.

Można przenieść koncentrator, który używa routingu wiadomości dość łatwo, jeśli nie można również przenieść zasoby używane dla punktów końcowych routingu. 

Jeśli koncentrator używa routingu wiadomości, masz dwie możliwości. 

1. Przenieś zasoby używane dla punktów końcowych marszruty do nowej lokalizacji.

    * Należy utworzyć nowe zasoby samodzielnie w [witrynie Azure portal](https://portal.azure.com) lub za pomocą szablonów Usługi Resource Manager. 

    * Należy zmienić nazwę wszystkich zasobów podczas tworzenia ich w nowej lokalizacji, ponieważ mają one globalnie unikatowe nazwy. 
     
    * Przed utworzeniem nowego centrum należy zaktualizować nazwy zasobów i klucze zasobów w szablonie nowego centrum. Zasoby powinny być obecne podczas tworzenia nowego centrum.

1. Nie należy przenosić zasobów używanych dla punktów końcowych routingu. Użyj ich "na miejscu".

   * W kroku, w którym edytujesz szablon, należy pobrać klucze dla każdego zasobu routingu i umieścić je w szablonie przed utworzeniem nowego centrum. 

   * Centrum nadal odwołuje się do oryginalnych zasobów routingu i kieruje do nich wiadomości zgodnie z konfiguracją.

   * Będzie miał mały wynik trafiony, ponieważ centrum i routingu zasobów punktu końcowego nie są w tej samej lokalizacji.

## <a name="prepare-to-migrate-the-hub-to-another-region"></a>Przygotowanie do migracji centrum do innego regionu

Ta sekcja zawiera szczegółowe instrukcje dotyczące migracji centrum.

### <a name="find-the-original-hub-and-export-it-to-a-resource-template"></a>Znajdź oryginalne centrum i wyeksportuj je do szablonu zasobu.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com). 

1. Przejdź do **grupy zasobów** i wybierz grupę zasobów zawierającą koncentrator, który chcesz przenieść. Można również przejść do **zasobów** i znaleźć centrum w ten sposób. Wybierz koncentrator.

1. Z listy właściwości i ustawień centrum wybierz **pozycję Eksportuj szablon.** 

   ![Zrzut ekranu przedstawiający polecenie eksportowania szablonu dla Centrum IoT Hub.](./media/iot-hub-how-to-clone/iot-hub-export-template.png)

1. Wybierz **pobierz,** aby pobrać szablon. Zapisz plik w miejscu, w których możesz go ponownie znaleźć. 

   ![Zrzut ekranu przedstawiający polecenie pobierania szablonu dla centrum IoT Hub.](./media/iot-hub-how-to-clone/iot-hub-download-template.png)

### <a name="view-the-template"></a>Wyświetlanie szablonu 

1. Przejdź do folderu Pobrane (lub do folderu użytego podczas eksportowania szablonu) i znajdź plik zip. Otwórz plik zip i znajdź `template.json`plik o nazwie . Zaznacz go, a następnie wybierz klawisze Ctrl+C, aby skopiować szablon. Przejdź do innego folderu, który nie znajduje się w pliku zip i wklej plik (Ctrl+V). Teraz możesz go edytować.
 
    Poniższy przykład dotyczy centrum ogólnego bez konfiguracji routingu. Jest to centrum warstwy S1 (z 1 jednostką) o nazwie **ContosoTestHub29358** w regionie **westus**. Oto wyeksportowany szablon.

    ``` json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "IotHubs_ContosoTestHub29358_name": {
                "defaultValue": "ContosoTestHub29358",
                "type": "String"
            }
        },
        "variables": {},
        "resources": [
            {
                "type": "Microsoft.Devices/IotHubs",
                "apiVersion": "2018-04-01",
                "name": "[parameters('IotHubs_ContosoTestHub29358_name')]",
                "location": "westus",
                "sku": {
                    "name": "S1",
                    "tier": "Standard",
                    "capacity": 1
                },
                "properties": {
                    "operationsMonitoringProperties": {
                        "events": {
                            "None": "None",
                            "Connections": "None",
                            "DeviceTelemetry": "None",
                            "C2DCommands": "None",
                            "DeviceIdentityOperations": "None",
                            "FileUploadOperations": "None",
                            "Routes": "None"
                        }
                    },
                    "ipFilterRules": [],
                    "eventHubEndpoints": {
                        "events": {
                            "retentionTimeInDays": 1,
                            "partitionCount": 2,
                            "partitionIds": [
                                "0",
                                "1"
                            ],
                            "path": "contosotesthub29358",
                            "endpoint": "sb://iothub-ns-contosotes-2227755-92aefc8b73.servicebus.windows.net/"
                        },
                        "operationsMonitoringEvents": {
                            "retentionTimeInDays": 1,
                            "partitionCount": 2,
                            "partitionIds": [
                                "0",
                                "1"
                            ],
                            "path": "contosotesthub29358-operationmonitoring",
                            "endpoint": "sb://iothub-ns-contosotes-2227755-92aefc8b73.servicebus.windows.net/"
                        }
                    },
                    "routing": {
                        "endpoints": {
                            "serviceBusQueues": [],
                            "serviceBusTopics": [],
                            "eventHubs": [],
                            "storageContainers": []
                        },
                        "routes": [],
                        "fallbackRoute": {
                            "name": "$fallback",
                            "source": "DeviceMessages",
                            "condition": "true",
                            "endpointNames": [
                                "events"
                            ],
                            "isEnabled": true
                        }
                    },
                    "storageEndpoints": {
                        "$default": {
                            "sasTtlAsIso8601": "PT1H",
                            "connectionString": "",
                            "containerName": ""
                        }
                    },
                    "messagingEndpoints": {
                        "fileNotifications": {
                            "lockDurationAsIso8601": "PT1M",
                            "ttlAsIso8601": "PT1H",
                            "maxDeliveryCount": 10
                        }
                    },
                    "enableFileUploadNotifications": false,
                    "cloudToDevice": {
                        "maxDeliveryCount": 10,
                        "defaultTtlAsIso8601": "PT1H",
                        "feedback": {
                            "lockDurationAsIso8601": "PT1M",
                            "ttlAsIso8601": "PT1H",
                            "maxDeliveryCount": 10
                        }
                    },
                    "features": "None"
                }
            }
        ]
    }
    ```

### <a name="edit-the-template"></a>Edytowanie szablonu 

Musisz wprowadzić pewne zmiany, zanim będzie można użyć szablonu do utworzenia nowego centrum w nowym regionie. Użyj [programu VS Code](https://code.visualstudio.com) lub edytora tekstu, aby edytować szablon.

#### <a name="edit-the-hub-name-and-location"></a>Edytowanie nazwy i lokalizacji centrum

1. Usuń sekcję parametrów u góry - znacznie prostsze jest użycie nazwy koncentratora, ponieważ nie będziemy mieć wielu parametrów. 

    ``` json
        "parameters": {
            "IotHubs_ContosoTestHub29358_name": {
                "defaultValue": "ContosoTestHub29358",
                "type": "String"
            }
        },
    ```

1. Zmień nazwę, aby użyć rzeczywistej (nowej) nazwy, zamiast pobierać ją z parametru (który został usunięty w poprzednim kroku). 

    W przypadku nowego koncentratora użyj nazwy oryginalnego koncentratora oraz *klonu* ciągu, aby uzupełnić nową nazwę. Zacznij od wyczyszcząc nazwę i lokalizację koncentratora.
    
    Stara wersja:

    ``` json 
    "name": "[parameters('IotHubs_ContosoTestHub29358_name')]",
    "location": "westus",
    ```
    
    Nowa wersja: 

    ``` json 
    "name": "ContosoTestHub29358clone",
    "location": "eastus",
    ```

    Następnie okaże się, że wartości **ścieżki** zawierają starą nazwę koncentratora. Zmień je, aby użyć nowego. Są to wartości ścieżki w obszarze **eventHubEndpoints** o nazwie **events** and **OperationsMonitoringEvents**.

    Po zakończeniu sekcja punktów końcowych centrum zdarzeń powinna wyglądać następująco:

    ``` json
    "eventHubEndpoints": {
        "events": {
            "retentionTimeInDays": 1,
            "partitionCount": 2,
            "partitionIds": [
                "0",
                "1"
            ],
            "path": "contosotesthub29358clone",
            "endpoint": "sb://iothub-ns-contosotes-2227755-92aefc8b73.servicebus.windows.net/"
        },
        "operationsMonitoringEvents": {
            "retentionTimeInDays": 1,
            "partitionCount": 2,
            "partitionIds": [
                "0",
                "1"
            ],
            "path": "contosotesthub29358clone-operationmonitoring",
            "endpoint": "sb://iothub-ns-contosotes-2227755-92aefc8b73.servicebus.windows.net/"
        }
    ```

#### <a name="update-the-keys-for-the-routing-resources-that-are-not-being-moved"></a>Aktualizowanie kluczy dla zasobów routingu, które nie są przenoszone

Podczas eksportowania szablonu Menedżera zasobów dla koncentratora, który ma skonfigurowany routing, zobaczysz, że klucze dla tych zasobów nie są podane w wyeksportowanym szablonie - ich umieszczenie jest oznaczone gwiazdkami. Należy je wypełnić, przechodząc do tych zasobów w portalu i pobierając klucze **przed** zaimportowanie nowego szablonu centrum i utworzenie centrum. 

1. Pobierz klucze wymagane dla dowolnego zasobu routingu i umieść je w szablonie. Klucze można pobrać z zasobu w [witrynie Azure portal](https://portal.azure.com). 

   Na przykład jeśli wysyłasz wiadomości do kontenera magazynu, znajdź konto magazynu w portalu. W sekcji Ustawienia wybierz pozycję **Klawisze dostępu**, a następnie skopiuj jeden z klawiszy. Oto jak wygląda klucz podczas pierwszego eksportowania szablonu:

   ```json
   "connectionString": "DefaultEndpointsProtocol=https;
   AccountName=fabrikamstorage1234;AccountKey=****",
   "containerName": "fabrikamresults",
   ```

1. Po pobraniu klucza konta dla konta magazynu umieść go `AccountKey=****` w szablonie w klauzuli w miejscu gwiazdek. 

1. W przypadku kolejek magistrali usług pobierz klucz dostępu udostępnionego pasujący do sharedaccessKeyName. Oto klucz i `SharedAccessKeyName` w json:

   ```json
   "connectionString": "Endpoint=sb://fabrikamsbnamespace1234.servicebus.windows.net:5671/;
   SharedAccessKeyName=iothubroutes_FabrikamResources;
   SharedAccessKey=****;
   EntityPath=fabrikamsbqueue1234",
   ```

1. To samo dotyczy tematów usługi Service Bus i połączeń Centrum zdarzeń.

#### <a name="create-the-new-routing-resources-in-the-new-location"></a>Tworzenie nowych zasobów routingu w nowej lokalizacji

Ta sekcja ma zastosowanie tylko w przypadku przenoszenia zasobów używanych przez koncentrator dla punktów końcowych routingu.

Jeśli chcesz przenieść zasoby routingu, musisz ręcznie skonfigurować zasoby w nowej lokalizacji. Zasoby routingu można utworzyć za pomocą [portalu Azure](https://portal.azure.com)lub wyeksportować szablon Menedżera zasobów dla każdego z zasobów używanych przez routing wiadomości, edytowanie ich i importowanie. Po skonfigurowaniu zasobów można zaimportować szablon koncentratora (który zawiera konfigurację routingu).

1. Utwórz każdy zasób używany przez marszrutę. Można to zrobić ręcznie za pomocą [witryny Azure portal](https://portal.azure.com)lub utworzyć zasoby przy użyciu szablonów Menedżera zasobów. Jeśli chcesz użyć szablonów, są to kroki, które należy wykonać:

    1. Dla każdego zasobu używanego przez marszrutę wyeksportuj go do szablonu Menedżera zasobów.
    
    1. Zaktualizuj nazwę i lokalizację zasobu. 

    1. Zaktualizuj wszelkie odsyłacze między zasobami. Na przykład jeśli tworzysz szablon dla nowego konta magazynu, musisz zaktualizować nazwę konta magazynu w tym szablonie i każdy inny szablon, który odwołuje się do niego. W większości przypadków sekcja routingu w szablonie dla centrum jest jedynym innym szablonem, który odwołuje się do zasobu. 

    1. Importuj każdy z szablonów, który wdraża każdy zasób.

    Po skonfigurowaniu i uruchomieniu zasobów używanych przez marszrutę można kontynuować.

1. W szablonie dla centrum IoT zmienić nazwę każdego z zasobów routingu na jego nową nazwę i zaktualizować lokalizację w razie potrzeby. 

Teraz masz szablon, który utworzy nowy koncentrator, który wygląda prawie dokładnie tak, jak stare centrum, w zależności od tego, jak zdecydowałeś się obsłużyć routingu.

## <a name="move----create-the-new-hub-in-the-new-region-by-loading-the-template"></a>Przenieś - utwórz nowe centrum w nowym regionie, ładując szablon

Utwórz nowe centrum w nowej lokalizacji przy użyciu szablonu. Jeśli masz zasoby routingu, które mają być przeniesione, zasoby powinny być skonfigurowane w nowej lokalizacji i odwołania w szablonie zaktualizowane do dopasowania. Jeśli nie przenosisz zasobów routingu, powinny one znajdować się w szablonie ze zaktualizowanymi kluczami.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com).

1. Wybierz pozycję **Utwórz zasób**. 

1. W polu wyszukiwania umieść w polu "wdrożenie szablonu" i wybierz enter.

1. Wybierz **wdrożenie szablonu (wdrażanie przy użyciu szablonów niestandardowych).** Spowoduje to przejście do ekranu dla wdrożenia szablonu. Wybierz **pozycję Utwórz**. Zostanie wyświetlony następujący ekran:

   ![Zrzut ekranu przedstawiający polecenie tworzenia własnego szablonu](./media/iot-hub-how-to-clone/iot-hub-custom-deployment.png)

1. Wybierz **pozycję Zbuduj własny szablon w edytorze,** który umożliwia przesłanie szablonu z pliku. 

1. Wybierz **pozycję Załaduj plik**. 

   ![Zrzut ekranu przedstawiający polecenie przesyłania pliku szablonu](./media/iot-hub-how-to-clone/iot-hub-upload-file.png)

1. Wyszukaj nowy edytowany szablon i wybierz go, a następnie wybierz pozycję **Otwórz**. Ładuje szablon w oknie edycji. Wybierz **pozycję Zapisz**. 

   ![Zrzut ekranu przedstawiający ładowanie szablonu](./media/iot-hub-how-to-clone/iot-hub-loading-template.png)

1. Wypełnij poniższe pola.

   **Subskrypcja**: wybierz subskrypcję do użycia.

   **Grupa zasobów**: utwórz nową grupę zasobów w nowej lokalizacji. Jeśli masz już nową konfigurację, możesz ją wybrać zamiast tworzyć nową.

   **Lokalizacja:** Jeśli wybrano istniejącą grupę zasobów, zostanie ono wypełnione, aby dopasować lokalizację grupy zasobów. Jeśli utworzono nową grupę zasobów, będzie to jej lokalizacja.

   **Zgadzam się pole wyboru:** to w zasadzie mówi, że zgadzasz się zapłacić za zasób (s) tworzysz.

1. Wybierz przycisk **Zakup.**

Portal sprawdza teraz szablon i wdraża sklonowane centrum. Jeśli masz dane konfiguracji routingu, zostaną one uwzględnione w nowym koncentratorze, ale wskaż zasoby w poprzedniej lokalizacji.

## <a name="managing-the-devices-registered-to-the-iot-hub"></a>Zarządzanie urządzeniami zarejestrowanymi w centrum IoT

Teraz, gdy klon jest uruchomiony, musisz skopiować wszystkie urządzenia z oryginalnego koncentratora do klonu. 

Istnieje wiele sposobów, aby to osiągnąć. Pierwotnie używano [usługi inicjowania obsługi administracyjnej (DPS)](/azure/iot-dps/about-iot-dps)do aprowizowania urządzeń lub nie. Jeśli tak, to nie jest trudne. Jeśli nie, może to być bardzo skomplikowane. 

Jeśli nie używasz DPS do aprowizowania urządzeń, możesz pominąć następną sekcję i zacząć od [za pomocą importu/eksportu, aby przenieść urządzenia do nowego koncentratora](#using-import-export-to-move-the-devices-to-the-new-hub).

## <a name="using-dps-to-re-provision-the-devices-in-the-new-hub"></a>Używanie dps do ponownego aprowizowania urządzeń w nowym koncentratorze

Aby użyć dps do przeniesienia urządzeń do nowej lokalizacji, zobacz [Jak ponownie aprowizować urządzenia](../iot-dps/how-to-reprovision.md). Po zakończeniu możesz wyświetlić urządzenia w [witrynie Azure portal](https://portal.azure.com) i sprawdzić, czy znajdują się w nowej lokalizacji.

Przejdź do nowego centrum za pomocą [witryny Azure Portal](https://portal.azure.com). Wybierz koncentrator, a następnie wybierz pozycję **Urządzenia IoT**. Widoczne są urządzenia, które zostały ponownie aprowizowane do sklonowanego koncentratora. Można również wyświetlić właściwości sklonowanego koncentratora. 

Jeśli zaimplementowano routing, należy przetestować i upewnij się, że wiadomości są kierowane do zasobów poprawnie.

### <a name="committing-the-changes-after-using-dps"></a>Zatwierdzanie zmian po użyciu DPS

Ta zmiana została zatwierdzona przez usługę DPS.

### <a name="rolling-back-the-changes-after-using-dps"></a>Wycofywanie zmian po użyciu DPS. 

Jeśli chcesz wycofać zmiany, ponownie aprowizować urządzenia z nowego koncentratora do starego.

Teraz zakończono migrację koncentratora i jego urządzeń. Możesz przejść do [clean-up](#clean-up).

## <a name="using-import-export-to-move-the-devices-to-the-new-hub"></a>Przenoszenie urządzeń do nowego koncentratora za pomocą importu i eksportu

Aplikacja jest przeznaczona dla platformy .NET Core, dzięki czemu można ją uruchomić w systemie Windows lub Linux. Można pobrać przykład, pobrać parametry połączenia, ustawić flagi, dla których bitów chcesz uruchomić i uruchomić go. Można to zrobić bez otwierania kodu.

### <a name="downloading-the-sample"></a>Pobieranie próbki

1. Użyj przykładów języka IoT C# z tej strony: [Przykłady usługi Azure IoT dla języka C#](https://azure.microsoft.com/resources/samples/azure-iot-samples-csharp/). Pobierz plik zip i rozpaj go na komputerze. 

1. Odpowiedni kod znajduje się w ./iot-hub/Samples/service/ImportExportDevicesSample. Aby uruchomić aplikację, nie trzeba wyświetlać ani edytować kodu.

1. Aby uruchomić aplikację, należy określić trzy parametry połączenia i pięć opcji. Przekazujesz te dane jako argumenty wiersza polecenia lub używasz zmiennych środowiskowych lub użyj kombinacji tych dwóch. Będziemy przekazywać opcje jako argumenty wiersza polecenia, a parametry połączenia jako zmienne środowiskowe. 

   Powodem tego jest to, że parametry połączenia są długie i niezgrabne i mało prawdopodobne, aby zmienić, ale można zmienić opcje i uruchomić aplikację więcej niż jeden raz. Aby zmienić wartość zmiennej środowiskowej, należy zamknąć okno polecenia i Visual Studio lub VS Code, niezależnie od tego, co używasz. 

### <a name="options"></a>Opcje

Oto pięć opcji określonych podczas uruchamiania aplikacji. Umieścimy je w wierszu polecenia w ciągu minuty.

*   **addDevices** (argument 1) - ustaw tę wartość na true, jeśli chcesz dodać urządzenia wirtualne, które są generowane dla Ciebie. Są one dodawane do centrum źródłowego. Ponadto ustaw **numToAdd** (argument 2), aby określić liczbę urządzeń, które chcesz dodać. Maksymalna liczba urządzeń, które można zarejestrować w centrum jest milion. Celem tej opcji jest testowanie — można wygenerować określoną liczbę urządzeń, a następnie skopiować je do innego koncentratora.

*   **copyDevices** (argument 3) -- ustaw to na true, aby skopiować urządzenia z jednego koncentratora do drugiego. 

*   **deleteSourceDevices** (argument 4) -- ustaw to na true, aby usunąć wszystkie urządzenia zarejestrowane w centrum źródłowym. Zalecamy oczekiwanie, aż masz pewność, że wszystkie urządzenia zostały przeniesione przed uruchomieniem tego. Po usunięciu urządzeń nie można ich odzyskać.

*   **deleteDestDevices** (argument 5) -- ustaw to na true, aby usunąć wszystkie urządzenia zarejestrowane w centrum docelowym (klon). Możesz to zrobić, jeśli chcesz skopiować urządzenia więcej niż jeden raz. 

Podstawowym poleceniem będzie *dotnet run* -- to mówi .NET do utworzenia lokalnego pliku csproj, a następnie uruchomić go. Przed uruchomieniem należy dodać argumenty wiersza polecenia na końcu. 

Wiersz polecenia będzie wyglądał jak w następujących przykładach:

``` console 
    // Format: dotnet run add-devices num-to-add copy-devices delete-source-devices delete-destination-devices

    // Add 1000 devices, don't copy them to the other hub, or delete them. 
    // The first argument is true, numToAdd is 50, and the other arguments are false.
    dotnet run true 1000 false false false 

    // Copy the devices you just added to the other hub; don't delete anything.
    // The first argument is false, numToAdd is 0, copy-devices is true, and the delete arguments are both false
    dotnet run false 0 true false false 
```

### <a name="using-environment-variables-for-the-connection-strings"></a>Używanie zmiennych środowiskowych dla ciągów połączeń

1. Aby uruchomić przykład, potrzebujesz parametry połączenia do starych i nowych centrów IoT i do konta magazynu, którego można użyć dla tymczasowych plików roboczych. Będziemy przechowywać wartości dla nich w zmiennych środowiskowych.

1. Aby uzyskać wartości ciągu połączenia, zaloguj się do [witryny Azure Portal](https://portal.azure.com). 

1. Umieść parametry połączenia w miejscu, w jakim można je odzyskać, na przykład NotePad. Jeśli skopiujesz następujące, można wkleić parametry połączenia bezpośrednio tam, gdzie idą. Nie dodawaj spacji wokół znaku równości ani nie zmienia nazwy zmiennej. Ponadto nie trzeba podwójnych cudzysłowów wokół ciągów połączeń. Jeśli umieścisz cudzysłowy wokół ciągu połączenia konta magazynu, nie będzie działać.

   W systemie Windows w ten sposób ustawiasz zmienne środowiskowe:

   ``` console  
   SET IOTHUB_CONN_STRING=<put connection string to original IoT Hub here>
   SET DEST_IOTHUB_CONN_STRING=<put connection string to destination or clone IoT Hub here>
   SET STORAGE_ACCT_CONN_STRING=<put connection string to the storage account here>
   ```
 
   W przypadku systemu Linux w ten sposób definiujesz zmienne środowiskowe:

   ``` console  
   export IOTHUB_CONN_STRING="<put connection string to original IoT Hub here>"
   export DEST_IOTHUB_CONN_STRING="<put connection string to destination or clone IoT Hub here>"
   export STORAGE_ACCT_CONN_STRING="<put connection string to the storage account here>"
   ```

1. Dla ciągów połączeń centrum IoT przejdź do każdego koncentratora w portalu. Można wyszukiwać w **zasobach** dla centrum. Jeśli znasz grupę zasobów, możesz przejść do **grup zasobów**, wybierz grupę zasobów, a następnie wybierz centrum z listy zasobów w tej grupie zasobów. 

1. Wybierz **pozycję Zasady dostępu współdzielonego** w ustawieniach centrum, a następnie wybierz **iothubowner** i skopiuj jeden z ciągów połączeń. Zrób to samo dla centrum docelowego. Dodaj je do odpowiednich poleceń SET.

1. Dla ciągu połączenia konta magazynu znajdź konto magazynu w **zasobach** lub w jego **grupie zasobów** i otwórz je. 
   
1. W sekcji Ustawienia wybierz pozycję **Klawisze dostępu** i skopiuj jeden z ciągów połączeń. Umieść ciąg połączenia w pliku tekstowym dla odpowiedniego polecenia SET. 

Teraz masz zmienne środowiskowe w pliku z poleceniami SET i wiesz, jakie są twoje argumenty wiersza polecenia. Uruchommy próbkę.

### <a name="running-the-sample-application-and-using-command-line-arguments"></a>Uruchamianie przykładowej aplikacji i używanie argumentów wiersza polecenia

1. Otwórz okno wiersza polecenia. Wybierz pozycję Windows `command prompt` i wpisz, aby uzyskać okno wiersza polecenia.

1. Skopiuj polecenia, które ustawiają zmienne środowiskowe po jednym naraz, i wklej je do okna wiersza polecenia i wybierz pozycję Enter. Po zakończeniu wpisz `SET` w oknie wiersza polecenia, aby wyświetlić zmienne środowiskowe i ich wartości. Po skopiowaniu ich do okna wiersza polecenia nie trzeba ich ponownie kopiować, chyba że otworzysz nowe okno wiersza polecenia.

1. W oknie wiersza polecenia zmieniaj katalogi, dopóki nie znajdujesz się w pliku ./ImportExportDevicesSample (gdzie istnieje plik ImportExportDevicesSample.csproj). Następnie wpisz następujące polecenie i dołącz argumenty wiersza polecenia.

    ``` console
    // Format: dotnet run add-devices num-to-add copy-devices delete-source-devices delete-destination-devices
    dotnet run arg1 arg2 arg3 arg4 arg5
    ```

    Polecenie dotnet tworzy i uruchamia aplikację. Ponieważ są przekazywanie w opcjach po uruchomieniu aplikacji, można zmienić ich wartości przy każdym uruchomieniu aplikacji. Na przykład można uruchomić go raz i utworzyć nowe urządzenia, a następnie uruchomić go ponownie i skopiować te urządzenia do nowego koncentratora i tak dalej. Można również wykonać wszystkie kroki w tym samym przebiegu, chociaż nie zaleca się usuwania żadnych urządzeń, dopóki nie masz pewności, że skończysz z klonowaniem. Oto przykład, który tworzy 1000 urządzeń, a następnie kopiuje je do innego koncentratora.

    ``` console
    // Format: dotnet run add-devices num-to-add copy-devices delete-source-devices delete-destination-devices

    // Add 1000 devices, don't copy them to the other hub or delete them. 
    dotnet run true 1000 false false false 

    // Do not add any devices. Copy the ones you just created to the other hub; don't delete anything.
    dotnet run false 0 true false false 
    ```

    Po sprawdzeniu, czy urządzenia zostały pomyślnie skopiowane, można usunąć urządzenia z centrum źródłowego w ten sposób:

   ``` console
   // Format: dotnet run add-devices num-to-add copy-devices delete-source-devices delete-destination-devices
   // Delete the devices from the source hub.
   dotnet run false 0 false true false 
   ```

### <a name="running-the-sample-application-using-visual-studio"></a>Uruchamianie przykładowej aplikacji przy użyciu programu Visual Studio

1. Jeśli chcesz uruchomić aplikację w programie Visual Studio, zmień bieżący katalog na folder, w którym znajduje się plik IoTHubServiceSamples.sln. Następnie uruchom to polecenie w oknie wiersza polecenia, aby otworzyć rozwiązanie w programie Visual Studio. Należy to zrobić w tym samym oknie polecenia, w którym ustawiono zmienne środowiskowe, aby te zmienne były znane.

   ``` console       
   IoTHubServiceSamples.sln
   ```
    
1. Kliknij prawym przyciskiem myszy projekt *ImportExportDevicesSample* i wybierz pozycję **Ustaw jako projekt startowy**.    
    
1. Ustaw zmienne u góry Program.cs w folderze ImportExportDevicesSample dla pięciu opcji.

   ``` csharp
   // Add randomly created devices to the source hub.
   private static bool addDevices = true;
   //If you ask to add devices, this will be the number added.
   private static int numToAdd = 0; 
   // Copy the devices from the source hub to the destination hub.
   private static bool copyDevices = false;
   // Delete all of the devices from the source hub. (It uses the IoTHubConnectionString).
   private static bool deleteSourceDevices = false;
   // Delete all of the devices from the destination hub. (Uses the DestIotHubConnectionString).
   private static bool deleteDestDevices = false;
   ```

1. Wybierz F5, aby uruchomić aplikację. Po zakończeniu pracy można wyświetlić wyniki.

### <a name="view-the-results"></a>Wyświetlanie wyników 

Możesz wyświetlić urządzenia w [witrynie Azure portal](https://portal.azure.com) i sprawdzić, czy znajdują się w nowej lokalizacji.

1. Przejdź do nowego centrum za pomocą [witryny Azure Portal](https://portal.azure.com). Wybierz koncentrator, a następnie wybierz pozycję **Urządzenia IoT**. Zobaczysz urządzenia skopiowane ze starego koncentratora do sklonowanego koncentratora. Można również wyświetlić właściwości sklonowanego koncentratora. 

1. Sprawdź błędy importu/eksportu, przechodząc do konta magazynu platformy Azure `devicefiles` w [witrynie Azure portal](https://portal.azure.com) i szukając w kontenerze `ImportErrors.log`dla . Jeśli ten plik jest pusty (rozmiar wynosi 0), nie wystąpiły żadne błędy. Jeśli spróbujesz zaimportować to samo urządzenie więcej niż raz, odrzuca urządzenie po raz drugi i dodaje komunikat o błędzie do pliku dziennika.

### <a name="committing-the-changes"></a>Zatwierdzanie zmian 

W tym momencie centrum zostało skopiowane do nowej lokalizacji i zmigrowane urządzenia do nowego klonu. Teraz musisz wprowadzić zmiany, aby urządzenia działały z sklonowanym koncentratorem.

Aby zatwierdzić zmiany, oto kroki, które należy wykonać: 

* Zaktualizuj każde urządzenie, aby zmienić nazwę hosta usługi IoT Hub, aby wskazać nazwę hosta usługi IoT Hub na nowe centrum. Należy to zrobić przy użyciu tej samej metody, która została użyta podczas pierwszej inicjowania obsługi administracyjnej urządzenia.

* Zmień wszystkie aplikacje, które odnoszą się do starego koncentratora, aby wskazać nowy koncentrator.

* Po zakończeniu nowy koncentrator powinien być uruchomiony. Stary koncentrator nie powinien mieć żadnych aktywnych urządzeń i być w stanie rozłączenia. 

### <a name="rolling-back-the-changes"></a>Wycofywanie zmian

Jeśli zdecydujesz się wycofać zmiany, oto kroki do wykonania:

* Zaktualizuj każde urządzenie, aby zmienić nazwa hosta usługi IoT Hub, aby wskazać nazwa hosta usługi IoT Hub dla starego koncentratora. Należy to zrobić przy użyciu tej samej metody, która została użyta podczas pierwszej inicjowania obsługi administracyjnej urządzenia.

* Zmień wszystkie aplikacje, które odnoszą się do nowego koncentratora, aby wskazać stare centrum. Na przykład jeśli korzystasz z usługi Azure Analytics, może być konieczne ponowne skonfigurowanie [danych wejściowych usługi Azure Stream Analytics.](../stream-analytics/stream-analytics-define-inputs.md#stream-data-from-iot-hub)

* Usuń nowy koncentrator. 

* Jeśli masz zasoby routingu, konfiguracja na starym koncentratorze powinna nadal wskazywać poprawną konfigurację routingu i powinna pracować z tymi zasobami po ponownym uruchomieniu koncentratora.

### <a name="checking-the-results"></a>Sprawdzanie wyników 

Aby sprawdzić wyniki, zmień rozwiązanie IoT, aby wskazać centrum w nowej lokalizacji i uruchom go. Innymi słowy wykonaj te same akcje z nowym koncentratorem, który został wykonany z poprzednim koncentratorem i upewnij się, że działają poprawnie. 

Jeśli zaimplementowano routing, należy przetestować i upewnij się, że wiadomości są kierowane do zasobów poprawnie.

## <a name="clean-up"></a>Oczyszczanie

Nie sprzątaj, dopóki nie masz pewności, że nowy koncentrator jest uruchomiony, a urządzenia działają poprawnie. Należy również przetestować routingu, jeśli używasz tej funkcji. Gdy wszystko będzie gotowe, wyczyść stare zasoby, wykonując następujące czynności:

* Jeśli jeszcze tego nie zrobiłeś, usuń stary koncentrator. Spowoduje to usunięcie wszystkich aktywnych urządzeń z koncentratora.

* Jeśli masz zasoby routingu, które zostały przeniesione do nowej lokalizacji, można usunąć stare zasoby routingu.

## <a name="next-steps"></a>Następne kroki

Sklonowałeś centrum IoT hub do nowego koncentratora w nowym regionie wraz z urządzeniami. Aby uzyskać więcej informacji na temat wykonywania operacji zbiorczych w rejestrze tożsamości w Centrum IoT, zobacz [Zbiorcze importowanie i eksportowanie tożsamości urządzeń usługi IoT Hub.](iot-hub-bulk-identity-mgmt.md)

Aby uzyskać więcej informacji na temat usługi IoT Hub i rozwoju centrum, zobacz następujące artykuły.

* [Przewodnik dla deweloperów usługi IoT Hub](iot-hub-devguide.md)

* [Samouczek routingu usługi IoT Hub](tutorial-routing.md)

* [Omówienie zarządzania urządzeniami w centrum IoT](iot-hub-device-management-overview.md)

* Jeśli chcesz wdrożyć przykładową aplikację, zobacz [wdrożenie aplikacji .NET Core](https://docs.microsoft.com/dotnet/core/deploying/index).
