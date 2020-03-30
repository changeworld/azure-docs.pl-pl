---
title: Konfigurowanie etycznego laboratorium hakerskiego za pomocą usług Azure Lab | Dokumenty firmy Microsoft
description: Dowiedz się, jak skonfigurować laboratorium przy użyciu usług Azure Lab Services w celu uczenia etycznego hakowania.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2019
ms.author: spelluru
ms.openlocfilehash: 2b600edc4c360a2b2990be34e44bb8fbd1c8f721
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74133179"
---
# <a name="set-up-a-lab-to-teach-ethical-hacking-class"></a>Skonfiguruj laboratorium do nauczania etycznej klasy hakerskiej 
W tym artykule pokazano, jak skonfigurować klasę, która koncentruje się na kryminalistyce etycznego hakowania. Testy penetracyjne, praktyka stosowana przez etyczną społeczność hakerów, ma miejsce, gdy ktoś próbuje uzyskać dostęp do systemu lub sieci, aby wykazać luki, które może wykorzystać złośliwy atakujący. 

W klasie etycznego hakowania uczniowie mogą nauczyć się nowoczesnych technik obrony przed lukami w zabezpieczeniach. Każdy student otrzymuje maszynę wirtualną hosta systemu Windows Server, która ma dwie zagnieżdżone maszyny wirtualne — jedną maszynę wirtualną z obrazem [Metasploitable3](https://github.com/rapid7/metasploitable3) i inną maszynę z obrazem [Kali Linux.](https://www.kali.org/) Metasploitable maszyny wirtualnej jest używany do wykorzystania celów i kali maszyny wirtualnej zapewnia dostęp do narzędzi potrzebnych do wykonywania zadań kryminalistycznych.

Ten artykuł zawiera dwie główne sekcje. Pierwsza sekcja opisuje, jak utworzyć laboratorium w klasie. Druga sekcja opisuje sposób tworzenia maszyny szablonu z włączoną wirtualizacją zagnieżdżoną oraz z potrzebnymi narzędziami i obrazami. W tym przypadku obraz Metasploitable i obraz Kali Linux na komputerze z funkcją Hyper-V do hostowania obrazów.

## <a name="lab-configuration"></a>Konfiguracja laboratorium
Aby skonfigurować to laboratorium, potrzebujesz subskrypcji platformy Azure, aby rozpocząć. Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem. Po uzyskaniu subskrypcji platformy Azure możesz utworzyć nowe konto laboratorium w usłudze Azure Lab Services lub użyć istniejącego konta. Zobacz następujący samouczek do tworzenia nowego konta laboratorium: [Samouczek do konfigurowania konta laboratorium](tutorial-setup-lab-account.md).

Wykonaj [ten samouczek,](tutorial-setup-classroom-lab.md) aby utworzyć nowe laboratorium, a następnie zastosuj następujące ustawienia:

| Rozmiar maszyny wirtualnej | Image (Obraz) |
| -------------------- | ----- | 
| Średni (wirtualizacja zagnieżdżona) | Windows Server 2019 Datacenter |

## <a name="template-machine"></a>Maszyna szablonu 

Po utworzeniu maszyny szablonu uruchom komputer i połącz się z nim, aby wykonać następujące trzy główne zadania. 
 
1. Skonfiguruj maszynę do zagnieżdżonej wirtualizacji. Umożliwia wszystkie odpowiednie funkcje systemu Windows, takie jak Funkcja Hyper-V, i konfiguruje sieć obrazów hyper-V, aby móc komunikować się ze sobą i Internetem.
2. Skonfiguruj obraz [Kali](https://www.kali.org/) Linux. Kali to dystrybucja Linuksa, która zawiera narzędzia do testowania penetracji i inspekcji bezpieczeństwa.
3. Skonfiguruj obraz Metasploitable. W tym przykładzie zostanie użyty obraz [Metasploitable3.](https://github.com/rapid7/metasploitable3) Ten obraz jest tworzony w celu celowego wyeliminowania luk w zabezpieczeniach.

Skrypt, który automatyzuje zadania opisane powyżej jest dostępny w [Lab Services Ethical Hacking Scripts](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/EthicalHacking).

### <a name="prepare-template-machine-for-nested-virtualization"></a>Przygotowanie maszyny szablonu do zagnieżdżonej wirtualizacji
Postępuj zgodnie z instrukcjami w [tym artykule,](how-to-enable-nested-virtualization-template-vm.md) aby przygotować maszynę wirtualną szablonu do zagnieżdżonej wirtualizacji. 

### <a name="set-up-a-nested-virtual-machine-with-kali-linux-image"></a>Konfigurowanie zagnieżdżonej maszyny wirtualnej za pomocą obrazu Kali Linux
Kali to dystrybucja Linuksa, która zawiera narzędzia do testowania penetracji i inspekcji bezpieczeństwa.

1. Pobierz obraz [https://www.offensive-security.com/kali-linux-vm-vmware-virtualbox-image-download/](https://www.offensive-security.com/kali-linux-vm-vmware-virtualbox-image-download/)z pliku .  
    1. Pobierz **Kali Linux Hyper-V 64 Bit** dla Hyper-V.
    1. Wyodrębnij plik .7z.  Jeśli nie masz jeszcze 7 zip, [https://www.7-zip.org/download.html](https://www.7-zip.org/download.html)pobierz go z . Zapamiętaj lokalizację wyodrębnianego folderu, ponieważ będzie on potrzebny później.
2. Otwórz **Menedżera funkcji Hyper-V** z narzędzi administracyjnych.
1. Wybierz **pozycję Akcja**, a następnie wybierz pozycję **Importuj maszynę wirtualną**. 
1. Na stronie **Lokalizowanie folderu** kreatora **Importuj maszynę wirtualną** wybierz lokalizację wyodrębnianego folderu zawierającego obraz Kali Linux.

    ![Okno dialogowe Zlokalizuj folder](../media/class-type-ethical-hacking/locate-folder.png)
1. Na stronie **Wybierz maszynę wirtualną** wybierz obraz Kali Linux.  W tym przypadku obraz jest **kali-linux-2019.3-hyperv**.

    ![Wybierz kali obraz](../media/class-type-ethical-hacking/select-kali-image.png)
1.  Na stronie **Wybierz typ importu** wybierz pozycję **Skopiuj maszynę wirtualną (utwórz nowy unikatowy identyfikator).**

    ![Wybierz typ importu](../media/class-type-ethical-hacking/choose-import-type.png)
1. Zaakceptuj ustawienia domyślne w **pozycji Wybierz foldery dla plików maszyn wirtualnych** i wybierz foldery do przechowywania stron **wirtualnych dysków twardych,** a następnie wybierz pozycję **Dalej**.
1. Na stronie **Połącz sieć** wybierz pozycję **LabServicesSwitch** utworzone wcześniej w sekcji **Przygotowywanie szablonu dla wirtualizacji zagnieżdżonej** w tym artykule, a następnie wybierz pozycję **Dalej**.

    ![Połącz stronę sieciową](../media/class-type-ethical-hacking/connect-network.png)
1. Wybierz **pozycję Zakończ** na stronie **Podsumowanie.** Poczekaj na zakończenie operacji kopiowania i importowania. Maszyna wirtualna Kali Linux będzie teraz dostępna w funkcji Hyper-V.
1. W **Menedżerze funkcji Hyper-V**wybierz pozycję **Akcja** -> **startowa**, a następnie wybierz pozycję **Action** -> **Connect,** aby połączyć się z maszyną wirtualną.  
12. Domyślna nazwa `root` użytkownika jest `toor`i hasło jest . 

    > [!NOTE]
    > Jeśli chcesz odblokować obraz, naciśnij klawisz CTRL i przeciągnij mysz w górę.

## <a name="set-up-a-nested-vm-with-metasploitable-image"></a>Konfigurowanie zagnieżdżonej maszyny Wirtualnej z obrazem metasploitable  
Obraz Metasploitable Rapid7 to obraz celowo skonfigurowany z lukami w zabezpieczeniach. Użyjesz tego obrazu do testowania i znajdowania problemów. Poniższe instrukcje pokazują, jak używać wstępnie utworzonego obrazu Metasploitable. Jeśli jednak potrzebna jest nowsza wersja obrazu Metasploitable, zobacz [https://github.com/rapid7/metasploitable3](https://github.com/rapid7/metasploitable3).

1. Przejdź [https://information.rapid7.com/download-metasploitable-2017.html](https://information.rapid7.com/download-metasploitable-2017.html)do pliku . Wypełnij formularz, aby pobrać obraz i wybierz przycisk **Prześlij.**
1. Wybierz przycisk **Pobierz metasploitable now.**
1. Po pobraniu pliku zip wyodrębnij plik zip i zapamiętaj lokalizację.
1. Konwertuj wyodrębniony plik vmdk na plik vhdx, aby można było używać z programem Hyper-V. Aby to zrobić, otwórz program PowerShell z uprawnieniami administracyjnymi i przejdź do folderu, w którym znajduje się plik vmdk, i postępuj zgodnie z poniższymi instrukcjami:
    1. Pobierz [konwerter maszyn wirtualnych firmy Microsoft](https://www.microsoft.com/download/details.aspx?id=42497)i uruchom plik mvmc_setup.msi po wyświetleniu monitu.
    1. Zaimportuj moduł programu PowerShell.  Domyślną lokalizacją, w której jest zainstalowany moduł, jest C:\Program Files\Microsoft Virtual Machine Converter\

        ```powershell
        Import-Module 'C:\Program Files\Microsoft Virtual Machine Converter\MvmcCmdlet.psd1'
        ```
    1. Konwertuj vmdk na plik vhd, który może być używany przez hyper-V. Ta operacja może potrwać kilka minut.
    
        ```powershell
        ConvertTo-MvmcVirtualHardDisk -SourceLiteralPath .\Metasploitable.vmdk -DestinationLiteralPath .\Metasploitable.vhdx -VhdType DynamicHardDisk -VhdFormat vhdx
        ```
    1. Skopiuj nowo utworzone pliku metasploitable.vhdx do folderu C:\Users\Public\Documents\Hyper-V\Virtual Hard Disks\. 
1. Utwórz nową maszynę wirtualną funkcji Hyper-V.
    1. Otwórz **Menedżera funkcji Hyper-V**.
    1. Wybierz **akcję** -> **Nowa** -> **maszyna wirtualna**.
    1. Na stronie **Przed rozpoczęciem** **Kreatora nowej maszyny wirtualnej**kliknij przycisk **Dalej**.
    1. Na stronie **Określanie nazwy i lokalizacji** wprowadź **metasploitable** dla **nazwy**i wybierz **pozycję Dalej**.

        ![Nowy kreator obrazów maszyny Wirtualnej](../media/class-type-ethical-hacking/new-vm-wizard-1.png)
    1. Na stronie **Określanie generacji** zaakceptuj wartości domyślne i wybierz pozycję **Dalej**.
    1. Na stronie **Przypisywanie pamięci** wprowadź **512 MB** **pamięci startowej**i wybierz pozycję **Dalej**. 

        ![Przypisywanie strony pamięci](../media/class-type-ethical-hacking/assign-memory-page.png)
    1. Na stronie **Konfigurowanie sieci** pozostaw połączenie jako **Niepołączone**. Karta sieciowa zostanie skonfigurowana później.
    1. Na stronie **Podłącz wirtualny dysk twardy** wybierz pozycję Użyj **istniejącego wirtualnego dysku twardego**. Przejdź do lokalizacji pliku **metasploitable.vhdx** utworzonego w poprzednim kroku i wybierz **przycisk Dalej**. 

        ![Połącz stronę dysku sieci wirtualnej](../media/class-type-ethical-hacking/connect-virtual-network-disk.png)
    1. Na stronie **Kończenie Kreatora nowej maszyny wirtualnej** i wybierz pozycję **Zakończ**.
    1. Po utworzeniu maszyny wirtualnej wybierz ją w Menedżerze funkcji Hyper-V. Nie włączaj jeszcze urządzenia.  
    1. Wybierz pozycję**Ustawienia** **akcji** -> .
    1. W oknie dialogowym **Ustawienia metasploitable** wybierz pozycję **Dodaj sprzęt**. 
    1. Wybierz **pozycję Starsza karta sieciowa**i wybierz pozycję **Dodaj**.

        ![Strona karty sieciowej](../media/class-type-ethical-hacking/network-adapter-page.png)
    1. Na stronie **Starsza karta sieciowa** wybierz **pozycję LabServicesSwitch** dla ustawienia **Przełącznik wirtualny** i wybierz przycisk **OK**. LabServicesSwitch został utworzony podczas przygotowywania maszyny szablonu dla funkcji Hyper-V w sekcji **Przygotowywanie szablonu dla wirtualizacji zagnieżdżonej.**

        ![Starsza karta sieciowa](../media/class-type-ethical-hacking/legacy-network-adapter-page.png)
    1. Obraz Metasploitable jest teraz gotowy do użycia. W **Menedżerze funkcji Hyper-V**wybierz pozycję **Akcja** -> **startowa**, a następnie wybierz pozycję **Action** -> **Connect,** aby połączyć się z maszyną wirtualną.  Domyślna nazwa użytkownika to **msfadmin,** a hasło **to msfadmin**. 


Szablon jest teraz aktualizowany i zawiera obrazy potrzebne do etycznego ataku hakerskiego, obraz z narzędziami do testowania penetracji i inny obraz z lukami w zabezpieczeniach do odkrycia. Obraz szablonu można teraz opublikować w klasie. Wybierz przycisk **Publikuj** na stronie szablonu, aby opublikować szablon w laboratorium.
  

## <a name="cost"></a>Koszty  
Jeśli chcesz oszacować koszt tego laboratorium, możesz użyć następującego przykładu: 
 
Dla klasy 25 uczniów z 20 godzin zaplanowanego czasu zajęć i 10 godzin przydziału na pracę domową lub zadania, cena za laboratorium będzie: 

25 uczniów * (20 + 10) godzin * 55 jednostek laboratoryjnych * 0.01 USD za godzinę = 412.50 USD. 

Aby uzyskać więcej informacji na temat cen, zobacz [Cennik usług Azure Lab Services](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="conclusion"></a>Podsumowanie
W tym artykule posuwał się przez kroki, aby utworzyć laboratorium dla etycznych hacking klasy. Zawiera kroki, aby skonfigurować zagnieżdżoną wirtualizację do tworzenia dwóch maszyn wirtualnych wewnątrz maszyny wirtualnej hosta do penetracji testowania.

## <a name="next-steps"></a>Następne kroki
Następne kroki są wspólne dla konfigurowania dowolnego laboratorium:

- [Dodaj użytkowników](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Ustawianie przydziału](how-to-configure-student-usage.md#set-quotas-for-users)
- [Ustawianie harmonogramu](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [E-mail linki do rejestracji do studentów](how-to-configure-student-usage.md#send-invitations-to-users). 

