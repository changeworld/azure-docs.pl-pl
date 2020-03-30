---
title: Często zadawane pytania dotyczące kondycji połączenia usługi Azure Active Directory — Azure | Dokumenty firmy Microsoft
description: To często zadawane pytania dotyczy pytań dotyczących usługi Azure AD Connect Health. Wśród często zadawanych pytań znajdują się pytania dotyczące korzystania z samej usługi, w tym między innymi na temat modelu rozliczeń, możliwości, ograniczeń i pomocy technicznej.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.assetid: f1b851aa-54d7-4cb4-8f5c-60680e2ce866
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 07/18/2017
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: f0c6484f46731e0ff2d16d00cb0038202511d193
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80331076"
---
# <a name="azure-ad-connect-health-frequently-asked-questions"></a>Często zadawane pytania dotyczące usługi Azure AD Connect Health
Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące kondycji connect usługi Azure Active Directory (Azure AD). Te często zadawane pytania obejmują pytania dotyczące korzystania z usługi, która obejmuje model rozliczeń, możliwości, ograniczenia i pomoc techniczną.

## <a name="general-questions"></a>Pytania ogólne
**P: Zarządzam wieloma katalogami usługi Azure AD. Jak przełączyć się na tę, która ma usługę Azure Active Directory Premium?**

Aby przełączać się między różnymi dzierżawami usługi Azure AD, wybierz aktualnie zalogowaną **nazwę użytkownika** w prawym górnym rogu, a następnie wybierz odpowiednie konto. Jeśli konta nie ma na liście, wybierz pozycję **Wyloguj się,** a następnie użyj poświadczeń administratora globalnego katalogu, w który ma włączoną usługę Azure Active Directory Premium, aby się zalogować.

**Pyt.: Jaka wersja ról tożsamości są obsługiwane przez usługę Azure AD Connect Health?**

W poniższej tabeli wymieniono role i obsługiwane wersje systemu operacyjnego.

|Rola| System operacyjny / Wersja|
|--|--|
|Active Directory Federation Services (AD FS)| <ul> <li> Windows Server 2008 R2 </li><li> Windows Server 2012  </li> <li>Windows Server 2012 R2 </li> <li> Windows Server 2016  </li> </ul>|
|Azure AD Connect | Wersja 1.0.9125 lub nowsza|
|Usługi domenowe Active Directory (AD DS)| <ul> <li> Windows Server 2008 R2 </li><li> Windows Server 2012  </li> <li>Windows Server 2012 R2 </li> <li> Windows Server 2016  </li> </ul>|

Należy zauważyć, że funkcje udostępniane przez usługę mogą się różnić w zależności od roli i systemu operacyjnego. Innymi słowy, wszystkie funkcje mogą nie być dostępne dla wszystkich wersji systemu operacyjnego. Szczegółowe informacje można znaleźć w opisach funkcji.

**Pyt.: Ile licencji jest potrzebne do monitorowania mojej infrastruktury?**

* Pierwszy agent kondycji Connect wymaga co najmniej jednej licencji usługi Azure AD Premium.
* Każdy dodatkowy zarejestrowany agent wymaga 25 dodatkowych licencji usługi Azure AD Premium.
* Liczba agentów jest równoważna całkowitej liczbie agentów, które są zarejestrowane we wszystkich monitorowanych ról (AD FS, Azure AD Connect i/lub AD DS).
* Licencjonowanie AAD Connect Health nie wymaga przypisania licencji określonym użytkownikom. Wystarczy mieć wymagany numer ważnych licencji.

Informacje o licencjonowaniu można również znaleźć na [stronie Cennik usługi Azure AD](https://aka.ms/aadpricing).

Przykład:

| Zarejestrowani agenci | Potrzebne licencje | Przykładowa konfiguracja monitorowania |
| ------ | --------------- | --- |
| 1 | 1 | 1 Serwer usługi Azure AD Connect |
| 2 | 26| 1 serwer usługi Azure AD Connect i 1 kontroler domeny |
| 3 | 51 | 1 Serwer usług federacyjnych Active Directory (AD FS), 1 serwer proxy usług AD FS i 1 kontroler domeny |
| 4 | 76 | 1 serwer usług AD FS, 1 serwer proxy usług AD FS i 2 kontrolery domeny |
| 5 | 101 | 1 serwer usługi Azure AD Connect, 1 serwer usług AD FS, 1 serwer proxy usług AD FS i 2 kontrolery domeny |

**Pyt.: Czy usługa Azure AD Connect Health obsługuje usługę Azure Germany Cloud?**

Usługa Azure AD Connect Health nie jest obsługiwana w chmurze Germany Cloud, z wyjątkiem [funkcji raportu o błędach synchronizacji](how-to-connect-health-sync.md#object-level-synchronization-error-report).

| Role | Funkcje | Obsługiwane w niemieckiej chmurze |
| ------ | --------------- | --- |
| Połącz kondycję dla synchronizacji | Monitoring / Wgląd / Alerty / Analiza | Nie |
|  | Raport o błędzie synchronizacji | Tak |
| Połącz kondycję dla usługi ADFS | Monitoring / Wgląd / Alerty / Analiza | Nie |
| Połącz kondycję dla ADDS | Monitoring / Wgląd / Alerty / Analiza | Nie |

Aby zapewnić łączność agenta Connect Health do synchronizacji, należy odpowiednio skonfigurować [wymagania dotyczące instalacji.](how-to-connect-health-agent-install.md#outbound-connectivity-to-the-azure-service-endpoints)

## <a name="installation-questions"></a>Pytania dotyczące instalacji

**Pyt.: Jaki jest wpływ instalowania agenta kondycji usługi Azure AD Connect na poszczególnych serwerach?**

Wpływ instalowania agenta kondycji usługi Microsoft Azure AD Connect, usług AD FS, serwerów proxy aplikacji sieci Web, serwerów usługi Azure AD Connect (synchronizacji), kontrolerów domeny jest minimalny w odniesieniu do procesora CPU, zużycia pamięci, przepustowości sieci i miejsca do magazynowania.

Następujące liczby są przybliżeniem:

* Zużycie procesora: ~1-5% wzrost.
* Zużycie pamięci: Do 10% całkowitej pamięci systemowej.

> [!NOTE]
> Jeśli agent nie może komunikować się z platformą Azure, agent przechowuje dane lokalnie dla zdefiniowanego limitu maksymalnego. Agent zastępuje "buforowane" dane na zasadzie "ostatnio serwisowane".
>
>

* Magazyn buforu lokalnego dla agentów kondycji usługi Azure AD Connect: ~20 MB.
* W przypadku serwerów usług AD FS zaleca się aprowizowanie miejsca na dysku o masie 1024 MB (1 GB) dla kanału inspekcji usług AD FS dla agentów kondycji usługi Azure AD Connect w celu przetworzenia wszystkich danych inspekcji przed ich nadpisaniem.

**Pyt.: Czy podczas instalacji agentów kondycji usługi Azure AD Connect będzie musiał ponownie uruchomić serwery?**

Nie. Instalacja agentów nie będzie wymagać ponownego uruchomienia serwera. Jednak instalacja niektórych kroków wymagania wstępne może wymagać ponownego uruchomienia serwera.

Na przykład w systemie Windows Server 2008 R2 instalacja programu .NET 4.5 Framework wymaga ponownego uruchomienia serwera.

**Pyt.: Czy usługa Azure AD Connect Health działa za pośrednictwem serwera proxy HTTP przekazu?**

Tak. W przypadku bieżących operacji można skonfigurować agenta kondycji do używania serwera proxy HTTP do przesyłania dalej wychodzących żądań HTTP.
Dowiedz się więcej o [konfigurowaniu serwera proxy HTTP dla agentów kondycji](how-to-connect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy).

Jeśli konieczne jest skonfigurowanie serwera proxy podczas rejestracji agenta, może być konieczne uprzednie zmodyfikowanie ustawień serwera proxy programu Internet Explorer.

1. Otwórz program Internet Explorer > **Ustawienia opcje** > **internetowe** > **Ustawienia** > sieci LAN **.**
2. Wybierz **pozycję Użyj serwera proxy dla sieci LAN**.
3. Wybierz **opcję Zaawansowane,** jeśli masz różne porty proxy dla protokołu HTTP i HTTPS/Secure.

**Pyt.: Czy kondycja usługi Azure AD Connect obsługuje uwierzytelnianie podstawowe podczas łączenia się z serwerami proxy HTTP?**

Nie. Mechanizm określania dowolnej nazwy użytkownika i hasła do uwierzytelniania podstawowego nie jest obecnie obsługiwany.

**Pyt.: Jakie porty zapory muszę otworzyć, aby agent kondycji usługi Azure AD Connect działał?**

Zobacz [sekcję wymagania,](how-to-connect-health-agent-install.md#requirements) aby uzyskać listę portów zapory i innych wymagań dotyczących łączności.

**Pyt.: Dlaczego w portalu usługi Azure AD Connect Health są widoczne dwa serwery o tej samej nazwie?**

Po usunięciu agenta z serwera serwer nie jest automatycznie usuwany z portalu usługi Azure AD Connect Health. Jeśli ręcznie usuniesz agenta z serwera lub usuniesz sam serwer, musisz ręcznie usunąć wpis serwera z portalu usługi Azure AD Connect Health.

Można ponownie zamówić serwer lub utworzyć nowy serwer z tymi samymi szczegółami (takimi jak nazwa komputera). Jeśli nie usuniesz już zarejestrowanego serwera z portalu usługi Azure AD Connect Health i zainstalowano agenta na nowym serwerze, mogą zostać wyświetlene dwa wpisy o tej samej nazwie.

W takim przypadku ręcznie usuń wpis, który należy do starszego serwera. Dane dla tego serwera powinny być nieaktualne.

## <a name="health-agent-registration-and-data-freshness"></a>Rejestracja agenta zdrowia i świeżość danych

**Pyt.: Jakie są typowe przyczyny błędów rejestracji agenta kondycji i jak rozwiązywać problemy?**

Agent zdrowia może nie zarejestrować się z następujących możliwych przyczyn:

* Agent nie może komunikować się z wymaganymi punktami końcowymi, ponieważ zapora blokuje ruch. Jest to szczególnie powszechne na serwerach proxy aplikacji sieci web. Upewnij się, że dozwolono komunikację wychodzącą z wymaganymi punktami końcowymi i portami. Szczegółowe informacje można znaleźć w [sekcji Wymagania.](how-to-connect-health-agent-install.md#requirements)
* Komunikacja wychodząca jest poddawana inspekcji TLS przez warstwę sieciową. Powoduje to, że certyfikat, który używa agenta, które mają zostać zastąpione przez serwer inspekcji/jednostki, a kroki, aby zakończyć rejestrację agenta nie powiedzie się.
* Użytkownik nie ma dostępu do wykonywania rejestracji agenta. Administratorzy globalni mają domyślny dostęp. Za pomocą [kontroli dostępu opartej na rolach](how-to-connect-health-operations.md#manage-access-with-role-based-access-control) można delegować dostęp do innych użytkowników.

**P: Otrzymuję powiadomienie, że "dane usługi kondycji nie są aktualne". Jak rozwiązać ten problem?**

Usługa Azure AD Connect Health generuje alert, gdy nie odbiera wszystkich punktów danych z serwera w ciągu ostatnich dwóch godzin. [Dowiedz się więcej](how-to-connect-health-data-freshness.md).

## <a name="operations-questions"></a>Pytania operacyjne
**Pyt.: Czy muszę włączyć inspekcję na serwerach proxy aplikacji sieci web?**

Nie, inspekcja nie musi być włączona na serwerach proxy aplikacji sieci web.

**Pyt.: Jak alerty kondycji usługi Azure AD Connect są rozwiązywane?**

Alerty usługi Azure AD Connect Health są rozwiązywane w warunkach powodzenia. Agenci kondycji usługi Azure AD Connect okresowo wykrywają i zgłaszają warunki powodzenia do usługi. W przypadku kilku alertów tłumienie jest oparte na czasie. Innymi słowy, jeśli ten sam warunek błędu nie jest przestrzegany w ciągu 72 godzin od generowania alertu, alert jest automatycznie rozpoznawany.

**Pyt.: Otrzymuję alerty, że "Żądanie uwierzytelniania testowego (transakcja syntetyczna) nie może uzyskać tokenu." Jak rozwiązać ten problem?**

Kondycja usługi Azure AD Connect dla usług AD FS generuje ten alert, gdy agent kondycji zainstalowany na serwerze usług AD FS nie może uzyskać tokenu w ramach syntetycznej transakcji zainicjowanej przez agenta kondycji. Agent kondycji używa kontekstu systemu lokalnego i próbuje uzyskać token dla jednostki samozaliczającej. Jest to test typu catch-all, aby upewnić się, że usługi AD FS są w stanie wystawiania tokenów.

Najczęściej ten test kończy się niepowodzeniem, ponieważ agent kondycji nie jest w stanie rozpoznać nazwy farmy usług AD FS. Może się tak zdarzyć, jeśli serwery usług AD FS znajdują się za modułami równoważenia obciążenia sieciowego, a żądanie zostanie zainicjowane z węzła, który znajduje się za modułem równoważenia obciążenia (w przeciwieństwie do zwykłego klienta, który znajduje się przed modułem równoważenia obciążenia). Można to naprawić, aktualizując plik "hosts" znajdujący się w obszarze "C:\Windows\System32\drivers\etc", aby uwzględnić adres IP serwera USŁUG AD FS lub adres IP sprzężenia zwrotnego (127.0.0.1) dla nazwy farmy usług AD FS (na przykład sts.contoso.com). Dodanie pliku hosta spowoduje zwarcie wywołania sieciowego, umożliwiając w ten sposób agentowi kondycji uzyskanie tokenu.

**P: Dostałem wiadomość e-mail z informacją, że moje maszyny NIE są załatane do ostatnich ataków ransomware. Dlaczego otrzymałem ten e-mail?**

Usługa Azure AD Connect Health zeskanowała wszystkie monitory, aby upewnić się, że zainstalowano wymagane poprawki. Wiadomość e-mail została wysłana do administratorów dzierżawy, jeśli co najmniej jeden komputer nie miał krytycznych poprawek. Do dokonania tego ustalenia użyto następującej logiki.
1. Znajdź wszystkie poprawki zainstalowane na urządzeniu.
2. Sprawdź, czy co najmniej jeden z poprawek z listy zdefiniowanej jest obecny.
3. Jeśli tak, urządzenie jest chronione. Jeśli nie, maszyna jest zagrożona atakiem.

Do wykonania tego wyboru można użyć następującego skryptu programu PowerShell, aby wykonać tę kontrolę ręcznie. Implementuje powyższą logikę.

```powershell
Function CheckForMS17-010 ()
{
    $hotfixes = "KB3205409", "KB3210720", "KB3210721", "KB3212646", "KB3213986", "KB4012212", "KB4012213", "KB4012214", "KB4012215", "KB4012216", "KB4012217", "KB4012218", "KB4012220", "KB4012598", "KB4012606", "KB4013198", "KB4013389", "KB4013429", "KB4015217", "KB4015438", "KB4015546", "KB4015547", "KB4015548", "KB4015549", "KB4015550", "KB4015551", "KB4015552", "KB4015553", "KB4015554", "KB4016635", "KB4019213", "KB4019214", "KB4019215", "KB4019216", "KB4019263", "KB4019264", "KB4019472", "KB4015221", "KB4019474", "KB4015219", "KB4019473"

    #checks the computer it's run on if any of the listed hotfixes are present
    $hotfix = Get-HotFix -ComputerName $env:computername | Where-Object {$hotfixes -contains $_.HotfixID} | Select-Object -property "HotFixID"

    #confirms whether hotfix is found or not
    if (Get-HotFix | Where-Object {$hotfixes -contains $_.HotfixID})
    {
        "Found HotFix: " + $hotfix.HotFixID
    } else {
        "Didn't Find HotFix"
    }
}

CheckForMS17-010

```

**Pyt.: Dlaczego polecenie cmdlet programu PowerShell <i>Get-MsolDirSyncProvisioningError</i> pokazuje mniej błędów synchronizacji w wyniku?**

<i>Get-MsolDirSyncProvisioningError</i> zwróci tylko błędy inicjowania obsługi administracyjnej programu DirSync. Poza tym portal Connect Health pokazuje również inne typy błędów synchronizacji, takie jak błędy eksportu. Jest to zgodne z wynikiem delta usługi Azure AD Connect. Dowiedz się więcej o [błędach usługi Azure AD Connect Sync](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-sync-errors).

**P: Dlaczego moje audyty usługi ADFS nie są generowane?**

Użyj polecenia cmdlet programu PowerShell <i>Get-AdfsProperties -AuditLevel,</i> aby upewnić się, że dzienniki inspekcji nie są w stanie wyłączonym. Dowiedz się więcej o [dziennikach inspekcji usługi ADFS](https://docs.microsoft.com/windows-server/identity/ad-fs/technical-reference/auditing-enhancements-to-ad-fs-in-windows-server#auditing-levels-in-ad-fs-for-windows-server-2016). Należy zauważyć, że w przypadku zaawansowanych ustawień inspekcji wypychanych na serwer usługi ADFS wszelkie zmiany w pliku auditpol.exe zostaną zastąpione (zdarzenie, jeśli aplikacja generated nie jest skonfigurowana). W takim przypadku należy ustawić lokalne zasady zabezpieczeń, aby rejestrować błędy i powodzenie generowane przez aplikację.

**Pyt.: Kiedy certyfikat agenta zostanie automatycznie odnowiony przed wygaśnięciem?**
Certyfikacja agenta zostanie automatycznie odnowiona **na 6 miesięcy** przed datą jego wygaśnięcia. Jeśli nie jest odnawiany, upewnij się, że połączenie sieciowe agenta jest stabilne. Uruchom ponownie usługi agenta lub zaktualizuj do najnowszej wersji może również rozwiązać problem.


## <a name="related-links"></a>Powiązane linki
* [Azure AD Connect Health](whatis-hybrid-identity-health.md)
* [Instalacja agenta kondycji usługi Azure AD Connect](how-to-connect-health-agent-install.md)
* [Operacje w programie Azure AD Connect Health](how-to-connect-health-operations.md)
* [Używanie programu Azure AD Connect Health z usługami AD FS](how-to-connect-health-adfs.md)
* [Używanie usługi Azure AD Connect Health do synchronizacji](how-to-connect-health-sync.md)
* [Używanie programu Azure AD Connect Health z usługami AD DS](how-to-connect-health-adds.md)
* [Historia wersji programu Azure AD Connect Health](reference-connect-health-version-history.md)
