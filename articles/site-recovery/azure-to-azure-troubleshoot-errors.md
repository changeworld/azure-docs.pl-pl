---
title: Rozwiązywanie problemów z replikacją maszyn wirtualnych platformy Azure w Azure Site Recovery
description: Rozwiązywanie problemów podczas replikowania maszyn wirtualnych platformy Azure na potrzeby odzyskiwania po awarii.
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/08/2019
ms.author: asgang
ms.openlocfilehash: b3c459c0eaac98a1cb704b4346153f77ec974188
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74084923"
---
# <a name="troubleshoot-azure-to-azure-vm-replication-errors"></a>Rozwiązywanie problemów z replikacją maszyny wirtualnej z platformy Azure do platformy Azure

W tym artykule opisano sposób rozwiązywania typowych błędów w Azure Site Recovery podczas replikacji i odzyskiwania maszyn wirtualnych platformy Azure z jednego regionu do innego. Aby uzyskać więcej informacji o obsługiwanych konfiguracjach, zobacz [macierz obsługi do replikowania maszyn wirtualnych platformy Azure](site-recovery-support-matrix-azure-to-azure.md).

## <a name="azure-resource-quota-issues-error-code-150097"></a>Problemy z przydziałami zasobów platformy Azure (kod błędu 150097)

Upewnij się, że subskrypcja jest włączona, aby utworzyć maszyny wirtualne platformy Azure w regionie docelowym, który ma być używany jako region odzyskiwania po awarii. Upewnij się również, że Twoja subskrypcja ma wystarczający przydział, aby utworzyć maszyny wirtualne o niezbędnych rozmiarach. Domyślnie Site Recovery wybiera docelowy rozmiar maszyny wirtualnej, który jest taki sam jak rozmiar źródłowej maszyny wirtualnej. Jeśli odpowiedni rozmiar nie jest dostępny, Site Recovery automatycznie wybiera najbliższy dostępny rozmiar.

Jeśli nie ma rozmiaru obsługującego konfigurację źródłowej maszyny wirtualnej, zostanie wyświetlony następujący komunikat:

> "Nie można włączyć replikacji dla maszyny wirtualnej *VmName*".

### <a name="possible-causes"></a>Możliwe przyczyny

- W Twoim IDENTYFIKATORze subskrypcji nie jest włączona funkcja tworzenia żadnych maszyn wirtualnych w lokalizacji regionu docelowego.
- Identyfikator subskrypcji nie jest włączony lub nie ma wystarczającego limitu przydziału, aby utworzyć określone rozmiary maszyn wirtualnych w lokalizacji regionu docelowego.
- Nie znaleziono odpowiedniego docelowego rozmiaru maszyny wirtualnej, aby dopasować liczbę kart sieciowych źródłowej maszyny wirtualnej (2) do identyfikatora subskrypcji w lokalizacji regionu docelowego.

### <a name="fix-the-problem"></a>Rozwiąż problem

Skontaktuj się z [pomocą techniczną dotyczącą rozliczeń platformy Azure](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) , aby umożliwić subskrypcji Tworzenie maszyn wirtualnych o wymaganych rozmiarach w lokalizacji docelowej. Następnie ponów próbę wykonania operacji zakończonej niepowodzeniem.

Jeśli lokalizacja docelowa ma ograniczenie pojemności, wyłącz replikację. Następnie należy włączyć replikację do innej lokalizacji, w której subskrypcja ma wystarczający przydział do tworzenia maszyn wirtualnych o wymaganych rozmiarach.

## <a name="trusted-root-certificates-error-code-151066"></a>Zaufane certyfikaty główne (kod błędu 151066)

Jeśli na maszynie wirtualnej nie ma wszystkich najnowszych zaufanych certyfikatów głównych, zadanie "Włącz replikację" Site Recovery może zakończyć się niepowodzeniem. Uwierzytelnianie i autoryzacja wywołań usługi Site Recovery z maszyny wirtualnej kończy się niepowodzeniem bez certyfikatów. 

Jeśli zadanie włączania replikacji nie powiedzie się, zostanie wyświetlony następujący komunikat:

> "Site Recovery konfiguracji nie powiodła się."

### <a name="possible-cause"></a>Możliwa przyczyna

Zaufane certyfikaty główne wymagane do autoryzacji i uwierzytelniania nie są obecne na maszynie wirtualnej.

### <a name="fix-the-problem"></a>Rozwiąż problem

#### <a name="windows"></a>Windows

Na maszynie wirtualnej z systemem operacyjnym Windows zainstaluj najnowsze aktualizacje systemu Windows na maszynie wirtualnej, aby wszystkie zaufane certyfikaty główne znajdowały się na komputerze. Postępuj zgodnie z typowym procesem zarządzania aktualizacjami systemu Windows lub aktualizacją certyfikatu w organizacji, aby uzyskać najnowsze certyfikaty główne i zaktualizowaną listę odwołania certyfikatów na maszynach wirtualnych.

Jeśli pracujesz w środowisku bez połączenia, postępuj zgodnie z standardowego procesu aktualizacji Windows w Twojej organizacji do uzyskania certyfikatów. Jeśli na maszynie Wirtualnej nie są wymagane certyfikaty, wywołań do usługi Site Recovery zakończyć się niepowodzeniem ze względu na bezpieczeństwo.

Aby sprawdzić, czy problem został rozwiązany, przejdź do login.microsoftonline.com za pomocą przeglądarki na maszynie wirtualnej.

Aby uzyskać więcej informacji, zobacz [Konfigurowanie zaufanych katalogów głównych i niedozwolonych certyfikatów](https://technet.microsoft.com/library/dn265983.aspx).

#### <a name="linux"></a>Linux

Postępuj zgodnie ze wskazówkami dostarczonymi przez dystrybutora wersji systemu operacyjnego Linux w celu uzyskania najnowszych zaufanych certyfikatów głównych i najnowszej listy odwołania certyfikatów na maszynie wirtualnej.

Ponieważ system SuSE Linux używa linków symbolicznych (lub *linków symbolicznych*) do obsługi listy certyfikatów, wykonaj następujące kroki:

1. Zaloguj się jako użytkownik root.

1. Uruchom to polecenie, aby zmienić katalog:

    **# CD/etc/SSL/certs**

1. Sprawdź, czy certyfikat głównego urzędu certyfikacji firmy Symantec jest obecny:

    **# LS VeriSign_Class_3_Public_Primary_Certification_Authority_G5. pem**

1. Jeśli certyfikat głównego urzędu certyfikacji firmy Symantec nie zostanie znaleziony, uruchom następujące polecenie, aby pobrać plik. Sprawdź pod kątem błędów i postępuj zgodnie z zalecanymi akcjami dotyczącymi błędów sieci.

    **# wget https://www.symantec.com/content/dam/symantec/docs/other-resources/verisign-class-3-public-primary-certification-authority-g5-en.pem-O VeriSign_Class_3_Public_Primary_Certification_Authority_G5. pem**

1. Sprawdź, czy certyfikat głównego urzędu certyfikacji Baltimore jest obecny:

    **# LS Baltimore_CyberTrust_Root. pem**

1. Jeśli certyfikat głównego urzędu certyfikacji Baltimore nie zostanie znaleziony, Uruchom to polecenie, aby pobrać certyfikat:

    **# wget https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem-O Baltimore_CyberTrust_Root. pem**

1. Sprawdź, czy certyfikat DigiCert_Global_Root_CA jest obecny:

    **# LS DigiCert_Global_Root_CA. pem**

1. Jeśli nie można odnaleźć DigiCert_Global_Root_CA, uruchom następujące polecenia, aby pobrać certyfikat:

    **# wget http://www.digicert.com/CACerts/DigiCertGlobalRootCA.crt**

    **# OpenSSL x509 — w DigiCertGlobalRootCA. CRT — informowanie DigiCert_Global_Root_CA PEM**

1. Uruchom skrypt rehash, aby zaktualizować skróty podmiotu certyfikatu dla nowo pobranych certyfikatów:

    **# c_rehash**

1. Uruchom te polecenia, aby sprawdzić, czy dla certyfikatów zostały utworzone wartości skrótu podmiotu jako linków symbolicznych:

    - Dotyczące

        **# ls-l | Baltimore grep**

    - Dane wyjściowe:

        `lrwxrwxrwx 1 root root   29 Jan  8 09:48 3ad48a91.0 -> Baltimore_CyberTrust_Root.pem`

        `-rw-r--r-- 1 root root 1303 Jun  5  2014 Baltimore_CyberTrust_Root.pem`

    - Dotyczące

        **# ls-l | VeriSign_Class_3_Public_Primary_Certification_Authority_G5 grep**

    - Dane wyjściowe:

        `-rw-r--r-- 1 root root 1774 Jun  5  2014 VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem`

        `lrwxrwxrwx 1 root root   62 Jan  8 09:48 facacbc6.0 -> VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem`

    - Dotyczące

        **# ls-l | DigiCert_Global_Root grep**

    - Dane wyjściowe:

        `lrwxrwxrwx 1 root root   27 Jan  8 09:48 399e7759.0 -> DigiCert_Global_Root_CA.pem`

        `-rw-r--r-- 1 root root 1380 Jun  5  2014 DigiCert_Global_Root_CA.pem`

1. Utwórz kopię pliku VeriSign_Class_3_Public_Primary_Certification_Authority_G5. pem z nazwą pliku b204d74a. 0:

    **# CP VeriSign_Class_3_Public_Primary_Certification_Authority_G5. pem b204d74a. 0**

1. Utwórz kopię pliku Baltimore_CyberTrust_Root. pem z nazwą pliku 653b494a. 0:

    **# CP Baltimore_CyberTrust_Root. pem 653b494a. 0**

1. Utwórz kopię pliku DigiCert_Global_Root_CA. pem z nazwą pliku 3513523f. 0:

    **# CP DigiCert_Global_Root_CA. pem 3513523f. 0**

1. Sprawdź, czy pliki są obecne:

    - Dotyczące

        **# ls-l 653b494a. 0 b204d74a. 0 3513523f. 0**

    - Dane wyjściowe

        `-rw-r--r-- 1 root root 1774 Jan  8 09:52 3513523f.0`

        `-rw-r--r-- 1 root root 1303 Jan  8 09:52 653b494a.0`

        `-rw-r--r-- 1 root root 1774 Jan  8 09:52 b204d74a.0`

## <a name="outbound-connectivity-for-site-recovery-urls-or-ip-ranges-error-code-151037-or-151072"></a>Połączenia ruchu wychodzącego dla zakresów adresów URL Site Recovery lub adres IP (kod błędu: 151037 lub 151072)

Aby replikacja Site Recovery działała, połączenie wychodzące jest wymagane z maszyny wirtualnej do określonych adresów URL lub zakresów adresów IP. Jeśli maszyna wirtualna znajduje się za zaporą lub używa reguł Sieciowej grupy zabezpieczeń sieci do sterowania ruchem wychodzącym, może być jedną z tych problemów twarzy.

### <a name="issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195-br"></a>Problem 1: nie można zarejestrować maszyny wirtualnej platformy Azure przy użyciu Site Recovery (kod błędu 151195)

#### <a name="possible-cause"></a>Możliwa przyczyna 

Nie można nawiązać połączenia z punktami końcowymi Site Recovery z powodu błędu rozpoznawania nazw DNS.

Ten problem występuje najczęściej podczas ponownej ochrony, po przełączeniu maszyny wirtualnej w tryb failover, ale serwer DNS nie jest dostępny z regionu odzyskiwania po awarii (DR).

#### <a name="fix-the-problem"></a>Rozwiąż problem

W przypadku korzystania z niestandardowej usługi DNS upewnij się, że serwer DNS jest dostępny z regionu odzyskiwania po awarii. Aby dowiedzieć się, czy na maszynie wirtualnej jest zainstalowany niestandardowy serwer DNS, przejdź do pozycji *Sieć odzyskiwania po awarii* > **serwery DNS**.

![Lista niestandardowych serwerów DNS](./media/azure-to-azure-troubleshoot-errors/custom_dns.PNG)

Wypróbuj, uzyskiwanie dostępu do serwera DNS z maszyny wirtualnej. Jeśli serwer jest niedostępny, udostępnij go przez przełączenie serwera DNS w tryb failover lub przez utworzenie linii lokacji między siecią DR a usługą DNS.

### <a name="issue-2-site-recovery-configuration-failed-error-code-151196"></a>Problem 2: Konfiguracja Site Recovery nie powiodła się (kod błędu 151196)

#### <a name="possible-cause"></a>Możliwa przyczyna

Nie można nawiązać połączenia z punktami końcowymi uwierzytelniania z pakietem Office 365 i tożsamością IP4.

#### <a name="fix-the-problem"></a>Rozwiąż problem

Site Recovery wymaga dostępu do zakresów adresów IP pakietu Office 365 na potrzeby uwierzytelniania.
Jeśli używasz reguł sieciowej grupy zabezpieczeń platformy Azure lub serwera proxy zapory do kontrolowania łączności sieciowej wychodzącej na maszynie wirtualnej, upewnij się, że zezwalasz na komunikację z zakresami adresów IP pakietu Office 365. Utwórz regułę sieciowej grupy zabezpieczeń na podstawie [tagu usługi Azure Active Directory (Azure AD)](../virtual-network/security-overview.md#service-tags), zezwalając na dostęp do wszystkich adresów IP odpowiadających usłudze Azure AD. Jeśli nowe adresy są dodawane do usługi Azure AD w przyszłości, należy utworzyć nowe reguły sieciowej grupy zabezpieczeń.

> [!NOTE]
> Jeśli maszyny wirtualne znajdują się za *standardowym* wewnętrznym modułem równoważenia obciążenia, moduł równoważenia obciążenia domyślnie nie ma dostępu do zakresów adresów IP pakietu Office 365 (czyli login.microsoftonline.com). Zmień typ wewnętrznego modułu równoważenia obciążenia na *podstawowy* lub Utwórz dostęp wychodzący zgodnie z opisem w artykule [Konfigurowanie równoważenia obciążenia i reguł ruchu wychodzącego](https://aka.ms/lboutboundrulescli).

### <a name="issue-3-site-recovery-configuration-failed-error-code-151197"></a>Problem 3: Konfiguracja Site Recovery nie powiodła się (kod błędu 151197)

#### <a name="possible-cause"></a>Możliwa przyczyna

Nie można nawiązać połączenia, aby Site Recovery punkty końcowe usługi.

#### <a name="fix-the-problem"></a>Rozwiąż problem

Site Recovery wymaga dostępu do [Site Recovery zakresów adresów IP](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-ip-address-ranges), w zależności od regionu. Upewnij się, że wymagane zakresy adresów IP są dostępne z maszyny wirtualnej.

### <a name="issue-4-azure-to-azure-replication-failed-when-the-network-traffic-goes-through-an-on-premises-proxy-server-error-code-151072"></a>Problem 4: replikacja z platformy Azure do platformy Azure nie powiodła się, gdy ruch sieciowy przechodzi przez lokalny serwer proxy (kod błędu 151072)

#### <a name="possible-cause"></a>Możliwa przyczyna

Niestandardowe ustawienia serwera proxy są nieprawidłowe, a Agent usługi mobilności Site Recovery nie wykrył automatycznie ustawień serwera proxy w programie Internet Explorer.

#### <a name="fix-the-problem"></a>Rozwiąż problem

Agent usługi mobilności wykrywa ustawienia serwera proxy z programu Internet Explorer w systemie Windows i z/etc/environment w systemie Linux.

Jeśli wolisz ustawić tylko serwer proxy dla usługi mobilności, możesz podać szczegóły serwera proxy w pliku ProxyInfo. conf w następujących lokalizacjach:

- **Linux**:/usr/local/InMage/config/
- **Windows**: C:\ProgramData\Microsoft witryny Azure Recovery\Config

W ProxyInfo. conf Podaj ustawienia serwera proxy w następującym formacie pliku inicjującego:

> [*serwer proxy*]

> Address = *http://1.2.3.4*

> Port =*567*


> [!NOTE]
> Agent usługi mobilności Site Recovery obsługuje tylko *nieuwierzytelnione serwery proxy*.

### <a name="more-information"></a>Więcej informacji

Aby określić [wymagane adresy URL](azure-to-azure-about-networking.md#outbound-connectivity-for-urls) lub [wymagane zakresy adresów IP](azure-to-azure-about-networking.md#outbound-connectivity-for-ip-address-ranges), postępuj zgodnie ze wskazówkami w [temacie dotyczącym sieci na platformie Azure do replikacji platformy Azure](site-recovery-azure-to-azure-networking-guidance.md).

## <a name="disk-not-found-in-the-machine-error-code-150039"></a>Nie znaleziono maszyny (kod błędu: 150039) dysku

Nowy dysk dołączony do maszyny Wirtualnej musi być zainicjowany. Jeśli dysk nie zostanie znaleziony, zostanie wyświetlony następujący komunikat:

> "Dysk danych Azure *diskname* *DiskURI* z numerem jednostki logicznej *LUN* *LUNValue* nie został zmapowany na odpowiedni dysk, który jest raportowany z poziomu maszyny wirtualnej o tej samej wartości LUN.

### <a name="possible-causes"></a>Możliwe przyczyny

- Nowy dysk danych został podłączony do maszyny wirtualnej, ale nie został zainicjowany.
- Dysk danych wewnątrz maszyny wirtualnej nie zgłasza poprawnie wartości numer jednostki logicznej (LUN), przy użyciu której dysk został dołączony do maszyny wirtualnej.

### <a name="fix-the-problem"></a>Rozwiąż problem

Upewnij się, że dyski danych są zainicjowane, a następnie spróbuj ponownie wykonać operację.

- **System Windows**: [Dołącz i zainicjuj nowy dysk](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal).

- **Linux**: [zainicjuj nowy dysk danych w systemie Linux](https://docs.microsoft.com/azure/virtual-machines/linux/add-disk).

Jeśli problem będzie się powtarzać, skontaktuj się z pomocą techniczną.

## <a name="one-or-more-disks-are-available-for-protection-error-code-153039"></a>Co najmniej jeden dysk jest dostępny do ochrony (kod błędu 153039)

### <a name="possible-causes"></a>Możliwe przyczyny

- Co najmniej jeden dysk został niedawno dodany do maszyny wirtualnej po włączeniu ochrony.
- Co najmniej jeden dysk został zainicjowany po włączeniu ochrony maszyny wirtualnej.

### <a name="fix-the-problem"></a>Rozwiąż problem

Aby ponownie zmienić stan replikacji maszyny wirtualnej, możesz wybrać opcję ochrony dysków lub odrzucić ostrzeżenie.

#### <a name="to-protect-the-disks"></a>Aby chronić dyski

1. Przejdź do **pozycji zreplikowane elementy** > *Nazwa maszyny wirtualnej* > **dyski**.
1. Wybierz dysk niechroniony, a następnie wybierz pozycję **Włącz replikację**:

    ![Włącz replikację na dyskach maszyn wirtualnych](./media/azure-to-azure-troubleshoot-errors/add-disk.png)

#### <a name="to-dismiss-the-warning"></a>Aby odrzucić ostrzeżenie

1. Przejdź do **pozycji zreplikowane elementy** > *Nazwa maszyny wirtualnej*.
1. Wybierz ostrzeżenie w sekcji **Przegląd** , a następnie wybierz przycisk **OK**.

    ![Odrzuć nowe ostrzeżenie dysku](./media/azure-to-azure-troubleshoot-errors/dismiss-warning.png)

## <a name="remove-the-virtual-machine-from-the-vault-completed-with-information-error-code-150225"></a>Usuwanie maszyny wirtualnej z magazynu zostało zakończone z informacjami (kod błędu 150225)

Gdy program chroni maszynę wirtualną, Site Recovery tworzy niektóre linki na źródłowej maszynie wirtualnej. Po usunięciu ochrony lub wyłączeniu replikacji program Site Recovery usuwa te linki w ramach zadania oczyszczania. Jeśli maszyna wirtualna ma blokadę zasobu, zadanie oczyszczania zostanie ukończone z informacjami. Informacje o tym, że maszyna wirtualna została usunięta z magazynu Recovery Services, ale nie można wyczyścić niektórych nieodświeżonych linków na maszynie źródłowej.

To ostrzeżenie można zignorować, jeśli nie chcesz ponownie chronić tej maszyny wirtualnej. Jeśli jednak chcesz później chronić tę maszynę wirtualną, postępuj zgodnie z instrukcjami w sekcji "Rozwiązywanie problemu", aby wyczyścić linki.

> [!WARNING]
> Jeśli nie wykonasz oczyszczania:
>
> - Po włączeniu replikacji za pomocą magazynu Recovery Services, maszyna wirtualna nie zostanie wyświetlona.
> - Jeśli spróbujesz chronić MASZYNę wirtualną przy użyciu **ustawień** > **maszyny wirtualnej** > **odzyskiwania po awarii**, operacja zakończy się niepowodzeniem z komunikatem "nie można włączyć replikacji z powodu istniejących starych linków zasobów na maszynie wirtualnej".

### <a name="fix-the-problem"></a>Rozwiąż problem

> [!NOTE]
> Site Recovery nie usuwa źródłowej maszyny wirtualnej ani nie wpływa na nie w żaden sposób podczas wykonywania tych kroków.

1. Usuń blokadę z maszyny wirtualnej lub grupy zasobów maszyny wirtualnej. Na przykład na poniższej ilustracji należy usunąć blokadę zasobu maszyny wirtualnej o nazwie "MoveDemo":

    ![Usuń blokadę z maszyny wirtualnej](./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png)

1. Pobierz skrypt, aby [usunąć nieaktualną konfigurację Site Recovery](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1).
1. Uruchom skrypt o nazwie Cleanup-stale-asr-config-Azure-VM. ps1. Podaj identyfikator subskrypcji, grupę zasobów maszyny wirtualnej i nazwę maszyny wirtualnej jako parametry.
1. Jeśli zostanie wyświetlony monit o podanie poświadczeń platformy Azure, podaj je. Następnie sprawdź, czy skrypt działa bez żadnych błędów.

## <a name="replication-cant-be-enabled-because-of-stale-resource-links-on-the-vm-error-code-150226"></a>Nie można włączyć replikacji z powodu starych linków zasobów na maszynie wirtualnej (kod błędu 150226)

### <a name="possible-cause"></a>Możliwa przyczyna

Maszyna wirtualna ma nieaktualną konfigurację z poprzedniej Site Recovery ochrony.

Nieodświeżona konfiguracja może wystąpić na maszynie wirtualnej platformy Azure, jeśli włączono replikację dla maszyny wirtualnej platformy Azure za pomocą Site Recovery, a następnie:

- Replikacja została wyłączona, ale źródłowa maszyna wirtualna miała blokadę zasobu.
- Magazyn Site Recovery został usunięty bez jawnego wyłączania replikacji na maszynie wirtualnej.
- Usunięto grupę zasobów zawierającą magazyn Site Recovery bez jawnego wyłączania replikacji na maszynie wirtualnej.

### <a name="fix-the-problem"></a>Rozwiąż problem

> [!NOTE]
> Site Recovery nie usuwa źródłowej maszyny wirtualnej ani nie wpływa na nie w żaden sposób podczas wykonywania tych kroków.

1. Usuń blokadę z maszyny wirtualnej lub grupy zasobów maszyny wirtualnej. Na przykład na poniższej ilustracji należy usunąć blokadę zasobu maszyny wirtualnej o nazwie "MoveDemo":

    ![Usuń blokadę z maszyny wirtualnej](./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png)

1. Pobierz skrypt, aby [usunąć nieaktualną konfigurację Site Recovery](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1).
1. Uruchom skrypt o nazwie Cleanup-stale-asr-config-Azure-VM. ps1. Podaj identyfikator subskrypcji, grupę zasobów maszyny wirtualnej i nazwę maszyny wirtualnej jako parametry.
1. Jeśli zostanie wyświetlony monit o podanie poświadczeń platformy Azure, podaj je. Następnie sprawdź, czy skrypt działa bez żadnych błędów.

## <a name="unable-to-see-the-azure-vm-or-resource-group-for-the-selection-in-the-enable-replication-job"></a>Nie można wyświetlić maszyny wirtualnej lub grupy zasobów platformy Azure dla wyboru w zadaniu "Włącz replikację"

### <a name="cause-1-the-resource-group-and-source-virtual-machine-are-in-different-locations"></a>Przyczyna 1: Grupa zasobów i źródłowa maszyna wirtualna znajdują się w różnych lokalizacjach

Site Recovery obecnie wymaga, aby grupa zasobów regionu źródłowego i maszyny wirtualne znajdować się w tej samej lokalizacji. Jeśli tak nie jest, nie będzie można znaleźć maszyny wirtualnej ani grupy zasobów podczas próby zastosowania ochrony.

Aby obejść ten sposób, można włączyć replikację z poziomu maszyny wirtualnej zamiast magazynu Recovery Services. Przejdź do pozycji **źródłowa maszyna wirtualna** > **Właściwości** > **odzyskiwanie awaryjne** i Włącz replikację.

### <a name="cause-2-the-resource-group-is-not-part-of-the-selected-subscription"></a>Przyczyna 2: Grupa zasobów nie należy do wybranej subskrypcji

Jeśli grupa zasobów nie jest częścią wybranej subskrypcji, nie będzie można znaleźć grupy zasobów w czasie ochrony. Upewnij się, że grupa zasobów należy do używanej subskrypcji.

### <a name="cause-3-stale-configuration"></a>Przyczyna 3: nieodświeżona konfiguracja

Na maszynie wirtualnej platformy Azure może nie być widoczna maszyna wirtualna, która ma zostać włączona na potrzeby Site Recovery replikacji. Ten stan może wystąpić, jeśli włączono replikację maszyny wirtualnej platformy Azure przy użyciu Site Recovery, a następnie:

- Magazyn Site Recovery został usunięty bez jawnego wyłączania replikacji na maszynie wirtualnej.
- Usunięto grupę zasobów zawierającą magazyn Site Recovery bez jawnego wyłączania replikacji na maszynie wirtualnej.
- Replikacja została wyłączona, ale źródłowa maszyna wirtualna miała blokadę zasobu.

### <a name="fix-the-problem"></a>Rozwiąż problem

> [!NOTE]
> Upewnij się, że moduł "AzureRM. resources" został zaktualizowany przed użyciem skryptu wymienionego w tej sekcji.  Site Recovery nie usuwa źródłowej maszyny wirtualnej ani nie wpływa na nie w żaden sposób podczas wykonywania tych kroków.

1. Usuń blokadę, jeśli istnieje, z maszyny wirtualnej lub grupy zasobów maszyny wirtualnej. Na przykład na poniższej ilustracji należy usunąć blokadę zasobu maszyny wirtualnej o nazwie "MoveDemo":

    ![Usuń blokadę z maszyny wirtualnej](./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png)

1. Pobierz skrypt, aby [usunąć nieaktualną konfigurację Site Recovery](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1).
1. Uruchom skrypt o nazwie Cleanup-stale-asr-config-Azure-VM. ps1. Podaj identyfikator subskrypcji, grupę zasobów maszyny wirtualnej i nazwę maszyny wirtualnej jako parametry.
1. Jeśli zostanie wyświetlony monit o podanie poświadczeń platformy Azure, podaj je. Następnie sprawdź, czy skrypt działa bez żadnych błędów.

## <a name="unable-to-select-a-virtual-machine-for-protection"></a>Nie można wybrać maszyny wirtualnej do ochrony

### <a name="cause-1-the-virtual-machine-has-an-extension-installed-in-a-failed-or-unresponsive-state"></a>Przyczyna 1: maszyna wirtualna ma rozszerzenie zainstalowane w stanie awarii lub braku odpowiedzi

Przejdź do pozycji **maszyny wirtualne** > **Ustawienia** > **rozszerzenia** i sprawdź, czy nie wystąpił błąd. Odinstaluj wszystkie nieudane rozszerzenia, a następnie spróbuj ponownie włączyć ochronę maszyny wirtualnej.

### <a name="cause-2-the-vms-provisioning-state-is-not-valid"></a>Przyczyna 2: stan aprowizacji maszyny wirtualnej jest nieprawidłowy

Instrukcje dotyczące rozwiązywania problemów w [stanie aprowizacji maszyny wirtualnej nie są prawidłowe](#the-vms-provisioning-state-is-not-valid-error-code-150019)w dalszej części tego artykułu.

## <a name="the-vms-provisioning-state-is-not-valid-error-code-150019"></a>Stan aprowizacji maszyny wirtualnej jest nieprawidłowy (kod błędu 150019)

Aby włączyć replikację na maszynie wirtualnej, jej stan aprowizacji musi się **powieść**. Wykonaj następujące kroki, aby sprawdzić stan aprowizacji:

1. W Azure Portal wybierz **Eksplorator zasobów** z **wszystkich usług**.
1. Rozwiń **subskrypcje** listy i wybierz swoją subskrypcję.
1. Rozwiń **ResourceGroups** listy i wybierz grupę zasobów maszyny wirtualnej.
1. Rozwiń listę **zasoby** i wybierz maszynę wirtualną.
1. Zaznacz pole **provisioningState** w widoku wystąpienia po prawej stronie.

### <a name="fix-the-problem"></a>Rozwiąż problem

- Jeśli **provisioningState** jest, skontaktuj się z pomocą techniczną, podając szczegóły, aby rozwiązać **.**
- W przypadku **aktualizowania** **provisioningState** może być wdrażane inne rozszerzenie. Sprawdź, czy na maszynie wirtualnej istnieją bieżące operacje, poczekaj na ich zakończenie, a następnie spróbuj ponownie wykonać zadanie "Włącz replikację" Site Recovery.

## <a name="unable-to-select-target-vm-network-selection-tab-is-unavailable"></a>Nie można wybrać docelowej maszyny wirtualnej (karta wybór sieci jest niedostępna)

### <a name="cause-1-your-vm-is-attached-to-a-network-thats-already-mapped-to-a-target-network"></a>Przyczyna 1: maszyna wirtualna jest dołączona do sieci, która jest już zamapowana na sieć docelową

Jeśli źródłowa maszyna wirtualna jest częścią sieci wirtualnej, a inna maszyna wirtualna z tej samej sieci wirtualnej jest już zamapowana przy użyciu sieci w docelowej grupie zasobów, pole listy rozwijanej Wybór sieci jest niedostępne (domyślnie wygaszone).

![Lista wyboru sieci jest niedostępna](./media/site-recovery-azure-to-azure-troubleshoot/unabletoselectnw.png)

### <a name="cause-2-you-previously-protected-the-vm-by-using-site-recovery-and-then-you-disabled-the-replication"></a>Przyczyna 2: wcześniej była chroniona maszyna wirtualna przy użyciu Site Recovery, a następnie została wyłączona replikacja

Wyłączenie replikacji maszyny wirtualnej nie powoduje usunięcia mapowania sieci. Należy usunąć mapowanie z magazynu Recovery Services, w którym maszyna wirtualna była chroniona. Przejdź do *magazynu Recovery Services* > **Site Recovery infrastruktury** > **mapowanie sieci**.

![Usuń mapowanie sieci](./media/site-recovery-azure-to-azure-troubleshoot/delete_nw_mapping.png)

Sieć docelowa, która została skonfigurowana podczas konfigurowania odzyskiwania po awarii, może zostać zmieniona po zakończeniu konfiguracji początkowej, gdy maszyna wirtualna jest chroniona:

![Modyfikowanie mapowania sieci](./media/site-recovery-azure-to-azure-troubleshoot/modify_nw_mapping.png)

Należy zauważyć, że zmiana mapowania sieci ma wpływ na wszystkie chronione maszyny wirtualne korzystające z tego samego mapowania sieci.

## <a name="com-or-volume-shadow-copy-service-error-error-code-151025"></a>Błąd usługi kopiowania woluminów w tle (kod błędu 151025)

W przypadku wystąpienia tego błędu zostanie wyświetlony następujący komunikat:

> "Nie można zainstalować rozszerzenia Site Recovery"

### <a name="possible-causes"></a>Możliwe przyczyny

- Usługa aplikacji systemu COM+ jest wyłączona.
- Usługa kopiowania woluminów w tle jest wyłączona.

### <a name="fix-the-problem"></a>Rozwiąż problem

Ustaw dla aplikacji systemu COM+ i usługi kopiowania woluminów w tle automatyczne lub ręczne tryb uruchamiania.

1. Otwórz konsolę usługi w systemie Windows.
1. Upewnij się, że aplikacja systemowa modelu COM+ i usługi kopiowania woluminów w tle nie są ustawione na wartość **wyłączone** jako ich **Typ uruchamiania**.

    ![Sprawdź typ uruchomienia aplikacji systemowej modelu COM+ i usług kopiowania woluminów w tle](./media/azure-to-azure-troubleshoot-errors/com-error.png)

## <a name="unsupported-managed-disk-size-error-code-150172"></a>Nieobsługiwany rozmiar dysku zarządzanego (kod błędu 150172)

W przypadku wystąpienia tego błędu zostanie wyświetlony następujący komunikat:

> "Nie można włączyć ochrony dla maszyny wirtualnej, ponieważ ma ona wartość *diskname* o rozmiarze *DiskSize*) *, która jest mniejsza niż minimalna obsługiwana wielkość 1024 MB".

### <a name="possible-cause"></a>Możliwa przyczyna

Dysk jest mniejszy niż obsługiwany rozmiar wynoszący 1024 MB.

### <a name="fix-the-problem"></a>Rozwiąż problem

Upewnij się, że rozmiar dysku mieści się w obsługiwanym zakresie rozmiaru, a następnie spróbuj ponownie wykonać operację.

## <a name="enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-error-code-151126"></a>Ochrona nie została włączona, ponieważ konfiguracja GRUB zawiera nazwę urządzenia zamiast identyfikatora UUID (kod błędu 151126)

### <a name="possible-cause"></a>Możliwa przyczyna

Pliki konfiguracji GRUB systemu Linux (/boot/grub/menu.lst ",/boot/grub/grub.cfg,/Boot/grub2/grub.cfg lub/etc/default/Grub) mogą określać rzeczywiste nazwy urządzeń zamiast wartości UUID dla parametrów *głównych* i *wznawiania* . Site Recovery wymaga identyfikatora UUID, ponieważ nazwy urządzeń mogą się zmieniać. Po ponownym uruchomieniu maszyny wirtualnej w trybie failover mogą nie mieć takiej samej nazwy, co spowodowało problemy.

Poniższe przykłady są wierszami z plików GRUB, w których pojawiają się nazwy urządzeń (pogrubienie) zamiast wymaganych identyfikatorów UUID:

- /Boot/grub2/grub.cfg pliku

  > Linux/Boot/vmlinuz-3.12.49-11-default **root =/dev/sda2** $ {extra_cmdline} **Resume =/dev/sda1** powitalny = ciche ciche showopts

- Plik:/boot/grub/menu.lst

  > jądro/Boot/vmlinuz-3.0.101-63-default **root =/dev/sda2** **Resume =/dev/sda1** powitalny = ciche crashkernel = 256M-: 128M showopts VGA = 0x314


### <a name="fix-the-problem"></a>Rozwiąż problem

Zastąp nazwy poszczególnych urządzeń odpowiednimi identyfikatorami UUID:

1. Znajdź identyfikator UUID urządzenia, wykonując polecenie **blkid** ***nazwę urządzenia***. Na przykład:

    ```
    blkid /dev/sda1
    /dev/sda1: UUID="6f614b44-433b-431b-9ca1-4dd2f6f74f6b" TYPE="swap"
    blkid /dev/sda2
    /dev/sda2: UUID="62927e85-f7ba-40bc-9993-cc1feeb191e4" TYPE="ext3"
   ```

1. Zastąp nazwę urządzenia identyfikatorem UUID w formacie **root = uuid**=*UUID* i **Resume = UUID**=*UUID*. Na przykład po zamianie wiersz z/boot/grub/menu.lst (opisany wcześniej) będzie wyglądać następująco:

    > jądro/Boot/vmlinuz-3.0.101-63-default **root = UUID = 62927e85-f7ba-40bc-9993-cc1feeb191e4** **Resume = UUID = 6f614b44-433B-431b-9ca1-4dd2f6f74f6b** powitalny = cichy crashkernel = 256M-: 128M showopts VGA = 0x314

1. Ponów próbę ochrony.

## <a name="enable-protection-failed-because-the-device-mentioned-in-the-grub-configuration-doesnt-exist-error-code-151124"></a>Włączenie ochrony nie powiodło się, ponieważ urządzenie wymienione w konfiguracji GRUB nie istnieje (kod błędu 151124)

### <a name="possible-cause"></a>Możliwa przyczyna

Pliki konfiguracji GRUB (/boot/grub/menu.lst,/boot/grub/grub.cfg,/Boot/grub2/grub.cfg lub/etc/default/Grub) mogą zawierać parametry *Rd.LVM.lv* lub *rd_LVM_LV*. Parametry te identyfikują urządzenia z menedżerem woluminów logicznych (LVM), które mają zostać odnalezione w czasie rozruchu. Jeśli te urządzenia LVM nie istnieją, chroniony system nie zostanie uruchomiony i zostanie zablokowany w procesie rozruchu. Ten sam problem będzie również widoczny dla maszyny wirtualnej trybu failover. Oto kilka przykładów:

- Plik:/Boot/grub2/grub.cfg na RHEL7:

    > linux16/vmlinuz-3.10.0-957.el7. x86_64 root =/dev/mapper/rhel_mup--rhel7u6-root ro crashkernel = 128M\@64M **Rd. LVM. lv = rootvg/root Rd. LVM. lv = rootvg/swap** RHGB quiet LANG = en_US. UTF-8

- Plik:/etc/default/grub na RHEL7:

    > GRUB_CMDLINE_LINUX = "crashkernel = **autord. LVM. lv = rootvg/root Rd. LVM. lv = rootvg/swap** rhgb quiet"

- Plik:/boot/grub/menu.lst na RHEL6:

    > jądro/vmlinuz-2.6.32-754.EL6. x86_64 ro root = UUID = 36dd8b45-e90d-40d6-81ac-ad0d0725d69e rd_NO_LUKS LANG = en_US. UTF-8 rd_NO_MD SYSFONT = latarcyrheb-sun16 crashkernel = **autord_LVM_LV = rootvg/lv_root** KeyboardType = komputer PC Table = US **rd_LVM_LV = rootvg/lv_swap** rd_NO_DM rhgb quiet

W każdym przykładzie część pogrubiona pokazuje, że GRUB musi wykryć dwa urządzenia LVM z nazwami "root" i "swap" z grupy woluminów "rootvg".

### <a name="fix-the-problem"></a>Rozwiąż problem

Jeśli urządzenie LVM nie istnieje, utwórz je lub usuń odpowiednie parametry z plików konfiguracji GRUB. Następnie spróbuj ponownie włączyć ochronę.

## <a name="a-site-recovery-mobility-service-update-finished-with-warnings-error-code-151083"></a>Aktualizacja usługi mobilności Site Recovery została ukończona z ostrzeżeniami (kod błędu 151083)

Usługa mobilności Site Recovery ma wiele składników, z których jeden jest nazywany sterownikiem filtru. Sterownik filtru jest ładowany do pamięci systemowej tylko podczas ponownego uruchamiania systemu. Za każdym razem, gdy aktualizacja usługi mobilności obejmuje zmiany sterownika filtru, komputer zostanie zaktualizowany, ale nadal zobaczysz ostrzeżenie, że niektóre poprawki wymagają ponownego uruchomienia. Ostrzeżenie jest wyświetlane, ponieważ poprawki sterownika filtru mogą obowiązywać dopiero po załadowaniu nowego sterownika filtru, który jest wykonywany tylko podczas ponownego uruchamiania.

> [!NOTE]
> Jest to tylko ostrzeżenie. Istniejąca replikacja będzie nadal działała nawet po nowej aktualizacji agenta. Możesz wybrać opcję ponownego uruchomienia w każdym przypadku, gdy chcesz skorzystać z nowego sterownika filtru, ale stary sterownik filtru nadal działa, jeśli nie zostanie uruchomiony ponownie.
>
> Oprócz sterownika filtru, korzyści wynikające z innych ulepszeń i poprawek w ramach aktualizacji usługi mobilności mają zastosowanie bez konieczności ponownego uruchamiania.  

## <a name="protection-couldnt-be-enabled-because-the-replica-managed-disk-already-exists-without-expected-tags-in-the-target-resource-group-error-code-150161"></a>Nie można włączyć ochrony, ponieważ dysk zarządzany repliki już istnieje bez oczekiwanych tagów w docelowej grupie zasobów (kod błędu 150161)

### <a name="possible-cause"></a>Możliwa przyczyna

Ten problem może wystąpić, jeśli maszyna wirtualna była wcześniej chroniona, a replikacja została wyłączona, dysk repliki nie został oczyszczony.

### <a name="fix-the-problem"></a>Rozwiąż problem

Usuń dysk repliki określony w komunikacie o błędzie i spróbuj ponownie wykonać zadanie ochrony zakończone niepowodzeniem.

## <a name="next-steps"></a>Następne kroki

[Replikowanie maszyn wirtualnych platformy Azure](site-recovery-replicate-azure-to-azure.md)
