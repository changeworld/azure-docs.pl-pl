---
title: Azure stosu Szybki Start — Tworzenie portalu maszyny Wirtualnej
description: Azure stosu Szybki Start — Utwórz Maszynę wirtualną systemu Linux przy użyciu portalu
services: azure-stack
cloud: azure-stack
author: brenduns
manager: femila
ms.service: azure-stack
ms.topic: quickstart
ms.date: 04/24/2018
ms.author: brenduns
ms.reviewer: ''
ms.custom: mvc
ms.openlocfilehash: 2ea07f04d4c566c0add39d75cad3d3a4ed81c6c8
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
ms.locfileid: "32152224"
---
# <a name="quickstart-create-a-linux-server-virtual-machine-with-the-azure-stack-portal"></a>Szybki Start: tworzenie maszyny wirtualnej serwera systemu Linux przy użyciu portalu Azure stosu

*Dotyczy: Azure stosu zintegrowanych systemów i Azure stosu Development Kit*

Można utworzyć maszyny wirtualnej systemu Ubuntu Server 16.04 LTS przy użyciu portalu Azure stosu. Wykonaj kroki opisane w tym artykule do utworzenia i użycia maszyny wirtualnej. Ten artykuł zawiera także zapoznać się z procedurą:

* Połączenie z maszyną wirtualną za pomocą zdalnego klienta.
* Zainstaluj serwer sieci web NGINX.
* Oczyszczanie zasobów.

## <a name="prerequisites"></a>Wymagania wstępne

* **Obraz systemu Linux w stosie Azure marketplace**

   Domyślnie, stos Azure marketplace nie zawiera obrazu systemu Linux. Przed utworzeniem maszyny wirtualnej systemu Linux serwera, upewnij się, że operator stosu Azure oferuje **Ubuntu Server 16.04 LTS** obrazu należy. Operator można użyć procedury opisanej w [pobieranie elementów marketplace z platformy Azure do stosu Azure](../azure-stack-download-azure-marketplace-item.md) artykułu.

* **Dostęp do klienta SSH**

   Jeśli używasz usługi Azure stosu Development Kit (ASDK) może utracić dostęp do klienta SSH. Jeśli potrzebujesz klienta istnieje kilka pakietów, które obejmują klienta SSH. Na przykład PuTTY zawiera klienta SSH i generatora klucza SSH (puttygen.exe). Aby uzyskać więcej informacji na temat dostępnych pakietów, przeczytaj następujące Azure artykuł: [jak klucze używanie SSH z systemem Windows Azure](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows#windows-packages-and-ssh-clients).

   Ta opcja szybkiego startu używa PuTTY do generowania kluczy SSH i nawiązywania połączenia z maszyny wirtualnej systemu Linux serwera. Aby pobrać i zainstalować program PuTTY, przejdź do [ http://www.putty.org/ ](http://www.putty.org).

## <a name="create-an-ssh-key-pair"></a>Tworzenie pary kluczy SSH

Należy parę kluczy SSH, aby zakończyć wszystkie kroki opisane w tym artykule. Jeśli masz istniejące pary kluczy SSH, możesz pominąć ten krok.

1. Przejdź do folderu instalacji programu PuTTY (domyślna lokalizacja to ```C:\Program Files\PuTTY```) i uruchom ```puttygen.exe```.
2. W oknie programu PuTTY generatora klucza upewnij się, **typ klucza do wygenerowania** ustawiono **RSA**i **liczba bitów wygenerowany klucz** ustawiono **2048**. Gdy wszystko jest gotowe, kliknij przycisk **Generuj**.

   ![Generator klucza Konfiguracja programu puTTY](media/azure-stack-quick-linux-portal/Putty01.PNG)

3. Aby wygenerować klucz, przesuń wskaźnik myszy losowo w oknie generatora klucza PuTTY.
4. Po zakończeniu generowania kluczy, kliknij **Zapisz klucz publiczny** , a następnie kliknij przycisk **Zapisz klucz prywatny** można zapisać klucze do plików.

   ![Programu puTTY wyniki generatora klucza](media/azure-stack-quick-linux-portal/Putty02.PNG)

## <a name="sign-in-to-the-azure-stack-portal"></a>Zaloguj się do portalu Azure stosu

Zaloguj się do portalu Azure stosu. Adres w portalu Azure stosu zależy od tego, na który produkt stosu Azure łączysz się:

* Dla usługi Azure stosu Development Kit (ASDK), przejdź do: https://portal.local.azurestack.external.
* System Azure stosu zintegrowane przejdź do adresu URL, zapewnianej przez operatora sieci Azure stosu.

## <a name="create-the-virtual-machine"></a>Tworzenie maszyny wirtualnej

1. Kliknij przycisk **Utwórz zasób** w lewym górnym rogu portalu Azure stosu.

2. Wybierz pozycję **Wystąpienia obliczeniowe**, a następnie wybierz pozycję **Ubuntu Server 16.04 LTS**.
3. Kliknij przycisk **Utwórz**.

4. Typ informacji o maszynie wirtualnej. W obszarze **Typ uwierzytelniania** wybierz pozycję **Klucz publiczny SSH**. Wklej klucz publiczny SSH zapisane, a następnie kliknij przycisk **OK**.

   >[!NOTE]
 Upewnij się, że usunięciu początkowych ani końcowych spacji biały one klucza.

   ![Podstawowe informacje o panelu — Konfigurowanie maszyny wirtualnej](media/azure-stack-quick-linux-portal/linux-01.PNG)

5. Wybierz **D1_V2** dla maszyny wirtualnej.

   ![Rozmiar panelu — wybierz rozmiar maszyny wirtualnej](media/azure-stack-quick-linux-portal/linux-02.PNG)

6. Na **ustawienia** , Zachowaj wartości domyślne i kliknij przycisk **OK**.

7. Na **Podsumowanie** kliknij przycisk **OK** rozpocząć wdrażanie maszyny wirtualnej.

## <a name="connect-to-the-virtual-machine"></a>Nawiązywanie połączenia z maszyną wirtualną

1. Kliknij przycisk **Connect** na stronie maszyny wirtualnej. Spowoduje to wyświetlenie ciąg połączenia SSH, które należy połączyć z maszyną wirtualną.

   ![Podłącz maszynę wirtualną](media/azure-stack-quick-linux-portal/linux-03.PNG)

2. Otwórz program PuTTY.
3. Na **Konfiguracja programu PuTTY** ekranu będzie używać **kategorii** okna przewiń w górę lub w dół. Przewiń w dół do **SSH**, rozwiń węzeł **SSH**, a następnie kliknij przycisk **uwierzytelniania**. Kliknij przycisk **Przeglądaj** i wybierz plik klucza prywatnego, który został zapisany.

   ![Wybieranie klucza prywatnego PuTTY](media/azure-stack-quick-linux-portal/Putty03.PNG)

4. Przewiń w górę **kategorii** okna, a następnie kliknij przycisk **sesji**.
5. W **nazwy hosta (lub adres IP)** Wklej parametry połączenia wyświetlana w portalu Azure stosu. W tym przykładzie jest ciąg ```asadmin@192.168.102.34```.

   ![Parametry połączenia Konfiguracja programu puTTY](media/azure-stack-quick-linux-portal/Putty04.PNG)

6. Kliknij przycisk **Otwórz** sesji dla maszyny wirtualnej.

   ![Sesja systemu Linux](media/azure-stack-quick-linux-portal/Putty05.PNG)

## <a name="install-the-nginx-web-server"></a>Zainstaluj serwer sieci web NGINX

Aby zaktualizować źródła pakietów i zainstaluj najnowszy pakiet NGINX na maszynie wirtualnej, użyj następujących poleceń bash.

```bash
#!/bin/bash

# update package source
sudo apt-get -y update

# install NGINX
sudo apt-get -y install nginx
```

Po zakończeniu instalowania NGINX, Zamknij sesję SSH i otwórz strony Przegląd maszyny wirtualnej w portalu Azure stosu.

## <a name="open-port-80-for-web-traffic"></a>Otwieranie portu 80 na potrzeby ruchu w sieci Web

Sieciowa grupa zabezpieczeń zabezpiecza ruch przychodzący i wychodzący. Po utworzeniu maszyny wirtualnej w portalu Azure stosu tworzona jest reguła dla ruchu przychodzącego na porcie 22 dla połączenia SSH. Ponieważ ta maszyna wirtualna jest hostem serwera sieci web, reguły NSG musi zostać utworzona zezwalająca na ruch sieci web na porcie 80.

1. Na maszynie wirtualnej **omówienie** kliknij nazwę **grupy zasobów**.
2. Wybierz **sieciowej grupy zabezpieczeń** dla maszyny wirtualnej. Sieciową grupę zabezpieczeń można zidentyfikować za pomocą kolumny **Typ**.
3. W menu po lewej stronie w obszarze **ustawienia**, kliknij przycisk **reguły zabezpieczeń dla ruchu przychodzącego**.
4. Kliknij pozycję **Add** (Dodaj).
5. W polu **Nazwa** wpisz wartość **http**. Upewnij się, że w polu **Zakres portów** ustawiono wartość 80, a w polu **Akcja** — wartość **Zezwalaj**.
6. Kliknij przycisk **OK**.

## <a name="view-the-nginx-welcome-page"></a>Wyświetlanie strony powitalnej serwera NGINX

Z NGINX zainstalowane, a port 80 jest otwarty na maszynie wirtualnej można uzyskać dostępu do serwera sieci web przy użyciu publicznego adresu IP maszyny wirtualnej. (Publiczny adres IP jest wyświetlany na stronie Omówienie maszyny wirtualnej).

Otwórz przeglądarkę sieci web i przejdź do ```http://<public IP address>```.

![Strona powitalna serwera sieci web NGINX](media/azure-stack-quick-linux-portal/linux-04.PNG)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Oczyszczanie zasobów, które nie jest już konieczne. Aby usunąć maszyny wirtualnej i jej zasobów, wybierz grupę zasobów, na stronie maszyny wirtualnej, a następnie kliknij przycisk **usunąć**.

## <a name="next-steps"></a>Kolejne kroki

W tym szybki start wdrożono podstawowego serwera maszyny wirtualnej systemu Linux przez serwer sieci web. Aby dowiedzieć się więcej o maszynach wirtualnych Azure stosu, nadal [zagadnienia dotyczące maszyn wirtualnych w stosie Azure](azure-stack-vm-considerations.md).
