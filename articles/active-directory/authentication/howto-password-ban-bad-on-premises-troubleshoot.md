---
title: Rozwiązywanie problemów i rejestrowanie w usłudze Azure AD hasło ochrony w wersji zapoznawczej
description: Zrozumienie ochrony hasłem usługi Azure AD rejestrowania i rozwiązywania typowych problemów w wersji preview
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 06/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: jsimmons
ms.openlocfilehash: e5b3dc1bfa7c7890be83529e863907ec056f188f
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2018
ms.locfileid: "36295659"
---
# <a name="preview-azure-ad-password-protection-monitoring-reporting-and-troubleshooting"></a>Podgląd: Usługi Azure AD monitorowania ochrony hasłem, raportowanie i rozwiązywanie problemów

|     |
| --- |
| Ochrony hasłem w usłudze Azure AD i listy zabronionych hasła niestandardowego są funkcje publicznej wersji zapoznawczej usługi Azure Active Directory. Aby uzyskać więcej informacji na temat wersji zapoznawczych, zobacz [uzupełniające warunki użytkowania dotyczącym wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

Po wdrożeniu usługi Azure AD ochrony hasłem, monitorowania i raportowania są podstawowe zadania. W tym artykule przechodzi w stan szczegóły, aby ułatwić się, że rozumiesz, gdzie każda usługa rejestruje informacje i raportowaniu dotyczących korzystania z ochrony hasłem usługi Azure AD.

## <a name="on-premises-logs-and-events"></a>Lokalne dzienniki i zdarzenia

### <a name="dc-agent-service"></a>Usługa agenta z kontrolera domeny

Na każdym kontrolerze domeny, oprogramowanie agenta DC zapisuje wyniki jego poprawności hasła (i innych stanu) w lokalnym dzienniku zdarzeń: \Applications i Logs\Microsoft\AzureADPasswordProtection\DCAgent\Admin usług

Zdarzenia są rejestrowane przez różne składniki agenta kontrolera domeny przy użyciu następujących zakresów:

|Składnik |Zakres Identyfikatora zdarzenia|
| --- | --- |
|Biblioteki dll filtru haseł agenta z kontrolera domeny| 10000 19999|
|Proces hostingu usługi agenta DC| 20000 29999|
|Logika sprawdzania poprawności zasad usługi agenta DC| 30000 39999|

Operacji sprawdzania poprawności hasła pomyślnie jest zazwyczaj co zdarzenie jest rejestrowane z biblioteki dll filtru haseł agenta kontrolera domeny. Dla niepowodzenie operacji sprawdzania poprawności hasła, są zwykle dwa zdarzenia zarejestrowane: jeden z kontrolera domeny usługi agenta i jeden z biblioteki dll filtru haseł agenta z kontrolera domeny.

Odrębny do przechwycenia tych sytuacji rejestrowanych zdarzeń, na podstawie tych następujące czynniki:

* Określa, czy podane hasło jest ustawiona lub zmienione.
* Określa, czy sprawdzanie poprawności hasła podanego przekazany lub niepowodzenie.
* Określa, czy Weryfikacja nie powiodła się z powodu vs globalne zasady firmy Microsoft zasady organizacji.
* Określa, czy trybu tylko inspekcja jest obecnie lub Wyłącz na bieżące zasady haseł.

Klucza zdarzenia dotyczące sprawdzania poprawności hasła są następujące:

|   |Zmiana hasła |Ustawianie hasła|
| --- | :---: | :---: |
|Powodzenie |10014 |10015|
|Niepowodzenie (nie przeszedł klienta zasad haseł)| 10016, 30002| 10017, 30003|
|Niepowodzenie (nie przeszedł zasadami haseł firmy Microsoft)| 10016, 30004| 10017, 30005|
|Przebieg tylko do inspekcji (nie powiodłoby się zasady haseł klienta)| 10024, 30008| 10025, 30007|
|Przebieg tylko do inspekcji (nie powiodłoby się zasadami haseł firmy Microsoft)| 10024, 30010| 10025, 30009|

> [!TIP]
> Przychodzące hasła są sprawdzane na liście globalnego hasła Microsoft najpierw; nie dalsze przetwarzanie nie jest wykonywana w przypadku niepowodzenia. Jest to samo, jak wykonać na zmiany hasła w usłudze Azure.

#### <a name="sample-event-log-message-for-event-id-10014-successful-password-set"></a>Przykładowy komunikat dziennika zdarzeń dla zestawu 10014 hasło pomyślnie identyfikator zdarzenia

Zmiany hasła dla określonego użytkownika została zweryfikowana za zgodne z zasadami bieżącego hasła usługi Azure.

 Nazwa użytkownika: BPL_02885102771 imię i nazwisko:

#### <a name="sample-event-log-message-for-event-id-10017-and-30003-failed-password-set"></a>Przykładowy komunikat dziennika zdarzeń dla Identyfikatora zdarzenia 10017 i 30003 nie powiodło się ustawianie hasła

10017:

Zresetuj hasło dla określonego użytkownika zostało odrzucone, ponieważ nie był zgodny z regułą bieżącego hasła usługi Azure. Sprawdź komunikat skorelowany dziennik zdarzeń, aby uzyskać więcej informacji.

 Nazwa użytkownika: BPL_03283841185 imię i nazwisko:

30003:

Zresetuj hasło dla określonego użytkownika zostało odrzucone, ponieważ był zgodny z co najmniej jeden z tokenów występuje na liście hasła na dzierżawy zakazane bieżący zasady haseł platformy Azure.

 Nazwa użytkownika: BPL_03283841185 imię i nazwisko:

Niektóre inne kluczowe komunikatach w dzienniku zdarzeń należy pamiętać o są:

#### <a name="sample-event-log-message-for-event-id-30001"></a>Przykładowy komunikat dziennika zdarzeń dla Identyfikatora zdarzenia 30001

Hasło dla określonego użytkownika został zaakceptowany, ponieważ zasady haseł Azure nie jest jeszcze dostępna

Nazwa użytkownika: <user> imię i nazwisko: <user>

Ta sytuacja może być spowodowana przez jedną lub więcej z następujących powodów: % n

1. Lasu nie została jeszcze zarejestrowana na platformie Azure.

   Kroki rozwiązania: administrator musi zarejestrować lasu za pomocą polecenia cmdlet Register-AzureADPasswordProtectionForest.

2. Ochrona za pomocą usługi Azure AD hasła serwera Proxy nie jest jeszcze dostępna w co najmniej jedna maszyna w bieżącym lesie.

   Kroki rozwiązania: administrator, należy zainstalować i Zarejestruj serwer proxy przy użyciu polecenia cmdlet Register-AzureADPasswordProtectionProxy.

3. Ten kontroler domeny nie ma łączności sieciowej wystąpienia usługi Azure AD hasło ochrony serwera Proxy.

   Kroki rozwiązania: Upewnij się, istnieje połączenie sieciowe do co najmniej jednego wystąpienia serwera Proxy ochrony hasła usługi Azure AD.

4. Ten kontroler domeny nie ma łączności z innymi kontrolerami domeny w domenie.

   Kroki rozwiązania: Upewnij się, istnieje połączenie sieciowe do domeny.

#### <a name="sample-event-log-message-for-event-id-30006"></a>Przykładowy komunikat dziennika zdarzeń dla Identyfikatora zdarzenia 30006

Usługa jest teraz wymuszania następujące zasady haseł platformy Azure.

 AuditOnly: 1

 Globalne zasady Data: 2018-05-15T00:00:00.000000000Z

 Dzierżawy zasad daty: 2018-06-10T20:15:24.432457600Z

 Wymuszanie zasad dzierżawy: 1

#### <a name="dc-agent-log-locations"></a>Lokalizacje dziennika agenta z kontrolera domeny

Usługa agenta kontrolera domeny również będzie rejestrować zdarzenia operacyjne związane z w dzienniku następujące: \Applications i Logs\Microsoft\AzureADPasswordProtection\DCAgent\Operational usług

Usługa agenta kontrolera domeny także zalogować się następujące dziennika zdarzeń informacji pełnej śledzenia poziomu debugowania: \Applications i Logs\Microsoft\AzureADPasswordProtection\DCAgent\Trace usług

> [!WARNING]
> Dziennik śledzenia jest domyślnie wyłączona. Po włączeniu tego dziennika odbiera dużą liczbę zdarzeń i może mieć wpływ na wydajność kontrolera domeny. W związku z tym ten dziennik rozszerzony tylko powinno być włączone, gdy problem wymaga głębsza analiza, a następnie dla skraca czas procesu.

### <a name="azure-ad-password-protection-proxy-service"></a>Usługa Serwer proxy ochrony hasłem Azure AD

Minimalny zbiór zdarzenia w dzienniku zdarzeń następujące emituje ochrony hasłem usługi serwera Proxy: \Applications i Logs\Microsoft\AzureADPasswordProtection\ProxyService\Operational usług

Ochrony hasłem usługi serwera Proxy można również rejestrowanie pełne debugowania poziom śledzenia zdarzeń w dzienniku następujące: \Applications i Logs\Microsoft\AzureADPasswordProtection\ProxyService\Trace usług

> [!WARNING]
> Dziennik śledzenia jest domyślnie wyłączona. Po włączeniu tego dziennika odbiera dużą liczbę zdarzeń i to może mieć wpływ na wydajność hosta serwera proxy. W związku z tym ten dziennik tylko powinno być włączone, gdy problem wymaga głębsza analiza, a następnie dla skraca czas procesu.

### <a name="dc-agent-discovery"></a>Odnajdywanie agenta z kontrolera domeny

`Get-AzureADPasswordProtectionDCAgent` Polecenia cmdlet mogą służyć do wyświetlania podstawowych informacji o różnych agentów kontrolera domeny w domenie lub lesie. Te informacje są pobierane z obiektów serviceConnectionPoint zarejestrowane przez usług agenta uruchomionego kontrolera domeny. Przykładowe dane wyjściowe tego polecenia cmdlet wygląda następująco:

```
PS C:\> Get-AzureADPasswordProtectionDCAgent
ServerFQDN            : bplChildDC2.bplchild.bplRootDomain.com
Domain                : bplchild.bplRootDomain.com
Forest                : bplRootDomain.com
Heartbeat             : 2/16/2018 8:35:01 AM
```

Różne właściwości są aktualizowane przez usługę agenta każdego kontrolera domeny przybliżonej godzinę. Dane są nadal może ulec opóźnienie replikacji usługi Active Directory.

Zakres kwerendy polecenie cmdlet może wpływać za pomocą parametrów — lesie lub — w domenie.

### <a name="emergency-remediation"></a>Korygowanie awaryjnego

Jeśli niefortunne sytuacja występuje, gdy usługa agenta kontrolera domeny powoduje problemy, Usługa agenta kontrolera domeny może być natychmiast zamknięty. Dll filtru haseł agenta DC podejmuje próbę wywołania tej usługi nie działa, a będzie rejestrować zdarzenia ostrzegawcze (10012, 10013), ale wszystkie hasła przychodzące są akceptowane w tym czasie. Usługa agenta kontrolera domeny może następnie również skonfigurować za pomocą systemu Windows Menedżera sterowania usługami typu uruchamiania "Disabled" zgodnie z potrzebami.

### <a name="performance-monitoring"></a>Monitorowanie wydajności

Oprogramowanie agenta DC instaluje obiektu licznika wydajności o nazwie **ochrony hasłem usługi Azure AD**. Obecnie dostępne są następujące liczniki wydajności:

|Nazwa licznika wydajności | Opis|
| --- | --- |
|Hasła przetworzone |Ten licznik wyświetla całkowitą liczbę przetworzonych haseł (zaakceptowane lub odrzucone) od czasu ostatniego ponownego uruchomienia.|
|Zaakceptowane hasła |Ten licznik wskazuje całkowitą liczbę haseł, które zostały zaakceptowane od momentu ostatniego uruchomienia.|
|Hasła odrzucone |Ten licznik wskazuje całkowitą liczbę haseł, które zostały odrzucone, od momentu ostatniego uruchomienia.|
|Hasło Filtrowanie żądań w toku |Ten licznik wskazuje liczbę hasła Filtrowanie żądań aktualnie w toku.|
|Szczytowa hasła Filtrowanie żądań |Ten licznik wskazuje całkowitą liczbę równoczesnych hasła Filtrowanie żądań od momentu ostatniego uruchomienia.|
|Błędy żądania filtru hasła |Ten licznik wskazuje całkowitą liczbę hasła Filtrowanie żądań, których nie powiodła się z powodu błędu od momentu ostatniego uruchomienia. Mogą wystąpić błędy, kiedy nie jest uruchomiona usługa agenta ochrony DC hasła usługi Azure AD.|
|Hasło filtr żądania/s |Ten licznik wskazuje liczbę jaką hasła są przetwarzane.|
|Czas przetwarzania żądania filtru haseł |Ten licznik wyświetla średni czas wymagany do przetwarzania żądań filtru haseł.|
|Czas przetwarzania żądania filtru haseł godzinami szczytu |Ten licznik wskazuje czas od momentu ostatniego uruchomienia przetwarzania żądania filtru godzinami szczytu hasła.|
|Zaakceptowane z powodu trybu inspekcji hasła |Ten licznik wyświetla całkowitą liczbę haseł, zazwyczaj będzie zostało odrzucone, które zostały zaakceptowane, ponieważ zasady haseł zostało skonfigurowane będzie w trybie inspekcji (od momentu ostatniego ponownego uruchomienia).|

## <a name="directory-services-repair-mode"></a>Trybie naprawy usług katalogowych

Jeśli kontroler domeny jest uruchomiony w trybie naprawy usług katalogowych, Usługa agenta kontroler domeny wykrywa, to przyczyną wszystkich sprawdzenie poprawności hasła lub działań wymuszanie wyłączenia, niezależnie od aktualnie aktywnej zasady konfiguracji.

## <a name="domain-controller-demotion"></a>Obniżenia poziomu kontrolera domeny

Możliwe jest obniżenie poziomu kontrolera domeny, na którym jest nadal uruchomione oprogramowanie agenta kontrolera domeny. Administratorzy Pamiętaj jednak czy oprogramowanie agenta DC kontynuowanie działania i kontynuuje wymuszania bieżące zasady haseł podczas wykonywania procedury obniżania poziomu. Nowe hasło administratora lokalnego konta (określony jako część operacji obniżania poziomu) sprawdzania poprawności podobnie jak inne hasło. Firma Microsoft zaleca, można wybrać bezpiecznych haseł dla lokalnych kont administratorów w ramach procedury obniżania poziomu kontrolera domeny; Jednakże walidacji nowe hasło administratora lokalnego konta przez oprogramowanie pośredniczące kontrolera domeny może być znaczący wpływ na istniejące procedury operacyjne obniżania poziomu.
Po obniżania poziomu zakończyła się pomyślnie, a kontroler domeny zostanie ponownie uruchomiony i jest uruchomiona ponownie jako serwer członkowski normalne, oprogramowanie agenta DC wraca do uruchamiania w trybie pasywnym. Być może następnie odinstalowana w dowolnym momencie.

## <a name="removal"></a>Usunięcie

Jeśli podjęto decyzję o odinstalować oprogramowanie publicznej wersji zapoznawczej i oczyszczania stan wszystkich powiązanych z domeny i lasu, to zadanie można wykonywać, wykonując następujące kroki:

> [!IMPORTANT]
> Należy wykonać następujące kroki w kolejności. Jeśli dowolne wystąpienie ochrony hasłem usługi serwera Proxy jest uruchomienie jej będzie okresowo ponownego tworzenia własnego obiektu serviceConnectionPoint jak również okresowo ponownie utworzyć stanu folderu sysvol.

1. Odinstaluj ochrony hasłem oprogramowanie serwera Proxy z wszystkich maszyn. Ten krok jest **nie** wymaga ponownego uruchomienia.
2. Odinstaluj oprogramowanie agenta kontroler domeny ze wszystkich kontrolerów domeny. Ten krok **wymaga** ponowne uruchomienie komputera.
3. Ręcznie usuń wszystkie punkty połączenia usługi serwera proxy w każdym kontekst nazewnictwa domeny. Lokalizacja tych obiektów są wykrywane przy użyciu następującego polecenia środowiska Powershell usługi Active Directory:
   ```
   $scp = “serviceConnectionPoint”
   $keywords = “{EBEFB703-6113-413D-9167-9F8DD4D24468}*”
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -eq $keywords }
   ```

   Nie pominąć gwiazdki ("*") na końcu $keywords wartość zmiennej.

   Obiekt wynikowy znalezione przez `Get-ADObject` polecenia można następnie przekazywany w potoku do `Remove-ADObject`, lub ręcznie usunięty. 

4. Ręcznie usuń wszystkie punkty połączenia agenta kontrolera domeny w każdym kontekst nazewnictwa domeny. Może istnieć jedna tych obiektów na kontrolerze domeny w lesie, w zależności od tego, jak bardzo publicznej wersji zapoznawczej oprogramowania została wdrożona. Lokalizacja tego obiektu są wykrywane przy użyciu następującego polecenia środowiska Powershell usługi Active Directory:

   ```
   $scp = “serviceConnectionPoint”
   $keywords = “{B11BB10A-3E7D-4D37-A4C3-51DE9D0F77C9}*”
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -eq $keywords }
   ```

   Obiekt wynikowy znalezione przez `Get-ADObject` polecenia można następnie przekazywany w potoku do `Remove-ADObject`, lub ręcznie usunięty.

5. Ręcznie usuń stan konfiguracji na poziomie lasu. Stan konfiguracji lasu jest zachowywana w kontenerze w kontekście nazewnictwa konfiguracji w usłudze Active Directory. Można go odnalezione i usunięte w następujący sposób:

   ```
   $passwordProtectonConfigContainer = "CN=Azure AD password protection,CN=Services," + (Get-ADRootDSE).configurationNamingContext
   Remove-ADObject $passwordProtectonConfigContainer
   ```

6. Ręcznie usuń wszystkie sysvol związane z programem stanu przez ręczne usuwanie następujący folder i całą jego zawartość:

   `\\<domain>\sysvol\<domain fqdn>\Policies\{4A9AB66B-4365-4C2A-996C-58ED9927332D}`

   W razie potrzeby tę ścieżkę można również uzyskać dostęp lokalnie na danego kontrolera domeny; Domyślna lokalizacja będzie przypominać następującą ścieżkę:

   `%windir%\sysvol\domain\Policies\{4A9AB66B-4365-4C2A-996C-58ED9927332D}`

   Ta ścieżka jest inny, jeśli skonfigurowano udziału sysvol w lokalizacji innej niż domyślna.

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na listach globalne i niestandardowych zabronione hasła, zobacz artykuł [zakaz błędnego hasła](concept-password-ban-bad.md)
