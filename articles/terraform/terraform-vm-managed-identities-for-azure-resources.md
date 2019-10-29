---
title: Samouczek — Tworzenie maszyny wirtualnej z systemem Linux przy użyciu tożsamości zarządzanej z obrazu portalu Azure Marketplace przy użyciu Terraform
description: Tworzenie maszyny wirtualnej z systemem Terraform Linux z zarządzaną tożsamością i zdalne zarządzaniem stanem przy użyciu obrazu portalu Azure Marketplace
ms.service: terraform
author: tomarchermsft
ms.author: tarcher
ms.topic: tutorial
ms.date: 10/26/2019
ms.openlocfilehash: e00b674df35516da559339af8028c1ca1845b0db
ms.sourcegitcommit: b1c94635078a53eb558d0eb276a5faca1020f835
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2019
ms.locfileid: "72969791"
---
# <a name="tutorial-create-a-linux-vm-with-a-managed-identity-from-the-azure-marketplace-image-using-terraform"></a>Samouczek: Tworzenie maszyny wirtualnej z systemem Linux przy użyciu tożsamości zarządzanej z obrazu portalu Azure Marketplace przy użyciu Terraform

W tym artykule pokazano, jak użyć [obrazu programu Terraform z witryny Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/azure-oss.terraform?tab=Overview) do utworzenia maszyny wirtualnej z systemem Ubuntu Linux (16.04 LTS) przy użyciu najnowszej wersji programu [Terraform](https://www.terraform.io/intro/index.html) zainstalowanej i skonfigurowanej za pomocą [tożsamości zarządzanych dla zasobów platformy Azure](/azure/active-directory/managed-service-identity/overview). Ten obraz konfiguruje również zdalne zaplecze umożliwiające zarządzanie [stanem zdalnym](https://www.terraform.io/docs/state/remote.html) przy użyciu programu Terraform. 

Obraz programu Terraform z witryny Marketplace ułatwia rozpoczęcie pracy z programem Terraform na platformie Azure bez konieczności ręcznego instalowania i konfigurowania programu Terraform. 

Za obraz maszyny wirtualnej programu Terraform nie są naliczane opłaty za oprogramowanie. Opłaty za użycie sprzętu platformy Azure są opłacane na podstawie rozmiaru maszyny wirtualnej, która została zainicjowana. 

Więcej informacji na temat opłat za obliczenia można znaleźć na [stronie cennika maszyny wirtualnej systemu Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

## <a name="prerequisites"></a>Wymagania wstępne
Aby można było utworzyć maszynę wirtualną z systemem Linux Terraform, musisz mieć subskrypcję platformy Azure. Jeśli nie masz jeszcze subskrypcji, zobacz stronę [Utwórz bezpłatne konto platformy Azure już dzisiaj](https://azure.microsoft.com/free/).  

## <a name="create-your-terraform-vm"></a>Tworzenie maszyny wirtualnej Terraform 

Poniżej przedstawiono kroki, które należy wykonać, aby utworzyć wystąpienie maszyny wirtualnej z systemem Linux Terraform: 

1. W witrynie Azure Portal przejdź do listy [Utwórz zasób](https://ms.portal.azure.com/#create/hub).

1. Użyj paska wyszukiwania **Przeszukaj witrynę Marketplace**, aby znaleźć program **Terraform**. 

1. Wybierz pozycję **Utwórz**. 

1. Poniższe sekcje zawierają dane wejściowe dla każdego z kroków kreatora w celu utworzenia maszyny wirtualnej z systemem Linux Terraform. W poniższej sekcji przedstawiono dane wejściowe wymagane do skonfigurowania każdego z tych kroków.

## <a name="details-on-the-create-terraform-tab"></a>Szczegółowe informacje na karcie Tworzenie programu Terraform

Podaj następujące szczegóły na karcie **Tworzenie programu Terraform**:

1. **Podstawy**
    
   * **Name**: Nazwa maszyny wirtualnej Terraform.
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

   * Aby rozpocząć proces aprowizowania, wybierz pozycję **Kup**. Dostępny jest link do warunków transakcji. Na maszynie wirtualnej nie są naliczane dodatkowe opłaty wykraczające poza obliczenia dla rozmiaru serwera wybranego w kroku size.

Obraz maszyny wirtualnej Terraform wykonuje następujące czynności:

* Tworzy maszynę wirtualną z tożsamością przypisaną przez system opartą na obrazie systemu Ubuntu 16.04 LTS.
* Program instaluje zarządzane tożsamości dla rozszerzenia Azure resources na maszynie wirtualnej, aby umożliwić wydawanie tokenów OAuth dla zasobów platformy Azure.
* Przypisuje uprawnienia RBAC do tożsamości zarządzanej, co powoduje udzielenie praw dostępu właściciela do grupy zasobów.
* Tworzy folder szablonu programu Terraform (tfTemplate).
* Konfiguruje wstępnie stan zdalny programu Terraform za pomocą zaplecza platformy Azure.

## <a name="access-and-configure-a-linux-terraform-vm"></a>Dostęp i Konfigurowanie maszyny wirtualnej z systemem Linux Terraform

Po utworzeniu maszyny wirtualnej wykonaj następujące czynności:

1. Zaloguj się do maszyny wirtualnej przy użyciu protokołu SSH. Użyj poświadczeń konta utworzonych w poprzedniej sekcji. W systemie Windows możesz pobrać narzędzia klienta SSH, takie jak program [Putty](https://www.putty.org/).

1. Przyznaj uprawnienia współautora dla całej subskrypcji tożsamościom zarządzanym dla zasobów platformy Azure na maszynie wirtualnej. 

    Uprawnienie współautor ułatwia zarządzanie tożsamościami zasobów platformy Azure na maszynie wirtualnej w celu używania Terraform do tworzenia zasobów poza grupą zasobów maszyny wirtualnej. Wykonaj tę akcję, uruchamiając następujący skrypt: 
    
    ```bash
    . ~/tfEnv.sh
    ```

    Ten skrypt używa [interaktywnego mechanizmu logowania interfejsu wiersza polecenia platformy Azure](/cli/azure/authenticate-azure-cli?view=azure-cli-latest#sign-in-interactively) do uwierzytelniania za pomocą platformy Azure. Ten proces przypisuje [uprawnienia współautora tożsamości zarządzanej](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) dla całej subskrypcji. 

1. Maszyna wirtualna ma zaplecze stanu zdalnego programu Terraform. Aby włączyć to wdrożenie Terraform, należy skopiować plik `remoteState.tf` do katalogu głównego skryptów Terraform.

    ```bash
    cp  ~/tfTemplate/remoteState.tf .
    ```

    Aby uzyskać więcej informacji na temat zdalnego zarządzania stanami, zobacz [Terraform Remote](https://www.terraform.io/docs/state/remote.html)State. Klucz dostępu do magazynu jest udostępniany w tym pliku. Wyklucz go przed zatwierdzeniem plików konfiguracji Terraform do kontroli źródła.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"] 
> [Terraform na platformie Azure](/azure/ansible/)