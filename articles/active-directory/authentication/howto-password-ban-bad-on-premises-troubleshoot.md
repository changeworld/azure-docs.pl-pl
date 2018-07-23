---
title: Rozwiązywanie problemów i rejestrowanie w wersji zapoznawczej ochrony haseł usługi Azure AD
description: Zrozumienie ochrona za pomocą hasła usługi Azure AD w wersji zapoznawczej rejestrowanie i rozwiązywanie typowych problemów
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: jsimmons
ms.openlocfilehash: 1eea6380d4276644db0c7681f23a4b0c5e79ff09
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/20/2018
ms.locfileid: "39187353"
---
# <a name="preview-azure-ad-password-protection-monitoring-reporting-and-troubleshooting"></a>Wersja zapoznawcza: Usługi Azure AD monitorowania ochrony hasłem, raportowanie i rozwiązywania problemów

|     |
| --- |
| Ochrony hasłem w usłudze Azure AD i listy niestandardowej zakazanych haseł są publicznej wersji zapoznawczej funkcji usługi Azure Active Directory. Aby uzyskać więcej informacji na temat wersji zapoznawczych, zobacz [dodatkowym warunkom użytkowania wersji zapoznawczych usług Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

Po wdrożeniu ochrona za pomocą hasła usługi Azure AD z monitorowaniem i raportowaniem są podstawowe zadania. W tym artykule przechodzi do szczegółów, aby pomóc, że rozumiesz, gdzie każda usługa rejestruje informacje i instrukcje sporządzić raport na temat użytkowania ochrona za pomocą hasła usługi Azure AD.

## <a name="on-premises-logs-and-events"></a>W środowisku lokalnym dzienników i zdarzeń

### <a name="dc-agent-service"></a>Usługa agenta kontrolera domeny

Na każdym kontrolerze domeny DC oprogramowani usługi zapisuje wyniki jego poprawności hasła (i inne stanu) w lokalnym dzienniku zdarzeń: \Applications i Logs\Microsoft\AzureADPasswordProtection\DCAgent\Admin usług

Zdarzenia są rejestrowane przez różne składniki agenta kontrolera domeny przy użyciu następujących zakresów:

|Składnik |Zakres identyfikatorów zdarzeń|
| --- | --- |
|Biblioteki dll filtru haseł agenta kontrolera domeny| 10000 19999|
|Proces hostingu usługi agenta kontrolera domeny| 20000 29999|
|Logikę walidacji zasad usługi agenta kontrolera domeny| 30000 39999|

Dla operacji sprawdzania poprawności hasła pomyślne jest zazwyczaj jedno zdarzenie zarejestrowane z dll filtru haseł agenta kontrolera domeny. Na potrzeby przechodzenia operacji sprawdzania poprawności hasła, są zwykle dwa zdarzenia zarejestrowane: jeden z usługi agenta kontrolera domeny i jeden z dll filtru haseł agenta kontrolera domeny.

Dyskretne zdarzenia do przechwycenia tych sytuacji są rejestrowane na podstawie następujących czynników:

* Czy jest podanym hasłem ustawił lub zmienił.
* Czy sprawdzanie poprawności hasła podanego zakończony powodzeniem lub niepowodzeniem.
* Czy Weryfikacja nie powiodła się z powodu Microsoft vs zasady globalne zasady organizacji.
* Czy trybu tylko inspekcja jest aktualnie włączone czy wyłączone dla bieżących zasad haseł.

Kluczowe informacje o zdarzeniach powiązanych sprawdzania poprawności hasła są następujące:

|   |Zmiana hasła |Ustawianie hasła|
| --- | :---: | :---: |
|Powodzenie |10014 |10015|
|Niepowodzenie (nie przeszedł klienta zasady haseł)| 10016, 30002| 10017, 30003|
|Niepowodzenie (nie przeszedł zasadami haseł firmy Microsoft)| 10016, 30004| 10017, 30005|
|Tylko inspekcja — dostęp próbny (nie powiodłoby się zasady haseł klienta)| 10024, 30008| 10025, 30007|
|Tylko inspekcja — dostęp próbny (nie powiodłoby się zasadami haseł firmy Microsoft)| 10024, 30010| 10025, 30009|

> [!TIP]
> Przychodzące hasła są sprawdzane Microsoft hasło globalne listy najpierw; w przypadku niepowodzenia nie dalsze przetwarzanie odbywa się. Jest to takie samo zachowanie, ponieważ jest wykonywane na zmiany hasła w systemie Azure.

#### <a name="sample-event-log-message-for-event-id-10014-successful-password-set"></a>Przykładowy komunikat dziennika zdarzeń dla zestawu 10014 hasło pomyślne identyfikator zdarzenia

Zmiany hasła dla określonego użytkownika został zweryfikowany jako zgodny z bieżącym zasadami haseł usługi Azure.

 Nazwa użytkownika: BPL_02885102771 imię i nazwisko:

#### <a name="sample-event-log-message-for-event-id-10017-and-30003-failed-password-set"></a>Przykładowy komunikat dziennika zdarzeń dla Identyfikatora zdarzenia 10017 i 30003 nie powiodło się ustawianie hasła

10017:

Resetuj hasło dla określonego użytkownika zostało odrzucone, ponieważ nie był zgodny ze bieżące zasady haseł w usłudze Azure. Zobacz komunikat skorelowany dziennik zdarzeń, aby uzyskać więcej informacji.

 Nazwa użytkownika: BPL_03283841185 imię i nazwisko:

30003:

Resetuj hasło dla określonego użytkownika zostało odrzucone, ponieważ był zgodny z co najmniej jeden z tokenów występuje na liście haseł na dzierżawie zakaz dostępu do bieżących zasad haseł usługi Azure.

 Nazwa użytkownika: BPL_03283841185 imię i nazwisko:

Niektóre inne kluczowe komunikatach w dzienniku zdarzeń pod uwagę są:

#### <a name="sample-event-log-message-for-event-id-30001"></a>Przykładowy komunikat dziennika zdarzeń dla Identyfikatora zdarzenia 30001

Hasło dla określonego użytkownika został zaakceptowany, ponieważ zasady haseł w usłudze Azure nie jest jeszcze dostępna

Nazwa użytkownika: <user> imię i nazwisko: <user>

Ten stan może być spowodowane przez co najmniej jeden z następujących powodów: % n

1. Las nie została jeszcze zarejestrowana przy użyciu platformy Azure.

   Kroki rozwiązania: administrator musi zarejestrować lasu za pomocą polecenia cmdlet Register-AzureADPasswordProtectionForest.

2. Ochrona za pomocą hasła usługi Azure AD serwera Proxy nie jest jeszcze dostępna w co najmniej jedna maszyna w bieżącym lesie.

   Kroki rozwiązania: administrator musi zainstalować i zarejestrować serwer proxy przy użyciu polecenia cmdlet Register-AzureADPasswordProtectionProxy.

3. Ten kontroler domeny nie ma łączności sieciowej do żadnych wystąpień serwera Proxy ochrony haseł usługi Azure AD.

   Kroki rozwiązania: Sprawdź istnieje połączenie sieciowe do co najmniej jedno wystąpienie serwera Proxy ochrony haseł usługi Azure AD.

4. Ten kontroler domeny nie ma łączności z innymi kontrolerami domeny w domenie.

   Kroki rozwiązania: Sprawdź istnieje połączenie sieciowe z domeną.

#### <a name="sample-event-log-message-for-event-id-30006"></a>Przykładowy komunikat dziennika zdarzeń dla Identyfikatora zdarzenia 30006

Usługa jest teraz wymuszania następujące zasady haseł w usłudze Azure.

 AuditOnly: 1

 Globalne zasady daty: 2018-05-15T00:00:00.000000000Z

 Dzierżawy Data zasad: 2018-06-10T20:15:24.432457600Z

 Wymuszanie zasad dzierżawy: 1

#### <a name="dc-agent-log-locations"></a>Lokalizacje dziennika agenta kontrolera domeny

Usługa agenta DC zarejestruje także zdarzenia operacyjne związane z następujących dziennik: \Applications i Logs\Microsoft\AzureADPasswordProtection\DCAgent\Operational usług

Usługa agenta kontrolera domeny można także rejestrować zdarzenia śledzenia pełne poziom debugowania następujący dziennik: \Applications i Logs\Microsoft\AzureADPasswordProtection\DCAgent\Trace usług

> [!WARNING]
> W dzienniku śledzenia jest domyślnie wyłączona. Po włączeniu tego dziennika odbiera dużej liczby zdarzeń i może mieć wpływ na wydajność kontrolera domeny. W związku z tym, ten rozszerzony dziennik tylko powinno być włączone, gdy problem wymaga głębszego zbadania, a następnie dla skraca czas procesu.

### <a name="azure-ad-password-protection-proxy-service"></a>Hasło ochrony serwera proxy usługi programu Azure AD

Ochrona za pomocą hasła usługi serwera Proxy emituje to minimalny zestaw zdarzeń w dzienniku zdarzeń następujące: \Applications i Logs\Microsoft\AzureADPasswordProtection\ProxyService\Operational usług

Ochrona za pomocą hasła usługi serwera Proxy można także rejestrować zdarzenia pełnego śledzenia na poziomie debugowania następujący dziennik: \Applications i Logs\Microsoft\AzureADPasswordProtection\ProxyService\Trace usług

> [!WARNING]
> W dzienniku śledzenia jest domyślnie wyłączona. Po włączeniu tego dziennika odbiera dużej liczby zdarzeń i może to mieć wpływ na wydajność hosta serwera proxy. W związku z tym, ten dziennik tylko powinno być włączone, gdy problem wymaga głębszego zbadania, a następnie dla skraca czas procesu.

### <a name="dc-agent-discovery"></a>Odnajdywanie agenta kontrolera domeny

`Get-AzureADPasswordProtectionDCAgent` Polecenia cmdlet mogą służyć do wyświetlania podstawowych informacji o różnych agentów kontrolera domeny w domenie lub lesie. Te informacje są pobierane z obiektów serviceConnectionPoint zarejestrowane przez uruchamianie usług agenta kontrolera domeny. Przykładowe dane wyjściowe tego polecenia cmdlet jest następująca:

```
PS C:\> Get-AzureADPasswordProtectionDCAgent
ServerFQDN            : bplChildDC2.bplchild.bplRootDomain.com
Domain                : bplchild.bplRootDomain.com
Forest                : bplRootDomain.com
Heartbeat             : 2/16/2018 8:35:01 AM
```

Różne właściwości są aktualizowane przez poszczególne usługi agenta kontrolera domeny w systemie godzinowym przybliżone. Dane są nadal podlega procesowi opóźnienie replikacji usługi Active Directory.

Zakres kwerendy polecenie cmdlet może wpływać przy użyciu parametrów — domenie lub lesie —.

### <a name="emergency-remediation"></a>Korygowanie awaryjnego

Jeśli niefortunne sytuacja występuje, gdy usługa agenta kontrolera domeny powoduje problemy, Usługa agenta kontrolera domeny może być natychmiast zamknięty. Dll filtru haseł agenta kontrolera domeny, próbuje wywołać bez działającej usługi i będzie rejestrować zdarzenia ostrzeżeń (10012, 10013), ale wszystkie przychodzące hasła są akceptowane w tym samym czasie. Usługa agenta kontrolera domeny może także można skonfigurować za pomocą Windows Menedżer sterowania usługami za pomocą typu uruchamiania "Disabled" zgodnie z potrzebami.

### <a name="performance-monitoring"></a>Monitorowanie wydajności

Oprogramowanie usługi agenta DC instaluje obiektu licznika wydajności, o nazwie **ochrona za pomocą hasła usługi Azure AD**. Obecnie dostępne są następujące liczniki wydajności:

|Nazwa licznika wydajności | Opis|
| --- | --- |
|Przetworzone hasła |Ten licznik wskazuje całkowitą liczbę haseł przetwarzane (zaakceptowane lub odrzucone) od momentu ostatniego uruchomienia.|
|Zaakceptowane hasła |Ten licznik wskazuje całkowitą liczbę haseł, które zostały zaakceptowane od momentu ostatniego uruchomienia.|
|Hasła odrzucone |Ten licznik wskazuje całkowitą liczbę haseł, które zostały odrzucone od momentu ostatniego uruchomienia.|
|Hasło Filtrowanie żądań w toku |Ten licznik wskazuje liczbę żądań filtru haseł obecnie w toku.|
|Szczytowy hasło Filtrowanie żądań |Ten licznik przedstawia szczytową liczbę jednoczesnych hasło Filtrowanie żądań od momentu ostatniego uruchomienia.|
|Błędy żądań filtru haseł |Ten licznik wyświetla całkowitą liczbę żądań filtru haseł, które nie powiodło się z powodu błędu od momentu ostatniego uruchomienia. Mogą wystąpić błędy, kiedy nie jest uruchomiona usługa agenta ochrony kontrolera domeny hasła usługi Azure AD.|
|Filtr haseł żądania/s |Ten licznik wyświetla szybkość jaką są przetwarzane hasła.|
|Czas przetwarzania żądania filtru haseł |Ten licznik wyświetla średni czas wymagany do przetwarzania żądań filtru haseł.|
|Czas przetwarzania żądania filtru haseł szczytu |Ten licznik wyświetla żądanie filtru hasła szczytu przetwarzania czasu od momentu ostatniego uruchomienia.|
|Hasła są akceptowane z powodu tryb inspekcji |Ten licznik wskazuje całkowitą liczbę haseł, zwykle będzie został odrzucony, które zostały zaakceptowane, ponieważ zasady haseł został skonfigurowany w trybie inspekcji (od momentu ostatniego uruchomienia).|

## <a name="directory-services-repair-mode"></a>Trybie naprawy usług katalogowych

Jeśli kontroler domeny jest uruchomiony w trybie naprawy usług katalogowych, Usługa agenta kontrolera domeny wykrywa to powoduje wszystkich sprawdzenie poprawności hasła lub działań wykonawczych, można wyłączyć niezależnie od aktualnie aktywne zasady konfiguracji.

## <a name="domain-controller-demotion"></a>Obniżenia poziomu kontrolera domeny

Możliwe jest obniżenie poziomu kontrolera domeny, który wciąż działa oprogramowanie agenta kontrolera domeny. Administratorzy Pamiętaj jednak, oprogramowanie agenta kontrolera domeny jest uruchomiony i jest kontynuowane wymuszanie bieżące zasady haseł podczas wykonywania procedury obniżania poziomu. Nowe hasło administratora lokalnego konta (określonego jako część operacji obniżania poziomu) jest weryfikowana, podobnie jak inne hasło. Firma Microsoft zaleca, można wybrać bezpiecznych haseł dla konta administratora lokalnego w ramach procedury obniżania poziomu kontrolera domeny; Jednakże weryfikacji nowe hasło administratora lokalnego konta przez oprogramowanie agenta kontrolera domeny może być znaczący wpływ na istniejące procedury operacyjne obniżania poziomu.
Po obniżania poziomu zakończyła się pomyślnie, a kontroler domeny zostanie ponownie uruchomiony i jest ponownie uruchomiony jako serwer członkowski normalne, przywraca uruchomiony w trybie pasywnym oprogramowanie agenta kontrolera domeny. Może on następnie odinstalowano w dowolnym momencie.

## <a name="removal"></a>Usunięcie

Jeśli zostanie podjęta decyzja, aby odinstalować oprogramowanie w publicznej wersji zapoznawczej i czyszczenia stan wszystkich powiązanych z domeny i lasu, to zadanie można osiągnąć wykonując następujące czynności:

> [!IMPORTANT]
> Należy wykonać następujące kroki w kolejności. Jeśli pole pozostanie dowolne wystąpienie ochrony hasłem usługi serwera Proxy uruchomieniu będzie okresowo ponownie utworzyć jej obiektu serviceConnectionPoint także okresowo ponownie utworzyć stan sysvol.

1. Odinstaluj ochrony hasłem oprogramowanie serwera Proxy z wszystkich maszyn. Ten krok jest **nie** konieczne jest ponowne uruchomienie.
2. Odinstaluj oprogramowanie agenta kontrolera domeny ze wszystkich kontrolerów domeny. W tym kroku **wymaga** ponowne uruchomienie komputera.
3. Ręcznie usuń wszystkie punkty połączenia usługi serwera proxy w każdym kontekście nazewnictwa domeny. Może zostać odnalezionych lokalizacji tych obiektów za pomocą następującego polecenia środowiska Powershell usługi Active Directory:
   ```
   $scp = “serviceConnectionPoint”
   $keywords = “{EBEFB703-6113-413D-9167-9F8DD4D24468}*”
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -like $keywords }
   ```

   Nie pominięto gwiazdki ("*") na końcu $keywords wartość zmiennej.

   Wynikowy obiekt znalezione przez `Get-ADObject` polecenia następnie mogą być przesyłane potokiem do `Remove-ADObject`, lub usunąć ręcznie. 

4. Ręcznie usuń wszystkie punkty połączenia agenta kontrolera domeny w każdym kontekście nazewnictwa domeny. Może istnieć tylko jeden tych obiektów na kontrolerze domeny w lesie, w zależności od tego, jak powszechnie prapremiery publicznej została wdrożona. Lokalizacja tego obiektu może zostać odnalezionych za pomocą następującego polecenia środowiska Powershell usługi Active Directory:

   ```
   $scp = “serviceConnectionPoint”
   $keywords = “{B11BB10A-3E7D-4D37-A4C3-51DE9D0F77C9}*”
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -like $keywords }
   ```

   Wynikowy obiekt znalezione przez `Get-ADObject` polecenia następnie mogą być przesyłane potokiem do `Remove-ADObject`, lub usunąć ręcznie.

5. Ręcznie usuń stan konfiguracji na poziomie lasu. Stan konfiguracji lasu jest zachowywana w kontenerze w kontekście nazewnictwa konfiguracji w usłudze Active Directory. Można go odnalezione i usunięte w następujący sposób:

   ```
   $passwordProtectonConfigContainer = "CN=Azure AD password protection,CN=Services," + (Get-ADRootDSE).configurationNamingContext
   Remove-ADObject $passwordProtectonConfigContainer
   ```

6. Ręcznie usuń wszystkie sysvol związane stanu przez ręczne usuwanie następujący folder i całą jego zawartość:

   `\\<domain>\sysvol\<domain fqdn>\Policies\{4A9AB66B-4365-4C2A-996C-58ED9927332D}`

   W razie potrzeby tę ścieżkę można również uzyskać dostęp lokalnie na kontrolerze danej domeny; Domyślna lokalizacja będzie podobny do następującej ścieżki:

   `%windir%\sysvol\domain\Policies\{4A9AB66B-4365-4C2A-996C-58ED9927332D}`

   Ta ścieżka jest inny, jeśli skonfigurowano udziału sysvol, w lokalizacji innej niż domyślna.

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat list globalnych i niestandardowych zakazanych haseł, zobacz artykuł [Zablokuj błędnego hasła](concept-password-ban-bad.md)
