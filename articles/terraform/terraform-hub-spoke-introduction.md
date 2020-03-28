---
title: Samouczek — tworzenie koncentratora i sieci hybrydowej na platformie Azure przy użyciu terraform
description: Samouczek ilustrujący sposób tworzenia całej architektury referencyjnej sieci hybrydowej na platformie Azure przy użyciu programu Terraform
ms.topic: tutorial
ms.date: 10/26/2019
ms.openlocfilehash: 6f156dd90b83ceaf5749c8c2acebae35bcb54a92
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77472183"
---
# <a name="tutorial-create-a-hub-and-spoke-hybrid-network-topology-in-azure-using-terraform"></a>Samouczek: Tworzenie koncentratora i szprychy hybrydowej topologii sieci na platformie Azure przy użyciu terraform

W tej serii samouczków pokazano, jak zaimplementować na platformie Azure [topologię sieci koncentratora i szprychy.](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) 

Topologia koncentratora i szprychy to sposób izolowania obciążeń podczas udostępniania typowych usług. Usługi te obejmują tożsamość i bezpieczeństwo. Koncentrator jest siecią wirtualną, która działa jako centralny punkt połączenia z siecią lokalną. Szprychy to sieci wirtualne komunikujące się równorzędnie z piastą. Usługi udostępnione są wdrażane w centrum, podczas gdy poszczególne obciążenia są wdrażane wewnątrz sieci szprychowych.

Ten samouczek obejmuje następujące zadania:

> [!div class="checklist"]
> * Użyj HCL (HashiCorp Language), aby rozłożyć zasoby architektury odniesienia sieci hybrydowej i szprychy
> * Tworzenie zasobów urządzeń sieciowych za pomocą programu Terraform
> * Tworzenie sieci centrum na platformie Azure za pomocą programu Terraform, aby działać jako wspólny punkt dla wszystkich zasobów
> * Tworzenie pojedynczych obciążeń jako sieci wirtualnych szprych na platformie Azure za pomocą programu Terraform
> * Funkcja Terraform umożliwia ustanawianie bram i połączeń między sieciami lokalnymi i sieciami platformy Azure
> * Tworzenie komunikacji równorzędnej sieci wirtualnej w sieciach szprychowych za pomocą programu Terraform

## <a name="prerequisites"></a>Wymagania wstępne

- **Subskrypcja platformy Azure:** jeśli nie masz jeszcze subskrypcji platformy Azure, utwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) przed rozpoczęciem.

- **Instalowanie i konfigurowanie programu Terraform**: Aby aprowizować maszyny wirtualne i inną infrastrukturę na platformie Azure, [zainstaluj i skonfiguruj program Terraform](terraform-install-configure.md)

## <a name="hub-and-spoke-topology-architecture"></a>Architektura topologii piasty i szprychy

W topologii koncentratora i szprychy koncentrator jest siecią wirtualną. Sieć wirtualna działa jako centralny punkt łączności z siecią lokalną. Szprychy są sieciami wirtualnymi równorzędnymi z piastą i mogą być używane do izolowania obciążeń. Ruch przepływa między lokalnym centrum danych a piastą za pośrednictwem połączenia bramy usługi ExpressRoute lub sieci VPN. Na poniższej ilustracji przedstawiono składniki w topologii koncentratora i szprychy:

![Architektura topologii koncentratora i szprychy na platformie Azure](./media/terraform-hub-and-spoke-tutorial-series/hub-spoke-architecture.png)

## <a name="benefits-of-the-hub-and-spoke-topology"></a>Zalety topologii piasty i szprychy

Topologia sieci koncentratora i szprychy to sposób izolowania obciążeń podczas udostępniania typowych usług. Usługi te obejmują tożsamość i bezpieczeństwo. Koncentrator jest siecią wirtualną, która działa jako centralny punkt połączenia z siecią lokalną. Szprychy to sieci wirtualne komunikujące się równorzędnie z piastą. Usługi udostępnione są wdrażane w centrum, podczas gdy poszczególne obciążenia są wdrażane wewnątrz sieci szprychowych. Oto kilka zalet topologii sieci koncentratora i szprychy:

- **Oszczędność kosztów** dzięki centralizacji usług w jednej lokalizacji, która może być współużytkowana przez wiele obciążeń. Obciążenia te obejmują wirtualne urządzenia sieciowe i serwery DNS.
- **Przezwyciężenie limitów subskrypcji** dzięki wprowadzeniu komunikacji równorzędnej sieci wirtualnych z różnych subskrypcji do centrum.
- **Separację problemów** między centralnym działem IT (SecOP, InfraOps) i obciążeniami (DevOps).

## <a name="typical-uses-for-the-hub-and-spoke-architecture"></a>Typowe zastosowania architektury piasty i szprychy

Niektóre typowe zastosowania architektury koncentratora i szprychy obejmują:

- Wielu klientów ma obciążeń, które są wdrażane w różnych środowiskach. Środowiska te obejmują rozwój, testowanie i produkcję. Wiele razy te obciążenia muszą współużytkować usługi, takie jak DNS, IDS, NTP lub AD DS. Te usługi udostępnione można umieścić w sieci wirtualnej centrum. W ten sposób każde środowisko jest wdrażane do mówił do utrzymania izolacji.
- Obciążenia, które nie wymagają łączności ze sobą, ale wymagają dostępu do usług udostępnionych.
- Przedsiębiorstwa, które wymagają centralnej kontroli nad aspektami bezpieczeństwa.
- Przedsiębiorstwa, które wymagają oddzielnego zarządzania dla obciążeń w każdej szprychy.

## <a name="preview-the-demo-components"></a>Wyświetlanie podglądu składników demonstracyjnych

Podczas pracy nad każdym samouczka w tej serii, różne składniki są definiowane w różnych skryptów Terraform. Architektura demo utworzona i wdrożona składa się z następujących składników:

- **Sieć lokalna**. Prywatna sieć lokalna działająca z organizacją. W przypadku architektury referencyjnej koncentratora i szprychy sieć wirtualna na platformie Azure jest używana do symulowania sieci lokalnej.

- **Urządzenie sieci VPN**. Urządzenie lub usługa sieci VPN zapewnia łączność zewnętrzną z siecią lokalną. Urządzenie sieci VPN może być urządzeniem sprzętowym lub rozwiązaniem programowym. 

- **Sieć wirtualna będąca piastą**. Koncentrator jest centralnym punktem łączności z siecią lokalną i miejscem hosta usług. Te usługi mogą być używane przez różne obciążenia hostowane w sieciach wirtualnych szprychy.

- **Podsieć bramy**. Bramy sieci wirtualnej są przechowywane w tej samej podsieci.

- **Sieci wirtualne będące szprychami**. Szprychy mogą być używane do izolowania obciążeń w ich własnych sieciach wirtualnych zarządzanych oddzielnie od innych szprych. Każde obciążenie może zawierać wiele warstw z wieloma podsieciami połączonymi za pośrednictwem modułów równoważenia obciążenia platformy Azure. 

- **Komunikacja równorzędna sieci wirtualnej**. Dwie sieci wirtualne można połączyć za pomocą połączenia równorzędnego. Połączenia komunikacji równorzędnej to nieprzechodnie połączenia między sieciami wirtualnymi o niskich opóźnieniach. Po wznieszonej sieci wirtualnej wymiany ruchu przy użyciu szkieletu platformy Azure, bez konieczności routera. W topologii sieci koncentratora i szprychy komunikacja równorzędna sieci wirtualnej jest używana do łączenia koncentratora z każdą szprychą. Sieci wirtualne można równorzędne w tym samym regionie lub w różnych regionach.

## <a name="create-the-directory-structure"></a>Tworzenie struktury katalogów

Utwórz katalog zawierający pliki konfiguracyjne Terraform dla wersji demonstracyjnej.

1. Przejdź do [witryny Azure portal](https://portal.azure.com).

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

1. W usłudze Cloud Shell `main.tf`otwórz nowy plik o nazwie .

    ```bash
    code main.tf
    ```

1. Wklej następujący kod do edytora:

    ```hcl
    provider "azurerm" {
        version = "~>1.22"
    }
    ```

1. Zapisz plik i zamknij edytor.

## <a name="create-the-variables-file"></a>Tworzenie pliku zmiennych

Utwórz plik konfiguracyjny Terraform dla typowych zmiennych, które są używane w różnych skryptach.

1. W usłudze Cloud Shell `variables.tf`otwórz nowy plik o nazwie .

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

1. Zapisz plik i zamknij edytor.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"] 
> [Tworzenie lokalnej sieci wirtualnej z terraformem na platformie Azure](./terraform-hub-spoke-on-prem.md)