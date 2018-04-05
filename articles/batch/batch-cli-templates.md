---
title: Uruchom partii zadań Azure zadania end-to-end bez pisania kodu (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: Utwórz pliki szablonów dla wiersza polecenia platformy Azure utworzyć pule, zadań i zadań wsadowych.
services: batch
author: mscurrell
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: na
ms.topic: article
ms.workload: big-compute
ms.date: 12/18/2017
ms.author: markscu
ms.openlocfilehash: c991a1535b82a76a3d0b9bdbf4ede8f3e22bc866
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2018
---
# <a name="use-azure-batch-cli-templates-and-file-transfer-preview"></a>Korzystanie z szablonów interfejsu wiersza polecenia usługi Azure Batch i transferu plików (wersja zapoznawcza)

Przy użyciu wiersza polecenia platformy Azure jest możliwe uruchamianie zadań wsadowych bez pisania kodu.

Tworzenie i używanie plików szablonu z wiersza polecenia platformy Azure do tworzenia pul, zadań i zadań wsadowych. Pliki wejściowe zadania mogą być łatwo przekazywane do konta magazynu skojarzone z partii konta i zadania pliki wyjściowe pobrane.

## <a name="overview"></a>Przegląd

Rozszerzenie interfejsu wiersza polecenia Azure Umożliwia wsadowe być używane na całej trasie przez użytkowników, którzy nie są deweloperów. Można utworzyć puli, przekazać dane wejściowe, zadań i skojarzonych zadań tworzenia i danych wyjściowych pobrane — żaden kod nie jest wymagane, interfejsu wiersza polecenia używane bezpośrednio lub zintegrowania skryptów.

Tworzenie szablonów usługi partia zadań na [istniejących obsługę partii w wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/batch/batch-cli-get-started#json-files-for-resource-creation) umożliwiająca pliki w formacie JSON można określić wartości właściwości dla tworzenia pul, zadania, zadania i inne elementy. Z szablonami partii przez co to jest możliwe za pomocą pliki w formacie JSON dodano następujące możliwości:

-   Można zdefiniować parametrów. Jeśli ten szablon jest używany, tylko wartości parametrów są określony w celu utworzenia elementu z innego elementu wartości właściwości jest określony w treści szablonu. Użytkownik, który obsługuje usługę partii i aplikacje do uruchamiania przez partię można utworzyć szablony, określając puli, zadania i wartości właściwości zadania. Użytkownik mniej znanych z partii i/lub aplikacje tylko należy określić wartości dla określonych parametrów.

-   Zadanie fabryki zadań utworzyć jedno lub więcej zadań skojarzonych z zadania, unikając konieczności wiele definicje zadań do utworzenia i znacznie uproszczenia przesyłanie zadań.


Pliki danych wejściowych muszą być dostarczane na potrzeby zadań i często są produkowane plików danych wyjściowych. Konto magazynu jest skojarzony z każdego konta usługi partia zadań domyślnie i pliki można łatwo przenosić do i z tego konta magazynu przy użyciu interfejsu wiersza polecenia, z nie kodowania i wymagane nie poświadczenia magazynu.

Na przykład [ffmpeg](http://ffmpeg.org/) jest popularnych aplikacji, która przetwarza plików audio i wideo. Azure CLI partii może służyć do wywołania ffmpeg transkodowanie źródła wideo pliki do innego rozwiązania.

-   Utworzono szablon puli. Tworzenie szablonu zna sposób wywołania ffmpeg aplikacji i jej wymagań; Określa odpowiedni system operacyjny, maszyna wirtualna rozmiar, jak ffmpeg jest zainstalowana (od pakietu aplikacji lub za pomocą Menedżera pakietów, na przykład) i inne wartości właściwości w puli. Parametry są tworzone, gdy używany jest szablon, tylko identyfikator puli i liczbę maszyn wirtualnych muszą być określone.

-   Utworzono szablon zadania. Tworzenie szablonu użytkownika wie jak ffmpeg musi być wywoływane ze źródłem transkodowanie wideo do innego rozwiązania i określa wiersz polecenia zadania; które znają, że istnieje folder zawierający pliki wideo źródła z zadaniem wymagane dla pliku wejściowego.

-   Użytkownik końcowy zestaw plików wideo transkodowanie najpierw tworzy puli przy użyciu szablonu puli tylko identyfikator puli i liczbę maszyn wirtualnych wymagana. Następnie przekazywać pliki źródłowe transkodowanie. Następnie można przesłać zadania przy użyciu szablonu zadania, określając tylko identyfikator puli i lokalizację plików źródłowych przekazany. Zadania wsadowego jest tworzony z jedno zadanie na Trwa generowanie pliku wejściowego. Ponadto pliki wyjściowe przekodowane może być pobierania.

## <a name="installation"></a>Instalacja

Możliwości transferu szablonu i pliku wymagają rozszerzenia do zainstalowania.

Instrukcje dotyczące sposobu instalowania wiersza polecenia platformy Azure można znaleźć [zainstalować Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli).

Po zainstalowaniu interfejsu wiersza polecenia Azure najnowszej wersji rozszerzenia partii można zainstalować przy użyciu interfejsu wiersza polecenia następujące polecenie:

```azurecli
az extension add --source https://github.com/Azure/azure-batch-cli-extensions/releases/download/azure-batch-cli-extensions-2.0.1/azure_batch_cli_extensions-2.0.1-py2.py3-none-any.whl
```

Aby uzyskać więcej informacji o rozszerzeniu partii, zobacz [Microsoft Azure partii CLI rozszerzeń dla systemu Windows, Mac i Linux](https://github.com/Azure/azure-batch-cli-extensions#microsoft-azure-batch-cli-extensions-for-windows-mac-and-linux).

## <a name="templates"></a>Szablony

Interfejsu wiersza polecenia dla usługi partia zadań Azure umożliwia elementów, takich jak pule, zadań i zadań, które ma zostać utworzony przez określenie plik JSON zawierający nazwy i wartości właściwości. Na przykład:

```azurecli
az batch pool create –-json-file AppPool.json
```

Szablony usługi partia zadań Azure są podobne do szablonów usługi Azure Resource Manager, funkcje i składni. Są to pliki w formacie JSON, które zawiera element nazwy i wartości właściwości, ale Dodaj następujące główne pojęcia:

-   **Parametry**

    -   Zezwalaj na wartości właściwości w części treści, można określić tylko wartości parametru konieczności dostarczane, gdy ten szablon jest używany. Na przykład pełnej definicji dla puli można umieścić w treści i tylko jeden parametr zdefiniowane dla Identyfikator puli; tylko ciąg Identyfikatora puli w związku z tym musi być dostarczane chcesz utworzyć pulę.
        
    -   Treść szablonów można tworzyć przez osobę mającą wiedzę na temat aplikacji wsadowych i aplikacji do uruchomienia przez partię; Jeśli ten szablon jest używany, należy podać tylko wartości parametrów zdefiniowane przez autora. Użytkownik bez szczegółowych partii i/lub bazy wiedzy w związku z tym można użyć szablonów.

-   **Zmienne**

    -   Zezwalaj na wartości parametrów prostymi lub złożonymi określona w jednym miejscu i używany w co najmniej jednego miejsca w treści szablonu. Zmienne można uprościć i zredukować rozmiar szablonu, a także była bardziej utrzymaniu przez jedną lokalizację, aby zmienić właściwości, którego wartość może zmienić.

-   **Konstrukcji wyższego poziomu**

    -   Niektóre konstrukcji wyższego poziomu dostępnych w szablonie, który nie są jeszcze dostępne, interfejsy API partii. Na przykład można zdefiniować fabryki zadań w szablonie zadania, który tworzy wiele zadań dla zadania przy użyciu wspólnej definicji zadania. Te konstrukcje uniknąć konieczności kod, aby dynamicznie utworzyć wiele plików JSON, takich jak jeden plik poszczególnych zadań, a także tworzyć plików skryptów do instalowania aplikacji za pomocą Menedżera pakietów, na przykład.

    -   W pewnym momencie Jeśli to możliwe, tych konstrukcji mogą być dodawane do usługi partia zadań i dostępne w partii interfejsów API, UI itp.

### <a name="pool-templates"></a>Szablony puli

Oprócz możliwości standardowego szablonu parametry i zmienne następujące konstrukcji wyższego poziomu są obsługiwane przez szablon puli:

-   **Odwołania do pakietu**

    -   Opcjonalnie umożliwia oprogramowania ma zostać skopiowany do węzłów puli za pomocą Menedżera pakietów. Określono Menedżera pakietów i identyfikator pakietu. Możliwość deklaruje co najmniej jednego pakietu pozwala uniknąć konieczności tworzenia skrypt, który pobiera wymagane pakiety, zainstalować skrypt i uruchom skrypt w każdym węźle puli.

Poniżej przedstawiono przykładowy szablon, który tworzy puli maszyn wirtualnych systemu Linux z ffmpeg zainstalowana i tylko wymaga ciąg Identyfikatora puli i liczbę maszyn wirtualnych mają być dostarczone do użycia:

```json
{
    "parameters": {
        "nodeCount": {
            "type": "int",
            "metadata": {
                "description": "The number of pool nodes"
            }
        },
        "poolId": {
            "type": "string",
            "metadata": {
                "description": "The pool ID "
            }
        }
    },
    "pool": {
        "type": "Microsoft.Batch/batchAccounts/pools",
        "apiVersion": "2016-12-01",
        "properties": {
            "id": "[parameters('poolId')]",
            "virtualMachineConfiguration": {
                "imageReference": {
                    "publisher": "Canonical",
                    "offer": "UbuntuServer",
                    "sku": "16.04.0-LTS",
                    "version": "latest"
                },
                "nodeAgentSKUId": "batch.node.ubuntu 16.04"
            },
            "vmSize": "STANDARD_D3_V2",
            "targetDedicatedNodes": "[parameters('nodeCount')]",
            "enableAutoScale": false,
            "maxTasksPerNode": 1,
            "packageReferences": [
                {
                    "type": "aptPackage",
                    "id": "ffmpeg"
                }
            ]
        }
    }
}
```

Jeśli plik szablonu został nazwany _ffmpeg.json puli_, a następnie szablon będzie można wywołać w następujący sposób:

```azurecli
az batch pool create --template pool-ffmpeg.json
```

### <a name="job-templates"></a>Szablony zadań

Oprócz możliwości standardowego szablonu parametry i zmienne następujące konstrukcji wyższego poziomu są obsługiwane przez szablon zadania:

-   **Fabryki zadań**

    -   Tworzy wiele zadań dla zadania na podstawie definicji zadania. Trzy typy fabryki zadań są obsługiwane — parametrycznych odchylenia zadań dla każdego pliku i zadań w kolekcji.

Poniżej przedstawiono przykładowy szablon, który tworzy zadanie, które używa ffmpeg w plikach wideo MP4 transkodowanie do jednej z dwóch mniejszej rozdzielczości, z jednego zadania utworzone na źródłowy plik wideo:

```json
{
    "parameters": {
        "poolId": {
            "type": "string",
            "metadata": {
                "description": "The name of Azure Batch pool which runs the job"
            }
        },
        "jobId": {
            "type": "string",
            "metadata": {
                "description": "The name of Azure Batch job"
            }
        },
        "resolution": {
            "type": "string",
            "defaultValue": "428x240",
            "allowedValues": [
                "428x240",
                "854x480"
            ],
            "metadata": {
                "description": "Target video resolution"
            }
        }
    },
    "job": {
        "type": "Microsoft.Batch/batchAccounts/jobs",
        "apiVersion": "2016-12-01",
        "properties": {
            "id": "[parameters('jobId')]",
            "constraints": {
                "maxWallClockTime": "PT5H",
                "maxTaskRetryCount": 1
            },
            "poolInfo": {
                "poolId": "[parameters('poolId')]"
            },
            "taskFactory": {
                "type": "taskPerFile",
                "source": { 
                    "fileGroup": "ffmpeg-input"
                },
                "repeatTask": {
                    "commandLine": "ffmpeg -i {fileName} -y -s [parameters('resolution')] -strict -2 {fileNameWithoutExtension}_[parameters('resolution')].mp4",
                    "resourceFiles": [
                        {
                            "blobSource": "{url}",
                            "filePath": "{fileName}"
                        }
                    ],
                    "outputFiles": [
                        {
                            "filePattern": "{fileNameWithoutExtension}_[parameters('resolution')].mp4",
                            "destination": {
                                "autoStorage": {
                                    "path": "{fileNameWithoutExtension}_[parameters('resolution')].mp4",
                                    "fileGroup": "ffmpeg-output"
                                }
                            },
                            "uploadOptions": {
                                "uploadCondition": "TaskSuccess"
                            }
                        }
                    ]
                }
            },
            "onAllTasksComplete": "terminatejob"
        }
    }
}
```

Jeśli plik szablonu został nazwany _ffmpeg.json zadania_, a następnie szablon będzie można wywołać w następujący sposób:

```azurecli
az batch job create --template job-ffmpeg.json
```

## <a name="file-groups-and-file-transfer"></a>Grup plików i transferu plików

Większość zadań i zadań wymagane pliki wejściowe i utworzyć pliki danych wyjściowych. Oba pliki wejściowe i pliki wyjściowe zwykle mają zostać przeniesione z klienta do węzła lub z węzła do klienta. Rozszerzenie interfejsu wiersza polecenia Azure partii abstracts transfer plików zadań i korzysta z konta magazynu, które jest tworzone domyślnie dla każdego konta usługi partia zadań.

Grupa plików jest równa kontener, który jest tworzony na koncie magazynu Azure. Grupa plików może mieć podfoldery.

Rozszerzenia interfejsu wiersza polecenia partii zawiera polecenia służące do przekazywania plików z klienta do grupy określonego pliku i pobieranie plików z określonej grupie plików do klienta.

```azurecli
az batch file upload --local-path c:\source_videos\*.mp4 
    --file-group ffmpeg-input

az batch file download --file-group ffmpeg-output --local-path
    c:\output_lowres_videos
```

Szablony puli i zadania umożliwiają plików przechowywanych w grup plików może być określony dla kopiowania na węzłach puli lub wyłącz węzłów puli do grupy plików. Na przykład w szablonie zadania z określonym wcześniej pliku grupy "ffmpeg — dane wejściowe" jest określona dla fabryki zadań jako lokalizacja źródłowych plików wideo kopiowane na węzeł transkodowanie; Plik grupy "ffmpeg-output" jest używana jako lokalizacji, w którym przekodowane pliki wyjściowe są kopiowane do od węzła, w którym każdego zadania.

## <a name="summary"></a>Podsumowanie

Obsługa transfer szablon i plik obecnie zostały dodane tylko do wiersza polecenia platformy Azure. Celem jest, aby rozwinąć odbiorców, którzy mogą być partii użytkowników, którzy nie chcą Opracuj kodu za pomocą interfejsów API partii, takie jak pracowników naukowo-badawczych użytkowników IT i tak dalej. Bez kodowania, użytkownicy mający wiedzę na temat usługi Azure, partii i aplikacje do uruchamiania przez partię można utworzyć szablony do tworzenia puli i zadania. Parametry szablonu użytkowników bez szczegółowej znajomości partii i aplikacje mogą za pomocą szablonów.

Wypróbowanie rozszerzenia partii dla wiersza polecenia platformy Azure i uzyskaliśmy wszelkie opinie i sugestie, albo w komentarzach tego artykułu lub za pośrednictwem [forum usługi partia zadań Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=azurebatch).

## <a name="next-steps"></a>Kolejne kroki

- Zobacz w partii szablony wpisie w blogu: [zadania uruchomione partii zadań Azure za pomocą wiersza polecenia platformy Azure — kod nie jest wymagany](https://azure.microsoft.com/en-us/blog/running-azure-batch-jobs-using-the-azure-cli-no-code-required/).
- Szczegółowa dokumentacja instalacji i użycia, przykłady i kodu źródłowego są dostępne w [repozytorium Azure GitHub](https://github.com/Azure/azure-batch-cli-extensions).
