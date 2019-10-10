---
title: Szybki Start — Wdrażanie szablonu rozwiązania rozwiązania ansible dla platformy Azure do usługi CentOS
description: W tym przewodniku szybki start dowiesz się, jak wdrożyć szablon rozwiązania rozwiązania ansible na maszynie wirtualnej CentOS hostowanej na platformie Azure wraz z narzędziami skonfigurowanymi do pracy z platformą Azure.
keywords: ansible, azure, devops, szablon rozwiązania, maszyna wirtualna, tożsamości zarządzane dla zasobów platformy azure, centos, red hat
ms.topic: quickstart
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: 5ababe9eb1f680378e882970df2d0b008287a7c4
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/10/2019
ms.locfileid: "72241712"
---
# <a name="quickstart-deploy-the-ansible-solution-template-for-azure-to-centos"></a>Szybki Start: Wdrażanie szablonu rozwiązania rozwiązania ansible dla platformy Azure w usłudze CentOS

Szablon rozwiązania Ansible platformy Azure jest zaprojektowany tak, aby umożliwić skonfigurowanie wystąpienia rozwiązania Ansible na maszynie wirtualnej z systemem CentOS wraz z rozwiązaniem Ansible oraz zestawem narzędzi skonfigurowanych pod kątem pracy na platformie Azure. Narzędzia te zawierają następujące składniki:

- **Moduły rozwiązania Ansible dla platformy Azure** — [moduły rozwiązania Ansible dla platformy Azure](./ansible-matrix.md) to zestaw modułów, który umożliwia utworzenie infrastruktury na platformie Azure i zarządzanie nią. Domyślnie wdrażana jest najnowsza wersja tych modułów. Jednak w trakcie procesu wdrożenia z użyciem szablonu rozwiązania możesz określić numer wersji, która jest odpowiednia dla Twojego środowiska.
- **Interfejs wiersza polecenia platformy Azure 2.0** — [interfejs wiersza polecenia platformy Azure 2.0](/cli/azure/?view=azure-cli-latest) to wieloplatformowe środowisko wiersza polecenia do zarządzania zasobami platformy Azure. 
- **Tożsamości zarządzane dla zasobów platformy Azure** — funkcja [tożsamości zarządzane dla zasobów platformy Azure](/azure/active-directory/managed-identities-azure-resources/overview) rozwiązuje problem bezpiecznego przechowywania poświadczeń aplikacji w chmurze.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]

## <a name="deploy-the-ansible-solution-template"></a>Wdróż szablon rozwiązania rozwiązania ansible

1. Przejdź do [szablonu rozwiązania Ansible w witrynie Azure Marketplace](https://azuremarketplace.microsoft.com/en-%20%20us/marketplace/apps/azure-oss.ansible?tab=Overview).

1. Wybierz pozycję **POBIERZ TERAZ**.

1. Zostanie wyświetlone okno, w którym są wyszczególnione warunki użytkowania, zasady ochrony prywatności oraz zasady korzystania z witryny Azure Marketplace. Wybierz przycisk **Kontynuuj**.

1. Zostanie wyświetlona witryna Azure Portal oraz strona rozwiązania Ansible, na której opisano szablon rozwiązania. Wybierz pozycję **Utwórz**.

1. Na stronie **Tworzenie rozwiązania Ansible** zostanie wyświetlonych kilka kart. Na karcie **Informacje podstawowe** wprowadź wymagane informacje:

   - **Nazwa** — określ nazwę wystąpienia rozwiązania Ansible. Dla celów demonstracyjnych użyto nazwy `ansiblehost`.
   - **Nazwa użytkownika** — określ nazwę użytkownika, który będzie mieć dostęp do wystąpienia rozwiązania Ansible. Dla celów demonstracyjnych użyto nazwy `ansibleuser`.
   - **Typ uwierzytelniania** — wybierz opcję **Hasło** lub **Klucz publiczny SSH**. Dla celów demonstracyjnych wybrano opcję **Klucz publiczny SSH**.
   - **Hasło** i **Potwierdź hasło** — jeśli wybrano opcję **Hasło** jako **typ uwierzytelniania**, wprowadź hasło dla tych wartości.
   - **Klucz publiczny SSH** — jeśli wybrano opcję **Klucz publiczny SSH** jako **typ uwierzytelniania**, wprowadź swój klucz publiczny RSA w formacie jednowierszowym — rozpoczynając od `ssh-rsa`.
   - **Subskrypcja** — wybierz z listy rozwijanej subskrypcję platformy Azure.
   - **Grupa zasobów** — wybierz istniejącą grupę zasobów z listy rozwijanej lub wybierz pozycję **Utwórz nową** i określ nazwę nowej grupy zasobów. Dla celów demonstracyjnych użyto nazwy `ansiblerg` dla nowej grupy zasobów.
   - **Lokalizacja** — wybierz z listy rozwijanej lokalizację, która jest odpowiednia dla Twojego scenariusza.

     ![Karta podstawowych ustawień rozwiązania Ansible w witrynie Azure Portal](./media/ansible-quick-deploy-solution-template/portal-ansible-setup-tab-1.png)

1. Kliknij przycisk **OK**.

1. Na karcie **Ustawienia dodatkowe** wprowadź wymagane informacje:

   - **Rozmiar** — w witrynie Azure Portal domyślnie stosowany jest rozmiar standardowy. Aby określić inny rozmiar, który odpowiada potrzebom konkretnego scenariusza,wybierz ikonę strzałki i wybierz rozmiar z listy.
   - **Typ dysku maszyny wirtualnej** — wybierz opcję **SSD** (Premium Solid-State Drive) lub **HDD** (Hard Disk Drive). Dla celów demonstracyjnych został wybrany dysk **SSD** z uwagi na lepszą wydajność. Aby uzyskać więcej informacji na temat rodzajów dysków, zobacz następujące artykuły:
       - [Magazyn Premium Storage o wysokiej wydajności i dyski zarządzane dla maszyn wirtualnych](/azure/virtual-machines/windows/premium-storage)
       - [Standard SSD Managed Disks for Azure Virtual machine workloads (Dyski zarządzane SSD w warstwie Standardowa dla obciążeń maszyny wirtualnej na platformie Azure)](/azure/virtual-machines/windows/disks-standard-ssd)
   - **Publiczny adres IP** – skonfiguruj to ustawienie, jeśli chcesz nawiązać komunikację z maszyną wirtualną spoza maszyny wirtualnej. Opcją domyślną jest nowy publiczny adres IP o nazwie `ansible-pip`. Aby określić inny adres IP, wybierz ikonę strzałki i określ atrybuty, takie jak nazwa, SKU oraz Przypisanie dla tego adresu IP. 
   - **Etykieta nazwy domeny** — wprowadź nazwę domeny publicznej maszyny wirtualnej. Nazwa musi być unikatowa i odpowiadać wymaganiom dotyczącym nazewnictwa. Aby uzyskać więcej informacji na temat nazw maszyn wirtualnych, zobacz [Naming conventions for Azure resources (Konwencja nazewnictwa dla zasobów platformy Azure)](/azure/architecture/best-practices/naming-conventions).
   - **Wersja rozwiązania Ansible** — określ numer wersji lub wprowadź wartość `latest`, aby wdrożyć najnowszą wersję. Wybierz ikonę informacji obok pola **Wersja rozwiązania Ansible**, aby wyświetlić więcej informacji na temat dostępnych wersji.

     ![Karta dodatkowych ustawień rozwiązania Ansible w witrynie Azure Portal](./media/ansible-quick-deploy-solution-template/portal-ansible-setup-tab-2.png)

1. Kliknij przycisk **OK**.

1. Na karcie **Ustawienia integracji rozwiązania Ansible** określ typ uwierzytelniania. Aby uzyskać więcej informacji na temat zabezpieczania zasobów platformy Azure, zobacz [Czym są tożsamości zarządzane dla zasobów platformy Azure?](/azure/active-directory/managed-identities-azure-resources/overview).

    ![Karta w ustawień integracji rozwiązania Ansible witrynie Azure Portal](./media/ansible-quick-deploy-solution-template/portal-ansible-setup-tab-3.png)

1. Kliknij przycisk **OK**.

1. Na stronie **Podsumowanie** jest wyświetlony proces weryfikacji oraz lista określonych kryteriów dotyczących wdrożenia rozwiązania Ansible. Link u dołu karty umożliwia **pobranie szablonu i parametrów**, których można używać z obsługiwanymi językami i platformami na platformie Azure. 

     ![Karta Podsumowanie rozwiązania Ansible w witrynie Azure Portal](./media/ansible-quick-deploy-solution-template/portal-ansible-setup-tab-4.png)

1. Kliknij przycisk **OK**.

1. Gdy pojawi się karta **Utwórz**, wybierz pozycję **OK**, aby wdrożyć rozwiązanie Ansible.

1. Wybierz ikonę **Powiadomienia** w górnej części strony portalu, aby śledzić proces wdrażania rozwiązania Ansible. Po zakończeniu wdrażania wybierz pozycję **Przejdź do grupy zasobów**. 

     ![Karta Podsumowanie rozwiązania Ansible w witrynie Azure Portal](./media/ansible-quick-deploy-solution-template/portal-ansible-setup-complete.png)

1. Na stronie grupy zasobów sprawdź adres IP Twojego hosta rozwiązania Ansible i zaloguj się, aby zarządzać zasobami platformy Azure przy użyciu rozwiązania Ansible.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"] 
> [Szybki Start: Konfigurowanie maszyny wirtualnej z systemem Linux na platformie Azure przy użyciu rozwiązania ansible](/azure/virtual-machines/linux/ansible-create-vm)