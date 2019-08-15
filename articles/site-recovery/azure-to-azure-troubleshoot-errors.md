---
title: Usługa Azure Site Recovery Rozwiązywanie problemów z błędów i problemów z replikacją Azure – Azure | Dokumentacja firmy Microsoft
description: Rozwiązywanie problemów z błędów i problemów podczas replikowania maszyn wirtualnych platformy Azure w celu odzyskiwania po awarii
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/08/2019
ms.author: asgang
ms.openlocfilehash: 4d8ba44cdd5161a1a5ff108837cb57af4cd98835
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/15/2019
ms.locfileid: "69034797"
---
# <a name="troubleshoot-azure-to-azure-vm-replication-issues"></a>Rozwiązywanie problemów z replikacją maszyny Wirtualnej platformy Azure do platformy Azure

W tym artykule opisano, jakie są najczęstsze problemy w usłudze Azure Site Recovery podczas replikacji i odzyskiwania maszyn wirtualnych platformy Azure z jednego regionu do innego regionu i wyjaśniono, jak rozwiązywać problemy z nimi. Aby uzyskać więcej informacji o obsługiwanych konfiguracjach, zobacz [macierz obsługi do replikowania maszyn wirtualnych platformy Azure](site-recovery-support-matrix-azure-to-azure.md).

## <a name="list-of-errors"></a>Lista błędów
- **[Problemy z przydziałami zasobów platformy Azure (kod błędu 150097)](#azure-resource-quota-issues-error-code-150097)**
- **[Zaufane certyfikaty główne (kod błędu 151066)](#trusted-root-certificates-error-code-151066)**
- **[Łączność wychodząca dla Site Recovery (kod błędu 151195)](#issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195-br)**

## <a name="azure-resource-quota-issues-error-code-150097"></a>Problemy z przydziałami zasobów platformy Azure (kod błędu 150097)
Twoja subskrypcja powinna być włączona w celu tworzenia maszyn wirtualnych platformy Azure w regionie docelowym, który ma być używany jako swój region odzyskiwania po awarii. Ponadto subskrypcji powinny mieć wystarczającego limitu przydziału, włączone, aby tworzyć maszyny wirtualne o określonym rozmiarze. Domyślnie usługa Site Recovery wybiera ten sam rozmiar dla docelowej maszyny Wirtualnej, co źródłowa maszyna wirtualna. Jeśli dopasowania rozmiaru nie jest dostępna, najbardziej zbliżony rozmiar jest pobierany automatycznie. W przypadku nie pasującego rozmiar, który obsługuje konfiguracji źródłowej maszyny Wirtualnej, zostanie wyświetlony ten komunikat o błędzie:

**Kod błędu:** | **Możliwe przyczyny** | **Zalecenie**
--- | --- | ---
150097<br></br>**Komunikat**: Nie można włączyć replikacji dla maszyny wirtualnej VmName. | — Identyfikator subskrypcji nie może być włączona do tworzenia maszyn wirtualnych w lokalizacji docelowej w regionie.</br></br>— Identyfikator subskrypcji, może nie być włączone lub nie ma wystarczającego limitu przydziału do tworzenia określonych rozmiarów maszyn wirtualnych w lokalizacji docelowej w regionie.</br></br>-Odpowiedni rozmiar docelowej maszyny Wirtualnej pasujący źródłowej maszyny Wirtualnej karty Sieciowej, liczba (2) nie zostanie odnaleziony dla Identyfikatora subskrypcji w lokalizacji docelowej w regionie.| Skontaktuj się z pomocą [Azure pomocy technicznej dotyczącej rozliczeń](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) umożliwiające tworzenia maszyn wirtualnych o wymaganych rozmiarach w lokalizacji docelowej dla Twojej subskrypcji. Po włączeniu ponów próbę wykonania operacji zakończonej niepowodzeniem.

### <a name="fix-the-problem"></a>Rozwiąż problem
Możesz skontaktować się ze [Azure pomocy technicznej dotyczącej rozliczeń](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) włączenie subskrypcji do tworzenia maszyn wirtualnych o wymaganych rozmiarach w lokalizacji docelowej.

Jeśli lokalizacja docelowa ma ograniczenie pojemności, wyłącz replikację i włącz go w innej lokalizacji, w którym Twoja subskrypcja ma wystarczający przydział do tworzenia maszyn wirtualnych o wymaganych rozmiarach.

## <a name="trusted-root-certificates-error-code-151066"></a>Zaufane certyfikaty główne (kod błędu 151066)

Jeśli wszystkie najnowsze certyfikaty zaufany główny urząd certyfikacji nie istnieje na maszynie Wirtualnej, zadanie "Włącz replikację" może zakończyć się niepowodzeniem. Bez certyfikatów uwierzytelniania i autoryzacji, wywołań usługi Site Recovery z maszyny Wirtualnej zakończyć się niepowodzeniem. Zostanie wyświetlony komunikat o błędzie o zadaniu zakończonym niepowodzeniem "Włącz replikację" Site Recovery:

**Kod błędu:** | **Możliwa przyczyna** | **Zalecenia**
--- | --- | ---
151066<br></br>**Komunikat**: Konfiguracja Site Recovery nie powiodła się. | Wymagane zaufane certyfikaty główne używane na potrzeby autoryzacji i uwierzytelniania nie są dostępne na komputerze. | — Dla maszyny Wirtualnej z systemem operacyjnym Windows upewnij się, że zaufane certyfikaty główne na maszynie. Aby uzyskać informacje, zobacz [Konfigurowanie zaufanych certyfikatów głównych i certyfikatów niedozwolonych](https://technet.microsoft.com/library/dn265983.aspx).<br></br>— Dla maszyny Wirtualnej z systemem operacyjnym Linux postępuj zgodnie z wytycznymi dla zaufanych certyfikatów głównych opublikowanymi przez dystrybutora wersji systemu operacyjnego Linux.

### <a name="fix-the-problem"></a>Rozwiąż problem
**Windows**

Zainstalowanie wszystkich najnowszych aktualizacji Windows na maszynie Wirtualnej, tak aby wszystkie zaufane certyfikaty główne są obecne na maszynie. Jeśli pracujesz w środowisku bez połączenia, postępuj zgodnie z standardowego procesu aktualizacji Windows w Twojej organizacji do uzyskania certyfikatów. Jeśli na maszynie Wirtualnej nie są wymagane certyfikaty, wywołań do usługi Site Recovery zakończyć się niepowodzeniem ze względu na bezpieczeństwo.

Postępuj zgodnie z typowe Windows update management lub certyfikat proces zarządzania aktualizacjami w organizacji, aby pobrać wszystkie najnowsze certyfikaty główne i listę odwołania certyfikatów zaktualizowane na maszynach wirtualnych.

Aby sprawdzić, czy problem został rozwiązany, przejdź do login.microsoftonline.com za pomocą przeglądarki na maszynie wirtualnej.

**Linux**

Postępuj zgodnie ze wskazówkami dystrybutora systemu Linux, aby uzyskać najnowsze zaufane certyfikaty główne i najnowsze listy odwołania certyfikatów na maszynie Wirtualnej.

Ponieważ SuSE Linux używa łączy symbolicznych, aby utrzymywać listę certyfikatów, wykonaj następujące kroki:

1.  Zaloguj się jako użytkownik root.

2.  Uruchom następujące polecenie, aby zmienić katalog.

      ``# cd /etc/ssl/certs``

1. Sprawdź, czy certyfikat urzędu certyfikacji firmy Symantec główny jest obecny.

      ``# ls VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem``

2. Jeśli certyfikat urzędu certyfikacji firmy Symantec główny nie zostanie znaleziony, uruchom następujące polecenie, aby pobrać plik. Sprawdź pod kątem błędów, a następnie postępuj zgodnie z zalecaną akcję dotyczącą awarie sieci.

      ``# wget https://www.symantec.com/content/dam/symantec/docs/other-resources/verisign-class-3-public-primary-certification-authority-g5-en.pem -O VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem``

3. Sprawdź, czy urząd certyfikacji certyfikatu głównego Baltimore jest obecny.

      ``# ls Baltimore_CyberTrust_Root.pem``

4. Jeśli certyfikat głównego urzędu certyfikacji Baltimore nie zostanie znaleziony, Pobierz certyfikat.  

    ``# wget https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem -O Baltimore_CyberTrust_Root.pem``

5. Sprawdź, czy certyfikatu DigiCert_Global_Root_CA jest obecny.

    ``# ls DigiCert_Global_Root_CA.pem``

6. Jeśli DigiCert_Global_Root_CA nie zostanie znaleziony, uruchom następujące polecenia, aby pobrać certyfikat.

    ``# wget http://www.digicert.com/CACerts/DigiCertGlobalRootCA.crt``

    ``# openssl x509 -in DigiCertGlobalRootCA.crt -inform der -outform pem -out DigiCert_Global_Root_CA.pem``

7. Uruchom rehash — skrypt, aby zaktualizować certyfikatu podmiotu skróty dla nowo pobranego certyfikatów.

    ``# c_rehash``

8.  Sprawdź, jeśli temat wyznacza wartość skrótu utworzonemu łączy symbolicznych dla certyfikatów.

    - Polecenie

      ``# ls -l | grep Baltimore``

    - Dane wyjściowe

      ``lrwxrwxrwx 1 root root   29 Jan  8 09:48 3ad48a91.0 -> Baltimore_CyberTrust_Root.pem
      -rw-r--r-- 1 root root 1303 Jun  5  2014 Baltimore_CyberTrust_Root.pem``

    - Polecenie

      ``# ls -l | grep VeriSign_Class_3_Public_Primary_Certification_Authority_G5``

    - Dane wyjściowe

      ``-rw-r--r-- 1 root root 1774 Jun  5  2014 VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem
      lrwxrwxrwx 1 root root   62 Jan  8 09:48 facacbc6.0 -> VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem``

    - Polecenie

      ``# ls -l | grep DigiCert_Global_Root``

    - Dane wyjściowe

      ``lrwxrwxrwx 1 root root   27 Jan  8 09:48 399e7759.0 -> DigiCert_Global_Root_CA.pem
      -rw-r--r-- 1 root root 1380 Jun  5  2014 DigiCert_Global_Root_CA.pem``

9.  Tworzenie kopii pliku VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem za pomocą b204d74a.0 nazwy pliku

    ``# cp VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem b204d74a.0``

10. Tworzenie kopii pliku Baltimore_CyberTrust_Root.pem za pomocą 653b494a.0 nazwy pliku

    ``# cp Baltimore_CyberTrust_Root.pem 653b494a.0``

13. Tworzenie kopii pliku DigiCert_Global_Root_CA.pem za pomocą 3513523f.0 nazwy pliku

    ``# cp DigiCert_Global_Root_CA.pem 3513523f.0``  


14. Sprawdź, czy pliki są obecne.  

    - Polecenie

      ``# ls -l 653b494a.0 b204d74a.0 3513523f.0``

    - Dane wyjściowe

      ``-rw-r--r-- 1 root root 1774 Jan  8 09:52 3513523f.0
      -rw-r--r-- 1 root root 1303 Jan  8 09:52 653b494a.0
      -rw-r--r-- 1 root root 1774 Jan  8 09:52 b204d74a.0``


## <a name="outbound-connectivity-for-site-recovery-urls-or-ip-ranges-error-code-151037-or-151072"></a>Połączenia ruchu wychodzącego dla zakresów adresów URL Site Recovery lub adres IP (kod błędu: 151037 lub 151072)

W przypadku replikacji usługi Site Recovery do pracy, łączność wychodząca z określonych adresów URL lub IP zakresów jest wymagane z maszyny Wirtualnej. Jeśli maszyna wirtualna znajduje się za zaporą lub używa reguł Sieciowej grupy zabezpieczeń sieci do sterowania ruchem wychodzącym, może być jedną z tych problemów twarzy.

### <a name="issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195-br"></a>Problem 1: Nie można zarejestrować maszyny wirtualnej platformy Azure w Site Recovery (151195) </br>
- **Możliwa przyczyna** </br>
  - Nie można nawiązać połączenia, aby Site Recovery punkty końcowe z powodu błędu rozpoznawania nazw DNS.
  - Jest to częściej występujące podczas ponownej ochrony, gdy zostały przełączone w tryb failover maszyny wirtualnej, ale serwer DNS nie jest dostępny w regionie odzyskiwania po awarii.

- **Rozdzielczość**
   - Jeśli używasz niestandardowego DNS, upewnij się, że serwer DNS jest dostępne w regionie odzyskiwania po awarii. Aby sprawdzić, czy masz niestandardowe DNS, przejdź do maszyny Wirtualnej > sieć odzyskiwania po awarii > serwery DNS. Wypróbuj, uzyskiwanie dostępu do serwera DNS z maszyny wirtualnej. Jeśli nie jest dostępny następnie go udostępnić, albo przejść w tryb failover serwera DNS lub tworząc wiersz lokacji między siecią odzyskiwania po awarii i usługą DNS.

    ![Błąd modelu COM](./media/azure-to-azure-troubleshoot-errors/custom_dns.png)


### <a name="issue-2-site-recovery-configuration-failed-151196"></a>Problem 2: Konfiguracja Site Recovery nie powiodła się (151196)
- **Możliwa przyczyna** </br>
  - Nie można ustanowić połączenia z uwierzytelnianiem usługi Office 365 i punktów końcowych IP4 tożsamości.

- **Rozdzielczość**
  - Usługa Azure Site Recovery zakresy adresów IP usługi Office 365 są wymagane do uwierzytelniania.
    Jeśli używasz proxy reguły i zapory Sieciowej grupy zabezpieczeń sieci platformy Azure do kontrolowania połączenia sieciowego ruchu wychodzącego na maszynie Wirtualnej, upewnij się, że zezwalają na komunikację zakresy adresów IP usługi Office 365. Tworzenie [tag usługi Azure Active Directory (AAD)](../virtual-network/security-overview.md#service-tags) na podstawie reguły sieciowej grupy zabezpieczeń do zezwalania na dostęp do wszystkich adresów IP odpowiadającej usługi AAD
      - Nowe adresy zostaną dodane do usługi Azure Active Directory (AAD) w przyszłości, należy utworzyć nowe reguły sieciowej grupy zabezpieczeń.

> [!NOTE]
> Jeśli maszyny wirtualne znajdują się za standardowym wewnętrznym modułem równoważenia obciążenia, nie będą miały dostępu do adresów IP usługi O365, tj. Domyślnie login.microsoftonline.com. Zmień go na **podstawowy** typ wewnętrznego modułu równoważenia obciążenia lub Utwórz powiązany dostęp określony w [artykule](https://aka.ms/lboutboundrulescli).

### <a name="issue-3-site-recovery-configuration-failed-151197"></a>Problem 3: Konfiguracja Site Recovery nie powiodła się (151197)
- **Możliwa przyczyna** </br>
  - Nie można ustanowić połączenia z punktami końcowymi usługi Azure Site Recovery.

- **Rozdzielczość**
  - Usługa Azure Site Recovery wymagany dostęp do [zakresów adresów IP odzyskiwania lokacji](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-ip-address-ranges) w zależności od regionu. Upewnij się, wymagane zakresy adresów ip są dostępne z maszyny wirtualnej.


### <a name="issue-4-a2a-replication-failed-when-the-network-traffic-goes-through-on-premises-proxy-server-151072"></a>Problem 4: Replikacja — A2A nie powiodła się, gdy ruch sieciowy przechodzi przez lokalny serwer proxy (151072)
- **Możliwa przyczyna** </br>
  - Niestandardowe ustawienia serwera proxy są nieprawidłowe, a Azure Site Recovery Agent usługi mobilności nie wykrył automatycznie ustawień serwera proxy z programu IE


- **Rozdzielczość**
  1. Agent usługi mobilności wykrywa ustawienia serwera proxy programu Internet Explorer na Windows i /etc/environment w systemie Linux.
  2. Jeśli wolisz ustawić serwer proxy tylko dla usługi mobilności Azure Site Recovery, możesz podać szczegóły serwera proxy w ProxyInfo. conf znajdującym się w:</br>
     - ``/usr/local/InMage/config/`` na ***systemu Linux***
     - ``C:\ProgramData\Microsoft Azure Site Recovery\Config`` na ***Windows***
  3. ProxyInfo.conf powinna mieć ustawienia serwera proxy w następującym formacie pliku INI.</br>
                *[proxy]*</br>
                *Adres =http://1.2.3.4*</br>
                *Port = 567*</br>
  4. Agent usługi mobilności Azure Site Recovery obsługuje tylko ***nieuwierzytelnione serwery proxy***.


### <a name="fix-the-problem"></a>Rozwiąż problem
Aby zezwolić na [wymagane adresy URL](azure-to-azure-about-networking.md#outbound-connectivity-for-urls) lub [wymagane zakresy adresów IP](azure-to-azure-about-networking.md#outbound-connectivity-for-ip-address-ranges), wykonaj kroki opisane w [dokumencie wskazówki dotyczące sieci](site-recovery-azure-to-azure-networking-guidance.md).

## <a name="disk-not-found-in-the-machine-error-code-150039"></a>Nie znaleziono maszyny (kod błędu: 150039) dysku

Nowy dysk dołączony do maszyny Wirtualnej musi być zainicjowany.

**Kod błędu:** | **Możliwe przyczyny** | **Zalecenia**
--- | --- | ---
150039<br></br>**Komunikat**: Dysk danych platformy Azure (diskname) (DiskURI) z numerem jednostki logicznej (LUN) (LUNValue) nie został zmapowany na odpowiedni dysk, który jest raportowany z poziomu maszyny wirtualnej o tej samej wartości LUN. | -Nowy dysk danych został dołączony do maszyny Wirtualnej, ale nie został on zainicjowany.</br></br>-Dysk danych wewnątrz maszyny Wirtualnej nie zgłasza poprawnie wartości LUN, w którym dysk został dołączony do maszyny Wirtualnej.| Upewnij się, czy są inicjowane dyski z danymi, a następnie spróbuj ponownie wykonać operację.</br></br>W przypadku systemu Windows: [Dołącz i zainicjuj nowy dysk](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal).</br></br>Dla systemu Linux: [Zainicjuj nowy dysk danych w systemie Linux](https://docs.microsoft.com/azure/virtual-machines/linux/add-disk).

### <a name="fix-the-problem"></a>Rozwiąż problem
Upewnij się, dyski danych zostały zainicjowane, a następnie spróbuj ponownie wykonać operację:

- W przypadku systemu Windows: [Dołącz i zainicjuj nowy dysk](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal).
- Dla systemu Linux: [Dodaj nowy dysk danych w systemie Linux](https://docs.microsoft.com/azure/virtual-machines/linux/add-disk).

Jeśli problem będzie się powtarzać, skontaktuj się z pomocą techniczną.

## <a name="one-or-more-disks-are-available-for-protectionerror-code-153039"></a>Co najmniej jeden dysk jest dostępny do ochrony (kod błędu 153039)
- **Możliwa przyczyna** </br>
  - Jeśli co najmniej jeden dysk został niedawno dodany do maszyny wirtualnej po włączeniu ochrony. 
  - Jeśli co najmniej jeden dysk został zainicjowany później po włączeniu ochrony maszyny wirtualnej.

### <a name="fix-the-problem"></a>Rozwiąż problem
Możesz wybrać opcję ochrony dysków lub zignorować ostrzeżenie, aby ponownie zmienić stan replikacji maszyny wirtualnej.</br>
1. W celu ochrony dysków. Przejdź do pozycji zreplikowane elementy > dysków > maszyn wirtualnych > kliknij dysk niechroniony > Włącz replikację.
 ![add_disks](./media/azure-to-azure-troubleshoot-errors/add-disk.png)
2. Aby odrzucić ostrzeżenie. Przejdź do pozycji zreplikowane elementy > VM > kliknij sekcję Odrzuć alert w obszarze przegląd.
![dismiss_warning](./media/azure-to-azure-troubleshoot-errors/dismiss-warning.png)


## <a name="remove-the-virtual-machine-from-the-vault-completed-with-information--error-code-150225"></a>Usuwanie maszyny wirtualnej z magazynu zostało zakończone z informacjami (kod błędu 150225)
W czasie ochrony maszyny wirtualnej Azure Site Recovery tworzy niektóre linki na źródłowej maszynie wirtualnej. Po usunięciu ochrony lub wyłączeniu replikacji Azure Site Recovery usunąć te linki w ramach zadania oczyszczania. Jeśli maszyna wirtualna ma blokadę zasobu, zadanie zostanie wykonane z informacjami. Informuje o tym, że maszyna wirtualna została usunięta z magazynu usługi Recovery Services, ale niektórych nieodświeżonych linków nie można oczyścić z maszyny źródłowej.

To ostrzeżenie można zignorować, jeśli nie chcesz ponownie chronić tej maszyny wirtualnej w przyszłości. Jeśli jednak chcesz chronić tę maszynę wirtualną później, wyczyść linki, jak wspomniano w poniższych krokach. 

**Jeśli nie wykonasz czyszczenia, wykonaj następujące czynności:**

1.  W czasie włączania replikacji za pomocą magazynu usługi Recovery Services maszyna wirtualna nie zostanie wyświetlona na liście. 
2.  Jeśli spróbujesz chronić MASZYNę wirtualną za pomocą **ustawień > maszyny wirtualnej > odzyskiwanie awaryjne** zakończy się niepowodzeniem z powodu błędu "*nie można włączyć replikacji, ponieważ istnieją linki starych zasobów na maszynie wirtualnej*".


### <a name="fix-the-problem"></a>Rozwiąż problem

>[!NOTE]
>
>Azure Site Recovery nie usuwa źródłowej maszyny wirtualnej ani nie wpływa na nie w jakikolwiek sposób, wykonując poniższe kroki.
>

1. Usuń blokadę z maszyny wirtualnej lub grupy zasobów maszyny wirtualnej. Na przykład: Nazwa maszyny wirtualnej "MoveDemo" ma blokadę zasobu, która musi zostać usunięta.

   ![Network_Selection_greyed_out](./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png)
2. Pobierz skrypt [Usuń nieodświeżoną Azure Site Recovery konfigurację](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1).
3. Wykonaj skrypt *Cleanup-stale-ASR-config-Azure-VM. ps1*.
4. Podaj identyfikator subskrypcji, grupę zasobów maszyny wirtualnej i nazwę maszyny wirtualnej jako parametr.
5. Jeśli zażądano poświadczeń platformy Azure, określ, czy skrypt ma być wykonywany bez żadnych błędów. 


## <a name="replication-cannot-be-enabled-because-of-the-existing-stale-resource-links-on-the-vm-error-code-150226"></a>Nie można włączyć replikacji z powodu istniejących starych linków zasobów na maszynie wirtualnej (kod błędu 150226)

**Przyczyna: Maszyna wirtualna ma nieaktualną konfigurację pozostałą z poprzedniej Site Recovery ochrony**

Nieaktualną konfigurację, może pozostać na Maszynie wirtualnej platformy Azure w następujących przypadkach:

- Włączono replikację maszyny wirtualnej platformy Azure przy użyciu Site Recovery, a następnie wyłącz replikację, ale **źródłowa maszyna wirtualna miała blokadę zasobu**.
- Włączyć replikację dla maszyny Wirtualnej platformy Azure przy użyciu usługi Site Recovery, a następnie usunąć magazyn usługi Site Recovery bez jawnie wyłączenie replikacji na maszynie Wirtualnej.
- Możesz włączyć replikację dla maszyny Wirtualnej platformy Azure przy użyciu usługi Site Recovery, a następnie usunąć grupę zasobów zawierającą magazyn usługi Site Recovery bez jawnie wyłączenie replikacji na maszynie Wirtualnej.

### <a name="fix-the-problem"></a>Rozwiąż problem

>[!NOTE]
>
>Azure Site Recovery nie usuwa źródłowej maszyny wirtualnej ani nie wpływa na nie w jakikolwiek sposób, wykonując poniższe kroki.


1. Usuń blokadę z maszyny wirtualnej lub grupy zasobów maszyny wirtualnej, jeśli istnieją. *Na przykład:* Nazwa maszyny wirtualnej "MoveDemo" ma blokadę zasobu, która musi zostać usunięta.
   
   ![Network_Selection_greyed_out](./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png)
2. Pobierz skrypt [Usuń nieodświeżoną Azure Site Recovery konfigurację](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1).
3. Wykonaj skrypt *Cleanup-stale-ASR-config-Azure-VM. ps1*.
4. Podaj identyfikator subskrypcji, grupę zasobów maszyny wirtualnej i nazwę maszyny wirtualnej jako parametr.
5. Jeśli zażądano poświadczeń platformy Azure, określ, czy skrypt ma być wykonywany bez żadnych błędów.  

## <a name="unable-to-see-the-azure-vm-or-resource-group--for-selection-in-enable-replication"></a>Nie można wyświetlić maszyny wirtualnej lub grupy zasobów platformy Azure do wyboru w pozycji "Włącz replikację"

 **Przyczyna 1:  Grupa zasobów i źródłowa maszyna wirtualna znajdują się w różnych lokalizacjach**
 
Azure Site Recovery obecnie zezwala na to, że grupa zasobów regionu źródłowego i maszyny wirtualne powinny znajdować się w tej samej lokalizacji. Jeśli tak nie jest, nie można znaleźć maszyny wirtualnej ani grupy zasobów w czasie ochrony. 

Aby obejść ten **sposób**, można włączyć replikację z poziomu maszyny wirtualnej zamiast magazynu usługi Recovery Services. Przejdź do pozycji źródłowa maszyna wirtualna > Właściwości > Odzyskiwanie awaryjne i Włącz replikację.

**Przyczyna 2: Grupa zasobów nie jest częścią wybranej subskrypcji**

Nie można odnaleźć grupy zasobów w czasie ochrony, jeśli nie jest częścią danej subskrypcji. Upewnij się, że grupa zasobów należy do subskrypcji, która jest używana.

 **Przyczyna 3: Nieodświeżona konfiguracja**
 
Jeśli chcesz włączyć replikację maszyny Wirtualnej nie jest widoczny, jego może z powodu nieaktualną konfigurację Site Recovery pozostać na maszynie Wirtualnej platformy Azure. Nieaktualną konfigurację, może pozostać na Maszynie wirtualnej platformy Azure w następujących przypadkach:

- Włączyć replikację dla maszyny Wirtualnej platformy Azure przy użyciu usługi Site Recovery, a następnie usunąć magazyn usługi Site Recovery bez jawnie wyłączenie replikacji na maszynie Wirtualnej.
- Możesz włączyć replikację dla maszyny Wirtualnej platformy Azure przy użyciu usługi Site Recovery, a następnie usunąć grupę zasobów zawierającą magazyn usługi Site Recovery bez jawnie wyłączenie replikacji na maszynie Wirtualnej.

- Włączono replikację maszyny wirtualnej platformy Azure przy użyciu Site Recovery, a następnie wyłącz replikację, ale źródłowa maszyna wirtualna miała blokadę zasobu.

### <a name="fix-the-problem"></a>Rozwiąż problem

> [!NOTE]
>
> Upewnij się, że moduł "" AzureRM. resources "" został zaktualizowany przed użyciem poniższego skryptu. Azure Site Recovery nie usuwa źródłowej maszyny wirtualnej ani nie wpływa na nie w jakikolwiek sposób, wykonując poniższe kroki.
>

1. Usuń blokadę z maszyny wirtualnej lub grupy zasobów maszyny wirtualnej, jeśli istnieją. *Na przykład:* Nazwa maszyny wirtualnej "MoveDemo" ma blokadę zasobu, która musi zostać usunięta.

   ![Network_Selection_greyed_out](./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png)
2. Pobierz skrypt [Usuń nieaktualną konfigurację](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1).
3. Wykonaj skrypt *Cleanup-stale-ASR-config-Azure-VM. ps1*.
4. Podaj identyfikator subskrypcji, grupę zasobów maszyny wirtualnej i nazwę maszyny wirtualnej jako parametr.
5. Jeśli zażądano poświadczeń platformy Azure, określ, czy skrypt ma być wykonywany bez żadnych błędów.

## <a name="unable-to-select-virtual-machine-for-protection"></a>Nie można wybrać maszynę wirtualną do ochrony
 **Przyczyna 1:  Niektóre rozszerzenia maszyny wirtualnej są zainstalowane w stanie awarii lub braku odpowiedzi** <br>
 Przejdź do maszyny wirtualnej > Ustawienia > rozszerzenia i sprawdź, czy istnieją jakiekolwiek rozszerzenia w stanie niepowodzenia. Odinstaluj rozszerzenie nie powiodło się i spróbuj ponownie ochronę maszyny wirtualnej.<br>
 **Przyczyna 2:  [Stan aprowizacji maszyny wirtualnej jest nieprawidłowy](#vms-provisioning-state-is-not-valid-error-code-150019)**

## <a name="vms-provisioning-state-is-not-valid-error-code-150019"></a>Stan inicjowania obsługi administracyjnej maszyny Wirtualnej jest nieprawidłowy (kod błędu: 150019)

Aby włączyć replikację na maszynie Wirtualnej, powinna być w stanie inicjowania obsługi administracyjnej **Powodzenie**. Możesz sprawdzić stan maszyny Wirtualnej, wykonując poniższe kroki.

1.  Wybierz **Eksploratora zasobów** z **wszystkich usług** w witrynie Azure portal.
2.  Rozwiń **subskrypcje** listy i wybierz swoją subskrypcję.
3.  Rozwiń **ResourceGroups** listy i wybierz grupę zasobów maszyny wirtualnej.
4.  Rozwiń **zasobów** listy i wybierz maszynę wirtualną
5.  Sprawdź **provisioningState** pola w widoku wystąpienia po prawej stronie.

### <a name="fix-the-problem"></a>Rozwiąż problem

- Jeśli **provisioningState** jest, skontaktuj się z pomocą techniczną, podając szczegóły, aby rozwiązać **.**
- Jeśli **provisioningState** jest **aktualizowanie**, innego rozszerzenia mogą być wprowadzenie wdrożone. Sprawdź, czy wszystkie trwające operacje na maszynie Wirtualnej, poczekaj na ukończenie i ponów próbę wykonania nie powiodło się, Usługa Site Recovery ich **włączyć replikację** zadania.

## <a name="unable-to-select-target-virtual-network---network-selection-tab-is-grayed-out"></a>Nie można wybrać docelowej sieci wirtualnej — karta wybór sieciowych jest wyszarzona.

**Przyczyna 1: Jeśli maszyna wirtualna jest dołączona do sieci, która jest już zamapowana na sieć docelową.**
- Jeśli źródłowa maszyna wirtualna jest częścią sieci wirtualnej i inną maszynę Wirtualną z tej samej sieci wirtualnej jest już zamapowana z siecią w docelowej grupie zasobów, następnie według wyboru sieci domyślne listy rozwijanej zostaną wyłączone.

![Network_Selection_greyed_out](./media/site-recovery-azure-to-azure-troubleshoot/unabletoselectnw.png)

**Przyczyna 2: Jeśli maszyna wirtualna została wcześniej chroniona przy użyciu Azure Site Recovery i wyłączono replikację.**
 - Wyłączanie replikacji maszyny Wirtualnej nie powoduje usunięcia mapowanie sieci. Musi on być usunięte z magazynu usługi recovery, gdy maszyna wirtualna była chroniona. </br>
 Przejdź do magazynu usług odzyskiwania > infrastruktura usługi Site Recovery > Mapowanie sieci. </br>
 ![Delete_NW_Mapping](./media/site-recovery-azure-to-azure-troubleshoot/delete_nw_mapping.png)
 - Sieć docelowa konfigurowanych podczas instalacji odzyskiwania po awarii można zmienić po początkowej skonfigurowane, po włączeniu ochrony maszyny Wirtualnej. </br>
 ![Modify_NW_mapping](./media/site-recovery-azure-to-azure-troubleshoot/modify_nw_mapping.png)
 - Należy pamiętać, że zmiana mapowania sieci ma wpływ na wszystkie chronione maszyny wirtualne korzystające z mapowanie w określonej sieci.


## <a name="comvolume-shadow-copy-service-error-error-code-151025"></a>COM +/ błąd usługi kopiowania woluminów w tle (kod błędu: 151025)

**Kod błędu:** | **Możliwe przyczyny** | **Zalecenia**
--- | --- | ---
151025<br></br>**Komunikat**: Nie można zainstalować rozszerzenia Site Recovery | -Usługa "COM + System Application" wyłączona.</br></br>-Usługa "Kopiowanie woluminów w tle" jest wyłączona.| Ustaw tryb automatycznego lub ręcznego uruchamiania usług "COM + System Application" i "Kopiowanie woluminów w tle".

### <a name="fix-the-problem"></a>Rozwiąż problem

Można otworzyć konsoli "Usługi" i upewnij się, "COM + System Application" i "Kopiowanie woluminów w tle" nie są ustawione na wartość "Wyłączony" dla "Typ uruchamiania".
  ![Błąd modelu COM](./media/azure-to-azure-troubleshoot-errors/com-error.png)

## <a name="unsupported-managed-disk-size-error-code-150172"></a>Nieobsługiwany rozmiar dysku zarządzanego (kod błędu 150172)


**Kod błędu:** | **Możliwe przyczyny** | **Zalecenia**
--- | --- | ---
150172<br></br>**Komunikat**: Nie można włączyć ochrony dla maszyny wirtualnej, ponieważ ma ona (diskname) o rozmiarze (DiskSize), która jest mniejsza niż minimalna obsługiwana wielkość 1024 MB. | -Dysk jest mniejszy niż obsługiwany rozmiar wynoszący 1024 MB| Upewnij się, że rozmiary dysków mieszczą się w zakresie obsługiwanego rozmiaru, a następnie spróbuj ponownie wykonać operację.

## <a name="enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-error-code-151126"></a>Włączenie ochrony nie powiodło się, ponieważ nazwa urządzenia wymieniona w konfiguracji GRUB zamiast identyfikatora UUID (kod błędu 151126)

**Możliwa przyczyna:** </br>
Pliki konfiguracji GRUB ("/boot/grub/menu.lst", "/boot/grub/grub.cfg", "/Boot/grub2/grub.cfg" lub "/etc/default/grub") mogą zawierać wartość parametrów **root** i **Resume** jako rzeczywiste nazwy urządzeń zamiast UUID. Site Recovery zezwala na metodę UUID, ponieważ nazwa urządzeń może ulec zmianie po ponownym uruchomieniu maszyny wirtualnej, ponieważ maszyna wirtualna może nie zostać utworzona z tą samą nazwą w przypadku przejścia w tryb failover, co spowodowało problemy. Na przykład: </br>


- Poniższy wiersz pochodzi z pliku GRUB **/Boot/grub2/grub.cfg**. <br>
  *Linux/Boot/vmlinuz-3.12.49-11-default **root =/dev/sda2** $ {extra_cmdline} **Resume =/dev/sda1** powitalny = ciche ciche showopts*


- Następujący wiersz pochodzi z pliku GRUB **/boot/grub/menu.lst**
  *jądra/Boot/vmlinuz-3.0.101-63-default **root =/dev/sda2** **Resume =/dev/sda1** powitalnego = Silent crashkernel = 256M-: 128M showopts VGA = 0x314*

Jeśli zobaczysz pogrubiony ciąg powyżej, GRUB ma rzeczywiste nazwy urządzeń dla parametrów "root" i "resume" zamiast identyfikatora UUID.

**Jak naprawić:**<br>
Nazwy urządzeń należy zamienić na odpowiedni identyfikator UUID.<br>


1. Znajdź identyfikator UUID urządzenia, wykonując polecenie "blkid \<Device Name >". Na przykład:<br>
   ```
   blkid /dev/sda1
   ```<br>
   ```/dev/sda1: UUID="6f614b44-433b-431b-9ca1-4dd2f6f74f6b" TYPE="swap" ```<br>
   ```blkid /dev/sda2```<br>
   ```/dev/sda2: UUID="62927e85-f7ba-40bc-9993-cc1feeb191e4" TYPE="ext3"
   ```<br>
   ```



1. Teraz Zastąp nazwę urządzenia identyfikatorem UUID w formacie takim jak "root = UUID =\<UUID >". Na przykład, jeśli zamienimy nazwy urządzeń z identyfikatorem UUID dla certyfikatu głównego i wznowienia wymienionego powyżej w plikach "/Boot/grub2/grub.cfg", "/Boot/grub2/grub.cfg" lub "/etc/default/grub:, wówczas wiersze w plikach wyglądają podobnie. <br>
   *jądro/Boot/vmlinuz-3.0.101-63-default **root = UUID = 62927e85-f7ba-40bc-9993-cc1feeb191e4** **Resume = UUID = 6f614b44-433B-431b-9ca1-4dd2f6f74f6b** powitalny = cichy crashkernel = 256M-: 128M showopts VGA = 0x314*
1. Ponownie uruchom ochronę

## <a name="enable-protection-failed-as-device-mentioned-in-the-grub-configuration-doesnt-existerror-code-151124"></a>Włączenie ochrony nie powiodło się, ponieważ urządzenie wymienione w konfiguracji GRUB nie istnieje (kod błędu 151124)
**Możliwa przyczyna:** </br>
Pliki konfiguracji GRUB ("/boot/grub/menu.lst", "/boot/grub/grub.cfg", "/Boot/grub2/grub.cfg" lub "/etc/default/grub") mogą zawierać parametry "rd.lvm.lv" lub "rd_LVM_LV" wskazujące urządzenie LVM, które ma zostać odnalezione w momencie rozruchu. Jeśli te urządzenia LVM nie istnieją, wówczas chroniony system nie zostanie uruchomiony i zablokowany w procesie rozruchu. Nawet te same będą obserwowane z maszyną wirtualną trybu failover. Poniżej przedstawiono kilka przykładów:

Kilka przykładów: </br>

1. Następujący wiersz pochodzi z pliku GRUB **"/Boot/grub2/grub.cfg"** na RHEL7. </br>
   *linux16/vmlinuz-3.10.0-957.el7.x86_64 root =/dev/mapper/rhel_mup--rhel7u6-root ro crashkernel = 128M\@64M **Rd. LVM. lv = rootvg/root Rd. LVM. lv = rootvg/swap** rhgb quiet lang = en_US. UTF-8*</br>
   W wyróżnionej części widać, że GRUB musi wykryć dwa urządzenia LVM z nazwami **"root"** i **"swap"** z grupy woluminów "rootvg".
1. Następujący wiersz pochodzi z pliku GRUB **"/etc/default/grub"** na RHEL7 </br>
   *GRUB_CMDLINE_LINUX = "crashkernel = **autord. LVM. lv = rootvg/root Rd. LVM. lv = rootvg/swap** rhgb quiet"*</br>
   W wyróżnionej części widać, że GRUB musi wykryć dwa urządzenia LVM z nazwami **"root"** i **"swap"** z grupy woluminów "rootvg".
1. Następujący wiersz pochodzi z pliku GRUB **"/boot/grub/menu.lst"** na RHEL6 </br>
   *jądro/vmlinuz-2.6.32-754.EL6.x86_64 ro root = UUID = 36dd8b45-e90d-40d6-81ac-ad0d0725d69e rd_NO_LUKS LANG = en_US. UTF-8 rd_NO_MD SYSFONT = latarcyrheb-sun16 crashkernel = autord_LVM_LV = rootvg/lv_root KEYBOARDtype = komputer PC TABLE = US rd_LVM_LV = rootvg/lv_swap rd_NO_DM rhgb quiet* </br>
   W wyróżnionej części widać, że GRUB musi wykryć dwa urządzenia LVM z nazwami **"root"** i **"swap"** z grupy woluminów "rootvg".<br>

**Jak naprawić:**<br>

Jeśli urządzenie LVM nie istnieje, napraw je przez utworzenie lub Usuń parametr dla tego samego z plików konfiguracji GRUB, a następnie ponów próbę włączenia ochrony. </br>

## <a name="site-recovery-mobility-service-update-completed-with-warnings--error-code-151083"></a>Aktualizacja usługi mobilności Site Recovery została ukończona z ostrzeżeniami (kod błędu 151083)
Usługa mobilności Site Recovery ma wiele składników, z których jeden jest nazywany sterownikiem filtru. Sterownik filtru jest ładowany do pamięci systemowej tylko w momencie ponownego uruchomienia systemu. Za każdym razem, gdy istnieją Site Recovery aktualizacje usługi mobilności, które mają zmiany sterownika filtru, należy zaktualizować maszynę, ale nadal zostanie wyświetlone ostrzeżenie, że niektóre poprawki wymagają ponownego uruchomienia. Oznacza to, że poprawki sterownika filtru można wykonać tylko w przypadku załadowania nowego sterownika filtru, który może wystąpić tylko w momencie ponownego uruchomienia systemu.<br>
**Należy pamiętać** , że jest to ostrzeżenie i istniejąca replikacja nadal działa nawet po nowej aktualizacji agenta. Możesz wybrać opcję ponownego uruchomienia w dowolnym momencie, aby uzyskać korzyści płynące z nowego sterownika filtru, ale jeśli nie zostanie uruchomiony ponowny rozruch, a także nadal trwa praca starego sterownika filtru. Oprócz sterownika filtru **korzyści wynikające z innych ulepszeń i poprawek w usłudze mobilności są realizowane bez konieczności ponownego uruchamiania, gdy agent zostanie zaktualizowany.**  


## <a name="protection-couldnt-be-enabled-as-replica-managed-disk-diskname-replica-already-exists-without-expected-tags-in-the-target-resource-group-error-code-150161"></a>Nie można włączyć ochrony, ponieważ dysk zarządzany repliki "diskname-Replica" już istnieje bez oczekiwanych tagów w docelowej grupie zasobów (kod błędu 150161

**Przyczyna**: Taka sytuacja może wystąpić, jeśli maszyna wirtualna była chroniona wcześniej w przeszłości i podczas wyłączania replikacji, dysk repliki nie został oczyszczony z powodu pewnej przyczyny.</br>
**Jak naprawić:** Usuń wymieniony dysk repliki w komunikacie o błędzie i ponownie uruchom zadanie ochrony zakończone niepowodzeniem.

## <a name="next-steps"></a>Następne kroki
[Replikowanie maszyn wirtualnych platformy Azure](site-recovery-replicate-azure-to-azure.md)
