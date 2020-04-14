---
title: Kontenery profilu FSLogix NetApp Windows Virtual Desktop — Azure
description: Jak utworzyć kontener profilu FSLogix przy użyciu plików NetApp platformy Azure na pulpicie wirtualnym systemu Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/13/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 149fb5818ee360c7333997655ea9eb8d7ded346c
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81270897"
---
# <a name="create-an-fslogix-profile-container-for-a-host-pool-using-azure-netapp-files"></a>Tworzenie kontenera profilu FSLogix dla puli hostów przy użyciu plików NetApp platformy Azure

Zalecamy używanie kontenerów profilu FSLogix jako rozwiązania profilu użytkownika dla [usługi Pulpitu wirtualnego systemu Windows.](overview.md) Kontenery profilu FSLogix przechowują pełny profil użytkownika w jednym kontenerze i są przeznaczone do poruszania się po profilach w nietrwałych środowiskach zdalnych, takich jak pulpit wirtualny systemu Windows. Po zalogowaniu kontener dynamicznie dołącza się do środowiska komputerowego przy użyciu lokalnie obsługiwanego wirtualnego dysku twardego (VHD) i wirtualnego dysku twardego funkcji Hyper-V (VHDX). Te zaawansowane technologie sterownika filtru umożliwiają natychmiastowe udostępnienie profilu użytkownika i wyświetlenie go w systemie dokładnie tak samo, jak lokalny profil użytkownika. Aby dowiedzieć się więcej o kontenerach profili FSLogix, zobacz [kontenery profilów FSLogix i pliki platformy Azure](fslogix-containers-azure-files.md).

Kontenery profilu FSLogix można tworzyć przy użyciu [usługi Azure NetApp Files](https://azure.microsoft.com/services/netapp/)— łatwej w użyciu usługi platformy natywnej platformy Azure, która pomaga klientom szybko i niezawodnie aprowizować woluminy SMB klasy korporacyjnej dla ich środowisk pulpitu wirtualnego systemu Windows. Aby dowiedzieć się więcej o plikach NetApp platformy Azure, zobacz [Co to są pliki NetApp platformy Azure?](../azure-netapp-files/azure-netapp-files-introduction.md)

W tym przewodniku pokazano, jak skonfigurować konto usługi Azure NetApp Files i utworzyć kontenery profilu FSLogix na pulpicie wirtualnym systemu Windows.

W tym artykule założono, że masz już [skonfigurowane pule hostów](create-host-pools-azure-marketplace.md) i pogrupowane w co najmniej jedną dzierżawę w środowisku pulpitu wirtualnego systemu Windows. Aby dowiedzieć się, jak skonfigurować dzierżawców, zobacz [Tworzenie dzierżawy na pulpicie wirtualnym systemu Windows](tenant-setup-azure-active-directory.md) i wpis w blogu społeczności [technicznej.](https://techcommunity.microsoft.com/t5/Windows-IT-Pro-Blog/Getting-started-with-Windows-Virtual-Desktop/ba-p/391054)

Instrukcje zawarte w tym przewodniku są przeznaczone specjalnie dla użytkowników pulpitu wirtualnego systemu Windows. Jeśli szukasz bardziej ogólnych wskazówek dotyczących konfigurowania plików NetApp platformy Azure i tworzenia kontenerów profilu FSLogix poza pulpitem wirtualnym systemu Windows, zobacz [Konfigurowanie plików NetApp platformy Azure i tworzenie szybkiego startu woluminu NFS](../azure-netapp-files/azure-netapp-files-quickstart-set-up-account-create-volumes.md).

>[!NOTE]
>W tym artykule nie okryje się najlepsze rozwiązania dotyczące zabezpieczania dostępu do udziału plików NetApp platformy Azure.

>[!NOTE]
>Jeśli szukasz materiałów porównawczych dotyczących różnych opcji magazynu kontenerów profilu FSLogix na platformie Azure, zobacz [Opcje usługi Storage dla kontenerów profilu FSLogix](store-fslogix-profile.md).

## <a name="prerequisites"></a>Wymagania wstępne

Przed utworzeniem kontenera profilu FSLogix dla puli hostów należy:

- Konfigurowanie i konfigurowanie pulpitu wirtualnego systemu Windows
- Aprowizuj pulę hosta pulpitu wirtualnego systemu Windows
- [Włącz subskrypcję usługi Azure NetApp Files](../azure-netapp-files/azure-netapp-files-register.md)

## <a name="set-up-your-azure-netapp-files-account"></a>Konfigurowanie konta usługi Azure NetApp Files

Aby rozpocząć, musisz skonfigurować konto usługi Azure NetApp Files.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Upewnij się, że twoje konto ma uprawnienia współautora lub administratora.

2. Wybierz **ikonę usługi Azure Cloud Shell** po prawej stronie paska wyszukiwania, aby otworzyć usługę Azure Cloud Shell.

3. Po otwarciu usługi Azure Cloud Shell wybierz pozycję **PowerShell**.

4. Jeśli po raz pierwszy korzystasz z usługi Azure Cloud Shell, utwórz konto magazynu w tej samej subskrypcji, w której zachowasz pliki NetApp platformy Azure i pulpit wirtualny systemu Windows.

   ![Okno konta magazynu z przyciskiem utwórz magazyn u dołu okna podświetlony na czerwono.](media/create-storage-button.png)

5. Po załadowaniu usługi Azure Cloud Shell uruchom następujące dwa polecenia cmdlet.

   ```azurecli
   az account set --subscription <subscriptionID>
   ```

   ```azurecli
   az provider register --namespace Microsoft.NetApp --wait
   ```

6. Po lewej stronie okna wybierz pozycję **Wszystkie usługi**. Wprowadź **pliki netapp platformy Azure** w polu wyszukiwania, które pojawia się w górnej części menu.

   ![Zrzut ekranu przedstawiający użytkownika wprowadzającego "Pliki usługi Azure NetApp" w polu wyszukiwania Wszystkie usługi. Wyniki wyszukiwania pokazują zasób Usługi Azure NetApp Files.](media/azure-netapp-files-search-box.png)


7. Wybierz **pozycję Pliki netapp platformy Azure** w wynikach wyszukiwania, a następnie wybierz pozycję **Utwórz**.

8. Wybierz przycisk **Add** (Dodaj).
9. Po otwarciu karty **Nowe konto NetApp** wprowadź następujące wartości:

    - W przypadku **nazwy**wprowadź nazwę konta NetApp.
    - W przypadku **opcji Subskrypcja**wybierz subskrypcję konta magazynu skonfigurowanego w kroku 4 z menu rozwijanego.
    - W przypadku **grupy zasobów**wybierz istniejącą grupę zasobów z menu rozwijanego lub utwórz nową, wybierając pozycję **Utwórz nowy**.
    - W obszarze **Lokalizacja**wybierz region konta NetApp z menu rozwijanego. Ten region musi być tym samym regionem co maszyny wirtualne hosta sesji.

   >[!NOTE]
   >Usługa Azure NetApp Files obecnie nie obsługuje instalowania woluminu w różnych regionach.

10. Po zakończeniu wybierz pozycję **Utwórz,** aby utworzyć konto NetApp.

## <a name="create-a-capacity-pool"></a>Tworzenie puli pojemności

Następnie utwórz nową pulę pojemności: 

1. Przejdź do menu Usługi Azure NetApp Files i wybierz nowe konto.
2. W menu konta wybierz **pule pojemności w** obszarze Usługa magazynowania.
3. Wybierz **pozycję Dodaj pulę**.
4. Po otwarciu karty **Nowa pula pojemności** wprowadź następujące wartości:

    - W **yjmij nazwę**wprowadź nazwę nowej puli pojemności.
    - W polu **Poziom serwisu**wybierz żądaną wartość z menu rozwijanego. Firma Microsoft zaleca **Premium** dla większości środowisk.
       >[!NOTE]
       >Ustawienie Premium zapewnia minimalną przepustowość dostępną dla poziomu usługi Premium, który wynosi 256 MB/s. Może być konieczne dostosowanie tej przepływności dla środowiska produkcyjnego. Ostateczna przepływność jest oparta na relacji opisanej w [limitach przepływności](../azure-netapp-files/azure-netapp-files-service-levels.md).
    - W przypadku **rozmiaru (TiB)** wprowadź rozmiar puli pojemności, który najlepiej odpowiada Twoim potrzebom. Minimalny rozmiar to 4 TiB.

5. Po zakończeniu wybierz opcję **OK**.

## <a name="join-an-active-directory-connection"></a>Dołączanie do połączenia usługi Active Directory

Następnie należy dołączyć do połączenia usługi Active Directory.

1. Wybierz **pozycję Połączenia usługi Active Directory** w menu po lewej stronie strony, a następnie wybierz przycisk **Dołącz,** aby otworzyć stronę **Dołącz do usługi Active Directory.**

   ![Zrzut ekranu przedstawiający menu Dołącz połączenia usługi Active Directory.](media/active-directory-connections-menu.png)

2. Wprowadź następujące wartości na stronie **Dołącz do usługi Active Directory,** aby dołączyć do połączenia:

    - W przypadku **podstawowego systemu DNS**wprowadź adres IP serwera DNS w swoim środowisku, który może rozwiązać nazwę domeny.
    - W przypadku **domeny**wprowadź w pełni kwalifikowaną nazwę domeny (FQDN).
    - W przypadku **prefiksu serwera (konta komputera) SMB**wprowadź ciąg, który chcesz dołączyć do nazwy konta komputera.
    - W obszarze **Nazwa użytkownika**wprowadź nazwę konta z uprawnieniami do dołączenia do domeny.
    - W obszarze **Hasło**wprowadź hasło konta.

## <a name="create-a-new-volume"></a>Tworzenie nowego woluminu

Następnie musisz utworzyć nowy wolumin.

1. Wybierz **pozycję Woluminy**, a następnie wybierz pozycję **Dodaj objętość**.

2. Po otwarciu karty **Utwórz wolumin** wprowadź następujące wartości:

    - W przypadku **nazwy woluminu**wprowadź nazwę nowego woluminu.
    - W przypadku **puli pojemności**wybierz pulę pojemności utworzoną właśnie z menu rozwijanego.
    - W przypadku **przydziału (GiB)** wprowadź rozmiar woluminu odpowiedni dla danego środowiska.
    - W przypadku **sieci wirtualnej**wybierz istniejącą sieć wirtualną, która ma łączność z kontrolerem domeny z menu rozwijanego.
    - W **obszarze Podsieć**wybierz pozycję **Utwórz nowy**. Należy pamiętać, że ta podsieć zostanie delegowana do plików NetApp platformy Azure.

3.  Wybierz **przycisk \> Dalej: Protokół,** aby otworzyć kartę Protokół i skonfigurować parametry dostępu do woluminu.

## <a name="configure-volume-access-parameters"></a>Konfigurowanie parametrów dostępu do woluminu

Po utworzeniu woluminu skonfiguruj parametry dostępu do woluminu.

1.  Wybierz **SMB** jako typ protokołu.
2.  W obszarze Konfiguracja w menu rozwijanym **usługi Active Directory** wybierz ten sam katalog, który został pierwotnie połączony w obszarze [Dołączanie do połączenia usługi Active Directory](create-fslogix-profile-container.md#join-an-active-directory-connection). Należy pamiętać, że istnieje limit jednego usługi Active Directory na subskrypcję.
3.  W polu tekstowym **Nazwa udziału** wprowadź nazwę udziału używanego przez pulę hosta sesji i jej użytkowników.

4.  Wybierz **pozycję Recenzja + utwórz** u dołu strony. Spowoduje to otwarcie strony sprawdzania poprawności. Po pomyślnym sprawdzeniu poprawności woluminu wybierz pozycję **Utwórz**.

5.  W tym momencie nowy wolumin rozpocznie wdrażanie. Po zakończeniu wdrażania można użyć udziału plików NetApp platformy Azure.

6.  Aby wyświetlić ścieżkę instalacji, wybierz pozycję **Przejdź do zasobu** i poszukaj jej na karcie Przegląd.

    ![Zrzut ekranu ekranu Przegląd z czerwoną strzałką skierowaną na ścieżkę instalacji.](media/overview-mount-path.png)

## <a name="configure-fslogix-on-session-host-virtual-machines-vms"></a>Konfigurowanie FSLogix na maszynach wirtualnych hosta sesji (VM)

Ta sekcja jest oparta na [Tworzenie kontenera profilu dla puli hostów przy użyciu udziału plików](create-host-pools-user-profile.md).

1. [Pobierz plik zip agenta FSLogix,](https://go.microsoft.com/fwlink/?linkid=2084562&clcid=0x409) gdy nadal jesteś zdalnie na maszynie wirtualnej hosta sesji.

2. Rozpaj pobrany plik.

3. W pliku przejdź do **x64** > **Releases** i uruchom **plik FSLogixAppsSetup.exe**. Otworzy się menu instalacyjne.

4.  Jeśli masz klucz produktu, wprowadź go w polu tekstowym Klucz produktu.

5. Zaznacz pole wyboru obok **wyrażam zgodę na warunki licencji.**

6. Wybierz pozycję **Zainstaluj**.

7. Przejdź do **\\C:\\Program Files\\FSLogix Apps,** aby potwierdzić zainstalowany agent.

8. Z menu Start uruchom **program RegEdit** jako administrator.

9. Przejdź do **aplikacji\\Komputer\\\\HKEY_LOCAL_MACHINE FSLogix**.

10. Utwórz klucz o nazwie **Profile**.

11.  Utwórz wartość o nazwie **Włączone** z typem **REG_DWORD** ustawionym na wartość danych **1**.

12. Utwórz wartość o nazwie **VHDLocations** z typem **wielosąsowym** i ustaw jego wartość danych na identyfikator URI dla udziału plików NetApp platformy Azure.

13. Utwórz wartość o nazwie **DeleteLocalProfileWhenVHDShouldApply** z wartość DWORD 1, aby uniknąć problemów z istniejącymi profilami lokalnymi przed zalogowaniem się.

     >[!WARNING]
     >Należy zachować ostrożność podczas tworzenia DeleteLocalProfileWhenVHDShouldApply wartość. Gdy system FSLogix Profiles określa, że użytkownik powinien mieć profil FSLogix, ale profil lokalny już istnieje, kontener profilu trwale usunie profil lokalny. Użytkownik zostanie zalogowany za pomocą nowego profilu FSLogix.

## <a name="assign-users-to-session-host"></a>Przypisywanie użytkowników do hosta sesji

1. Otwórz **program PowerShell ISE** jako administrator i zaloguj się na pulpicie wirtualnym systemu Windows.

2. Uruchom następujące polecenia cmdlet:

   ```powershell
   Import-Module Microsoft.RdInfra.RdPowershell
   # (Optional) Install-Module Microsoft.RdInfra.RdPowershell
   $brokerurl = "https://rdbroker.wvd.microsoft.com"
   Add-RdsAccount -DeploymentUrl $brokerurl
   ```

3. Po wyświetleniu monitu o poświadczenia wprowadź poświadczenia dla użytkownika za pomocą ról Kreator dzierżawy lub Właściciel usług PULPITU ZDALNEGO/Współautor usług pulpitu zdalnego w dzierżawie pulpitu wirtualnego systemu Windows.

4. Uruchom następujące polecenia cmdlet, aby przypisać użytkownika do grupy pulpitu zdalnego:

   ```powershell
   $wvdTenant = "<your-wvd-tenant>"
   $hostPool = "<wvd-pool>"
   $appGroup = "Desktop Application Group"
   $user = "<user-principal>"
   Add-RdsAppGroupUser $wvdTenant $hostPool $appGroup $user
   ```

## <a name="make-sure-users-can-access-the-azure-netapp-file-share"></a>Upewnij się, że użytkownicy mogą uzyskać dostęp do udziału plików NetApp platformy Azure

1. Otwórz przeglądarkę internetową <https://rdweb.wvd.microsoft.com/webclient/index.html>i przejdź do pliku .

2. Zaloguj się przy użyciu poświadczeń użytkownika przypisanego do grupy Pulpit zdalny.

3. Po ustanowieniu sesji użytkownika zaloguj się do witryny Azure portal przy za pomocą konta administracyjnego.

4. Otwórz **usługę Azure NetApp Files**, wybierz swoje konto usługi Azure NetApp Files, a następnie wybierz pozycję **Woluminy**. Po otwarciu menu Woluminy wybierz odpowiednią objętość.

   ![Zrzut ekranu przedstawiający konto NetApp skonfigurowane wcześniej w witrynie Azure portal z wybranym przyciskiem Woluminy.](media/netapp-account.png)

5. Przejdź do **karty Przegląd** i upewnij się, że kontener profilu FSLogix używa miejsca.

6. Połącz się bezpośrednio z dowolną częścią maszyny Wirtualnej puli hostów za pomocą pulpitu zdalnego i otwórz **Eksploratora plików.** Następnie przejdź do **ścieżki instalacji** (w poniższym \\ \\przykładzie\\ścieżka instalacji jest anf-SMB-3863.gt1107.onmicrosoft.com anf-VOL).

   W tym folderze powinien istnieć profil VHD (lub VHDX) podobny do tego w poniższym przykładzie.

   ![Zrzut ekranu zawartości folderu w ścieżce instalacji. Wewnątrz znajduje się pojedynczy plik VHD o nazwie "Profile_ssbb".](media/mount-path-folder.png)

## <a name="next-steps"></a>Następne kroki

Kontenery profilu FSLogix można użyć do skonfigurowania udziału profilu użytkownika. Aby dowiedzieć się, jak tworzyć udziały profilu użytkownika za pomocą nowych kontenerów, zobacz [Tworzenie kontenera profilu dla puli hostów przy użyciu udziału plików](create-host-pools-user-profile.md).
