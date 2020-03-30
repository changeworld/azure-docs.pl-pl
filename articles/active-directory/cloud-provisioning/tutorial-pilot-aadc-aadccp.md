---
title: Samouczek — pilotażowe inicjowanie obsługi administracyjnej w chmurze usługi Azure AD Connect dla istniejącego zsynchronizowanego lasu usługi AD
description: Samouczek.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 03/04/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: aba42e6bd9b11e47d793219c0ff06b9177d609f5
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "78298823"
---
# <a name="pilot-cloud-provisioning-for-an-existing-synced-ad-forest"></a>Pilotażowa aprowizacja w chmurze dla istniejącego zsynchronizowanego lasu usługi AD 

W tym samouczku przeprowadzi cię pilotażowe inicjowanie obsługi administracyjnej w chmurze dla testowego lasu usługi Active Directory, który jest już synchronizowany przy użyciu synchronizacji usługi Azure Active Directory (Azure AD) Connect.

![Utwórz](media/tutorial-migrate-aadc-aadccp/diagram.png)

## <a name="considerations"></a>Zagadnienia do rozważenia
Przed wypróbowaniem tego samouczka należy wziąć pod uwagę następujące elementy:
1. Upewnij się, że znasz podstawy inicjowania obsługi administracyjnej w chmurze. 
2. Upewnij się, że korzystasz z synchronizacji usługi Azure AD Connect w wersji 1.4.32.0 lub nowszej i skonfigurowano reguły synchronizacji zgodnie z udokumentowanymi. Podczas pilotowania zostanie usunięcie testowej instalacji organizacyjnej lub grupy z zakresu synchronizacji usługi Azure AD Connect. Przenoszenie obiektów poza zakres prowadzi do usunięcia tych obiektów w usłudze Azure AD. W przypadku obiektów użytkownika obiekty w usłudze Azure AD są usuwane bezchwalnie i można je przywrócić. W przypadku obiektów grupy obiekty w usłudze Azure AD są na czas usunięte i nie można ich przywrócić. Nowy typ łącza został wprowadzony w synchronizacji usługi Azure AD Connect, co uniemożliwi usunięcie w przypadku scenariusza pilotażowego. 
3. Upewnij się, że obiekty w zakresie pilotażowym mają ms-ds-consistencyGUID wypełnione tak chmura inicjowania obsługi administracyjnej twarde mecze obiektów. 

   > [!NOTE]
   > Synchronizacja usługi Azure AD Connect domyślnie nie wypełnia *usługi MS-ds-consistencyGUID* dla obiektów grupy.

4. Jest to zaawansowany scenariusz. Upewnij się, że należy wykonać kroki opisane w tym samouczku dokładnie.

## <a name="prerequisites"></a>Wymagania wstępne
Poniżej przedstawiono wymagania wstępne niezbędne do wykonania kroków tego samouczka
- Środowisko testowe z synchronizacją usługi Azure AD Connect w wersji 1.4.32.0 lub nowszej
- OU lub grupy, która jest w zakresie synchronizacji i może być używany pilot. Zalecamy rozpoczęcie od małego zestawu obiektów.
- Serwer z systemem Windows Server 2012 R2 lub nowszym, który będzie obsługiwał agenta inicjowania obsługi administracyjnej.  Nie może to być ten sam serwer co serwer usługi Azure AD Connect.
- Źródło zakotwiczenia dla synchronizacji AAD Connect powinno być *objectGuid* lub *ms-ds-consistencyGUID*

## <a name="update-azure-ad-connect"></a>Aktualizowanie usługi Azure AD Connect

Co najmniej powinien mieć [usługi Azure AD connect](https://www.microsoft.com/download/details.aspx?id=47594) 1.4.32.0. Aby zaktualizować synchronizację usługi Azure AD Connect, wykonaj kroki opisane w [usłudze Azure AD Connect: Uaktualnienie do najnowszej wersji](../hybrid/how-to-upgrade-previous-version.md).  

## <a name="stop-the-scheduler"></a>Zatrzymaj harmonogram
Synchronizacja usługi Azure AD Connect synchronizuje zmiany zachodzące w katalogu lokalnym przy użyciu harmonogramu. Aby zmodyfikować i dodać reguły niestandardowe, chcesz wyłączyć harmonogram, aby synchronizacja nie była uruchamiana podczas pracy nad tym.  Wykonaj następujące czynności:

1.  Na serwerze z uruchomień synchronizacji usługi Azure AD Connect otwórz program PowerShell z uprawnieniami administracyjnymi.
2.  Uruchom polecenie `Stop-ADSyncSyncCycle`.  Hit Enter.
3.  Uruchom polecenie `Set-ADSyncScheduler -SyncCycleEnabled $false`.

>[!NOTE] 
>Jeśli korzystasz z własnego harmonogramu niestandardowego dla synchronizacji AAD Connect, wyłącz harmonogram. 

## <a name="create-custom-user-inbound-rule"></a>Tworzenie niestandardowej reguły przychodzącej użytkownika

 1. Uruchom edytor synchronizacji z menu aplikacji na pulpicie, jak pokazano poniżej:</br>
 ![Menu edytora reguł synchronizacji](media/how-to-cloud-custom-user-rule/user8.png)</br>
 
 2. Wybierz **pozycję Ruch przychodzący** z listy rozwijanej Kierunek i kliknij **pozycję Dodaj nową regułę**.
 ![Reguła niestandardowa](media/how-to-cloud-custom-user-rule/user1.png)</br>
 
 3. Na stronie **Opis** wprowadź następujące elementy i kliknij przycisk **Dalej:**

    **Nazwa:** Nadaj regule znaczącą nazwę<br>
    **Opis:** Dodawanie opisowego opisu<br>
    **Podłączony system:** Wybierz łącznik usługi AD, dla którego piszesz regułę synchronizacji niestandardowej<br>
    **Typ obiektu systemu podłączonego:** Użytkownika<br>
    **Typ obiektu Metaverse:** Osoba<br>
    **Typ łącza:** Dołączyć<br>
    **Pierwszeństwo:** Podaj wartość, która jest unikatowa w systemie<br>
    **Tag:** Pozostaw to puste<br>
    ![Reguła niestandardowa](media/how-to-cloud-custom-user-rule/user2.png)</br>
 
 4. Na stronie **filtru określania zakresu** wprowadź instalację organizacyjną lub grupę zabezpieczeń, w której ma być wyłączony pilot.  Aby filtrować na oku, dodaj część OU nazwy wyróżniającej. Ta reguła będzie stosowana do wszystkich użytkowników, którzy znajdują się w tej oi.  Tak więc, jeśli dn kończy się "OU = CPUers,DC = contoso,DC = com, należy dodać ten filtr.  Następnie kliknij przycisk **Dalej**. 

    |Reguła|Atrybut|Operator|Wartość|
    |-----|----|----|-----|
    |Zakres OU|Dn|ENDSWITH|Nazwa wyróżniająca OU.|
    |Grupa zakresu||IMEMBEROF|Nazwa wyróżniająca grupy zabezpieczeń.|

    ![Reguła niestandardowa](media/how-to-cloud-custom-user-rule/user3.png)</br>
 
 5. Na stronie Reguły **dołączania** kliknij przycisk **Dalej**.
 6. Na **transformacji** strony dodaj stałą transformację: przepływ True do cloudNoFlow atrybutu. Kliknij przycisk **Dodaj**.
 ![Reguła niestandardowa](media/how-to-cloud-custom-user-rule/user4.png)</br>

Te same kroki należy wykonać dla wszystkich typów obiektów (użytkownika, grupy i kontaktu). Powtórz kroki na skonfigurowany łącznik usługi AD / na las AD. 

## <a name="create-custom-user-outbound-rule"></a>Tworzenie niestandardowej reguły wychodzącej użytkownika

 1. Wybierz **pozycję Wychodzący** z listy rozwijanej Kierunek i kliknij **dodaj regułę**.
 ![Reguła niestandardowa](media/how-to-cloud-custom-user-rule/user5.png)</br>
 
 2. Na stronie **Opis** wprowadź następujące elementy i kliknij przycisk **Dalej:**

    **Nazwa:** Nadaj regule znaczącą nazwę<br>
    **Opis:** Dodawanie opisowego opisu<br>
    **Podłączony system:** Wybierz łącznik AAD, dla którego piszesz regułę synchronizacji niestandardowej<br>
    **Typ obiektu systemu podłączonego:** Użytkownika<br>
    **Typ obiektu Metaverse:** Osoba<br>
    **Typ łącza:** JoinNoFlow (Dołącz do łączyć się z przepływ<br>
    **Pierwszeństwo:** Podaj wartość, która jest unikatowa w systemie<br>
    **Tag:** Pozostaw to puste<br>
    
    ![Reguła niestandardowa](media/how-to-cloud-custom-user-rule/user6.png)</br>
 
 3. Na stronie **filtru zakres** wybierz **cloudNoFlow** równe **True**. Następnie kliknij przycisk **Dalej**.
 ![Reguła niestandardowa](media/how-to-cloud-custom-user-rule/user7.png)</br>
 
 4. Na stronie Reguły **dołączania** kliknij przycisk **Dalej**.
 5. Na stronie **Przekształcenia** kliknij pozycję **Dodaj**.

Te same kroki należy wykonać dla wszystkich typów obiektów (użytkownika, grupy i kontaktu).

## <a name="install-the-azure-ad-connect-provisioning-agent"></a>Instalowanie agenta inicjowania obsługi administracyjnej usługi Azure AD Connect
1. Zaloguj się na serwerze, którego będziesz używać z uprawnieniami administratora przedsiębiorstwa.  Jeśli używasz [samouczka środowiska podstawowej usługi AD i platformy Azure,](tutorial-basic-ad-azure.md) będzie to CP1.
2. Pobierz agenta inicjowania obsługi administracyjnej w chmurze usługi Azure AD Connect, wykonując kroki opisane [w tym miejscu](how-to-install.md#install-the-agent).
3. Uruchamianie inicjowania obsługi administracyjnej w chmurze usługi Azure AD Connect (AADConnectProvisioningAgent.Installer)
3. Na ekranie powityla **zaakceptuj** postanowienia licencyjne i kliknij przycisk **Zainstaluj**.</br>
![Ekran powitalny](media/how-to-install/install1.png)</br>

4. Po zakończeniu tej operacji zostanie uruchomiony kreator konfiguracji.  Zaloguj się za pomocą konta administratora globalnego usługi Azure AD.
5. Na ekranie **Połącz usługa Active Directory** kliknij pozycję **Dodaj katalog,** a następnie zaloguj się za pomocą konta administratora usługi Active Directory.  Ta operacja spowoduje dodanie katalogu lokalnego.  Kliknij przycisk **alej**.</br>
![Ekran powitalny](media/how-to-install/install3.png)</br>

6. Na ekranie **Konfiguracja pełna** kliknij przycisk **Potwierdź**.  Ta operacja zarejestruje i uruchomi ponownie agenta.</br>
![Ekran powitalny](media/how-to-install/install4.png)</br>

7. Po zakończeniu tej operacji powinieneś zobaczyć **powiadomienie, które zostało pomyślnie zweryfikowane.**  Możesz kliknąć przycisk **Zakończ**.</br>
![Ekran powitalny](media/how-to-install/install5.png)</br>
8. Jeśli nadal widzisz początkowy ekran powitalny, kliknij przycisk **Zamknij**.

## <a name="verify-agent-installation"></a>Weryfikowanie instalacji agenta
Weryfikacja agenta odbywa się w witrynie Azure portal i na serwerze lokalnym, który jest uruchomiony agenta.

### <a name="azure-portal-agent-verification"></a>Weryfikacja agenta usługi Azure portal
Aby sprawdzić, czy agent jest postrzegany przez platformę Azure, wykonaj następujące kroki:

1. Zaloguj się do Portalu Azure.
2. Po lewej stronie wybierz pozycję **Azure Active Directory**, kliknij pozycję **Azure AD Connect** i w centrum wybierz pozycję **Zarządzaj inicjowania obsługi administracyjnej (wersja zapoznawcza)**.</br>
![Azure Portal](media/how-to-install/install6.png)</br>

3.  Na ekranie **inicjowania obsługi administracyjnej usługi Azure AD (wersja zapoznawcza)** kliknij pozycję **Przejrzyj wszystkich agentów**.
![Inicjowanie obsługi administracyjnej usługi Azure AD](media/how-to-install/install7.png)</br>
 
4. Na **ekranie lokalnych agentów inicjowania obsługi administracyjnej** zobaczysz zainstalowane agentów.  Sprawdź, czy dany agent istnieje i jest oznaczony **jako Wyłączone**.  Agent jest domyślnie ![wyłączony przez agentów inicjowania obsługi administracyjnej](media/how-to-install/verify1.png)</br>

### <a name="on-the-local-server"></a>Na serwerze lokalnym
Aby sprawdzić, czy agent jest uruchomiony, wykonaj następujące kroki:

1.  Zaloguj się do serwera przy użyciu konta administratora
2.  Otwórz **usługi,** przechodząc do niego lub przechodząc do strony Start/Run/Services.msc.
3.  W obszarze **Usługi** upewnij się, że program **Microsoft Azure AD Connect Agent Updater** i agent **aprowizujący usługi Microsoft Azure AD Connect** są tam dostępne, a stan jest **uruchomiony**.
![Usługi](media/how-to-troubleshoot/troubleshoot1.png)

## <a name="configure-azure-ad-connect-cloud-provisioning"></a>Konfigurowanie inicjowania obsługi administracyjnej usługi Azure AD Connect
Aby skonfigurować inicjowanie obsługi administracyjnej, należy wykonać następujące czynności:

 1. Zaloguj się do portalu usługi Azure AD.
 2. Kliknij pozycję **Usługa Azure Active Directory**
 3. Kliknij pozycję **Usługa Azure AD Connect**
 4. Wybierz **pozycję Zarządzaj inicjowania obsługi administracyjnej (wersja zapoznawcza)**
 ![](media/how-to-configure/manage1.png)</br>
 5.  Kliknij **pozycję Nowa konfiguracja**
 ![](media/tutorial-single-forest/configure1.png)</br>
 6.  Na ekranie konfiguracji wprowadź **wiadomość e-mail z powiadomieniem**, przesuń selektor do **opcji Włącz** i kliknij przycisk **Zapisz**.
 ![](media/tutorial-single-forest/configure2.png)</br>
 7. W obszarze **Konfiguruj**wybierz pozycję **Wszyscy użytkownicy,** aby zmienić zakres reguły konfiguracji.
 ![](media/how-to-configure/scope2.png)</br>
 8. Po prawej stronie zmień zakres, aby uwzględnić określoną ou, które właśnie utworzono "OU = cpuers,DC=contoso,DC=com".
 ![](media/tutorial-existing-forest/scope2.png)</br>
 9.  Kliknij **pozycję Gotowe** i **zapisz**.
 10. Zakres powinien teraz być ustawiony na jedną jednostkę organizacyjną. 
 ![](media/tutorial-existing-forest/scope3.png)</br>
 

## <a name="verify-users-are-provisioned-by-cloud-provisioning"></a>Sprawdzanie, czy użytkownicy są aprowiedni przez aprowizacji w chmurze
Teraz sprawdzisz, czy użytkownicy, którzy mieli w naszym katalogu lokalnym zostały zsynchronizowane i teraz istnieją w obecnie dzierżawy usługi Azure AD.  Ukończenie tego procesu może potrwać kilka godzin.  Aby sprawdzić, czy użytkownicy inicjują inicjowanie obsługi administracyjnej przez aprowizacji w chmurze, wykonaj następujące kroki:

1. Przejdź do witryny [Azure Portal](https://portal.azure.com) i zaloguj się przy użyciu konta z subskrypcją platformy Azure.
2. W obszarze po lewej stronie wybierz pozycję **Azure Active Directory**.
3. Kliknij usługę **Azure AD Connect**
4. Kliknij **pozycję Zarządzaj inicjowania obsługi administracyjnej (wersja zapoznawcza)**
5. Kliknij przycisk **Dzienniki**
6. Wyszukaj nazwę użytkownika, aby potwierdzić, że użytkownik jest aprowidzony przez aprowiowanie w chmurze

Ponadto można sprawdzić, czy użytkownik i grupa istnieją w usłudze Azure AD.

## <a name="start-the-scheduler"></a>Uruchamianie harmonogramu
Synchronizacja usługi Azure AD Connect synchronizuje zmiany zachodzące w katalogu lokalnym przy użyciu harmonogramu. Teraz, gdy zostały zmodyfikowane reguły, można ponownie uruchomić harmonogram.  Wykonaj następujące czynności:

1.  Na serwerze z uruchomieniem synchronizacji usługi Azure AD Connect otwórz program PowerShell z uprawnieniami administracyjnymi
2.  Uruchom polecenie `Set-ADSyncScheduler -SyncCycleEnabled $true`.
3.  Uruchom polecenie `Start-ADSyncSyncCycle`.  Hit Enter.  

>[!NOTE] 
>Jeśli korzystasz z własnego harmonogramu niestandardowego dla synchronizacji AAD Connect, włącz harmonogram. 

## <a name="something-went-wrong"></a>Wystąpił błąd
W przypadku, gdy program pilotażowy nie działa zgodnie z oczekiwaniami, możesz wrócić do konfiguracji synchronizacji usługi Azure AD Connect, wykonując poniższe kroki:
1.  Wyłącz konfigurację inicjowania obsługi administracyjnej w witrynie Azure portal. 
2.  Wyłącz wszystkie niestandardowe reguły synchronizacji utworzone dla inicjowania obsługi administracyjnej w chmurze za pomocą narzędzia Edytor reguł synchronizacji. Wyłączenie powinno spowodować pełną synchronizację wszystkich łączników.

## <a name="configure-azure-ad-connect-sync-to-exclude-the-pilot-ou"></a>Konfigurowanie synchronizacji usługi Azure AD Connect w celu wykluczenia pilotażowej instalacji organizacyjnej
Po sprawdzeniu, że użytkownicy z jednostki organizacyjnej pilotażowej są pomyślnie zarządzane przez aprowizacji w chmurze, można ponownie skonfigurować usługi Azure AD Connect, aby wykluczyć jednostkę organizacyjną pilotażową, który został utworzony powyżej.  Agent inicjowania obsługi administracyjnej chmury będzie obsługiwać synchronizacji dla tych użytkowników w przyszłości.  Poniższe kroki obejmują usługę Azure AD Connect.

 1. Na serwerze z uruchomieniem usługi Azure AD Connect kliknij dwukrotnie ikonę usługi Azure AD Connect.
 2. Kliknij pozycję **Konfiguruj**.
 3. Wybierz **pozycję Dostosuj opcje synchronizacji** i kliknij przycisk Dalej.
 4. Zaloguj się do usługi Azure AD i kliknij przycisk **Dalej**.
 5. Na ekranie **Połącz katalogi** kliknij przycisk **Dalej**.
 6. Na ekranie **filtrowania domeny i usługi organizacyjnej** wybierz pozycję **Synchronizuj wybrane domeny i nazwy użytkownika**.
 7. Rozwiń swoją domenę i **odznacz** **procesory.**  Kliknij przycisk **alej**.
![Zakres](media/tutorial-existing-forest/scope1.png)</br>
 9. Na ekranie **Funkcje opcjonalne** kliknij przycisk **Dalej**.
 10. Na ekranie **Gotowe do skonfigurowania** kliknij pozycję **Konfiguruj**.
 11. Po zakończeniu kliknij przycisk **Zakończ**. 

## <a name="next-steps"></a>Następne kroki 

- [Co to jest aprowizacja?](what-is-provisioning.md)
- [Co to jest aprowizacja w chmurze programu Azure AD Connect?](what-is-cloud-provisioning.md)

