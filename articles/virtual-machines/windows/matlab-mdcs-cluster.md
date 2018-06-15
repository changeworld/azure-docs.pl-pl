---
title: MATLAB klastrów na maszynach wirtualnych | Dokumentacja firmy Microsoft
description: Umożliwia tworzenie klastrów MATLAB Distributed Computing serwera do uruchamiania obciążeń obliczeniowych równoległych MATLAB maszyny wirtualne Microsoft Azure
services: virtual-machines-windows
documentationcenter: ''
author: mscurrell
manager: jeconnoc
editor: ''
ms.assetid: e9980ce9-124a-41f1-b9ec-f444c8ea5c72
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Windows
ms.workload: infrastructure-services
ms.date: 05/09/2016
ms.author: markscu
ms.openlocfilehash: 695833fb12c0c7a130e98fe9b3bdfa502672ab29
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
ms.locfileid: "30914922"
---
# <a name="create-matlab-distributed-computing-server-clusters-on-azure-vms"></a>Tworzenie klastrów serwera przetwarzania danych rozproszonych MATLAB na maszynach wirtualnych Azure
Maszyny wirtualne Microsoft Azure umożliwia utworzenie co najmniej jeden klaster serwera Computing rozproszone MATLAB do uruchamiania obciążeń MATLAB równoległych obliczeniowych. Zainstaluj oprogramowanie serwera przetwarzania danych rozproszonych MATLAB na maszynie Wirtualnej jako obrazu podstawowego i przy użyciu szablonu Azure szybkiego startu skryptu programu Azure PowerShell (dostępne na [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/matlab-cluster)) do wdrażania i zarządzania klastrem. Po wdrożeniu Połącz się z klastrem do uruchamiania obciążeń.

## <a name="about-matlab-and-matlab-distributed-computing-server"></a>Temat MATLAB i MATLAB rozproszonego przetwarzania danych serwera
[MATLAB](http://www.mathworks.com/products/matlab/) platformy jest zoptymalizowany do rozwiązywania problemów inżynieryjne i naukowe. Użytkownikom MATLAB symulacje na dużą skalę i zadań przetwarzania danych Umożliwia równoległe MathWorks obliczeniowych produktów przyspieszenia ich obciążeń obliczeniowych dzięki wykorzystaniu klastrów obliczeniowych oraz usług siatki. [Równoległe przetwarzanie przybornika](http://www.mathworks.com/products/parallel-computing/) umożliwia użytkownikom MATLAB parallelize aplikacji i skorzystać z wielordzeniowych procesorów GPU i klastrów obliczeniowych. [Serwer przetwarzania danych rozproszonych MATLAB](http://www.mathworks.com/products/distriben/) MATLAB użytkownicy mogą korzystać z wielu komputerów w klastrze obliczeniowym.

Przy użyciu maszyn wirtualnych platformy Azure, można utworzyć klastry serwerów rozproszonych obliczeniowych MATLAB, które mają te same mechanizmy dostępne mógł przesłać pracy równoległej jako klastry lokalnych, takich jak interakcyjne zadań, zadania wsadowe, niezależnie od zadania i komunikacji zadania. Za pomocą platformy Azure w połączeniu z platformą MATLAB ma wiele zalet w porównaniu do inicjowania obsługi administracyjnej i przy użyciu tradycyjnych lokalnymi sprzętu: rozmiary zakres maszyny wirtualnej, tworzenie klastrów na żądanie, więc płacisz tylko za zasoby obliczeniowe, należy użyć, i możliwość testowania modeli na dużą skalę.  

## <a name="prerequisites"></a>Wymagania wstępne
* **Komputer kliencki** — należy komputer kliencki z systemem Windows do komunikowania się z Azure i klastrem serwera przetwarzania rozproszonego MATLAB po wdrożeniu.
* **Program Azure PowerShell** — zobacz [jak instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/overview) go zainstalować na komputerze klienckim.
* **Subskrypcja platformy Azure** — Jeśli nie masz subskrypcji, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/free/) w zaledwie kilka minut. W przypadku dużych klastrów rozważ z subskrypcji lub inne opcje zakupu.
* **przydział Vcpu** — może być konieczne zwiększyć przydział vCPU do wdrożenia klastra dużych lub więcej niż jednym klastrze MATLAB rozproszonego przetwarzania danych serwera. Aby zwiększyć przydział, [otwarcia żądania pomocy technicznej online klienta](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) bez dodatkowych opłat.
* **Licencje MATLAB, przybornika przetwarzanie równoległe i serwera przetwarzania danych rozproszonych MATLAB** -skrypty przyjęto założenie, że [MathWorks hostowanej License Manager](http://www.mathworks.com/products/parallel-computing/mathworks-hosted-license-manager/) jest używany dla wszystkich licencji.  
* **Oprogramowanie serwera przetwarzania rozproszonego MATLAB** — zostanie zainstalowana na maszynie Wirtualnej, która będzie służyć jako podstawowy obraz maszyny Wirtualnej dla maszyn wirtualnych klastra.

## <a name="high-level-steps"></a>Wysokiego poziomu kroków
Aby używać maszyn wirtualnych platformy Azure dla klastrów serwerów rozproszonych obliczeniowych MATLAB, wymagane są następujące ogólne kroki. Szczegółowe instrukcje znajdują się w dokumentacji towarzyszącego na szablonie Szybki Start i skrypty [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/matlab-cluster).

1. **Tworzenie obrazu maszyny Wirtualnej**  

   * Pobierz i zainstaluj oprogramowanie serwera przetwarzania danych rozproszonych MATLAB na tej maszyny Wirtualnej.

     > [!NOTE]
     > Ten proces może potrwać kilka godzin, ale należy to zrobić, gdy dla każdej wersji MATLAB używasz.   
     >
     >
2. **Utwórz co najmniej jeden klaster**  

   * Aby utworzyć klaster z obrazu maszyny Wirtualnej, należy użyć podany skrypt programu PowerShell lub szablonie Szybki Start.   
   * Zarządzanie klastrami przy użyciu dostarczonego skrypt programu PowerShell, co pozwala na liście, wstrzymać, wznowić i usuwać klastry.

## <a name="cluster-configurations"></a>Konfiguracje klastrów
Obecnie skryptu tworzenia klastra i szablon umożliwiają utworzenie jednego topologii MATLAB rozproszonego przetwarzania danych serwera. Należy utworzyć co najmniej jeden klaster dodatkowe z każdym klastrze mających różne liczby procesu roboczego maszyny wirtualne, przy użyciu różnych rozmiarów maszyn wirtualnych i tak dalej.

### <a name="matlab-client-and-cluster-in-azure"></a>MATLAB klienta i klaster na platformie Azure
MATLAB klienta węzła, węzeł MATLAB harmonogramu zadań i węzłów serwera przetwarzania danych rozproszonych MATLAB "procesu roboczego" są wszystkie skonfigurowane jako maszynach wirtualnych platformy Azure w sieci wirtualnej, jak pokazano na poniższej ilustracji.


* Do korzystania z klastra, nawiązać przez pulpit zdalny na węźle klienta. Ten klient MATLAB jest uruchamiany na węźle klienta.
* Węzeł Klient ma udziału plików, które są dostępne dla wszystkich pracowników.
* Menedżer licencji hostowanej MathWorks służy do kontroli licencji oprogramowania MATLAB.
* Domyślnie jednego serwera przetwarzania danych rozproszonych MATLAB procesu roboczego na vCPU zostało utworzone w procesie roboczym maszyn wirtualnych, ale można określić dowolną liczbę.

## <a name="use-an-azure-based-cluster"></a>Używanie klastra bazujących na platformie Azure
Jako inne typy klastrów serwerów rozproszonych obliczeniowych MATLAB, należy użyć Menedżera profilu klastra w kliencie MATLAB (na kliencie maszyny Wirtualnej) aby utworzyć profil klastra MATLAB harmonogramu zadań.

![Menedżer profilu klastra](./media/matlab-mdcs-cluster/cluster_profile_manager.png)

## <a name="next-steps"></a>Kolejne kroki
* Aby uzyskać szczegółowe instrukcje dotyczące wdrażania i zarządzania klastrami serwerów rozproszonych obliczeniowych MATLAB na platformie Azure, zobacz [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/matlab-cluster) repozytorium zawierającego szablony i skryptów.
* Przejdź do [lokacji MathWorks](http://www.mathworks.com/) szczegółowa dokumentacja MATLAB i MATLAB rozproszonego przetwarzania danych serwera.
