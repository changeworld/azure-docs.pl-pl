---
title: Użyj obrazu portalu Azure Marketplace do utworzenia maszyny wirtualnej systemu Terraform Linux zarządzane tożsamości usługi
description: Aby utworzyć maszyny wirtualnej systemu Terraform Linux z zarządzanych tożsamość usługi i zdalne zarządzanie stanem łatwe wdrażanie zasobów na platformie Azure, Użyj obrazu z witryny Marketplace.
keywords: terraform, devops, MSI, virtual machine, remote state, azure
author: VaijanathB
manager: rloutlaw
ms.author: tarcher
ms.date: 3/12/2018
ms.topic: article
ms.openlocfilehash: db45e9fe1eb724e6404f5e83bbbe4f62ee32343d
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
# <a name="use-an-azure-marketplace-image-to-create-a-terraform-linux-virtual-machine-with-managed-service-identity"></a>Użyj obrazu portalu Azure Marketplace do utworzenia maszyny wirtualnej systemu Terraform Linux zarządzane tożsamości usługi

W tym artykule przedstawiono sposób użycia [obrazu z witryny Terraform Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/azure-oss.terraform?tab=Overview) do utworzenia maszyny Wirtualnej systemu Ubuntu Linux (16.04 LTS) przy użyciu najnowszych [Terraform](https://www.terraform.io/intro/index.html) wersji zainstalowany i skonfigurowany za pomocą [zarządzane Usługa tożsamości (MSI)](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview). Ten obraz konfiguruje również zdalnego zaplecze umożliwiające [stan zdalnego](https://www.terraform.io/docs/state/remote.html) zarządzania przy użyciu Terraform. 

Obrazu witryny Terraform Marketplace ułatwia rozpoczęcie pracy z Terraform na platformie Azure, bez konieczności instalowania i konfigurowania Terraform ręcznie. 

Nie ma żadnych opłat oprogramowania dla tego obrazu Terraform maszyny Wirtualnej. Płaci się tylko opłaty użycia Azure sprzętu, które są oceniane na podstawie rozmiaru maszyny wirtualnej, która zostanie zainicjowana. Aby uzyskać więcej informacji o opłatach obliczeń, zobacz [cennik maszyn wirtualnych systemu Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

## <a name="prerequisites"></a>Wymagania wstępne
Przed utworzeniem maszyny wirtualnej systemu Linux Terraform musi mieć subskrypcję platformy Azure. Jeśli nie masz jeszcze jeden, zobacz [utworzyć bezpłatne konto platformy Azure obecnie](https://azure.microsoft.com/free/).  

## <a name="create-your-terraform-virtual-machine"></a>Tworzenie maszyny wirtualnej Terraform 

Poniżej przedstawiono kroki, aby utworzyć wystąpienie maszyny wirtualnej systemu Linux Terraform: 

1. W portalu Azure, przejdź do [Utwórz zasób](https://ms.portal.azure.com/#create/hub) wyświetlania.

2. W **wyszukiwania portalu Marketplace** pasek wyszukiwania, wyszukiwanie **Terraform**. Wybierz **Terraform** szablonu. 

3. Na karcie Szczegóły Terraform w prawej dolnej wybierz **Utwórz** przycisku.

    ![Utwórz maszynę wirtualną Terraform](media\terraformmsi.png)

4. Poniższe sekcje zawierają dane wejściowe dla poszczególne kroki w kreatorze, aby utworzyć maszynę wirtualną systemu Terraform Linux. W poniższej sekcji przedstawiono wejść, które są potrzebne do skonfigurowania każdego z tych kroków.

## <a name="details-on-the-create-terraform-tab"></a>Szczegółowe informacje o karcie Tworzenie Terraform

Wprowadź następujące informacje szczegółowe **utworzyć Terraform** karty:

1. **Podstawy**
    
   * **Nazwa**: Nazwa Terraform maszyny wirtualnej.
   * **Nazwa użytkownika**: pierwsze konto logowania identyfikatora.
   * **Hasło**: pierwszy hasło do konta. (Zamiast hasła można użyć klucza publicznego SSH).
   * **Subskrypcja**: subskrypcji, na którym maszyna ma być utworzony i są rozliczane. Musi mieć uprawnienia do tworzenia zasobów dla tej subskrypcji.
   * **Grupa zasobów**: nowy lub istniejący zasób grupy.
   * **Lokalizacja**: centrum danych, która jest najbardziej odpowiednia. Jest zazwyczaj większość danych lub jeden, zbliżony do fizycznej lokalizacji najszybszy dostęp do sieci centrum danych.

2. **Dodatkowe ustawienia**

   * **Rozmiar**: rozmiar maszyny wirtualnej. 
   * **Typ dysku maszyny Wirtualnej**: dysków SSD i HDD.

3. **Podsumowanie Terraform**

   * Sprawdź, czy wszystkie wprowadzone informacje jest poprawna. 

4. **Buy**

   * Aby rozpocząć proces inicjowania obsługi administracyjnej, wybierz **kupić**. Łącze znajduje się na warunki transakcji. Maszyna wirtualna nie ma żadnych dodatkowych kosztów poza obliczeniowe dla rozmiaru serwera, które wybrano w kroku rozmiar.

Obraz maszyny Wirtualnej Terraform wykonuje następujące czynności:

* Tworzy Maszynę wirtualną z przypisane systemu tożsamości opartego na obrazie Ubuntu 16.04 LTS.
* Instaluje rozszerzenia MSI na maszynie Wirtualnej, aby umożliwić tokenów OAuth zostanie wysłane do zasobów platformy Azure.
* Przypisywanie uprawnień RBAC tożsamości zarządzanych, przyznanie praw właściciela grupy zasobów.
* Tworzy folder szablonu Terraform (tfTemplate).
* Wstępnie konfiguruje Terraform stan zdalnego przy użyciu zwrotnego Azure zakończenia.

## <a name="access-and-configure-a-linux-terraform-virtual-machine"></a>Dostęp i konfigurowanie maszyny wirtualnej systemu Linux Terraform

Po utworzeniu maszyny Wirtualnej można logowania się do go przy użyciu protokołu SSH. Korzystać z poświadczeń konta, które zostały utworzone w sekcji "Podstawowe" krok 3 dla interfejsu powłoki tekstu. W systemie Windows, możesz pobrać narzędzie klienta SSH, takie jak [Putty](http://www.putty.org/).

Połącz z maszyną wirtualną przy użyciu protokołu SSH, należy przyznać uprawnienia współautora dla całej subskrypcji do zarządzanego tożsamości usługi na maszynie wirtualnej. 

Uprawnienia współautora pomaga MSI w maszynie Wirtualnej, aby Terraform umożliwiają utworzenie zasobów spoza grupy zasobów maszyny Wirtualnej. Ta akcja można łatwo osiągnąć przez uruchomienie skryptu raz. Użyj następującego polecenia:

`. ~/tfEnv.sh`

Poprzedni skrypt używa [AZ CLI v 2.0 interakcyjnego logowania](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest#interactive-log-in) mechanizm uwierzytelniania w usłudze Azure i przypisać maszynie wirtualnej zarządzane tożsamość usługi uprawnienia współautora dla całej subskrypcji. 

 Maszyna wirtualna ma zaplecze stan zdalnego Terraform. Aby ją włączyć w danym wdrożeniu Terraform, skopiuj plik remoteState.tf z katalogu tfTemplate do katalogu głównego Terraform skryptów.  

 `cp  ~/tfTemplate/remoteState.tf .`

 Aby uzyskać więcej informacji na temat zdalnego zarządzania stanem, zobacz [tę stronę o stanie zdalnego Terraform](https://www.terraform.io/docs/state/remote.html). Klucz dostępu do magazynu jest widoczne w tym pliku i musi zostać dokładnie sprawdzony pod kontrolą źródła.  

## <a name="next-steps"></a>Kolejne kroki
W tym artykule przedstawiono sposób konfigurowania maszyny wirtualnej systemu Terraform Linux na platformie Azure. Poniżej przedstawiono dodatkowe zasoby ułatwiające Dowiedz się więcej o Terraform na platformie Azure: 

 [Koncentrator Terraform w domenie Microsoft.com](https://docs.microsoft.com/azure/terraform/)  
 [Dokumentacji dostawcy Terraform platformy Azure](http://aka.ms/terraform)  
 [Źródłowy dostawca Terraform Azure](http://aka.ms/tfgit)  
 [Moduły Terraform Azure](http://aka.ms/tfmodules)
 

















