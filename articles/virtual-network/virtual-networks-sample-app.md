---
title: Usługa Azure przykładowej aplikacji do użytku z programem stref DMZ
titlesuffix: Azure Virtual Network
description: Wdrażanie to prostą aplikację sieci web po utworzeniu sieci obwodowej do testowania scenariuszy przepływu ruchu
services: virtual-network
documentationcenter: na
author: tracsman
manager: rossort
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/03/2017
ms.author: jonor
ms.openlocfilehash: 6753b3a76ff5d3e0266f238d8e354943dec694a8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60788920"
---
# <a name="sample-application-for-use-with-dmzs"></a>Przykładowa aplikacja do użycia z sieci obwodowej
[Wróć do strony zabezpieczeń granic najlepsze praktyki][HOME]

Te skrypty programu PowerShell mogą być uruchamiane lokalnie na serwerach IIS01 i AppVM01, aby zainstalować i skonfigurować prostą aplikację sieci web wyświetlający stronę html, na serwerze frontonu IIS01 zawartością z serwerów AppVM01 zaplecza.

Ta aplikacja zawiera prostym środowisku testowym dla wielu przykłady sieci obwodowej i w jaki sposób zmiany punktów końcowych, grup NSG, przez i zapory reguły mogą mieć wpływ na ruch.

## <a name="firewall-rule-to-allow-icmp"></a>Reguły zapory, aby umożliwić protokołu ICMP
Tej prostej instrukcji programu PowerShell można uruchomić na dowolnej maszynie Wirtualnej Windows, aby zezwolić na ruch protokołu ICMP (Ping). Ta aktualizacja zapory umożliwia łatwiejsze Rozwiązywanie problemów i testowanie, umożliwiając protokół ping do przekazania przez zaporę systemu windows (w przypadku większości dystrybucje systemu Linux, który protokół ICMP jest domyślnie włączone).

```powershell
# Turn On ICMPv4
New-NetFirewallRule -Name Allow_ICMPv4 -DisplayName "Allow ICMPv4" `
    -Protocol ICMPv4 -Enabled True -Profile Any -Action Allow
```

Korzystając z poniższych skryptów, to dodawanie reguły zapory jest pierwszą instrukcję.

## <a name="iis01---web-application-installation-script"></a>IIS01 — skrypt instalacji aplikacji sieci Web
Ten skrypt wykonują następujące czynności:

1. Otwórz IMCPv4 (Ping), zapory systemu windows serwer lokalny, aby ułatwić sobie testowanie
2. Instalacja usług IIS i .NET Framework 4.5
3. Tworzenie strony sieci web ASP.NET i pliku Web.config
4. Zmienianie domyślnej puli aplikacji, aby ułatwić dostęp do plików
5. Ustaw użytkownika anonimowego do konta administratora i hasła

Ten skrypt programu PowerShell powinno być uruchamiane lokalnie, podczas gdy RDP ma pod IIS01.

```powershell
# IIS Server Post Build Config Script
# Get Admin Account and Password
    Write-Host "Please enter the admin account information used to create this VM:" -ForegroundColor Cyan
    $theAdmin = Read-Host -Prompt "The Admin Account Name (no domain or machine name)"
    $thePassword = Read-Host -Prompt "The Admin Password"

# Turn On ICMPv4
    Write-Host "Creating ICMP Rule in Windows Firewall" -ForegroundColor Cyan
    New-NetFirewallRule -Name Allow_ICMPv4 -DisplayName "Allow ICMPv4" -Protocol ICMPv4 -Enabled True -Profile Any -Action Allow

# Install IIS
    Write-Host "Installing IIS and .NET 4.5, this can take some time, like 15+ minutes..." -ForegroundColor Cyan
    add-windowsfeature Web-Server, Web-WebServer, Web-Common-Http, Web-Default-Doc, Web-Dir-Browsing, Web-Http-Errors, Web-Static-Content, Web-Health, Web-Http-Logging, Web-Performance, Web-Stat-Compression, Web-Security, Web-Filtering, Web-App-Dev, Web-ISAPI-Ext, Web-ISAPI-Filter, Web-Net-Ext, Web-Net-Ext45, Web-Asp-Net45, Web-Mgmt-Tools, Web-Mgmt-Console

# Create Web App Pages
    Write-Host "Creating Web page and Web.Config file" -ForegroundColor Cyan
    $MainPage = '<%@ Page Language="vb" AutoEventWireup="false" %>
    <%@ Import Namespace="System.IO" %>
    <script language="vb" runat="server">
        Protected Sub Page_Load(ByVal sender As Object, ByVal e As System.EventArgs) Handles Me.Load
            Dim FILENAME As String = "\\10.0.2.5\WebShare\Rand.txt"
            Dim objStreamReader As StreamReader
            objStreamReader = File.OpenText(FILENAME)
            Dim contents As String = objStreamReader.ReadToEnd()
            lblOutput.Text = contents
            objStreamReader.Close()
            lblTime.Text = Now()
        End Sub
    </script>

    <!DOCTYPE html>
    <html xmlns="http://www.w3.org/1999/xhtml">
    <head runat="server">
        <title>DMZ Example App</title>
    </head>
    <body style="font-family: Optima,Segoe,Segoe UI,Candara,Calibri,Arial,sans-serif;">
      <form id="frmMain" runat="server">
        <div>
          <h1>Looks like you made it!</h1>
          This is a page from the inside (a web server on a private network),<br />
          and it is making its way to the outside! (If you are viewing this from the internet)<br />
          <br />
          The following sections show:
          <ul style="margin-top: 0px;">
            <li> Local Server Time - Shows if this page is or isn''t cached anywhere</li>
            <li> File Output - Shows that the web server is reaching AppVM01 on the backend subnet and successfully returning content</li>
            <li> Image from the Internet - Doesn''t really show anything, but it made me happy to see this when the app worked</li>
          </ul>
          <div style="border: 2px solid #8AC007; border-radius: 25px; padding: 20px; margin: 10px; width: 650px;">
            <b>Local Web Server Time</b>: <asp:Label runat="server" ID="lblTime" /></div>
          <div style="border: 2px solid #8AC007; border-radius: 25px; padding: 20px; margin: 10px; width: 650px;">
            <b>File Output from AppVM01</b>: <asp:Label runat="server" ID="lblOutput" /></div>
          <div style="border: 2px solid #8AC007; border-radius: 25px; padding: 20px; margin: 10px; width: 650px;">
            <b>Image File Linked from the Internet</b>:<br />
            <br />
            <img src="https://sd.keepcalm-o-matic.co.uk/i/keep-calm-you-made-it-7.png" alt="You made it!" width="150" length="175"/></div>
        </div>
      </form>
    </body>
    </html>'

    $WebConfig ='<?xml version="1.0" encoding="utf-8"?>
    <configuration>
      <system.web>
        <compilation debug="true" strict="false" explicit="true" targetFramework="4.5" />
        <httpRuntime targetFramework="4.5" />
        <identity impersonate="true" />
        <customErrors mode="Off"/>
      </system.web>
      <system.webServer>
        <defaultDocument>
          <files>
            <add value="Home.aspx" />
          </files>
        </defaultDocument>
      </system.webServer>
    </configuration>'

    $MainPage | Out-File -FilePath "C:\inetpub\wwwroot\Home.aspx" -Encoding ascii
    $WebConfig | Out-File -FilePath "C:\inetpub\wwwroot\Web.config" -Encoding ascii

# Set App Pool to Classic Pipeline to remote file access will work easier
    Write-Host "Updating IIS Settings" -ForegroundColor Cyan
    c:\windows\system32\inetsrv\appcmd.exe set app "Default Web Site/" /applicationPool:".NET v4.5 Classic"
    c:\windows\system32\inetsrv\appcmd.exe set config "Default Web Site/" /section:system.webServer/security/authentication/anonymousAuthentication /userName:$theAdmin /password:$thePassword /commit:apphost

# Make sure the IIS settings take
    Write-Host "Restarting the W3SVC" -ForegroundColor Cyan
    Restart-Service -Name W3SVC

    Write-Host
    Write-Host "Web App Creation Successful!" -ForegroundColor Green
    Write-Host
```

## <a name="appvm01---file-server-installation-script"></a>AppVM01 — skrypt instalacji serwera plików
Ten skrypt konfiguruje zaplecza dla tej prostej aplikacji. Ten skrypt wykonują następujące czynności:

1. Otwórz IMCPv4 (Ping), na zaporze, aby ułatwić sobie testowanie
2. Utwórz katalog dla witryny sieci web
3. Utwórz plik tekstowy jako zdalnego dostępu przez stronę sieci web
4. Ustaw uprawnienia dla katalogu i pliku na anonimowe, aby zezwolić na dostęp
5. Wyłącz zwiększone zabezpieczenia programu Internet Explorer umożliwia łatwiejsze przeglądania z tego serwera

> [!IMPORTANT]
> **Najlepsze rozwiązanie**: Nigdy nie zostanie wyłączone zwiększonych zabezpieczeń programu Internet Explorer na serwerze produkcyjnym, a także zazwyczaj jest to zły pomysł przeglądania stron internetowych z serwera produkcyjnego. Ponadto otwierania udziałów plików dla dostępu anonimowego jest to zły pomysł, ale gotowe w tym miejscu dla uproszczenia.
>
>

Ten skrypt programu PowerShell powinno być uruchamiane lokalnie, podczas gdy RDP ma pod AppVM01. PowerShell jest wymagany do uruchamiania jako Administrator, aby zapewnić pomyślne wykonanie.

```powershell
# AppVM01 Server Post Build Config Script
# PowerShell must be run as Administrator for Net Share commands to work

# Turn On ICMPv4
    New-NetFirewallRule -Name Allow_ICMPv4 -DisplayName "Allow ICMPv4" -Protocol ICMPv4 -Enabled True -Profile Any -Action Allow

# Create Directory
    New-Item "C:\WebShare" -ItemType Directory

# Write out Rand.txt
    $FileContent = "Hello, I'm the contents of a remote file on AppVM01."
    $FileContent | Out-File -FilePath "C:\WebShare\Rand.txt" -Encoding ascii

# Set Permissions on share
    $Acl = Get-Acl "C:\WebShare"
    $AccessRule = New-Object system.security.accesscontrol.filesystemaccessrule("Everyone","ReadAndExecute, Synchronize","ContainerInherit, ObjectInherit","InheritOnly","Allow")
    $Acl.SetAccessRule($AccessRule)
    Set-Acl "C:\WebShare" $Acl

# Create network share
    Net Share WebShare=C:\WebShare "/grant:Everyone,READ"

# Turn Off IE Enhanced Security Configuration for Admins
    Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Active Setup\Installed Components\{A509B1A7-37EF-4b3f-8CFC-4F3A74704073}" -Name "IsInstalled" -Value 0

    Write-Host
    Write-Host "File Server Set up Successful!" -ForegroundColor Green
    Write-Host
```

## <a name="dns01---dns-server-installation-script"></a>DNS01 — skrypt instalacji serwera DNS
Brak skrypt nie jest uwzględniony w tej przykładowej aplikacji, aby skonfigurować serwer DNS. Jeśli testowanie reguł zapory, sieciowej grupy zabezpieczeń lub Routing zdefiniowany przez użytkownika musi zawierać ruch DNS, serwer DNS01 musi ręcznie skonfigurować. Plik xml konfiguracji sieci i szablonu usługi Resource Manager dla obu przykładach zawiera DNS01 jako podstawowy serwer DNS i publiczny serwer DNS hostowane przez poziom 3 jako kopii zapasowej serwera DNS. Poziom 3 serwer DNS może być rzeczywista serwera DNS dla ruchu innego niż lokalne i za pomocą DNS01 nie instalacji żadnych sieci lokalnej, które wystąpiłyby DNS.

## <a name="next-steps"></a>Kolejne kroki
* Uruchom skrypt IIS01 na serwerze IIS
* Uruchom skrypt serwera plików na AppVM01
* Przejdź do publicznego adresu IP w IIS01 do weryfikacji kompilacji

<!--Link References-->
[HOME]: ../best-practices-network-security.md
