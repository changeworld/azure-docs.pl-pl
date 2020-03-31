---
title: 'Synchronizacja usługi Azure AD Connect: zadania i zagadnienia operacyjne | Dokumenty firmy Microsoft'
description: W tym temacie opisano zadania operacyjne dla synchronizacji usługi Azure AD Connect i sposób przygotowania do obsługi tego składnika.
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
ms.openlocfilehash: bc88640cdff4f716902a80bb149913b961d40ae3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261023"
---
# <a name="azure-ad-connect-staging-server-and-disaster-recovery"></a>Usługa Azure AD Connect: serwer przemieszczania i odzyskiwanie po awarii
Za pomocą serwera w trybie przejściowym można wprowadzać zmiany w konfiguracji i wyświetlać podgląd zmian przed uaktywnieniem serwera. Umożliwia również uruchomienie pełnego importu i pełnej synchronizacji, aby sprawdzić, czy wszystkie zmiany są oczekiwane przed wprowadzeniem tych zmian w środowisku produkcyjnym.

## <a name="staging-mode"></a>Tryb przejściowy
Tryb przemieszczania może być używany w kilku scenariuszach, w tym:

* Wysoka dostępność.
* Przetestuj i wdrażaj nowe zmiany konfiguracji.
* Przedstaw nowy serwer i likwiduj stary.

Podczas instalacji można wybrać serwer, który ma być w **trybie przejściowym**. Ta akcja sprawia, że serwer jest aktywny do importu i synchronizacji, ale nie uruchamia żadnych eksportu. Serwer w trybie przejściowym nie jest uruchomiony synchronizacja hasła lub zapisywanie hasła, nawet jeśli te funkcje zostały wybrane podczas instalacji. Po wyłączeniu trybu przejściowego serwer rozpoczyna eksportowanie, włącza synchronizację haseł i włącza zapisywanie hasła.

> [!NOTE]
> Załóżmy, że masz włączoną funkcję synchronizacji skrótów haseł usługi Azure AD Connect z włączoną synchronizacją haszyszu haseł. Po włączeniu trybu przejściowego serwer przestaje synchronizować zmiany hasła z lokalnej usługi AD. Po wyłączeniu trybu przejściowego serwer wznawia synchronizowanie zmian hasła od miejsca, w którym zostało ostatnio przerwane. Jeśli serwer pozostaje w trybie przejściowym przez dłuższy czas, może upłynąć trochę czasu, aby serwer zsynchronizowany wszystkie zmiany hasła, które wystąpiły w tym okresie.
>
>

Nadal można wymusić eksport przy użyciu menedżera usług synchronizacji.

Serwer w trybie przejściowym nadal otrzymuje zmiany z usługi Active Directory i usługi Azure AD i może szybko przejąć obowiązki innego serwera w przypadku awarii. Jeśli wprowadzasz zmiany konfiguracji na serwerze podstawowym, należy wprowadzić te same zmiany na serwerze w trybie przejściowym.

Dla tych z Was, którzy znają starsze technologie synchronizacji, tryb przemieszczania jest inny, ponieważ serwer ma własną bazę danych SQL. Ta architektura umożliwia serwer tryb przemieszczania, które mają być zlokalizowane w innym centrum danych.

### <a name="verify-the-configuration-of-a-server"></a>Sprawdzanie konfiguracji serwera
Aby zastosować tę metodę, wykonaj następujące kroki:

1. [Przygotowanie](#prepare)
2. [Konfiguracja](#configuration)
3. [Importowanie i synchronizowanie](#import-and-synchronize)
4. [Sprawdź](#verify)
5. [Przełączanie aktywnego serwera](#switch-active-server)

#### <a name="prepare"></a>Przygotowanie
1. Zainstaluj usługę Azure AD Connect, wybierz **tryb przemieszczania**i usuń zaznaczenie **synchronizacji początkowej** na ostatniej stronie kreatora instalacji. Ten tryb umożliwia ręczne uruchomienie aparatu synchronizacji.
   ![Konfiguracja ReadyToConfigure](./media/how-to-connect-sync-staging-server/readytoconfigure.png)
2. Zaloguj się/zaloguj się i z menu Start wybierz pozycję **Usługa synchronizacji**.

#### <a name="configuration"></a>Konfigurowanie
Jeśli wprowadzono niestandardowe zmiany na serwerze podstawowym i chcesz porównać konfigurację z serwerem przejściowym, użyj [dokumentu konfiguracji usługi Azure AD Connect](https://github.com/Microsoft/AADConnectConfigDocumenter).

#### <a name="import-and-synchronize"></a>Importowanie i synchronizowanie
1. Wybierz **pozycję Łączniki**i wybierz pierwszy łącznik z **typem Usługi domenowe Active Directory**. Kliknij **pozycję Uruchom**, wybierz pozycję Pełny **import**i **OK**. Wykonaj następujące kroki dla wszystkich łączników tego typu.
2. Wybierz łącznik z **typem usługi Azure Active Directory (Microsoft)**. Kliknij **pozycję Uruchom**, wybierz pozycję Pełny **import**i **OK**.
3. Upewnij się, że karta Łączniki są nadal zaznaczone. Dla każdego łącznika z **typem Usługi domenowe Active Directory**kliknij pozycję **Uruchom**, wybierz pozycję **Synchronizacja delta**i **OK**.
4. Wybierz łącznik z **typem usługi Azure Active Directory (Microsoft)**. Kliknij **pozycję Uruchom**, wybierz pozycję **Synchronizacja delta**i **OK**.

Masz teraz etapowe zmiany eksportu do usługi Azure AD i lokalnej usługi AD (jeśli używasz wdrożenia hybrydowego programu Exchange). Następne kroki umożliwiają sprawdzenie, co ma się zmienić przed faktycznym rozpoczęciem eksportu do katalogów.

#### <a name="verify"></a>Weryfikuj
1. Rozpocznij monit cmd i przejdź do`%ProgramFiles%\Microsoft Azure AD Sync\bin`
2. Uruchom: `csexport "Name of Connector" %temp%\export.xml /f:x` Nazwę łącznika można znaleźć w usłudze synchronizacji. Ma nazwę podobną do "contoso.com — AAD" dla usługi Azure AD.
3. Uruchom: `CSExportAnalyzer %temp%\export.xml > %temp%\export.csv` masz plik o nazwie %temp% o nazwie export.csv, który można zbadać w programie Microsoft Excel. Ten plik zawiera wszystkie zmiany, które mają zostać wyeksportowane.
4. Wprowadzać niezbędne zmiany w danych lub konfiguracji i uruchomić te kroki ponownie (Import i Synchronizuj i weryfikuj), dopóki nie oczekuje się zmian, które mają zostać wyeksportowane.

**Opis pliku export.csv** Większość pliku jest oczywista. Niektóre skróty, aby zrozumieć zawartość:
* OMODT – Typ modyfikacji obiektu. Wskazuje, czy operacja na poziomie obiektu to Dodaj, Aktualizuj lub Usuń.
* AMODT – Typ modyfikacji atrybutu. Wskazuje, czy operacja na poziomie atrybutu jest Dodaj, Aktualizuj lub usuń.

**Pobieranie wspólnych identyfikatorów** Plik export.csv zawiera wszystkie zmiany, które mają zostać wyeksportowane. Każdy wiersz odpowiada zmianie dla obiektu w przestrzeni łącznika, a obiekt jest identyfikowany przez atrybut DN. Atrybut DN jest unikatowym identyfikatorem przypisanym do obiektu w przestrzeni łącznika. Jeśli masz wiele wierszy/zmian w export.csv do analizy, może być trudne do ustalenia, które obiekty zmiany są dla podstawie samego atrybutu DN. Aby uprościć proces analizowania zmian, użyj skryptu programu csanalyzer.ps1 PowerShell. Skrypt pobiera typowe identyfikatory (na przykład displayName, userPrincipalName) obiektów. Aby użyć skryptu:
1. Skopiuj skrypt programu PowerShell z sekcji `csanalyzer.ps1` [CSAnalyzer](#appendix-csanalyzer) do pliku o nazwie .
2. Otwórz okno programu PowerShell i przejdź do folderu, w którym utworzono skrypt programu PowerShell.
3. Uruchom polecenie `.\csanalyzer.ps1 -xmltoimport %temp%\export.xml`.
4. Masz teraz plik o nazwie **processedusers1.csv,** który można zbadać w programie Microsoft Excel. Należy zauważyć, że plik zawiera mapowanie z atrybutu DN do wspólnych identyfikatorów (na przykład displayName i userPrincipalName). Obecnie nie obejmuje rzeczywistych zmian atrybutów, które mają zostać wyeksportowane.

#### <a name="switch-active-server"></a>Przełączanie aktywnego serwera
1. Na aktualnie aktywnym serwerze wyłącz serwer (DirSync/FIM/Azure AD Sync), aby nie eksportować do usługi Azure AD lub ustaw go w trybie przejściowym (Azure AD Connect).
2. Uruchom kreatora instalacji na serwerze w **trybie przejściowym** i wyłącz **tryb przejściowy**.
   ![Konfiguracja ReadyToConfigure](./media/how-to-connect-sync-staging-server/additionaltasks.png)

## <a name="disaster-recovery"></a>Odzyskiwanie po awarii
Częścią projektu implementacji jest zaplanowanie, co należy zrobić w przypadku awarii, w której tracisz serwer synchronizacji. Istnieją różne modele do wykorzystania i który z nich do wykorzystania zależy od kilku czynników, w tym:

* Jaka jest twoja tolerancja na to, że nie można wprowadzać zmian w obiektach w usłudze Azure AD podczas przestoju?
* Jeśli używasz synchronizacji haseł, czy użytkownicy akceptują, że muszą używać starego hasła w usłudze Azure AD w przypadku, gdy zmienią je lokalnie?
* Czy masz zależność od operacji w czasie rzeczywistym, takich jak storcowania hasła?

W zależności od odpowiedzi na te pytania i zasad organizacji można zaimplementować jedną z następujących strategii:

* Odbuduj w razie potrzeby.
* Mieć zapasowy serwer rezerwowy, znany jako **tryb przejściowy**.
* Użyj maszyn wirtualnych.

Jeśli nie używasz wbudowanej bazy danych SQL Express, należy również przejrzeć sekcję [wysokiej dostępności SQL.](#sql-high-availability)

### <a name="rebuild-when-needed"></a>Odbuduj w razie potrzeby
Realną strategią jest zaplanowanie odbudowy serwera w razie potrzeby. Zwykle zainstalowanie aparatu synchronizacji i początkowego importu i synchronizacji można zakończyć w ciągu kilku godzin. Jeśli nie ma wolnego serwera, można tymczasowo użyć kontrolera domeny do hostowania aparatu synchronizacji.

Serwer aparatu synchronizacji nie przechowuje żadnego stanu obiektów, dzięki czemu można przebudować bazę danych z danych w usłudze Active Directory i usłudze Azure AD. **SourceAnchor** atrybut jest używany do łączenia obiektów z lokalnego i chmury. Jeśli przebudowujesz serwer z istniejącymi obiektami lokalnie i chmurą, aparat synchronizacji ponownie dopasowuje te obiekty do siebie podczas ponownej instalacji. Elementy, które należy udokumentować i zapisać, to zmiany konfiguracji wprowadzone na serwerze, takie jak reguły filtrowania i synchronizacji. Te konfiguracje niestandardowe muszą zostać ponownie zastosowane przed rozpoczęciem synchronizacji.

### <a name="have-a-spare-standby-server---staging-mode"></a>Mieć zapasowy serwer rezerwowy - tryb przejściowy
Jeśli masz bardziej złożone środowisko, zaleca się posiadanie co najmniej jednego serwera rezerwowego. Podczas instalacji można włączyć serwer w **trybie przejściowym**.

Aby uzyskać więcej informacji, zobacz [tryb przemieszczania](#staging-mode).

### <a name="use-virtual-machines"></a>Korzystanie z maszyn wirtualnych
Wspólną i obsługiwaną metodą jest uruchomienie aparatu synchronizacji na maszynie wirtualnej. W przypadku, gdy host ma problem, obraz z serwerem aparatu synchronizacji można migrować na inny serwer.

### <a name="sql-high-availability"></a>Wysoka dostępność sql
Jeśli nie używasz programu SQL Server Express, który jest dostarczany z usługą Azure AD Connect, należy również wziąć pod uwagę wysoką dostępność programu SQL Server. Obsługiwane rozwiązania o wysokiej dostępności obejmują klastrowanie SQL i AOA (grupy zawsze dostępne). Nieobsługiwały rozwiązania obejmują dublowanie.

Obsługa aoa SQL został dodany do usługi Azure AD Connect w wersji 1.1.524.0. Przed zainstalowaniem usługi Azure AD Connect należy włączyć usługę SQL AOA. Podczas instalacji usługa Azure AD Connect wykrywa, czy podane wystąpienie SQL jest włączone dla usługi SQL AOA, czy nie. Jeśli usługa SQL AOA jest włączona, usługa Azure AD Connect dodatkowo określa, czy usługa SQL AOA jest skonfigurowana do używania replikacji synchronicznego lub replikacji asynchronicznego. Podczas konfigurowania odbiornika grupy dostępności zaleca się ustawienie właściwości RegisterAllProvidersIP na 0. Dzieje się tak, ponieważ usługa Azure AD Connect obecnie używa klienta macierzystego SQL do łączenia się z klientem macierzystym SQL i SQL nie obsługuje użycia właściwości MultiSubNetFailover.

## <a name="appendix-csanalyzer"></a>Dodatek CSAnalyzer
Zobacz sekcję [sprawdź,](#verify) jak używać tego skryptu.

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

## <a name="next-steps"></a>Następne kroki
**Tematy omówienie**  

* [Synchronizacja usługi Azure AD Connect: zrozumienie i dostosowywanie synchronizacji](how-to-connect-sync-whatis.md)  
* [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md)  
