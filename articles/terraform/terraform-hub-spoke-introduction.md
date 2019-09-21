---
title: Tworzenie topologii sieci hybrydowej Hub i satelity za pomocą Terraform na platformie Azure
description: Samouczek przedstawiający sposób tworzenia całej architektury referencyjnej sieci hybrydowej na platformie Azure przy użyciu Terraform
services: terraform
ms.service: azure
keywords: Terraform, Hub i szprych, sieci, sieci hybrydowe, DevOps, maszyna wirtualna, Azure, Komunikacja równorzędna sieci wirtualnych, sieciowe urządzenie wirtualne
author: VaijanathB
manager: jeconnoc
ms.author: vaangadi
ms.topic: tutorial
ms.date: 09/20/2019
ms.openlocfilehash: 5c2a61dd9da6d233a4b1410042f2125a1c300758
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/20/2019
ms.locfileid: "71173440"
---
# <a name="tutorial-create-a-hub-and-spoke-hybrid-network-topology-with-terraform-in-azure"></a>Samouczek: Tworzenie topologii sieci hybrydowej Hub i satelity za pomocą Terraform na platformie Azure

W tej serii samouczków pokazano, jak używać Terraform do implementowania na platformie Azure [topologii sieci Hub i szprych](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke). 

Topologia gwiazdy jest sposobem izolowania obciążeń podczas udostępniania wspólnych usług. Te usługi obejmują tożsamość i zabezpieczenia. Centrum to sieć wirtualna (VNet), która działa jako centralny punkt połączenia w sieci lokalnej. Szprychy to sieci wirtualne komunikujące się równorzędnie z piastą. Usługi udostępnione są wdrażane w centrum, podczas gdy indywidualne obciążenia są wdrażane w sieciach szprych.

Ten samouczek obejmuje następujące zadania:

> [!div class="checklist"]
> * Użyj HCL (HashiCorp Language) do układania zasobów architektury referencyjnej sieci hybrydowej Hub i satelity
> * Tworzenie zasobów urządzeń sieciowych centrów przy użyciu programu Terraform
> * Utwórz sieć centrów na platformie Azure za pomocą Terraform, aby pełnić rolę wspólnego punktu dla wszystkich zasobów
> * Tworzenie pojedynczych obciążeń jako szprych sieci wirtualnych na platformie Azure przy użyciu Terraform
> * Korzystanie z usługi Terraform do nawiązywania bram i połączeń między lokalnymi i sieciami platformy Azure
> * Korzystanie z Terraform do tworzenia wirtualnych sieci równorzędnych w sieciach szprych

## <a name="prerequisites"></a>Wymagania wstępne

- **Subskrypcja platformy Azure**: Jeśli nie masz jeszcze subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) .

- **Zainstaluj i skonfiguruj Terraform**: Aby udostępnić maszyny wirtualne i inne infrastruktury na platformie Azure, [Zainstaluj i skonfiguruj Terraform](/azure/virtual-machines/linux/terraform-install-configure)

## <a name="hub-and-spoke-topology-architecture"></a>Architektura topologii gwiazdy i szprych

W topologii gwiazdy koncentrator jest siecią wirtualną. Sieć wirtualna działa jako centralny punkt łączności z siecią lokalną. Szprychy są sieciami wirtualnymi równorzędnymi z piastą i mogą być używane do izolowania obciążeń. Ruch przepływa między lokalnym centrum danych a piastą za pośrednictwem połączenia bramy usługi ExpressRoute lub sieci VPN. Na poniższej ilustracji przedstawiono składniki w topologii gwiazdy:

![Architektura topologii gwiazdy i szprych na platformie Azure](./media/terraform-hub-and-spoke-tutorial-series/hub-spoke-architecture.png)

## <a name="benefits-of-the-hub-and-spoke-topology"></a>Zalety topologii gwiazdy

Topologia sieci gwiazdy jest sposobem izolowania obciążeń podczas udostępniania wspólnych usług. Te usługi obejmują tożsamość i zabezpieczenia. Koncentrator jest siecią wirtualną, która działa jako centralny punkt połączenia do sieci lokalnej. Szprychy to sieci wirtualne komunikujące się równorzędnie z piastą. Usługi udostępnione są wdrażane w centrum, podczas gdy indywidualne obciążenia są wdrażane w sieciach szprych. Poniżej przedstawiono niektóre zalety topologii sieci gwiazdy i gwiazdy:

- **Oszczędność kosztów** Dzięki scentralizowaniu usług w jednej lokalizacji, która może być współużytkowana przez wiele obciążeń. Te obciążenia obejmują wirtualne urządzenia sieciowe i serwery DNS.
- **Przezwyciężenie limitów subskrypcji** dzięki wprowadzeniu komunikacji równorzędnej sieci wirtualnych z różnych subskrypcji do centrum.
- **Separację problemów** między centralnym działem IT (SecOP, InfraOps) i obciążeniami (DevOps).

## <a name="typical-uses-for-the-hub-and-spoke-architecture"></a>Typowe zastosowania architektury Hub i szprychy

Niektóre typowe zastosowania architektury gwiazdy obejmują:

- Wielu klientów ma obciążenia, które są wdrażane w różnych środowiskach. Te środowiska obejmują programowanie, testowanie i produkcję. Często te obciążenia muszą udostępniać usługi, takie jak DNS, identyfikatory, NTP lub AD DS. Te usługi udostępnione można umieścić w sieci wirtualnej centrum. W ten sposób każde środowisko zostanie wdrożone w szprychie, aby zachować izolację.
- Obciążenia, które nie wymagają łączności ze sobą, ale wymagają dostępu do usług udostępnionych.
- Przedsiębiorstwa wymagające centralnej kontroli nad aspektami zabezpieczeń.
- Przedsiębiorstwa, które wymagają rozdzielnego zarządzania dla obciążeń w każdej szprychie.

## <a name="preview-the-demo-components"></a>Podgląd składników demonstracyjnych

Podczas pracy przez poszczególne samouczki w tej serii różne składniki są definiowane w odrębnych skryptach Terraform. Utworzona i wdrożona architektura demonstracyjna składa się z następujących składników:

- **Sieć lokalna**. Prywatna sieć lokalna działająca z organizacją. W przypadku architektury referencyjnej Hub i szprych Sieć wirtualna na platformie Azure jest używana do symulowania sieci lokalnej.

- **Urządzenie sieci VPN**. Urządzenie sieci VPN lub usługa zapewnia łączność zewnętrzną z siecią lokalną. Urządzenie sieci VPN może być urządzeniem sprzętowym lub rozwiązaniem programowym. 

- **Sieć wirtualna będąca piastą**. Centrum jest centralnym punktem łączności z siecią lokalną oraz miejscem, w którym można hostować usługi. Te usługi mogą być używane przez różne obciążenia hostowane w sieci wirtualnych szprychy.

- **Podsieć bramy**. Bramy sieci wirtualnej są przechowywane w tej samej podsieci.

- **Sieci wirtualne będące szprychami**. Szprychy mogą być używane do izolowania obciążeń w ich własnych sieciach wirtualnych zarządzanych oddzielnie od innych szprych. Każde obciążenie może zawierać wiele warstw z wieloma podsieciami połączonymi za pośrednictwem modułów równoważenia obciążenia platformy Azure. 

- **Komunikacja równorzędna sieci wirtualnych**. Dwa sieci wirtualnych można połączyć przy użyciu połączenia komunikacji równorzędnej. Połączenia komunikacji równorzędnej to nieprzechodnie połączenia między sieciami wirtualnymi o niskich opóźnieniach. Po nawiązaniu połączenia równorzędnego sieci wirtualnych ruch z programu Exchange przy użyciu szkieletu platformy Azure bez konieczności używania routera. W topologii sieci piasty i szprych wirtualne sieci równorzędne są używane do łączenia koncentratora z każdą szprychą. Elementy równorzędne można sieci wirtualnych w tym samym regionie lub w różnych regionach.

## <a name="create-the-directory-structure"></a>Tworzenie struktury katalogów

Utwórz katalog, który zawiera pliki konfiguracji Terraform dla demonstracji.

1. Przejdź do witryny [Azure Portal](https://portal.azure.com).

1. Otwórz usługę [Azure Cloud Shell](/azure/cloud-shell/overview). Jeśli środowisko nie zostało wybrane wcześniej, wybierz pozycję **Bash** jako swoje środowisko.

    ![Wiersz polecenia usługi Cloud Shell](./media/terraform-common/azure-portal-cloud-shell-button-min.png)

1. Zmień katalog na `clouddrive`.

    ```bash
    cd clouddrive
    ```

1. Utwórz katalog o nazwie `hub-spoke`.

    ```bash
    mkdir hub-spoke
    ```

1. Zmień katalog na nowy:

    ```bash
    cd hub-spoke
    ```

## <a name="declare-the-azure-provider"></a>Deklarowanie dostawcy platformy Azure

Utwórz plik konfiguracji narzędzia Terraform zawierający deklarację dostawcy platformy Azure.

1. W Cloud Shell Otwórz nowy plik o nazwie `main.tf`.

    ```bash
    code main.tf
    ```

1. Wklej następujący kod do edytora:

    ```hcl
    provider "azurerm" {
        version = "~>1.22"
    }
    ```

1. Zapisz plik i Zamknij Edytor.

## <a name="create-the-variables-file"></a>Utwórz plik zmiennych

Utwórz plik konfiguracji Terraform dla wspólnych zmiennych, które są używane w różnych skryptach.

1. W Cloud Shell Otwórz nowy plik o nazwie `variables.tf`.

    ```bash
    code variables.tf
    ```

1. Wklej następujący kod do edytora:

    ```hcl
    variable "location" {
      description = "Location of the network"
      default     = "centralus"
    }
    
    variable "username" {
      description = "Username for Virtual Machines"
      default     = "testadmin"
    }
    
    variable "password" {
      description = "Password for Virtual Machines"
      default     = "Password1234!"
    }
    
    variable "vmsize" {
      description = "Size of the VMs"
      default     = "Standard_DS1_v2"
    }
    ```

1. Zapisz plik i Zamknij Edytor.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"] 
> [Tworzenie lokalnej sieci wirtualnej z usługą Terraform na platformie Azure](./terraform-hub-spoke-on-prem.md)
