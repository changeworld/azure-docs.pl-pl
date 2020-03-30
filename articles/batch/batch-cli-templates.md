---
title: Uruchamianie zadań end-to-end przy użyciu szablonów — usługa Azure Batch
description: Za pomocą tylko poleceń interfejsu wiersza polecenia można utworzyć pulę, przekazać dane wejściowe, utworzyć zadania i skojarzone zadania oraz pobrać wynikowe dane wyjściowe.
services: batch
author: LauraBrenner
manager: evansma
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.workload: big-compute
ms.date: 12/07/2018
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: c7459c4dc700f034feafbf133b831a52b9233d11
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77020169"
---
# <a name="use-azure-batch-cli-templates-and-file-transfer"></a>Korzystanie z szablonów interfejsu wiersza polecenia usługi Azure Batch i transferu plików

Za pomocą rozszerzenia usługi Azure Batch do interfejsu wiersza polecenia platformy Azure, jest możliwe do uruchamiania zadań usługi Batch bez pisania kodu.

Tworzenie i używanie plików szablonów JSON za pomocą interfejsu wiersza polecenia platformy Azure do tworzenia puli, zadań i zadań usługi Batch. Polecenia rozszerzenia interfejsu wiersza polecenia cli można łatwo przesyłać pliki wejściowe zadania na konto magazynu skojarzone z kontem usługi Batch i pobierać pliki wyjściowe zadania.

## <a name="overview"></a>Omówienie

Rozszerzenie interfejsu wiersza polecenia platformy Azure umożliwia batch do użycia end-to-end przez użytkowników, którzy nie są deweloperami. Za pomocą tylko poleceń interfejsu wiersza polecenia można utworzyć pulę, przekazać dane wejściowe, utworzyć zadania i skojarzone zadania oraz pobrać wynikowe dane wyjściowe. Nie jest wymagany żaden dodatkowy kod. Uruchom polecenia interfejsu wiersza polecenia bezpośrednio lub zintegrować je ze skryptami.

Szablony wsadowe opierają się na [istniejącej obsłudze usługi Batch w plikuch](batch-cli-get-started.md#json-files-for-resource-creation) JSON platformy Azure dla plików JSON, aby określić wartości właściwości podczas tworzenia pul, zadań, zadań i innych elementów. Szablony wsadowe dodają następujące możliwości:

-   Można zdefiniować parametry. Gdy szablon jest używany, tylko wartości parametrów są określone do utworzenia elementu, z innymi wartościami właściwości elementu określonymi w treści szablonu. Użytkownik, który rozumie Batch i aplikacje, które mają być uruchamiane przez partię, może tworzyć szablony, określając wartości puli, zadania i właściwości zadania. Użytkownik mniej zaznajomiony z batch i/lub aplikacje musi tylko określić wartości dla zdefiniowanych parametrów.

-   Fabryki zadań zadań tworzą jedno lub więcej zadań skojarzonych z zadaniem, unikając konieczności tworzenia wielu definicji zadań i znacznie upraszczając przesyłanie zadań.


Zadania zazwyczaj używają wejściowych plików danych i generują pliki danych wyjściowych. Konto magazynu jest domyślnie skojarzone z każdym kontem usługi Batch. Przenieść pliki do i z tego konta magazynu przy użyciu interfejsu wiersza polecenia, bez kodowania i bez poświadczeń magazynu.

Na przykład [ffmpeg](https://ffmpeg.org/) jest popularną aplikacją, która przetwarza pliki audio i wideo. Poniżej przedstawiono kroki z interfejsu wiersza polecenia partii Azure do wywoływania ffmpeg do transkodowania źródłowych plików wideo do różnych rozdzielczości.

-   Utwórz szablon puli. Użytkownik tworzący szablon wie, jak wywołać aplikację ffmpeg i jej wymagania; określają odpowiedni system operacyjny, rozmiar maszyny Wirtualnej, sposób instalowania ffmpeg (z pakietu aplikacji lub przy użyciu menedżera pakietów, na przykład) i inne wartości właściwości puli. Parametry są tworzone, więc gdy szablon jest używany, tylko identyfikator puli i liczba maszyn wirtualnych muszą być określone.

-   Tworzenie szablonu zadania. Użytkownik tworzący szablon wie, jak ffmpeg musi być wywoływany do transkodowania źródłowego wideo do innej rozdzielczości i określa wiersz polecenia zadania; wiedzą również, że istnieje folder zawierający źródłowe pliki wideo, z zadaniem wymaganym dla pliku wejściowego.

-   Użytkownik końcowy z zestawem plików wideo do transkodowania najpierw tworzy pulę przy użyciu szablonu puli, określając tylko identyfikator puli i liczbę wymaganych maszyn wirtualnych. Następnie mogą przesłać pliki źródłowe do transkodowania. Zadanie można następnie przesłać za pomocą szablonu zadania, określając tylko identyfikator puli i lokalizację przekazanych plików źródłowych. Zostanie utworzone zadanie Batch z generuleczonym jednym zadaniem na plik wejściowy. Na koniec można pobrać transkodowane pliki wyjściowe.

## <a name="installation"></a>Instalacja

Aby zainstalować rozszerzenie interfejsu wiersza polecenia usługi Azure Batch, najpierw [zainstaluj narzędzie cli platformy Azure 2.0](/cli/azure/install-azure-cli)lub uruchom narzędzie cli platformy Azure w [usłudze Azure Cloud Shell](../cloud-shell/overview.md).

Zainstaluj najnowszą wersję rozszerzenia usługi Batch przy użyciu następującego polecenia interfejsu wiersza polecenia platformy Azure:

```azurecli
az extension add --name azure-batch-cli-extensions
```

Aby uzyskać więcej informacji na temat rozszerzenia interfejsu wiersza polecenia wsadowego i dodatkowych opcji instalacji, zobacz [repozytorium GitHub](https://github.com/Azure/azure-batch-cli-extensions).


Aby korzystać z funkcji rozszerzenia interfejsu wiersza polecenia, potrzebujesz konta usługi Azure Batch i, w przypadku poleceń przesyłanych pliki do i z magazynu, połączonego konta magazynu.

Aby zalogować się do konta usługi Batch za pomocą interfejsu wiersza polecenia platformy Azure, zobacz [Zarządzanie zasobami usługi Batch za pomocą interfejsu wiersza polecenia platformy Azure](batch-cli-get-started.md).

## <a name="templates"></a>Szablony

Szablony usługi Azure Batch są podobne do szablonów usługi Azure Resource Manager, w funkcjonalności i składni. Są to pliki JSON, które zawierają nazwy właściwości elementu i wartości, ale dodają następujące główne pojęcia:

-   **Parametry**

    -   Zezwalaj na określanie wartości właściwości w sekcji treści, a tylko wartości parametrów muszą być podane, gdy używany jest szablon. Na przykład pełna definicja puli może być umieszczona w treści i tylko jeden parametr zdefiniowany dla identyfikatora puli; tylko ciąg identyfikatora puli w związku z tym musi być dostarczony do utworzenia puli.
        
    -   Treść szablonu może być autorstwa osoby posiadającej wiedzę na temat usługi Batch i aplikacji uruchamianych przez partię; Tylko wartości dla parametrów zdefiniowanych przez autora muszą być podane, gdy używany jest szablon. Użytkownik bez szczegółowej wiedzy partii i/lub aplikacji można zatem użyć szablonów.

-   **Zmienne**

    -   Zezwalaj na określanie prostych lub złożonych wartości parametrów w jednym miejscu i użycie ich w jednym lub więcej miejscach w treści szablonu. Zmienne mogą uprościć i zmniejszyć rozmiar szablonu, a także uczynić go bardziej łatwe do utrzymania, mając jedną lokalizację, aby zmienić właściwości.

-   **Konstrukcje wyższego poziomu**

    -   Niektóre konstrukcje wyższego poziomu są dostępne w szablonie, które nie są jeszcze dostępne w interfejsach API partii. Na przykład fabrykę zadań można zdefiniować w szablonie zadania, który tworzy wiele zadań dla zadania, przy użyciu wspólnej definicji zadania. Konstrukcje te uniknąć konieczności kodu dynamicznie utworzyć wiele plików JSON, takich jak jeden plik na zadanie, a także tworzenie plików skryptów do instalowania aplikacji za pośrednictwem menedżera pakietów.

    -   W pewnym momencie te konstrukcje mogą być dodawane do usługi Batch i dostępne w interfejsach API partii, interfejsów użytkownika itp.

### <a name="pool-templates"></a>Szablony puli

Szablony puli obsługują standardowe możliwości szablonu parametrów i zmiennych. Obsługują one również następujące konstrukcji wyższego poziomu:

-   **Odwołania do pakietu**

    -   Opcjonalnie umożliwia kopiowanie oprogramowania do węzłów puli przy użyciu menedżerów pakietów. Menedżer pakietów i identyfikator pakietu są określone. Deklarując jeden lub więcej pakietów, można uniknąć tworzenia skryptu, który pobiera wymagane pakiety, instalowanie skryptu i uruchamianie skryptu w każdym węźle puli.

Poniżej przedstawiono przykład szablonu, który tworzy pulę maszyn wirtualnych z systemem Linux z zainstalowanym ffmpeg. Aby go użyć, podaj tylko ciąg identyfikatora puli i liczbę maszyn wirtualnych w puli:

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

Jeśli plik szablonu został nazwany _pool-ffmpeg.json,_ wywołaj szablon w następujący sposób:

```azurecli
az batch pool create --template pool-ffmpeg.json
```

Cli monituje o podanie wartości `poolId` `nodeCount` i parametrów. Można również podać parametry w pliku JSON. Przykład:

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

Jeśli parametry pliku JSON został nazwany *pool-parameters.json*, a następnie wywołać szablon w następujący sposób:

```azurecli
az batch pool create --template pool-ffmpeg.json --parameters pool-parameters.json
```

### <a name="job-templates"></a>Szablony zadań

Szablony zadań obsługują standardowe możliwości szablonu parametrów i zmiennych. Obsługują one również następujące konstrukcji wyższego poziomu:

-   **Fabryka zadań**

    -   Tworzy wiele zadań dla zadania z jednej definicji zadania. Obsługiwane są trzy typy fabryki zadań — parametryczne wyciągnięcie po ścieżce, zadanie na plik i zbieranie zadań.

Poniżej przedstawiono przykład szablonu, który tworzy zadanie do transkodowania plików wideo MP4 z ffmpeg do jednej z dwóch niższych rozdzielczości. Tworzy jedno zadanie na źródłowy plik wideo. Zobacz [Grupy plików i transfer plików,](#file-groups-and-file-transfer) aby uzyskać więcej informacji o grupach plików dla wprowadzania i danych wyjściowych zadań.

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

Jeśli plik szablonu został nazwany _job-ffmpeg.json,_ wywołaj szablon w następujący sposób:

```azurecli
az batch job create --template job-ffmpeg.json
```

Tak jak poprzednio, wiersz polecenia monituje o podanie wartości parametrów. Można również podać parametry w pliku JSON.

### <a name="use-templates-in-batch-explorer"></a>Używanie szablonów w Eksploratorze wsadowym

Szablon interfejsu wiersza polecenia partii można przekazać do aplikacji klasycznej [Eksploratora partii](https://github.com/Azure/BatchExplorer) (dawniej nazywane BatchLabs), aby utworzyć pulę lub zadanie batch. Można również wybrać z wstępnie zdefiniowanych szablonów puli i zadań w Galerii Eksploratora wsadowego.

Aby przesłać szablon:

1. W Eksploratorze wsadowym wybierz pozycję **Szablony** > **lokalne galerii**.

2. Wybierz lub przeciągnij i upuść, lokalną pulę lub szablon zadania.

3. Wybierz **pozycję Użyj tego szablonu**i postępuj zgodnie z instrukcjami wyświetlanymi na ekranie.

## <a name="file-groups-and-file-transfer"></a>Grupy plików i przesyłanie plików

Większość zadań i zadań wymaga plików wejściowych i tworzenia plików wyjściowych. Zazwyczaj pliki wejściowe i wyjściowe są przesyłane z klienta do węzła lub z węzła do klienta. Rozszerzenie interfejsu wiersza polecenia usługi Azure Batch wyodrębnia transfer plików i wykorzystuje konto magazynu, które można skojarzyć z każdym kontem usługi Batch.

Grupa plików jest równa kontenerowi utworzonego na koncie magazynu platformy Azure. Grupa plików może mieć podfoldery.

Rozszerzenie Batch CLI udostępnia polecenia przekazywania plików z klienta do określonej grupy plików i pobierania plików z określonej grupy plików do klienta.

```azurecli
az batch file upload --local-path c:\source_videos\*.mp4 
    --file-group ffmpeg-input

az batch file download --file-group ffmpeg-output --local-path
    c:\output_lowres_videos
```

Szablony puli i zadań umożliwiają określanie plików przechowywanych w grupach plików do kopiowania do węzłów puli lub poza węzłami puli z powrotem do grupy plików. Na przykład w szablonie zadania określonym wcześniej, grupa plików *ffmpeg-input* jest określona dla fabryki zadań jako lokalizacja źródłowych plików wideo skopiowanych do węzła w celu transkodowania. Grupa plików *ffmpeg-output* jest lokalizacją, w której transkodowane pliki wyjściowe są kopiowane z węzła z każdym zadaniem.

## <a name="summary"></a>Podsumowanie

Obsługa transferu szablonów i plików została obecnie dodana tylko do interfejsu wiersza polecenia platformy Azure. Celem jest rozszerzenie grupy odbiorców, które mogą używać usługi Batch do użytkowników, którzy nie muszą tworzyć kodu przy użyciu interfejsów API partii, takich jak badacze i użytkownicy IT. Bez kodowania użytkownicy posiadający wiedzę na temat platformy Azure, partii i aplikacji uruchamianych przez usługę Batch mogą tworzyć szablony dla puli i tworzenia zadań. Dzięki parametrom szablonu użytkownicy bez szczegółowej wiedzy na temat usługi Batch i aplikacji mogą korzystać z szablonów.

Wypróbuj rozszerzenie usługi Batch dla interfejsu wiersza polecenia platformy Azure i przekaż nam wszelkie opinie lub sugestie w komentarzach do tego artykułu lub za pośrednictwem [repozytorium społeczności wsadowej.](https://github.com/Azure/Batch)

## <a name="next-steps"></a>Następne kroki

- Szczegółowa dokumentacja instalacji i użycia, przykłady i kod źródłowy są dostępne w [repozytorium Usługi Azure GitHub.](https://github.com/Azure/azure-batch-cli-extensions)

- Dowiedz się więcej o tworzeniu zasobów usługi Batch i zarządzaniu nimi za pomocą [Eksploratora wsadowego.](https://github.com/Azure/BatchExplorer)
