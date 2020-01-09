---
title: Co to jest Data Science Virtual Machine platformy Azure
description: Kluczowe składniki i scenariusze analizy dla maszyn wirtualnych do nauki o danych z systemami Windows i Linux.
keywords: data science tools, data science virtual machine, tools for data science, linux data science
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: overview
ms.date: 12/31/2019
ms.openlocfilehash: a63087620d50336c67472348da3b7f37fb380635
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/02/2020
ms.locfileid: "75611972"
---
# <a name="what-is-the-azure-data-science-virtual-machine-for-linux-and-windows"></a>Co to jest Data Science Virtual Machine platformy Azure dla systemów Linux i Windows?

Data Science Virtual Machine (DSVM) to dostosowany obraz maszyny wirtualnej na platformie Azure Cloud Platform zbudowany specjalnie na potrzeby analizy danych. Ma wiele popularnych narzędzi do nauki o danych i wstępnie skonfigurowanych do szybko Rozpocznij pracę tworzenia inteligentnych aplikacji na potrzeby zaawansowanej analizy. 

DSVM jest dostępny w:
+ **Windows Server 2019 (wersja zapoznawcza)**
+ **Ubuntu 18,04 LTS (wersja zapoznawcza)**
+ Windows Server 2016
+ Ubuntu 16,04 LTS i CentOS 7,4


> [!NOTE]
> Wszystkie narzędzia maszyny wirtualnej do uczenia głębokiego zostały złożone w Data Science Virtual Machine. 


## <a name="why-choose-the-dsvm"></a>Dlaczego warto wybrać DSVM?
Celem Data Science Virtual Machine jest zapewnienie pracownikom danych wszystkich poziomów umiejętności i między branżami ze wstępnie skonfigurowanym środowiskiem nauki o danych. Zamiast samodzielnie wdrażać porównywalny obszar roboczy, możesz udostępnić DSVM. Ten wybór może zaoszczędzić dni lub nawet _tygodnie_ w procesach instalacji, konfiguracji i zarządzania pakietami. Po przydzieleniu maszyny wirtualnej do nauki o danych można natychmiast rozpocząć pracę nad projektem nauki o danych.

## <a name="sample-use-cases"></a>Przykładowe przypadki użycia

Poniżej przedstawiono niektóre typowe przypadki użycia dla klientów DSVM.

### <a name="moving-data-science-workloads-to-the-cloud"></a>Przeniesienie obciążeń analizy danych do chmury

DSVM zawiera konfigurację linii bazowej dla zespołów nauki danych, które chcą zamienić swoje komputery lokalne na zarządzane pulpity w chmurze, dzięki czemu wszyscy analitykowie danych w zespole mają spójną konfigurację, z którą można weryfikować eksperymenty i wspierać współpracę. Obniża ona również koszty, zmniejszając obciążenie administratora systemu. Zmniejszenie obciążenia pozwala zaoszczędzić czas wymagany do oszacowania, zainstalowania i konserwacji pakietów oprogramowania na potrzeby zaawansowanej analizy.

### <a name="data-science-training-and-education"></a>Szkolenia i edukacja w zakresie analizy danych
Instruktorzy i nauczyciele, którzy uczą klasy nauki o danych, zwykle udostępniają obraz maszyny wirtualnej. Obraz ten gwarantuje, że uczniowie mają spójną konfigurację oraz że próbki przewidywalnie działają. 

DSVM tworzy środowisko na żądanie z spójną konfiguracją, która ułatwia problemy z obsługą i niezgodnością. To rozwiązanie oferuje znaczące korzyści w sytuacjach, w których jest konieczne częste tworzenie takich środowisk (zwłaszcza w przypadku krótszych szkoleń).

### <a name="on-demand-elastic-capacity-for-large-scale-projects"></a>Elastyczna wydajność na żądanie dla projektów w dużej skali
Imprezy rozpoczynająa i konkursy danych na dużą skalę, dzięki czemu można przeskalować wydajność sprzętu, zazwyczaj przez krótki czas trwania. DSVM może ułatwić replikację środowiska nauki o danych na żądanie, na skalowalnych w poziomie serwerach, które umożliwiają eksperymenty, w których można uruchamiać zasoby obliczeniowe o wysokiej jakości.

### <a name="custom-compute-power-for-azure-notebooks"></a>Niestandardowa moc obliczeniowa dla Azure Notebooks
[Azure Notebooks](../../notebooks/azure-notebooks-overview.md) to bezpłatna usługa hostowana, która umożliwia tworzenie, uruchamianie i udostępnianie notesów Jupyter w chmurze bez konieczności instalacji. Bezpłatna warstwa usługi jest ograniczona do 4 GB pamięci i 1 GB danych. 

Aby zwolnić wszystkie limity, można dołączyć projekt notesów do DSVM lub innej maszyny wirtualnej działającej na serwerze Jupyter. Jeśli zalogujesz się do Azure Notebooks przy użyciu konta za pomocą Azure Active Directory (na przykład konta firmowego), notesy automatycznie pokazują DSVMs w każdej subskrypcji skojarzonej z tym kontem. Możesz [dołączyć DSVM do Azure Notebooks](../../notebooks/configure-manage-azure-notebooks-projects.md#compute-tier) , aby rozwinąć dostępną moc obliczeniową.

### <a name="short-term-experimentation-and-evaluation"></a>Krótkoterminowe eksperymenty i ocena
Możesz użyć DSVM, aby oszacować lub poznać nowe [Narzędzia](./tools-included.md)do nauki o danych, szczególnie poprzez przechodzenie między opublikowanymi [przykładami i przewodnikami](./dsvm-samples-and-walkthroughs.md).


### <a name="deep-learning-with-gpus"></a>Uczenie głębokie za pomocą procesorów GPU
W DSVM modele szkoleniowe mogą korzystać z algorytmów uczenia głębokiego na sprzęcie opartym na procesorach GPU. Korzystając z możliwości skalowania maszyn wirtualnych platformy Azure, DSVM ułatwia korzystanie z sprzętu opartego na procesorze GPU w chmurze zgodnie z potrzebami. Możesz przełączyć się na maszynę wirtualną opartą na procesorze GPU podczas uczenia dużych modeli lub gdy potrzebujesz obliczeń o dużej szybkości przy zachowaniu tego samego dysku systemu operacyjnego. Można wybrać dowolną jednostkę SKU maszyn wirtualnych z serii N z funkcją DSVM. Zanotuj bezpłatne konta platformy Azure nie obsługują jednostek SKU maszyn wirtualnych obsługujących procesor GPU.

Wydanie systemu Windows Server 2016 DSVM jest wstępnie zainstalowane ze sterownikami procesora GPU, platformami i wersjami procesora GPU dla platform edukacyjnych. W wersji systemu Linux uczenie głębokie na procesorach GPU jest włączone zarówno w CentOS, jak i Ubuntu DSVMs. 

Możesz również wdrożyć Ubuntu, CentOS lub Windows 2016 wydania DSVM na maszynie wirtualnej platformy Azure, która nie jest oparta na procesorach GPU. W takim przypadku wszystkie platformy uczenia głębokiego zostaną przywrócone do trybu procesora CPU.
 
[Dowiedz się więcej o dostępnych strukturach głębokiego uczenia i AI](dsvm-tools-deep-learning-frameworks.md).

<a name="included"></a>

## <a name="whats-included-on-the-dsvm"></a>Co zawiera DSVM?

Zapoznaj się z pełną listą narzędzi w systemie Windows i Linux DSVM [.](tools-included.md)

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej z następujących artykułów:

+ W systemie Windows:
  + [Konfigurowanie maszyny DSVM z systemem Windows](provision-vm.md)
  + [Dziesięć rzeczy, które można wykonać na DSVM systemu Windows](vm-do-ten-things.md)

+ W systemie Linux:
  + [Konfigurowanie systemu Linux DSVM (Ubuntu)](dsvm-ubuntu-intro.md)
  + [Konfigurowanie systemu Linux DSVM (CentOS)](linux-dsvm-intro.md)
  + [Analiza danych w systemie Linux DSVM](linux-dsvm-walkthrough.md)
