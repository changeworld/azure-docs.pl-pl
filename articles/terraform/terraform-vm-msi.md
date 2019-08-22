---
title: Użyj obrazu z witryny Azure Marketplace do utworzenia maszyny wirtualnej programu Terraform z systemem Linux przy użyciu tożsamości zarządzanej
description: Użyj obrazu z witryny Marketplace do utworzenia maszyny wirtualnej programu Terraform z systemem Linux przy użyciu tożsamości zarządzanej i zarządzania stanem zdalnym, aby łatwo wdrażać zasoby na platformie Azure.
services: terraform
ms.service: azure
keywords: terraform, devops, tożsamość usługi zarządzanej, maszyna wirtualna, stan zdalny, azure
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 3/12/2018
ms.openlocfilehash: e3aaf943b5eecd9ec1853c09ea36156b98560d3d
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/20/2019
ms.locfileid: "69648705"
---
# <a name="use-an-azure-marketplace-image-to-create-a-terraform-linux-virtual-machine-with-managed-identities-for-azure-resources"></a>Użyj obrazu z witryny Azure Marketplace do utworzenia maszyny wirtualnej programu Terraform z systemem Linux przy użyciu tożsamości zarządzanych dla zasobów platformy Azure

W tym artykule pokazano, jak użyć [obrazu programu Terraform z witryny Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/azure-oss.terraform?tab=Overview) do utworzenia maszyny wirtualnej z systemem Ubuntu Linux (16.04 LTS) przy użyciu najnowszej wersji programu [Terraform](https://www.terraform.io/intro/index.html) zainstalowanej i skonfigurowanej za pomocą [tożsamości zarządzanych dla zasobów platformy Azure](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview). Ten obraz konfiguruje również zdalne zaplecze umożliwiające zarządzanie [stanem zdalnym](https://www.terraform.io/docs/state/remote.html) przy użyciu programu Terraform. 

Obraz programu Terraform z witryny Marketplace ułatwia rozpoczęcie pracy z programem Terraform na platformie Azure bez konieczności ręcznego instalowania i konfigurowania programu Terraform. 

Za obraz maszyny wirtualnej programu Terraform nie są naliczane opłaty za oprogramowanie. Ponosisz opłaty tylko za użycie sprzętu platformy Azure, które są naliczane na podstawie rozmiaru aprowizowanej maszyny wirtualnej. Aby uzyskać więcej informacji na temat opłat za przetwarzanie, zobacz [stronę z cennikiem maszyn wirtualnych z systemem Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

## <a name="prerequisites"></a>Wymagania wstępne
Utworzenie maszyny wirtualnej programu Terraform z systemem Linux wymaga subskrypcji platformy Azure. Jeśli nie masz jeszcze subskrypcji, zobacz stronę [Utwórz bezpłatne konto platformy Azure już dzisiaj](https://azure.microsoft.com/free/).  

## <a name="create-your-terraform-virtual-machine"></a>Tworzenie maszyny wirtualnej programu Terraform 

Poniżej przedstawiono kroki tworzenia wystąpienia maszyny wirtualnej programu Terraform z systemem Linux: 

1. W witrynie Azure Portal przejdź do listy [Utwórz zasób](https://ms.portal.azure.com/#create/hub).

2. Użyj paska wyszukiwania **Przeszukaj witrynę Marketplace**, aby znaleźć program **Terraform**. Wybierz szablon programu **Terraform**. 

3. Na karcie Szczegóły programu Terraform w prawym dolnym rogu wybierz przycisk **Utwórz**.

    ![Tworzenie maszyny wirtualnej programu Terraform](media/terraformmsi.png)

4. Poniższe sekcje zawierają dane wejściowe dla każdego z kroków kreatora na potrzeby tworzenia maszyny wirtualnej programu Terraform z systemem Linux. W poniższej sekcji przedstawiono dane wejściowe wymagane do skonfigurowania każdego z tych kroków.

## <a name="details-on-the-create-terraform-tab"></a>Szczegółowe informacje na karcie Tworzenie programu Terraform

Podaj następujące szczegóły na karcie **Tworzenie programu Terraform**:

1. **Podstawy**
    
   * **Nazwa**: nazwa maszyny wirtualnej programu Terraform.
   * **Nazwa użytkownika**: identyfikator logowania pierwszego konta.
   * **Hasło**: hasło pierwszego konta. (Zamiast hasła można użyć klucza publicznego SSH).
   * **Subskrypcja**: subskrypcja, w ramach której maszyna ma zostać utworzona i być rozliczana. Musisz mieć uprawnienia do tworzenia zasobów dla tej subskrypcji.
   * **Grupa zasobów**: nowa lub istniejąca grupa zasobów.
   * **Lokalizacja**: najbardziej odpowiednie centrum danych. Zazwyczaj jest to centrum danych, które zawiera większość Twoich danych lub które jest najbliżej Twojej lokalizacji fizycznej, co zapewnia najszybszy dostęp do sieci.

2. **Ustawienia dodatkowe**

   * **Rozmiar**: rozmiar maszyny wirtualnej. 
   * **Typ dysku maszyny wirtualnej**: SSD lub HDD.

3. **Podsumowanie programu Terraform**

   * Sprawdź, czy wszystkie podane informacje są poprawne. 

4. **Kup**

   * Aby rozpocząć proces aprowizowania, wybierz pozycję **Kup**. Dostępny jest link do warunków transakcji. Za maszynę wirtualną nie są naliczane żadne dodatkowe opłaty poza opłatą wynikającą z rozmiaru serwera wybranego w kroku wyboru rozmiaru.

Obraz maszyny wirtualnej programu Terraform wykonuje następujące kroki:

* Tworzy maszynę wirtualną z tożsamością przypisaną przez system opartą na obrazie systemu Ubuntu 16.04 LTS.
* Instaluje rozszerzenie tożsamości usługi zarządzanej na maszynie wirtualnej, aby umożliwić wystawianie tokenów OAuth dla zasobów platformy Azure.
* Przypisuje uprawnienia RBAC do tożsamości zarządzanej, co powoduje udzielenie praw dostępu właściciela do grupy zasobów.
* Tworzy folder szablonu programu Terraform (tfTemplate).
* Konfiguruje wstępnie stan zdalny programu Terraform za pomocą zaplecza platformy Azure.

## <a name="access-and-configure-a-linux-terraform-virtual-machine"></a>Konfigurowanie maszyny wirtualnej programu Terraform z systemem Linux i dostęp do niej

Po utworzeniu maszyny wirtualnej możesz zalogować się do niej przy użyciu protokołu SSH. Użyj poświadczeń konta utworzonych w sekcji podstaw kroku 3 dla interfejsu powłoki tekstowej. W systemie Windows możesz pobrać narzędzia klienta SSH, takie jak program [Putty](https://www.putty.org/).

Po połączeniu z maszyną wirtualną przy użyciu protokołu SSH należy nadać uprawnienia współautora do tożsamości zarządzanych dla zasobów platformy Azure na maszynie wirtualnej dla całej subskrypcji. 

Uprawnienia współautora ułatwiają tożsamości usługi zarządzanej na maszynie wirtualnej użycie programu Terraform do tworzenia zasobów poza grupą zasobów maszyny wirtualnej. Tę akcję można łatwo wykonać przez jednokrotne uruchomienie skryptu. Użyj następującego polecenia:

`. ~/tfEnv.sh`

Poprzedni skrypt używa mechanizmu [interaktywnego logowania programu AZ CLI 2.0](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest#sign-in-interactively) do uwierzytelnienia na platformie Azure i przypisania uprawnień współautora tożsamości zarządzanej maszyny wirtualnej dla całej subskrypcji. 

 Maszyna wirtualna ma zaplecze stanu zdalnego programu Terraform. Aby włączyć je we wdrożeniu programu Terraform, skopiuj plik remoteState.tf z katalogu tfTemplate do katalogu głównego skryptów programu Terraform.  

 `cp  ~/tfTemplate/remoteState.tf .`

 Aby uzyskać więcej informacji na temat zarządzania stanem zdalnym, zobacz [tę stronę o stanie zdalnym programu Terraform](https://www.terraform.io/docs/state/remote.html). W tym pliku jest uwidoczniony klucz dostępu magazynu. Należy go wykluczyć przed zaewidencjonowaniem plików konfiguracji programu Terraform w kontroli źródła.

## <a name="next-steps"></a>Następne kroki
W tym artykule przedstawiono sposób konfigurowania maszyny wirtualnej programu Terraform z systemem Linux na platformie Azure. Poniżej przedstawiono kilka dodatkowych zasobów zawierających więcej informacji na temat narzędzia Terraform na platformie Azure: 

 [Centrum narzędzia Terraform w witrynie Microsoft.com](https://docs.microsoft.com/azure/terraform/)  
 [Dokumentacja dostawcy narzędzia Terraform na platformie Azure](https://aka.ms/terraform)  
 [Źródło dostawcy narzędzia Terraform na platformie Azure](https://aka.ms/tfgit)  
 [Moduły narzędzia Terraform na platformie Azure](https://aka.ms/tfmodules)
 

















