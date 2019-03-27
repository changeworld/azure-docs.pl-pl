---
title: MATLAB klastrów na maszynach wirtualnych z | Dokumentacja firmy Microsoft
description: Usługa Microsoft Azure virtual machines umożliwia utworzyć klastry serwera przetwarzania rozproszonego MATLAB do uruchamiania obciążeń MATLAB intensywnych obliczeń równoległych
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
ms.openlocfilehash: a9de0f0021d92c59c44e85f0487cc9a08b6a3099
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2019
ms.locfileid: "58497366"
---
# <a name="create-matlab-distributed-computing-server-clusters-on-azure-vms"></a>Tworzenie klastrów serwera rozproszonych obliczeń MATLAB na maszynach wirtualnych platformy Azure
Maszyny wirtualne Microsoft Azure umożliwia utworzenie co najmniej jeden klaster MATLAB rozproszonego przetwarzania serwera do uruchamiania obciążeń MATLAB intensywnych obliczeń równoległych. Instalowanie oprogramowania serwera rozproszonych obliczeń MATLAB na maszynie Wirtualnej do użycia jako obraz podstawowy i użyj szablonu szybkiego startu platformy Azure lub skrypt programu Azure PowerShell (dostępne na [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/matlab-cluster)) do wdrażania i zarządzania klastrem. Po wdrożeniu Połącz się z klastrem, do uruchamiania obciążeń.

## <a name="about-matlab-and-matlab-distributed-computing-server"></a>Temat MATLAB a serwerem przetwarzania MATLAB rozproszonych
[MATLAB](https://www.mathworks.com/products/matlab/) platformy zoptymalizowana pod kątem rozwiązywania problemów inżynieryjnych i naukowych. Użytkownicy MATLAB przy użyciu symulacji na dużą skalę i zadań przetwarzania danych mogą używać MathWorks równoległego przetwarzania danych produktów, aby przyspieszyć ich obciążeń intensywnie korzystających z obliczeń, dzięki wykorzystaniu usług siatki i klastrów obliczeniowych. [Równoległe przetwarzanie przybornika](https://www.mathworks.com/products/parallel-computing/) umożliwia użytkownikom MATLAB zrównoleglić aplikacje i korzystać z zalet wielordzeniowych procesorach GPU i klastrów obliczeniowych. [Serwer przetwarzania danych rozproszonych MATLAB](https://www.mathworks.com/products/distriben/) umożliwia użytkownikom MATLAB korzystanie z wielu komputerów w klastrze obliczeniowym.

Za pomocą maszyn wirtualnych platformy Azure, można utworzyć klastrów serwera rozproszonych obliczeń MATLAB, które mają te same mechanizmy dostępne do przesłania równoległą pracę jako klastrów lokalnych, takich jak interakcyjne zadań, zadań wsadowych, niezależnych zadań i komunikacji zadania. Korzystanie z platformy Azure w połączeniu z platformą MATLAB ma wiele zalet w porównaniu do inicjowania obsługi administracyjnej i przy użyciu tradycyjnych lokalnych sprzętu: szeroką gamę maszyn wirtualnych o rozmiarach, tworzenie klastrów na żądanie, dzięki czemu płacisz tylko za zasoby obliczeniowe używane, i możliwość testowania modeli na dużą skalę.  

## <a name="prerequisites"></a>Wymagania wstępne
* **Komputer kliencki** — należy komputer kliencki z systemem Windows do komunikowania się z platformą Azure i klastrem serwera rozproszonych obliczeń MATLAB po wdrożeniu.
* **Program Azure PowerShell** — zobacz [jak zainstalować i skonfigurować program Azure PowerShell](/powershell/azure/overview) go zainstalować na komputerze klienckim.
* **Subskrypcja platformy Azure** — Jeśli nie masz subskrypcji, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/free/) w zaledwie kilka minut. W przypadku większych klastrów należy wziąć pod uwagę subskrypcji zgodnie z rzeczywistym użyciem lub inne opcje zakupu.
* **limit przydziału procesorów wirtualnych Vcpu** — może być konieczne zwiększyć limit przydziału procesorów wirtualnych, aby wdrożyć więcej niż jeden klaster serwera rozproszonych obliczeń MATLAB lub dużego klastra. Aby zwiększyć limit przydziału, [Otwórz żądanie obsługi klienta online](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) bez dodatkowych opłat.
* **Licencje MATLAB przybornika obliczeń równoległych oraz serwera rozproszonych obliczeń MATLAB** — skrypty przyjęto założenie, że [MathWorks hostowanych License Manager](https://www.mathworks.com/help/install/license-management.html) służy wobec wszystkich licencji.  
* **Oprogramowanie serwerowe rozproszonych obliczeń MATLAB** — zostanie zainstalowany na maszynie Wirtualnej, która będzie służyć jako podstawowy obraz maszyny Wirtualnej dla maszyn wirtualnych klastra.

## <a name="high-level-steps"></a>Wysokiego poziomu kroków
Aby użyć maszyn wirtualnych platformy Azure dla klastrów serwerów rozproszonych obliczeń MATLAB, następujące ogólne kroki są wymagane. Szczegółowe instrukcje znajdują się w dokumentacji towarzyszącej szablon szybkiego startu i skryptów na [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/matlab-cluster).

1. **Tworzenie podstawowego obrazu maszyny Wirtualnej**  

   * Pobieranie i instalowanie oprogramowania serwera rozproszonych obliczeń MATLAB na tej maszynie Wirtualnej.

     > [!NOTE]
     > Ten proces może potrwać kilka godzin, ale trzeba zrobić, gdy dla każdej wersji MATLAB używasz.   
     >
     >
2. **Utwórz co najmniej jeden klaster**  

   * Aby utworzyć klaster z podstawowego obrazu maszyny Wirtualnej, należy użyć dostarczony skrypt programu PowerShell lub szablonu szybkiego startu.   
   * Zarządzanie klastrami przy użyciu dostarczony skrypt programu PowerShell, co pozwala na liście, wstrzymywanie, wznawianie i usuwać klastry.

## <a name="cluster-configurations"></a>Konfiguracje klastrów
Obecnie szablon i skryptu tworzenia klastra umożliwiają tworzenie jednego topologii serwerów rozproszonych obliczeń MATLAB. Jeśli chcesz, należy utworzyć co najmniej jeden klaster dodatkowe z każdym klastrze mające różną liczbę procesów roboczych maszyn wirtualnych przy użyciu różnych rozmiarów maszyn wirtualnych i tak dalej.

### <a name="matlab-client-and-cluster-in-azure"></a>Klient MATLAB i klaster na platformie Azure
Węzeł klienta MATLAB, węzeł MATLAB harmonogramu zadań i węzłów serwera rozproszonych obliczeń MATLAB "procesu roboczego" są wszystkie konfigurowane jako maszyny wirtualne platformy Azure w sieci wirtualnej, jak pokazano na poniższej ilustracji.


* Aby korzystać z klastra, nawiązać połączenia przez pulpit zdalny węzeł klienta. Węzeł klienta działa klient MATLAB.
* Węzeł klienta ma udziału plików, które są dostępne dla wszystkich pracowników.
* MathWorks hostowanych License Manager jest używany do sprawdzenia licencji dla całego oprogramowania MATLAB.
* Domyślnie jednego serwera rozproszonych obliczeń MATLAB procesu roboczego na każdy procesor wirtualny jest tworzony w ramach procesu roboczego maszyn wirtualnych, ale można określić dowolną liczbę.

## <a name="use-an-azure-based-cluster"></a>Używanie klastra opartych na platformie Azure
Jako przy użyciu innych typów klastrów serwerów rozproszonych obliczeń MATLAB, należy użyć Menedżera profilu klastra w kliencie MATLAB (na kliencie, maszyna wirtualna) do utworzenia profilu klaster MATLAB harmonogramu zadań.

![Menedżer profilu klastra](./media/matlab-mdcs-cluster/cluster_profile_manager.png)

## <a name="next-steps"></a>Kolejne kroki
* Aby uzyskać szczegółowe instrukcje wdrażania i zarządzania klastrami serwerów rozproszonych obliczeń MATLAB na platformie Azure, zobacz [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/matlab-cluster) repozytorium zawierające szablonów i skryptów.
* Przejdź do [witryny MathWorks](https://www.mathworks.com/) ze szczegółową dokumentacją MATLAB i serwera rozproszonych obliczeń MATLAB.
