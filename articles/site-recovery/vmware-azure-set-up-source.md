---
title: Konfigurowanie źródła środowiska i konfiguracji między aplikacjami serwera odzyskiwania po awarii maszyn wirtualnych programu VMware na platformę Azure za pomocą usługi Azure Site Recovery | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób konfigurowania sieci w środowisku lokalnym i serwerem konfiguracji na potrzeby odzyskiwania po awarii maszyn wirtualnych VMware na platformę Azure za pomocą usługi Azure Site Recovery.
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 10/29/2018
ms.author: ramamill
ms.openlocfilehash: e09e93ef85449c51e35b8da7ad93ee7088a0e7b4
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/12/2018
ms.locfileid: "51566316"
---
# <a name="set-up-the-source-environment-and-configuration-server"></a>Konfigurowanie środowiska i konfiguracji serwera źródłowego

W tym artykule opisano sposób konfigurowania środowiska lokalnego źródła dla odzyskiwania po awarii maszyn wirtualnych VMware na platformę Azure za pomocą [usługi Azure Site Recovery](site-recovery-overview.md). Obejmuje kroki konfigurowania na komputerze lokalnym jako serwera konfiguracji usługi Site Recovery, a automatyczne odnajdowanie lokalnych maszyn wirtualnych. 

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że wykonano następujące czynności

- Planowane swoją awaryjnego odzyskiwania wdrożenia przy użyciu [planista wdrażania usługi Azure Site Recovery](site-recovery-deployment-planner.md). Planistę pomaga upewnić się, ma wystarczające zasoby i przepustowość na potrzeby odzyskiwania po awarii i punktów odzyskiwania założony cel w Twojej organizacji.
- [Konfigurowanie zasobów platformy Azure](tutorial-prepare-azure.md) w [witryny Azure portal](http://portal.azure.com).
- [Konfigurowanie lokalnego wdrożenia oprogramowania VMware](vmware-azure-tutorial-prepare-on-premises.md) zasobów, w tym dedykowanego konta do automatycznego odnajdowania lokalnych maszyn wirtualnych programu VMware.
- Możesz [Przejrzyj często zadawane pytania dotyczące](vmware-azure-common-questions.md#configuration-server) o wdrożenie serwera konfiguracji i zarządzania.


## <a name="choose-protection-goals"></a>Wybierz cele ochrony

1. W obszarze **Magazyny usługi Recovery Services** wybierz nazwę magazynu. 
2. W obszarze **Wprowadzenie** wybierz pozycję Site Recovery. Następnie wybierz pozycję **Przygotowywanie infrastruktury**.
3. W obszarze **Cel ochrony** > **Gdzie znajdują się maszyny** wybierz pozycję **Lokalne**.
4. W obszarze **Gdzie chcesz zreplikować maszyny** wybierz pozycję **Na platformę Azure**.
5. W obszarze **Czy maszyny są zwirtualizowane** wybierz pozycję **Tak, z funkcją VMware vSphere Hypervisor**. Następnie wybierz przycisk **OK**.

## <a name="about-the-configuration-server"></a>Dotyczące serwera konfiguracji

Podczas konfigurowania odzyskiwania po awarii maszyn wirtualnych programu VMware do platformy Azure możesz wdrożyć lokalny serwer konfiguracji.

- Konfiguracja współrzędne międzyserwerowej komunikacji lokalnych programu VMware i platformą Azure. Umożliwia także zarządzanie replikacją danych.
- Możesz wdrożyć serwer konfiguracji jako Maszynę wirtualną VMware.
- Usługa Site Recovery zawiera szablon OVA możesz pobrać z witryny Azure portal i zaimportować na serwer vCenter, aby skonfigurować serwer konfiguracji maszyny Wirtualnej.
- [Dowiedz się więcej](vmware-azure-architecture.md) dotyczące składników serwera konfiguracji i procesów.


>[!NOTE]
Nie należy używać w instrukcjach, Jeżeli wdrażasz serwera konfiguracji na potrzeby odzyskiwania po awarii lokalnych maszyn fizycznych do platformy Azure. W tym scenariuszu, postępuj zgodnie z [w tym artykule](physical-azure-set-up-source.md).


## <a name="before-you-deploy-the-configuration-server"></a>Przed wdrożeniem serwera konfiguracji

Jeśli zainstalujesz serwer konfiguracji jako Maszynę wirtualną VMware przy użyciu szablonu OVA maszyny Wirtualnej zostanie zainstalowany z wszystkie wstępnie wymagane składniki. Jeśli chcesz przejrzeć wymagania wstępne, skorzystaj z poniższych artykułów.

- [Dowiedz się więcej o](vmware-azure-configuration-server-requirements.md) sprzętu, oprogramowania i pojemność wymagania dotyczące serwera konfiguracji.
- Jeśli replikujesz wielu maszyn wirtualnych VMware, należy przejrzeć [zagadnienia dotyczące planowania pojemności](site-recovery-plan-capacity-vmware.md)i uruchom [planista wdrażania usługi Azure Site Recovery](site-recovery-deployment-planner.md) narzędzie na potrzeby replikacji oprogramowania VMWare.
- Licencja Windows szablonu OVA jest licencji ewaluacyjnej, która jest ważna przez 180 dni. Po upływie tego czasu należy aktywować Windows mający ważną licencję. 
- Szablon OVA udostępnia prosty sposób, aby skonfigurować serwer konfiguracji jako Maszynę wirtualną VMware. Jeśli z jakiegoś powodu, musisz skonfigurować maszynę Wirtualną VMware bez szablonu, należy przejrzeć wymagania wstępne i postępuj zgodnie z ręcznego zgodnie z instrukcjami w [w tym artykule](physical-azure-set-up-source.md).
- Twoje konto platformy Azure wymaga uprawnień do tworzenia aplikacji w usłudze Azure AD.


>[!IMPORTANT]
Zgodnie z opisem w tym artykule, należy wdrożyć serwer konfiguracji. Kopiowanie lub klonowanie istniejącego serwera konfiguracji nie jest obsługiwane.

### <a name="set-up-azure-account-permissions"></a>Ustawianie uprawnień konta platformy Azure

Dzierżawa/globalnego administratora można przypisać uprawnienia do tworzenia aplikacji usługi Azure AD do konta lub przypisać rolę dewelopera aplikacji, (który ma uprawnienia) do konta.


Administrator dzierżawy/globalny można przyznać uprawnienia dla konta w następujący sposób:

1. W usłudze Azure AD administratora globalnego/dzierżawy należy przejść do **usługi Azure Active Directory** > **użytkowników** > **ustawienia użytkownika**.
2. Administrator powinien być ustawiony **rejestracje aplikacji** do **tak**.

 > [!NOTE]
 > To ustawienie domyślne, które nie są poufne. [Dowiedz się więcej](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance).

Można również dzierżawy/globalne administrator ma uprawnienia do przypisywania roli do konta. [Dowiedz się więcej](https://docs.microsoft.comazure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal).

 


## <a name="download-the-ova-template"></a>Pobierz szablon OVA

1. W magazynie przejdź do pozycji **Przygotowanie infrastruktury** > **Źródłowa**.
2. W obszarze **Przygotowywanie źródła** wybierz pozycję **+Serwer konfiguracji**.
3. W obszarze **Dodawanie serwera** sprawdź, czy w sekcji **Typ serwera** jest widoczna pozycja **Serwer konfiguracji dla oprogramowania VMware**.
4. Pobierz szablon Open Virtualization aplikacji (OVA) dla serwera konfiguracji.

  > [!TIP]
>Możesz również pobrać najnowszą wersję szablonu serwera konfiguracji z [Microsoft Download Center](https://aka.ms/asrconfigurationserver).


## <a name="import-the-ova-template-in-vmware"></a>Importowanie szablonu OVA w środowisku programu VMware

1. Zaloguj się do serwera VMware vCenter lub hosta vSphere ESXi przy użyciu klienta VMware vSphere.
2. W menu **File** (Plik) wybierz pozycję **Deploy OVF Template** (Wdróż szablon OVF), aby uruchomić kreatora wdrażania szablonu OVF.

     ![Szablon OVF](./media/vmware-azure-deploy-configuration-server/vcenter-wizard.png)

3. W **wybierz źródło**, wprowadź lokalizację pobranego szablonu OVF.
4. W **Przejrzyj szczegóły**, wybierz opcję **dalej**.
5. W **wybierz nazwę i folder** i **wybierz konfigurację**, zaakceptuj ustawienia domyślne.
6. Na stronie **Wybierz magazyn** dla najlepszej wydajności wybierz pozycję **Thick Provision Eager Zeroed** w sekcji **Wybierz format dysku wirtualnego**.
7. Na pozostałych stronach kreatora zaakceptuj ustawienia domyślne.
8. W **gotowa do zakończenia**, aby skonfigurować maszynę Wirtualną z ustawieniami domyślnymi, wybierz opcję **Włącz po wdrożeniu** > **Zakończ**.
9. Domyślnie maszyna wirtualna jest wdrażany z jedną kartą sieciową. Jeśli chcesz dodać dodatkowe karty Sieciowej, wyczyść **Włącz po wdrożeniu**i kliknij przycisk **Zakończ**. Następnie postępuj zgodnie z następnej procedury.

## <a name="add-an-adapter-to-the-configuration-server"></a>Dodaj kartę do serwera konfiguracji

Jeśli chcesz dodać dodatkową kartę Sieciową do serwera konfiguracji, należy go dodać, aby zarejestrować serwer w magazynie. Po zarejestrowaniu nie można dodawać kart sieciowych.

1. Kliknij prawym przyciskiem myszy maszynę wirtualną na liście w kliencie vSphere, a następnie wybierz pozycję **Edytuj ustawienia**.
2. Na stronie **Hardware** (Sprzęt) wybierz pozycje **Add** > **Ethernet Adapter** (Dodaj, Karta Ethernet). Następnie wybierz przycisk **Dalej**.
3. Wybierz typ karty i sieć.
4. Aby połączyć z wirtualnej karty Sieciowej, gdy maszyna wirtualna jest włączona, wybierz pozycję **Połącz po włączeniu**. Następnie wybierz pozycję **dalej** > **Zakończ** > **OK**.

## <a name="register-the-configuration-server"></a>Rejestrowanie serwera konfiguracji 
Włącz maszynę Wirtualną i rejestrowanie serwera konfiguracji w magazynie usługi Site Recovery.

1. Włącz maszynę wirtualną z poziomu konsoli klienta VMware vSphere.
2. Maszyna wirtualna uruchamia się do środowiska instalacji systemu Windows Server 2016. Zaakceptuj umowę licencyjną i wprowadź hasło administratora.
3. Po zakończeniu instalacji zaloguj się na maszynie wirtualnej jako administrator.



## <a name="set-up-the-configuration-server"></a>Konfigurowanie serwera konfiguracji

W ramach wdrożenia należy zainstalować oprogramowanie MySQL na serwerze konfiguracji maszyny Wirtualnej. Można to zrobić na kilka sposobów:

- Pozwól, Usługa Site Recovery, pobieranie i instalowanie programu MySQL. Jeśli chcesz użyć tej opcji, nie trzeba nic robić, przed rozpoczęciem instalacji serwera konfiguracji.
- Pobierz i zainstaluj ręcznie MySQL: przed rozpoczęciem konfigurowania serwera konfiguracji, Pobierz Instalator MySQL i umieść je **C:\Temp\ASRSetup** folderu. Teraz instalowanie programu MySQL. 
- Pobierz ręcznie i pozwól Site Recovery Zainstaluj. Przed skonfigurowaniem serwera konfiguracji, Pobierz Instalator MySQL i umieścić go w **C:\Temp\ASRSetup** folderu.


1. Podczas pierwszego logowania się do maszyny Wirtualnej, uruchamia narzędzie konfiguracji usługi Azure Site Recovery.
2. Określ nazwę używaną do zarejestrowania serwera konfiguracji w magazynie usługi Site Recovery. Następnie wybierz przycisk **Dalej**.
3. Narzędzie sprawdza, czy maszyna wirtualna może połączyć się z platformą Azure. Po nawiązaniu połączenia wybierz pozycję **Zaloguj się**, aby zalogować się do subskrypcji platformy Azure. Należy pamiętać, że konto musi mieć dostęp do magazynu, w którym chcesz zarejestrować serwer konfiguracji.
4. Narzędzie wykonuje pewne zadania konfiguracyjne, a następnie wywołuje ponowne uruchomienie.
5. Ponownie zaloguj się do maszyny. Kreator zarządzania serwerem konfiguracji rozpoczyna się automatycznie w ciągu kilku sekund.
6. W kreatorze Wybierz **Konfiguracja łączności**.
7. Wybierz kartę Sieciową, która korzysta z serwera przetwarzania (domyślnie uruchamiane na serwerze konfiguracji) do odbierania ruchu replikacji na maszynach wirtualnych.
8. Następnie wybierz pozycję **Zapisz**. Należy pamiętać, że nie można zmienić ustawienia magazynu, po zarejestrowaniu serwera konfiguracji. 
9. W **magazyn usługi Recovery Services zaznacz**, zaloguj się w usłudze Microsoft Azure, wybierz swoją subskrypcję platformy Azure i grupę zasobów i magazyn. 
10. W **instalowanie oprogramowania innych firm**, instalowanie programu MySQL:
     - Jeśli usługa Site Recovery obsługuje MySQL pobierania i instalacji, kliknij przycisk **Pobierz i zainstaluj**. Poczekaj na ukończenie, a następnie przejść za pomocą Kreatora instalacji.
     - Jeśli pobrano MySQL i Site Recovery zostanie zainstalowany, kliknij **Pobierz i zainstaluj**. Poczekaj na zakończenie instalacji, a następnie kontynuować pracę kreatora.
     - Jeśli ręcznie pobrać i zainstalować MySQL, kliknij przycisk **Pobierz i zainstaluj**. Aplikacja będzie wyświetlana jako **zainstalowane**. Kontynuować pracę kreatora.
11. W **Weryfikowanie konfiguracji urządzenia**, Instalator sprawdza wymagania wstępne, przed kontynuowaniem. 
12. W **poświadczenia serwera vCenter Server/vSphere ESXi Konfiguruj**, wprowadź nazwę FQDN lub adres IP serwera vCenter lub hosta vSphere, na których maszynach wirtualnych, którą chcesz replikować znajdują się. Wprowadź port, na którym nasłuchuje serwer, a następnie wprowadź przyjazną nazwę dla serwera VMware w magazynie.
13. Wprowadź poświadczenia, które będą używane przez serwer konfiguracji do łączenia się z serwerem VMware i automatycznie odnajduje maszyny wirtualne VMware dostępne do replikacji. Wybierz **Dodaj** >  **nadal**. Poświadczenia są zapisywane lokalnie.
14. W **skonfiguruj poświadczenia maszyny wirtualnej**, określ poświadczenia, które usługa Site Recovery będzie używane do automatycznego instalowania usługi mobilności, po włączeniu replikacji dla maszyny Wirtualnej.
    - W przypadku maszyn z systemem Windows konto wymaga uprawnień administratora lokalnego na replikowanych maszynach.
    - W przypadku systemu Linux należy podać dane superużytkownika.
15. Aby ukończyć rejestrację, wybierz pozycję **Zakończ konfigurację**.
16. Po zakończeniu rejestracji Otwórz witrynę Azure portal i sprawdź, czy serwer konfiguracji i serwer VMware są widoczne w **magazyn usługi Recovery Services** > **Zarządzaj**  >   **Infrastruktura usługi Site Recovery** > **serwery konfiguracji**.


## <a name="exclude-antivirus-on-the-configuration-server"></a>Wyklucz oprogramowania antywirusowego na serwerze konfiguracji

Jeśli oprogramowanie antywirusowe jest uruchomiona na serwerze konfiguracji maszyny Wirtualnej, upewnij się, że następujące foldery są wykluczone z operacji oprogramowania antywirusowego. Gwarantuje to, że replikacja i łączność działać zgodnie z oczekiwaniami: 

- Agent usług C:\Program Files\Microsoft Azure Recovery Services
- Dostawca usługi C:\Program Files\Microsoft Azure Site Recovery
- C:\Program Files\Microsoft Azure Site Recovery Configuration Manager
- Narzędzie do C:\Program Files\Microsoft usługi Azure Site Recovery błąd zbierania
- C:\thirdparty
- C:\Temp
- C:\strawberry
- C:\ProgramData\MySQL
- C:\Program pliki (x86) \MySQL
- C:\ProgramData\ASR
- C:\ProgramData\Microsoft Azure Site Recovery
- C:\ProgramData\ASRLogs
- C:\ProgramData\ASRSetupLogs
- C:\ProgramData\LogUploadServiceLogs
- C:\Inetpub
- Katalogu instalacyjnego odzyskiwania lokacji. Na przykład: pliki E:\Program (x86) \Microsoft Azure Site Recovery


## <a name="next-steps"></a>Kolejne kroki
- Jeśli napotkasz problemy, zapoznaj się z [często zadawane pytania dotyczące](vmware-azure-common-questions.md#configuration server) i [Rozwiązywanie problemów z](vmware-azure-troubleshoot-configuration-server.md) dla serwera konfiguracji.
- Teraz [Konfigurowanie środowiska docelowego](./vmware-azure-set-up-target.md) na platformie Azure.
