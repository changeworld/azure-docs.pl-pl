---
title: Uruchamianie zadań przy użyciu szablonów — Azure Batch end-to-end | Dokumentacja firmy Microsoft
description: Tworzenie pul, zadań i zadania usługi Batch za pomocą plików szablonów i interfejsu wiersza polecenia platformy Azure.
services: batch
author: laurenhughes
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: na
ms.topic: article
ms.workload: big-compute
ms.date: 12/07/2018
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: 80d2e995a18a2d6dafbb8d92fdd5996b10eab17c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60783742"
---
# <a name="use-azure-batch-cli-templates-and-file-transfer"></a>Użyj szablonów interfejsu wiersza polecenia usługi Azure Batch i transferu plików

Za pomocą rozszerzenia wiersza polecenia platformy Azure dla usługi Azure Batch, jest możliwe uruchamianie zadań wsadowych bez konieczności pisania kodu.

Tworzenie i używanie plików szablonów JSON przy użyciu wiersza polecenia platformy Azure, do tworzenia pul, zadań i zadania usługi Batch. Używanie interfejsu wiersza polecenia rozszerzenia poleceń, aby łatwo przekazywać plików wejściowych zadania do konta magazynu skojarzone z kontem usługi Batch i zadanie pobierania plików wyjściowych.

## <a name="overview"></a>Omówienie

Rozszerzenie interfejsu wiersza polecenia platformy Azure umożliwia usługi Batch jako używane end-to-end osoby niebędące deweloperami. Za pomocą tylko polecenia interfejsu wiersza polecenia można utworzyć pulę, przekazywanie danych wejściowych, tworzenie zadań i skojarzonych zadań i pobrać wynikowe dane wyjściowe. Żaden dodatkowy kod nie jest wymagana. Do uruchamiania poleceń interfejsu wiersza polecenia, bezpośrednio lub zintegrować skryptów.

Tworzenie szablonów usługi Batch, na [istniejących obsługę usługi Batch w interfejsie wiersza polecenia platformy Azure](batch-cli-get-started.md#json-files-for-resource-creation) dla plików JSON do określenia wartości właściwości, podczas tworzenia pul, zadań, zadań i innych elementów. Szablonów usługi Batch, Dodaj następujące funkcje:

-   Parametry można definiować. Gdy szablon jest używany, tylko wartości parametrów są określone można utworzyć elementu, za pomocą wartości innych właściwości elementu określona w treści szablonu. Użytkownik, który rozumie wsadowe i aplikacje do uruchamiania przy użyciu usługi Batch można tworzyć szablony, określając z puli, zadań i wartości właściwości zadania. Użytkownik mniej znanych z usługi Batch i/lub aplikacji wystarczy tylko określić wartości dla zdefiniowanych parametrów.

-   Fabryki zadań zadania Utwórz co najmniej jedno zadanie skojarzone z zadaniem, unikając konieczności wiele definicji zadań ma zostać utworzony i znacznie uproszczenia przesłania zadania.


Zadania zazwyczaj użyć plików danych wejściowych i tworzące plików danych wyjściowych. Konto magazynu skojarzone jest domyślnie, każde konto usługi Batch. Transfer plików do i z tego konta magazynu przy użyciu interfejsu wiersza polecenia, bez kodowania i żadne poświadczenia magazynu.

Na przykład [ffmpeg](https://ffmpeg.org/) jest popularnych aplikacji, która przetwarza plików audio i wideo. Poniżej przedstawiono kroki przy użyciu interfejsu wiersza polecenia usługi Batch Azure do wywołania, narzędzie ffmpeg w celu przekodowanie źródłowych plików wideo do różnych rozdzielczościach.

-   Utwórz szablon puli. Użytkownik tworzący szablonu wie, jak wywołanie aplikacji narzędzia ffmpeg i jego wymagania; określają odpowiedni system operacyjny, maszyna wirtualna rozmiar, jak narzędzie ffmpeg jest zainstalowana (od pakietu aplikacji lub przy użyciu Menedżera pakietów, na przykład) i innych wartości właściwości puli. Parametry są tworzone, gdy szablon jest używany, tylko identyfikator puli i liczby maszyn wirtualnych muszą być określone.

-   Utworzyć szablon zadania. Użytkownik tworzący szablonu wie, jak narzędzie ffmpeg musi być wywoływane ze źródłem transkodowanie wideo do innego rozwiązania i określa wiersz polecenia zadania; one także wiedzieć, że istnieje folder zawierający pliki wideo źródła z zadaniem wymaganych do obsługi każdego pliku wejściowego.

-   Użytkownik końcowy z zestawem pliki wideo do transkodowania najpierw tworzy pulę przy użyciu szablonu puli określanie tylko identyfikator puli i liczby maszyn wirtualnych wymagana. Transkodowanie mogą następnie przekazać pliki źródłowe. Następnie można przesłać zadania przy użyciu szablonu zadania, określając tylko identyfikator puli i lokalizację plików źródłowych, przekazany. Tworzone jest zadanie usługi Batch za pomocą jednego zadania na wygenerowanie pliku wejściowego. Na koniec można pobrać pliki wyjściowe transkodowane.

## <a name="installation"></a>Instalacja

Rozszerzenie interfejsu wiersza polecenia usługi Azure Batch, należy najpierw zainstalować [zainstalować interfejs wiersza polecenia platformy Azure w wersji 2.0](/cli/azure/install-azure-cli), lub uruchomić z wiersza polecenia platformy Azure w [usługi Azure Cloud Shell](../cloud-shell/overview.md).

Zainstaluj najnowszą wersję rozszerzenia usługi Batch za pomocą następującego polecenia wiersza polecenia platformy Azure:

```azurecli
az extension add --name azure-batch-cli-extensions
```

Aby uzyskać więcej informacji na temat rozszerzenie interfejsu wiersza polecenia usługi Batch i dodatkowe opcje instalacji, zobacz [repozytorium GitHub](https://github.com/Azure/azure-batch-cli-extensions).


Korzystanie z funkcji rozszerzenia interfejsu wiersza polecenia jest potrzebne konto usługi Azure Batch i poleceń, które transferu plików do i z magazynu, połączone konto magazynu.

Aby zalogować się do konta usługi Batch przy użyciu wiersza polecenia platformy Azure, zobacz [zarządzanie zasobami usługi Batch przy użyciu wiersza polecenia platformy Azure](batch-cli-get-started.md).

## <a name="templates"></a>Szablony

Szablony usługi Azure Batch są podobne do szablonów usługi Azure Resource Manager, w funkcji i składni. Są to pliki w formacie JSON, które zawiera element nazwy i wartości właściwości, ale Dodaj następujące główne pojęcia:

-   **Parametry**

    -   Zezwalaj na wartości właściwości musi być podana w części treści, tylko wartości parametrów konieczności dostarczane, gdy jest używany szablon. Na przykład kompletną definicję dla puli można można umieścić w treści i tylko jeden parametr zdefiniowane dla identyfikatora puli; w związku z tym tylko ciąg Identyfikatora puli musi być dostarczane do tworzenia puli.
        
    -   Treści szablonu mogą być edytowane przez osobę przy zachowaniu wiedzy o wsadowych i aplikacji do uruchomienia przy użyciu usługi Batch; wymagane jest podanie tylko wartości parametrów zdefiniowanych przez autora, gdy jest używany szablon. Użytkownik bez szczegółowe partii i/lub bazy wiedzy w związku z tym można używać szablonów.

-   **Zmienne**

    -   Zezwalaj na proste lub złożone parametry określone w jednym miejscu i używane w jeden lub więcej miejsc, w treści szablonu. Zmienne można uproszczenie i Zmniejsz rozmiar szablonu, a także wprowadzić będzie łatwiejszy w utrzymaniu, po jednym miejscu, aby zmienić właściwości.

-   **Konstrukcji wyższego poziomu**

    -   Niektóre konstrukcji wyższego poziomu są dostępne w szablonie, które nie są jeszcze dostępne w interfejsach API usługi Batch. Na przykład można zdefiniować w szablonie zadania, który tworzy wiele zadań dla zadania przy użyciu wspólnej definicji zadania fabryki zadań. Te konstrukcje uniknąć konieczności kod, aby dynamicznie utworzyć wiele plików JSON, takich jak jeden plik na zadania, a także tworzyć pliki skryptów do instalowania aplikacji za pomocą Menedżera pakietów.

    -   W pewnym momencie te konstrukcje mogą być dodawane do usługi Batch i dostępne w interfejsów API usługi Batch, UI itp.

### <a name="pool-templates"></a>Szablony puli

Szablony puli obsługują funkcje standardowego szablonu zmiennych i parametrów. Obsługują one również następujące konstrukcji wyższego poziomu:

-   **Odwołania do pakietu**

    -   Opcjonalnie umożliwia oprogramowania, które mają być kopiowane do węzłów w puli przy użyciu menedżerów pakietów. Określono Menedżera pakietów i Identyfikatora pakietu. DEKLARUJĄC co najmniej jeden pakiet, unikaj tworzenia skryptu, który pobiera wymagane pakiety, skrypt instalacji i uruchamiania skryptu w każdym węźle puli.

Oto przykładowy szablon, który tworzy pulę maszyn wirtualnych systemu Linux przy użyciu narzędzia ffmpeg zainstalowane. Aby go użyć, należy podać tylko ciąg Identyfikatora puli i liczby maszyn wirtualnych w puli:

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
                    "sku": "16.04-LTS",
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

Jeśli plik szablonu został nazwany _ffmpeg.json puli_, wywołaj szablonu w następujący sposób:

```azurecli
az batch pool create --template pool-ffmpeg.json
```

Interfejs wiersza polecenia wyświetli monit o podanie wartości dla `poolId` i `nodeCount` parametrów. Może też podawać parametrów w pliku JSON. Na przykład:

```json
{
  "poolId": {
    "value": "mypool"
  },
  "nodeCount": {
    "value": 2
  }
}
```

Jeśli plik JSON parametrów nosiła nazwę *puli parameters.json*, wywołaj szablonu w następujący sposób:

```azurecli
az batch pool create --template pool-ffmpeg.json --parameters pool-parameters.json
```

### <a name="job-templates"></a>Szablony zadań

Szablony zadań obsługują funkcje standardowego szablonu zmiennych i parametrów. Obsługują one również następujące konstrukcji wyższego poziomu:

-   **Fabryki zadań**

    -   Tworzy wiele zadań na podstawie definicji jedno zadanie. Fabryki zadań obsługiwane są trzy typy — parametric sweep zadania na plik i zadań zbierania.

Oto przykładowy szablon, który tworzy zadanie w plikach wideo MP4 transkodowanie przy użyciu narzędzia ffmpeg do jednej z dwóch stosowania niższych rozdzielczości. Tworzy jedno zadanie na źródłowego pliku wideo. Zobacz [plików grup i transfer plików](#file-groups-and-file-transfer) więcej informacji na temat grup plików dla zadania danych wejściowych i wyjściowych.

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

Jeśli plik szablonu został nazwany _ffmpeg.json zadania_, wywołaj szablonu w następujący sposób:

```azurecli
az batch job create --template job-ffmpeg.json
```

Jako przed, interfejsu wiersza polecenia wyświetli monit o podanie wartości parametrów. Może też podawać parametrów w pliku JSON.

### <a name="use-templates-in-batch-explorer"></a>Korzystanie z szablonów w Eksploratorze usługi Batch

Możesz przekazać szablon interfejsu wiersza polecenia usługi Batch, aby [programu Batch Explorer](https://github.com/Azure/BatchExplorer) aplikacji pulpitu (dawniej o nazwie BatchLabs) do tworzenia puli usługi Batch lub zadania. Możesz również wybrać spośród wstępnie zdefiniowanych szablonów puli i zadania w galerii usługi Batch Explorer.

Aby przekazać zawartość szablonu:

1. W Eksploratorze usługi Batch wybierz **galerii** > **lokalne szablony**.

2. Wybierz lub przeciągnij i upuść lokalna pula lub szablonów zadań.

3. Wybierz **za pomocą tego szablonu**i postępuj zgodnie z monitami wyświetlanymi na ekranie.

## <a name="file-groups-and-file-transfer"></a>Grupy plików i transferu plików

Większość zadań i podzadań wymagane pliki wejściowe i generuje pliki wyjściowe. Zazwyczaj pliki wejściowe i wyjściowe pliki są przesyłane, od klienta do węzła lub z węzła do klienta. Rozszerzenie interfejsu wiersza polecenia usługi Azure Batch przenosi transfer plików away i korzysta z konta magazynu, które można skojarzyć z każdego konta usługi Batch.

Grupa plików jest równa kontenera, który jest tworzony w ramach konta usługi Azure storage. Grupa plików może mieć podfoldery.

Rozszerzenie interfejsu wiersza polecenia usługi Batch udostępnia polecenia w celu przekazywania plików z klienta do określonej grupy i pobieranie plików z określonej grupie plików do klienta.

```azurecli
az batch file upload --local-path c:\source_videos\*.mp4 
    --file-group ffmpeg-input

az batch file download --file-group ffmpeg-output --local-path
    c:\output_lowres_videos
```

Szablony puli i zadania umożliwiają plików przechowywanych w grup plików, aby określić związanym z kopiowaniem na węzłach puli lub wyłącz węzły puli do grupy plików. Na przykład w ramach zadania szablon określony wcześniej grupy plików *dane wejściowe ffmpeg* jest określona dla fabryki zadań jako lokalizacja źródłowych plików wideo, kopiowane do węzła w celu przetranskodowania jej. Grupa plików *dane wyjściowe narzędzia ffmpeg* to lokalizacja, w których pliki wyjściowe transkodowane są kopiowane z węzła, w którym każde zadanie podrzędne.

## <a name="summary"></a>Podsumowanie

Obecnie dodano obsługę transferu szablonu i pliku tylko do wiersza polecenia platformy Azure. Celem jest, aby rozwinąć odbiorców, którzy można używać usługi Batch dla użytkowników, którzy nie ma potrzeby tworzenia kodu za pomocą interfejsów API usługi Batch, np. pracowników naukowo-badawczych i użytkowników IT. Bez kodowania, użytkownicy z wiedzy na temat platformy Azure, wsadowe i aplikacje do uruchamiania przy użyciu usługi Batch mogą tworzyć szablony do tworzenia puli i zadania. Za pomocą parametrów szablonu użytkownicy bez szczegółowej znajomości wsadowe i aplikacje mogą używać szablonów.

Wypróbuj rozszerzenie usługi partia zadań wiersza polecenia platformy Azure i przekaż nam wszelkie opinie i sugestie, albo w komentarzach, w tym artykule lub za pośrednictwem [repozytorium społeczności usługi Batch](https://github.com/Azure/Batch).

## <a name="next-steps"></a>Kolejne kroki

- Szczegółowa dokumentacja instalowaniu i używaniu, przykłady i kodu źródłowego są dostępne w [repozytorium Azure GitHub](https://github.com/Azure/azure-batch-cli-extensions).

- Dowiedz się więcej o korzystaniu z [programu Batch Explorer](https://github.com/Azure/BatchExplorer) do tworzenia i zarządzania zasobami usługi Batch.
