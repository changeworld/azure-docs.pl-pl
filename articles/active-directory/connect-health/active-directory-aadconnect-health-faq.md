---
title: Azure Active Directory Connect Health — często zadawane pytania - Azure | Dokumentacja firmy Microsoft
description: Często zadawane pytania odpowiedzi na pytania dotyczące usługi Azure AD Connect Health. Wśród często zadawanych pytań znajdują się pytania dotyczące korzystania z samej usługi, w tym między innymi na temat modelu rozliczeń, możliwości, ograniczeń i pomocy technicznej.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: curtand
ms.assetid: f1b851aa-54d7-4cb4-8f5c-60680e2ce866
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: billmath
ms.openlocfilehash: b4c977b54c87276bd4b168bc56e70f3b918e6634
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
---
# <a name="azure-ad-connect-health-frequently-asked-questions"></a>Azure AD Connect Health — często zadawane pytania
Ten artykuł zawiera odpowiedzi na często zadawane pytania (FAQ) dotyczące usługi Azure Active Directory (Azure AD) Connect Health. Te często zadawane pytania dotyczące pokrycia pytania dotyczące sposobu korzystania z usługi, w tym modelu rozliczeń możliwości, ograniczeń i pomocy technicznej.

## <a name="general-questions"></a>Pytania ogólne
**Pytanie: czy mogę zarządzać wielu katalogów usługi Azure AD. Jak przełączyć na jeden z usługi Azure Active Directory — wersja Premium**

Aby przełączyć między różnymi dzierżaw usługi Azure AD, wybierz aktualnie zalogowanego **nazwy użytkownika** w prawym górnym rogu, a następnie wybierz odpowiednie konto. Jeśli konto nie ma na liście, wybierz **Wyloguj**, a następnie użyj poświadczeń administratora globalnego katalogu, który ma Azure Active Directory Premium włączone do logowania.

**Pytanie: jakie wersji role są obsługiwane przez program Azure AD Connect Health tożsamości?**

Poniższa tabela zawiera listę ról i obsługiwanych wersjach systemu operacyjnego.

|Rola| System operacyjny / wersji|
|--|--|
|Usługi federacyjne Active Directory (AD FS)| <ul> <li> Windows Server 2008 R2 </li><li> Windows Server 2012  </li> <li>Windows Server 2012 R2 </li> <li> Windows Server 2016  </li> </ul>|
|Program Azure AD Connect | Wersja 1.0.9125 lub nowszej|
|Usługi domenowe Active Directory (AD DS)| <ul> <li> Windows Server 2008 R2 </li><li> Windows Server 2012  </li> <li>Windows Server 2012 R2 </li> <li> Windows Server 2016  </li> </ul>|

Należy pamiętać, że funkcje oferowane przez usługę mogą się różnić na podstawie roli i systemu operacyjnego. Innymi słowy wszystkie funkcje mogą być dostępne dla wszystkich wersji systemu operacyjnego. Zobacz opis funkcji, aby uzyskać szczegółowe informacje.

**Pytanie: jak liczby licencji należy monitorować Moje infrastruktury?**

* Pierwszy połączyć agenta programu Health wymaga co najmniej jedną licencję usługi Azure AD Premium.
* Każdy agent, dodatkowe zarejestrowane wymaga 25 dodatkowe licencje usługi Azure AD Premium.
* Liczba agentów jest odpowiednikiem łączna liczba agentów, które są zarejestrowane przez wszystkie role monitorowanych (usług AD FS, Azure AD Connect lub usług AD DS).
* Licencjonowanie usługi AAD Connect Health nie wymaga przypisania licencji do określonych użytkowników. Musisz mieć wymagane liczbę ważnych licencji.

Informacje o licencji znajduje się także na [strony cennik usługi Azure AD](https://aka.ms/aadpricing).

Przykład:

| Zarejestrowany agentów | Potrzebnych licencji | Przykładowa konfiguracja monitorowania |
| ------ | --------------- | --- |
| 1 | 1 | 1 serwer azure AD Connect |
| 2 | 26| 1 serwera azure AD Connect i kontrolera domeny 1 |
| 3 | 51 | 1 serwer usługi active Directory Federation Services (AD FS), 1, AD FS z serwera proxy i kontrolera domeny 1 |
| 4 | 76 | Serwera 1 AD FS, 1, AD FS z serwera proxy i 2 kontrolerów domeny |
| 5 | 101 | 1 serwer azure AD Connect, serwera 1 AD FS 1 AD FS z serwera proxy i 2 kontrolerów domeny |

**Pytanie: czy obsługa usługi Azure AD Connect Health chmury Niemcy Azure?**

Azure AD Connect Health ma [instalacji](active-directory-aadconnect-health-agent-install.md) dla platformy Azure w Niemczech. Wszystkie dane dla klientów, niemiecki chmury jest przechowywana w chmurze Niemcy Azure.


## <a name="installation-questions"></a>Pytania dotyczące instalacji

**Pytanie: co to jest wpływ Instalowanie agenta programu Azure AD Connect Health na poszczególnych serwerach?**

Wpływ Instalowanie agenta programu Microsoft Azure AD Connect Health, usługi AD FS, serwery proxy aplikacji sieci web, Azure AD Connect (synchronizacja) serwery kontrolerów domeny jest minimalny względem Procesora, wykorzystanie pamięci, przepustowości sieci i magazynu.

Następujące numery są zbliżenia:

* Użycie procesora CPU: Zwiększ ~ 1-5%.
* Zużycie pamięci: maksymalnie 10% całkowitej pamięci systemu.

> [!NOTE]
> Jeśli agent nie może komunikować się z platformą Azure, agent magazynuje dane lokalnie zdefiniowany maksymalny limit. Agent zastępuje "" buforowane na zasadzie "najdawniej obsługiwane".
>
>

* Bufor lokalnego magazynu dla usługi Azure AD Connect agentów kondycji: ~ 20 MB.
* W przypadku serwerów usług AD FS firma Microsoft zaleca obsługi administracyjnej miejsca 1024 MB (1 GB) dla kanału inspekcji usług AD FS dla usługi Azure AD Connect agentów kondycji, aby przetworzyć wszystkie dane inspekcji, zanim zostanie on zastąpiony.

**Pytanie: czy będzie mieć ponowne uruchomienie serwerów. podczas instalacji programu Azure AD Connect agentów kondycji?**

Nie. Instalacja agentów będą nie wymagać ponownego uruchomienia serwera. Jednak niektóre wstępnie wymagane kroki instalacji może wymagać ponownego uruchomienia serwera.

Na przykład systemu Windows Server 2008 R2, instalacja programu .NET 4.5 Framework wymaga ponownego uruchomienia serwera.

**Pytanie: czy Azure AD Connect Health pracy za pośrednictwem przekazujący serwer proxy HTTP?**

Tak. Dla bieżących operacji można skonfigurować agenta programu Health na potrzeby przekazywania wychodzących żądań HTTP przez serwer proxy HTTP.
Przeczytaj więcej na temat [Konfigurowanie serwera HTTP Proxy dla agentów kondycji](active-directory-aadconnect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy).

Należy skonfigurować serwer proxy podczas rejestracji agenta, może być konieczne wcześniej modyfikować ustawienia serwera Proxy programu Internet Explorer.

1. Otwórz program Internet Explorer > **ustawienia** > **Opcje internetowe** > **połączeń** > **ustawienia sieci LAN**.
2. Wybierz **Użyj serwera Proxy dla sieci LAN**.
3. Wybierz **zaawansowane** Jeśli porty na inny serwer proxy dla protokołu HTTP i HTTPS/Secure.

**Pytanie: czy uwierzytelnianie podstawowe pomocy technicznej usługi Azure AD Connect Health, łącząc się z serwerów proxy HTTP?**

Nie. Podaj nazwę dowolnego użytkownika i hasło dla uwierzytelniania podstawowego mechanizmu nie jest obecnie obsługiwane.

**Pytanie: jakie porty zapory należy otworzyć do agenta programu Azure AD Connect Health do pracy?**

Zobacz [sekcji wymagania](active-directory-aadconnect-health-agent-install.md#requirements) listę portów zapory i inne wymagania dotyczące łączności.

**Pytanie: Dlaczego ma dwa serwery z taką samą nazwą w portalu Azure AD Connect Health**

Po usunięciu agenta z serwera, serwer nie jest automatycznie usunięte z portalu Azure AD Connect Health. Ręcznie usuń agenta z serwera lub usunięcie serwera, należy ręcznie usunąć wpis serwera z portalu Azure AD Connect Health.

Można odtworzyć serwer lub Utwórz nowy serwer o tej samej szczegółów (takich jak nazwa komputera). Jeśli nie usunięto już zarejestrowanego serwera z portalu Azure AD Connect Health, a agent jest zainstalowany na nowym serwerze, mogą występować dwa wpisy o takiej samej nazwie.

W takim przypadku ręcznie usuń wpis, który należy do starszym serwerze. Dane dla tego serwera powinny być nieaktualne.

## <a name="health-agent-registration-and-data-freshness"></a>Agent kondycji świeżości rejestracji i danych

**Pytanie: jakie są typowe przyczyny niepowodzenia rejestracji agenta programu Health i sposób rozwiązywania problemów?**

Agent kondycji może zakończyć się niepowodzeniem do rejestrowania z powodu następujących powodów:

* Agent nie może komunikować się z wymagane punkty końcowe, ponieważ Zapora blokuje ruch. Jest to szczególnie wspólnej na serwerach proxy aplikacji sieci web. Upewnij się, zezwolono komunikacji wychodzącej do wymaganych punktów końcowych i portów. Zobacz [sekcji wymagania](active-directory-aadconnect-health-agent-install.md#requirements) szczegółowe informacje.
* Komunikacji wychodzącej podlega kontroli SSL przez warstwę sieci. Powoduje to, że certyfikat używany przez agenta mają zostać zastąpione przez serwer inspekcji na jednostkę i kroki, aby zakończyć rejestrację agenta zakończyć się niepowodzeniem.
* Użytkownik nie ma dostępu do wykonywania rejestracji agenta. Administratorzy globalni mają dostęp domyślnie. Można użyć [kontroli dostępu opartej na rolach](active-directory-aadconnect-health-operations.md#manage-access-with-role-based-access-control) delegować dostęp do innych użytkowników.

**Pytanie: czy mogę am uzyskiwanie alertów czy "dane usługi kondycji nie jest aktualny." Jak rozwiązać ten problem?**

Azure AD Connect Health generuje alert, gdy nie otrzyma punktów danych z serwera w ciągu ostatnich dwóch godzin. [Dowiedz się więcej](active-directory-aadconnect-health-data-freshness.md).

## <a name="operations-questions"></a>Pytania dotyczące działań
**Pytanie: należy włączyć inspekcję na serwerach proxy aplikacji sieci web?**

Nie, inspekcja nie muszą być włączone na serwerach proxy aplikacji sieci web.

**Pytanie: jak uzyskać rozwiązane alerty dotyczące kondycji połączenia usługi Azure AD?**

Alerty usługi Azure AD Connect Health uzyskać rozwiązane na stan powodzenia. Azure AD Connect agentów kondycji wykrywania i okresowo zgłoszenia warunki powodzenia do usługi. Kilka alertów pomijanie jest oparte na czasie. Innymi słowy jeśli sam błąd nie jest przestrzegana w 72 godziny od Generowanie alertów, alert został rozwiązany automatycznie.

**Pytanie: czy mam pobierania alerty czy "Test żądania uwierzytelniania (transakcja syntetyczna) nie można uzyskać tokenu." Jak rozwiązać ten problem?**

Azure AD Connect Health dla usług AD FS generuje ten alert, gdy Agent kondycji zainstalowane na serwerze usług AD FS nie powiedzie się do uzyskania tokenu w ramach transakcji syntetycznych inicjowane przez agenta programu Health. Agent kondycji używa kontekstu systemu lokalnego i próbuje uzyskać token dla własnym jednostki zależnej. To jest test wychwytywania aby upewnić się, że usługi AD FS jest w stanie wydawania tokenów.

W większości przypadków tego testu nie działa, ponieważ Agent kondycji nie może rozpoznać nazwy farmy usług AD FS. Może to nastąpić, jeśli serwery usług AD FS znajdują się za moduły równoważenia obciążenia sieciowego i żądania pobiera inicjowany z węzłem związanej z modułem równoważenia obciążenia (w przeciwieństwie do regularnych klienta, który jest przed usługi równoważenia obciążenia). Można to naprawić przez zaktualizowanie pliku "hostów" znajduje się w obszarze "C:\Windows\System32\drivers\etc" uwzględnić adres IP serwera usług AD FS lub adresem IP sprzężenia zwrotnego (127.0.0.1) dla nazwy farmy usług AD FS (np. sts.contoso.com). Dodawanie pliku hosta będzie zwarcia wywołania sieci, dzięki czemu agenta kondycji, aby uzyskać token.

**Pytanie: otrzymano wiadomość e-mail wskazujący, że moje maszyny nie są poprawiono ostatnie atakom ransomeware. Dlaczego otrzymuję ten adres e-mail?**

Usługa Azure AD Connect Health skanowane wszystkie maszyny, który monitoruje zapewnienie wymagane poprawki zostały zainstalowane. Wiadomość e-mail została wysłana do administratorów dzierżawy, jeśli co najmniej jeden komputer nie miał krytycznych poprawek. Następującą logiką została użyta, aby określić to.
1. Znajdź wszystkie poprawki, które są zainstalowane na tym komputerze.
2. Sprawdź, czy znajduje się co najmniej jedna z poprawek z zdefiniowanej listy.
3. Jeśli tak, komputer jest chroniony. Jeśli nie, komputer jest narażone na atak.

Poniższy skrypt programu PowerShell służy do ręcznego wykonania tego sprawdzenia. Implementuje logiki powyżej.

```
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

**Pytanie: Dlaczego polecenia cmdlet programu PowerShell <i>Get MsolDirSyncProvisioningError</i> Pokaż mniej błędy synchronizacji w wyniku?**

<i>Get-MsolDirSyncProvisioningError</i> zwróci tylko te narzędzia DirSync, inicjowanie obsługi błędów. Oprócz, portal Connect Health pokazuje też inne synchronizacji typy błędów, takich jak błędy eksportu. Jest to zgodne z wynikiem delta Azure AD Connect. Przeczytaj więcej na temat [błędy synchronizacji Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-sync-errors).


## <a name="related-links"></a>Powiązane linki
* [Azure AD Connect Health](active-directory-aadconnect-health.md)
* [Instalowanie agenta programu Azure AD Connect Health](active-directory-aadconnect-health-agent-install.md)
* [Operacje w programie Azure AD Connect Health](active-directory-aadconnect-health-operations.md)
* [Używanie programu Azure AD Connect Health z usługami AD FS](active-directory-aadconnect-health-adfs.md)
* [Używanie programu Azure AD Connect Health w celu synchronizacji](active-directory-aadconnect-health-sync.md)
* [Używanie programu Azure AD Connect Health z usługami AD DS](active-directory-aadconnect-health-adds.md)
* [Historia wersji programu Azure AD Connect Health](active-directory-aadconnect-health-version-history.md)
