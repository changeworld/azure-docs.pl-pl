---
title: Tworzenie obrazu maszyny wirtualnej dla portalu Azure Marketplace | Dokumentacja firmy Microsoft
description: Szczegółowe instrukcje dotyczące sposobu tworzenia obrazu maszyny wirtualnej do portalu Azure Marketplace innym osobom do zakupu.
services: Azure Marketplace
documentationcenter: ''
author: msmbaldwin
manager: mbaldwin
editor: ''
ms.assetid: 5c937b8e-e28d-4007-9fef-624046bca2ae
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Azure
ms.workload: na
ms.date: 01/05/2017
ms.author: mbaldwin
ms.openlocfilehash: ad6d48a03575e8fabd7eed2ebc1f7926ec4559d4
ms.sourcegitcommit: 6cf20e87414dedd0d4f0ae644696151e728633b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/06/2018
ms.locfileid: "34808744"
---
# <a name="guide-to-create-a-virtual-machine-image-for-the-azure-marketplace"></a>Przewodnik dotyczący tworzenia obrazu maszyny wirtualnej do portalu Azure Marketplace
W tym artykule **krok 2**, przeprowadzi Cię przez przygotowanie wirtualnych dysków twardych (VHD), które zostaną wdrożone w portalu Azure Marketplace. Dyski VHD są podstawę sieci jednostki SKU. Proces jest różny w zależności od tego, czy udostępniasz SKU opartych na systemie Linux lub z systemem Windows. W tym artykule przedstawiono oba scenariusze. Ten proces można przeprowadzić równolegle z [o tworzeniu konta i rejestracji][link-acct-creation].

## <a name="1-define-offers-and-skus"></a>1. Zdefiniuj oferty i jednostki SKU
W tej sekcji dowiesz się zdefiniować oferty i ich skojarzonych jednostki SKU.

Oferta pełni rolę nadrzędną względem wszystkich swoich jednostek SKU. Można określić wiele ofert. Decyzja dotycząca określenia struktury ofert należy do Ciebie. Wypchnięcie oferty do wdrażania przejściowego jest równoznaczne z wypchnięciem wszystkich jej jednostek SKU. Zastanów się uważnie z identyfikatorów jednostki SKU, ponieważ będą one widoczne w adresie URL:

* Azure.com: http://azure.microsoft.com/marketplace/partners/{PartnerNamespace}/{OfferIdentifier}-{SKUidentifier}
* Portal Azure w wersji zapoznawczej: https://portal.azure.com/#gallery/{PublisherNamespace}.{OfferIdentifier}{SKUIDdentifier}  

Jednostka SKU jest komercyjnych nazwę obrazu maszyny Wirtualnej. Obraz maszyny Wirtualnej zawiera jeden system operacyjny dysku oraz zero lub więcej dysków z danymi. Zasadniczo stanowi on kompletny profil magazynu dla maszyny wirtualnej. Jeden wirtualny dysk twardy jest wymagany na dysku. Dysków z danymi nawet puste wymagane do utworzenia dysku VHD.

Niezależnie od używanego systemu operacyjnego należy dodać minimalną liczbę dysków z danymi, które są wymagane przez jednostkę SKU. Nie można usunąć dysków, które są częścią obrazu w czasie wdrażania klientów, ale można dodać dysków podczas lub po wdrożeniu, jeśli to konieczne.

> [!IMPORTANT]
> **Nie należy zmieniać liczba dysków w nowej wersji obrazu.** Jeśli należy zmienić konfigurację dysków z danymi w obrazie, należy zdefiniować nowe jednostki SKU. Publikowania nowej wersji obrazu z liczbą inny dysk będzie mieć możliwości podziału nowego wdrożenia na podstawie nowej wersji obrazu w przypadku wdrożeń automatyczne skalowanie, automatyczne rozwiązań za pomocą szablonów ARM i innych scenariuszy.
>
>

### <a name="11-add-an-offer"></a>1.1. Dodaj ofertę
1. Zaloguj się do [Portal publikowania] [ link-pubportal] przy użyciu konta sprzedaży.
2. Wybierz **maszyn wirtualnych** kartę Portal publikowania. W polu zostanie wyświetlony monit o wpisu wprowadź nazwę oferty. Nazwa oferty jest zwykle nazwa produktu lub usługi, które mają zostać sprzedawać w witrynie Azure Marketplace.
3. Wybierz pozycję **Utwórz**.

### <a name="12-define-a-sku"></a>1.2 zdefiniować jednostki SKU
Po dodaniu oferty, należy zdefiniować i identyfikuje użytkownika jednostki SKU. Może mieć wielu ofert, a każdy oferta może mieć wiele jednostek SKU w nim. Wypchnięcie oferty do wdrażania przejściowego jest równoznaczne z wypchnięciem wszystkich jej jednostek SKU.

1. **Dodaj jednostki SKU.** Jednostka SKU wymaga identyfikatora, który jest używany w adresie URL. Identyfikator musi być unikatowa w ramach profilu publikowania, ale nie występuje ryzyko kolizji identyfikator z innych wydawców.

   > [!NOTE]
   > Oferta i identyfikatory SKU są wyświetlane w adresie URL oferta w witrynie Marketplace.
   >
   >
2. **Dodaj opis podsumowujący dla Twojej wersji produktu.** Podsumowanie opisy są widoczne dla klientów, dlatego należy utworzyć je łatwo odczytać. Te informacje nie musi być zablokowany do momentu fazy "Push do przemieszczania". Można je do tego czasu swobodnie edytować.
3. Jeśli używasz jednostek SKU opartych na systemie Windows, użyj podanych linków, aby uzyskać zatwierdzone wersje systemu Windows Server.

## <a name="2-create-an-azure-compatible-vhd-linux-based"></a>2. Tworzenie wirtualnego dysku twardego zgodnego Azure (opartych na systemie Linux)
Ta sekcja dotyczy przede wszystkim najlepsze rozwiązania dotyczące tworzenia obrazu maszyny Wirtualnej opartych na systemie Linux dla portalu Azure Marketplace. Przewodnik krok po kroku, można znaleźć w następującej dokumentacji: [utworzyć niestandardowy obraz maszyny Wirtualnej systemu Linux](../virtual-machines/linux/create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="3-create-an-azure-compatible-vhd-windows-based"></a>3. Tworzenie wirtualnego dysku twardego zgodnego Azure (z systemem Windows)
W tej części przedstawiono kroki, aby utworzyć SKU, oparte na systemie Windows Server do portalu Azure Marketplace.

### <a name="31-ensure-that-you-are-using-the-correct-base-vhds"></a>3.1 Upewnij się, że używasz prawidłowe wirtualne dyski twarde podstawowej
Wirtualny dysk twardy systemu operacyjnego dla obrazu maszyny Wirtualnej musi być oparta na obrazu podstawową zatwierdzony Azure zawierającego systemu Windows Server lub SQL Server.

Aby rozpocząć, utwórz maszynę Wirtualną z jednego z następujących obrazów w lokalizacji [portalu Microsoft Azure][link-azure-portal]:

* Windows Server ([2012 R2 Datacenter][link-datactr-2012-r2], [2012 Datacenter][link-datactr-2012], [2008 R2 z dodatkiem SP1][link-datactr-2008-r2])
* Program SQL Server 2014 ([Enterprise][link-sql-2014-ent], [standardowe][link-sql-2014-std], [Web][link-sql-2014-web])
* SQL Server 2012 z dodatkiem SP2 ([Enterprise][link-sql-2012-ent], [standardowe][link-sql-2012-std], [Web][link-sql-2012-web])

Te linki można również znaleźć w Portalu wydawców na stronie Jednostka SKU.

> [!TIP]
> Jeśli używasz bieżącego portalu platformy Azure lub programu PowerShell są zatwierdzane obrazów systemu Windows Server, opublikowane w 8 września 2014 lub nowszy.
>
>

### <a name="32-create-your-windows-based-vm"></a>3.2 Tworzenie maszyny Wirtualnej z systemem Windows
W portalu Microsoft Azure można utworzyć maszyny Wirtualnej na podstawie zatwierdzonych obrazu podstawowego w kilku prostych krokach. Poniżej przedstawiono omówienie procesu:

1. Na stronie obrazu podstawowego wybierz **Utwórz maszynę wirtualną** będą kierowane do nowego [portalu Microsoft Azure][link-azure-portal].

    ![Rysowanie][img-acom-1]
2. Zaloguj się do portalu konta Microsoft i hasło dla subskrypcji platformy Azure, którego chcesz użyć.
3. Postępuj zgodnie z monitami, aby utworzyć Maszynę wirtualną przy użyciu wybranego obrazu podstawowego. Należy zapewnić host name (nazwa komputera), nazwę użytkownika (zarejestrowane jako administrator) i hasło dla maszyny Wirtualnej.

    ![Rysowanie][img-portal-vm-create]
4. Wybierz rozmiar maszyny wirtualnej do wdrożenia:

    a.    Jeśli planujesz tworzenie wirtualnego dysku twardego lokalną, rozmiar nie ma znaczenia. Zastanów się nad użyciem maszyny wirtualnej o mniejszym rozmiarze.

    b.    Jeśli zamierzasz utworzyć obraz na platformie Azure, warto wybrać jeden z zalecanych rozmiarów maszyny wirtualnej dla wybranego obrazu.

    c.    Aby uzyskać informacje o cenach, zobacz **zalecana warstwa cenowa** selektora wyświetlane w portalu. Zobaczysz trzy zalecane rozmiary udostępniane przez wydawcę. (W tym przypadku wydawcą jest firma Microsoft).

    ![Rysowanie][img-portal-vm-size]
5. Ustaw właściwości:

    a.    Do szybkiego wdrożenia, można pozostawić wartości domyślne dla właściwości w obszarze **konfiguracji opcjonalnej** i **grupy zasobów**.

    b.    W obszarze **konta magazynu**, można opcjonalnie wybierz konto magazynu, w którym będą przechowywane wirtualnego dysku twardego systemu operacyjnego.

    c.    W obszarze **grupy zasobów**, można opcjonalnie wybierz grupę logiczną, w którym można umieścić maszyny Wirtualnej.
6. Wybierz **lokalizacji** wdrożenia:

    a.    Jeśli planujesz tworzenie wirtualnego dysku twardego lokalnej lokalizacji nie ma znaczenia, ponieważ możesz przekazać obraz później na platformie Azure.

    b.    Jeśli chcesz utworzyć obraz na platformie Azure, warto na początku wybrać odpowiedni region platformy Microsoft Azure w Stanach Zjednoczonych. Przyspiesza to proces kopiowania wirtualnego dysku twardego, wykonująca firmy Microsoft w imieniu użytkownika podczas przesyłania obrazu do certyfikacji.

    ![Rysowanie][img-portal-vm-location]
7. Kliknij przycisk **Utwórz**. Maszyna wirtualna zacznie do wdrożenia. Po upływie kilku minut, gdy wdrażanie zostanie zakończone, możesz przystąpić do tworzenia obrazu dla jednostki SKU.

### <a name="33-develop-your-vhd-in-the-cloud"></a>3.3 opracowanie dysk VHD w chmurze
Zdecydowanie zaleca się tworzenie dysk VHD w chmurze za pomocą protokołu RDP (Remote Desktop). Połączenie RDP z nazwą użytkownika i hasło określone podczas inicjowania obsługi.

> [!IMPORTANT]
> **Nie należy używać dysków zarządzanych.** Stworzono wirtualnego dysku twardego do chmury maszyny wirtualnej nie musi być oparta na dyskach zarządzanych zgodnie z obecnie nie obsługuje tworzenia obrazu z nich.
> Tworzenie maszyny wirtualnej w przypadku zmiany funkcja opcjonalna wartość domyślna dla dysków zarządzanych.

> W przypadku tworzenia dysk VHD lokalnymi (co nie jest zalecane), zobacz [Tworzenie obrazu maszyny wirtualnej z lokalnymi](marketplace-publishing-vm-image-creation-on-premise.md). Pobieranie dysk VHD nie jest konieczne, jeśli tworzysz w chmurze.
>
>

**Łączenie za pośrednictwem protokołu RDP za pomocą [portalu Microsoft Azure][link-azure-portal]**

1. Wybierz **wszystkie usługi** > **maszyn wirtualnych**.
2. Zostanie otwarty blok maszyn wirtualnych. Upewnij się, maszyny Wirtualnej, który chcesz połączyć z działa, a następnie wybierz z listy wdrożonych maszyn wirtualnych.
3. Zostanie otwarty blok, który opisuje wybranej maszynie Wirtualnej. U góry, kliknij przycisk **Connect**.
4. Monit o wprowadzenie nazwy użytkownika i hasło, które zostały określone podczas inicjowania obsługi administracyjnej.

**Łączenie za pośrednictwem protokołu RDP przy użyciu programu PowerShell**

Aby pobrać pliku z pulpitu zdalnego na komputerze lokalnym, należy użyć [polecenia cmdlet Get-AzureRemoteDesktopFile][link-technet-2]. Aby użyć tego polecenia cmdlet, musisz znać nazwę usługi i nazwy maszyny wirtualnej. Jeśli utworzono maszynę Wirtualną z [portalu Microsoft Azure][link-azure-portal], można znaleźć te informacje w obszarze właściwości maszyny Wirtualnej:

1. W portalu Microsoft Azure, wybierz **wszystkie usługi** > **maszyn wirtualnych**.
2. Zostanie otwarty blok maszyn wirtualnych. Wybierz maszynę Wirtualną, która została wdrożona.
3. Zostanie otwarty blok, który opisuje wybranej maszynie Wirtualnej.
4. Kliknij pozycję **Właściwości**.
5. Pierwszą część nazwy domeny stanowi nazwa usługi. Nazwą hosta jest nazwa maszyny wirtualnej.

    ![Rysowanie][img-portal-vm-rdp]
6. Polecenie cmdlet, aby pobrać plik RDP dla maszyny Wirtualnej utworzonej na pulpicie lokalnego administratora ma następującą składnię.

        Get‐AzureRemoteDesktopFile ‐ServiceName “baseimagevm‐6820cq00” ‐Name “BaseImageVM” –LocalPath “C:\Users\Administrator\Desktop\BaseImageVM.rdp”

Więcej informacji na temat protokołu RDP można znaleźć w witrynie MSDN w artykule [Połącz z maszyny Wirtualnej platformy Azure z protokołu RDP lub SSH](http://msdn.microsoft.com/library/azure/dn535788.aspx).

**Skonfiguruj Maszynę wirtualną i Utwórz użytkownika jednostki SKU**

Po system operacyjny, który jest pobierany wirtualnego dysku twardego Użyj funkcji Hyper-v i konfigurowanie maszyny Wirtualnej, aby rozpocząć tworzenie użytkownika jednostki SKU. Szczegółowy opis kroków można znaleźć w następujących łącze TechNet: [Instalowanie funkcji Hyper-v i konfigurowanie maszyny Wirtualnej](http://technet.microsoft.com/library/hh846766.aspx).

### <a name="34-choose-the-correct-vhd-size"></a>3.4, wybierz odpowiedni rozmiar wirtualnego dysku twardego
System operacyjny Windows wirtualnego dysku twardego w obrazie maszyny Wirtualnej należy utworzyć jako 128 GB stały format wirtualnego dysku twardego.  

Jeśli fizyczny rozmiar jest mniejszy niż 128 GB, powinien być rozrzedzonych wirtualnego dysku twardego. Podstawowy obrazów systemu Windows i program SQL Server podane już spełnia te wymagania, więc nie zmieniaj format lub rozmiar wirtualnego dysku twardego uzyskany.  

Dyski danych może być większy niż 1 TB. Decydując o rozmiar dysku, należy pamiętać, że klienci nie może zmienić rozmiaru wirtualnych dysków twardych w obrębie obrazu w czasie wdrażania. Wirtualne dyski twarde dysku danych należy utworzyć jako stały format wirtualnego dysku twardego. Powinny być również rozrzedzone. Dyski z danymi mogą zawierać dane lub mogą być puste.

### <a name="35-install-the-latest-windows-patches"></a>3.5 zainstalować najnowsze poprawki systemu Windows
Podstawowe obrazy zawierają poprawki wydane do dnia opublikowania obrazu. Przed opublikowaniem systemu operacyjnego utworzeniu wirtualnego dysku twardego, upewnij się, aktualizacji systemu Windows została uruchomiona i czy zostały zainstalowane wszystkie najnowsze krytyczne i ważne aktualizacje zabezpieczeń.

### <a name="36-perform-additional-configuration-and-schedule-tasks-as-necessary"></a>3,6 wykonanie dodatkowych zadań konfiguracji i harmonogram, zgodnie z potrzebami
Jeśli potrzebne są dodatkowe czynności konfiguracyjne, należy wziąć pod uwagę przy użyciu zaplanowanego zadania, który jest uruchamiany podczas uruchamiania w celu zmiany końcowego do maszyny Wirtualnej po jej wdrożeniu:

* Dobrym rozwiązaniem jest takie skonfigurowanie zadania, aby zostało ono usunięte po pomyślnym wykonaniu.
* Konfiguracja nie polegać na dyskach, niż dyski C lub D, ponieważ są one tylko dwóch dysków, które są zawsze istnieje. Dysk C jest dysk systemu operacyjnego i dysku D jest tymczasowy dysku lokalnym.

### <a name="37-generalize-the-image"></a>3.7 Uogólnij obraz
Wszystkie obrazy w portalu Azure Marketplace musi być wielokrotnego użytku w sposób ogólny. Innymi słowy musi być uogólniony wirtualny dysk twardy systemu operacyjnego:

* W systemie Windows, obraz powinien być "Sysprep" i nie ma żadnych konfiguracji należy zrobić, które nie obsługują **sysprep** polecenia.
* Można uruchomić następujące polecenie z katalogu % windir%\System32\Sysprep.

        sysprep.exe /generalize /oobe /shutdown

  Wskazówki dotyczące sposobu do programu sysprep systemu operacyjnego znajduje się w kroku następujący artykuł w witrynie MSDN: [tworzenie i przekazywanie serwera wirtualnego dysku twardego Windows Azure](../virtual-machines/windows/classic/createupload-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="4-deploy-a-vm-from-your-vhds"></a>4. Wdrożenie maszyny Wirtualnej z sieci wirtualnych dysków twardych
Po dyski VHD (uogólniony wirtualny dysk twardy systemu operacyjnego i zero lub więcej danych na dysku VHD) zostały przekazane do konta magazynu platformy Azure, można je zarejestrować jako obraz użytkownika maszyny Wirtualnej. Następnie można przetestować tego obrazu. Należy pamiętać, że ponieważ uogólniony wirtualny dysk twardy systemu operacyjnego, nie można bezpośrednio wdrożyć maszyny Wirtualnej podając adres URL dysku VHD.

Aby dowiedzieć się więcej na temat obrazów maszyn wirtualnych, należy przejrzeć następujących wpisach w blogu:

* [Obraz maszyny Wirtualnej](https://azure.microsoft.com/blog/vm-image-blog-post/)
* [Jak PowerShell obrazu maszyny Wirtualnej](https://azure.microsoft.com/blog/vm-image-powershell-how-to-blog-post/)
* [Informacje o obrazach maszyny Wirtualnej na platformie Azure](https://msdn.microsoft.com/library/azure/dn790290.aspx)

### <a name="set-up-the-necessary-tools-powershell-and-azure-cli"></a>Konfigurowanie niezbędne narzędzia programu PowerShell i interfejsu wiersza polecenia Azure
* [Konfigurowanie środowiska PowerShell](/powershell/azure/overview)
* [Konfigurowanie interfejsu wiersza polecenia Azure](../cli-install-nodejs.md)

### <a name="41-create-a-user-vm-image"></a>4.1 tworzenia obrazu użytkownika maszyny Wirtualnej
#### <a name="capture-vm"></a>Przechwytywanie maszyny Wirtualnej
Przeczytaj łączy podanych poniżej w celu uzyskania wskazówek dotyczących Przechwytywanie maszyny Wirtualnej przy użyciu interfejsu wiersza polecenia Azure-API/programu PowerShell.

* [Interfejs API](https://msdn.microsoft.com/library/mt163560.aspx)
* [Program PowerShell](../virtual-machines/windows/capture-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Interfejs wiersza polecenia platformy Azure](../virtual-machines/linux/capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

### <a name="generalize-image"></a>Obraz
Przeczytaj łączy podanych poniżej w celu uzyskania wskazówek dotyczących Przechwytywanie maszyny Wirtualnej przy użyciu interfejsu wiersza polecenia Azure-API/programu PowerShell.

* [Interfejs API](https://msdn.microsoft.com/library/mt269439.aspx)
* [Program PowerShell](../virtual-machines/windows/capture-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Interfejs wiersza polecenia platformy Azure](../virtual-machines/linux/capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

### <a name="42-deploy-a-vm-from-a-user-vm-image"></a>4.2 wdrożyć maszynę Wirtualną z obrazu użytkownika maszyny Wirtualnej
Aby wdrożyć maszynę Wirtualną z obrazu użytkownika maszyny Wirtualnej, można użyć bieżącej [portalu Azure](https://manage.windowsazure.com) lub programu PowerShell.

**Wdrażanie maszyny Wirtualnej z bieżącego portalu platformy Azure**

1. Przejdź do **nowy** > **obliczeniowe** > **maszyny wirtualnej** > **z galerii**.

2. Przejdź do **Moje obrazy**, a następnie wybierz obraz maszyny Wirtualnej, z której chcesz wdrożyć Maszynę wirtualną:

   1. Należy zwrócić szczególną uwagę, aby obraz należy wybrać, ponieważ **Moje obrazy** widoku są wyszczególnione zarówno obrazy systemu operacyjnego i obrazy maszyny Wirtualnej.
   2. Spojrzenie na liczbę dysków może pomóc ustalić wdrażasz rodzaju obrazu, ponieważ większość obrazów maszyn wirtualnych ma więcej niż jeden dysk. Jednak jest nadal możliwe obraz maszyny Wirtualnej z tylko jednego systemu operacyjnego dysku, który będzie zmuszony **liczba dysków** ustawioną wartość 1.

      ![Rysowanie][img-manage-vm-select]
3. Użyj Kreatora tworzenia maszyny Wirtualnej i określ maszyny Wirtualnej nazwy, maszyna wirtualna rozmiar lokalizacji, nazwę użytkownika i hasło.

**Wdróż Maszynę wirtualną z programu PowerShell**

Aby wdrożyć dużych maszyny Wirtualnej z uogólniony obraz maszyny Wirtualnej utworzone, służy następujące polecenia cmdlet.

    $img = Get-AzureVMImage -ImageName "myVMImage"
    $user = "user123"
    $pass = "adminPassword123"
    $myVM = New-AzureVMConfig -Name "VMImageVM" -InstanceSize "Large" -ImageName $img.ImageName | Add-AzureProvisioningConfig -Windows -AdminUsername $user -Password $pass
    New-AzureVM -ServiceName "VMImageCloudService" -VMs $myVM -Location "West US" -WaitForBoot

> [!IMPORTANT]
> Aby uzyskać dodatkową pomoc, zapoznaj się z [Rozwiązywanie typowych problemów napotykanych podczas tworzenia dysku VHD].
>
>

## <a name="5-obtain-certification-for-your-vm-image"></a>5. Uzyskaj certyfikacji dla obrazu maszyny Wirtualnej
Następnym krokiem w celu przygotowania obrazu maszyny Wirtualnej do portalu Azure Marketplace jest ona certyfikowane.

Proces ten obejmuje uruchomione narzędzie certyfikacji specjalne, przekazywanie wyniki weryfikacji do kontenera platformy Azure, w którym dyski VHD znajdują się dodanie propozycją, definiowania użytkownika jednostki SKU i przesyłanie obrazu maszyny Wirtualnej do certyfikacji.

### <a name="51-download-and-run-the-certification-test-tool-for-azure-certified"></a>5.1 Pobierz i uruchom narzędzie Test certyfikacji dla certyfikowane Azure
Uruchamia narzędzie certyfikacji na uruchomionej maszynie Wirtualnej, udostępnione z obrazu użytkownika maszyny Wirtualnej, aby upewnić się, że obraz maszyny Wirtualnej jest zgodna z Microsoft Azure. Weryfikowane jest spełnienie wytycznych oraz wymagań dotyczących przygotowywania wirtualnego dysku twardego. Dane wyjściowe narzędzia jest raport zgodności, które należy przekazać w portalu publikowania podczas żądania certyfikacji.

Narzędzie certyfikacji służy w systemach Windows i maszyn wirtualnych systemu Linux. Nawiązanie połączenia z systemem Windows maszyn wirtualnych za pomocą programu PowerShell, a łączy do maszyn wirtualnych systemu Linux za pomocą SSH.Net:

1. Najpierw należy pobrać to narzędzie certyfikacji w [witryny pobierania firmy Microsoft][link-msft-download].
2. Otwórz narzędzie certyfikacji, a następnie kliknij przycisk **uruchomienia nowego testu** przycisku.
3. Z **testowanie informacji** ekranu, wprowadź nazwę dla uruchomienia testu.
4. Wybierz system operacyjny maszyny wirtualnej (Linux lub Windows). W zależności od wybranej opcji określ kolejne opcje.

### <a name="connect-the-certification-tool-to-a-linux-vm-image"></a>**Narzędzie certyfikacji nawiązać połączenia z obrazu maszyny Wirtualnej systemu Linux**
1. Wybierz tryb uwierzytelniania SSH: za pomocą hasła lub pliku klucza.
2. Jeśli przy użyciu uwierzytelniania opartego na hasłach, wprowadź nazwę systemu nazw domen (DNS, Domain Name System), nazwę użytkownika i hasło.
3. Jeśli przy użyciu pliku klucza uwierzytelniania, wprowadź nazwę DNS, nazwy użytkownika i lokalizacji klucza prywatnego.

   ![Uwierzytelnianie hasła z obrazu maszyny Wirtualnej systemu Linux][img-cert-vm-pswd-lnx]

   ![Plik klucza uwierzytelniania obrazu maszyny Wirtualnej systemu Linux][img-cert-vm-key-lnx]

### <a name="connect-the-certification-tool-to-a-windows-based-vm-image"></a>**Narzędzie certyfikacji nawiązać połączenia z obrazu maszyny Wirtualnej z systemem Windows**
1. Wprowadź w pełni kwalifikowana nazwa DNS maszyny Wirtualnej (na przykład MyVMName.Cloudapp.net).
2. Wprowadź nazwę użytkownika i hasło.

   ![Uwierzytelnianie hasła obrazu maszyny Wirtualnej systemu Windows][img-cert-vm-pswd-win]

Po wybraniu odpowiednich opcji zostały wybrane dla obrazu systemu Linux lub maszyny Wirtualnej z systemem Windows, wybierz **Testuj połączenie** aby upewnić się, że SSH.Net lub programu PowerShell ma prawidłową połączenia do celów testowych. Po nawiązaniu połączenia wybierz **dalej** można uruchomić testu.

Po ukończeniu testu zostaną wyświetlone wyniki (powodzenie/niepowodzenie/ostrzeżenie) dla każdego elementu testu.

![Przypadków testowych dla obrazu maszyny Wirtualnej systemu Linux][img-cert-vm-test-lnx]

![Przypadki testowe dla obrazu maszyny Wirtualnej systemu Windows][img-cert-vm-test-win]

Jeśli którykolwiek z testów nie powiedzie się, obraz nie będzie certyfikat. W takim przypadku zapoznaj się z wymaganiami, a następnie wprowadź niezbędne zmiany.

Po zautomatyzowanych testów zostanie wyświetlona prośba zapewnienie dodatkowych danych wejściowych w obrazie maszyny Wirtualnej za pośrednictwem ekranu kwestionariusz.  Zakończenie pytań, a następnie wybierz **dalej**.

![Kwestionariusz narzędzie certyfikacji][img-cert-vm-questionnaire]

![Kwestionariusz narzędzie certyfikacji][img-cert-vm-questionnaire-2]

Po wykonaniu kwestionariusz, musisz podać dodatkowe informacje, takie jak informacje o dostępu SSH dla maszyny Wirtualnej systemu Linux, obrazu i wyjaśnienie ocen nie powiodło się. Oprócz odpowiedzi na kwestionariusz można pobrać wyników testu i pliki dziennika, aby wykonać przypadków testowych. Zapisz wyniki w tym samym kontenerze co dyski VHD.

![Zapisz certyfikacji wyników testu][img-cert-vm-results]

### <a name="52-get-the-shared-access-signature-uri-for-your-vm-images"></a>5.2 Pobierz identyfikator URI sygnatury dostępu współdzielonego dla obrazów maszyny Wirtualnej
W procesie publikowania możesz określić uniform resource identifier (URI) prowadzących do każdego z dysków VHD zostały utworzone dla Twojego jednostki SKU. Dostęp do tych wirtualnych dysków twardych jest potrzebny firmie Microsoft w procesie certyfikacji. W związku z tym należy utworzyć identyfikator URI sygnatury dostępu współdzielonego dla każdego wirtualnego dysku twardego. Jest to identyfikator URI, który powinny być wprowadzane w **obrazów** kartę w portalu publikowania.

Sygnatury dostępu współdzielonego, utworzenia identyfikatora URI należy stosować następujące wymagania:

Uwaga: poniższe instrukcje dotyczą tylko niezarządzane dyski, które są tylko rodzaj obsługiwane.

* Podczas generowania sygnatury dostępu współdzielonego identyfikatorów URI dla dyski VHD, listy i Odczyt uprawnienia są wystarczające. Nie należy przydzielać uprawnień do zapisywania ani usuwania.
* Czas trwania dla dostępu powinien mieć co najmniej trzech (3) tygodni od, podczas tworzenia sygnatury dostępu współdzielonego identyfikatora URI.
* Ochrona dla czasu UTC, wybierz dzień przed bieżącą datą. Na przykład jeśli bieżąca data jest 6 października 2014 r., wybierz 2014-10-5.

Adres URL SAS mogą być generowane na wiele sposobów, aby udostępnić dysk VHD do portalu Azure Marketplace.
Poniżej przedstawiono zalecane narzędzia 3:

1.  Eksplorator usługi Azure Storage
2.  Eksplorator usługi Storage firmy Microsoft
3.  Interfejs wiersza polecenia platformy Azure

**Eksplorator usługi Storage platformy Azure (zalecane w przypadku użytkowników systemu Windows)**

Poniżej przedstawiono kroki podczas generowania adresu URL SAS za pomocą Eksploratora usługi Storage platformy Azure

1. Pobierz [Explorer 6 magazynu Azure w wersji zapoznawczej 3](https://azurestorageexplorer.codeplex.com/) w witrynie CodePlex. Przejdź do [Podgląd 6 Eksploratora magazynu Azure](https://azurestorageexplorer.codeplex.com/) i kliknij przycisk **"Pobieranie"**.

    ![Rysowanie](media/marketplace-publishing-vm-image-creation/img5.2_01.png)

2. Pobierz [AzureStorageExplorer6Preview3.zip](https://azurestorageexplorer.codeplex.com/downloads/get/891668) i zainstalować po rozpakować go.

    ![Rysowanie](media/marketplace-publishing-vm-image-creation/img5.2_02.png)

3. Po zakończeniu instalacji, Otwórz aplikację.
4. Kliknij przycisk **Dodaj konto**.

    ![Rysowanie](media/marketplace-publishing-vm-image-creation/img5.2_03.png)

5. Określ nazwę konta magazynu, klucz konta magazynu i domeny punkty końcowe magazynu. To jest konto magazynu w ramach subskrypcji platformy Azure, gdzie dysk VHD mają być przechowywane w portalu Azure.

    ![Rysowanie](media/marketplace-publishing-vm-image-creation/img5.2_04.png)

6. Po Eksploratora usługi Storage platformy Azure jest połączona z konta magazynu określonych, zostanie ona rozpoczęta wyświetlane są wszystkie zawiera w ramach konta magazynu. Wybierz kontener, w której został skopiowany plik VHD dysku systemu operacyjnego (także dysków danych, jeśli są one odpowiednie dla danego scenariusza).

    ![Rysowanie](media/marketplace-publishing-vm-image-creation/img5.2_05.png)

7. Po wybraniu kontenera obiektów blob, uruchamia Eksploratora usługi Storage Azure przedstawiający pliki w kontenerze. Wybierz plik obrazu (VHD), który musi zostać przesłane.

    ![Rysowanie](media/marketplace-publishing-vm-image-creation/img5.2_06.png)

8.  Po wybraniu pliku VHD w kontenerze, kliknij przycisk **zabezpieczeń** kartę.

    ![Rysowanie](media/marketplace-publishing-vm-image-creation/img5.2_07.png)

9.  W **zabezpieczeń kontenera obiektów Blob** okna dialogowego pozostaw wartości domyślne na **poziom dostępu** , a następnie kliknij pozycję **sygnatury dostępu współdzielonego** kartę.

    ![Rysowanie](media/marketplace-publishing-vm-image-creation/img5.2_08.png)

10. Wykonaj poniższe kroki, aby wygenerować identyfikator URI sygnatury dostępu współdzielonego dla obrazu vhd:

    ![Rysowanie](media/marketplace-publishing-vm-image-creation/img5.2_09.png)

    a. **Zezwala na dostęp:** ochrona dla czasu UTC, wybierz dzień przed bieżącą datą. Na przykład jeśli bieżąca data jest 6 października 2014 r., wybierz 2014-10-5.

    b. **Dozwolony dostęp do:** wybierz datę, która jest co najmniej 3 tygodni po **zezwala na dostęp** daty.

    c. **Akcje dozwolone:** wybierz **listy** i **odczytu** uprawnienia.

    d. Jeśli wybrano plik VHD poprawnie, a następnie plik zostanie wyświetlony w **nazwa obiektu Blob, aby uzyskać dostęp do** z rozszerzenie VHD.

    e. Kliknij przycisk **generowania podpisu**.

    f. W **wygenerowany udostępnionego dostępu podpisu identyfikator URI tego kontenera**, sprawdź, czy jako wyróżnione powyżej:

       - Upewnij się, że nazwa pliku obrazu i **"VHD"** są w identyfikatorze URI.
       - Upewnij się, że na końcu podpis **"= rl"** pojawi się. Oznacza to, że dostęp do odczytu i listy podano pomyślnie.
       - Upewnij się, że w środku podpisu, **"sr = c"** pojawi się. Oznacza to, czy masz dostęp na poziomie kontenera

11. Zapewnienie wygenerowany udostępnionych działania identyfikator URI sygnatury dostępu kliknij **testu w przeglądarce**. Należy ją uruchomić procesu pobierania.

12. Skopiuj identyfikator URI sygnatury dostępu współdzielonego. Należy go wkleić w Portalu wydawców.

13. Powtórz kroki od 6-10 dla każdego wirtualnego dysku twardego w jednostce SKU.

**Eksplorator usługi Storage platformy Microsoft Azure (systemem Windows lub MAC/Linux)**

Poniżej przedstawiono kroki podczas generowania adresu URL SAS za pomocą Eksploratora usługi Microsoft Azure Storage

1.  Pobierz formularza Eksploratora usługi Microsoft Azure Storage [ http://storageexplorer.com/ ](http://storageexplorer.com/) witryny sieci Web. Przejdź do [Eksploratora usługi Microsoft Azure Storage](http://storageexplorer.com/releasenotes.html) i kliknij przycisk **"Pobierz dla systemu Windows"**.

    ![Rysowanie](media/marketplace-publishing-vm-image-creation/img5.2_10.png)

2.  Po zakończeniu instalacji, Otwórz aplikację.

3.  Kliknij przycisk **Dodaj konto**.

4.  Konfigurowanie Eksploratora usługi Microsoft Azure Storage do subskrypcji przez Zaloguj się do swojego konta

    ![Rysowanie](media/marketplace-publishing-vm-image-creation/img5.2_11.png)

5.  Przejdź do konta magazynu, a następnie wybierz kontener

6.  Wybierz **"Get podpisu dostępu do udziału."** za pomocą kliknij prawym przyciskiem myszy z **kontenera**

    ![Rysowanie](media/marketplace-publishing-vm-image-creation/img5.2_12.png)

7.  Godzina rozpoczęcia aktualizacji, czas wygaśnięcia i uprawnienia, zgodnie z harmonogramem poniżej

    ![Rysowanie](media/marketplace-publishing-vm-image-creation/img5.2_13.png)

    a.  **Czas rozpoczęcia:** ochrona dla czasu UTC, wybierz dzień przed bieżącą datą. Na przykład jeśli bieżąca data jest 6 października 2014 r., wybierz 2014-10-5.

    b.  **Czas wygaśnięcia:** wybierz datę, która jest co najmniej 3 tygodni po **czas rozpoczęcia** daty.

    c.  **Uprawnienia:** wybierz **listy** i **odczytu** uprawnień

8.  Skopiuj sygnatury dostępu współdzielonego kontenera identyfikatora URI

    ![Rysowanie](media/marketplace-publishing-vm-image-creation/img5.2_14.png)

    Wygenerowany adres URL SAS do kontenera poziom i teraz należy dodać nazwę dysku VHD w nim.

    Format adresu URL SAS poziomu kontenera: `https://testrg009.blob.core.windows.net/vhds?st=2016-04-22T23%3A05%3A00Z&se=2016-04-30T23%3A05%3A00Z&sp=rl&sv=2015-04-05&sr=c&sig=J3twCQZv4L4EurvugRW2klE2l2EFB9XyM6K9FkuVB58%3D`

    Wstaw nazwę dysku VHD po nazwie kontenera w adresie URL SAS, jak pokazano poniżej `https://testrg009.blob.core.windows.net/vhds/<VHD NAME>?st=2016-04-22T23%3A05%3A00Z&se=2016-04-30T23%3A05%3A00Z&sp=rl&sv=2015-04-05&sr=c&sig=J3twCQZv4L4EurvugRW2klE2l2EFB9XyM6K9FkuVB58%3D`

    Przykład:

    ![Rysowanie](media/marketplace-publishing-vm-image-creation/img5.2_15.png)

    TestRGVM201631920152.vhd jest nazwa wirtualnego dysku twardego, a następnie będzie adres URL SAS wirtualnego dysku twardego `https://testrg009.blob.core.windows.net/vhds/TestRGVM201631920152.vhd?st=2016-04-22T23%3A05%3A00Z&se=2016-04-30T23%3A05%3A00Z&sp=rl&sv=2015-04-05&sr=c&sig=J3twCQZv4L4EurvugRW2klE2l2EFB9XyM6K9FkuVB58%3D`

    - Upewnij się, że nazwa pliku obrazu i **"VHD"** są w identyfikatorze URI.
    - Upewnij się, że w środku podpisu, **"sp = rl"** pojawi się. Oznacza to, że dostęp do odczytu i listy podano pomyślnie.
    - Upewnij się, że w środku podpisu, **"sr = c"** pojawi się. Oznacza to, czy masz dostęp na poziomie kontenera

9.  Aby upewnić się, wygenerowany udostępnionych działania identyfikator URI sygnatury dostępu, przetestować go w przeglądarce. Należy uruchomić proces pobierania

10. Skopiuj identyfikator URI sygnatury dostępu współdzielonego. Należy go wkleić w Portalu wydawców.

11. Powtórz te czynności dla każdego wirtualnego dysku twardego w ramach jednostki SKU.

**Interfejs wiersza polecenia platformy Azure (zalecane w przypadku integracji z systemem innym niż Windows & ciągłe)**

Poniżej przedstawiono kroki podczas generowania adresu URL sygnatury dostępu Współdzielonego przy użyciu wiersza polecenia platformy Azure

1.  Pobierz program Microsoft Azure CLI z [tutaj](https://azure.microsoft.com/en-in/documentation/articles/xplat-cli-install/). Możesz również znaleźć różnych łączy dla **[Windows](http://aka.ms/webpi-azure-cli)** i  **[systemu MAC OS](http://aka.ms/mac-azure-cli)**.

2.  Po pobraniu jej Zainstaluj

3.  Tworzenie programu PowerShell (lub innego pliku wykonywalnego skryptu) pliku z następującym kodem i zapisać ją lokalnie

          $conn="DefaultEndpointsProtocol=https;AccountName=<StorageAccountName>;AccountKey=<Storage Account Key>"
          azure storage container list vhds -c $conn
          azure storage container sas create vhds rl <Permission End Date> -c $conn --start <Permission Start Date>  

    Zaktualizuj następujące parametry w powyżej

    a. **`<StorageAccountName>`**: Nadaj nazwę konta magazynu

    b. **`<Storage Account Key>`**: Podać klucz konta magazynu

    c. **`<Permission Start Date>`**: Aby chronić dla czasu UTC, wybierz dzień przed bieżącą datą. Na przykład, jeśli bieżąca data jest 25 października 2016 r. następnie wartość powinna być 2016-10-25. Jeśli za pomocą usługi Azure CLI 2.0 (polecenie az), podaj datę i godzinę rozpoczęcia oraz datę zakończenia, na przykład: 10-25-2016T00:00:00Z.

    d. **`<Permission End Date>`**: Wybierz datę, która jest co najmniej 3 tygodni po **Data rozpoczęcia**. Wartość powinna być **2016-11-02**. Jeśli za pomocą usługi Azure CLI 2.0 (polecenie az), podaj datę i godzinę rozpoczęcia oraz datę zakończenia, na przykład: 11-02-2016T00:00:00Z.

    Poniżej przedstawiono przykładowy kod po zaktualizowaniu odpowiednie parametry

          $conn="DefaultEndpointsProtocol=https;AccountName=st20151;AccountKey=<account-key>"
          azure storage container list vhds -c $conn
          azure storage container sas create vhds rl 11/02/2016 -c $conn --start 10/25/2016  

4.  Otwórz Edytor programu Powershell w trybie "Uruchom jako Administrator", a następnie otwórz plik w kroku #3. Można użyć dowolnego edytora skryptów, które są dostępne w systemie operacyjnym.

5.  Uruchom skrypt i będzie on zawierał możesz adres URL SAS dla kontenera poziom dostępu

    Następujące będą dane wyjściowe sygnatury SAS i skopiuj zaznaczony element w Notatniku

    ![Rysowanie](media/marketplace-publishing-vm-image-creation/img5.2_16.png)

6.  Teraz otrzymasz poziom kontenera adres URL SAS i konieczne jest dodanie Nazwa wirtualnego dysku twardego w nim.

    Adres URL SAS poziomu kontenera #

    `https://st20151.blob.core.windows.net/vhds?st=2016-10-25T07%3A00%3A00Z&se=2016-11-02T07%3A00%3A00Z&sp=rl&sv=2015-12-11&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

7.  Wstaw nazwę dysku VHD po nazwie kontenera w adresie URL SAS, jak pokazano poniżej `https://st20151.blob.core.windows.net/vhds/<VHDName>?st=2016-10-25T07%3A00%3A00Z&se=2016-11-02T07%3A00%3A00Z&sp=rl&sv=2015-12-11&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

    Przykład:

    TestRGVM201631920152.vhd jest nazwa wirtualnego dysku twardego, a następnie będzie adres URL SAS wirtualnego dysku twardego

    `https://st20151.blob.core.windows.net/vhds/ TestRGVM201631920152.vhd?st=2016-10-25T07%3A00%3A00Z&se=2016-11-02T07%3A00%3A00Z&sp=rl&sv=2015-12-11&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

    - Upewnij się, że nazwa pliku obrazu i "VHD" są w identyfikatorze URI.
    -   Upewnij się, że w środku podpisu, "sp = rl" pojawia się. Oznacza to, że dostęp do odczytu i listy podano pomyślnie.
    -   Upewnij się, że w środku podpisu, "sr = c" pojawia się. Oznacza to, czy masz dostęp na poziomie kontenera

8.  Aby upewnić się, wygenerowany udostępnionych działania identyfikator URI sygnatury dostępu, przetestować go w przeglądarce. Należy uruchomić proces pobierania

9.  Skopiuj identyfikator URI sygnatury dostępu współdzielonego. Należy go wkleić w Portalu wydawców.

10. Powtórz te czynności dla każdego wirtualnego dysku twardego w ramach jednostki SKU.


### <a name="53-provide-information-about-the-vm-image-and-request-certification-in-the-publishing-portal"></a>5.3 zawierają informacje o obrazie maszyny Wirtualnej i żądanie certyfikacji w portalu publikowania
Po utworzeniu oferty i wersji produktu, należy wprowadzić szczegóły obrazu skojarzonego z tej wersji produktu:

1. Przejdź do [Portal publikowania][link-pubportal], a następnie zaloguj się przy użyciu konta sprzedaży.
2. Wybierz **obrazów maszyn wirtualnych** kartę.
3. Identyfikator wyświetlonym u góry strony jest rzeczywiście identyfikator oferty i nie identyfikator jednostki SKU.
4. Wypełnianie właściwości pod **jednostki SKU** sekcji.
5. W obszarze **rodziny systemów operacyjnych**, kliknij typ systemu operacyjnego skojarzonego z wirtualnego dysku twardego systemu operacyjnego.
6. W **systemu operacyjnego** pozycję opisują systemu operacyjnego. Należy wziąć pod uwagę format jak rodziny systemów operacyjnych, typ, wersja i aktualizacje. Przykładem jest "Windows Server Datacenter 2014 R2".
7. Wybierz sześciu rozmiary zalecane maszyny wirtualnej. Są to zaleceń, które są wyświetlane na rzecz klienta, w bloku warstwa cenowa w portalu Azure po zdecydować kupić i wdrożyć obraz. **Są to tylko zalecenia. Klient jest możliwe wybranie dowolnego rozmiar maszyny Wirtualnej, uwzględniający dysków określona w obrazie.**
8. Wprowadź wersji. Pole wersji hermetyzuje wersję do identyfikowania produktu i jego aktualizacje:
   * Wersje powinna mieć postać X.Y.Z, gdzie X, Y i Z są liczbami całkowitymi.
   * Obrazy w różne jednostki magazynowe mogą mieć różne wersje główne i pomocnicze.
   * Wersje w jednostce SKU powinien mieć tylko zmiany przyrostowe, które zwiększają wersji poprawki (Z z X.Y.Z).
9. W **adres URL dysku VHD systemu operacyjnego** wprowadź sygnatury dostępu współdzielonego, identyfikator URI utworzony dla wirtualnego dysku twardego systemu operacyjnego.
10. W przypadku dysków z danymi skojarzonego z tą WERSJĄ, wybierz numer jednostki logicznej (LUN), do którego chcesz dysk danych można zainstalować po wdrożeniu.
11. W **adres URL dysku VHD X LUN** wprowadź sygnatury dostępu współdzielonego, identyfikator URI utworzony na pierwsze dane wirtualnego dysku twardego.

    ![Rysowanie](media/marketplace-publishing-vm-image-creation/vm-image-pubportal-skus-3.png)


## <a name="common-sas-url-issues--fixes"></a>Typowe problemy adres URL SAS i poprawki

|Problem|Komunikat o błędzie|Napraw|Łącze dokumentacji|
|---|---|---|---|
|Błąd podczas kopiowania obrazy — "?" nie znajduje się w adresie url SAS|Błąd: Kopiowanie obrazów. Nie można pobrać obiektu blob przy użyciu podany identyfikator Uri sygnatury dostępu Współdzielonego.|Narzędzia zalecanych aktualizacji przy użyciu adresu Url SAS|[https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/)|
|Błąd podczas kopiowania obrazy — parametry "st" i "se", nie w SAS url|Błąd: Kopiowanie obrazów. Nie można pobrać obiektu blob przy użyciu podany identyfikator Uri sygnatury dostępu Współdzielonego.|Zaktualizuj adres Url SAS z dat rozpoczęcia i zakończenia na nim|[https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/)|
|Błąd podczas kopiowania obrazów — "sp = rl" nie znajduje się w adres url SAS|Błąd: Kopiowanie obrazów. Nie można pobrać obiektu blob przy użyciu podany identyfikator Uri sygnatury dostępu Współdzielonego|Zaktualizuj adres Url SAS z uprawnienia "Odczyt" & "Lista jako|[https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/)|
|Błąd podczas kopiowania obrazów — adres url SAS ma białe znaki w nazwie wirtualnego dysku twardego|Błąd: Kopiowanie obrazów. Nie można pobrać obiektu blob przy użyciu podany identyfikator Uri sygnatury dostępu Współdzielonego.|Aktualizacja adresu Url SAS, bez spacji|[https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/)|
|Błąd podczas kopiowania obrazów — błąd autoryzacji adresów Url SAS|Błąd: Kopiowanie obrazów. Nie można pobrać obiektu blob ze względu na błąd autoryzacji|Wygeneruj ponownie adres Url SAS|[https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/)|
|Błąd podczas kopiowania obrazów — adres Url SAS "st" i "se" parametrów nie mają specyfikacji pełnej daty i godziny|Błąd: Kopiowanie obrazów. Nie można pobrać obiektu blob ze względu na nieprawidłowy adres Url SAS |Parametry Start adres Url SAS i Data zakończenia ("st", "se") muszą mieć specyfikacji pełnej daty i godziny, takich jak 11-02-2017T00:00:00Z, a nie tylko datę lub skróconej wersji po raz. Użytkownik może wystąpić ten scenariusz przy użyciu usługi Azure CLI 2.0 (polecenie az). Należy podać specyfikację daty godziny i ponowne wygenerowanie adres Url SAS.|[https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/)|

## <a name="next-step"></a>Następny krok
Po wykonaniu szczegóły jednostka SKU, można przenieść do przodu [marketing przewodnik zawartości portalu Azure Marketplace][link-pushstaging]. W tym kroku procesu publikowania można zapewnić zawartość marketing, ceny i inne informacje, które są niezbędne przed **krok 3: testowanie maszyny Wirtualnej oferują tymczasowych**, który testować różne scenariusze przypadek użycia przed wdrożeniem oferty w portalu Azure Marketplace widoczności publicznej i zakupu.  

## <a name="see-also"></a>Zobacz także
* [Wprowadzenie: jak publikowanie oferty w portalu Azure Marketplace](marketplace-publishing-getting-started.md)

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
[link-sql-2014-ent]:http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2014enterprisewindowsserver2012r2/
[link-sql-2014-std]:http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2014standardwindowsserver2012r2/
[link-sql-2014-web]:http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2014webwindowsserver2012r2/
[link-sql-2012-ent]:http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2012sp2enterprisewindowsserver2012/
[link-sql-2012-std]:http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2012sp2standardwindowsserver2012/
[link-sql-2012-web]:http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2012sp2webwindowsserver2012/
[link-datactr-2012-r2]:http://azure.microsoft.com/marketplace/partners/microsoft/windowsserver2012r2datacenter/
[link-datactr-2012]:http://azure.microsoft.com/marketplace/partners/microsoft/windowsserver2012datacenter/
[link-datactr-2008-r2]:http://azure.microsoft.com/marketplace/partners/microsoft/windowsserver2008r2sp1/
[link-acct-creation]:marketplace-publishing-accounts-creation-registration.md
[link-technet-1]:https://technet.microsoft.com/library/hh848454.aspx
[link-azure-vm-2]:./virtual-machines-linux-agent-user-guide/
[link-openssl]:https://www.openssl.org/
[link-intsvc]:http://www.microsoft.com/download/details.aspx?id=41554
[link-python]:https://www.python.org/
