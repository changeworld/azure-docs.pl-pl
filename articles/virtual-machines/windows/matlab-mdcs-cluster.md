---
title: Klastry MATLAB na maszynach wirtualnych
description: Używanie maszyn wirtualnych platformy Microsoft Azure do tworzenia klastrów rozproszonego serwera obliczeniowego MATLAB w celu uruchamiania równoległych obciążeń MATLAB intensywnie korzystających z mocy obliczeniowej
services: virtual-machines-windows
documentationcenter: ''
author: mscurrell
manager: gwallace
editor: ''
ms.assetid: e9980ce9-124a-41f1-b9ec-f444c8ea5c72
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: Windows
ms.workload: infrastructure-services
ms.date: 05/09/2016
ms.author: markscu
ms.openlocfilehash: a2fb2479f5544b869b51e796085fcb4d0b76121a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74038142"
---
# <a name="create-matlab-distributed-computing-server-clusters-on-azure-vms"></a>Tworzenie klastrów serwerów przetwarzania rozproszonego MATLAB na maszynach wirtualnych platformy Azure
Maszyny wirtualne platformy Microsoft Azure umożliwia utworzenie co najmniej jednego klastra rozproszonego serwera obliczeniowego MATLAB w celu uruchamiania równoległych obciążeń MATLAB intensywnie korzystających z mocy obliczeniowej. Zainstaluj oprogramowanie MATLAB Distributed Computing Server na maszynie Wirtualnej, aby używać go jako obrazu podstawowego, i użyj szablonu szybkiego startu platformy Azure lub skryptu programu Azure PowerShell (dostępnego w [usłudze GitHub)](https://github.com/Azure/azure-quickstart-templates/tree/master/matlab-cluster)do wdrażania klastra i zarządzania nim. Po wdrożeniu połącz się z klastrem, aby uruchomić obciążenia.

## <a name="about-matlab-and-matlab-distributed-computing-server"></a>Informacje o matlab i matlab rozproszonego serwera obliczeniowego
Platforma [MATLAB](https://www.mathworks.com/products/matlab/) jest zoptymalizowana pod kątem rozwiązywania problemów inżynieryjnych i naukowych. Użytkownicy MATLAB z symulacjami na dużą skalę i zadaniami przetwarzania danych mogą korzystać z równoległych produktów obliczeniowych MathWorks, aby przyspieszyć swoje obciążenia wymagające obliczeniowe, korzystając z klastrów obliczeniowych i usług sieciowych. [Parallel Computing Toolbox](https://www.mathworks.com/products/parallel-computing/) umożliwia użytkownikom MATLAB równoległość aplikacji i korzystanie z procesorów wielordzeniowych, procesorów graficznych i klastrów obliczeniowych. [Matlab Distributed Computing Server](https://www.mathworks.com/products/distriben/) umożliwia użytkownikom MATLAB korzystanie z wielu komputerów w klastrze obliczeniowym.

Korzystając z maszyn wirtualnych platformy Azure, można utworzyć klastry serwerów przetwarzania rozproszonego MATLAB, które mają wszystkie te same mechanizmy dostępne do przesyłania pracy równoległej jako klastry lokalne, takie jak zadania interaktywne, zadania wsadowe, zadania niezależne i komunikacja Zadania. Korzystanie z platformy Azure w połączeniu z platformą MATLAB ma wiele zalet w porównaniu z inicjowania obsługi administracyjnej i przy użyciu tradycyjnego sprzętu lokalnego: zakres rozmiarów maszyn wirtualnych, tworzenie klastrów na żądanie, dzięki czemu płacisz tylko za zasoby obliczeniowe, których używasz, i możliwość testowania modeli na dużą skalę.  

## <a name="prerequisites"></a>Wymagania wstępne
* **Komputer kliencki** — do komunikowania się z platformą Azure i klastrem serwera przetwarzania rozproszonego MATLAB po wdrożeniu potrzebny jest komputer kliencki z systemem Windows.
* **Program Azure PowerShell** — zobacz [Jak zainstalować i skonfigurować program Azure PowerShell,](/powershell/azure/overview) aby zainstalować go na komputerze klienckim.
* **Subskrypcja platformy Azure** — jeśli nie masz subskrypcji, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/free/) w ciągu zaledwie kilku minut. W przypadku większych klastrów należy wziąć pod uwagę subskrypcję płatności zgodnie z rzeczywistymu polub lub inne opcje zakupu.
* **Przydział procesorów wirtualnych** — może być konieczne zwiększenie przydziału procesora wirtualnego w celu wdrożenia dużego klastra lub więcej niż jednego klastra rozproszonego serwera obliczeniowego MATLAB. Aby zwiększyć przydział, [otwórz żądanie obsługi klienta online](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) bez żadnych opłat.
* **Licencje MATLAB, Parallel Computing Toolbox i MATLAB Distributed Computing Server** — skrypty zakładają, że [Menedżer licencji hostowanych MathWorks](https://www.mathworks.com/help/install/license-management.html) jest używany dla wszystkich licencji.  
* **Oprogramowanie MATLAB Distributed Computing Server** — zostanie zainstalowane na maszynie wirtualnej, która będzie używana jako podstawowy obraz maszyny Wirtualnej dla maszyn wirtualnych klastra.

## <a name="high-level-steps"></a>Kroki wysokiego poziomu
Aby używać maszyn wirtualnych platformy Azure dla klastrów serwerów przetwarzania rozproszonego MATLAB, wymagane są następujące kroki wysokiego poziomu. Szczegółowe instrukcje znajdują się w dokumentacji dołączonej do szablonu szybkiego startu i skryptów na [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/matlab-cluster).

1. **Tworzenie podstawowego obrazu maszyny Wirtualnej**  

   * Pobierz i zainstaluj oprogramowanie MATLAB Distributed Computing Server na tej maszynie wirtualnej.

     > [!NOTE]
     > Ten proces może potrwać kilka godzin, ale wystarczy zrobić to tylko raz dla każdej wersji MATLAB, której używasz.   
     >
     >
2. **Tworzenie jednego lub większej liczby klastrów**  

   * Użyj dostarczonego skryptu programu PowerShell lub użyj szablonu szybkiego startu, aby utworzyć klaster z podstawowego obrazu maszyny Wirtualnej.   
   * Zarządzanie klastrami przy użyciu dostarczonego skryptu programu PowerShell, który umożliwia wyświetlanie, wstrzymywanie, wznawianie i usuwanie klastrów.

## <a name="cluster-configurations"></a>Konfiguracje klastra
Obecnie skrypt i szablon tworzenia klastra umożliwiają utworzenie pojedynczej topologii serwera przetwarzania rozproszonego MATLAB. Jeśli chcesz, utwórz jeden lub więcej dodatkowych klastrów, z każdego klastra o innej liczbie maszyn wirtualnych procesu roboczego, przy użyciu różnych rozmiarów maszyn wirtualnych i tak dalej.

### <a name="matlab-client-and-cluster-in-azure"></a>Klient i klaster MATLAB na platformie Azure
Węzeł klienta MATLAB, węzeł harmonogram zadań MATLAB i węzły "roboczy" serwera przetwarzania rozproszonego MATLAB są skonfigurowane jako maszyny wirtualne platformy Azure w sieci wirtualnej, jak pokazano na poniższym rysunku.


* Aby użyć klastra, połącz się za pomocą pulpitu zdalnego z węzłem klienta. Węzeł klienta uruchamia klienta MATLAB.
* Węzeł klienta ma udział plików, do który mogą uzyskiwać dostęp wszyscy pracownicy.
* MathWorks Hosted License Manager służy do sprawdzania licencji dla całego oprogramowania MATLAB.
* Domyślnie na maszynach wirtualnych procesu roboczego procesu roboczego procesu roboczego obliczeń rozproszonych MATLAB tworzony jest jeden pracownik przetwarzania rozproszonego MATLAB na procesor wirtualny, ale można określić dowolną liczbę.

## <a name="use-an-azure-based-cluster"></a>Korzystanie z klastra opartego na platformie Azure
Podobnie jak w przypadku innych typów klastrów serwerów przetwarzania rozproszonego MATLAB, aby utworzyć profil klastra harmonogramu zadań MATLAB, należy użyć Menedżera profilu klastra na kliencie MATLAB (na maszynie wirtualnej klienta).

![Menedżer profilu klastra](./media/matlab-mdcs-cluster/cluster_profile_manager.png)

## <a name="next-steps"></a>Następne kroki
* Aby uzyskać szczegółowe instrukcje dotyczące wdrażania klastrów serwerów przetwarzania rozproszonego MATLAB i zarządzania nimi na platformie Azure, zobacz repozytorium [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/matlab-cluster) zawierające szablony i skrypty.
* Przejdź do [witryny MathWorks,](https://www.mathworks.com/) aby uzyskać szczegółową dokumentację dla MATLAB i MATLAB Distributed Computing Server.
