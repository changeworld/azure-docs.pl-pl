---
title: 'Synchronizacja programu Azure AD Connect: zagadnienia i zadania operacyjne | Dokumentacja firmy Microsoft'
description: W tym temacie opisano zadania operacyjne synchronizacja programu Azure AD Connect i sposoby przygotowania do działania tego składnika.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: b29c1790-37a3-470f-ab69-3cee824d220d
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/13/2017
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 6e35dc0ad8493d2f92b7e81fc66f444048482bd9
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34592946"
---
# <a name="azure-ad-connect-sync-operational-tasks-and-consideration"></a>Synchronizacja programu Azure AD Connect: brany pod uwagę i zadania operacyjne
Celem tego tematu jest do opisywania zadań operacyjnych synchronizacji usługi Azure AD Connect.

## <a name="staging-mode"></a>Tryb przejściowy
Tryb przejściowy może służyć do kilka scenariuszy, w tym:

* Wysoka dostępność.
* Testowania i wdrażania nowych zmian konfiguracji.
* Wprowadzenie nowego serwera i zlikwidować stary.

Z serwerem w trybie przejściowym można wprowadzić zmiany w konfiguracji i Podgląd zmian przed wprowadzeniem serwera active. Można też uruchomić pełny import i pełną synchronizację, aby sprawdzić wszystkie zmiany oczekuje przed ich wprowadzeniem do środowiska produkcyjnego.

Podczas instalacji, możesz wybrać serwer będzie **Tryb przejściowy**. Ta akcja powoduje, że serwer jest active importowania i synchronizacji, ale nie działa on żadnych eksportów. Serwer w trybie przejściowym nie jest uruchomiona, synchronizacja haseł lub zapisywania zwrotnego haseł, nawet jeśli te funkcje są wybrane podczas instalacji. Po wyłączeniu trybu przejściowego serwer rozpoczyna eksportowanie, umożliwia synchronizację haseł i włącza funkcję zapisywania zwrotnego haseł.

> [!NOTE]
> Załóżmy, że masz Azure AD Connect z włączoną funkcją synchronizacji skrótu hasła. Po włączeniu trybu przejściowego Zatrzymuje serwer, który synchronizacji haseł zostanie zmieniony z lokalnej usługi AD. Po wyłączeniu trybu przejściowego serwera wznawia synchronizowanie zmian haseł, z którym ostatnio przerwał. Jeśli serwer jest w trybie przejściowym przez dłuższy czas, może upłynąć trochę czasu na serwerze zsynchronizować wszystkie zmiany hasła, które wystąpiły w czasie.
>
>

Nadal można wymusić eksportu przy użyciu Menedżera usługi synchronizacji.

Serwer w trybie przejściowym nadal do otrzymywania zmian z usługi Active Directory i Azure AD. Zawsze ma kopię najnowsze zmiany i podejmij bardzo szybko mogą za pośrednictwem obowiązki innego serwera. Jeśli wprowadzisz zmiany w konfiguracji podstawowy serwer jest obowiązek wprowadzić te same zmiany na serwerze w trybie przejściowym.

Dla osób, które znajomości starszych technologii synchronizacji trybu przejściowego różni się od serwera ma własną bazę danych SQL. Taka architektura umożliwia przemieszczania serwerze działającym w trybie znajdować się w różnych centrach danych.

### <a name="verify-the-configuration-of-a-server"></a>Weryfikowanie konfiguracji serwera
Aby zastosować tę metodę, wykonaj następujące kroki:

1. [Przygotowanie](#prepare)
2. [Konfiguracja](#configuration)
3. [Importuje i synchronizuje](#import-and-synchronize)
4. [Sprawdź](#verify)
5. [Serwer active przełącznika](#switch-active-server)

#### <a name="prepare"></a>Przygotowanie
1. Zainstalować program Azure AD Connect, wybierz **Tryb przejściowy**i usuń zaznaczenie pozycji **rozpoczęcia synchronizacji** na ostatniej stronie Kreatora instalacji. Ten tryb umożliwia ręczne uruchomienie aparatu synchronizacji.
   ![ReadyToConfigure](./media/active-directory-aadconnectsync-operations/readytoconfigure.png)
2. Znak poza/logowania do i z menu start wybierz opcję **usługi synchronizacji**.

#### <a name="configuration"></a>Konfigurowanie
Jeśli wprowadzono zmiany niestandardowego serwera podstawowego i do porównania z konfiguracją o serwerze, należy użyć [Dokumentatora konfiguracji usługi Azure AD Connect](https://github.com/Microsoft/AADConnectConfigDocumenter).

#### <a name="import-and-synchronize"></a>Importuje i synchronizuje
1. Wybierz **łączniki**i wybierz pierwszy łącznik z typem **usług domenowych w usłudze Active Directory**. Kliknij przycisk **Uruchom**, wybierz pozycję **pełny import**, i **OK**. Wykonaj te kroki dla wszystkich łączników tego typu.
2. Wybierz łącznik z typem **usługi Azure Active Directory (Microsoft)**. Kliknij przycisk **Uruchom**, wybierz pozycję **pełny import**, i **OK**.
3. Upewnij się, że karta łączniki nadal jest zaznaczone. Dla poszczególnych łączników o typie **usług domenowych w usłudze Active Directory**, kliknij przycisk **Uruchom**, wybierz pozycję **synchronizacja przyrostowa**, i **OK**.
4. Wybierz łącznik z typem **usługi Azure Active Directory (Microsoft)**. Kliknij przycisk **Uruchom**, wybierz pozycję **synchronizacja przyrostowa**, i **OK**.

Ma teraz przemieszczane eksportu zmiany do usługi Azure AD i lokalnej usłudze AD (Jeśli używasz wdrożenia hybrydowego programu Exchange). Następne kroki pozwalają sprawdzić, jakie ma zostać zmienione przed rozpoczęciem faktycznie eksportu do katalogów.

#### <a name="verify"></a>Weryfikuj
1. Uruchom wiersz polecenia i przejdź do `%ProgramFiles%\Microsoft Azure AD Sync\bin`
2. Instalacja: `csexport "Name of Connector" %temp%\export.xml /f:x` nazwę łącznika można znaleźć w usługi synchronizacji. Ma on nazwę, podobnie jak "contoso.com — AAD" dla usługi Azure AD.
3. Instalacja: `CSExportAnalyzer %temp%\export.xml > %temp%\export.csv` plik w % temp % o nazwie export.csv, która może być zbadana w programie Microsoft Excel. Ten plik zawiera wszystkie zmiany, które mają być eksportowane.
4. Wprowadź niezbędne zmiany dane lub konfiguracja, a następnie uruchom następujące kroki ponownie (importowania i synchronizowania i sprawdź, czy), dopóki nie zostaną wprowadzone zmiany, które mają zostać wyeksportowane.

**Opis plików export.csv** najczęściej pliku jest nie wymaga objaśnień. Niektóre skróty do poznania treści:
* OMODT — Typ modyfikacji obiektu. Wskazuje, czy operacja na poziomie obiektu jest Add, Update lub Delete.
* AMODT — typ atrybutu modyfikacji. Wskazuje, czy operacja na poziomie atrybutu jest Add, Update lub delete.

**Pobierać wspólne identyfikatory** plik export.csv zawiera wszystkie zmiany, które mają być eksportowane. Każdy wiersz odpowiada zmiany obiektu do przestrzeni łącznika i obiekt jest identyfikowany przez atrybut nazwy domeny. Nazwa Wyróżniająca atrybutu to unikatowy identyfikator przypisany do obiektu w przestrzeni łącznika. Jeśli masz wiele wierszy/zmian w export.csv do analizowania może być trudne można dowiedzieć się, które obiekty się, że zmiany są dla na podstawie atrybutu Nazwa Wyróżniająca samodzielnie. Aby uprościć proces analizowania zmiany, należy użyć csanalyzer.ps1 skrypt programu PowerShell. Skrypt pobiera wspólne identyfikatory obiektów (na przykład, nazwa wyświetlana, userPrincipalName). Aby użyć skryptu:
1. Skopiuj skrypt programu PowerShell z sekcji [CSAnalyzer](#appendix-csanalyzer) w pliku o nazwie `csanalyzer.ps1`.
2. Otwórz okno programu PowerShell i przejdź do folderu, w którym utworzono skrypt programu PowerShell.
3. Instalacja: `.\csanalyzer.ps1 -xmltoimport %temp%\export.xml`.
4. Istnieje już plik o nazwie **processedusers1.csv** które można zbadać w programie Microsoft Excel. Należy pamiętać, że plik zawiera mapowanie atrybutu nazwy domeny do typowych identyfikatorów (na przykład, nazwa wyświetlana i userPrincipalName). Obecnie nie obejmuje zmiany rzeczywistej atrybutów, które mają być eksportowane.

#### <a name="switch-active-server"></a>Serwer active przełącznika
1. Na aktualnie aktywnym serwerem wyłączyć serwer (DirSync/FIM/usługi Azure AD Sync), nie jest eksportowany do usługi Azure AD lub ustaw go w trybie (Azure AD Connect) przejściowym.
2. Uruchom Kreatora instalacji na serwerze w **Tryb przejściowy** i Wyłącz **Tryb przejściowy**.
   ![ReadyToConfigure](./media/active-directory-aadconnectsync-operations/additionaltasks.png)

## <a name="disaster-recovery"></a>Odzyskiwanie po awarii
Część projektu wdrożenia jest opracowanie planu co zrobić w przypadku braku awarii, w którym utratę serwera synchronizacji. Istnieją różne modele i które zależy od wielu czynników, takich jak:

* Co to jest ustawiona tolerancja, dla którego nie można wprowadzać zmiany obiektów w usłudze Azure AD podczas Przestój?
* Jeśli używasz synchronizacji haseł, czy zaakceptowane muszą używać starego hasła w usłudze Azure AD, w przypadku ich zmiany lokalne?
* Czy użytkownik ma zależności w czasie rzeczywistym operacje, takie jak zapisywanie zwrotne haseł?

W zależności od odpowiedzi na te pytania i zasady organizacji można zaimplementować jedną z następujących strategii:

* Ponownie w razie potrzeby.
* Zapasowe serwer zapasowy nie powinien, znany jako **Tryb przejściowy**.
* Użyj maszyn wirtualnych.

Jeśli nie używasz wbudowaną bazą danych programu SQL Express, a następnie należy także przejrzeć [wysokiej dostępności SQL](#sql-high-availability) sekcji.

### <a name="rebuild-when-needed"></a>Skompiluj ponownie w razie potrzeby
Strategia działało jest opracowanie planu do odbudowywania serwera, w razie potrzeby. Zazwyczaj Instalowanie aparatu synchronizacji i czy początkowej importowania i synchronizacji można wykonać w kilka godzin. Jeśli serwer zapasowe jest niedostępna, istnieje możliwość tymczasowo kontroler domeny jest używany do obsługi aparatu synchronizacji.

Serwer aparatu synchronizacji nie przechowuje jakikolwiek stan o obiektach, bazy danych może zostać ponownie skompilowany od danych w usłudze Active Directory i Azure AD. **SourceAnchor** atrybut służy do sprzęgania obiektów z firmą i chmurą. Po odbudowaniu serwera z istniejących obiektów lokalnie i w chmurze, a następnie aparat synchronizacji jest zgodna tych obiektów jednocześnie ponownie na ponowną instalację. Potrzebne do dokumentów i zapisać kwestie zmian konfiguracji na serwerze, takie jak reguły filtrowania i synchronizacji. Przed rozpoczęciem tej synchronizacji, należy ponownie zastosować te konfiguracje niestandardowe.

### <a name="have-a-spare-standby-server---staging-mode"></a>Zapasowe serwer zapasowy nie powinien — Tryb przejściowy
Jeśli masz środowisku bardziej złożonym, następnie o jeden lub więcej serwerów rezerwy jest zalecane. Podczas instalacji, można włączyć serwer będzie **Tryb przejściowy**.

Aby uzyskać więcej informacji, zobacz [Tryb przejściowy](#staging-mode).

### <a name="use-virtual-machines"></a>Użyj maszyn wirtualnych
Metoda typowe i obsługiwane jest do uruchamiania aparatu synchronizacji na maszynie wirtualnej. W przypadku, gdy host ma problemu, obrazu na serwerze aparatu synchronizacji można poddać migracji do innego serwera.

### <a name="sql-high-availability"></a>SQL wysokiej dostępności
Jeśli nie używasz programu SQL Server Express dostarczonego z programem Azure AD Connect, następnie wysoką dostępność dla programu SQL Server należy również rozważyć. Rozwiązania wysokiej dostępności, obsługiwane obejmują klastrowania SQL i AOA (zawsze włączone grupy dostępności). Nieobsługiwany rozwiązania obejmują dublowania.

Dodano obsługę SQL AOA Azure AD Connect w wersji 1.1.524.0. Przed zainstalowaniem usługi Azure AD Connect, należy włączyć SQL AOA. Podczas instalacji Azure AD Connect wykrywa, czy podane wystąpienie programu SQL jest włączone dla SQL AOA. Po włączeniu SQL AOA Azure dalsze AD Connect danych liczbowych Jeśli SQL AOA jest skonfigurowana do używania synchronicznego lub asynchronicznego replikacji. Podczas ustawiania odbiornika grupy dostępności, zalecane jest, ustaw właściwość RegisterAllProvidersIP na 0. Jest to spowodowane Azure AD Connect aktualnie używa programu SQL Native Client do połączenia z serwerem SQL i programu SQL Native Client nie obsługuje użycie właściwości MultiSubNetFailover.

## <a name="appendix-csanalyzer"></a>Appendix CSAnalyzer
Zobacz sekcję [Sprawdź](#verify) dotyczące sposobu używania tego skryptu.

```
Param(
    [Parameter(Mandatory=$true, HelpMessage="Must be a file generated using csexport 'Name of Connector' export.xml /f:x)")]
    [string]$xmltoimport="%temp%\exportedStage1a.xml",
    [Parameter(Mandatory=$false, HelpMessage="Maximum number of users per output file")][int]$batchsize=1000,
    [Parameter(Mandatory=$false, HelpMessage="Show console output")][bool]$showOutput=$false
)

#LINQ isn't loaded automatically, so force it
[Reflection.Assembly]::Load("System.Xml.Linq, Version=3.5.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089") | Out-Null

[int]$count=1
[int]$outputfilecount=1
[array]$objOutputUsers=@()

#XML must be generated using "csexport "Name of Connector" export.xml /f:x"
write-host "Importing XML" -ForegroundColor Yellow

#XmlReader.Create won't properly resolve the file location,
#so expand and then resolve it
$resolvedXMLtoimport=Resolve-Path -Path ([Environment]::ExpandEnvironmentVariables($xmltoimport))

#use an XmlReader to deal with even large files
$result=$reader = [System.Xml.XmlReader]::Create($resolvedXMLtoimport) 
$result=$reader.ReadToDescendant('cs-object')
do 
{
    #create the object placeholder
    #adding them up here means we can enforce consistency
    $objOutputUser=New-Object psobject
    Add-Member -InputObject $objOutputUser -MemberType NoteProperty -Name ID -Value ""
    Add-Member -InputObject $objOutputUser -MemberType NoteProperty -Name Type -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name DN -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name operation -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name UPN -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name displayName -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name sourceAnchor -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name alias -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name primarySMTP -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name onPremisesSamAccountName -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name mail -Value ""

    $user = [System.Xml.Linq.XElement]::ReadFrom($reader)
    if ($showOutput) {Write-Host Found an exported object... -ForegroundColor Green}

    #object id
    $outID=$user.Attribute('id').Value
    if ($showOutput) {Write-Host ID: $outID}
    $objOutputUser.ID=$outID

    #object type
    $outType=$user.Attribute('object-type').Value
    if ($showOutput) {Write-Host Type: $outType}
    $objOutputUser.Type=$outType

    #dn
    $outDN= $user.Element('unapplied-export').Element('delta').Attribute('dn').Value
    if ($showOutput) {Write-Host DN: $outDN}
    $objOutputUser.DN=$outDN

    #operation
    $outOperation= $user.Element('unapplied-export').Element('delta').Attribute('operation').Value
    if ($showOutput) {Write-Host Operation: $outOperation}
    $objOutputUser.operation=$outOperation

    #now that we have the basics, go get the details

    foreach ($attr in $user.Element('unapplied-export-hologram').Element('entry').Elements("attr"))
    {
        $attrvalue=$attr.Attribute('name').Value
        $internalvalue= $attr.Element('value').Value

        switch ($attrvalue)
        {
            "userPrincipalName"
            {
                if ($showOutput) {Write-Host UPN: $internalvalue}
                $objOutputUser.UPN=$internalvalue
            }
            "displayName"
            {
                if ($showOutput) {Write-Host displayName: $internalvalue}
                $objOutputUser.displayName=$internalvalue
            }
            "sourceAnchor"
            {
                if ($showOutput) {Write-Host sourceAnchor: $internalvalue}
                $objOutputUser.sourceAnchor=$internalvalue
            }
            "alias"
            {
                if ($showOutput) {Write-Host alias: $internalvalue}
                $objOutputUser.alias=$internalvalue
            }
            "proxyAddresses"
            {
                if ($showOutput) {Write-Host primarySMTP: ($internalvalue -replace "SMTP:","")}
                $objOutputUser.primarySMTP=$internalvalue -replace "SMTP:",""
            }
        }
    }

    $objOutputUsers += $objOutputUser

    Write-Progress -activity "Processing ${xmltoimport} in batches of ${batchsize}" -status "Batch ${outputfilecount}: " -percentComplete (($objOutputUsers.Count / $batchsize) * 100)

    #every so often, dump the processed users in case we blow up somewhere
    if ($count % $batchsize -eq 0)
    {
        Write-Host Hit the maximum users processed without completion... -ForegroundColor Yellow

        #export the collection of users as as CSV
        Write-Host Writing processedusers${outputfilecount}.csv -ForegroundColor Yellow
        $objOutputUsers | Export-Csv -path processedusers${outputfilecount}.csv -NoTypeInformation

        #increment the output file counter
        $outputfilecount+=1

        #reset the collection and the user counter
        $objOutputUsers = $null
        $count=0
    }

    $count+=1

    #need to bail out of the loop if no more users to process
    if ($reader.NodeType -eq [System.Xml.XmlNodeType]::EndElement)
    {
        break
    }

} while ($reader.Read)

#need to write out any users that didn't get picked up in a batch of 1000
#export the collection of users as CSV
Write-Host Writing processedusers${outputfilecount}.csv -ForegroundColor Yellow
$objOutputUsers | Export-Csv -path processedusers${outputfilecount}.csv -NoTypeInformation
```

## <a name="next-steps"></a>Kolejne kroki
**Tematy poglądowe**  

* [Synchronizacja programu Azure AD Connect: zrozumienie i dostosowywanie synchronizacji](active-directory-aadconnectsync-whatis.md)  
* [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](active-directory-aadconnect.md)  
