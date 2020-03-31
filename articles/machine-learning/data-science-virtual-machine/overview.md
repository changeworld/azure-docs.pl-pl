---
title: Co to jest maszyna wirtualna do nauki o danych platformy Azure
titleSuffix: Azure Data Science Virtual Machine
description: Kluczowe składniki i scenariusze analizy dla maszyn wirtualnych do nauki o danych z systemami Windows i Linux.
keywords: data science tools, data science virtual machine, tools for data science, linux data science
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: overview
ms.date: 12/31/2019
ms.openlocfilehash: a5fbcc1eef8717fdb1aa7f914c3e0ba6594fc27a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80281803"
---
# <a name="what-is-the-azure-data-science-virtual-machine-for-linux-and-windows"></a>Co to jest maszyna wirtualna do nauki o danych platformy Azure dla systemów Linux i Windows?

Maszyna wirtualna do nauki o danych (DSVM) to dostosowany obraz maszyny Wirtualnej na platformie chmury platformy Azure, stworzonej specjalnie do nauki o danych. Posiada wiele popularnych narzędzi do nauki o danych preinstalowanych i wstępnie skonfigurowanych do szybkiego tworzenia inteligentnych aplikacji do zaawansowanej analizy. 

DSVM jest dostępny na:
+ **Windows Server 2019**
+ **Ubuntu 18.04 LTS**
+ Windows Server 2016
+ Ubuntu 16.04 LTS i CentOS 7.4


> [!NOTE]
> Wszystkie narzędzia maszyn wirtualnych do uczenia głębokiego zostały złożone w maszynie wirtualnej do nauki o danych. 


## <a name="why-choose-the-dsvm"></a>Dlaczego warto wybrać DSVM?
Celem maszyny wirtualnej do nauki o danych jest zapewnienie specjalistom danych na wszystkich poziomach umiejętności i w różnych branżach beztarciowego, wstępnie skonfigurowanego środowiska nauki o danych. Zamiast wprowadzać porównywalne obszaru roboczego na własną rękę, można aprowizować DSVM. Ten wybór może zaoszczędzić dni lub nawet _tygodnie_ na procesach instalacji, konfiguracji i zarządzania pakietami. Po przydzieleniu maszyny wirtualnej do nauki o danych można natychmiast rozpocząć pracę nad projektem nauki o danych.

## <a name="sample-use-cases"></a>Przykładowe przypadki użycia

Poniżej przedstawiamy niektóre typowe przypadki użycia dla klientów DSVM.

### <a name="moving-data-science-workloads-to-the-cloud"></a>Przenoszenie obciążeń do nauki o danych do chmury

DSVM zapewnia konfigurację linii bazowej dla zespołów do nauki o danych, które chcą zastąpić swoje pulpity lokalne zarządzanym pulpitem w chmurze, zapewniając, że wszyscy analitycy danych w zespole mają spójną konfigurację, z którą można weryfikować eksperymenty i promować współpracę. Obniża również koszty poprzez zmniejszenie obciążenia sysadmin. To zmniejszenie obciążenia pozwala zaoszczędzić czas potrzebny na ocenę, zainstalowanie i konserwację pakietów oprogramowania do zaawansowanej analizy.

### <a name="data-science-training-and-education"></a>Szkolenia i edukacja w zakresie nauki o danych
Trenerzy i edukatorzy przedsiębiorstw, którzy uczą lekcji nauki o danych, zazwyczaj zapewniają obraz maszyny wirtualnej. Obraz zapewnia, że uczniowie mają spójną konfigurację i że próbki działają przewidywalnie. 

DSVM tworzy środowisko na żądanie z spójną konfiguracją, która ułatwia wyzwania związane z obsługą i niezgodnością. To rozwiązanie oferuje znaczące korzyści w sytuacjach, w których jest konieczne częste tworzenie takich środowisk (zwłaszcza w przypadku krótszych szkoleń).

### <a name="on-demand-elastic-capacity-for-large-scale-projects"></a>Elastyczna wydajność na żądanie dla projektów w dużej skali
Hackathons/konkursy do nauki o danych lub modelowanie i eksploracja danych na dużą skalę wymagają skalowalności pojemności sprzętowej, zazwyczaj przez krótki czas. DSVM może pomóc w szybkim replikowaniu środowiska nauki o danych na żądanie na serwerach skalowanych w poziomie, które umożliwiają eksperymenty, które mogą działać zasoby obliczeniowe o dużej mocy.

### <a name="custom-compute-power-for-azure-notebooks"></a>Niestandardowa moc obliczeniowa notesów platformy Azure
[Notesy platformy Azure](../../notebooks/azure-notebooks-overview.md) to bezpłatna usługa hostowana do tworzenia, uruchamiania i udostępniania notesów Jupyter w chmurze bez instalacji. Warstwa bezpłatnej usługi jest ograniczona do 4 GB pamięci i 1 GB danych. 

Aby zwolnić wszystkie limity, można dołączyć projekt notesów do dsvm lub innej maszyny Wirtualnej uruchomionej na serwerze Jupyter. Jeśli zalogujesz się do notesów platformy Azure przy użyciu konta przy użyciu usługi Azure Active Directory (na przykład konta firmowego), notesy automatycznie wyświetla dsvms w dowolnej subskrypcji skojarzonych z tym kontem. Model [DSVM można dołączyć do notesów platformy Azure,](../../notebooks/configure-manage-azure-notebooks-projects.md#compute-tier) aby rozszerzyć dostępną moc obliczeniową.

### <a name="short-term-experimentation-and-evaluation"></a>Krótkoterminowe eksperymenty i ocena
Za pomocą dsvm do oceny lub uczenia się nowych [narzędzi](./tools-included.md)do nauki o danych, zwłaszcza przechodząc przez niektóre z naszych [opublikowanych próbek i instruktaży.](./dsvm-samples-and-walkthroughs.md)


### <a name="deep-learning-with-gpus"></a>Głębokie uczenie się za pomocą procesorów graficznych
W dsvm modeli szkoleniowych można używać algorytmów uczenia głębokiego na sprzęcie, który jest oparty na procesorach graficznych (GPU). Korzystając z możliwości skalowania maszyny Wirtualnej platformy Azure, dsvm pomaga używać sprzętu opartego na procesorze GPU w chmurze zgodnie z potrzebami. Maszynę wirtualną opartą na procesorze GPU można przełączyć podczas szkolenia dużych modeli lub gdy potrzebujesz szybkich obliczeń przy zachowaniu tego samego dysku systemu operacyjnego. Można wybrać dowolną z jednostek SKU maszyny wirtualnej obsługującej maszyny gpu serii N z dsvm. Należy pamiętać, że bezpłatne konta platformy Azure nie obsługują jednostek SKU obsługujących maszyny wirtualne z obsługą procesora GPU.

Wersja dsvm systemu Windows Server 2016 jest fabrycznie zainstalowana ze sterownikami GPU, strukturami i wersjami procesorów GPU w ramach uczenia głębokiego. W wersji Linux głębokie uczenie na procesorach gpu jest włączone zarówno na systemach DVM CentOS, jak i Ubuntu. 

Można również wdrożyć Ubuntu, CentOS lub Windows 2016 wersja DSVM do maszyny wirtualnej platformy Azure, która nie jest oparta na procesorach GPU. W takim przypadku wszystkie struktury uczenia głębokiego powrócą do trybu procesora CPU.
 
[Dowiedz się więcej o dostępnych platformach uczenia głębokiego i si.](dsvm-tools-deep-learning-frameworks.md)

<a name="included"></a>

## <a name="whats-included-on-the-dsvm"></a>Co znajduje się w dsvm?

Zobacz pełną listę narzędzi zarówno w systemie Windows, jak i Linux DSVM [tutaj](tools-included.md).

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej z tych artykułów:

+ W systemie Windows:
  + [Konfigurowanie maszyny DSVM z systemem Windows](provision-vm.md)
  + [Dziesięć czynności, które można wykonać w systemie Windows DSVM](vm-do-ten-things.md)

+ W systemie Linux:
  + [Konfigurowanie systemu Linux DSVM (Ubuntu)](dsvm-ubuntu-intro.md)
  + [Konfigurowanie systemu Linux DSVM (CentOS)](linux-dsvm-intro.md)
  + [Analityka danych na linuxowym dsvm](linux-dsvm-walkthrough.md)
