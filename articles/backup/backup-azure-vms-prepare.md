---
title: Przygotowywanie środowiska do tworzenia kopii zapasowych maszyn wirtualnych platformy Azure | Dokumentacja firmy Microsoft
description: Upewnij się, że środowisko jest przygotowane do tworzenia kopii zapasowych maszyn wirtualnych na platformie Azure
services: backup
documentationcenter: ''
author: markgalioto
manager: carmonn
editor: ''
keywords: Tworzenie kopii zapasowych; Tworzenie kopii zapasowej;
ms.assetid: 238ab93b-8acc-4262-81b7-ce930f76a662
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 9/10/2017
ms.author: cwatson
ms.openlocfilehash: 03bb4c3e68643ded686c20b7ba0d5d079a9d4057
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
---
# <a name="prepare-your-environment-to-back-up-azure-virtual-machines"></a>Przygotowywanie środowiska do tworzenia kopii zapasowych maszyn wirtualnych platformy Azure
> [!div class="op_single_selector"]
> * [Model usługi Resource Manager](backup-azure-arm-vms-prepare.md)
> * [Modelu klasycznego](backup-azure-vms-prepare.md)
>
>

Przed utworzeniem kopii zapasowej maszyny wirtualnej platformy Azure (VM), istnieją trzy warunki, które musi istnieć.

* Musisz utworzyć magazyn kopii zapasowych lub wskazać istniejącego magazynu kopii zapasowych *w tym samym regionie co maszyna wirtualna*.
* Ustanów połączenie sieciowe między Azure publicznych adresów internetowych i punktów końcowych usługi Azure storage.
* Zainstaluj agenta maszyny Wirtualnej w maszynie Wirtualnej.

Jeśli znasz te warunki już istnieje w danym środowisku, a następnie przejdź do [kopii zapasowych maszyn wirtualnych artykuł](backup-azure-vms.md). W przeciwnym razie odczytać, ten artykuł przeprowadzi Cię przez kroki w celu przygotowania środowiska do tworzenia kopii zapasowych maszyny Wirtualnej platformy Azure.

##<a name="supported-operating-system-for-backup"></a>Obsługiwany system operacyjny do utworzenia kopii zapasowej
 * **Linux**: usługa Azure Backup obsługuje [dystrybucje zalecane dla platformy Azure](../virtual-machines/linux/endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) z wyjątkiem systemu operacyjnego Linux Core. _Innych Bring-Your-właścicielem — dystrybucje systemu Linux mogą również działać, dopóki agent maszyny Wirtualnej jest dostępne na maszynie wirtualnej i obsługę języka Python istnieje. Jednak firma Microsoft nie zatwierdza tych dystrybucji dla kopii zapasowej._
 * **Windows Server**: wersje starsze niż Windows Server 2008 R2 nie są obsługiwane.


## <a name="limitations-when-backing-up-and-restoring-a-vm"></a>Ograniczenia w przypadku tworzenia kopii zapasowej i przywracanie maszyny Wirtualnej
> [!NOTE]
> Platforma Azure ma dwa modele wdrażania związane z tworzeniem i pracą z zasobami: [Resource Manager i Model Klasyczny](../azure-resource-manager/resource-manager-deployment-model.md). Poniższa lista zawiera ograniczenia w przypadku wdrażania w klasycznym modelu.
>
>

* Tworzenie kopii zapasowych maszyn wirtualnych z więcej niż 16 dysków danych nie jest obsługiwane.
* Tworzenie kopii zapasowych maszyn wirtualnych z zastrzeżonego adresu IP i nie zdefiniowanych punktów końcowych nie jest obsługiwane.
* Dane kopii zapasowej nie zawiera dyski sieciowe zainstalowane podłączona do maszyny Wirtualnej.
* Zamiana istniejącej maszyny wirtualnej podczas przywracania nie jest obsługiwana. Najpierw usuń istniejącą maszynę wirtualną i wszystkie skojarzone dyski, a następnie przywróć dane z kopii zapasowej.
* Region między kopii zapasowej i przywracania nie jest obsługiwana.
* Tworzenie kopii zapasowych maszyn wirtualnych za pomocą usługi Kopia zapasowa Azure jest obsługiwane we wszystkich regionach publicznej platformy Azure (zobacz [Lista kontrolna](https://azure.microsoft.com/regions/#services) obsługiwanych regionów). Jeśli obecnie jest obsługiwany region, którego szukasz, nie zostanie wyświetlony na liście rozwijanej podczas tworzenia magazynu.
* Tworzenie kopii zapasowych maszyn wirtualnych za pomocą usługi Kopia zapasowa Azure jest obsługiwana tylko dla wersji systemu operacyjnego wybierz:
* Przywracanie kontrolera domeny (DC) maszyny Wirtualnej, która jest częścią konfiguracji kontrolera domeny na wielu jest obsługiwane tylko za pomocą programu PowerShell. Przeczytaj więcej na temat [Przywracanie kontrolera domeny, kontrolera domeny na wielu](backup-azure-restore-vms.md#restoring-domain-controller-vms).
* Przywracanie maszyn wirtualnych, które mają następujące konfiguracje sieciowe specjalne jest obsługiwane tylko za pomocą programu PowerShell. Maszyny wirtualne utworzone za pomocą przepływu pracy przywracania w interfejsie użytkownika nie będą miały te konfiguracje sieci po zakończeniu operacji przywracania. Aby dowiedzieć się więcej, zobacz [przywracanie maszyn wirtualnych z konfiguracjami sieci specjalne](backup-azure-restore-vms.md#restoring-vms-with-special-network-configurations).
  * Maszyny wirtualne znajdujące się w konfiguracji usługi równoważenia obciążenia (wewnętrznych i zewnętrznych)
  * Maszyny wirtualne z wielu zastrzeżonych adresów IP
  * Maszyny wirtualne z wieloma kartami sieciowymi

## <a name="create-a-backup-vault-for-a-vm"></a>Utwórz magazyn kopii zapasowych dla maszyny Wirtualnej
Magazyn kopii zapasowych to jednostka, w której przechowywane są wszystkie kopie zapasowe i punkty odzyskiwania, które zostały utworzone na przestrzeni czasu. Magazyn kopii zapasowych zawiera również zasady tworzenia kopii zapasowej, które będą stosowane do maszyn wirtualnych, z których powstaje kopia zapasowa.

> [!IMPORTANT]
> Począwszy od marca 2017 r. nie można już tworzyć magazynów kopii zapasowych za pomocą klasycznego portalu. Istniejące magazyny kopii zapasowych są nadal obsługiwane i możliwe jest [użycie środowiska Azure PowerShell w celu utworzenia magazynów kopii zapasowych](./backup-client-automation-classic.md#create-a-backup-vault). Firma Microsoft zaleca jednak tworzenie dla wszystkich wdrożeń magazynów usługi Recovery Services, ponieważ przyszłe rozszerzenia będą miały zastosowanie tylko do magazynów tej usługi.


Ten obraz zawiera relacje między różnymi jednostkami kopia zapasowa Azure: ![kopia zapasowa Azure jednostki i relacje](./media/backup-azure-vms-prepare/vault-policy-vm.png)



## <a name="network-connectivity"></a>Połączenie sieciowe
Aby zarządzać migawek maszyny Wirtualnej, zapasowy numer wewnętrzny musi mieć łączność Azure publicznych adresów IP. Bez prawo łączność z Internetem limit czasu żądania HTTP maszyny wirtualnej i tworzenia kopii zapasowej nie powiedzie się. Jeśli wdrożenie obowiązują ograniczenia dostępu w (za pośrednictwem sieciową grupę zabezpieczeń (NSG), na przykład), wybierz jedną z następujących opcji do prezentowania wyczyść ścieżki dla kopii zapasowej ruchu:

* [Lista dozwolonych adresów IP centrum danych Azure zakresów](http://www.microsoft.com/en-us/download/details.aspx?id=41653) — zobacz artykuł instrukcje w sposób do listy dozwolonych adresów IP.
* Wdrażanie serwera proxy HTTP dla routingu ruchu.

Podczas wybierania opcji, kompromisy należą do zakresu od możliwości zarządzania, kontrolę i kosztów.

| Opcja | Zalety | Wady |
| --- | --- | --- |
| Lista dozwolonych adresów IP, zakresów |Brak dodatkowych kosztów.<br><br>Do otwierania dostępu w grupy NSG, użyj <i>AzureNetworkSecurityRule zestaw</i> polecenia cmdlet. |Złożone, aby zarządzać jako objęte wpływem zakresów IP ulec zmianie.<br><br>Zapewnia dostęp do całej platformy Azure i nie tylko magazynu. |
| Serwer proxy HTTP |Dozwolone adresy URL kontrolę na serwerze proxy za pośrednictwem magazynu. Aby kontrolę Instalatora na serwerze proxy https://\*.blob.core.windows.net/\* wzorca adresu URL musi być białej. Do listy dozwolonych tylko konta magazynu, które są używane przez maszynę Wirtualną https://\<storageAccount\>.blob.core.windows.net/\* wzorca adresu URL musi być białej. <br>Pojedynczy punkt Internet dostęp do maszyn wirtualnych.<br>Nie może ulec zmiany adresu IP platformy Azure. |Dodatkowe koszty uruchomioną maszynę Wirtualną z oprogramowaniem serwera proxy. |

### <a name="whitelist-the-azure-datacenter-ip-ranges"></a>Zakresy IP dozwolonych centrum danych Azure
Do listy dozwolonych zakresy IP centrum danych Azure, zobacz [witryny sieci Web Azure](http://www.microsoft.com/en-us/download/details.aspx?id=41653) szczegółowe informacje na temat zakresów adresów IP i instrukcje.

### <a name="using-an-http-proxy-for-vm-backups"></a>Za pomocą serwera proxy HTTP dla kopii zapasowych maszyn wirtualnych
Podczas tworzenia kopii zapasowej maszyny Wirtualnej, tworzenia kopii zapasowej rozszerzenia na maszynie Wirtualnej wysyła migawki polecenia zarządzania do magazynu Azure przy użyciu interfejsu API protokołu HTTPS. Kierować ruchem zapasowy numer wewnętrzny, za pośrednictwem serwera proxy HTTP, ponieważ jest jedynym składnikiem skonfigurowany do uzyskiwania dostępu do publicznej sieci Internet.

> [!NOTE]
> Nie ma żadnych zalecenia dotyczące oprogramowania serwera proxy, który ma zostać użyty. Upewnij się, że wybierz serwer proxy, który ma lepkości wychodzących i który jest zgodny z poniższych czynności konfiguracyjne. Upewnij się, że oprogramowanie innych firm nie należy modyfikować ustawienia serwera proxy
>
>

Obraz przykład poniżej przedstawiono kroki trzech konfiguracji koniecznych do używania serwera proxy HTTP:

* Maszyna wirtualna aplikacji kieruje cały ruch HTTP do publicznego Internetu za pośrednictwem serwera Proxy maszyny Wirtualnej.
* Maszyna wirtualna serwera proxy zezwala na ruch przychodzący z maszyn wirtualnych w sieci wirtualnej.
* Grupy zabezpieczeń sieci (NSG) o nazwie NF blokady musi zabezpieczeń reguły zezwalanie Internet ruch wychodzący z maszyny Wirtualnej serwera Proxy.

![Grupy NSG z diagram wdrożenia serwera proxy HTTP](./media/backup-azure-vms-prepare/nsg-with-http-proxy.png)

Aby użyć serwera proxy HTTP do komunikacji z Internetem, wykonaj następujące kroki:

#### <a name="step-1-configure-outgoing-network-connections"></a>Krok 1. Konfigurowanie połączeń wychodzących sieci
###### <a name="for-windows-machines"></a>W przypadku komputerów z systemem Windows
Będzie to ustawienia konfiguracji serwera proxy dla lokalnego konta systemowego.

1. Pobierz [PsExec](https://technet.microsoft.com/sysinternals/bb897553)
2. Uruchom następujące polecenia z podwyższonym poziomem uprawnień wiersza

     ```
     psexec -i -s "c:\Program Files\Internet Explorer\iexplore.exe"
     ```
     Otworzy się okno programu internet explorer.
3. Przejdź do pozycji Narzędzia -> Opcje internetowe -> połączenia -> Ustawienia sieci LAN.
4. Sprawdź ustawienia serwera proxy dla konta System. Ustaw Proxy adresu IP i portu.
5. Zamknij program Internet Explorer.

Zostanie utworzenie konfiguracji serwera proxy dla komputera i będą używane dla każdego wychodzącego ruchu HTTP/HTTPS.

Jeśli skonfigurowano serwer proxy dla bieżącego konta użytkownika (nie lokalnego konta systemowego), użyj następującego skryptu, aby zastosować je do SYSTEMACCOUNT:

```
   $obj = Get-ItemProperty -Path Registry::”HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections"
   Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name DefaultConnectionSettings -Value $obj.DefaultConnectionSettings
   Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name SavedLegacySettings -Value $obj.SavedLegacySettings
   $obj = Get-ItemProperty -Path Registry::”HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings"
   Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name ProxyEnable -Value $obj.ProxyEnable
   Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name Proxyserver -Value $obj.Proxyserver
```

> [!NOTE]
> Jeśli zauważysz "(407) uwierzytelniania serwera Proxy wymagane" w dzienniku serwera proxy, sprawdź, czy uwierzytelnianie jest poprawnie skonfigurowana.
>
>

###### <a name="for-linux-machines"></a>Dla maszyn z systemem Linux
Dodaj następujący wiersz do ```/etc/environment``` pliku:

```
http_proxy=http://<proxy IP>:<proxy port>
```

Dodaj następujące wiersze do ```/etc/waagent.conf``` pliku:

```
HttpProxy.Host=<proxy IP>
HttpProxy.Port=<proxy port>
```

#### <a name="step-2-allow-incoming-connections-on-the-proxy-server"></a>Krok 2. Zezwalaj na połączenia przychodzące na serwerze proxy:
1. Na serwerze proxy otwórz Zaporę systemu Windows. Najprostszym sposobem dostęp zapory jest do wyszukiwania Zapora systemu Windows z zabezpieczeniami zaawansowanymi.

    ![Otwórz Zaporę](./media/backup-azure-vms-prepare/firewall-01.png)
2. W oknie dialogowym zapory systemu Windows, kliknij prawym przyciskiem myszy **reguły ruchu przychodzącego** i kliknij przycisk **nową regułę...** .

    ![Utwórz nową regułę](./media/backup-azure-vms-prepare/firewall-02.png)
3. W **Kreatora nowej reguły przychodzącej**, wybierz **niestandardowy** opcja dla **typ reguły** i kliknij przycisk **dalej**.
4. Na stronie, aby wybrać **Program**, wybierz **wszystkie programy** i kliknij przycisk **dalej**.
5. Na **protokoły i porty** , wprowadź następujące informacje i kliknij przycisk **dalej**:

    ![Utwórz nową regułę](./media/backup-azure-vms-prepare/firewall-03.png)

   * Aby uzyskać *protokół typu* wybierz *TCP*
   * dla *portów lokalnych* wybierz *określonych portów*, w poniższym polu Określ ```<Proxy Port>``` który został skonfigurowany.
   * Aby uzyskać *port zdalny* wybierz *wszystkie porty*

     W pozostałej części kreatora kliknij przycisk aż do zakończenia i nadaj nazwę tej reguły.

#### <a name="step-3-add-an-exception-rule-to-the-nsg"></a>Krok 3. Dodaj regułę wyjątku do grupy NSG:
W wierszu polecenia programu PowerShell systemu Azure wprowadź następujące polecenie:

Polecenie dodaje wyjątek do grupy NSG. Ten wyjątek zezwala na ruch TCP z dowolnego portu na 10.0.0.5 do dowolnego adresu internetowego na porcie 80 (HTTP) lub 443 (HTTPS). Jeśli potrzebujesz określonego portu w publicznej sieci Internet, należy dodać tego portu do ```-DestinationPortRange``` również.

```
Get-AzureNetworkSecurityGroup -Name "NSG-lockdown" |
Set-AzureNetworkSecurityRule -Name "allow-proxy " -Action Allow -Protocol TCP -Type Outbound -Priority 200 -SourceAddressPrefix "10.0.0.5/32" -SourcePortRange "*" -DestinationAddressPrefix Internet -DestinationPortRange "80-443"
```

*Upewnij się, Zastąp nazwy w przykładzie ze szczegółami odpowiednich dla danego wdrożenia.*

## <a name="vm-agent"></a>Agent maszyny Wirtualnej
Przed utworzeniem kopii zapasowej maszyny wirtualnej platformy Azure, należy się upewnić, że agent maszyny Wirtualnej platformy Azure jest poprawnie zainstalowane na maszynie wirtualnej. Ponieważ agenta maszyny Wirtualnej jest opcjonalnym składnikiem w czasie tworzenia maszyny wirtualnej, upewnij się, że zaznaczono pole wyboru dla agenta maszyny Wirtualnej przed udostępnieniu maszyny wirtualnej.

### <a name="manual-installation-and-update"></a>Aktualizacja i Instalacja ręczna
Agent maszyny Wirtualnej jest już obecny na maszynach wirtualnych, które są tworzone z poziomu galerii Azure. Jednakże maszyn wirtualnych, które są migrowane z lokalnych centrów danych nie mieć zainstalowanego agenta maszyny Wirtualnej. Dla tych maszyn wirtualnych agent maszyny Wirtualnej musi być jawnie zainstalowany. 

| **Operacja** | **Windows** | **Linux** |
| --- | --- | --- |
| Instalowanie agenta maszyny wirtualnej |Pobierz i zainstaluj [plik MSI agenta](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Do ukończenia procesu instalacji niezbędne są uprawnienia administratora.<li>[Zaktualizuj właściwości maszyny wirtualnej](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx), aby wskazać, że agent jest zainstalowany. |<li> Zainstaluj najnowszą [agenta systemu Linux](../virtual-machines/extensions/agent-linux.md). Do ukończenia procesu instalacji niezbędne są uprawnienia administratora. Zaleca się zainstalowanie agenta z repozytorium dystrybucji. Firma Microsoft **nie jest zalecane** Instalowanie agenta maszyny Wirtualnej systemu Linux bezpośrednio z usługi github.  |
| Aktualizowanie agenta maszyny wirtualnej |Aktualizowanie agenta maszyny wirtualnej jest równie proste, jak ponowne zainstalowanie [plików binarnych agenta maszyny wirtualnej](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). <br>Upewnij się, że żadna operacja tworzenia kopii zapasowej nie jest uruchomiona podczas aktualizowania agenta maszyny wirtualnej. |Postępuj zgodnie z instrukcjami dotyczącymi [aktualizowania agenta maszyny wirtualnej systemu Linux ](../virtual-machines/linux/update-agent.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Firma Microsoft zaleca aktualizacji agenta z repozytorium dystrybucji. Firma Microsoft **nie jest zalecane** aktualizacji agenta maszyny Wirtualnej systemu Linux bezpośrednio z usługi github.<br>Upewnij się, że podczas aktualizowania agenta maszyny wirtualnej żadna operacja tworzenia kopii zapasowej nie jest uruchomiona. |
| Sprawdzanie poprawności instalacji agenta maszyny wirtualnej |<li>Przejdź do folderu *C:\WindowsAzure\Packages* w maszynie wirtualnej Azure. <li>Powinien znajdować się w nim plik WaAppAgent.exe.<li> Kliknij plik prawym przyciskiem myszy, przejdź do opcji **Właściwości**, a następnie wybierz kartę **Szczegóły**. W polu Wersja produktu powinna znajdować się wartość 2.6.1198.718 lub wyższa. |ND |

Dowiedz się więcej o [agenta maszyny Wirtualnej](https://go.microsoft.com/fwLink/?LinkID=390493&clcid=0x409) i [sposobu jego instalacji](https://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/).

### <a name="backup-extension"></a>Rozszerzenie kopii zapasowej
Aby utworzyć kopię zapasową maszyny wirtualnej, usługi Azure Backup instaluje rozszerzenia agenta maszyny Wirtualnej. Usługa Azure Backup płynnie uaktualnia rozszerzenia kopii zapasowej i wprowadza do nich poprawki bez dodatkowej interwencji użytkownika.

Zapasowy numer wewnętrzny jest zainstalowany, jeśli maszyna wirtualna jest uruchomiona. Uruchomionych maszynach wirtualnych zapewnia także największe prawdopodobieństwo pobieranie punktów odzyskiwania zapewniających spójność aplikacji. Jednak kopia zapasowa Azure usługi będą w dalszym ciągu kopię zapasową maszyny Wirtualnej — nawet jeśli jest wyłączona, a rozszerzenie nie może być zainstalowana (alias w trybie Offline maszyny Wirtualnej). W takim przypadku punkt odzyskiwania będzie *awaria spójne* zgodnie z powyższym opisem.

## <a name="questions"></a>Pytania?
Jeśli masz pytania lub jeśli brakuje Ci jakiejś funkcji, [prześlij nam opinię](http://aka.ms/azurebackup_feedback).

## <a name="next-steps"></a>Kolejne kroki
Teraz, kiedy przygotowania środowiska do tworzenia kopii zapasowej maszyny Wirtualnej, następnym krokiem logicznej jest utworzenie kopii zapasowej. Planowania artykuł zawiera bardziej szczegółowe informacje o tworzeniu kopii zapasowych maszyn wirtualnych.

* [Tworzenie kopii zapasowych maszyn wirtualnych](backup-azure-vms.md)
* [Zaplanuj infrastrukturę kopii zapasowej maszyny Wirtualnej](backup-azure-vms-introduction.md)
* [Zarządzanie kopii zapasowych maszyn wirtualnych](backup-azure-manage-vms.md)
