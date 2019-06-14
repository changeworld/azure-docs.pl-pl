---
title: 'Synchronizacja programu Azure AD Connect: Zagadnienia i zadania operacyjne | Dokumentacja firmy Microsoft'
description: W tym temacie opisano zadania operacyjne dla synchronizacji programu Azure AD Connect i sposoby przygotowania do działania tego składnika.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: b29c1790-37a3-470f-ab69-3cee824d220d
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/27/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 176b8509892ef16b631697a686471e7fa52bb380
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60381590"
---
# <a name="azure-ad-connect-staging-server-and-disaster-recovery"></a>Program Azure AD Connect: Serwer przejściowy i odzyskiwanie po awarii
Z serwerem w trybie przejściowym można wprowadzić zmiany w konfiguracji i Podgląd zmian, zanim serwer był aktywny. Umożliwia również uruchom pełne importowanie i pełną synchronizację, aby sprawdzić, oczekiwano wszystkich zmian przed wprowadzeniem tych zmian w środowisku produkcyjnym.

## <a name="staging-mode"></a>Tryb przejściowy
Tryb przejściowy może służyć do kilka scenariuszy, w tym:

* Wysoka dostępność.
* Testuj i wdrażaj nowych zmian konfiguracji.
* Wprowadzenie nowego serwera i zlikwidować stary.

Podczas instalacji, możesz wybrać serwer w **Tryb przejściowy**. Ta akcja sprawia, że serwer jest aktywny w celu importowania i synchronizacji, ale nie działa on eksportowanie. Serwer w trybie przejściowym nie jest uruchomiony, synchronizacja haseł lub zapisywania zwrotnego haseł, nawet jeśli te funkcje są wybrane podczas instalacji. Po wyłączeniu trybu przejściowego serwer rozpoczyna eksportowanie, umożliwia synchronizację haseł i włącza funkcję zapisywania zwrotnego haseł.

> [!NOTE]
> Załóżmy, że masz program Azure AD Connect z włączoną funkcją synchronizacji skrótów haseł. Po włączeniu trybu przejściowego zatrzymuje serwera, który synchronizacji haseł zmieni się z lokalnej usługi AD. Po wyłączeniu trybu przejściowego serwer wznawia synchronizowanie zmian haseł z jej ostatniej zamknięciem. Jeśli serwer jest w trybie przejściowym przez dłuższy czas, może upłynąć trochę czasu w serwera synchronizować wszystkie zmiany hasła, które wystąpiły w czasie.
>
>

Aby wymusić eksportu, można nadal przy użyciu Menedżera usługi synchronizacji.

Serwer w trybie przejściowym w dalszym ciągu otrzymywać zmian usługi Active Directory a usługą Azure AD. Zawsze ma kopię najnowszych zmian i podejmij bardzo szybko może za pośrednictwem obowiązki innego serwera. Jeśli wprowadzisz zmiany w konfiguracji podstawowego serwera jest odpowiedzialny za wprowadzenie identycznych zmian do serwera w trybie przejściowym.

Dla osób, przy zachowaniu wiedzy o starsze technologie synchronizacji trybu przejściowego różni się od serwera ma własną bazę danych SQL. Ta architektura pozwala serwer Tryb przejściowy, muszą znajdować się w innym centrum danych.

### <a name="verify-the-configuration-of-a-server"></a>Sprawdź konfigurację serwera
Aby zastosować tę metodę, wykonaj następujące kroki:

1. [Przygotowywanie](#prepare)
2. [Konfiguracja](#configuration)
3. [Importuj i Synchronizuj](#import-and-synchronize)
4. [Weryfikacja](#verify)
5. [Przełącznik aktywnego serwera.](#switch-active-server)

#### <a name="prepare"></a>Przygotowanie
1. Zainstaluj program Azure AD Connect, wybierz **Tryb przejściowy**i usuń zaznaczenie pozycji **rozpoczęcia synchronizacji od** na ostatniej stronie Kreatora instalacji. Tryb ten umożliwia ręczne uruchomienie aparatu synchronizacji.
   ![ReadyToConfigure](./media/how-to-connect-sync-staging-server/readytoconfigure.png)
2. Znak poza/logowanie do i z menu start, wybierz **usługi synchronizacji**.

#### <a name="configuration"></a>Konfigurowanie
Jeśli wprowadzono zmiany niestandardowy serwer podstawowy i chcesz porównać konfigurację na serwerze przejściowym, należy użyć [Dokumentatora konfiguracji usługi Azure AD Connect](https://github.com/Microsoft/AADConnectConfigDocumenter).

#### <a name="import-and-synchronize"></a>Importuj i Synchronizuj
1. Wybierz **łączników**i wybierz łącznik pierwszy z typem **Active Directory Domain Services**. Kliknij przycisk **Uruchom**, wybierz opcję **pełny import**, i **OK**. Wykonaj te czynności dla wszystkich łączników tego typu.
2. Wybierz łącznik usługi z typem **usługi Azure Active Directory (Microsoft)** . Kliknij przycisk **Uruchom**, wybierz opcję **pełny import**, i **OK**.
3. Upewnij się, że nadal wybrana jest karta łączników. Dla każdego łącznika z typem **Active Directory Domain Services**, kliknij przycisk **Uruchom**, wybierz opcję **rozpoczynana jest synchronizacja przyrostowa**, i **OK**.
4. Wybierz łącznik usługi z typem **usługi Azure Active Directory (Microsoft)** . Kliknij przycisk **Uruchom**, wybierz opcję **rozpoczynana jest synchronizacja przyrostowa**, i **OK**.

Masz teraz przygotowane eksportu zmiany do usługi Azure AD i lokalnej usługi AD (Jeśli używasz wdrożenie hybrydowe programu Exchange). Następne kroki umożliwiają sprawdzanie, co ma zostać zmieniona, przed rozpoczęciem faktycznego eksportu do katalogów.

#### <a name="verify"></a>Weryfikuj
1. Uruchom wiersz polecenia i przejdź do `%ProgramFiles%\Microsoft Azure AD Sync\bin`
2. Uruchomienie: `csexport "Name of Connector" %temp%\export.xml /f:x` Nazwa łącznika znajdują się w usłudze synchronizacji. Ma on nazwę, podobnie jak "contoso.com — usługi AAD" dla usługi Azure AD.
3. Uruchomienie: `CSExportAnalyzer %temp%\export.xml > %temp%\export.csv` Masz plik w % temp % o nazwie export.csv, które można zbadać w programie Microsoft Excel. Ten plik zawiera wszystkie zmiany, które mają być eksportowane.
4. Wprowadź niezbędne zmiany dane lub konfiguracja, a następnie uruchom następujące kroki, ponownie (Importuj i Synchronizuj i sprawdź, czy) do momentu zmiany, które mają być eksportowane są oczekiwane.

**Opis plików export.csv** większość pliku jest oczywista. Niektóre skróty do poznania treści:
* OMODT — Typ modyfikacji obiektu. Wskazuje, czy operacja na poziomie obiektu Add, Update lub Delete.
* AMODT — Typ modyfikacji atrybutu. Wskazuje, czy operacja na poziomie atrybutu Add, Update lub delete.

**Pobierz identyfikatory typowych** plik export.csv zawiera wszystkie zmiany, które mają być eksportowane. Każdy wiersz odpowiada zmiany w odniesieniu do obiektu w przestrzeni łącznika, a obiekt jest identyfikowany przez atrybut nazwy domeny. Atrybut nazwy domeny jest unikatowy identyfikator przypisany do obiektu w przestrzeni łącznika. Jeśli masz wiele wierszy/zmian w export.csv do analizowania, może być trudno ustalić, które obiekty, że zmiany są na podstawie atrybutu Nazwa Wyróżniająca samodzielnie. Aby uprościć proces analizowania zmiany, należy użyć csanalyzer.ps1 skrypt programu PowerShell. Skrypt pobiera wspólnej identyfikatorów obiektów (na przykład, displayName, userPrincipalName). Aby użyć skryptu:
1. Skopiuj skrypt programu PowerShell z sekcji [CSAnalyzer](#appendix-csanalyzer) w pliku o nazwie `csanalyzer.ps1`.
2. Otwórz okno programu PowerShell i przejdź do folderu, w której utworzono skryptu programu PowerShell.
3. Uruchom: `.\csanalyzer.ps1 -xmltoimport %temp%\export.xml`.
4. Istnieje już plik o nazwie **processedusers1.csv** , można sprawdzić w programie Microsoft Excel. Należy pamiętać, że plik zawiera mapowanie atrybutu nazwy domeny do wspólnego identyfikatorów (na przykład, displayName i userPrincipalName). Obecnie nie obejmuje zmiany rzeczywistego atrybutów, które mają być eksportowane.

#### <a name="switch-active-server"></a>Przełącznik aktywnego serwera.
1. Na serwerze aktualnie aktywnego wyłączyć serwera (narzędzie DirSync/FIM/Azure AD Sync) tak nie jest eksportowany do usługi Azure AD lub ustaw go w trybie (Azure AD Connect) przejściowym.
2. Uruchom Kreatora instalacji na serwerze w **Tryb przejściowy** i Wyłącz **Tryb przejściowy**.
   ![ReadyToConfigure](./media/how-to-connect-sync-staging-server/additionaltasks.png)

## <a name="disaster-recovery"></a>Odzyskiwanie po awarii
Jest częścią projektu implementacji planować co należy zrobić w przypadku awarii gdzie utraty serwera synchronizacji. Istnieją różne modele i który z nich do użycia zależy od kilku czynników, takich jak:

* Co to jest ustawiona tolerancja, dla którego nie można dokonać zmian do obiektów w usłudze Azure AD podczas przestoju?
* Jeśli korzystasz z synchronizacji haseł, czy użytkownicy akceptować muszą użyć starego hasła w usłudze Azure AD, w przypadku ich zmiany w środowisku lokalnym?
* Czy masz zależność w operacji w czasie rzeczywistym, takich jak funkcja zapisywania zwrotnego haseł?

W zależności od odpowiedzi na te pytania i zasad organizacji można zaimplementować jedną z następujących strategii:

* Ponownie skompiluj, kiedy potrzebne.
* Zapasowe serwer rezerwy dynamicznej, znane jako **Tryb przejściowy**.
* Użyj maszyn wirtualnych.

Jeśli nie korzystasz z wbudowaną bazą danych programu SQL Express, a następnie należy także przejrzeć [wysokiej dostępności SQL](#sql-high-availability) sekcji.

### <a name="rebuild-when-needed"></a>Ponownie skompiluj w razie
Wygodną strategia polega na planowanie odbudowie serwera, gdy potrzebne. Zazwyczaj Instalowanie aparatu synchronizacji i czy początkowego importowania i synchronizacji można wykonać w kilka godzin. Jeśli serwer wolnym jest niedostępna, istnieje możliwość tymczasowo kontroler domeny jest używany do hostowania aparat synchronizacji.

Serwera aparat synchronizacji nie przechowuje dowolny stan o obiektach, dzięki czemu można ponownie skompilować bazy danych w usłudze Active Directory a usługą Azure AD. **SourceAnchor** atrybut jest używany do łączenia obiektów ze środowiska lokalnego i w chmurze. Po odbudowaniu serwera z istniejących obiektów w środowisku lokalnym i chmurą, a następnie aparat synchronizacji pasuje do tych obiektów jednocześnie ponownie na ponowną instalację. Potrzebne do dokumentów i Zapisz kwestie zmian konfiguracji na serwerze, takie jak reguły filtrowania i synchronizacji. Przed rozpoczęciem tej synchronizacji, należy ponownie zastosować te konfiguracje niestandardowe.

### <a name="have-a-spare-standby-server---staging-mode"></a>Serwer rezerwy wolnym — Tryb przejściowy
W przypadku bardziej złożonego środowiska, a następnie zaleca się posiadanie co najmniej jeden serwer rezerwy dynamicznej. Podczas instalacji, aby umożliwić serwera w **Tryb przejściowy**.

Aby uzyskać więcej informacji, zobacz [Tryb przejściowy](#staging-mode).

### <a name="use-virtual-machines"></a>Użyj maszyn wirtualnych
Metoda typowe i obsługiwane jest uruchamianie aparatu synchronizacji na maszynie wirtualnej. W przypadku, gdy host ma problem, obraz z serwera aparat synchronizacji można poddać migracji do innego serwera.

### <a name="sql-high-availability"></a>SQL wysokiej dostępności
Jeśli nie używasz programu SQL Server Express, który jest dostarczany z programem Azure AD Connect, następnie wysoką dostępność dla programu SQL Server należy również rozważyć. Rozwiązania wysokiej dostępności obsługiwane obejmują klastrowania SQL i AOA (zawsze włączone grupy dostępności). Nieobsługiwana rozwiązania obejmują dublowania.

Obsługa SQL AOA został dodany do programu Azure AD Connect w 1.1.524.0 wersji. Przed zainstalowaniem programu Azure AD Connect, należy włączyć SQL AOA. Podczas instalacji program Azure AD Connect wykrywa, czy podane wystąpienie programu SQL jest włączone dla SQL AOA. Po włączeniu SQL AOA Azure dalsze AD Connect wpadł na pomysł Jeśli SQL AOA jest skonfigurowana do replikacji synchronicznej lub asynchronicznej replikacji. Podczas konfigurowania odbiornika grupy dostępności, zalecane jest, ustaw właściwość RegisterAllProvidersIP na 0. Jest to spowodowane aktualnie program Azure AD Connect używa programu SQL Native Client nawiązać połączenia z programem SQL i programu SQL Native Client nie obsługuje korzystanie z właściwości MultiSubNetFailover.

## <a name="appendix-csanalyzer"></a>Appendix CSAnalyzer
Zobacz sekcję [Sprawdź](#verify) dotyczący sposobu użycia tego skryptu.

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
$result=$reader = [System.Xml.XmlReader]::Create($resolvedXMLtoimport) 
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

        #export the collection of users as a CSV
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

* [Synchronizacja w programie Azure AD Connect: Zrozumienie i dostosowywanie synchronizacji](how-to-connect-sync-whatis.md)  
* [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md)  
