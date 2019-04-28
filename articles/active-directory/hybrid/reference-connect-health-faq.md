---
title: Azure Active Directory Connect Health — często zadawane pytania — Azure | Dokumentacja firmy Microsoft
description: Często zadawane pytania dotyczące usługi Azure AD Connect Health. Wśród często zadawanych pytań znajdują się pytania dotyczące korzystania z samej usługi, w tym między innymi na temat modelu rozliczeń, możliwości, ograniczeń i pomocy technicznej.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.assetid: f1b851aa-54d7-4cb4-8f5c-60680e2ce866
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 07/18/2017
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: ec88caafa9a6168860a8e9e2ff9e2abe0cfd0e77
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62096126"
---
# <a name="azure-ad-connect-health-frequently-asked-questions"></a>Azure AD Connect Health — często zadawane pytania
Ten artykuł zawiera odpowiedzi na często zadawane pytania (FAQ) dotyczących usługi Azure Active Directory (Azure AD) Connect Health. Wśród często zadawanych pytań obejmują pytania dotyczące sposobu korzystania z usługi, który zawiera model rozliczeń możliwości, ograniczeń i pomocy technicznej.

## <a name="general-questions"></a>Pytania ogólne
**Pyt.: Czy mogę zarządzać wieloma katalogami usługi Azure AD. Jak przełączać się ten, który ma usługi Azure Active Directory Premium systemu Azure?**

Aby przełączyć się między różnymi dzierżaw usługi Azure AD wybierz aktualnie zalogowanego **nazwy użytkownika** w prawym górnym rogu, a następnie wybierz odpowiednie konto. Jeśli konto nie ma na liście, wybierz **Wyloguj**, a następnie użyj poświadczenia administratora globalnego katalogu, który ma usługi Azure Active Directory Premium włączone do logowania.

**Pyt.: Jakie wersje ról tożsamości są obsługiwane przez program Azure AD Connect Health?**

Poniższa tabela zawiera listę ról i obsługiwane wersje systemu operacyjnego.

|Rola| System operacyjny / wersja|
|--|--|
|Usługi federacyjne Active Directory (AD FS)| <ul> <li> Windows Server 2008 R2 </li><li> Windows Server 2012  </li> <li>Windows Server 2012 R2 </li> <li> Windows Server 2016  </li> </ul>|
|Azure AD Connect | Wersja 1.0.9125 lub nowszej|
|Active Directory Domain Services (AD DS)| <ul> <li> Windows Server 2008 R2 </li><li> Windows Server 2012  </li> <li>Windows Server 2012 R2 </li> <li> Windows Server 2016  </li> </ul>|

Należy pamiętać, że funkcje udostępniane przez usługę może różnić się zależnie od roli oraz system operacyjny. Innymi słowy wszystkie funkcje nie mogą być dostępne dla wszystkich wersji systemu operacyjnego. Zobacz opisy funkcji, aby uzyskać szczegółowe informacje.

**Pyt.: Liczba licencji należy monitorować mojej infrastruktury?**

* Pierwszy Agent programu Connect Health wymaga co najmniej jedną licencję Azure AD Premium.
* Każdy kolejny zarejestrowanych agent wymaga 25 dodatkowych licencji usługi Azure AD Premium.
* Liczba agentów jest odpowiednikiem łączna liczba agentów, które są zarejestrowane wszystkich monitorowanych ról (usług AD FS, Azure AD Connect lub usług AD DS).
* Licencjonowanie programu AAD Connect Health nie wymaga przypisania licencji do określonych użytkowników. Musisz mieć wymaganego liczbę ważnych licencji.

Informacje o licencjonowaniu znajduje się również na [stronę z cennikiem usługi Azure AD](https://aka.ms/aadpricing).

Przykład:

| Zarejestrowanych agentów | Potrzebnych licencji | Przykładowa konfiguracja monitorowania |
| ------ | --------------- | --- |
| 1 | 1 | 1 serwer usługi azure AD Connect |
| 2 | 26| 1 serwer usługi azure AD Connect a kontrolerem domeny 1 |
| 3 | 51 | 1 serwer usługi active Directory Federation Services (AD FS), 1, AD FS z serwera proxy i kontrolera domeny 1 |
| 4 | 76 | Serwer 1, AD FS, 1, AD FS z serwera proxy i 2 kontrolerów domeny |
| 5 | 101 | 1 serwer usługi azure AD Connect, serwer 1, AD FS, 1, AD FS z serwera proxy i 2 kontrolerów domeny |

**Pyt.: Usługa Azure AD Connect Health obsługuje Azure Germany Cloud?**

Program Azure AD Connect Health nie jest obsługiwana w chmurze (Niemcy) z wyjątkiem [funkcja raport błędów synchronizacji](how-to-connect-health-sync.md#object-level-synchronization-error-report).

| Role | Funkcje | Obsługiwane w niemieckiej wersji chmury |
| ------ | --------------- | --- |
| Connect Health do celów synchronizacji | Monitorowanie / Insight / alerty / analizy | Nie |
|  | Raport o błędach synchronizacji | Yes |
| Program Connect Health dla usług AD FS | Monitorowanie / Insight / alerty / analizy | Nie |
| Program Connect Health dla usług AD DS | Monitorowanie / Insight / alerty / analizy | Nie |

W celu zapewnienia łączności agenta programu Connect Health do celów synchronizacji, należy skonfigurować [wymaganie instalacyjne](how-to-connect-health-agent-install.md#outbound-connectivity-to-the-azure-service-endpoints) odpowiednio.

## <a name="installation-questions"></a>Pytania dotyczące instalacji

**Pyt.: Jaki jest wpływ instalowania agenta programu Azure AD Connect Health na poszczególnych serwerach?**

Wpływ instalowania agenta: Microsoft Azure AD Connect Health, usług AD FS, serwery proxy aplikacji sieci web, serwery usługi Azure AD Connect (synchronizacja) kontrolerów domeny jest minimalny względem procesora CPU, użycie pamięci, przepustowości sieci i magazynu.

Następujące numery są przybliżeniem:

* Użycie procesora CPU: wzrost o 5 około 1%.
* Zużycie pamięci: Maksymalnie 10% całkowitej pamięci systemu.

> [!NOTE]
> Jeśli agent nie może komunikować się z platformą Azure, agent przechowuje dane lokalnie zdefiniowany maksymalny limit. Agent zastępuje "buforowane" dane na zasadzie "najdawniej obsługiwane".
>
>

* Magazyn lokalny buforu dla agentów Azure AD Connect Health: ~ 20 MB.
* W przypadku serwerów usług AD FS zaleca się obsługi administracyjnej miejsca na dysku na 1024 MB (1 GB) dla kanału danych inspekcji usług AD FS dla agentów Azure AD Connect Health do przetworzenia wszystkich danych inspekcji, zanim zostanie zastąpiony.

**Pyt.: Będzie konieczne ponowne uruchomienie serwerów. podczas instalowania agentów programu Azure AD Connect Health?**

Nie. Instalacja agentów będą nie wymagać ponownego uruchomienia serwera. Jednak niektóre kroki wymagań wstępnych instalacji może wymagać ponownego uruchomienia serwera.

Instalacja platformy .NET Framework 4.5, na przykład w systemie Windows Server 2008 R2, wymaga ponownego uruchomienia serwera.

**Pyt.: Usługa Azure AD Connect Health? przy użyciu przekazywanego serwer proxy HTTP**

Tak. Dla trwających operacji można skonfigurować agenta programu Health na potrzeby przekazywania ruchu wychodzącego żądań HTTP serwera proxy HTTP.
Przeczytaj więcej na temat [konfigurowania serwera HTTP Proxy dla agentów kondycji](how-to-connect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy).

Jeśli musisz skonfigurować serwer proxy podczas rejestracji agenta, konieczne może być wcześniej zmodyfikować ustawienia serwera Proxy programu Internet Explorer.

1. Otwórz program Internet Explorer > **ustawienia** > **Opcje internetowe** > **połączeń** > **ustawienia sieci LAN** .
2. Wybierz **Użyj serwera Proxy dla sieci LAN**.
3. Wybierz **zaawansowane** w przypadku portów inny serwer proxy dla protokołu HTTP i HTTPS/Secure.

**Pyt.: Program Azure AD Connect Health obsługuje uwierzytelnianie podstawowe podczas nawiązywania połączenia z serwerami proxy HTTP?**

Nie. Mechanizm do określenia nazwy dowolnego użytkownika i hasła dla uwierzytelniania podstawowego nie jest obecnie obsługiwane.

**Pyt.: Jakie porty zapory należy otworzyć do agenta programu Azure AD Connect Health do pracy?**

Zobacz [sekcji wymagania dotyczące](how-to-connect-health-agent-install.md#requirements) listę portów zapory i inne wymagania dotyczące łączności.

**Pyt.: Dlaczego są wyświetlane dwa serwery z taką samą nazwą w portalu usługi Azure AD Connect Health?**

Po usunięciu agenta z serwera serwer nie zostanie automatycznie usunięta z portalu usługi Azure AD Connect Health. Ręcznie usuń agenta z serwera lub usunąć samego serwera, należy ręcznie usunąć wpis server z portalu usługi Azure AD Connect Health.

Można odtworzyć z obrazu serwera lub utworzyć nowy serwer przy użyciu tego samego szczegółów (takich jak nazwa komputera). Jeśli już zarejestrowanego serwera nie został usunięty z portalu usługi Azure AD Connect Health, a agent jest zainstalowany na nowym serwerze, mogą pojawić się dwa wpisy o takiej samej nazwie.

W takim przypadku ręcznie usuń wpis, który należy do starszej serwera. Dane dla tego serwera powinny być nieaktualne.

## <a name="health-agent-registration-and-data-freshness"></a>Agent kondycji świeżości rejestracji i danych

**Pyt.: Jakie są typowe przyczyny niepowodzenia rejestracji agenta programu Health i jak rozwiązywać problemy?**

Agent kondycji może zakończyć się niepowodzeniem do rejestrowania z powodu następujących przyczyn:

* Agent nie może komunikować się z wymagane punkty końcowe, ponieważ Zapora blokuje ruch. Jest to szczególnie powszechny na serwerach proxy aplikacji sieci web. Upewnij się, czy możesz uzyskać komunikacja wychodząca wymagane punkty końcowe i porty. Zobacz [sekcji wymagania dotyczące](how-to-connect-health-agent-install.md#requirements) Aby uzyskać szczegółowe informacje.
* Komunikacja wychodząca jest poddawana inspekcji połączenia SSL przez warstwę sieciową. Powoduje to, że certyfikat, który agent używa mają zostać zastąpione przez serwer/jednostka inspekcji i niepowodzenie kroki, aby ukończyć rejestrację agenta.
* Użytkownik nie ma dostępu do przeprowadzenia rejestracji agenta. Domyślnie administratorzy globalni mają dostęp. Możesz użyć [kontroli dostępu opartej na rolach](how-to-connect-health-operations.md#manage-access-with-role-based-access-control) można delegować dostępu do innych użytkowników.

**Pyt.: Czy mogę jestem wprowadzenie alerty, "dane usługi kondycji nie jest aktualny." Jak rozwiązać ten problem?**

Program Azure AD Connect Health generuje alert, gdy nie otrzyma wszystkich punktów danych z serwera w ostatnich dwóch godzin. [Dowiedz się więcej](how-to-connect-health-data-freshness.md).

## <a name="operations-questions"></a>Pytania dotyczące operacji
**Pyt.: Czy muszę włączyć funkcję inspekcji na serwerach proxy aplikacji sieci web?**

Nie, inspekcji nie muszą być włączone na serwerach proxy aplikacji sieci web.

**Pyt.: Jak rozwiązać Azure alerty AD Connect Health?**

Alerty programu Azure AD Connect Health można rozwiązać, pod warunkiem powodzenia. Agentów Azure AD Connect Health wykrywanie i okresowo zgłoszenia warunków powodzenia do usługi. Kilka alertów pomijanie jest oparte na czasie. Oznacza to jeśli ten sam błąd nie zostanie wykryty w ciągu 72 godzin z generowania alertów, alert zostanie rozwiązany automatycznie.

**Pyt.: Czy mogę jestem wprowadzenie alertów, czy "testowe żądanie uwierzytelniania (transakcja syntetyczna) nie można uzyskać tokenu". Jak rozwiązać ten problem?**

Azure AD Connect Health dla usług AD FS generuje ten alert, gdy Agent kondycji zainstalowany na serwerze usług AD FS nie powiedzie się do uzyskania tokenu jako część transakcji syntetycznych inicjowane przez agenta programu Health. Agent kondycji używa kontekstu systemu lokalnego i próbuje uzyskać token dla własnym jednostki zależnej. Jest to test wychwytywania, aby upewnić się, że usług AD FS znajduje się w stanie wydawania tokenów.

W większości przypadków ten test zakończy się, ponieważ Agent kondycji nie jest w stanie rozpoznać nazwę farmy usług AD FS. Może to nastąpić, jeśli serwery usług AD FS znajdują się za usługą równoważenia obciążenia sieci, a żądanie pobiera inicjowany z węzła, który jest modułem równoważenia obciążenia (w przeciwieństwie do regularnych klienta, który znajduje się przed modułu równoważenia obciążenia). Może to być naprawiony przez zaktualizowanie pliku "hosts" znajdujący się w folderze "C:\Windows\System32\drivers\etc" uwzględnić adres IP serwera usług AD FS lub adresem IP sprzężenia zwrotnego (127.0.0.1) dla nazwy farmy usług AD FS (np. sts.contoso.com). Dodawanie pliku hosta będzie zwarcie wywołania sieci, dzięki czemu agenta programu Health można pobrać tokenu.

**Pyt.: Wysłano do mnie wiadomość e-mail z informacją, że moich maszynach są nie zastosowano poprawkę luki ostatnie wymuszającego okup. Dlaczego otrzymuję tę wiadomość e-mail?**

Usługi Azure AD Connect Health skanowania wszystkich maszyn, które monitoruje ona zapewnienie wymagane poprawki zostały zainstalowane. Wiadomość e-mail została wysłana do administratorów dzierżawy, jeśli co najmniej jedna maszyna nie miał krytycznych poprawek. Poniższa logika był używany, aby określić to.
1. Znajdź wszystkie poprawki, które są zainstalowane na komputerze.
2. Sprawdź, jeśli występuje co najmniej jedna z poprawek, które z zdefiniowanej listy.
3. Jeśli tak, komputer jest chroniony. W przeciwnym razie komputer jest narażone na atak.

Poniższy skrypt programu PowerShell można użyć, aby to sprawdzić ręcznie. Implementuje logikę powyżej.

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

**Pyt.: Dlaczego polecenie cmdlet programu PowerShell <i>Get MsolDirSyncProvisioningError</i> Pokaż mniej błędów synchronizacji w wynikach?**

<i>Get-MsolDirSyncProvisioningError</i> zwróci tylko narzędzia DirSync, inicjowania obsługi błędów. Oprócz, w portalu programu Connect Health pokazuje również inne synchronizacji typy błędów, takie jak błędy eksportu. Jest to zgodne z wynikiem różnicowej program Azure AD Connect. Przeczytaj więcej na temat [błędy Azure AD Connect Sync](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-sync-errors).

**Pyt.: Dlaczego są moje usług AD FS inspekcji nie są generowane?**

Użyj polecenia cmdlet programu PowerShell <i>Get-AdfsProperties - AuditLevel</i> upewnij się, dzienniki inspekcji nie znajduje się w stanie wyłączony. Przeczytaj więcej na temat [dzienniki inspekcji usług AD FS](https://docs.microsoft.com/windows-server/identity/ad-fs/technical-reference/auditing-enhancements-to-ad-fs-in-windows-server#auditing-levels-in-ad-fs-for-windows-server-2016). Zwróć uwagę, jeśli są to zaawansowane ustawienia inspekcji, przekazywane do serwera usług AD FS, zmiany przy użyciu auditpol.exe zostaną zastąpione (zdarzenie w przypadku aplikacji generowany jest nieskonfigurowany). W tym przypadku Ustaw zasady zabezpieczeń lokalnych, aby rejestrować błędy generowane aplikację i Powodzenie.

**Pyt.: Kiedy certyfikat agenta będzie automatyczne wygaśnięcie odnowiony przed?**
Certyfikacja agent będzie automatyczne odnowić **6 miesięcy** przed datą wygaśnięcia. Jeśli nie zostanie odnowiona, upewnij się, że połączenie sieciowe agenta jest stabilna. Ponowne uruchamianie usługi agenta lub aktualizacji do najnowszej wersji może rozwiązać ten problem.


## <a name="related-links"></a>Powiązane linki
* [Azure AD Connect Health](whatis-hybrid-identity-health.md)
* [Instalowanie agenta programu Azure AD Connect Health](how-to-connect-health-agent-install.md)
* [Operacje w programie Azure AD Connect Health](how-to-connect-health-operations.md)
* [Używanie programu Azure AD Connect Health z usługami AD FS](how-to-connect-health-adfs.md)
* [Używanie programu Azure AD Connect Health w celu synchronizacji](how-to-connect-health-sync.md)
* [Używanie programu Azure AD Connect Health z usługami AD DS](how-to-connect-health-adds.md)
* [Historia wersji programu Azure AD Connect Health](reference-connect-health-version-history.md)
