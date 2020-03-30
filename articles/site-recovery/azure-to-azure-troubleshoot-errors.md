---
title: Rozwiązywanie problemów z replikacją maszyn wirtualnych platformy Azure w usłudze Azure Site Recovery
description: Rozwiązywanie problemów z błędami podczas replikowania maszyn wirtualnych platformy Azure w celu odzyskiwania po awarii.
author: rochakm
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/08/2019
ms.author: rochakm
ms.openlocfilehash: c131609a5622061e2ea49db422bc4e9da96666d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80276686"
---
# <a name="troubleshoot-azure-to-azure-vm-replication-errors"></a>Rozwiązywanie problemów z błędami replikacji maszyny wirtualnej platformy Azure do platformy Azure

W tym artykule opisano, jak rozwiązywać typowe błędy w usłudze Azure Site Recovery podczas replikacji i odzyskiwania maszyn wirtualnych platformy Azure (VM) z jednego regionu do drugiego. Aby uzyskać więcej informacji na temat obsługiwanych konfiguracji, zobacz [macierz obsługi replikacji maszyn wirtualnych platformy Azure](site-recovery-support-matrix-azure-to-azure.md).

## <a name="azure-resource-quota-issues-error-code-150097"></a><a name="azure-resource-quota-issues-error-code-150097"></a>Problemy z przydziałem zasobów platformy Azure (kod błędu 150097)

Upewnij się, że subskrypcja jest włączona do tworzenia maszyn wirtualnych platformy Azure w regionie docelowym, który ma być używany jako region odzyskiwania po awarii. Upewnij się również, że subskrypcja ma wystarczający przydział do tworzenia maszyn wirtualnych o niezbędnych rozmiarach. Domyślnie usługa Site Recovery wybiera docelowy rozmiar maszyny Wirtualnej, który jest taki sam jak rozmiar źródłowej maszyny Wirtualnej. Jeśli pasujący rozmiar nie jest dostępny, usługa Site Recovery automatycznie wybiera najbliższy dostępny rozmiar.

Jeśli nie ma rozmiaru obsługującego konfigurację źródłowej maszyny Wirtualnej, zostanie wyświetlony następujący komunikat:

> "Nie można włączyć replikacji dla maszyny wirtualnej *VmName*."

### <a name="possible-causes"></a>Możliwe przyczyny

- Identyfikator subskrypcji nie jest włączona do tworzenia żadnych maszyn wirtualnych w lokalizacji regionu docelowego.
- Identyfikator subskrypcji nie jest włączony lub nie ma wystarczającego przydziału, aby utworzyć określone rozmiary maszyn wirtualnych w lokalizacji regionu docelowego.
- Nie znaleziono odpowiedniego docelowego rozmiaru maszyny Wirtualnej, aby dopasować się do licznika karty interfejsu sieciowego maszyny Wirtualnej źródła (2) dla identyfikatora subskrypcji w lokalizacji regionu docelowego.

### <a name="fix-the-problem"></a>Rozwiązywanie problemu

Skontaktuj się z [pomocą techniczną rozliczeń platformy Azure,](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request) aby umożliwić subskrypcję w celu tworzenia maszyn wirtualnych o wymaganych rozmiarach w lokalizacji docelowej. Następnie ponów próbę wykonania nieudanej operacji.

Jeśli lokalizacja docelowa ma ograniczenie pojemności, wyłącz replikację do niego. Następnie włącz replikację do innej lokalizacji, w której subskrypcja ma wystarczający przydział do tworzenia maszyn wirtualnych o wymaganych rozmiarach.

## <a name="trusted-root-certificates-error-code-151066"></a><a name="trusted-root-certificates-error-code-151066"></a>Zaufane certyfikaty główne (kod błędu 151066)

Jeśli nie wszystkie najnowsze zaufane certyfikaty główne są obecne na maszynie wirtualnej, zadanie odzyskiwania lokacji "Włącz replikację" może zakończyć się niepowodzeniem. Uwierzytelnianie i autoryzacja wywołań usługi odzyskiwania lokacji z maszyny Wirtualnej zakończy się niepowodzeniem bez tych certyfikatów.

Jeśli zadanie "Włącz replikację" nie powiedzie się, zostanie wyświetlony następujący komunikat:

> "Konfiguracja odzyskiwania lokacji nie powiodła się".

### <a name="possible-cause"></a>Możliwa przyczyna

Zaufane certyfikaty główne wymagane do autoryzacji i uwierzytelniania nie są obecne na maszynie wirtualnej.

### <a name="fix-the-problem"></a>Rozwiązywanie problemu

#### <a name="windows"></a>Windows

W przypadku maszyny Wirtualnej z systemem operacyjnym Windows zainstaluj najnowsze aktualizacje systemu Windows na maszynie Wirtualnej, tak aby wszystkie zaufane certyfikaty główne były obecne na komputerze. Postępuj zgodnie z typowym procesem zarządzania aktualizacjami lub aktualizacjami certyfikatów systemu Windows w organizacji, aby uzyskać najnowsze certyfikaty główne i zaktualizowaną listę odwołania certyfikatów na maszynach wirtualnych.

Jeśli jesteś w środowisku rozłączony, postępuj zgodnie ze standardowym procesem aktualizacji systemu Windows w organizacji, aby uzyskać certyfikaty. Jeśli wymagane certyfikaty nie są obecne na maszynie Wirtualnej, wywołania usługi site recovery nie powiodą się ze względów bezpieczeństwa.

Aby sprawdzić, czy problem został rozwiązany, przejdź do login.microsoftonline.com z przeglądarki na maszynie Wirtualnej.

Aby uzyskać więcej informacji, zobacz [Konfigurowanie zaufanych katalogów głównych i niedozwolonych certyfikatów](https://technet.microsoft.com/library/dn265983.aspx).

#### <a name="linux"></a>Linux

Postępuj zgodnie ze wskazówkami dostarczonymi przez dystrybutora wersji systemu operacyjnego Linux, aby uzyskać najnowsze zaufane certyfikaty główne i najnowszą listę odwołania certyfikatów na maszynie wirtualnej.

Ponieważ SUSE Linux używa dowiązań symbolicznych (lub *dowiązań symbolicznych)* do obsługi listy certyfikatów, wykonaj następujące kroki:

1. Zaloguj się jako użytkownik root.

1. Uruchom to polecenie, aby zmienić katalog:

    **# cd /etc/ssl/certs**

1. Sprawdź, czy certyfikat głównego urzędu certyfikacji firmy Symantec jest obecny:

    **# ls VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem**

1. Jeśli certyfikat głównego urzędu certyfikacji firmy Symantec nie zostanie znaleziony, uruchom następujące polecenie, aby pobrać plik. Sprawdź, czy nie ma błędów i postępuj zgodnie z zalecanymi akcjami w przypadku awarii sieci.

    **# wget https://docs.broadcom.com/docs-and-downloads/content/dam/symantec/docs/other-resources/verisign-class-3-public-primary-certification-authority-g5-en.pem -O VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem**

1. Sprawdź, czy certyfikat głównego urzędu certyfikacji Baltimore jest obecny:

    **# ls Baltimore_CyberTrust_Root.pem**

1. Jeśli certyfikat głównego urzędu certyfikacji Baltimore nie został znaleziony, uruchom to polecenie, aby pobrać certyfikat:

    **# wget https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem -O Baltimore_CyberTrust_Root.pem**

1. Sprawdź, czy certyfikat DigiCert_Global_Root_CA jest obecny:

    **# ls DigiCert_Global_Root_CA.pem**

1. Jeśli DigiCert_Global_Root_CA nie zostanie znaleziony, uruchom następujące polecenia, aby pobrać certyfikat:

    **# wgethttp://www.digicert.com/CACerts/DigiCertGlobalRootCA.crt**

    **# openssl x509 -in DigiCertGlobalRootCA.crt -inform der -outform pem -out DigiCert_Global_Root_CA.pem**

1. Uruchom skrypt rehash, aby zaktualizować skróty tematu certyfikatu dla nowo pobranych certyfikatów:

    **# c_rehash**

1. Uruchom następujące polecenia, aby sprawdzić, czy dla certyfikatów utworzono skróty tematu jako dowiązanie symboliczne:

    - Polecenia:

        **# ls -l | grep Baltimore**

    - Dane wyjściowe:

        `lrwxrwxrwx 1 root root   29 Jan  8 09:48 3ad48a91.0 -> Baltimore_CyberTrust_Root.pem`

        `-rw-r--r-- 1 root root 1303 Jun  5  2014 Baltimore_CyberTrust_Root.pem`

    - Polecenia:

        **# ls -l | grep VeriSign_Class_3_Public_Primary_Certification_Authority_G5**

    - Dane wyjściowe:

        `-rw-r--r-- 1 root root 1774 Jun  5  2014 VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem`

        `lrwxrwxrwx 1 root root   62 Jan  8 09:48 facacbc6.0 -> VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem`

    - Polecenia:

        **# ls -l | grep DigiCert_Global_Root**

    - Dane wyjściowe:

        `lrwxrwxrwx 1 root root   27 Jan  8 09:48 399e7759.0 -> DigiCert_Global_Root_CA.pem`

        `-rw-r--r-- 1 root root 1380 Jun  5  2014 DigiCert_Global_Root_CA.pem`

1. Utwórz kopię pliku VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem z nazwami plików b204d74a.0:

    **# cp VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem b204d74a.0**

1. Utwórz kopię pliku Baltimore_CyberTrust_Root.pem z nazwami plików 653b494a.0:

    **# cp Baltimore_CyberTrust_Root.pem 653b494a.0**

1. Utwórz kopię pliku DigiCert_Global_Root_CA.pem z nazwami plików 3513523f.0:

    **# cp DigiCert_Global_Root_CA.pem 3513523f.0**

1. Sprawdź, czy pliki są obecne:

    - Polecenia:

        **# ls -l 653b494a.0 b204d74a.0 3513523f.0**

    - Dane wyjściowe

        `-rw-r--r-- 1 root root 1774 Jan  8 09:52 3513523f.0`

        `-rw-r--r-- 1 root root 1303 Jan  8 09:52 653b494a.0`

        `-rw-r--r-- 1 root root 1774 Jan  8 09:52 b204d74a.0`

## <a name="outbound-connectivity-for-site-recovery-urls-or-ip-ranges-error-code-151037-or-151072"></a>Łączność wychodząca dla adresów URL lub zakresów adresów IP odzyskiwania witryny (kod błędu 151037 lub 151072)

Aby replikacja usługi Site Recovery działała, wymagana jest łączność wychodząca z określonymi adresami URL z maszyny Wirtualnej. Jeśli maszyna wirtualna znajduje się za zaporą lub używa reguł sieciowej grupy zabezpieczeń (NSG) do kontrolowania łączności wychodzącej, może napotkać jeden z tych problemów. Należy pamiętać, że podczas gdy nadal obsługujemy dostęp wychodzący za pośrednictwem adresów URL, korzystanie z listy dozwolonych zakresów adresów IP nie jest już obsługiwane.

### <a name="issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195-br"></a><a name="issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195-br"></a>Problem 1: nie można zarejestrować maszyny wirtualnej platformy Azure w usłudze Site Recovery (151195) </br>
- **Możliwa przyczyna** </br>
  - Nie można ustanowić połączenia z punktami końcowymi usługi Site Recovery z powodu błędu rozpoznawania DNS.
  - Ta sytuacja występuje częściej podczas ponownego włączania ochrony, gdy maszyna wirtualna została przełączona w tryb failover, ale serwer DNS jest nieosiągalny z regionu odzyskiwania po awarii.

- **Rozdzielczość**
   - Jeśli używasz niestandardowego systemu DNS, upewnij się, że serwer DNS jest dostępny z regionu odzyskiwania po awarii. Aby sprawdzić, czy masz niestandardowy system DNS, przejdź do sieci odzyskiwania po awarii> maszyny Wirtualnej> serwerów DNS. Spróbuj uzyskać dostęp do serwera DNS z maszyny wirtualnej. Jeśli nie jest dostępny, udostępnij ją przez niepowodzenie serwera DNS lub tworząc linię lokacji między siecią odzyskiwania po awarii a systemem DNS.

    ![com-błąd](./media/azure-to-azure-troubleshoot-errors/custom_dns.png)


### <a name="issue-2-site-recovery-configuration-failed-151196"></a>Problem 2: Konfiguracja odzyskiwania lokacji nie powiodła się (151196)
- **Możliwa przyczyna** </br>
  - Nie można ustanowić połączenia z punktami końcowymi uwierzytelniania usługi Office 365 i tożsamości IP4.

- **Rozdzielczość**
  - Usługa Azure Site Recovery wymagała dostępu do zakresów usług Office 365 do uwierzytelniania.
    Jeśli używasz reguł/serwera proxy usługi Azure Network Security Group (NSG) do kontrolowania wychodzącej łączności sieciowej na maszynie Wirtualnej, upewnij się, że używasz [reguły nsg opartej na usłudze Azure Active Directory (AAD)](../virtual-network/security-overview.md#service-tags) zezwalając na dostęp do usługi AAD. Nie obsługujemy już reguł sieciowej sieciowej o numerze IP opartych na adresach IP.


### <a name="issue-3-site-recovery-configuration-failed-151197"></a>Problem 3: Konfiguracja odzyskiwania lokacji nie powiodła się (151197)
- **Możliwa przyczyna** </br>
  - Nie można ustanowić połączenia z punktami końcowymi usługi Azure Site Recovery.

- **Rozdzielczość**
  - Jeśli używasz reguł usługi Azure Network Security Group (NSG)/serwera proxy zapory do kontrolowania wychodzącej łączności sieciowej na maszynie Wirtualnej, upewnij się, że używasz tagów usługi. Nie obsługujemy już przy użyciu listy dozwolonych adresów IP za pośrednictwem witryn NSG dla usługi Azure Site Recovery (ASR).


### <a name="issue-4-a2a-replication-failed-when-the-network-traffic-goes-through-on-premise-proxy-server-151072"></a>Problem 4: Replikacja A2A nie powiodła się, gdy ruch sieciowy przechodzi przez lokalny serwer proxy (151072)
 - **Możliwa przyczyna** </br>
   - Niestandardowe ustawienia serwera proxy są nieprawidłowe, a agent ASR Mobility Service nie wykrył automatycznie ustawień serwera proxy z programu IE


 - **Rozdzielczość**
   1.    Agent usługi mobilności wykrywa ustawienia serwera proxy z IE w systemie Windows i /etc/environment w systemie Linux.
   2.  Jeśli wolisz ustawić serwer proxy tylko dla asr mobility service, a następnie można podać szczegóły serwera proxy w ProxyInfo.conf znajduje się pod adresem:</br>
       - ``/usr/local/InMage/config/``na ***Linuksie***
       - ``C:\ProgramData\Microsoft Azure Site Recovery\Config``w ***systemie Windows***
   3.    Plik ProxyInfo.conf powinien zawierać ustawienia serwera proxy w następującym formacie pliku INI.</br>
                   *[proxy]*</br>
                   *Adres=http://1.2.3.4*</br>
                   *Port=567*</br>
   4. Agent ASR Mobility Service obsługuje tylko ***nieu uwierzytelnione serwery proxy.***


### <a name="fix-the-problem"></a>Rozwiązywanie problemu

Aby dodać [wymagane adresy URL](azure-to-azure-about-networking.md#outbound-connectivity-for-urls) do listy dozwolonych, wykonaj czynności opisane w [dokumencie zawierającym wskazówki dotyczące sieci](site-recovery-azure-to-azure-networking-guidance.md).


### <a name="more-information"></a>Więcej informacji

Aby określić [wymagane adresy URL](azure-to-azure-about-networking.md#outbound-connectivity-for-urls) lub [wymagane zakresy adresów IP,](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags)postępuj zgodnie ze wskazówkami dotyczącymi sieci na [platformie Azure do replikacji platformy Azure](site-recovery-azure-to-azure-networking-guidance.md).

## <a name="disk-not-found-in-the-machine-error-code-150039"></a>Nie znaleziono dysku w urządzeniu (kod błędu 150039)

Należy zainicjować nowy dysk dołączony do maszyny Wirtualnej. Jeśli dysk nie zostanie znaleziony, zostanie wyświetlony następujący komunikat:

> "Dysk danych platformy Azure *DiskName* *DiskURI* o numerze jednostki logicznej *LUN LUNValue* nie został zamapowany na odpowiedni dysk zgłaszany z poziomu maszyny Wirtualnej, która ma tę samą wartość jednostki LUN. *LUN*

### <a name="possible-causes"></a>Możliwe przyczyny

- Nowy dysk danych został dołączony do maszyny Wirtualnej, ale nie został zainicjowany.
- Dysk danych wewnątrz maszyny Wirtualnej nie poprawnie zgłasza wartość numeru jednostki logicznej (LUN), przy której dysk został dołączony do maszyny Wirtualnej.

### <a name="fix-the-problem"></a>Rozwiązywanie problemu

Upewnij się, że dyski danych są inicjowane, a następnie ponów próbę wykonania operacji.

- **Windows**: [Dołączanie i inicjowanie nowego dysku](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal).

- **Linux**: [Inicjowanie nowego dysku danych w Systemie Linux](https://docs.microsoft.com/azure/virtual-machines/linux/add-disk).

Jeśli problem będzie się powtarzał, skontaktuj się z pomocą techniczną.

## <a name="one-or-more-disks-are-available-for-protection-error-code-153039"></a>Jeden lub więcej dysków jest dostępnych do ochrony (kod błędu 153039)

### <a name="possible-causes"></a>Możliwe przyczyny

- Jeden lub więcej dysków zostały niedawno dodane do maszyny wirtualnej po ochronie.
- Jeden lub więcej dysków zostało zainicjowanych po ochronie maszyny wirtualnej.

### <a name="fix-the-problem"></a>Rozwiązywanie problemu

Aby ponownie wyprogramować stan replikacji maszyny Wirtualnej, można wybrać opcję ochrony dysków lub odrzucenia ostrzeżenia.

#### <a name="to-protect-the-disks"></a>Aby chronić dyski

1. Przejdź do pozycji **Replikowane elementy** > *nazwy* > maszyny**Wirtualnej Dyski**.
1. Wybierz dysk niechroniony, a następnie wybierz pozycję **Włącz replikację:**

    ![Włączanie replikacji na dyskach maszyn wirtualnych](./media/azure-to-azure-troubleshoot-errors/add-disk.png)

#### <a name="to-dismiss-the-warning"></a>Aby odrzucić ostrzeżenie

1. Przejdź do*nazwy maszyny wirtualnej* **z replikowanymi elementami** > .
1. Zaznacz ostrzeżenie w sekcji **Przegląd,** a następnie wybierz przycisk **OK**.

    ![Odrzucanie ostrzeżenia o nowym dysku](./media/azure-to-azure-troubleshoot-errors/dismiss-warning.png)

## <a name="remove-the-virtual-machine-from-the-vault-completed-with-information-error-code-150225"></a>Usuwanie maszyny wirtualnej z przechowalni zakończonej informacjami (kod błędu 150225)

Gdy chroni maszynę wirtualną, usługa Site Recovery tworzy niektóre łącza na źródłowej maszynie wirtualnej. Po usunięciu ochrony lub wyłączeniu replikacji usługa Site Recovery usuwa te łącza jako część zadania oczyszczania. Jeśli maszyna wirtualna ma blokadę zasobów, zadanie oczyszczania zostanie zakończone z informacjami. Informacje mówią, że maszyna wirtualna została usunięta z magazynu usług odzyskiwania, ale niektóre nieaktualne łącza nie można wyczyścić na komputerze źródłowym.

Można zignorować to ostrzeżenie, jeśli nigdy nie zamierzasz ponownie chronić tej maszyny wirtualnej. Ale jeśli musisz chronić tę maszynę wirtualną później, wykonaj kroki w obszarze "Rozwiąż problem", aby wyczyścić łącza.

> [!WARNING]
> Jeśli nie wykonasz oczyszczania:
>
> - Po włączeniu replikacji za pomocą magazynu usług odzyskiwania, maszyna wirtualna nie zostanie wyświetlona.
> - Jeśli spróbujesz chronić maszynę wirtualną przy użyciu**odzyskiwania po awarii****ustawień** >  **maszyny wirtualnej,** > operacja zakończy się niepowodzeniem z komunikatem "Nie można włączyć replikacji z powodu istniejących starych łączy zasobów na maszynie wirtualnej."

### <a name="fix-the-problem"></a>Rozwiązywanie problemu

> [!NOTE]
> Usługa Site Recovery nie usuwa źródłowej maszyny wirtualnej ani nie wpływa na nią w żaden sposób podczas wykonywania tych kroków.

1. Usuń blokadę z grupy zasobów maszyny Wirtualnej lub Maszyny Wirtualnej. Na przykład na poniższej ilustracji blokada zasobów na maszynie wirtualnej o nazwie "MoveDemo" musi zostać usunięta:

    ![Usuwanie blokady z maszyny Wirtualnej](./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png)

1. Pobierz skrypt, aby [usunąć przestarzałą konfigurację odzyskiwania witryny](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1).
1. Uruchom skrypt, który jest nazywany Cleanup-starych-asr-config-Azure-VM.ps1. Podaj identyfikator subskrypcji, grupę zasobów maszyny Wirtualnej i nazwę maszyny wirtualnej jako parametry.
1. Jeśli zostaniesz poproszony o poświadczenia platformy Azure, podaj je. Następnie sprawdź, czy skrypt działa bez żadnych błędów.

## <a name="replication-cant-be-enabled-because-of-stale-resource-links-on-the-vm-error-code-150226"></a>Nie można włączyć replikacji z powodu starych łączy zasobów na maszynie wirtualnej (kod błędu 150226)

### <a name="possible-cause"></a>Możliwa przyczyna

Maszyna wirtualna ma przestarzałą konfigurację z poprzedniej ochrony odzyskiwania witryny.

Nieaktualna konfiguracja może wystąpić na maszynie Wirtualnej platformy Azure, jeśli włączono replikację maszyny Wirtualnej platformy Azure przy użyciu usługi Site Recovery, a następnie:

- Replikacja została wyłączona, ale źródłowa maszyna wirtualna miała blokadę zasobów.
- Magazyn odzyskiwania witryny został usunięty bez jawnego wyłączania replikacji na maszynie wirtualnej.
- Usunięto grupę zasobów zawierającą magazyn odzyskiwania lokacji bez jawnego wyłączania replikacji na maszynie wirtualnej.

### <a name="fix-the-problem"></a>Rozwiązywanie problemu

> [!NOTE]
> Usługa Site Recovery nie usuwa źródłowej maszyny wirtualnej ani nie wpływa na nią w żaden sposób podczas wykonywania tych kroków.

1. Usuń blokadę z grupy zasobów maszyny Wirtualnej lub Maszyny Wirtualnej. Na przykład na poniższej ilustracji blokada zasobów na maszynie wirtualnej o nazwie "MoveDemo" musi zostać usunięta:

    ![Usuwanie blokady z maszyny Wirtualnej](./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png)

1. Pobierz skrypt, aby [usunąć przestarzałą konfigurację odzyskiwania witryny](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1).
1. Uruchom skrypt, który jest nazywany Cleanup-starych-asr-config-Azure-VM.ps1. Podaj identyfikator subskrypcji, grupę zasobów maszyny Wirtualnej i nazwę maszyny wirtualnej jako parametry.
1. Jeśli zostaniesz poproszony o poświadczenia platformy Azure, podaj je. Następnie sprawdź, czy skrypt działa bez żadnych błędów.

## <a name="unable-to-see-the-azure-vm-or-resource-group-for-the-selection-in-the-enable-replication-job"></a>Nie można wyświetlić maszyny Wirtualnej platformy Azure lub grupy zasobów dla zaznaczenia w zadaniu "Włącz replikację"

### <a name="cause-1-the-resource-group-and-source-virtual-machine-are-in-different-locations"></a>Przyczyna 1: Grupa zasobów i źródło maszyny wirtualnej znajdują się w różnych lokalizacjach

Usługa Site Recovery wymaga obecnie, aby grupa zasobów regionu źródłowego i maszyny wirtualne znajdować się w tej samej lokalizacji. Jeśli tak nie jest, nie będzie można znaleźć maszyny wirtualnej lub grupy zasobów podczas próby zastosowania ochrony.

Aby obejść ten problem, można włączyć replikację z maszyny Wirtualnej zamiast magazynu usług odzyskiwania. Przejdź do odzyskiwania po awarii**właściwości** >  **maszyny** > **wirtualnej** źródła i włącz replikację.

### <a name="cause-2-the-resource-group-is-not-part-of-the-selected-subscription"></a>Przyczyna 2: Grupa zasobów nie jest częścią wybranej subskrypcji

Nie można znaleźć grupy zasobów w momencie ochrony, jeśli grupa zasobów nie jest częścią wybranej subskrypcji. Upewnij się, że grupa zasobów należy do używanej subskrypcji.

### <a name="cause-3-stale-configuration"></a>Przyczyna 3: Nieaktualna konfiguracja

Maszyna wirtualna, którą chcesz włączyć dla replikacji, może nie być widoczna, jeśli na maszynie wirtualnej platformy Azure pozostała nieaktualna konfiguracja usługi Site Recovery. Ten warunek może wystąpić, jeśli włączono replikację maszyny Wirtualnej platformy Azure przy użyciu usługi Site Recovery, a następnie:

- Magazyn odzyskiwania witryny został usunięty bez jawnego wyłączania replikacji na maszynie wirtualnej.
- Usunięto grupę zasobów zawierającą magazyn odzyskiwania lokacji bez jawnego wyłączania replikacji na maszynie wirtualnej.
- Replikacja została wyłączona, ale źródłowa maszyna wirtualna miała blokadę zasobów.

### <a name="fix-the-problem"></a>Rozwiązywanie problemu

> [!NOTE]
> Przed użyciem skryptu wymienionego w tej sekcji należy zaktualizować moduł "AzureRM.Resources".  Usługa Site Recovery nie usuwa źródłowej maszyny wirtualnej ani nie wpływa na nią w żaden sposób podczas wykonywania tych kroków.

1. Usuń blokadę, jeśli istnieje, z grupy zasobów maszyny Wirtualnej lub Maszyny Wirtualnej. Na przykład na poniższej ilustracji blokada zasobów na maszynie wirtualnej o nazwie "MoveDemo" musi zostać usunięta:

    ![Usuwanie blokady z maszyny Wirtualnej](./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png)

1. Pobierz skrypt, aby [usunąć przestarzałą konfigurację odzyskiwania witryny](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1).
1. Uruchom skrypt, który jest nazywany Cleanup-starych-asr-config-Azure-VM.ps1. Podaj identyfikator subskrypcji, grupę zasobów maszyny Wirtualnej i nazwę maszyny wirtualnej jako parametry.
1. Jeśli zostaniesz poproszony o poświadczenia platformy Azure, podaj je. Następnie sprawdź, czy skrypt działa bez żadnych błędów.

## <a name="unable-to-select-a-virtual-machine-for-protection"></a>Nie można wybrać maszyny wirtualnej do ochrony

### <a name="cause-1-the-virtual-machine-has-an-extension-installed-in-a-failed-or-unresponsive-state"></a>Przyczyna 1: Maszyna wirtualna ma zainstalowane rozszerzenie w stanie nierozpoznawczym lub nieodpowiadanym

Przejdź do rozszerzenia**ustawień** >  **maszyn** > **wirtualnych** i sprawdź, czy nie ma żadnych rozszerzeń w stanie awarii. Odinstaluj wszystkie nieudane rozszerzenie, a następnie spróbuj ponownie chronić maszynę wirtualną.

### <a name="cause-2-the-vms-provisioning-state-is-not-valid"></a>Przyczyna 2: Stan inicjowania obsługi administracyjnej maszyny Wirtualnej jest nieprawidłowy

Zobacz kroki rozwiązywania problemów w [stanie inicjowania obsługi administracyjnej maszyny Wirtualnej jest nieprawidłowy](#the-vms-provisioning-state-is-not-valid-error-code-150019), w dalszej części tego artykułu.

## <a name="the-vms-provisioning-state-is-not-valid-error-code-150019"></a>Stan inicjowania obsługi administracyjnej maszyny Wirtualnej jest nieprawidłowy (kod błędu 150019)

Aby włączyć replikację na maszynie Wirtualnej, jej stan inicjowania obsługi administracyjnej musi **zakończyć się pomyślnie**. Wykonaj następujące kroki, aby sprawdzić stan inicjowania obsługi administracyjnej:

1. W witrynie Azure portal wybierz **Eksploratora zasobów** ze **wszystkich usług**.
1. Rozwiń listę **Subskrypcje** i wybierz subskrypcję.
1. Rozwiń listę **ResourceGroups** i wybierz grupę zasobów maszyny Wirtualnej.
1. Rozwiń listę **Zasoby** i wybierz maszynę wirtualną.
1. Sprawdź pole **inicjowanie obsługi administracyjnejState** w widoku wystąpienia po prawej stronie.

### <a name="fix-the-problem"></a>Rozwiązywanie problemu

- Jeśli **inicjowanie obsługi administracyjnejState** jest **niepowodzeniem,** skontaktuj się z pomocą techniczną ze szczegółami, aby rozwiązać problem.
- Jeśli **inicjowanie obsługi administracyjnejState** jest **aktualizacja,** inne rozszerzenie może być wdrażane. Sprawdź, czy istnieją bieżące operacje na maszynie Wirtualnej, poczekaj na zakończenie, a następnie ponów próbę zadania "Włącz replikację" odzyskiwania lokacji nie powiodło się.

## <a name="unable-to-select-target-vm-network-selection-tab-is-unavailable"></a>Nie można wybrać docelowej maszyny Wirtualnej (karta wyboru sieci jest niedostępna)

### <a name="cause-1-your-vm-is-attached-to-a-network-thats-already-mapped-to-a-target-network"></a>Przyczyna 1: Maszyna wirtualna jest dołączona do sieci, która jest już mapowana do sieci docelowej

Jeśli źródłowa maszyna wirtualna jest częścią sieci wirtualnej, a inna maszyna wirtualna z tej samej sieci wirtualnej jest już mapowana za pomocą sieci w docelowej grupie zasobów, pole listy rozwijanej wyboru sieci jest domyślnie niedostępne (jest wyszarzone).

![Lista wyboru sieci niedostępna](./media/site-recovery-azure-to-azure-troubleshoot/unabletoselectnw.png)

### <a name="cause-2-you-previously-protected-the-vm-by-using-site-recovery-and-then-you-disabled-the-replication"></a>Przyczyna 2: Maszyna wirtualna była wcześniej chroniona przy użyciu usługi Site Recovery, a następnie została wyłączona replikacja

Wyłączenie replikacji maszyny Wirtualnej nie powoduje usunięcia mapowania sieci. Mapowanie musi zostać usunięte z magazynu usług odzyskiwania, w którym maszyna wirtualna była chroniona. Przejdź do *mapowania* > **sieci infrastruktury** > odzyskiwania magazynu usług odzyskiwania usług odzyskiwania **.**

![Usuwanie mapowania sieci](./media/site-recovery-azure-to-azure-troubleshoot/delete_nw_mapping.png)

Sieć docelowa skonfigurowana podczas konfiguracji odzyskiwania po awarii może zostać zmieniona po wstępnej instalacji, po ochronie maszyny Wirtualnej:

![Modyfikowanie mapowania sieci](./media/site-recovery-azure-to-azure-troubleshoot/modify_nw_mapping.png)

Należy zauważyć, że zmiana mapowania sieci wpływa na wszystkie chronione maszyny wirtualne, które używają tego samego mapowania sieci.

## <a name="com-or-volume-shadow-copy-service-error-error-code-151025"></a>Błąd usługi COM+ lub Volume Shadow Copy service (kod błędu 151025)

W przypadku wystąpienia tego błędu zostanie wyświetlony następujący komunikat:

> "Nie można zainstalować rozszerzenia odzyskiwania witryny"

### <a name="possible-causes"></a>Możliwe przyczyny

- Usługa aplikacji systemowych COM+ jest wyłączona.
- Usługa kopiowania woluminów w tle jest wyłączona.

### <a name="fix-the-problem"></a>Rozwiązywanie problemu

Ustaw usługi aplikacji systemowych i kopiowania woluminów COM+ na tryb automatycznego lub ręcznego uruchamiania.

1. Otwórz konsolę Usługi w systemie Windows.
1. Upewnij się, że usługi aplikacji systemowej COM+ i kopiowania woluminów w tle nie są ustawione **na Wyłączone** jako ich **typ uruchamiania**.

    ![Sprawdź typ uruchamiania usług aplikacji systemowych COM+ i kopiowania woluminów w tle](./media/azure-to-azure-troubleshoot-errors/com-error.png)

## <a name="unsupported-managed-disk-size-error-code-150172"></a>Nieobsługiwał rozmiar dysku zarządzanego (kod błędu 150172)

W przypadku wystąpienia tego błędu zostanie wyświetlony następujący komunikat:

> "Nie można włączyć ochrony dla maszyny wirtualnej, ponieważ ma *diskname* o rozmiarze *DiskSize*)* który jest mniejszy niż minimalny obsługiwany rozmiar 1024 MB."

### <a name="possible-cause"></a>Możliwa przyczyna

Dysk jest mniejszy niż obsługiwany rozmiar 1024 MB.

### <a name="fix-the-problem"></a>Rozwiązywanie problemu

Upewnij się, że rozmiar dysku mieści się w obsługiwanym zakresie rozmiaru, a następnie ponów próbę wykonania operacji.

## <a name="protection-was-not-enabled-because-the-grub-configuration-includes-the-device-name-instead-of-the-uuid-error-code-151126"></a><a name="enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-error-code-151126"></a>Ochrona nie została włączona, ponieważ konfiguracja GRUB zawiera nazwę urządzenia zamiast identyfikatora UUID (kod błędu 151126)

### <a name="possible-cause"></a>Możliwa przyczyna

Pliki konfiguracyjne Linux GRUB (/boot/grub/menu.lst", /boot/grub/grub.cfg, /boot/grub2/grub.cfg lub /etc/default/grub) mogą określać rzeczywiste nazwy urządzeń zamiast wartości UUID dla parametrów *root* i *resume.* Odzyskiwanie witryny wymaga identyfikatorów UUID, ponieważ nazwy urządzeń mogą ulec zmianie. Po ponownym uruchomieniu maszyny Wirtualnej może nie wymyślić tej samej nazwy w pracy awaryjnej, co powoduje problemy.

Poniższe przykłady to wiersze z plików GRUB, w których zamiast wymaganych identyfikatorów UUID są wyświetlane nazwy urządzeń (wyświetlane pogrubioną czcionką):

- Plik /boot/grub2/grub.cfg

  > linux /boot/vmlinuz-3.12.49-11-default **root=/dev/sda2** ${extra_cmdline} **resume=/dev/sda1** splash=ciche ciche showopts

- Plik: /boot/grub/menu.lst

  > jądro /boot/vmlinuz-3.0.101-63-default **root=/dev/sda2** **resume=/dev/sda1** splash=silent crashkernel=256M-:128M showopts vga=0x314


### <a name="fix-the-problem"></a>Rozwiązywanie problemu

Zastąp każdą nazwę urządzenia odpowiednim identyfikatorem UUID:

1. Znajdź identyfikator UUID urządzenia, wykonując ***nazwę urządzenia*** **blkid** polecenia . Przykład:

    ```
    blkid /dev/sda1
    /dev/sda1: UUID="6f614b44-433b-431b-9ca1-4dd2f6f74f6b" TYPE="swap"
    blkid /dev/sda2
    /dev/sda2: UUID="62927e85-f7ba-40bc-9993-cc1feeb191e4" TYPE="ext3"
   ```

1. Zastąp nazwę urządzenia jego identyfikatorem UUID w formatach **root=UUID**=*UUID* i **resume=UUID**=*UUID*. Na przykład po wymianie wiersz z /boot/grub/menu.lst (omówiony wcześniej) będzie wyglądać następująco:

    > jądro /boot/vmlinuz-3.0.101-63-default **root=UUID=62927e85-f7ba-40bc-9993-cc1feeb191e4** **resume=UUID=6f614b44-433b-431b-9ca1-4dd2f6f74f6b** splash=cichy crashkernel=256M-:128M showopts vga=0x314

1. Ponów próbę ochrony.

## <a name="enable-protection-failed-because-the-device-mentioned-in-the-grub-configuration-doesnt-exist-error-code-151124"></a>Włączenie ochrony nie powiodło się, ponieważ urządzenie wymienione w konfiguracji GRUB nie istnieje (kod błędu 151124)

### <a name="possible-cause"></a>Możliwa przyczyna

Pliki konfiguracyjne GRUB (/boot/grub/menu.lst, /boot/grub/grub.cfg, /boot/grub2/grub.cfg lub /etc/default/grub) mogą zawierać parametry *rd.lvm.lv* lub *rd_LVM_LV*. Parametry te identyfikują urządzenia Menedżera woluminów logicznych (LVM), które mają zostać wykryte podczas rozruchu. Jeśli te urządzenia LVM nie istnieją, chroniony system sam nie uruchomi się i utknie w procesie rozruchu. Ten sam problem będzie również widoczny w pracy awaryjnej maszyny Wirtualnej. Oto kilka przykładów:

- Plik: /boot/grub2/grub.cfg na RHEL7:

    > linux16 /vmlinuz-3.10.0-957.el7.x86_64 root=/dev/mapper/rhel_mup--rhel7u6-root ro crashkernel=128M\@64M **rd.lvm.lv=rootvg/root rd.lvm.lv=rootvg/swap** rhgb quiet LANG=en_US. UTF-8

- Plik: /etc/default/grub na RHEL7:

    > GRUB_CMDLINE_LINUX="crashkernel=auto **rd.lvm.lv=rootvg/root rd.lvm.lv=rootvg/swap** rhgb quiet"

- Plik: /boot/grub/menu.lst na RHEL6:

    > jądro /vmlinuz-2.6.32-754.el6.x86_64 ro root=UUID=36dd8b45-e90d-40d6-81ac-ad0d0725d69e rd_NO_LUKS LANG=en_US. UTF-8 rd_NO_MD SYSFONT=latarcyrheb-sun16 crashkernel=auto **rd_LVM_LV=rootvg/lv_root** KEYBOARDTYPE=pc KEYTABLE=us **rd_LVM_LV=rootvg/lv_swap** rd_NO_DM rhgb quiet

W każdym przykładzie porcja pogrubioną pokazuje, że GRUB musi wykryć dwa urządzenia LVM o nazwach "root" i "swap" z grupy woluminów "rootvg".

### <a name="fix-the-problem"></a>Rozwiązywanie problemu

Jeśli urządzenie LVM nie istnieje, utwórz je lub usuń odpowiednie parametry z plików konfiguracyjnych GRUB. Następnie spróbuj ponownie, aby włączyć ochronę.

## <a name="a-site-recovery-mobility-service-update-finished-with-warnings-error-code-151083"></a>Aktualizacja usługi mobilności odzyskiwania witryny zakończona ostrzeżeniami (kod błędu 151083)

Usługa mobilności odzyskiwania witryny ma wiele składników, z których jeden jest nazywany sterownikiem filtru. Sterownik filtru jest ładowany do pamięci systemowej tylko podczas ponownego uruchamiania systemu. Za każdym razem, gdy aktualizacja usługi mobilności zawiera zmiany sterownika filtru, komputer jest aktualizowany, ale nadal jest widoczne ostrzeżenie, że niektóre poprawki wymagają ponownego uruchomienia. Ostrzeżenie pojawia się, ponieważ poprawki sterownika filtru mogą zostać zastosowane tylko po załadowaniu nowego sterownika filtru, co ma miejsce tylko podczas ponownego uruchamiania.

> [!NOTE]
> To tylko ostrzeżenie. Istniejąca replikacja nadal działa nawet po aktualizacji nowego agenta. Możesz ponownie uruchomić komputer, gdy chcesz korzystać z zalet nowego sterownika filtru, ale stary sterownik filtru działa, jeśli nie zostanie uruchomiony ponownie.
>
> Oprócz sterownika filtru korzyści wynikające z innych ulepszeń i poprawek w aktualizacji usługi mobilności są skuteczne bez konieczności ponownego uruchamiania.

## <a name="protection-couldnt-be-enabled-because-the-replica-managed-disk-already-exists-without-expected-tags-in-the-target-resource-group-error-code-150161"></a>Nie można włączyć ochrony, ponieważ dysk zarządzany repliki już istnieje, bez oczekiwanych tagów, w docelowej grupie zasobów (kod błędu 150161)

### <a name="possible-cause"></a>Możliwa przyczyna

Ten problem może wystąpić, jeśli maszyna wirtualna była wcześniej chroniona, a gdy replikacja została wyłączona, dysk repliki nie został wyczyszczony.

### <a name="fix-the-problem"></a>Rozwiązywanie problemu

Usuń dysk repliki zidentyfikowany w komunikacie o błędzie i ponów próbę wykonania nieudanego zadania ochrony.

## <a name="next-steps"></a>Następne kroki

[Replikowanie maszyn wirtualnych platformy Azure](site-recovery-replicate-azure-to-azure.md)
