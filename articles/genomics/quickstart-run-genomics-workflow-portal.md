---
title: 'Szybki start: uruchamianie przepływu pracy — Microsoft Genomics'
description: Ten poradnik Szybki start opisuje sposób ładowania danych wejściowych do usługi Azure Blob Storage oraz uruchamiania przepływu pracy za pośrednictwem usługi Microsoft Genomics.
services: genomics
author: grhuynh
manager: cgronlun
ms.author: grhuynh
ms.service: genomics
ms.topic: quickstart
ms.date: 01/11/2019
ms.openlocfilehash: 05b94ca9bd14392bad5288882a80f5c75590ef7b
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "76931796"
---
# <a name="quickstart-run-a-workflow-through-the-microsoft-genomics-service"></a>Szybki start: Uruchamianie przepływu za pośrednictwem usługi Microsoft Genomics

W tym przewodniku Szybki start należy przekazać dane wejściowe do konta magazynu obiektów Blob platformy Azure i uruchomić przepływ pracy za pośrednictwem usługi Microsoft Genomics przy użyciu klienta Python Genomics. Microsoft Genomics to skalowalna, bezpieczna usługa umożliwiająca dodatkową analizę zapewniającą szybkie przetwarzanie genomów, począwszy od pierwotnych operacji odczytywania i tworzenia dopasowanych operacji odczytywania i wywołań odmian. 

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto za darmo](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). 
- [Python 2.7.12+](https://www.python.org/downloads/release/python-2714/) `pip` , z `python` zainstalowanym i w ścieżce systemowej. Klient Microsoft Genomics nie jest zgodny z Pythonem 3. 

## <a name="set-up-create-a-microsoft-genomics-account-in-the-azure-portal"></a>Konfiguracja: tworzenie konta usługi Microsoft Genomics w witrynie Azure Portal

Aby utworzyć konto Microsoft Genomics, przejdź do [pozycji Utwórz konto genomiki](https://portal.azure.com/#create/Microsoft.Genomics) w witrynie Azure portal. Jeśli nie masz jeszcze subskrypcji platformy Azure, utwórz ją przed utworzeniem konta usługi Microsoft Genomics. 

![Microsoft Genomics w witrynie Azure portal](./media/quickstart-run-genomics-workflow-portal/genomics-create-blade.png "Microsoft Genomics w witrynie Azure portal")

Skonfiguruj konto usługi Genomics w sposób pokazany na wcześniejszej ilustracji, używając następujących informacji. 

 |**Ustawienie**          |  **Sugerowana wartość**  | **Opis pola** |
 |:-------------       |:-------------         |:----------            |
 |Subskrypcja         | Nazwa subskrypcji użytkownika|Jest to jednostka rozliczeniowa usług platformy Azure — aby uzyskać szczegółowe informacje o subskrypcji, zobacz [Subskrypcje](https://account.azure.com/Subscriptions) |      
 |Grupa zasobów       | MyResourceGroup       |  Grupy zasobów umożliwiają grupowanie wielu zasobów platformy Azure (konto magazynu, konto usługi Genomics itp.) w jednej grupie, co upraszcza zarządzanie. Aby uzyskać więcej informacji, zobacz [Grupy zasobów](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups). Prawidłowe nazwy grup zasobów opisano w artykule [Reguły nazewnictwa](/azure/architecture/best-practices/resource-naming) |
 |Nazwa konta         | MyGenomicsAccount     |Wybierz unikatowy identyfikator konta. Aby uzyskać informacje o prawidłowych nazwach, zobacz [Reguły nazewnictwa](/azure/architecture/best-practices/resource-naming) |
 |Lokalizacja                   | Zachodnie stany USA 2                    |    Usługa jest dostępna w regionach: Zachodnie stany USA 2, Europa Zachodnia i Azja Południowo-Wschodnia |

Aby monitorować proces wdrażania, można wybrać **opcję Powiadomienia** na górnym pasku menu.

![Powiadomienia](./media/quickstart-run-genomics-workflow-portal/genomics-notifications-box1.png "Powiadomienia")

Aby uzyskać więcej informacji na temat microsoft genomiki, zobacz [Co to jest Microsoft Genomics?](overview-what-is-genomics.md)

## <a name="set-up-install-the-microsoft-genomics-python-client"></a>Konfiguracja: instalacja klienta Microsoft Genomics Python

Musisz zainstalować zarówno Python, jak i klienta Microsoft Genomics Python w środowisku lokalnym. 

### <a name="install-python"></a>Instalacja języka Python

Klient Microsoft Genomics Python jest zgodny z python 2.7.12 lub nowszą wersją 2.7.xx. 2.7.14 jest sugerowaną wersją. Pliki do pobrania możesz znaleźć [tutaj](https://www.python.org/downloads/release/python-2714/). 

> [!IMPORTANT]
> Język Python 3.x nie jest zgodny z językiem Python 2.7.xx.  MSGen to aplikacja napisana w języku Python 2.7. Przy uruchamianiu aplikacji MSGen upewnij się, że w aktywnym środowisku jest używana wersja 2.7.xx języka Python. W przypadku używania aplikacji MSGen w środowisku języka Python w wersji 3.x mogą występować błędy.

### <a name="install-the-microsoft-genomics-client"></a>Instalacja klienta usługi Microsoft Genomics

Użyj `pip` języka Python, aby zainstalować `msgen`klienta Microsoft Genomics . W następujących instrukcjach założono, że język Python jest już zainstalowany w ścieżce systemowej. Jeśli masz problemy `pip` z instalacją nie rozpoznaną, musisz dodać Pythona i podfolder skryptów do ścieżki systemowej.

```
pip install --upgrade --no-deps msgen
pip install msgen
```

Jeśli nie chcesz instalować `msgen` jako pakietów binarnych całego systemu i `–-user` modyfikować pakietów Pythona dla całego systemu, użyj flagi z . `pip`
Jeśli używasz instalacji opartej na pakiecie lub pliku setup.py, zostaną zainstalowane wszystkie wymagane pakiety. W przeciwnym razie podstawowe `msgen` wymagane pakiety są 

 * [Azure-storage](https://pypi.python.org/pypi/azure-storage) 
 * [Requests](https://pypi.python.org/pypi/requests) 

Możesz zainstalować te pakiety przy użyciu modułu `pip`, procedury `easy_install` lub za pośrednictwem standardowych procedur `setup.py`. 

### <a name="test-the-microsoft-genomics-client"></a>Testowanie klienta usługi Microsoft Genomics
Aby przetestować klienta Microsoft Genomics, pobierz plik konfiguracyjny ze swojego konta Genomics. W witrynie Azure portal przejdź do konta Genomics, wybierając **pozycję Wszystkie usługi** w lewym górnym rogu, a następnie wyszukując i wybierając konta genomiki.

![Znajdź microsoft genomics w witrynie Azure portal](./media/quickstart-run-genomics-workflow-portal/genomics-filter-box.png "Znajdź microsoft genomics w witrynie Azure portal")

Wybierz konto Genomics, które właśnie stworzyłeś, przejdź do **klawiszy dostępu**i pobierz plik konfiguracyjny.

![Pobierz plik konfiguracyjny z microsoft genomics](./media/quickstart-run-genomics-workflow-portal/genomics-mygenomicsaccount-box.png "Pobierz plik konfiguracyjny z microsoft genomics")

Przetestuj działanie klienta Microsoft Genomics Python przy użyciu następującego polecenia:

```Python
msgen list -f "<full path where you saved the config file>"
```

## <a name="create-a-microsoft-azure-storage-account"></a>Tworzenie konta usługi Microsoft Azure Storage 
Usługa Microsoft Genomics oczekuje przechowywania danych wejściowych w formie blokowych obiektów blob na koncie magazynu platformy Azure. Usługa również zapisuje pliki wyjściowe jako blokowe obiekty blob w kontenerze określonym przez użytkownika na koncie magazynu platformy Azure. Pliki wejściowe i wyjściowe mogą znajdować się w różnych kontach magazynu.
Jeśli masz już dane na koncie magazynu platformy Azure, musisz tylko upewnić się, że znajdują się w tej samej lokalizacji co konto usługi Genomics. W przeciwnym razie opłaty za wyjście są naliczane podczas uruchamiania usługi Microsoft Genomics. Jeśli nie masz jeszcze konta magazynu platformy Azure, musisz je utworzyć i przekazać dane. Więcej informacji na temat kont usługi Azure storage można znaleźć [tutaj,](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account)w tym, jakie jest konto magazynu i jakie usługi zapewnia. Aby utworzyć konto magazynu platformy Azure, przejdź do [pozycji Utwórz konto magazynu](https://portal.azure.com/#create/Microsoft.StorageAccount-ARM) w witrynie Azure Portal.  

![Strona tworzenia konta magazynu](./media/quickstart-run-genomics-workflow-portal/genomics-storage-create-blade1.png "Strona tworzenia konta magazynu")

Skonfiguruj konto magazynu z następującymi informacjami, jak pokazano na poprzednim obrazie. Użyj większości standardowych opcji dla konta magazynu, określając tylko, że konto jest BlobStorage, a nie ogólnego przeznaczenia. Magazyn obiektów blob może być 2–5 razy szybszy w przypadku pobierania i przekazywania.  Zaleca się domyślny model wdrażania usługi Azure Resource Manager.  

 |**Ustawienie**          |  **Sugerowana wartość**  | **Opis pola** |
 |:-------------------------       |:-------------         |:----------            |
 |Subskrypcja         | Twoja subskrypcja platformy Azure |Aby uzyskać szczegółowe informacje o subskrypcji, zobacz [Subskrypcje](https://account.azure.com/Subscriptions) |      
 |Grupa zasobów       | MyResourceGroup       |  Możesz wybrać tę samą grupę zasobów co konto Genomics. Aby uzyskać prawidłowe nazwy grup zasobów, zobacz [Reguły nazewnictwa](/azure/architecture/best-practices/resource-naming) |
 |Nazwa konta magazynu         | MyStorageAccount     |Wybierz unikatowy identyfikator konta. W przypadku prawidłowych nazw zobacz [Reguły nazewnictwa](/azure/architecture/best-practices/resource-naming) |
 |Lokalizacja                  | Zachodnie stany USA 2                  | Użyj tej samej lokalizacji co lokalizacja konta Genomics, aby zmniejszyć opłaty za wyjście i zmniejszyć opóźnienia.  | 
 |Wydajność                  | Standardowa                   | Wartość domyślna to Standardowa. Aby uzyskać więcej informacji na temat kont magazynu standardowego i premium, zobacz [Wprowadzenie do magazynu platformy Microsoft Azure](https://docs.microsoft.com/azure/storage/common/storage-introduction)    |
 |Rodzaj konta       | BlobStorage (BlobStorage)       |  Magazyn obiektów blob może być 2–5 razy szybszy od konta ogólnego przeznaczenia w przypadku pobierania i przekazywania. |
 |Replikacja                  | Magazyn lokalnie nadmiarowy                  | Magazyn lokalnie nadmiarowy replikuje dane w centrum danych w regionie, w którym utworzono konto magazynu. Aby uzyskać więcej informacji, zobacz [Replikacja usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-redundancy)    |
 |Warstwa dostępu                  | Gorąca                   | Gorąca warstwa dostępu oznacza, że dostęp do obiektów na koncie magazynu będzie uzyskiwany częściej.    |

Następnie wybierz **pozycję Przejrzyj + utwórz,** aby utworzyć konto magazynu. Podobnie jak w razie tworzenia konta Genomics, możesz wybrać **powiadomienia** na górnym pasku menu, aby monitorować proces wdrażania. 

## <a name="upload-input-data-to-your-storage-account"></a>Przekazywanie danych wejściowych do konta magazynu

Usługa Microsoft Genomics oczekuje sparowanych odczytów końcowych (plików fastq lub bam) jako plików wejściowych. Możesz przekazać własne dane lub eksplorować publicznie dostępne dane przykładowe. Jeśli chcesz użyć publicznie dostępnych danych przykładowych, są one hostowane tutaj:

[https://msgensampledata.blob.core.windows.net/small/chr21_1.fq.gz](https://msgensampledata.blob.core.windows.net/small/chr21_1.fq.gz)
[https://msgensampledata.blob.core.windows.net/small/chr21_2.fq.gz](https://msgensampledata.blob.core.windows.net/small/chr21_2.fq.gz)

Na koncie magazynu musisz utworzyć jeden kontener obiektów blob na dane wejściowe oraz drugi kontener obiektów blob na dane wyjściowe.  Przekaż dane wejściowe do kontenera wejściowych obiektów blob. Można do tego użyć różnych narzędzi, w tym [Microsoft Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/), [BlobPorter](https://github.com/Azure/blobporter)lub [AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy?toc=%2fazure%2fstorage%2fblobs%2ftoc.json). 

## <a name="run-a-workflow-through-the-microsoft-genomics-service-using-the-python-client"></a>Uruchamianie przepływu za pośrednictwem usługi Microsoft Genomics przy użyciu klienta języka Python 

Aby uruchomić przepływ pracy za pośrednictwem usługi Microsoft Genomics, edytuj plik *config.txt,* aby określić kontener magazynu wejściowego i wyjściowego dla danych.
Otwórz plik *config.txt* pobrany z konta Genomics. Sekcje, które należy określić, to klucz subskrypcji i sześć elementów u dołu, nazwa konta magazynu, klucz i nazwa kontenera dla danych wejściowych i wyjściowych. Te informacje można znaleźć, przechodząc w witrynie Azure portal do **kluczy programu Access** dla konta magazynu lub bezpośrednio z Eksploratora usługi Azure Storage.  

![Konfiucja genomiki](./media/quickstart-run-genomics-workflow-portal/genomics-config.png "Konfiucja genomiki")

Jeśli chcesz uruchomić GATK4, ustaw `process_name` parametr `gatk4`na .

Domyślnie usługa Genomics generuje pliki VCF. Jeśli chcesz mieć wyjście gVCF, a nie wyjście `-emitRefConfidence` VCF (równoważne `emit-ref-confidence` w GATK 3.x `emit_ref_confidence` i GATK 4.x), dodaj `gvcf`parametr do *pliku config.txt* i ustaw go na , jak pokazano na poprzednim rysunku.  Aby zmienić dane wyjściowe vcf, usuń go z pliku *config.txt* lub ustaw parametr na `emit_ref_confidence` `none`. 

### <a name="submit-your-workflow-to-the-microsoft-genomics-service-the-microsoft-genomics-client"></a>Przesyłanie przepływu pracy do usługi Microsoft Genomics przy użyciu klienta usługi Microsoft Genomics

Użyj klienta Microsoft Genomics Python, aby przesłać przepływ pracy przy użyciu następującego polecenia:

```python
msgen submit -f [full path to your config file] -b1 [name of your first paired end read] -b2 [name of your second paired end read]
```

Możesz wyświetlić stan przepływów pracy przy użyciu następującego polecenia: 
```python
msgen list -f c:\temp\config.txt 
```

Po zakończeniu przepływu pracy można wyświetlić pliki wyjściowe na koncie magazynu platformy Azure w kontenerze wyjściowym, który został skonfigurowany. 

## <a name="next-steps"></a>Następne kroki
W tym artykule zostały przekazane przykładowe dane wejściowe do magazynu platformy Azure i `msgen` przesłane przepływu pracy do usługi Microsoft Genomics za pośrednictwem klienta języka Python. Aby dowiedzieć się więcej o innych typach plików wejściowych, które mogą być używane z usługą Microsoft Genomics, zobacz następujące strony: [sparowany FASTQ](quickstart-input-pair-FASTQ.md) | [BAM](quickstart-input-BAM.md) | [Multiple FASTQ lub BAM](quickstart-input-multiple.md). Możesz też zapoznać się z tym samouczkiem przy użyciu [samouczka dotyczącego notesu platformy Azure](https://aka.ms/genomicsnotebook).
