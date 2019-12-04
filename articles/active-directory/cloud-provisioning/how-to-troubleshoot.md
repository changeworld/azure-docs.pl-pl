---
title: Azure AD Connect Rozwiązywanie problemów z obsługą chmury
description: W tym dokumencie opisano sposób rozwiązywania problemów, które mogą wystąpić w przypadku agenta aprowizacji w chmurze.
author: billmath
ms.author: billmath
manager: daveba
ms.date: 12/02/2019
ms.topic: article
ms.prod: windows-server-threshold
ms.technology: identity-adfs
ms.openlocfilehash: 4100886e0a24fa961b9085bd507ae3f4ebfdd6eb
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74794239"
---
# <a name="cloud-provisioning-troubleshooting"></a>Rozwiązywanie problemów z obsługą chmury

Obsługa administracyjna w chmurze styka się z wieloma różnymi elementami i ma wiele różnych zależności.  W naturalny sposób może to spowodować różne problemy.  Ten dokument został zaprojektowany w celu rozpoczęcia rozwiązywania tych problemów.  W tym dokumencie przedstawiono typowe obszary, na których należy się skoncentrować, jak zbierać dodatkowe informacje i różne techniki, których można użyć do śledzenia problemów.  


## <a name="common-troubleshooting-areas"></a>Typowe obszary rozwiązywania problemów

|Nazwa|Opis|
|-----|-----|
|[Problemy z agentem](#agent-issues)|Sprawdź, czy Agent został poprawnie zainstalowany i czy komunikuje się z usługą Azure AD.|
|[Problemy z synchronizacją obiektów](#object-synchronization-issues)|Dzienniki aprowizacji umożliwiają rozwiązywanie problemów z synchronizacją obiektów.|
|[Inicjowanie obsługi administracyjnej problemów z kwarantanną](#provisioning-quarantined-issues)|Opis problemu z kwarantanną i sposobu jego rozwiązania.|


## <a name="agent-issues"></a>Problemy z agentem
Poniżej wymieniono niektóre z nich, które mają być zweryfikowane przez agenta:


-  Czy jest zainstalowana?
-  czy agent działa lokalnie?
-  czy Agent jest w portalu?
-  czy Agent jest oznaczony jako w dobrej kondycji?  

Te elementy można zweryfikować w Azure Portal i na serwerze lokalnym, na którym jest uruchomiony Agent programu.

### <a name="azure-portal-agent-verification"></a>Weryfikacja agenta Azure Portal

Aby zweryfikować, że Agent jest widziany przez platformę Azure i jest w dobrej kondycji, wykonaj następujące czynności:

1. Zaloguj się do Portalu Azure.
2. Po lewej stronie wybierz pozycję **Azure Active Directory**, kliknij pozycję **Azure AD Connect** i w centrum wybierz pozycję **Zarządzaj Provisioning (wersja zapoznawcza)** .
3.  Na ekranie **Azure AD Provisioning (wersja zapoznawcza)** kliknij pozycję **Przejrzyj wszystkich agentów**.
 ![](media/how-to-install/install7.png) aprowizacji usługi Azure AD</br>
 
4. Na **ekranie agenci aprowizacji lokalnego** zostaną zainstalowani agenci.  Sprawdź, czy dany Agent jest tam i jest oznaczony jako w **dobrej kondycji**.
 ![agentów aprowizacji](media/how-to-install/install8.png)</br>

### <a name="verify-the-port"></a>Weryfikowanie portu

Aby sprawdzić, czy platforma Azure nasłuchuje na porcie 443 i czy Agent może się z nim komunikować, możesz użyć następującego narzędzia:

https://aadap-portcheck.connectorporttest.msappproxy.net/ 

Ten test sprawdzi, czy agenci mogą komunikować się z platformą Azure przez port 443.  Otwórz przeglądarkę i przejdź do powyższego adresu URL z serwera, na którym Agent jest zainstalowany.
 ![Usługi](media/how-to-install/verify2.png)

### <a name="on-the-local-server"></a>Na serwerze lokalnym

Aby sprawdzić, czy agent działa, wykonaj następujące kroki:

1.  Na serwerze z zainstalowanym agentem Otwórz **usługi** , przechodząc do niego lub uruchamiając polecenie Start/Run/Services. msc.
2.  W obszarze **usługi**upewnij się, że **Microsoft Azure AD Connect agent Aktualizator** i **Microsoft Azure AD Connect Agent aprowizacji** jest tam, a stan jest **uruchomiony**.
 ![Usługi](media/how-to-troubleshoot/troubleshoot1.png)

### <a name="common-agent-installation-issues"></a>Typowe problemy z instalacją agenta

Poniżej przedstawiono niektóre typowe problemy z instalacją agentów oraz opis typowej rozdzielczości.

#### <a name="agent-failed-to-start"></a>Uruchomienie agenta nie powiodło się

Jeśli zostanie wyświetlony komunikat o błędzie informujący o tym, że:

**Nie można uruchomić usługi "Microsfoft Azure AD Connect Agent aprowizacji".  Sprawdź, czy masz wystarczające uprawnienia do uruchamiania usług systemowych.** 

Zwykle jest to spowodowane przez zasady grupy, które uniemożliwiły zastosowanie uprawnień do lokalnej usługi NT "konto logowania" utworzone przez Instalatora (NT SERVICE\AADConnectProvisioningAgent) te uprawnienia są wymagane do uruchomienia usługi.

Aby rozwiązać ten problem, wykonaj następujące czynności:

1.  Zaloguj się na serwerze przy użyciu konta administratora
2.  Otwórz **usługi** , przechodząc do niej lub uruchamiając/Uruchom/Services. msc.
3.  W obszarze **usługi** kliknij dwukrotnie pozycję **Microsoft Azure AD Połącz agenta aprowizacji**
4. Na karcie Zmień wartość "konto logowania" na administrator domeny i ponownie uruchom usługę. 

 ![Usługi](media/how-to-troubleshoot/troubleshoot3.png)

#### <a name="agent-times-out-or-certificate-is-invalid"></a>Limit czasu agenta lub nieprawidłowy certyfikat

Jeśli próbujesz zarejestrować agenta, mogą pojawić się następujące błędy.

 ![Usługi](media/how-to-troubleshoot/troubleshoot4.png)

Zwykle jest to spowodowane tym, że Agent nie może nawiązać połączenia z usługą tożsamości hybrydowej i wymaga konfiguracji serwera proxy HTTP.  Aby rozwiązać ten problem, skonfiguruj wychodzący serwer proxy. 

Agent aprowizacji obsługuje korzystanie z serwera proxy wychodzącego. Można ją skonfigurować, edytując plik konfiguracji agenta **C:\Program Files\Microsoft Azure AD Connect aprowizacji Agent\AADConnectProvisioningAgent.exe.config**. Dodaj do niego następujące wiersze przed końcem pliku tuż przed tagiem zamykającym `</configuration>`.
Zastąp zmienne [Proxy-Server] i [proxy-port] wartościami nazw i portów serwera proxy.

```xml
    <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
            <proxy
                usesystemdefault="true"
                proxyaddress="http://[proxy-server]:[proxy-port]"
                bypassonlocal="true"
            />
        </defaultProxy>
    </system.net>
```

#### <a name="agent-registration-fails-with-security-error"></a>Rejestracja agenta kończy się niepowodzeniem z powodu błędu zabezpieczeń

Podczas instalacji agenta aprowizacji w chmurze może zostać wyświetlony następujący błąd.

Zwykle jest to spowodowane tym, że Agent nie może wykonać skryptów rejestracji programu PowerShell z powodu lokalnych zasad wykonywania programu PowerShell.

Aby rozwiązać ten problem, Zmień zasady wykonywania programu PowerShell na serwerze. Zasady dotyczące maszyn i użytkowników muszą mieć wartość "undefined" lub "RemoteSigned". Jeśli ma wartość "unstricted", zostanie wyświetlony ten błąd.  Aby uzyskać więcej informacji, zobacz [zasady wykonywania programu PowerShell](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_execution_policies?view=powershell-6). 

### <a name="log-files"></a>Pliki dziennika

Domyślnie agent emituje bardzo minimalne komunikaty o błędach i informacje śledzenia stosu. Te dzienniki śledzenia można znaleźć w folderze: **C:\ProgramData\Microsoft\Azure usługi AD Connect Provisioning Agent\Trace**

Wykonaj następujące kroki, aby zebrać dodatkowe szczegóły dotyczące rozwiązywania problemów związanych z agentem.

1. Zatrzymaj usługę **Microsoft Azure AD Połącz agenta aprowizacji**
2. Utwórz kopię oryginalnego pliku konfiguracji: **C:\Program Files\Microsoft Azure AD Connect aprowizacji Agent\AADConnectProvisioningAgent.exe.config**
3. Zastąp istniejące < System. Diagnostics > następującym i wszystkie komunikaty śledzenia zostaną umieszczone w pliku **ProvAgentTrace. log**

      ```xml
        <system.diagnostics>
            <sources>
            <source name="AAD Connect Provisioning Agent">
                <listeners>
                <add name="console"/>
                <add name="etw"/>
                <add name="textWriterListener"/>
                </listeners>
            </source>
            </sources>
            <sharedListeners>
            <add name="console" type="System.Diagnostics.ConsoleTraceListener" initializeData="false"/>
            <add name="etw" type="System.Diagnostics.EventLogTraceListener" initializeData="Azure AD Connect Provisioning Agent">
                <filter type="System.Diagnostics.EventTypeFilter" initializeData="All"/>
            </add>
            <add name="textWriterListener" type="System.Diagnostics.TextWriterTraceListener" initializeData="C:/ProgramData/Microsoft/Azure AD Connect Provisioning Agent/Trace/ProvAgentTrace.log"/>
            </sharedListeners>
        </system.diagnostics>

      ```
4. Uruchom usługę Service **Microsoft Azure AD Connect Agent aprowizacji**
5. Poniższe polecenie służy do naśladowania problemów z plikiem i debugowaniem: 
    ```
    Get-Content “C:/ProgramData/Microsoft/Azure AD Connect Provisioning Agent/Trace/ProvAgentTrace.log” -Wait
    ```
## <a name="object-synchronization-issues"></a>Problemy z synchronizacją obiektów

Poniższa sekcja zawiera informacje dotyczące rozwiązywania problemów z synchronizacją obiektów.

### <a name="provisioning-logs"></a>Dzienniki aprowizowania

W Azure Portal dzienniki aprowizacji mogą pomóc w śledzeniu i rozwiązywaniu problemów z synchronizacją obiektów.  Aby wyświetlić dzienniki, wybierz pozycję **dzienniki**.
 ![dzienniki aprowizacji](media/how-to-troubleshoot/log1.png)

Dzienniki aprowizacji zapewniają mnóstwo informacji na temat stanu obiektów synchronizowanych między lokalnym środowiskiem usługi AD i platformą Azure.

 ![Dzienniki aprowizowania](media/how-to-troubleshoot/log2.png)

Możesz użyć listy rozwijanej w górnej części strony, aby przefiltrować widok do zera w określonych problemach, datach itd.  Dwukrotne kliknięcie pojedynczego zdarzenia spowoduje dostarczenie dodatkowych szczegółowych informacji.

 ![Dzienniki aprowizowania](media/how-to-troubleshoot/log3.png)

Te informacje dostarczają szczegółowych czynności i miejsca, w którym występuje problem z synchronizacją.  W ten sposób, co pozwala na zero w i wypróbować dokładne miejsce problemu.


## <a name="provisioning-quarantined-issues"></a>Inicjowanie obsługi administracyjnej problemów z kwarantanną

Inicjowanie obsługi administracyjnej w chmurze służy do monitorowania kondycji konfiguracji i umieszczania obiektów w złej kondycji w stanie "Kwarantanna". Jeśli większość lub wszystkie wywołania związane z systemem docelowym są spójne niepowodzeniem z powodu błędu, na przykład nieprawidłowe poświadczenia administratora, zadanie aprowizacji zostanie oznaczone jako w kwarantannie.

 ![Magazynu](media/how-to-troubleshoot/quarantine1.png)

Klikając stan, można wyświetlić dodatkowe informacje o kwarantannie.  Możesz również uzyskać kod błędu i komunikat.

 ![Magazynu](media/how-to-troubleshoot/quarantine2.png)

### <a name="resolving-a-quarantine"></a>Rozpoznawanie kwarantanny

- Użyj Azure Portal, aby ponownie uruchomić zadanie aprowizacji. Na stronie Konfiguracja agenta wybierz pozycję **Uruchom ponownie Inicjowanie obsługi**.

  ![Magazynu](media/how-to-troubleshoot/quarantine3.png)

- Użyj Microsoft Graph, aby [ponownie uruchomić zadanie aprowizacji](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-restart?view=graph-rest-beta&tabs=http). Będziesz mieć pełną kontrolę nad tym, co zostało ponownie uruchomione. Możesz wybrać opcję wyczyszczenia usługi Escrow (aby ponownie uruchomić licznik Escrow, który naliczy na status kwarantanny), wyczyścić opcję kwarantanny (w celu usunięcia aplikacji z kwarantanny) lub wyczyścić znaki wodne. Użyj następującego żądania:
 
  `POST /servicePrincipals/{id}/synchronization/jobs/{jobId}/restart`

## <a name="next-steps"></a>Następne kroki 

- [Co to jest inicjowanie obsługi?](what-is-provisioning.md)
- [Co to jest Azure AD Connect aprowizacji w chmurze?](what-is-cloud-provisioning.md)



