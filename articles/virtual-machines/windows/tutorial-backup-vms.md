---
title: Samouczek — tworzenie kopii zapasowych maszyn wirtualnych z systemem Windows w witrynie Azure Portal | Microsoft Docs
description: Z tego samouczka dowiesz się, jak za pomocą witryny Azure Portal chronić maszyny wirtualne z systemem Windows przy użyciu usługi Azure Backup.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/27/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 7c6f5e199041af7d0ecd829ace2b56f5789f4955
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60785362"
---
# <a name="tutorial-back-up-and-restore-files-for-windows-virtual-machines-in-azure"></a>Samouczek: Tworzenie kopii zapasowej i przywracanie plików Windows maszyn wirtualnych na platformie Azure

Możesz chronić swoje dane, tworząc kopie zapasowe w regularnych odstępach czasu. Usługa Azure Backup tworzy punkty odzyskiwania przechowywane w geograficznie nadmiarowych magazynach odzyskiwania. Z punktu odzyskiwania można przywrócić całą maszynę wirtualną lub poszczególne pliki. W tym artykule opisano sposób przywracania pojedynczego pliku na maszynę wirtualną z systemem Windows Server i usługami IIS. Jeśli nie masz jeszcze maszyny wirtualnej gotowej do użycia, możesz ją utworzyć, korzystając z [przewodnika Szybki start dla systemu Windows](quick-create-portal.md). Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie kopii zapasowej maszyny wirtualnej
> * Harmonogram codziennego wykonywania kopii zapasowej
> * Przywracanie pliku z kopii zapasowej

## <a name="backup-overview"></a>Omówienie usługi Backup

Gdy usługa Azure Backup inicjuje zadanie utworzenia kopii zapasowej, wyzwala rozszerzenie kopii zapasowej, aby utworzyć migawkę punktu w czasie. Usługa Azure Backup używa rozszerzenia _VMSnapshot_. Rozszerzenie jest instalowane podczas tworzenia pierwszej kopii zapasowej maszyny wirtualnej, jeśli maszyna wirtualna jest uruchomiona. Jeśli maszyna wirtualna nie jest uruchomiona, usługa Backup utworzy migawkę powiązanego magazynu (ponieważ gdy maszyna wirtualna jest zatrzymana, nie występują zapisy aplikacji).

Podczas wykonywania migawki maszyn wirtualnych z systemem Windows usługa Backup koordynuje się z usługą kopiowania woluminów w tle (VSS, Volume Shadow Copy Service), aby uzyskać spójną migawkę dysków maszyny wirtualnej. Po utworzeniu migawki w usłudze Azure Backup dane są przesyłane do magazynu. Aby zmaksymalizować wydajność, usługa rozpoznaje i przesyła jedynie te bloki danych, które uległy zmianie od czasu utworzenia poprzedniej kopii zapasowej.

Po ukończeniu przesyłania danych migawka jest usuwana, a utworzony zostaje punkt odzyskiwania.


## <a name="create-a-backup"></a>Tworzenie kopii zapasowej
Utwórz prostą, zaplanowaną, codzienną operację tworzenia kopii zapasowych w magazynie usługi Recovery Services. 

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. W menu po lewej stronie wybierz pozycję **Maszyny wirtualne**. 
3. Z listy wybierz maszynę wirtualną do utworzenia kopii zapasowej.
4. W bloku maszyny wirtualnej w sekcji **Operacje** kliknij pozycję **Kopia zapasowa**. Spowoduje to otwarcie bloku **Włącz kopię zapasową**.
5. W **Magazynie usługi Recovery Services** kliknij pozycję **Utwórz nowy** i podaj nazwę nowego magazynu. Nowy magazyn zostanie utworzony w tej samej lokalizacji i tej samej grupie zasobów co maszyna wirtualna.
6. Kliknij opcję **Zasady tworzenia kopii zapasowych**. W tym przykładzie zachowaj ustawienia domyślne i kliknij przycisk **OK**.
7. W bloku **Włącz kopię zapasową** kliknij pozycję **Włącz kopię zapasową**. Spowoduje to codzienne tworzenie kopii zapasowej na podstawie harmonogramu domyślnego.
10. Aby utworzyć początkowy punkt odzyskiwania, w bloku **Kopia zapasowa** kliknij opcję **Utwórz kopię zapasową teraz**.
11. W bloku **Utwórz kopię zapasową teraz** kliknij ikonę kalendarza, wybierz ostatni dzień okresu zachowywania tego punktu odzyskiwania przy użyciu kontrolki kalendarza, a następnie kliknij pozycję **Kopia zapasowa**.
12. W bloku **Kopia zapasowa** dla maszyny wirtualnej wyświetlana jest liczba ukończonych punktów odzyskiwania.

    ![Punkty odzyskiwania](./media/tutorial-backup-vms/backup-complete.png)
    
Tworzenie pierwszej kopii zapasowej zajmuje około 20 minut. Przejdź do następnej części tego samouczka po utworzeniu kopii zapasowej.

## <a name="recover-a-file"></a>Odzyskiwanie pliku

Jeżeli przypadkowo usuniesz plik lub wprowadzisz w nim zmiany, możesz użyć opcji odzyskiwania plików, aby odzyskać plik z magazynu kopii zapasowej. Podczas odzyskiwania plików używany jest skrypt uruchamiany na maszynie wirtualnej, aby zainstalować punkt odzyskiwania jako dysk lokalny. Dyski te pozostają zainstalowane przez 12 godzin, co umożliwia kopiowanie plików z punktu odzyskiwania i przywracanie ich na maszynę wirtualną.  

W tym przykładzie przedstawiono sposób odzyskiwania pliku obrazu używanego na domyślnej stronie internetowej dla usług IIS. 

1. Otwórz przeglądarkę i połącz się z adresem IP maszyny wirtualnej, aby wyświetlić domyślną stronę usług IIS.

    ![Domyślna strona internetowa usług IIS](./media/tutorial-backup-vms/iis-working.png)

2. Nawiąż połączenie z maszyną wirtualną.
3. Otwórz **Eksploratora plików** na maszynie wirtualnej, przejdź do folderu \inetpub\wwwroot i usuń plik **iisstart.png**.
4. Na komputerze lokalnym odśwież przeglądarkę, aby zobaczyć, że domyślna strona usług IIS została usunięta.

    ![Domyślna strona internetowa usług IIS](./media/tutorial-backup-vms/iis-broken.png)

5. Na komputerze lokalnym otwórz nową kartę i przejdź do witryny [Azure Portal](https://portal.azure.com).
6. W menu po lewej stronie wybierz pozycję **Maszyny wirtualne** i wybierz maszynę wirtualną z listy.
8. W bloku maszyny wirtualnej w sekcji **Ustawienia** kliknij pozycję **Kopia zapasowa**. Zostanie otwarty blok **Kopia zapasowa**. 
9. W menu w górnej części bloku wybierz opcję **Odzyskiwanie pliku**. Zostanie otwarty blok **Odzyskiwanie plików**.
10. W **krok 1: Wybierz punkt odzyskiwania**, wybierz punkt odzyskiwania z listy rozwijanej.
11. W **krok 2: Pobierz skrypt do przeglądania i odzyskiwania plików**, kliknij przycisk **Pobierz plik wykonywalny** przycisku. Zapisz plik w folderze **Pobrane**.
12. Na komputerze lokalnym otwórz **Eksploratora plików**, przejdź do folderu **Pobrane** i skopiuj pobrany plik exe. Nazwa pliku jest poprzedzona nazwą maszyny wirtualnej. 
13. Wklej plik exe na pulpicie maszyny wirtualnej za pośrednictwem połączenia RDP. 
14. Przejdź do pulpitu maszyny wirtualnej i kliknij dwukrotnie plik exe. Spowoduje to uruchomienie wiersza polecenia, a następnie zainstalowanie punktu odzyskiwania jako udziału plików, do którego możesz uzyskać dostęp. Po zakończeniu tworzenia udziału wpisz **q**, aby zamknąć wiersz polecenia.
15. Na maszynie wirtualnej otwórz **Eksploratora plików** i przejdź do dysku oznaczonego literą, której użyto na potrzeby udziału plików.
16. Przejdź do folderu \inetpub\wwwroot, skopiuj plik **iisstart.png** z udziału plików i wklej go do folderu \inetpub\wwwroot. Na przykład skopiuj plik F:\inetpub\wwwroot\iisstart.png i wklej go do folderu C:\inetpub\wwwroot, aby odzyskać plik.
17. Na komputerze lokalnym otwórz kartę przeglądarki, na której nawiązano połączenie z adresem IP maszyny wirtualnej i wyświetlono domyślną stronę usług IIS. Naciśnij klawisze CTRL + F5, aby odświeżyć stronę przeglądarki. Teraz można zobaczyć, że obraz został przywrócony.
18. Na komputerze lokalnym wróć do karty przeglądarki dla portalu Azure i w **krok 3: Odinstalowanie dysków po odzyskiwaniu** kliknij **odinstaluj dyski** przycisku. Jeśli pominiesz ten krok, połączenie z punktem instalacji zostanie automatycznie zamknięte po 12 godzinach. Po upłynięciu tych 12 godzin musisz pobrać nowy skrypt, aby utworzyć nowy punkt instalacji.


## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie kopii zapasowej maszyny wirtualnej
> * Harmonogram codziennego wykonywania kopii zapasowej
> * Przywracanie pliku z kopii zapasowej

Przejdź do następnego samouczka, aby uzyskać informacje o monitorowaniu maszyn wirtualnych.

> [!div class="nextstepaction"]
> [Zarządzanie maszynami wirtualnymi](tutorial-govern-resources.md)









