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
ms.date: 07/03/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: d5f44c634b953194ad4f112722d82f282d8c8f1a
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2018
ms.locfileid: "37444614"
---
# <a name="quickstart-create-a-windows-virtual-machine-in-the-azure-portal"></a>Szybki start: tworzenie maszyny wirtualnej z systemem Windows w witrynie Azure Portal

Maszyny wirtualne platformy Azure można utworzyć za pomocą witryny Azure Portal. Ta metoda bazuje na opartym na przeglądarce interfejsie użytkownika umożliwiającym tworzenie maszyn wirtualnych i powiązanych z nimi zasobów. Z tego przewodnika Szybki start dowiesz się, jak za pomocą witryny Azure Portal wdrożyć maszynę wirtualną platformy Azure z systemem Windows Server 2016. Aby zobaczyć działanie maszyny wirtualnej, połączysz się z nią za pomocą protokołu RDP i zainstalujesz serwer internetowy usług IIS.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.

## <a name="create-virtual-machine"></a>Tworzenie maszyny wirtualnej

1. W lewym górnym rogu okna witryny Azure Portal wybierz pozycję **Utwórz zasób**.

2. W polu wyszukiwania nad listą zasobów Microsoft Azure Marketplace wpisz **Windows Server 2016 Datacenter**, wybierz odpowiednią pozycję i kliknij **Utwórz**.

3. Podaj nazwę maszyny wirtualnej, na przykład *myVM*, pozostaw *SSD* jako typ dysku, a następnie podaj nazwę użytkownika, na przykład *azureuser*. Hasło musi mieć co najmniej 12 znaków i spełniać [zdefiniowane wymagania dotyczące złożoności](faq.md#what-are-the-password-requirements-when-creating-a-vm).

    ![Wprowadzanie podstawowych informacji o maszynie wirtualnej w bloku portalu](./media/quick-create-portal/create-windows-vm-portal-basic-blade.png)

5. Wybierz opcję **Utwórz nowy**, aby utworzyć grupę zasobów, i podaj jej nazwę, na przykład *myResourceGroup*. Wybierz pozycję **Lokalizacja**, a następnie pozycję **OK**.

4. Wybierz rozmiar maszyny wirtualnej. Możesz filtrować między innymi według wartości *Typ obliczeń* lub *Typ dysku*. Sugerowany rozmiar maszyny wirtualnej to *D2s_v3*. Kliknij polecenie **Wybierz** po wybraniu rozmiaru.

    ![Zrzut ekranu przedstawiający rozmiary maszyn wirtualnych](./media/quick-create-portal/create-windows-vm-portal-sizes.png)

5. Na stronie **Ustawienia** w obszarze **Sieć** > **Sieciowa grupa zabezpieczeń** > **Dodaj publiczne porty wejściowe** wybierz pozycje **HTTP** i **RDP (3389)** z menu rozwijanego. Pozostaw resztę ustawień domyślnych, a następnie wybierz pozycję **OK**.

6. Na stronie podsumowania wybierz pozycję **Utwórz**, aby rozpocząć wdrażanie maszyny wirtualnej.

7. Maszyna wirtualna jest przypięta do pulpitu nawigacyjnego witryny Azure Portal. Po zakończeniu wdrażania zostanie automatycznie otwarte podsumowanie maszyny wirtualnej.

## <a name="connect-to-virtual-machine"></a>Nawiązywanie połączenia z maszyną wirtualną

Utwórz połączenie pulpitu zdalnego z maszyną wirtualną. Te kroki pozwolą Ci połączyć się z maszyną wirtualną z komputera z systemem Windows. Na komputerze Mac należy skorzystać z klienta RDP, takiego jak ten [klient pulpitu zdalnego](https://itunes.apple.com/us/app/microsoft-remote-desktop/id715768417?mt=12) ze sklepu Mac App Store.

1. Kliknij przycisk **Połącz** na stronie właściwości maszyny wirtualnej. 

    ![Nawiązywanie połączenia z maszyną wirtualną na platformie Azure z portalu](./media/quick-create-portal/quick-create-portal/portal-quick-start-9.png)
    
2. Na stronie **Nawiązywanie połączenia z maszyną wirtualną** zostaw opcje domyślne, aby połączyć się za pomocą nazwy DNS przez port 3389 i kliknij pozycję **Pobierz plik RDP**.

2. Otwórz pobrany plik RDP i kliknij **Połącz**, gdy wyświetli się odpowiedni monit. 

3. W oknie **Zabezpieczenia systemu Windows** wybierz pozycję **Więcej opcji**, a następnie pozycję **Użyj innego konta**. Wpisz nazwę użytkownika w formacie *nazwa_maszyny_wirtualnej*\*nazwa_użytkownika* oraz hasło utworzone dla maszyny wirtualnej, a następnie kliknij pozycję **OK**.

4. Podczas procesu logowania może pojawić się ostrzeżenie o certyfikacie. Kliknij pozycję **Tak** lub **Kontynuuj**, aby utworzyć połączenie.

## <a name="install-web-server"></a>Instalowanie serwera internetowego

Aby zobaczyć działanie maszyny wirtualnej, zainstaluj serwer internetowy usług IIS. Na maszynie wirtualnej otwórz wiersz polecenia programu PowerShell i uruchom następujące polecenie:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

Po zakończeniu zamknij połączenie RDP z maszyną wirtualną.


## <a name="view-the-iis-welcome-page"></a>Wyświetlanie strony powitalnej usług IIS

W portalu wybierz maszynę wirtualną, a następnie w przeglądzie maszyny wirtualnej użyj przycisku **Kliknij, aby skopiować** po prawej stronie adresu IP w celu skopiowania go i później wklejenia w karcie przeglądarki. Zostanie otwarta domyślna strona powitalna usług IIS, która powinna wyglądać następująco:

![Domyślna witryna usług IIS](./media/quick-create-powershell/default-iis-website.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy grupa zasobów, maszyna wirtualna i wszystkie pokrewne zasoby nie będą już potrzebne, można je usunąć. Aby to zrobić, wybierz grupę zasobów maszyny wirtualnej, wybierz opcję **Usuń**, a następnie potwierdź nazwę grupy zasobów, którą chcesz usunąć.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start wdrożono prostą maszynę wirtualną, otwarto port sieciowy na ruch internetowy oraz zainstalowano podstawowy serwer internetowy. Aby dowiedzieć się więcej o maszynach wirtualnych platformy Azure, przejdź do samouczka dla maszyn wirtualnych z systemem Windows.

> [!div class="nextstepaction"]
> [Samouczki dla maszyny wirtualnej platformy Azure z systemem Windows](./tutorial-manage-vm.md)
