---
title: Azure Stack — Szybki Start — tworzenie maszyny Wirtualnej za pomocą portalu
description: 'Azure Stack: Szybki Start — tworzenie maszyny Wirtualnej systemu Linux przy użyciu portalu'
services: azure-stack
cloud: azure-stack
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.topic: quickstart
ms.date: 09/05/2018
ms.author: mabrigg
ms.reviewer: ''
ms.custom: mvc
ms.openlocfilehash: e82c3de4461e2d663496cd4ae4a98c10e7819466
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/06/2018
ms.locfileid: "44025414"
---
# <a name="quickstart-create-a-linux-server-virtual-machine-with-the-azure-stack-portal"></a>Szybki Start: tworzenie maszyny wirtualnej z systemem Linux server za pomocą portalu usługi Azure Stack

*Dotyczy: Usługa Azure Stack zintegrowane systemy i usługi Azure Stack Development Kit*

Za pomocą portalu usługi Azure Stack, można utworzyć maszynę wirtualną Ubuntu Server 16.04 LTS. Wykonaj kroki opisane w tym artykule, aby utworzyć maszynę wirtualną. Ten artykuł zawiera również kroki, aby:

* Łączenie z maszyną wirtualną za pomocą zdalnego klienta.
* Zainstaluj serwer internetowy NGINX.
* Czyszczenie zasobów.

> [!NOTE]  
> Obrazy ekranu, w tym artykule zostaną zaktualizowane w celu zmiany wprowadzone przy użyciu usługi Azure Stack w wersji 1808. 1808 dodaje obsługę za pomocą *usługi managed disks* oprócz dysków niezarządzanych. Jeśli używasz starszej wersji, niektóre obrazy, zadań, takich jak wybranego dysku będzie różnić się od wyświetlanych w tym artykule.  


## <a name="prerequisites"></a>Wymagania wstępne

* **Obraz systemu Linux w witrynie marketplace usługi Azure Stack**

   W portalu marketplace usługi Azure Stack nie zawiera obraz systemu Linux, domyślnie. Przed utworzeniem maszyny wirtualnej z systemem Linux server upewnij się, że operatora infrastruktury Azure Stack zapewnia **Ubuntu Server 16.04 LTS** obrazu potrzebujesz. Operator może użyć procedury opisanej w [pobieranie elementów portalu marketplace z platformy Azure do usługi Azure Stack](../azure-stack-download-azure-marketplace-item.md) artykułu.

* **Dostęp do klienta SSH**

   Jeśli używasz usługi Azure Stack Development Kit (ASDK), możesz utracić dostęp do klienta SSH. Jeśli potrzebujesz klienta, istnieje kilka pakietów, które zawierają klienta SSH. Na przykład program PuTTY zawiera klient SSH i generatora klucza SSH (puttygen.exe). Aby uzyskać więcej informacji na temat dostępnych pakietów, przeczytaj następujący artykuł platformy Azure: [sposobu użycia kluczy SSH z Windows Azure](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows#windows-packages-and-ssh-clients).

   Ten przewodnik Szybki Start używa programu PuTTY, można wygenerować kluczy SSH i łączenie z maszyną wirtualną serwera dla systemu Linux. Aby pobrać i zainstalować program PuTTY, przejdź do [ http://www.putty.org/ ](http://www.putty.org).

## <a name="create-an-ssh-key-pair"></a>Tworzenie pary kluczy SSH

Należy pary kluczy SSH, aby zakończyć wszystkie kroki opisane w tym artykule. Jeśli masz już parę kluczy SSH, możesz pominąć ten krok.

1. Przejdź do folderu instalacyjnego programu PuTTY (domyślna lokalizacja to ```C:\Program Files\PuTTY```) i uruchom ```puttygen.exe```.
2. W oknie Generator kluczy PuTTY, upewnij się, **typ klucza do wygenerowania** ustawiono **RSA**i **liczbę bitów w wygenerowany klucz** ustawiono **2048**. Gdy wszystko będzie gotowe, kliknij przycisk **Generuj**.

   ![Generator kluczy Konfiguracja programu puTTY](media/azure-stack-quick-linux-portal/Putty01.PNG)

3. Aby wygenerować klucz, przesuń kursor myszy losowo w oknie Generator kluczy PuTTY.
4. Po zakończeniu generowania kluczy, kliknij **Zapisz klucz publiczny** a następnie kliknij przycisk **Zapisz klucz prywatny** można zapisać klucze do plików.

   ![Generator kluczy puTTY wyników](media/azure-stack-quick-linux-portal/Putty02.PNG)

## <a name="sign-in-to-the-azure-stack-portal"></a>Zaloguj się do portalu usługi Azure Stack

Zaloguj się do portalu usługi Azure Stack. Adres portalu usługi Azure Stack zależy od tego, jaki produkt Azure Stack, którą jest nawiązywane połączenie:

* Dla usługi Azure Stack Development Kit (ASDK) przejdź do: https://portal.local.azurestack.external.
* Aby to system zintegrowany z usługi Azure Stack przejdź do adresu URL, który dostarczony operator usługi Azure Stack.

## <a name="create-the-virtual-machine"></a>Tworzenie maszyny wirtualnej

1. Kliknij przycisk **Utwórz zasób** w lewym górnym rogu portalu usługi Azure Stack.

2. Wybierz pozycję **Wystąpienia obliczeniowe**, a następnie wybierz pozycję **Ubuntu Server 16.04 LTS**.
   
   ![Wybierz serwer z systemem Linux](media/azure-stack-quick-linux-portal/select.png)
1. Kliknij pozycję **Utwórz**.

4. Wpisz informacje o maszynie wirtualnej. W obszarze **Typ uwierzytelniania** wybierz pozycję **Klucz publiczny SSH**. Wklej klucz publiczny SSH, zapisać, a następnie kliknij przycisk **OK**.

   >[!NOTE]
 Upewnij się, że usuniesz wszelkie odstępy wiodące lub końcowe one klucza.

   ![Podstawowe informacje dotyczące panelu — Konfigurowanie maszyny wirtualnej](media/azure-stack-quick-linux-portal/linux-01.PNG)

5. Wybierz **D1** dla maszyny wirtualnej.

   ![Rozmiar panelu — wybierz rozmiar maszyny wirtualnej](media/azure-stack-quick-linux-portal/linux-02.PNG)

6. Na **ustawienia** strony, wprowadź potrzebne zmiany do ustawień domyślnych.
   
    - Począwszy od wersji usługi Azure Stack 1808, można skonfigurować **magazynu** której użytkownik może użyć *usługi managed disks*. Przed wersją 1808 można tylko dyski niezarządzane.    
      ![Skonfiguruj magazyn dla dysków zarządzanych](media/azure-stack-quick-linux-portal/linux-03.PNG)
    
    Jeśli konfiguracje są gotowe, wybierz **OK** aby kontynuować.

7. Na **Podsumowanie** kliknij **OK** rozpocząć wdrażanie maszyny wirtualnej.  
   ![Wdrażanie](media/azure-stack-quick-linux-portal/deploy.png)

## <a name="connect-to-the-virtual-machine"></a>Nawiązywanie połączenia z maszyną wirtualną

1. Kliknij przycisk **Connect** na stronie maszyny wirtualnej. Spowoduje to wyświetlenie parametry połączenia SSH, który chcesz połączyć z maszyną wirtualną. 

2. Otwórz program PuTTY.

3. Na **Konfiguracja programu PuTTY** ekranu będzie używać **kategorii** okna, aby przewijać w górę lub w dół. Przewiń w dół do **SSH**, rozwiń węzeł **SSH**, a następnie kliknij przycisk **uwierzytelniania**. Kliknij przycisk **Przeglądaj** i wybierz plik klucza prywatnego, który został zapisany.
   ![Połączenia z maszyną wirtualną](media/azure-stack-quick-linux-portal/putty03.PNG)

4. Przewiń w górę **kategorii** okna, a następnie kliknij przycisk **sesji**.
5. W **nazwy hosta (lub adres IP)** pole, Wklej parametry połączenia, wyświetlana w portalu usługi Azure Stack. W tym przykładzie ciąg jest ```asadmin@192.168.102.34```.

   ![Konfiguracja programu puTTY, ciąg połączenia](media/azure-stack-quick-linux-portal/Putty04.PNG)

6. Kliknij przycisk **Otwórz** można otworzyć sesji dla maszyny wirtualnej.

   ![Sesja systemu Linux](media/azure-stack-quick-linux-portal/Putty05.PNG)

## <a name="install-the-nginx-web-server"></a>Instalowanie serwera internetowego NGINX

Użyj następujących poleceń powłoki bash w celu zaktualizowania źródeł pakietów i zainstalowania najnowszego pakietu NGINX na maszynie wirtualnej.

```bash
#!/bin/bash

# update package source
sudo apt-get -y update

# install NGINX
sudo apt-get -y install nginx
```

Po zainstalowaniu serwera NGINX, Zamknij sesję SSH, a następnie otwórz stronę przeglądu maszyny wirtualnej w portalu Azure Stack.

## <a name="open-port-80-for-web-traffic"></a>Otwieranie portu 80 na potrzeby ruchu w sieci Web

Sieciowa grupa zabezpieczeń zabezpiecza ruch przychodzący i wychodzący. Po utworzeniu maszyny wirtualnej w portalu Azure Stack na porcie 22 dla połączeń SSH jest tworzona reguła ruchu przychodzącego. Ponieważ ta maszyna wirtualna hostuje serwer sieci web, regułę sieciowej grupy zabezpieczeń musi utworzyć, aby zezwolić na ruch internetowy na porcie 80.

1. Na maszynie wirtualnej **Przegląd** kliknij nazwę **grupy zasobów**.
2. Wybierz **sieciowej grupy zabezpieczeń** dla maszyny wirtualnej. Sieciową grupę zabezpieczeń można zidentyfikować za pomocą kolumny **Typ**.
3. W menu po lewej stronie w obszarze **ustawienia**, kliknij przycisk **reguły zabezpieczeń dla ruchu przychodzącego**.
4. Kliknij pozycję **Add** (Dodaj).
5. W polu **Nazwa** wpisz wartość **http**. Upewnij się, że w polu **Zakres portów** ustawiono wartość 80, a w polu **Akcja** — wartość **Zezwalaj**.
6. Kliknij przycisk **OK**.

## <a name="view-the-nginx-welcome-page"></a>Wyświetlanie strony powitalnej serwera NGINX

Dzięki po zainstalowaniu serwera NGINX i otwarciu na maszynie wirtualnej portu 80 możesz uzyskać dostęp do serwera sieci web przy użyciu publicznego adresu IP maszyny wirtualnej. (Publiczny adres IP znajduje się na stronie Przegląd maszyny wirtualnej).

Otwórz przeglądarkę internetową i przejdź do ```http://<public IP address>```.

![Strona powitalna serwera sieci web NGINX](media/azure-stack-quick-linux-portal/linux-05.PNG)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Wyczyść zasoby, których nie potrzebujesz już. Aby usunąć maszyny wirtualnej i jej zasobów, wybierz grupę zasobów, na stronie maszyny wirtualnej, a następnie kliknij przycisk **Usuń**.

## <a name="next-steps"></a>Kolejne kroki

W tym przewodniku Szybki start wdrożono podstawowe Linux serwera maszynę wirtualną z serwerem sieci web. Aby dowiedzieć się więcej o maszynach wirtualnych usługi Azure Stack, w dalszym ciągu [uwagi dotyczące maszyn wirtualnych w usłudze Azure Stack](azure-stack-vm-considerations.md).
