---
title: Utwórz koncentrator i topologię sieci hybrydowej za pomocą programu Terraform na platformie Azure typu gwiazda
description: Samouczek pokazujący, jak utworzyć architekturę referencyjną sieci hybrydowej całej platformie Azure przy użyciu programu Terraform
services: terraform
ms.service: azure
keywords: terraform, gwiazdy, sieci, hybrydowej sieci, metodyki devops, maszyny wirtualnej, azure, komunikacja równorzędna sieci wirtualnych, wirtualne urządzenie sieciowe
author: VaijanathB
manager: jeconnoc
ms.author: vaangadi
ms.topic: tutorial
ms.date: 03/01/2019
ms.openlocfilehash: 648369d89bd2b5b08171e1f6f5482c81bfba3c66
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60884725"
---
# <a name="tutorial-create-a-hub-and-spoke-hybrid-network-topology-with-terraform-in-azure"></a>Samouczek: Utwórz koncentrator i topologię sieci hybrydowej za pomocą programu Terraform na platformie Azure typu gwiazda

W tej serii samouczków pokazano, jak wdrożyć na platformie Azure za pomocą narzędzia Terraform [topologię sieci typu gwiazda](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke). 

Topologia gwiazdy jest sposób do izolowania obciążeń, a jednocześnie udostępnianie usług wspólnej. Usługi te obejmują tożsamości i zabezpieczeń. Koncentrator jest sieć wirtualną (VNet), który działa jako punkt centralny połączenia z siecią lokalną. Szprychy to sieci wirtualne komunikujące się równorzędnie z piastą. Usługi udostępnione są wdrażane w piaście, poszczególnych obciążeń są wdrażane w sieciach szprychy.

Ten samouczek obejmuje następujące zadania:

> [!div class="checklist"]
> * Użyj HCL (język HashiCorp) układ gwiazdy hybrydowego odwołanie do architektury zasobów sieciowych
> * Aby utworzyć sieć Centrum zasobów urządzenia za pomocą narzędzia Terraform
> * Program Terraform służy do tworzenia Centrum sieci na platformie Azure, aby pełnić rolę punktu wspólne dla wszystkich zasobów
> * Program Terraform służy do tworzenia poszczególnych obciążeń jako szprychy sieci wirtualnych na platformie Azure
> * Program Terraform służy do ustanawiania bram i połączeń między lokalnie i sieci platformy Azure
> * Aby utworzyć wirtualne sieci równorzędne sieci szprychy za pomocą narzędzia Terraform

## <a name="prerequisites"></a>Wymagania wstępne

- **Subskrypcja platformy Azure**: Jeśli nie masz jeszcze subskrypcji platformy Azure, Utwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) przed przystąpieniem do wykonywania.

- **Instalowanie i konfigurowanie programu Terraform**: Do aprowizowania maszyn wirtualnych i innych infrastruktury na platformie Azure, [Instalowanie i konfigurowanie programu Terraform](/azure/virtual-machines/linux/terraform-install-configure)

## <a name="hub-and-spoke-topology-architecture"></a>Architektura topologii gwiazdy

W topologii gwiazdy Centrum jest sieci wirtualnej. Sieć wirtualna działa jako centralny punkt łączności z siecią lokalną. Szprychy są sieciami wirtualnymi równorzędnymi z piastą i mogą być używane do izolowania obciążeń. Ruch przepływa między lokalnym centrum danych a piastą za pośrednictwem połączenia bramy usługi ExpressRoute lub sieci VPN. Poniższa ilustracja pokazuje składników w topologii gwiazdy:

![Architekturę topologia gwiazdy na platformie Azure](./media/terraform-hub-and-spoke-tutorial-series/hub-spoke-architecture.png)

## <a name="benefits-of-the-hub-and-spoke-topology"></a>Zalety tej topologii gwiazdy

Gwiazda topologii sieci jest sposób do izolowania obciążeń, a jednocześnie udostępnianie usług wspólnej. Usługi te obejmują tożsamości i zabezpieczeń. Piasta to sieć wirtualną, która działa jako punkt centralny połączenia z siecią lokalną. Szprychy to sieci wirtualne komunikujące się równorzędnie z piastą. Usługi udostępnione są wdrażane w piaście, poszczególnych obciążeń są wdrażane w sieciach szprychy. Oto niektóre korzyści w topologii gwiazdy sieci:

- **Oszczędności kosztów** dzięki centralizacji usług, w obrębie jednej lokalizacji, która może być współużytkowana przez wiele obciążeń. Te obciążenia obejmują wirtualne urządzenia sieciowe i serwery DNS.
- **Przezwyciężenie limitów subskrypcji** dzięki wprowadzeniu komunikacji równorzędnej sieci wirtualnych z różnych subskrypcji do centrum.
- **Separację problemów** między centralnym działem IT (SecOP, InfraOps) i obciążeniami (DevOps).

## <a name="typical-uses-for-the-hub-and-spoke-architecture"></a>Typowe zastosowania architektury gwiazdy

Typowe zastosowania architekturę gwiazdy, należą:

- Wielu klientów ma obciążenia, które są wdrożone w różnych środowiskach. Te środowiska obejmują programowania, testowania i produkcji. Wiele razy te obciążenia muszą udostępniać usług, takich jak DNS, Identyfikatory, NTP lub AD DS. Tych udostępnionych usług można umieścić w sieci wirtualnej serwera centralnego. Dzięki temu każde środowisko jest wdrażane jako szprycha w celu zachowania izolacji.
- Obciążenia, które nie wymagają połączenia ze sobą, ale wymagają dostępu do usług udostępnionych.
- Przedsiębiorstwa, które wymagają centralnej kontroli nad aspektami zabezpieczeń.
- Przedsiębiorstwa, które wymagają segregowany zarządzania obciążeniami w każdej szprysze.

## <a name="preview-the-demo-components"></a>Składniki pokaz w wersji zapoznawczej

Podczas pracy za pośrednictwem każdy samouczek z tej serii, różne składniki są definiowane w odrębne skrypty narzędzia Terraform. Architektura pokaz, utworzeniu i wdrożeniu obejmuje następujące składniki:

- **Sieć lokalna**. Prywatna sieć lokalna z organizacji. Gwiazda architektury referencyjnej sieć wirtualną na platformie Azure jest używany do symulowania między siecią lokalną.

- **Urządzenie sieci VPN**. Urządzenie sieci VPN lub usługi zapewnia łączność zewnętrzna do sieci lokalnej. Urządzenie sieci VPN może być urządzenie sprzętowe lub rozwiązaniem programowym. 

- **Sieć wirtualna będąca piastą**. Piasta to centralny punkt łączności z siecią lokalną i miejsce hostowania usług. Te usługi mogą być używane przez różne obciążenia hostowane w sieci wirtualne będące szprychami.

- **Podsieć bramy**. Bramy sieci wirtualnej są przechowywane w tej samej podsieci.

- **Sieci wirtualne będące szprychami**. Szprychy mogą być używane do izolowania obciążeń w ich własnych sieciach wirtualnych zarządzanych oddzielnie od innych szprych. Każde obciążenie może zawierać wiele warstw z wieloma podsieciami połączonymi za pośrednictwem modułów równoważenia obciążenia platformy Azure. 

- **Komunikacja równorzędna sieci wirtualnych**. Dwie sieci wirtualne mogą być połączone za pomocą połączenia komunikacji równorzędnej. Połączenia komunikacji równorzędnej to nieprzechodnie połączenia między sieciami wirtualnymi o niskich opóźnieniach. Po nawiązaniu komunikacji równorzędnej, sieci wirtualne wymieniają ruch przy użyciu sieci szkieletowej platformy Azure, bez konieczności używania routera. W topologii sieci gwiazdy komunikacja równorzędna sieci wirtualnych jest używana do łączenia piasty z każdą szprychą. Można nawiązać komunikację równorzędną między sieciami wirtualnymi w tym samym regionie lub w różnych regionach.

## <a name="create-the-directory-structure"></a>Tworzenie struktury katalogów

Utwórz katalog, który zawiera pliki konfiguracji narzędzia Terraform w pokazie.

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

1. W usłudze Cloud Shell, otwórz nowy plik o nazwie `main.tf`.

    ```bash
    code main.tf
    ```

1. Wklej następujący kod do edytora:

    ```JSON
    provider "azurerm" {
        version = "~>1.22"
    }
    ```

1. Zapisz plik i zamknij Edytor.

## <a name="create-the-variables-file"></a>Utwórz plik zmiennych

Utwórz plik konfiguracji programu Terraform dla wspólnych zmiennych, które są używane w różnych skryptów.

1. W usłudze Cloud Shell, otwórz nowy plik o nazwie `variables.tf`.

    ```bash
    code variables.tf
    ```

1. Wklej następujący kod do edytora:

    ```JSON
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

1. Zapisz plik i zamknij Edytor.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"] 
> [Tworzenie sieci wirtualnej w środowisku lokalnym za pomocą programu Terraform na platformie Azure](./terraform-hub-spoke-on-prem.md)
