---
title: Kopiowanie aplikacji i danych do węzłów puli
description: Dowiedz się, jak kopiować aplikacje i dane do węzłów puli.
services: batch
author: LauraBrenner
manager: evansma
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 02/17/2020
ms.author: labrenne
ms.openlocfilehash: 226a0d69ac387142ecf580537e35f8754ac848a6
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2020
ms.locfileid: "80385586"
---
# <a name="copying-applications-and-data-to-pool-nodes"></a>Kopiowanie aplikacji i danych do węzłów puli

Usługa Azure Batch obsługuje kilka sposobów wprowadzania danych i aplikacji do węzłów obliczeniowych, dzięki czemu dane i aplikacje są dostępne do użycia przez zadania. Dane i aplikacje mogą być wymagane do uruchomienia całego zadania i dlatego muszą być zainstalowane w każdym węźle. Niektóre mogą być wymagane tylko dla określonego zadania lub muszą być zainstalowane dla zadania, ale nie muszą znajdować się w każdym węźle. Partia ma narzędzia dla każdego z tych scenariuszy.

- **Pliki zasobów uruchamiania puli:** Dla aplikacji lub danych, które muszą być zainstalowane w każdym węźle w puli. Ta metoda jest używana wraz z pakietem aplikacji lub kolekcją plików zasobów zadania startowego w celu wykonania polecenia instalacji.  

Przykłady: 
- Przenoszenie lub instalowanie aplikacji za pomocą wiersza polecenia zadania startowego

- Określ listę określonych plików lub kontenerów na koncie magazynu platformy Azure. Aby uzyskać więcej informacji, zobacz [dodawanie#resourcefile w dokumentacji REST](https://docs.microsoft.com/rest/api/batchservice/pool/add#resourcefile)

- Każde zadanie uruchamiane w puli uruchamia myapplication.exe, które musi najpierw zostać zainstalowane z myApplication.msi. Jeśli używasz tego mechanizmu, należy ustawić zadanie uruchamiania **czekać na sukces** właściwość **true**. Aby uzyskać więcej informacji, zobacz [add#starttask w dokumentacji REST](https://docs.microsoft.com/rest/api/batchservice/pool/add#starttask).

- **Odwołania do pakietu aplikacji** w puli: dla aplikacji lub danych, które muszą być zainstalowane w każdym węźle w puli. Nie ma polecenia instalacji skojarzonego z pakietem aplikacji, ale można użyć zadania startowego, aby uruchomić dowolne polecenie instalacji. Jeśli aplikacja nie wymaga instalacji lub składa się z dużej liczby plików, można użyć tej metody. Pakiety aplikacji są dobrze dostosowane do dużej liczby plików, ponieważ łączą dużą liczbę odniesień do plików w mały ładunek. Jeśli spróbujesz dołączyć więcej niż 100 oddzielnych plików zasobów do jednego zadania, usługa Batch może wystąpić z wewnętrznymi ograniczeniami systemowymi dla pojedynczego zadania. Ponadto należy użyć pakietów aplikacji, jeśli masz rygorystyczne wymagania dotyczące przechowywania wersji, gdzie może mieć wiele różnych wersji tej samej aplikacji i trzeba wybrać między nimi. Aby uzyskać więcej informacji, zobacz [Wdrażanie aplikacji do węzłów obliczeniowych za pomocą pakietów aplikacji usługi Batch](https://docs.microsoft.com/azure/batch/batch-application-packages).

- **Pliki zasobów zadań przygotowania zadania:** Dla aplikacji lub danych, które muszą być zainstalowane, aby zadanie zostało uruchomione, ale nie muszą być instalowane na całej puli. Na przykład: jeśli pula ma wiele różnych typów zadań i tylko jeden typ zadania wymaga myapplication.msi do uruchomienia, warto umieścić krok instalacji w zadaniu przygotowania zadania. Aby uzyskać więcej informacji na temat zadań przygotowania zadania, zobacz [Uruchamianie zadań przygotowania zadania i zadań zwalniania zadań w węzłach obliczeniowych wsadowych](https://azure.microsoft.com/documentation/articles/batch-job-prep-release/).

- **Pliki zasobów zadań**: Gdy aplikacja lub dane są istotne tylko dla pojedynczego zadania. Na przykład: Masz pięć zadań, każdy przetwarza inny plik, a następnie zapisuje dane wyjściowe do magazynu obiektów blob.  W takim przypadku plik wejściowy powinien być określony w kolekcji **plików zasobów zadań,** ponieważ każde zadanie ma własny plik wejściowy.

## <a name="determine-the-scope-required-of-a-file"></a>Określanie zakresu wymaganego przez plik

Należy określić zakres pliku - jest to plik wymagany dla puli, zadania lub zadania. Pliki, które są ograniczone do puli należy użyć pakietów aplikacji puli lub zadania uruchamiania. Pliki o zakresie zadania należy użyć zadania przygotowania zadania. Dobrym przykładem plików o zakresie w puli lub na poziomie zadania są aplikacje. Pliki o zakresie zadania powinny używać plików zasobów zadań.

### <a name="other-ways-to-get-data-onto-batch-compute-nodes"></a>Inne sposoby przekazywania danych do węzłów obliczeniowych usługi Batch

Istnieją inne sposoby, aby uzyskać dane do węzłów obliczeniowych usługi Batch, które nie są oficjalnie zintegrowane z interfejsem API REST partii. Ponieważ masz kontrolę nad węzłami usługi Azure Batch i można uruchamiać niestandardowe pliki wykonywalne, można pobierać dane z dowolnej liczby źródeł niestandardowych, o ile węzeł usługi Batch ma łączność z obiektem docelowym i masz poświadczenia do tego źródła do węzła usługi Azure Batch. Oto kilka typowych przykładów:

- Pobieranie danych z języka SQL
- Pobieranie danych z innych usług internetowych/lokalizacji niestandardowych
- Mapowanie udziału sieciowego

### <a name="azure-storage"></a>Azure Storage

Magazyn obiektów Blob ma cele skalowalności pobierania. Cele skalowalności udziału plików magazynu azure są takie same jak w przypadku pojedynczego obiektu blob. Rozmiar będzie miał wpływ na liczbę węzłów i pul, których potrzebujesz.

