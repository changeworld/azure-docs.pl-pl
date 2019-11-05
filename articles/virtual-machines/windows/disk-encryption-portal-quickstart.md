---
title: Tworzenie i szyfrowanie maszyny wirtualnej z systemem Windows przy użyciu Azure Portal
description: W tym przewodniku szybki start dowiesz się, jak za pomocą Azure Portal utworzyć i zaszyfrować maszynę wirtualną z systemem Windows.
author: msmbaldwin
ms.author: mbaldwin
ms.service: security
ms.topic: quickstart
ms.date: 10/02/2019
ms.openlocfilehash: 2590b9851b654990795f111e76f6cc5ea6c96723
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73491952"
---
# <a name="quickstart-create-and-encrypt-a-windows-virtual-machine-with-the-azure-portal"></a>Szybki Start: Tworzenie i szyfrowanie maszyny wirtualnej z systemem Windows przy użyciu Azure Portal

Maszyny wirtualne platformy Azure można utworzyć za pomocą witryny Azure Portal. Witryna Azure Portal to oparty na przeglądarce interfejs użytkownika umożliwiający tworzenie maszyn wirtualnych i powiązanych z nimi zasobów. W tym przewodniku szybki start użyjesz Azure Portal, aby wdrożyć maszynę wirtualną z systemem Windows w systemie Ubuntu 18,04 LTS, utworzyć magazyn kluczy na potrzeby przechowywania kluczy szyfrowania i zaszyfrować maszynę wirtualną.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-azure"></a>Zaloguj się w usłudze Azure

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

## <a name="create-a-key-vault"></a>Tworzenie magazynu kluczy

1. Wybierz opcję **Utwórz zasób** w lewym górnym rogu witryny Azure Portal
1. W polu wyszukiwania wpisz **Key Vault**.
1. Z listy wyników wybierz pozycję **Key Vault**.
1. W sekcji Key Vault wybierz pozycję **Utwórz**.
1. Na ekranie **Tworzenie magazynu kluczy** wybierz unikatową nazwę nowego magazynu kluczy.

    > [!Important]
    > Każdy Key Vault musi mieć unikatową nazwę. Poniższy przykład tworzy Key Vault o nazwie *myADEKV*, ale należy nazwać coś innego.

1. Wybierz **subskrypcję**.
1.  W obszarze **Grupa zasobów**wybierz pozycję **Utwórz nową**. W wyskakującym okienku wpisz wartość *MyResourceGroup* w polu nazwy grupy zasobów, a następnie wybierz przycisk **OK**. 

    ![Ekran tworzenia grupy zasobów](../media/disk-encryption/portal-qs-keyvaultcreation.png)

1. W menu rozwijanym **Lokalizacja** wybierz pozycję **Wschodnie stany USA**.
1. Dla pozostałych opcji zostaw wartości domyślne.
1. Wybierz pozycję "zasady dostępu", co spowoduje przejście do nowego ekranu.
1. Zaznacz pole wyboru obok pozycji "Włącz dostęp do Azure Disk Encryption szyfrowania woluminów.

    ![Ekran tworzenia z przetworzeniem zasobów](../media/disk-encryption/portal-qs-keyvault-enable-encryption.png)

1. W dolnej części ekranu zasady dostępu kliknij pozycję "Przejrzyj + Utwórz".
1. Po przejrzeniu kliknij pozycję "Utwórz".

## <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej

1. Wybierz pozycję **Utwórz zasób** w lewym górnym rogu okna witryny Azure Portal.

1. Na stronie nowy w obszarze popularne wybierz pozycję **Windows Server 2016 Datacenter**.
1. Na karcie **podstawy** w obszarze **szczegóły projektu**upewnij się, że wybrano poprawną subskrypcję.
1. W obszarze **Grupa zasobów**wybierz grupę zasobów, która została utworzona podczas tworzenia magazynu kluczy powyżej (np. Grupa **zasobów).**
1. W obszarze **Nazwa maszyny wirtualnej**wprowadź *MyVM*.
1. W polu **region**wybierz ten sam region, który został użyty podczas tworzenia magazynu kluczy (np. **Wschodnie stany USA**).
1. Upewnij się, że **rozmiar** jest *Standardowy D2s v3*.
1. W obszarze **konto administratora**wybierz pozycję **hasło**. Wprowadź nazwę użytkownika i hasło.
    ![ekranu tworzenia z przetworzeniem zasobów](../media/disk-encryption/portal-qs-windows-vm-creation.png)
1. Wybierz kartę Zarządzanie i sprawdź, czy masz konto magazynu diagnostyki. Jeśli nie masz żadnych kont magazynu, wybierz pozycję "Utwórz nowe", Nadaj nowemu kontu nazwę, a następnie wybierz przycisk "OK" ![ekranu tworzenia podsieci](../media/disk-encryption/portal-qs-vm-creation-storage.png)
1. Kliknij pozycję "Przejrzyj + Utwórz".
1. Na stronie **Tworzenie maszyny wirtualnej** wyświetlone są szczegółowe informacje o maszynie wirtualnej, którą masz zamiar utworzyć. Gdy wszystko będzie gotowe, wybierz pozycję **Utwórz**.

Wdrożenie maszyny wirtualnej potrwa kilka minut. Po zakończeniu wdrażania przejdź do następnej sekcji.

## <a name="encrypt-the-virtual-machine"></a>Szyfruj maszynę wirtualną

1. Po zakończeniu wdrożenia maszyny wirtualnej wybierz pozycję **Przejdź do zasobu**.
1. Na pasku bocznym po lewej stronie wybierz pozycję **dyski**.
1. Na ekranie dyski wybierz pozycję **szyfrowanie**. 

    ![wybór dysków i szyfrowania](../media/disk-encryption/portal-qs-disks-to-encryption.png)

1. Na ekranie szyfrowanie w obszarze **dyski do zaszyfrowania**wybierz opcję **dyski systemu operacyjnego i danych**.
1. W obszarze **Ustawienia szyfrowania**kliknij pozycję "Wybierz magazyn kluczy i klucz do szyfrowania".
1. Na pasku bocznym po prawej stronie wybierz nazwę magazynu kluczy utworzonego wcześniej jako wartość dla **magazynu kluczy**, a następnie kliknij pozycję **Wybierz**.

    ![wybór dysków i szyfrowania](../media/disk-encryption/portal-qs-encrypt-vm-screen.png)
1. W górnej części ekranu szyfrowania kliknij pozycję "Zapisz". W oknie podręcznym zostanie wyświetlone ostrzeżenie o ponownym uruchomieniu maszyny wirtualnej. Kliknij przycisk **Yes** (Tak).


## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy grupa zasobów, maszyna wirtualna i wszystkie pokrewne zasoby nie będą już potrzebne, można je usunąć. W tym celu wybierz grupę zasobów dla maszyny wirtualnej, wybierz pozycję Usuń, a następnie Potwierdź nazwę grupy zasobów do usunięcia.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono Key Vault, które zostały włączone dla kluczy szyfrowania, utworzono maszynę wirtualną i włączono maszynę wirtualną w celu szyfrowania.  

> [!div class="nextstepaction"]
> [Przegląd Azure Disk Encryption](disk-encryption-overview.md)
