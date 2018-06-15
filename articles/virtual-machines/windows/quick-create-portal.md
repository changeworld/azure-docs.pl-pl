---
title: Szybki start — tworzenie maszyny wirtualnej z systemem Windows w witrynie Azure Portal | Microsoft Docs
description: Z tego przewodnika Szybki start dowiesz się, jak utworzyć maszynę wirtualną z systemem Windows w witrynie Azure Portal
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/09/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: c28686c3b6494a0cf8938d39ab9b8338de7aa0c1
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
ms.locfileid: "34012584"
---
# <a name="quickstart-create-a-windows-virtual-machine-in-the-azure-portal"></a>Szybki start: tworzenie maszyny wirtualnej z systemem Windows w witrynie Azure Portal

Maszyny wirtualne platformy Azure można utworzyć za pomocą witryny Azure Portal. Ta metoda bazuje na opartym na przeglądarce interfejsie użytkownika umożliwiającym tworzenie maszyn wirtualnych i powiązanych z nimi zasobów. Z tego przewodnika Szybki start dowiesz się, jak za pomocą witryny Azure Portal wdrożyć maszynę wirtualną platformy Azure z systemem Windows Server 2016. Aby zobaczyć działanie maszyny wirtualnej, połączysz się z nią za pomocą protokołu RDP i zainstalujesz na niej serwer sieci Web usług IIS.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure.

Zaloguj się do witryny Azure Portal na stronie https://portal.azure.com.

## <a name="create-virtual-machine"></a>Tworzenie maszyny wirtualnej

1. W lewym górnym rogu witryny Azure Portal wybierz pozycję **Utwórz zasób**.

2. W polu wyszukiwania nad listą zasobów Microsoft Azure Marketplace wpisz **Windows Server 2016 Datacenter**, wybierz odpowiednią pozycję i kliknij **Utwórz**.

3. Podaj nazwę maszyny wirtualnej, na przykład *myVM*, pozostaw *SSD* jako typ dysku, a następnie podaj nazwę użytkownika, na przykład *azureuser*. Hasło musi mieć co najmniej 12 znaków i spełniać [zdefiniowane wymagania dotyczące złożoności](faq.md#what-are-the-password-requirements-when-creating-a-vm).

    ![Wprowadzanie podstawowych informacji o maszynie wirtualnej w bloku portalu](./media/quick-create-portal/create-windows-vm-portal-basic-blade.png)

5. W sekcji grupa zasobów wybierz opcję **Utwórz nowy** i podaj nazwę grupy, na przykład *myResourceGroup*. Wybierz żądaną wartość z menu **Lokalizacja**, a następnie kliknij **OK**.

4. Wybierz rozmiar maszyny wirtualnej. Możesz filtrować między innymi według *Typu obliczeń* lub *Typu dysku*. Sugerowany rozmiar maszyny wirtualnej to *D2s_v3*.

    ![Zrzut ekranu przedstawiający rozmiary maszyn wirtualnych](./media/quick-create-portal/create-windows-vm-portal-sizes.png)

5. W sekcji **Ustawienia** pozostaw wartości domyślne i wybierz **OK**.

6. Na stronie podsumowania wybierz pozycję **Utwórz**, aby rozpocząć wdrażanie maszyny wirtualnej.

7. Maszyna wirtualna jest przypięta do pulpitu nawigacyjnego witryny Azure Portal. Po zakończeniu wdrażania zostanie automatycznie otwarte podsumowanie maszyny wirtualnej.

## <a name="connect-to-virtual-machine"></a>Nawiązywanie połączenia z maszyną wirtualną

Utwórz połączenie pulpitu zdalnego z maszyną wirtualną. Te kroki pozwolą Ci połączyć się z maszyną wirtualną z komputera z systemem Windows. Na komputerze Mac należy skorzystać z klienta RDP, takiego jak ten [klient pulpitu zdalnego](https://itunes.apple.com/us/app/microsoft-remote-desktop/id715768417?mt=12) ze sklepu Mac App Store.

1. Kliknij przycisk **Połącz** na stronie właściwości maszyny wirtualnej. 

    ![Nawiązywanie połączenia z maszyną wirtualną na platformie Azure z portalu](./media/quick-create-portal/quick-create-portal/portal-quick-start-9.png)
    
2. Na stronie **Nawiązywanie połączenia z maszyną wirtualną** zostaw opcje domyślne, aby połączyć się za pomocą nazwy DNS przez port 3389 i kliknij pozycję **Pobierz plik RDP**.

2. Otwórz pobrany plik RDP i kliknij **Połącz**, gdy wyświetli się odpowiedni monit. 

3. W oknie **Zabezpieczenia systemu Windows** wybierz pozycję **Więcej opcji**, a następnie pozycję **Użyj innego konta**. Wpisz nazwę użytkownika w formacie *nazwa_maszyny_wirtualnej*\*nazwa_użytkownika* oraz hasło utworzone dla maszyny wirtualnej, a następnie kliknij przycisk **OK**.

4. Podczas procesu logowania może pojawić się ostrzeżenie o certyfikacie. Kliknij przycisk **Tak** lub **Kontynuuj**, aby kontynuować nawiązywanie połączenia.

## <a name="install-web-server"></a>Instalowanie serwera sieci Web

Aby zobaczyć działanie maszyny wirtualnej, zainstaluj serwer sieci Web usług IIS. Na maszynie wirtualnej otwórz wiersz polecenia programu PowerShell i uruchom następujące polecenie:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

Po zakończeniu zamknij połączenie RDP z maszyną wirtualną.

## <a name="open-port-80-for-web-traffic"></a>Otwieranie portu 80 na potrzeby ruchu w sieci Web

Sieciowa grupa zabezpieczeń zabezpiecza ruch przychodzący i wychodzący. Po utworzeniu maszyny wirtualnej z poziomu witryny Azure Portal na porcie 3389 jest tworzona reguła ruchu przychodzącego dla połączeń RDP. Ponieważ maszyna wirtualna hostuje serwer sieci Web, należy utworzyć regułę sieciowej grupy zabezpieczeń dla portu 80.

1. Na stronie podsumowania maszyny wirtualnej wybierz pozycję **Sieć**.
2. Wyświetli się lista reguł ruchu przychodzącego i wychodzącego. Wybierz opcję **Dodaj regułę portu wejściowego**.
3. Wybierz opcję **Podstawowa** u góry, a następnie pozycję *HTTP* z listy dostępnych usług. Port 80, priorytet i nazwa zostaną podane.
4. Aby utworzyć regułę, wybierz pozycję **Dodaj**.

## <a name="view-the-iis-welcome-page"></a>Wyświetlanie strony powitalnej usług IIS

Po zainstalowaniu usług IIS i otwarciu portu 80 na maszynie wirtualnej na ruch z Internetu użyj wybranej przeglądarki sieci Web, aby wyświetlić domyślną stronę powitalną usług IIS. Użyj publicznego adresu IP maszyny wirtualnej uzyskanego w poprzednim kroku. W poniższym przykładzie pokazano domyślną stronę usług IIS:

![Domyślna witryna usług IIS](./media/quick-create-powershell/default-iis-website.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy grupa zasobów, maszyna wirtualna i wszystkie pokrewne zasoby nie będą już potrzebne, można je usunąć. Aby to zrobić, wybierz grupę zasobów maszyny wirtualnej, wybierz opcję **Usuń**, a następnie potwierdź nazwę grupy zasobów, którą chcesz usunąć.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start zawarto informacje na temat wdrażania maszyny wirtualnej, otwierania portu sieciowego na ruch internetowy oraz instalowania podstawowego serwera sieci Web. Aby dowiedzieć się więcej o maszynach wirtualnych platformy Azure, przejdź do samouczka dla maszyn wirtualnych z systemem Windows.

> [!div class="nextstepaction"]
> [Samouczki dla maszyny wirtualnej platformy Azure z systemem Windows](./tutorial-manage-vm.md)
