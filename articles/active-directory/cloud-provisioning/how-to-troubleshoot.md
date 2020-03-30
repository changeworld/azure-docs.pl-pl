---
title: Rozwiązywanie problemów z aprowisją w chmurze usługi Azure AD Connect
description: W tym artykule opisano sposób rozwiązywania problemów, które mogą pojawić się z agentem inicjowania obsługi administracyjnej w chmurze.
author: billmath
ms.author: billmath
manager: daveba
ms.date: 12/02/2019
ms.topic: article
ms.prod: windows-server-threshold
ms.technology: identity-adfs
ms.openlocfilehash: e41be4b76245f2567015eb0ede317830120ee61a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75549489"
---
# <a name="cloud-provisioning-troubleshooting"></a>Rozwiązywanie problemów z aprowisją w chmurze

Inicjowanie obsługi administracyjnej w chmurze dotyka wielu różnych rzeczy i ma wiele różnych zależności. Ten szeroki zakres może prowadzić do różnych problemów. Ten artykuł ułatwia rozwiązywanie tych problemów. Wprowadza typowe obszary, na których można się skupić, jak zbierać dodatkowe informacje i różne techniki, których można użyć do śledzenia problemów.


## <a name="common-troubleshooting-areas"></a>Typowe obszary rozwiązywania problemów

|Nazwa|Opis|
|-----|-----|
|[Problemy z agentem](#agent-problems)|Sprawdź, czy agent został poprawnie zainstalowany i czy komunikuje się z usługą Azure Active Directory (Azure AD).|
|[Problemy z synchronizacją obiektów](#object-synchronization-problems)|Użyj dzienników inicjowania obsługi administracyjnej, aby rozwiązać problemy z synchronizacją obiektów.|
|[Inicjowanie obsługi administracyjnej problemów z kwarantanną](#provisioning-quarantined-problems)|Poznaj problemy z analizą obsługi administracyjnej i sposób ich rozwiązywania.|


## <a name="agent-problems"></a>Problemy z agentem
Oto niektóre z pierwszych czynności, które chcesz zweryfikować za pomocą agenta:

-  Czy jest zainstalowany?
-  Czy agent działa lokalnie?
-  Czy agent w portalu?
-  Czy środek jest oznaczony jako zdrowy?

Te elementy można zweryfikować w witrynie Azure portal i na serwerze lokalnym, na który jest uruchomiony agent.

### <a name="azure-portal-agent-verification"></a>Weryfikacja agenta usługi Azure portal

Aby sprawdzić, czy agent jest widoczny dla platformy Azure i jest w dobrej kondycji, wykonaj następujące kroki.

1. Zaloguj się do Portalu Azure.
1. Po lewej stronie wybierz pozycję **Azure Active Directory** > **Azure AD Connect**. W centrum wybierz pozycję **Zarządzaj inicjowania obsługi administracyjnej (wersja zapoznawcza)**.
1. Na ekranie **inicjowania obsługi administracyjnej usługi Azure AD (wersja zapoznawcza)** wybierz pozycję **Przejrzyj wszystkich agentów**.

   ![Przejrzyj wszystkich agentów](media/how-to-install/install7.png)</br>
 
1. Na ekranie **lokalnych agentów inicjowania obsługi administracyjnej** są wyświetlane zainstalowane agenty. Sprawdź, czy dany agent istnieje i jest oznaczony *jako Zdrowy*.

   ![Ekran lokalnych agentów inicjowania obsługi administracyjnej](media/how-to-install/install8.png)</br>

### <a name="verify-the-port"></a>Sprawdź port

Aby sprawdzić, czy platforma Azure nasłuchuje na porcie 443 i czy agent może się z nią komunikować, użyj następującego narzędzia:

https://aadap-portcheck.connectorporttest.msappproxy.net/ 

Ten test sprawdza, czy agenci mogą komunikować się z platformą Azure za pomocą portu 443. Otwórz przeglądarkę i przejdź do poprzedniego adresu URL z serwera, na którym jest zainstalowany agent.

![Weryfikacja osiągów portu](media/how-to-install/verify2.png)

### <a name="on-the-local-server"></a>Na serwerze lokalnym

Aby sprawdzić, czy agent jest uruchomiony, wykonaj następujące kroki.

1. Na serwerze z zainstalowanym agentem otwórz **usługę,** przechodząc do niego lub przechodząc do **strony Uruchom** > **program** > **Services.msc**.
1. W obszarze **Usługi**upewnij się, że program **Microsoft Azure AD Connect Agent Updater** i agent **aprowizowania usługi Microsoft Azure AD Connect** są tam dostępne, a ich stan jest *uruchomiony*.

   ![Ekran usług](media/how-to-troubleshoot/troubleshoot1.png)

### <a name="common-agent-installation-problems"></a>Typowe problemy z instalacją agenta

W poniższych sekcjach opisano niektóre typowe problemy z instalacją agenta i typowe rozwiązania.

#### <a name="agent-failed-to-start"></a>Nie można uruchomić agenta

Może pojawić się komunikat o błędzie informujący:

**Nie można uruchomić usługi "Agent aprowizowania usługi Microsoft Azure AD Connect". Sprawdź, czy masz wystarczające uprawnienia do uruchamiania usług systemowych.** 

Ten problem jest zazwyczaj spowodowany przez zasady grupy, które uniemożliwiały stosowanie uprawnień do lokalnego konta logowania usługi NT utworzonego przez instalatora (NT SERVICE\AADConnectProvisioningAgent). Te uprawnienia są wymagane do uruchomienia usługi.

Aby rozwiązać ten problem, wykonaj następujące kroki.

1. Zaloguj się do serwera przy za pomocą konta administratora.
1. Otwórz **usługi,** przechodząc do niego lub przechodząc do **strony Uruchom** > **Run** > **plik Services.msc**.
1. W obszarze **Usługi**kliknij dwukrotnie pozycję **Agent aprowizowania usługi Microsoft Azure AD Connect**.
1. Na karcie **Logowanie** zmień **to konto** na administratora domeny. Następnie uruchom ponownie usługę. 

   ![Karta Logowanie](media/how-to-troubleshoot/troubleshoot3.png)

#### <a name="agent-times-out-or-certificate-is-invalid"></a>Upoświadcznik czasu agenta lub certyfikat jest nieprawidłowy

Podczas próby zarejestrowania agenta może zostać wyświetlony następujący komunikat o błędzie.

![Komunikat o błędzie przesuwu](media/how-to-troubleshoot/troubleshoot4.png)

Ten problem jest zwykle spowodowany przez agenta nie może połączyć się z usługą tożsamości hybrydowej i wymaga skonfigurowania serwera proxy HTTP. Aby rozwiązać ten problem, należy skonfigurować wychodzący serwer proxy. 

Agent inicjowania obsługi administracyjnej obsługuje korzystanie z wychodzącego serwera proxy. Można go skonfigurować, edytując plik konfiguracji agenta *C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\AADConnectProvisioningAgent.exe.config*. Dodaj do niego następujące wiersze, na końcu pliku `</configuration>` tuż przed tagiem zamknięcia.
Zastąp `[proxy-server]` `[proxy-port]` zmienne oraz nazwę serwera proxy i wartości portów.

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

Podczas instalowania agenta inicjowania obsługi administracyjnej w chmurze może zostać wyświetlony komunikat o błędzie.

Ten problem jest zazwyczaj spowodowane przez agenta nie można wykonać skryptów rejestracji programu PowerShell ze względu na lokalne zasady wykonywania programu PowerShell.

Aby rozwiązać ten problem, zmień zasady wykonywania programu PowerShell na serwerze. Musisz mieć zasady komputera i użytkownika ustawione jako *Niezdefiniowane* lub *RemoteSigned*. Jeśli są one ustawione jako *Nieograniczone*, zobaczysz ten błąd. Aby uzyskać więcej informacji, zobacz [zasady wykonywania programu PowerShell](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_execution_policies?view=powershell-6). 

### <a name="log-files"></a>Pliki dziennika

Domyślnie agent emituje minimalne komunikaty o błędach i informacje śledzenia stosu. Te dzienniki śledzenia można znaleźć w folderze *C:\ProgramData\Microsoft\Azure AD Connect Agent inicjowania obsługi administracyjnej\Śledź*.

Aby zebrać dodatkowe szczegóły dotyczące rozwiązywania problemów związanych z agentem, wykonaj następujące kroki.

1. Zatrzymaj usługę **Agent aprowizujący Usługi Microsoft Azure AD Connect**.
1. Utwórz kopię oryginalnego pliku konfiguracji: *C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\AADConnectProvisioningAgent.exe.config*.
1. Zastąp istniejącą `<system.diagnostics>` sekcję następującym, a wszystkie komunikaty śledzenia zostaną dodane do pliku *ProvAgentTrace.log*.

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
1. Uruchom usługę **Agent aprowizowania**usługi Microsoft Azure AD Connect .
1. Użyj następującego polecenia, aby zaostniu problemów z plikiem i debugowaniem. 
    ```
    Get-Content “C:/ProgramData/Microsoft/Azure AD Connect Provisioning Agent/Trace/ProvAgentTrace.log” -Wait
    ```
## <a name="object-synchronization-problems"></a>Problemy z synchronizacją obiektów

Poniższa sekcja zawiera informacje dotyczące rozwiązywania problemów z synchronizacją obiektów.

### <a name="provisioning-logs"></a>Dzienniki aprowizowania

W witrynie Azure portal dzienniki inicjowania obsługi administracyjnej mogą służyć do śledzenia i rozwiązywania problemów z synchronizacją obiektów. Aby wyświetlić dzienniki, wybierz pozycję **Dzienniki**.

![Przycisk Dzienniki](media/how-to-troubleshoot/log1.png)

Dzienniki inicjowania obsługi administracyjnej zapewniają wiele informacji na temat stanu obiektów synchronizowanych między lokalnym środowiskiem usługi Active Directory a platformą Azure.

![Ekran Dzienniki inicjowania obsługi administracyjnej](media/how-to-troubleshoot/log2.png)

Można użyć pól rozwijanych w górnej części strony, aby filtrować widok do zera w przypadku określonych problemów, takich jak daty. Kliknij dwukrotnie pojedyncze zdarzenie, aby wyświetlić dodatkowe informacje.

![Informacje o polu rozwijającym Dzienniki inicjowania obsługi administracyjnej](media/how-to-troubleshoot/log3.png)

Te informacje zawierają szczegółowe kroki i gdzie występuje problem synchronizacji. W ten sposób można wskazać dokładne miejsce problemu.


## <a name="provisioning-quarantined-problems"></a>Inicjowanie obsługi administracyjnej problemów z kwarantanną

Inicjowanie obsługi administracyjnej w chmurze monitoruje kondycję konfiguracji i umieszcza w złej kondycji obiektów w stanie kwarantanny. Jeśli większość lub wszystkie wywołania wykonane przeciwko systemowi docelowemu konsekwentnie nie powiedzie się z powodu błędu, na przykład nieprawidłowe poświadczenia administratora, zadanie inicjowania obsługi administracyjnej jest oznaczony jako w kwarantannie.

![Stan kwarantanny](media/how-to-troubleshoot/quarantine1.png)

Wybierając stan, można wyświetlić dodatkowe informacje o kwarantannie. Można również uzyskać kod błędu i komunikat.

![Informacje o stanie kwarantanny](media/how-to-troubleshoot/quarantine2.png)

### <a name="resolve-a-quarantine"></a>Rozwiązywanie kwarantanny

- Użyj witryny Azure portal, aby ponownie uruchomić zadanie inicjowania obsługi administracyjnej. Na stronie konfiguracji agenta wybierz pozycję **Uruchom ponownie aprowizacji**.

  ![Ponowne uruchamianie obsługi administracyjnej](media/how-to-troubleshoot/quarantine3.png)

- Użyj programu Microsoft Graph, aby [ponownie uruchomić zadanie inicjowania obsługi administracyjnej](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-restart?view=graph-rest-beta&tabs=http). Będziesz mieć pełną kontrolę nad tym, co ponownie uruchomić. Możesz wyczyścić:
  - Escrows, aby ponownie uruchomić licznik escrow, który jest naliczany do stanu kwarantanny.
  - Kwarantanna, aby usunąć aplikację z kwarantanny.
  - Znaki wodne. 
  
  Użyj następującego żądania:
 
  `POST /servicePrincipals/{id}/synchronization/jobs/{jobId}/restart`

## <a name="next-steps"></a>Następne kroki 

- [Co to jest aprowizacja?](what-is-provisioning.md)
- [Co to jest aprowizacja w chmurze programu Azure AD Connect?](what-is-cloud-provisioning.md)



