---
title: Maszyny wirtualne systemu Windows Azure Backup | Dokumentacja firmy Microsoft
description: Ochrona maszyn wirtualnych systemu Windows przez tworzenie ich kopii zapasowych za pomocą usługi Kopia zapasowa Azure.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/27/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 12859bf967cf8de1b57ab9dfd5c0bd080806f2eb
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="back-up-windows-virtual-machines-in-azure"></a>Tworzenie kopii zapasowych maszyn wirtualnych systemu Windows na platformie Azure

Możesz chronić swoje dane, tworząc kopie zapasowe w regularnych odstępach czasu. Usługa Azure Backup tworzy punkty odzyskiwania przechowywane w geograficznie nadmiarowych magazynach odzyskiwania. Z punktu odzyskiwania można przywrócić całą maszynę wirtualną lub tylko poszczególne pliki. W tym artykule opisano sposób przywracania jednego pliku do maszyny Wirtualnej z systemem Windows Server i usług IIS. Jeśli nie masz jeszcze maszynę Wirtualną do obsługi, możesz utworzyć go przy użyciu [Szybki Start systemu Windows](quick-create-portal.md). Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie kopii zapasowej maszyny wirtualnej
> * Harmonogram codziennego wykonywania kopii zapasowej
> * Przywracanie pliku z kopii zapasowej




## <a name="backup-overview"></a>Omówienie usługi Backup

Gdy usługa Azure Backup Inicjuje zadania tworzenia kopii zapasowej, wyzwala zapasowy numer wewnętrzny do tworzenia migawki punktu w czasie. Używa usługi Azure Backup _VMSnapshot_ rozszerzenia. Rozszerzenie jest instalowane podczas tworzenia pierwszej kopii zapasowej maszyny wirtualnej, jeśli maszyna wirtualna jest uruchomiona. Jeśli maszyna wirtualna nie jest uruchomiona, usługa Backup utworzy migawkę powiązanego magazynu (ponieważ gdy maszyna wirtualna jest zatrzymana, nie występują zapisy aplikacji).

Podczas wykonywania migawki maszyn wirtualnych systemu Windows, usługi Kopia zapasowa koordynuje z woluminów w tle kopii Service (VSS) do uzyskania migawki spójne z dysków maszyny wirtualnej. Po utworzeniu migawki w usłudze Azure Backup dane są przesyłane do magazynu. Aby zmaksymalizować wydajność, usługa rozpoznaje i przesyła jedynie te bloki danych, które uległy zmianie od czasu utworzenia poprzedniej kopii zapasowej.

Po ukończeniu przesyłania danych migawka jest usuwana, a utworzony zostaje punkt odzyskiwania.


## <a name="create-a-backup"></a>Tworzenie kopii zapasowej
Utwórz prostą, zaplanowaną, codzienną operację tworzenia kopii zapasowych w magazynie usługi Recovery Services. 

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. W menu po lewej stronie wybierz pozycję **Maszyny wirtualne**. 
3. Z listy wybierz maszynę wirtualną do utworzenia kopii zapasowej.
4. W bloku maszyny wirtualnej w sekcji **Ustawienia** kliknij pozycję **Kopia zapasowa**. Spowoduje to otwarcie bloku **Włącz kopię zapasową**.
5. W **Magazynie usługi Recovery Services** kliknij pozycję **Utwórz nowy** i podaj nazwę nowego magazynu. Nowy magazyn zostanie utworzony w tej samej lokalizacji i tej samej grupie zasobów co maszyna wirtualna.
6. Kliknij opcję **Zasady tworzenia kopii zapasowych**. W tym przykładzie zachowaj ustawienia domyślne i kliknij przycisk **OK**.
7. W bloku **Włącz kopię zapasową** kliknij pozycję **Włącz kopię zapasową**. Spowoduje to codzienne tworzenie kopii zapasowej na podstawie harmonogramu domyślnego.
10. Aby utworzyć początkowy punkt odzyskiwania, w bloku **Kopia zapasowa** kliknij opcję **Utwórz kopię zapasową teraz**.
11. W bloku **Utwórz kopię zapasową teraz** kliknij ikonę kalendarza, wybierz ostatni dzień okresu zachowywania tego punktu odzyskiwania przy użyciu kontrolki kalendarza, a następnie kliknij pozycję **Kopia zapasowa**.
12. W bloku **Kopia zapasowa** dla maszyny wirtualnej zobaczysz liczbę ukończonych punktów odzyskiwania.

    ![Punkty odzyskiwania](./media/tutorial-backup-vms/backup-complete.png)
    
Tworzenie pierwszej kopii zapasowej zajmuje około 20 minut. Przejdź do następnej części tego samouczka po utworzeniu kopii zapasowej.

## <a name="recover-a-file"></a>Odzyskiwanie pliku

Jeżeli przypadkowo usuniesz plik lub wprowadzisz w nim zmiany, możesz użyć opcji odzyskiwania plików, aby odzyskać plik z magazynu kopii zapasowej. Odzyskiwanie plików używa skryptu uruchamianego na maszynie Wirtualnej, należy zainstalować punkt odzyskiwania jako dysk lokalny. Dyski te pozostaną zainstalowane przez 12 godzin, więc możesz kopiować pliki z punktu odzyskiwania i przywracać je na maszynę wirtualną.  

W tym przykładzie zostanie przedstawiony sposób odzyskać plik obrazu, który jest używany w domyślnej strony sieci web dla usług IIS. 

1. Otwórz przeglądarkę i połącz się adres IP maszyny wirtualnej można wyświetlić domyślną stronę usług IIS.

    ![Domyślna strona sieci web usług IIS](./media/tutorial-backup-vms/iis-working.png)

2. Połączenie z maszyną Wirtualną.
3. Na Maszynie wirtualnej, należy otworzyć **Eksploratora plików** i przejdź do \inetpub\wwwroot i usuń plik **iisstart.png**.
4. Na komputerze lokalnym Odśwież przeglądarkę, aby zobaczyć, czy obraz na domyślną stronę usług IIS został usunięty.

    ![Domyślna strona sieci web usług IIS](./media/tutorial-backup-vms/iis-broken.png)

5. Na komputerze lokalnym, otwórz nową kartę i Przejdź [portalu Azure](https://portal.azure.com).
6. W menu po lewej stronie wybierz **maszyn wirtualnych** i wybierz maszynę Wirtualną z listy.
8. W bloku maszyny wirtualnej w sekcji **Ustawienia** kliknij pozycję **Kopia zapasowa**. Zostanie otwarty blok **Kopia zapasowa**. 
9. W menu w górnej części bloku wybierz opcję **Odzyskiwanie pliku**. Zostanie otwarty blok **Odzyskiwanie plików**.
10. W obszarze **Krok 1. Wybieranie punktu odzyskiwania** wybierz punkt odzyskiwania z listy rozwijanej.
11. W obszarze **Krok 2. Pobieranie skryptu na potrzeby przeglądania i odzyskiwania plików** kliknij przycisk **Pobierz plik wykonywalny**. Zapisz plik, aby Twoje **pobiera** folderu.
12. Na komputerze lokalnym, otwórz **Eksploratora plików** i przejdź do Twojej **pobiera** folderu i skopiuj pobrany plik .exe. Nazwa pliku będzie ona poprzedzona przez nazwę maszyny Wirtualnej. 
13. Na maszynie Wirtualnej (za pośrednictwem połączenia RDP) Wklej plik .exe na pulpicie maszyny Wirtualnej. 
14. Przejdź do pulpitu z maszyną Wirtualną, a następnie kliknij dwukrotnie ikonę na .exe. Zostanie uruchomiony wiersz polecenia i następnie zainstalować punkt odzyskiwania jako udział pliku, który można uzyskać dostęp. Po zakończeniu tworzenia udziału wpisz **q** aby zamknąć okno wiersza polecenia.
15. Na maszynie Wirtualnej, należy otworzyć **Eksploratora plików** i przejdź do litery dysku, którego użyto do udziału plików.
16. Przejdź do \inetpub\wwwroot i skopiuj **iisstart.png** z pliku udziału i wklej go do \inetpub\wwwroot. Na przykład F:\inetpub\wwwroot\iisstart.png skopiować i wkleić c:\inetpub\wwwroot odzyskać pliku.
17. Na komputerze lokalnym otwórz kartę przeglądarki, w której są podłączone do adresu IP maszyny wirtualnej przedstawiający domyślną stronę usług IIS. Naciśnij klawisze CTRL + F5, aby odświeżyć stronę przeglądarki. Powinna zostać wyświetlona, że obraz został przywrócony.
18. Na komputerze lokalnym wróć do karty przeglądarki internetowej z witryną Azure Portal i w obszarze **Krok 3. Odinstalowanie dysków po odzyskiwaniu** kliknij przycisk **Odinstaluj dyski**. Jeśli zapomnisz wykonać ten krok, połączenie punktu instalacji jest automatycznie Zamknij po 12 godzinach. Po upłynięciu tych 12 godzin musisz pobrać nowy skrypt, aby utworzyć nowy punkt instalacji.


## <a name="next-steps"></a>Kolejne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie kopii zapasowej maszyny wirtualnej
> * Harmonogram codziennego wykonywania kopii zapasowej
> * Przywracanie pliku z kopii zapasowej

Przejdź do następnego samouczka, aby uzyskać informacje o monitorowaniu maszyn wirtualnych.

> [!div class="nextstepaction"]
> [Zarządzanie maszynami wirtualnymi](tutorial-govern-resources.md)









