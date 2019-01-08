---
title: Tworzenie obrazu maszyny wirtualnej w portalu Azure Marketplace | Dokumentacja firmy Microsoft
description: Aby uzyskać szczegółowe instrukcje dotyczące sposobu tworzenia obrazu maszyny wirtualnej dla witryny Azure Marketplace przez inne osoby do zakupu.
services: Azure Marketplace
documentationcenter: ''
author: v-miclar
manager: hascipio
editor: ''
ms.assetid: 5c937b8e-e28d-4007-9fef-624046bca2ae
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Azure
ms.workload: na
ms.date: 01/05/2017
ms.author: hascipio; v-divte
ROBOTS: NOINDEX
ms.openlocfilehash: 6737e16efa93370b5b5d2b46026fce3bbc22d38f
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/08/2019
ms.locfileid: "54075162"
---
# <a name="guide-to-create-a-virtual-machine-image-for-the-azure-marketplace"></a>Przewodnik, aby utworzyć obraz maszyny wirtualnej w portalu Azure Marketplace
W tym artykule **kroku 2**, przeprowadzi Cię przez przygotowywania wirtualnych dysków twardych (VHD), które zostanie wdrożone w portalu Azure Marketplace. Twoje dyski VHD są podstawą dla jednostki SKU. Ten proces różni się w zależności od tego, czy udostępniasz opartych na systemie Linux lub Windows, na podstawie jednostki SKU. W tym artykule opisano oba scenariusze. Ten proces można wykonać równolegle z [o tworzeniu konta i rejestracji][link-acct-creation].

## <a name="1-define-offers-and-skus"></a>1. Definiowanie ofert oraz jednostek SKU
W tej sekcji przedstawiono sposób Definiowanie ofert oraz ich skojarzone jednostki SKU.

Oferta pełni rolę nadrzędną względem wszystkich swoich jednostek SKU. Można określić wiele ofert. Decyzja dotycząca określenia struktury ofert należy do Ciebie. Wypchnięcie oferty do wdrażania przejściowego jest równoznaczne z wypchnięciem wszystkich jej jednostek SKU. Zastanów się uważnie nazwy identyfikatorów jednostek SKU, ponieważ będą one widoczne w adresie URL:

* Azure.com: http://azure.microsoft.com/marketplace/partners/{PartnerNamespace}/{OfferIdentifier}-{SKUidentifier}
* Witryna Azure portal: https://portal.azure.com/#gallery/{PublisherNamespace}.{OfferIdentifier}{SKUIDdentifier}  

Jednostka SKU jest komercyjną nazwą obrazu maszyny Wirtualnej. Obraz maszyny Wirtualnej zawiera dysk z jednego systemu operacyjnego i zero lub więcej dysków danych. Zasadniczo stanowi on kompletny profil magazynu dla maszyny wirtualnej. Jeden wirtualny dysk twardy jest wymagany na dysku. Puste dyski z danymi wymagają wirtualny dysk twardy ma zostać utworzony.

Niezależnie od używanego systemu operacyjnego należy dodać minimalną liczbę dysków z danymi, które są wymagane przez jednostkę SKU. Klienci nie mogą usuwać dysków, które należą do obrazu w czasie wdrażania, ale mogą dodawać dyski podczas lub po wdrożeniu, jeśli ich potrzebują.

> [!IMPORTANT]
> *Nie zmieniaj liczba dysków w nowej wersji obrazu.* Jeśli musisz ponownie skonfigurować dyski z danymi w obrazie, należy zdefiniować nowej jednostki SKU. Publikowania nowej wersji obrazu z dużymi liczbami inny dysk będzie mieć potencjał istotne nowe wdrożenie oparte na nową wersję obrazu w przypadku automatycznego skalowania, automatyczne wdrożeń rozwiązań za pomocą szablonów ARM i innych scenariuszy.
>
>

### <a name="11-add-an-offer"></a>1.1. Dodaj ofertę
1. Zaloguj się do [portalu wydawców] [ link-pubportal] przy użyciu konta sprzedawcy.
2. Wybierz **maszyn wirtualnych** karta w portalu wydawców. W pole wpisu wprowadź nazwę swojej oferty. Nazwa oferty zwykle jest nazwa produktu lub usługi, który chcesz sprzedawać w portalu Azure Marketplace.
3. Wybierz pozycję **Utwórz**.

### <a name="12-define-a-sku"></a>1.2 definiowania jednostka SKU
Po dodaniu oferty należy zdefiniować i zidentyfikować od jednostek SKU. Można określić wiele ofert, a każda oferta może mieć wielu jednostek SKU w nim. Wypchnięcie oferty do wdrażania przejściowego jest równoznaczne z wypchnięciem wszystkich jej jednostek SKU.

1. **Dodaj jednostkę SKU.** Jednostka SKU wymaga ona identyfikatora, który jest używany w adresie URL. Identyfikator musi być unikatowa w obrębie swój profil publikowania, ale nie istnieje ryzyko kolizji identyfikatora z innych wydawców.

   > [!NOTE]
   > Oferty i identyfikatorów jednostek SKU są wyświetlane w adresie URL oferty w portalu Marketplace.
   >
   >
2. **Dodaj podsumowanie dotyczące jednostki SKU.** Podsumowanie opisy są widoczne dla klientów, dlatego należy je łatwo odczytać. Te informacje nie musi być zablokowany do momentu fazy "Push do Staging". Można je do tego czasu swobodnie edytować.
3. Jeśli używasz jednostek SKU opartych na systemie Windows, użyj podanych linków, aby uzyskać zatwierdzone wersje systemu Windows Server.

## <a name="2-create-an-azure-compatible-vhd-linux-based"></a>2. Utwórz wirtualny dysk twardy zgodnych z platformą Azure (opartych na systemie Linux)
Ta sekcja koncentruje się na najlepsze rozwiązania dotyczące tworzenia obrazu maszyny Wirtualnej systemu Linux w portalu Azure Marketplace. Przewodnik krok po kroku można znaleźć w następującej dokumentacji: [Tworzenie niestandardowego obrazu maszyny Wirtualnej systemu Linux](../virtual-machines/linux/create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="3-create-an-azure-compatible-vhd-windows-based"></a>3. Utwórz wirtualny dysk twardy zgodnych z platformą Azure (z systemem Windows)
Ta sekcja koncentruje się na kroki, aby utworzyć jednostki SKU opartej na systemie Windows Server w portalu Azure Marketplace.

### <a name="31-ensure-that-you-are-using-the-correct-base-vhds"></a>3.1, upewnij się, że używasz poprawne podstawowe wirtualne dyski twarde
Wirtualny dysk twardy systemu operacyjnego obrazu maszyny Wirtualnej musi być oparta na zatwierdzone Azure podstawowy obraz, który zawiera systemu Windows Server lub SQL Server.

Aby rozpocząć, Utwórz Maszynę wirtualną z jednego z następujących obrazów znajdujących się na [portalu Microsoft Azure][link-azure-portal]:

* System Windows Server ([2012 R2 Datacenter] [link-datactr-2012-r2], [2012 Datacenter] [link-datactr-2012], [2008 R2 SP1][link-datactr-2008-r2])
* SQL Server 2014 
* SQL Server 2012 SP2 

Te linki można również znaleźć w Portalu wydawców na stronie Jednostka SKU.

> [!TIP]
> Jeśli używasz aktualnej wersji portalu Azure lub programu PowerShell obrazy systemu Windows Server opublikowane 8 września 2014 r. i nowszych są zatwierdzane.
>
>

### <a name="32-create-your-windows-based-vm"></a>3.2 Tworzenie maszyny Wirtualnej z systemem Windows
W portalu Microsoft Azure można utworzyć maszynę Wirtualną opartą na zatwierdzonym obrazie podstawowym w kilku prostych krokach. Poniższa lista zawiera omówienie procesu:

1. Na stronie obrazu podstawowego wybierz **Utwórz maszynę wirtualną** były kierowane do nowego [portalu Microsoft Azure][link-azure-portal].

    ![Rysowanie][img-acom-1]
2. Zaloguj się do portalu przy użyciu konta Microsoft i hasło do subskrypcji platformy Azure, którego chcesz użyć.
3. Postępuj zgodnie z monitami, aby utworzyć Maszynę wirtualną przy użyciu obrazu podstawowego, który wybrano. Zapewnić host name (nazwa komputera), nazwę użytkownika (zarejestrowane jako administrator) i hasło dla maszyny Wirtualnej.

    ![Rysowanie][img-portal-vm-create]
4. Wybierz rozmiar maszyny Wirtualnej do wdrożenia:

    a.    Jeśli zamierzasz utworzyć wirtualny dysk twardy w środowisku lokalnym, rozmiar nie ma znaczenia. Zastanów się nad użyciem maszyny wirtualnej o mniejszym rozmiarze.

    b.    Jeśli zamierzasz utworzyć obraz na platformie Azure, warto wybrać jeden z zalecanych rozmiarów maszyny wirtualnej dla wybranego obrazu.

    c.    Aby uzyskać informacje o cenach, zobacz **zalecane warstwy cenowe** selektor wyświetlany w portalu. Zobaczysz trzy zalecane rozmiary udostępniane przez wydawcę. (W tym przypadku wydawcą jest firma Microsoft).

    ![Rysowanie][img-portal-vm-size]
5. Ustaw właściwości:

    a.    W przypadku szybkiego wdrożenia można pozostawić wartości domyślne dla właściwości w obszarze **opcjonalna konfiguracja** i **grupy zasobów**.

    b.    W obszarze **konta magazynu**, opcjonalnie można wybrać konto magazynu, w którym przechowywane będą wirtualnego dysku twardego systemu operacyjnego.

    c.    W obszarze **grupy zasobów**, opcjonalnie można wybrać grupę logiczną, w której chcesz umieścić maszynę Wirtualną.
6. Wybierz **lokalizacji** dla wdrożenia:

    a.    Jeśli zamierzasz utworzyć wirtualny dysk twardy w środowisku lokalnym, lokalizacja nie ma znaczenia, ponieważ możesz przekazać obraz później na platformie Azure.

    b.    Jeśli chcesz utworzyć obraz na platformie Azure, warto na początku wybrać odpowiedni region platformy Microsoft Azure w Stanach Zjednoczonych. Ten wybór przyspiesza proces kopiowania wirtualnego dysku twardego, którą firma Microsoft wykona w Twoim imieniu po przesłaniu obrazu do certyfikacji.

    ![Rysowanie][img-portal-vm-location]
7. Kliknij pozycję **Utwórz**. Maszyna wirtualna uruchamia się do wdrożenia. Po upływie kilku minut, gdy wdrażanie zostanie zakończone, możesz przystąpić do tworzenia obrazu dla jednostki SKU.

### <a name="33-develop-your-vhd-in-the-cloud"></a>3.3 Tworzenie wirtualnego dysku twardego w chmurze
Zdecydowanie zaleca się tworzenie wirtualnego dysku twardego w chmurze przy użyciu protokołu RDP (Remote Desktop). Połączysz się nawiązać połączenie RDP z nazwą użytkownika i hasło określone podczas aprowizowania.

> [!IMPORTANT]
> **Nie należy używać dysków zarządzanych.** Maszyna wirtualna, służące do tworzenia dysku VHD do chmury nie musi być oparta na dyski zarządzane zgodnie z aktualnie nie obsługuje tworzenia obrazu z nich.
> Tworzenie maszyny wirtualnej w zmiany opcjonalna wartość domyślna w przypadku dysków zarządzanych.

> W przypadku tworzenia wirtualnego dysku twardego w środowisku lokalnym (co nie jest zalecane), zobacz [tworzenia obrazu maszyny wirtualnej w środowisku lokalnym](marketplace-publishing-vm-image-creation-on-premise.md). Pobieranie wirtualnego dysku twardego nie jest konieczne, jeśli tworzysz w chmurze.
>
>

**Nawiązywanie połączenia za pośrednictwem protokołu RDP przy użyciu [portalu Microsoft Azure][link-azure-portal]**

1. Wybierz **wszystkich usług** > **maszyn wirtualnych**.
2. Zostanie otwarty blok maszyny wirtualne. Upewnij się, że maszynę Wirtualną, którą chcesz połączyć się z i następnie wybierz z listy wdrożonych maszyn wirtualnych.
3. Zostanie otwarty blok, który opisuje wybranej maszyny Wirtualnej. U góry strony, kliknij przycisk **Connect**.
4. Monit o podanie nazwy użytkownika i hasło określone podczas aprowizowania.

**Połącz używając protokołu RDP przy użyciu programu PowerShell**

Aby pobrać pliku pulpitu zdalnego na komputerze lokalnym, użyj [polecenia cmdlet Get-AzureRemoteDesktopFile][link-technet-2]. Aby użyć tego polecenia cmdlet, musisz znać nazwę usługi i nazwę maszyny Wirtualnej. Jeśli utworzono maszynę Wirtualną z [portalu Microsoft Azure][link-azure-portal], można znaleźć te informacje w obszarze właściwości maszyny Wirtualnej:

1. W witrynie Microsoft Azure portal wybierz **wszystkich usług** > **maszyn wirtualnych**.
2. Zostanie otwarty blok maszyny wirtualne. Wybierz maszynę Wirtualną, która została wdrożona.
3. Zostanie otwarty blok, który opisuje wybranej maszyny Wirtualnej.
4. Kliknij pozycję **Właściwości**.
5. Pierwszą część nazwy domeny stanowi nazwa usługi. Nazwą hosta jest nazwa maszyny wirtualnej.

    ![Rysowanie][img-portal-vm-rdp]
6. Polecenie cmdlet, aby pobrać plik protokołu RDP dla utworzonej maszyny Wirtualnej na lokalny pulpit administratora wyglądają następująco.

        Get‐AzureRemoteDesktopFile ‐ServiceName “baseimagevm‐6820cq00” ‐Name “BaseImageVM” –LocalPath “C:\Users\Administrator\Desktop\BaseImageVM.rdp”

Więcej informacji na temat protokołu RDP można znaleźć w witrynie MSDN w artykule [nawiązywanie połączenia z Maszyną wirtualną platformy Azure przy użyciu protokołu RDP lub SSH](https://msdn.microsoft.com/library/azure/dn535788.aspx).

**Konfigurowanie maszyny Wirtualnej i tworzenie jednostki SKU**

Po pobraniu wirtualnego dysku twardego systemu operacyjnego Użyj funkcji Hyper-v i konfigurowanie maszyny Wirtualnej, aby rozpocząć tworzenie jednostki SKU. Szczegółowy opis kroków można znaleźć w witrynie TechNet: [Instalowanie funkcji Hyper-v i konfigurowanie maszyny Wirtualnej](https://technet.microsoft.com/library/hh846766.aspx).

### <a name="34-choose-the-correct-vhd-size"></a>3.4, wybierz odpowiedni rozmiar wirtualnego dysku twardego
System operacyjny Windows wirtualnego dysku twardego w obrazie maszyny Wirtualnej powinien zostać utworzony jako dysk VHD o stałym formacie 128 GB.  

Jeśli rozmiar fizyczny jest mniejszy niż 128 GB, wirtualny dysk twardy powinien być rozrzedzony. Podstawowe obrazy Windows i program SQL Server, podano już spełniają te wymagania, więc nie zmieniaj format lub rozmiar wirtualnego dysku twardego.  

Dyski danych może być większy niż 1 TB. Podczas ustawiania rozmiaru dysku, należy pamiętać, że klienci nie mogą zmieniać rozmiarów wirtualnych dysków twardych w ramach obrazu po ich wdrożeniu. Twarde z danymi, powinien zostać utworzony jako dysk VHD o stałym formacie. Również powinien być rozrzedzony. Dyski z danymi mogą zawierać dane lub mogą być puste.

### <a name="35-install-the-latest-windows-patches"></a>3.5 zainstalować najnowsze poprawki Windows
Podstawowe obrazy zawierają poprawki wydane do dnia opublikowania obrazu. Przed opublikowaniem utworzonego wirtualnego dysku twardego systemu operacyjnego, upewnij się, że został uruchomiony aktualizacji Windows i zainstalowano wszystkie najnowsze krytyczne i ważne aktualizacje zabezpieczeń.

### <a name="36-perform-additional-configuration-and-schedule-tasks-as-necessary"></a>3.6 wykonywanie dodatkowych zadań konfiguracji i harmonogram, zgodnie z potrzebami
Jeśli wymagane jest dodatkowe czynności konfiguracyjne, należy rozważyć użycie zaplanowanego zadania, które jest uruchamiane podczas uruchamiania, aby wprowadzić zmiany końcowy do maszyny Wirtualnej, po jej wdrożeniu:

* Dobrym rozwiązaniem jest takie skonfigurowanie zadania, aby zostało ono usunięte po pomyślnym wykonaniu.
* Konfiguracja nie będą miały innych dysków niż dyski C i D, ponieważ dyski te są tylko dwa, które są zawsze musi istnieć. Dysk C jest dysk systemu operacyjnego i dysk D to tymczasowy dysk lokalny.

### <a name="37-generalize-the-image"></a>3.7 Uogólnij obraz
Wszystkie obrazy w portalu Azure Marketplace muszą być wielokrotnego użytku w ogólny sposób. Innymi słowy musi być uogólniony wirtualny dysk twardy systemu operacyjnego:

* Dla Windows, obraz powinien być "Sysprep" i nie ma żadnych konfiguracji ma się odbywać, które nie obsługują **sysprep** polecenia.
* Można uruchomić następujące polecenie z katalogu % windir%\System32\Sysprep.

        sysprep.exe /generalize /oobe /shutdown

  Wskazówki dotyczące narzędzia sysprep systemu operacyjnego znajduje się w kroku następujący artykuł w witrynie MSDN: [Tworzenie i przekazywanie wirtualnego dysku twardego systemu Windows Server na platformie Azure](../virtual-machines/windows/classic/createupload-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="4-deploy-a-vm-from-your-vhds"></a>4. Wdrażanie maszyny Wirtualnej z sieci wirtualnych dysków twardych
Twoje dyski VHD (uogólniony wirtualny dysk twardy systemu operacyjnego i danych zero lub więcej dysków wirtualnych dysków twardych) przekazane do konta usługi Azure storage można zarejestrować je jako obraz maszyny Wirtualnej użytkownika. Następnie można przetestować tego obrazu. Ponieważ wirtualny dysk twardy systemu operacyjnego jest uogólniony, nie można bezpośrednio wdrożyć maszynę Wirtualną, podając adres URL wirtualnego dysku twardego.

Aby dowiedzieć się więcej na temat obrazów maszyn wirtualnych, przejrzyj następujące wpisy na blogu:

* [Obraz maszyny Wirtualnej](https://azure.microsoft.com/blog/vm-image-blog-post/)
* [Jak program PowerShell obrazu maszyny Wirtualnej](https://azure.microsoft.com/blog/vm-image-powershell-how-to-blog-post/)
* [Informacje o obrazach maszyn wirtualnych na platformie Azure](https://msdn.microsoft.com/library/azure/dn790290.aspx)

### <a name="set-up-the-necessary-tools-powershell-and-azure-classic-cli"></a>Skonfiguruj niezbędne narzędzia, programu PowerShell i klasycznego wiersza polecenia platformy Azure
* [Jak skonfigurować program PowerShell](/powershell/azure/overview)
* [Jak skonfigurować interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)

### <a name="41-create-a-user-vm-image"></a>4.1 tworzenia obrazu maszyny Wirtualnej użytkownika
#### <a name="capture-vm"></a>Przechwytywanie maszyny Wirtualnej
Przeczytaj łączy podanych poniżej w celu uzyskania wskazówek dotyczących Przechwytywanie maszyny Wirtualnej, przy użyciu wiersza polecenia platformy Azure/PowerShell/interfejsu API.

* [Interfejs API](https://msdn.microsoft.com/library/mt163560.aspx)
* [Program PowerShell](../virtual-machines/windows/capture-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Interfejs wiersza polecenia platformy Azure](../virtual-machines/linux/capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

### <a name="generalize-image"></a>Uogólnianie obrazu
Przeczytaj łączy podanych poniżej w celu uzyskania wskazówek dotyczących Przechwytywanie maszyny Wirtualnej, przy użyciu wiersza polecenia platformy Azure/PowerShell/interfejsu API.

* [Interfejs API](https://msdn.microsoft.com/library/mt269439.aspx)
* [Program PowerShell](../virtual-machines/windows/capture-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Interfejs wiersza polecenia platformy Azure](../virtual-machines/linux/capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

### <a name="42-deploy-a-vm-from-a-user-vm-image"></a>4.2 wdrożyć Maszynę wirtualną z obrazu maszyny Wirtualnej użytkownika
Aby wdrożyć Maszynę wirtualną z obrazu maszyny Wirtualnej użytkownika, można użyć bieżącej [witryny Azure portal](https://manage.windowsazure.com) lub programu PowerShell.

**Wdrażanie maszyny Wirtualnej z bieżącego portalu platformy Azure**

1. Przejdź do **New** > **obliczenia** > **maszyny wirtualnej** > **z galerii**.

2. Przejdź do **Moje obrazy**, a następnie wybierz obraz maszyny Wirtualnej, z którym chcesz wdrożyć Maszynę wirtualną:

   1. Płatność szczególną uwagę do obrazu, który zostanie wybrana, ponieważ **Moje obrazy** widok zawiera zarówno obrazy systemu operacyjnego i obrazy maszyn wirtualnych.
   2. Spojrzenie na liczbę dysków może pomóc ustalić, jakiego typu obraz jest wdrażany, ponieważ większość obrazów maszyn wirtualnych ma więcej niż jeden dysk. Będzie jednak nadal możliwe obrazu maszyny Wirtualnej z tylko jednym dyskiem systemu operacyjnego, który będzie miała **liczbę dysków** ustawiona na 1.

      ![Rysowanie][img-manage-vm-select]
3. Użyj Kreatora tworzenia maszyny Wirtualnej, a następnie określ maszyny Wirtualnej nazwy, maszyny Wirtualnej rozmiar, lokalizację, nazwę użytkownika i hasło.

**Wdrażanie maszyny Wirtualnej za pomocą programu PowerShell**

Aby wdrożyć dużą maszynę Wirtualną z nowo utworzonego, uogólnionego obrazu maszyny Wirtualnej, można użyć następujących poleceń cmdlet.

    $img = Get-AzureVMImage -ImageName "myVMImage"
    $user = "user123"
    $pass = "adminPassword123"
    $myVM = New-AzureVMConfig -Name "VMImageVM" -InstanceSize "Large" -ImageName $img.ImageName | Add-AzureProvisioningConfig -Windows -AdminUsername $user -Password $pass
    New-AzureVM -ServiceName "VMImageCloudService" -VMs $myVM -Location "West US" -WaitForBoot

> [!IMPORTANT]
> Aby uzyskać dodatkową pomoc, zapoznaj się z [Rozwiązywanie typowych problemów napotykanych podczas tworzenia wirtualnego dysku twardego].
>
>

## <a name="5-obtain-certification-for-your-vm-image"></a>5. Uzyskiwanie certyfikacji obrazu maszyny Wirtualnej
Następny etap przygotowywania obrazu maszyny Wirtualnej w portalu Azure Marketplace jest ona certyfikat.

Ten proces obejmuje uruchamianie specjalnego narzędzia certyfikacji, przekazanie wyników weryfikacji do kontenera platformy Azure, w którym znajdują się Twoje wirtualne dyski twarde, dodanie oferty, zdefiniowanie jednostki SKU i przesyłanie obrazu maszyny Wirtualnej do certyfikacji.

### <a name="51-download-and-run-the-certification-test-tool-for-azure-certified"></a>5.1 Pobierz i uruchom narzędzie Test certyfikacji do certyfikatu platformy Azure
Narzędzie certyfikacji działa na uruchomioną maszynę Wirtualną, udostępnioną za pomocą obrazu maszyny Wirtualnej użytkownika, aby upewnić się, że obraz maszyny Wirtualnej jest zgodna z platformą Microsoft Azure. Weryfikowane jest spełnienie wytycznych oraz wymagań dotyczących przygotowywania wirtualnego dysku twardego. Wynikiem pracy narzędzia jest raport zgodności, które powinny zostać przekazane w portalu wydawców podczas żądanie certyfikacji.

Narzędzia certyfikacji można używać zarówno Windows, jak i maszyny wirtualne systemu Linux. Jego nawiązanie połączenia z systemem Windows z maszyn wirtualnych za pomocą programu PowerShell i nawiązanie połączenia z maszyn wirtualnych systemu Linux przy użyciu biblioteki SSH.Net:

1. Najpierw Pobierz narzędzie certyfikacji z [witryny pobierania firmy Microsoft][link-msft-download].
2. Otwórz narzędzie certyfikacji, a następnie kliknij przycisk **Rozpocznij nowy Test** przycisku.
3. Z **informacji dotyczących testu** ekranu, wprowadź nazwę dla przebiegu testu.
4. Wybierz system operacyjny maszyny wirtualnej (Linux lub Windows). W zależności od wybranej opcji określ kolejne opcje.

### <a name="connect-the-certification-tool-to-a-linux-vm-image"></a>**Połącz narzędzie certyfikacji obrazu maszyny Wirtualnej systemu Linux**
1. Wybierz tryb uwierzytelniania SSH: za pomocą hasła lub pliku klucza.
2. Jeśli przy użyciu uwierzytelniania opartego na hasłach, wprowadź nazwę systemu nazw domen (DNS, Domain Name System), nazwę użytkownika i hasło.
3. Jeśli przy użyciu uwierzytelniania opartego na pliku klucza, wprowadź nazwę DNS, nazwę użytkownika i lokalizację klucza prywatnego.

   ![Uwierzytelnianie hasłem obrazu maszyny Wirtualnej systemu Linux][img-cert-vm-pswd-lnx]

   ![Plik klucza uwierzytelniania obrazu maszyny Wirtualnej z systemem Linux][img-cert-vm-key-lnx]

### <a name="connect-the-certification-tool-to-a-windows-based-vm-image"></a>**Połącz narzędzie certyfikacji obrazem maszyny Wirtualnej z systemem Windows**
1. Wprowadź w pełni kwalifikowana nazwa DNS maszyny Wirtualnej (na przykład MyVMName.Cloudapp.net).
2. Wprowadź nazwę użytkownika i hasło.

   ![Hasło uwierzytelniania Windows obrazu maszyny Wirtualnej][img-cert-vm-pswd-win]

Po wybraniu poprawnych opcji dla obrazu systemu Linux lub maszyny Wirtualnej systemu Windows wybierz **Testuj połączenie** aby upewnić się, że biblioteki SSH.Net lub programu PowerShell ma prawidłowe połączenie do celów testowych. Po nawiązaniu połączenia wybierz **dalej** aby rozpocząć test.

Po ukończeniu testu zostaną wyświetlone wyniki (powodzenie/niepowodzenie/ostrzeżenie) dla każdego elementu testu.

![Przypadki testowe dla obrazu maszyny Wirtualnej z systemem Linux][img-cert-vm-test-lnx]

![Przypadki testowe dla obrazu maszyny Wirtualnej Windows][img-cert-vm-test-win]

Jeśli którykolwiek z testów nie powiedzie się, obraz nie zostanie potwierdzone. Jeśli wystąpi ten problem, zapoznaj się z wymaganiami, a następnie wprowadź niezbędne zmiany.

Po zautomatyzowany test prośba o podanie dodatkowych danych wejściowych w obrazie maszyny Wirtualnej za pomocą ekranu kwestionariusza.  Wykonaj pytań, a następnie wybierz **dalej**.

![Kwestionariusz narzędzie certyfikacji][img-cert-vm-questionnaire]

![Kwestionariusz narzędzie certyfikacji][img-cert-vm-questionnaire-2]

Po zakończeniu kwestionariusz, możesz podać dodatkowe informacje, takie jak informacje dostęp SSH dla maszyny Wirtualnej systemu Linux, obrazu i wyjaśnienia dla dowolnej oceny nie powiodło się. Oprócz odpowiedzi na kwestionariusz, możesz pobrać wyniki testów i pliki dziennika dla przypadków testowych, wykonane. Zapisz wyniki w tym samym kontenerze co Twoje dyski VHD.

![Zapisz certyfikacji wyników testu][img-cert-vm-results]

### <a name="52-get-the-shared-access-signature-uri-for-your-vm-images"></a>5.2 pobieranie identyfikatora URI sygnatury dostępu współdzielonego dla obrazów maszyn wirtualnych
W procesie publikowania należy określić identyfikatory uniform resource identifier (URI), które mogą prowadzić do każdego z wirtualnych dysków twardych, zostały utworzone dla danej jednostki SKU. Dostęp do tych wirtualnych dysków twardych jest potrzebny firmie Microsoft w procesie certyfikacji. W związku z tym należy utworzyć identyfikator URI sygnatury dostępu współdzielonego dla poszczególnych wirtualnych dysków Twardych. Ten identyfikator URI powinny być wprowadzane w **obrazów** karta w portalu wydawców.

Sygnatura dostępu współdzielonego, utworzonych przez identyfikator URI powinien spełniać następujące wymagania:

Poniższe instrukcje mają zastosowanie tylko w przypadku dysków niezarządzanych, które są jedynym rodzajem obsługiwane.

* Generowanie sygnatury dostępu współdzielonego identyfikatorów URI dla generowanych, uprawnienia do wyświetlania i odczytu są wystarczające. Nie należy przydzielać uprawnień do zapisywania ani usuwania.
* Czas trwania dla dostępu powinien być co najmniej trzech (3) tygodni od utworzenia identyfikatora URI sygnatury dostępu współdzielonego.
* Aby chronić czasu UTC, wybierz dzień przed bieżącą datą. Na przykład jeśli bieżąca data to 6 października 2014 roku, wybierz 2014-10-5.

Adres URL sygnatury dostępu Współdzielonego można wygenerować na wiele sposobów na udostępnianie wirtualnego dysku twardego dla witryny Azure Marketplace.
Poniżej przedstawiono trzy zalecanych narzędzi:

1.  Eksplorator usługi Azure Storage
2.  Eksplorator usługi Storage firmy Microsoft
3.  Interfejs wiersza polecenia platformy Azure

**Eksplorator usługi Azure Storage (zalecane dla użytkowników Windows)**

Poniżej przedstawiono kroki do generowania adresu URL sygnatury dostępu Współdzielonego za pomocą Eksploratora usługi Azure Storage

1. Pobierz [usługi Azure Storage Explorer 6 (wersja zapoznawcza) 3](https://azurestorageexplorer.codeplex.com/) z witryny CodePlex. Przejdź do [usługi Azure Storage Explorer 6 w wersji zapoznawczej](https://azurestorageexplorer.codeplex.com/) i kliknij przycisk **"Pobierania"**.

    ![Rysowanie](media/marketplace-publishing-vm-image-creation/img5.2_01.png)

2. Pobierz [AzureStorageExplorer6Preview3.zip](https://azurestorageexplorer.codeplex.com/downloads/get/891668) i zainstaluj po rozpakowaniu go.

    ![Rysowanie](media/marketplace-publishing-vm-image-creation/img5.2_02.png)

3. Po jego zainstalowaniu, Otwórz aplikację.
4. Kliknij przycisk **Dodaj konto**.

    ![Rysowanie](media/marketplace-publishing-vm-image-creation/img5.2_03.png)

5. Określ nazwę konta magazynu, klucz konta magazynu i domena punktów końcowych magazynu. Jest to konto magazynu w ramach subskrypcji platformy Azure, gdzie wirtualnego dysku twardego mają być przechowywane w witrynie Azure portal.

    ![Rysowanie](media/marketplace-publishing-vm-image-creation/img5.2_04.png)

6. Gdy Eksplorator usługi Azure Storage jest połączony z konkretnym kontem magazynu, rozpocznie wyświetlanie wszystkich zawiera w ramach konta magazynu. Wybierz kontener, w której został skopiowany plik VHD dysku systemu operacyjnego (także dysków danych, jeśli są one odpowiednie dla danego scenariusza).

    ![Rysowanie](media/marketplace-publishing-vm-image-creation/img5.2_05.png)

7. Po wybraniu kontenera obiektów blob, Azure Storage Explorer zacznie pliki znajdujące się w kontenerze. Wybierz plik obrazu (VHD), który należy do przesłania.

    ![Rysowanie](media/marketplace-publishing-vm-image-creation/img5.2_06.png)

8.  Po wybraniu pliku VHD w kontenerze, kliknij przycisk **zabezpieczeń** kartę.

    ![Rysowanie](media/marketplace-publishing-vm-image-creation/img5.2_07.png)

9.  W **zabezpieczenia kontenerów obiektów Blob** okna dialogowego pozostaw wartości domyślne na **poziom dostępu** kartę, a następnie kliknij przycisk **sygnatur dostępu współdzielonego** kartę.

    ![Rysowanie](media/marketplace-publishing-vm-image-creation/img5.2_08.png)

10. Wykonaj poniższe kroki, aby wygenerować identyfikator URI sygnatury dostępu współdzielonego dla obrazu vhd:

    ![Rysowanie](media/marketplace-publishing-vm-image-creation/img5.2_09.png)

    a. **Dozwolony dostęp z:** Aby chronić czasu UTC, wybierz dzień przed bieżącą datą. Na przykład jeśli bieżąca data to 6 października 2014 roku, wybierz 2014-10-5.

    b. **Dozwolony dostęp do:** Wybierz datę, która jest co najmniej trzy tygodnie po **dozwolony dostęp z** daty.

    c. **Dozwolone akcje:** Wybierz **listy** i **odczytu** uprawnienia.

    d. Jeśli wybrano plik VHD poprawnie, a następnie plik, który pojawia się w **nazwa obiektu Blob w celu uzyskania dostępu do** za pomocą rozszerzenie VHD.

    e. Kliknij przycisk **generowania podpisu**.

    f. W **generowane udostępnione podpisu identyfikatora URI dostępu tego kontenera**, sprawdź następujące czynności, jak podkreślono powyżej:

       - Upewnij się, że nazwa pliku obrazu i **".vhd"** znajdują się w identyfikatorze URI.
       - Upewnij się, że na końcu podpis **"= rl"** pojawia się. Ta wartość pokazuje dostęp do odczytu i listy podano pomyślnie.
       - Upewnij się, że w środkowej części podpisu **"sr = c"** pojawia się. Ta wartość pokazuje, że masz dostęp na poziomie kontenera

11. Aby sprawdzić, wygenerowany udostępnione działania identyfikator URI podpisu dostępu, kliknij pozycję **Testuj w przeglądarce**. Należy go uruchomić proces pobierania.

12. Kopiowanie identyfikatora URI sygnatury dostępu współdzielonego. Wklej ten identyfikator URI do publikowania w portalu.

13. Powtórz kroki od 6 do 10 dla poszczególnych wirtualnych dysków Twardych w ramach jednostki SKU.

**Microsoft Azure Storage Explorer (Windows/MAC/Linux)**

Poniżej przedstawiono kroki do generowania adresu URL sygnatury dostępu Współdzielonego za pomocą Eksploratora usługi Microsoft Azure Storage

1.  Pobierz Eksplorator usługi Microsoft Azure Storage formularza [ http://storageexplorer.com/ ](http://storageexplorer.com/) witryny sieci Web. Przejdź do [Microsoft Azure Storage Explorer](http://storageexplorer.com/releasenotes.html) i kliknij przycisk **"Pobierz dla Windows"**.

    ![Rysowanie](media/marketplace-publishing-vm-image-creation/img5.2_10.png)

2.  Po jego zainstalowaniu, Otwórz aplikację.

3.  Kliknij przycisk **Dodaj konto**.

4.  Konfigurowanie systemu Microsoft Azure Storage Explorer do Twojej subskrypcji, zaloguj się do swojego konta

    ![Rysowanie](media/marketplace-publishing-vm-image-creation/img5.2_11.png)

5.  Przejdź do konta magazynu i wybierz kontener

6.  Wybierz **"Pobierz podpis dostępu do udziału."** za pomocą kliknij prawym przyciskiem myszy elementu **kontenera**

    ![Rysowanie](media/marketplace-publishing-vm-image-creation/img5.2_12.png)

7.  Godzina rozpoczęcia aktualizacji, czas wygaśnięcia i uprawnienia, zgodnie z następujących czynności

    ![Rysowanie](media/marketplace-publishing-vm-image-creation/img5.2_13.png)

    a.  **Godzina rozpoczęcia:** Aby chronić czasu UTC, wybierz dzień przed bieżącą datą. Na przykład jeśli bieżąca data to 6 października 2014 roku, wybierz 2014-10-5.

    b.  **Czas wygaśnięcia:** Wybierz datę, która jest co najmniej trzy tygodnie po **czas rozpoczęcia** daty.

    c.  **Uprawnienia:** Wybierz **listy** i **odczytu** uprawnień

8.  Kopiowanie identyfikatora URI sygnatury dostępu współdzielonego kontenera

    ![Rysowanie](media/marketplace-publishing-vm-image-creation/img5.2_14.png)

    Wygenerowany adres URL sygnatury dostępu Współdzielonego dotyczy kontener poziom i teraz należy dodać nazwę wirtualnego dysku twardego w nim.

    Format adresu URL SAS poziomu kontenera: `https://testrg009.blob.core.windows.net/vhds?st=2016-04-22T23%3A05%3A00Z&se=2016-04-30T23%3A05%3A00Z&sp=rl&sv=2015-04-05&sr=c&sig=J3twCQZv4L4EurvugRW2klE2l2EFB9XyM6K9FkuVB58%3D`

    Wstaw nazwę wirtualnego dysku twardego po nazwie kontenera w adresie URL sygnatury dostępu Współdzielonego zgodnie z poniższymi instrukcjami `https://testrg009.blob.core.windows.net/vhds/<VHD NAME>?st=2016-04-22T23%3A05%3A00Z&se=2016-04-30T23%3A05%3A00Z&sp=rl&sv=2015-04-05&sr=c&sig=J3twCQZv4L4EurvugRW2klE2l2EFB9XyM6K9FkuVB58%3D`

    Przykład:

    ![Rysowanie](media/marketplace-publishing-vm-image-creation/img5.2_15.png)

    TestRGVM201631920152.vhd jest nazwa wirtualnego dysku twardego, a następnie będzie adres URL sygnatury dostępu Współdzielonego dysku VHD `https://testrg009.blob.core.windows.net/vhds/TestRGVM201631920152.vhd?st=2016-04-22T23%3A05%3A00Z&se=2016-04-30T23%3A05%3A00Z&sp=rl&sv=2015-04-05&sr=c&sig=J3twCQZv4L4EurvugRW2klE2l2EFB9XyM6K9FkuVB58%3D`

    - Upewnij się, że nazwa pliku obrazu i **".vhd"** znajdują się w identyfikatorze URI.
    - Upewnij się, że w środkowej części podpisu **"sp = rl"** pojawia się. Ta wartość pokazuje dostęp do odczytu i listy podano pomyślnie.
    - Upewnij się, że w środkowej części podpisu **"sr = c"** pojawia się. Ta wartość pokazuje, że masz dostęp na poziomie kontenera

9.  Aby upewnić się, wygenerowany udostępnione działania identyfikator URI podpisu dostępu, należy przetestować ją w przeglądarce. Należy go uruchomić proces pobierania

10. Kopiowanie identyfikatora URI sygnatury dostępu współdzielonego. Wklej ten identyfikator URI do publikowania w portalu.

11. Powtórz te czynności dla każdego wirtualnego dysku twardego w ramach jednostki SKU.

**Azure CLI 2.0 (zalecane dla innych niż Windows i ciągłej integracji)**

Poniżej przedstawiono kroki do generowania adresu URL sygnatury dostępu Współdzielonego za pomocą klasycznego wiersza polecenia platformy Azure

[!INCLUDE [outdated-cli-content](../../includes/contains-classic-cli-content.md)]

1.  Pobierz interfejs wiersza polecenia Microsoft Azure, z [tutaj](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). Możesz również znaleźć różne linki dla **[Windows](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?view=azure-cli-latest)** i  **[systemu MAC OS](https://docs.microsoft.com/cli/azure/install-azure-cli-macos?view=azure-cli-latest)**.

2.  Po jej pobraniu instalacji tego narzędzia.

3.  Tworzenie powłoki Bash (lub innego pliku wykonywalnego, skrypt równoważne) pliku następującym kodem i zapisz go lokalnie

        export AZURE_STORAGE_ACCOUNT=<Storage Account Name>
        EXPIRY=$(date -d "3 weeks" '+%Y-%m-%dT%H:%MZ')
        CONTAINER_SAS=$(az storage container generate-sas --account-name -n vhds --permissions rl --expiry $EXPIRY -otsv)
        BLOB_URL=$(az storage blob url -c vhds -n <VHD Blob Name> -otsv)
        echo $BLOB_URL\?$CONTAINER_SAS

    Zaktualizuj następujące parametry w powyżej

    a. **`<Storage Account Name>`**: Nadaj nazwę konta magazynu

    b. **`<VHD Blob Name>`**: Nadaj nazwę obiektu blob wirtualnego dysku twardego.

    Wybierz datę, która jest co najmniej trzy tygodnie po dacie rozpoczęcia (wartość domyślna to generowania tokenu sygnatury dostępu współdzielonego). Przykładowa wartość to: `2018-10-11T23:56Z`.

    Poniżej przedstawiono przykładowy kod po aktualizowania odpowiednich parametrów wyeksportować AZURE_STORAGE_ACCOUNT = vhdstorage1ba78dfb6bc2d8 wygaśnięcia = $(Data -d "trzy tygodnie" "+ %T %m % dT % H: % MZ") CONTAINER_SAS = $(az storage kontenera Generowanie sygnatury dostępu współdzielonego - n wirtualne dyski twarde — uprawnienia rl-- wygaśnięcia $EXPIRY - otsv) BLOB_URL = $(az storage blob url - c wirtualne dyski twarde - n osdisk_1ba78dfb6b.vhd - otsv) echo $BLOB_URL\?$CONTAINER_SAS

4.  Uruchom skrypt i zostanie w niej podana adresu URL sygnatury dostępu Współdzielonego dla poziomu dostępu do kontenera.

5.  Sprawdź adres URL sygnatury dostępu Współdzielonego.

    - Upewnij się, że nazwa pliku obrazu i ".vhd" są w identyfikatorze URI.
    -   Upewnij się, że w środkowej części podpisu "sp = rl" pojawia się. Ta wartość pokazuje dostęp do odczytu i listy podano pomyślnie.
    -   Upewnij się, że w środkowej części podpisu "sr = c" zostanie wyświetlony. Oznacza to, że masz dostęp na poziomie kontenera

    Przykład:

    `https://vhdstorage1ba78dfb6bc2d8.blob.core.windows.net/vhds/osdisk_1ba78dfb6b.vhd?se=2018-10-12T00%3A04Z&sp=rl&sv=2018-03-28&sr=c&sig=...`

8.  Aby upewnić się, wygenerowany udostępnione działania identyfikator URI podpisu dostępu, należy przetestować ją w przeglądarce. Należy go uruchomić proces pobierania

9.  Kopiowanie identyfikatora URI sygnatury dostępu współdzielonego. Wklej ten identyfikator URI do publikowania w portalu.

10. Powtórz te czynności dla każdego wirtualnego dysku twardego w ramach jednostki SKU.


### <a name="53-provide-information-about-the-vm-image-and-request-certification-in-the-publishing-portal"></a>5.3 zawierają informacje dotyczące obrazu maszyny Wirtualnej i żądanie certyfikacji w portalu wydawców
Po utworzeniu oferty i jednostki SKU należy wprowadzić szczegółowe informacje dotyczące obrazu z tej jednostki SKU:

1. Przejdź do [portalu wydawców][link-pubportal], a następnie zaloguj się przy użyciu konta sprzedawcy.
2. Wybierz **obrazów maszyn wirtualnych** kartę.
3. Identyfikator wyświetlany u góry strony jest faktycznie oferty, a nie identyfikatora jednostki SKU.
4. Wypełnij właściwości w obszarze **jednostki SKU** sekcji.
5. W obszarze **Rodzina systemów operacyjnych**, kliknij typ systemu operacyjnego skojarzony z wirtualnego dysku twardego systemu operacyjnego.
6. W **systemu operacyjnego** pole, wprowadź opis systemu operacyjnego. Należy wziąć pod uwagę formatu: rodzina systemów operacyjnych, typ, wersja i aktualizacje. Przykładem jest "Systemu Windows Server Datacenter 2014 R2".
7. Wybierz maksymalnie sześć zalecane rozmiary maszyn wirtualnych. Te rozmiary są zalecenia mające pobrać wyświetlane z klientem w bloku warstwa cenowa w portalu Azure, gdy będą rozważać zakup i wdrożenie Twojego obrazu. **Te wartości są jedynie zaleceniami. Klient jest w stanie wybrać dowolny rozmiar maszyny Wirtualnej, który obsługuje dyski określone w obrazie.**
8. Wprowadź informacje o wersji. Pole wersji hermetyzuje semantycznego wersji do identyfikowania produktu i jego aktualizacje:
   * Wersje powinny być w postaci X.Y.Z, gdzie X, Y i Z są liczbami całkowitymi.
   * Obrazy w innych jednostkach SKU może mieć różne wersje główne i pomocnicze.
   * Wersje w ramach jednostki SKU należy tylko zmiany przyrostowe, które zwiększają wersja poprawki zabezpieczeń (Z z X.Y.Z).
9. W **adres URL wirtualnego dysku twardego systemu operacyjnego** wprowadź sygnatury dostępu współdzielonego, identyfikator URI utworzony dla wirtualnego dysku twardego systemu operacyjnego.
10. W przypadku dysków danych skojarzonych z tej jednostki SKU, wybierz numer jednostki logicznej (LUN), do którego chcesz ten dysk danych ma zostać zainstalowany po wdrożeniu.
11. W **adres URL wirtualnego dysku twardego X jednostki LUN** wprowadź identyfikator URI utworzony dla pierwszego dysku VHD danych sygnatura dostępu współdzielonego.

    ![Rysowanie](media/marketplace-publishing-vm-image-creation/vm-image-pubportal-skus-3.png)


## <a name="common-sas-url-issues--fixes"></a>Typowe problemy z adresu URL sygnatury dostępu Współdzielonego i poprawki

|Problem|Komunikat o błędzie|Napraw|Link do dokumentacji|
|---|---|---|---|
|Wystąpił błąd podczas kopiowania obrazy — "?" nie znajduje się w adresie url sygnatury dostępu Współdzielonego|Błąd: Kopiowanie obrazów. Nie można pobrać obiekt blob przy użyciu podany identyfikator Uri sygnatury dostępu Współdzielonego.|Zaktualizować adres Url sygnatury dostępu Współdzielonego za pomocą zalecane narzędzia|[https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/)|
|Wystąpił błąd podczas kopiowania obrazy - parametrów "st" i "se" nie w url sygnatury dostępu Współdzielonego|Błąd: Kopiowanie obrazów. Nie można pobrać obiekt blob przy użyciu podany identyfikator Uri sygnatury dostępu Współdzielonego.|Zaktualizuj adres Url sygnatury dostępu Współdzielonego za pomocą daty rozpoczęcia i zakończenia na nim|[https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/)|
|Wystąpił błąd podczas kopiowania obrazów — "sp = rl" nie w url sygnatury dostępu Współdzielonego|Błąd: Kopiowanie obrazów. Nie można pobrać obiektu blob przy użyciu podany identyfikator Uri sygnatury dostępu Współdzielonego|Zaktualizuj adres Url sygnatury dostępu Współdzielonego z uprawnieniami ustawionymi jako "Odczyt" i "Lista|[https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/)|
|Wystąpił błąd podczas kopiowania obrazów — adres url sygnatury dostępu Współdzielonego mają białych znaków w nazwie wirtualnego dysku twardego|Błąd: Kopiowanie obrazów. Nie można pobrać obiekt blob przy użyciu podany identyfikator Uri sygnatury dostępu Współdzielonego.|Aktualizacja adresu Url sygnatury dostępu Współdzielonego bez białych znaków|[https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/)|
|Wystąpił błąd podczas kopiowania obrazów — błąd Autoryzacja adresów Url sygnatury dostępu Współdzielonego|Błąd: Kopiowanie obrazów. Nie można pobrać obiektu blob ze względu na błąd autoryzacji|Ponowne generowanie adresu Url sygnatury dostępu Współdzielonego|[https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/)|
|Wystąpił błąd podczas kopiowania obrazów — adres Url sygnatury dostępu Współdzielonego "st" i "se" parametry bez specyfikacji pełnej daty i godziny|Błąd: Kopiowanie obrazów. Nie można pobrać obiektu blob ze względu na nieprawidłowy adres Url sygnatury dostępu Współdzielonego |Uruchom adres Url sygnatury dostępu Współdzielonego i Data zakończenia parametry ("st", "se") muszą mieć specyfikację pełnej daty i godziny, takich jak 11-02-2017T00:00:00Z, a nie tylko datę lub skróconą wersji po raz. Użytkownik może wystąpić ten scenariusz przy użyciu wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Pamiętaj podać specyfikację pełnej daty i godziny lub ponownie wygenerować adresu Url sygnatury dostępu Współdzielonego.|[https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/)|

## <a name="next-step"></a>Następny krok
Po wykonaniu szczegółów jednostki SKU, możesz przejść do [portalu Azure Marketplace Przewodnik po zawartości marketingowej][link-pushstaging]. W tym kroku proces publikowania, zapewniasz zawartości marketingowej, cennik i inne informacje, które są niezbędne w systemach wcześniejszych niż **krok 3: Testowanie maszyny Wirtualnej oferty w środowisku tymczasowym**, który testować różne scenariusze przypadków użycia przed wdrożeniem tej oferty w portalu Azure Marketplace dla publicznej widoczności i zakupu.  

## <a name="see-also"></a>Zobacz także
* [Wprowadzenie: Jak opublikować ofertę w portalu Azure Marketplace](marketplace-publishing-getting-started.md)

[img-acom-1]:media/marketplace-publishing-vm-image-creation/vm-image-acom-datacenter.png
[img-portal-vm-size]:media/marketplace-publishing-vm-image-creation/vm-image-portal-size.png
[img-portal-vm-create]:media/marketplace-publishing-vm-image-creation/vm-image-portal-create-vm.png
[img-portal-vm-location]:media/marketplace-publishing-vm-image-creation/vm-image-portal-location.png
[img-portal-vm-rdp]:media/marketplace-publishing-vm-image-creation/vm-image-portal-rdp.png
[img-azstg-add]:media/marketplace-publishing-vm-image-creation/vm-image-storage-add.png
[img-manage-vm-new]:media/marketplace-publishing-vm-image-creation/vm-image-manage-new.png
[img-manage-vm-select]:media/marketplace-publishing-vm-image-creation/vm-image-manage-select.png
[img-cert-vm-key-lnx]:media/marketplace-publishing-vm-image-creation/vm-image-certification-keyfile-linux.png
[img-cert-vm-pswd-lnx]:media/marketplace-publishing-vm-image-creation/vm-image-certification-password-linux.png
[img-cert-vm-pswd-win]:media/marketplace-publishing-vm-image-creation/vm-image-certification-password-win.png
[img-cert-vm-test-lnx]:media/marketplace-publishing-vm-image-creation/vm-image-certification-test-sample-linux.png
[img-cert-vm-test-win]:media/marketplace-publishing-vm-image-creation/vm-image-certification-test-sample-win.png
[img-cert-vm-results]:media/marketplace-publishing-vm-image-creation/vm-image-certification-results.png
[img-cert-vm-questionnaire]:media/marketplace-publishing-vm-image-creation/vm-image-certification-questionnaire.png
[img-cert-vm-questionnaire-2]:media/marketplace-publishing-vm-image-creation/vm-image-certification-questionnaire-2.png
[img-pubportal-vm-skus]:media/marketplace-publishing-vm-image-creation/vm-image-pubportal-skus.png
[img-pubportal-vm-skus-2]:media/marketplace-publishing-vm-image-creation/vm-image-pubportal-skus-2.png

[link-pushstaging]:marketplace-publishing-push-to-staging.md
[link-github-waagent]:https://github.com/Azure/WALinuxAgent
[link-azure-codeplex]:https://azurestorageexplorer.codeplex.com/
[link-azure-2]:../storage/blobs/storage-dotnet-shared-access-signature-part-2.md
[link-azure-1]:../storage/common/storage-dotnet-shared-access-signature-part-1.md
[link-msft-download]:http://www.microsoft.com/download/details.aspx?id=44299
[link-technet-3]:https://technet.microsoft.com/library/hh846766.aspx
[link-technet-2]:https://msdn.microsoft.com/library/dn495261.aspx
[link-azure-portal]:https://portal.azure.com
[link-pubportal]:https://publish.windowsazure.com
[link-acct-creation]:marketplace-publishing-accounts-creation-registration.md
[link-technet-1]:https://technet.microsoft.com/library/hh848454.aspx
[link-azure-vm-2]:./virtual-machines-linux-agent-user-guide/
[link-openssl]:https://www.openssl.org/
[link-intsvc]:http://www.microsoft.com/download/details.aspx?id=41554
[link-python]:https://www.python.org/
